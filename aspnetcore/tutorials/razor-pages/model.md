---
title: Část 2 – Přidání modelu do Razor aplikace stránky v ASP.NET Core
author: rick-anderson
description: Část 2 série kurzů na Razor stránkách.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/model
ms.openlocfilehash: 053e35e78b2bf58fded49c9a2860060d6a81c544
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408107"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="16426-103">Část 2 – Přidání modelu do Razor aplikace stránky v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="16426-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="16426-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="16426-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="16426-105">V této části jsou přidány třídy pro správu filmů.</span><span class="sxs-lookup"><span data-stu-id="16426-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="16426-106">Třídy modelu aplikace používají [Entity Framework Core (EF Core)](/ef/core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="16426-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="16426-107">EF Core je objektově-relační Mapovač (O/RM), který zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="16426-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="16426-108">Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="16426-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="16426-109">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="16426-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="16426-110">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="16426-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16426-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16426-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16426-112">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="16426-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="16426-113">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="16426-113">Name the folder *Models*.</span></span>

<span data-ttu-id="16426-114">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="16426-114">Right click the *Models* folder.</span></span> <span data-ttu-id="16426-115">Vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="16426-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="16426-116">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="16426-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="16426-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16426-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="16426-118">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="16426-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="16426-119">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="16426-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16426-120">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="16426-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="16426-121">V oblast řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a pak vyberte **Přidat** > **novou složku...**. Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="16426-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="16426-122">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor...**.</span><span class="sxs-lookup"><span data-stu-id="16426-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="16426-123">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="16426-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="16426-124">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="16426-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="16426-125">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="16426-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="16426-126">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="16426-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="16426-127">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.</span><span class="sxs-lookup"><span data-stu-id="16426-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="16426-128">Generování uživatelského rozhraní modelu filmů</span><span class="sxs-lookup"><span data-stu-id="16426-128">Scaffold the movie model</span></span>

<span data-ttu-id="16426-129">V této části je model filmu vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="16426-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="16426-130">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="16426-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16426-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16426-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16426-132">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="16426-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="16426-133">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="16426-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="16426-134">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="16426-134">Name the folder *Movies*</span></span>

<span data-ttu-id="16426-135">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="16426-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

<span data-ttu-id="16426-137">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte \*\* Razor stránky pomocí Entity Framework (CRUD)\*\* > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="16426-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

<span data-ttu-id="16426-139">Dokončete dialog **Přidat Razor stránky pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="16426-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="16426-140">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="16426-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="16426-141">V řádku **třídy kontextu dat** vyberte **+** znaménko (plus) a změňte vygenerovaný název z RazorPagesMovie.\*\* Modely\*\*. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="16426-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="16426-142">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="16426-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="16426-143">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="16426-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="16426-144">Vyberte možnost **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="16426-144">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/3/arp.png)

<span data-ttu-id="16426-146">*appsettings.jsv* souboru se aktualizuje pomocí připojovacího řetězce použitého pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="16426-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="16426-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16426-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="16426-148">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="16426-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="16426-149">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="16426-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="16426-150">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="16426-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="16426-151">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="16426-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16426-152">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="16426-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="16426-153">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="16426-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="16426-154">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="16426-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="16426-155">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="16426-155">Name the folder *Movies*</span></span>

<span data-ttu-id="16426-156">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **nové generování uživatelského rozhraní...**.</span><span class="sxs-lookup"><span data-stu-id="16426-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/scaMac.png)

<span data-ttu-id="16426-158">V dialogovém okně **nové generování uživatelského rozhraní** vyberte možnost \*\* Razor stránky pomocí Entity Framework (CRUD)\*\* > **Další**.</span><span class="sxs-lookup"><span data-stu-id="16426-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="16426-160">Dokončete dialog **Přidat Razor stránky pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="16426-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="16426-161">V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video (RazorPagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="16426-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="16426-162">Do řádku **Třída kontextu dat** zadejte název nové třídy, RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="16426-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="16426-163">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="16426-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="16426-164">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="16426-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="16426-165">Vyberte možnost **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="16426-165">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/arpMac.png)

<span data-ttu-id="16426-167">*appsettings.jsv* souboru se aktualizuje pomocí připojovacího řetězce použitého pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="16426-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="16426-168">Přidat nástroje EF</span><span class="sxs-lookup"><span data-stu-id="16426-168">Add EF tools</span></span>

<span data-ttu-id="16426-169">Spusťte následující příkaz .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="16426-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="16426-170">Předchozí příkaz přidá nástroje Entity Framework Core pro .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="16426-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="16426-171">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="16426-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16426-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16426-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16426-173">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="16426-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="16426-174">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="16426-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="16426-175">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="16426-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="16426-176">Aktualizováno</span><span class="sxs-lookup"><span data-stu-id="16426-176">Updated</span></span>

* <span data-ttu-id="16426-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="16426-177">*Startup.cs*</span></span>

<span data-ttu-id="16426-178">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="16426-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16426-179">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="16426-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="16426-180">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="16426-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="16426-181">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="16426-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="16426-182">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="16426-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="16426-183">Aktualizováno</span><span class="sxs-lookup"><span data-stu-id="16426-183">Updated</span></span>

* <span data-ttu-id="16426-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="16426-184">*Startup.cs*</span></span>

<span data-ttu-id="16426-185">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="16426-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="16426-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16426-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="16426-187">Proces generování uživatelského rozhraní vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="16426-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="16426-188">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="16426-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="16426-189">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="16426-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="16426-190">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="16426-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16426-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16426-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16426-192">V této části se používá konzola správce balíčků (PMC) k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="16426-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="16426-193">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="16426-193">Add an initial migration.</span></span>
* <span data-ttu-id="16426-194">Aktualizujte databázi pomocí prvotní migrace.</span><span class="sxs-lookup"><span data-stu-id="16426-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="16426-195">V nabídce **nástroje** vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="16426-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="16426-197">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="16426-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="16426-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16426-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16426-199">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="16426-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="16426-200">Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '.</span><span class="sxs-lookup"><span data-stu-id="16426-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="16426-201">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="16426-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="16426-202">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="16426-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="16426-203">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="16426-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="16426-204">Příkaz migrace generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="16426-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="16426-205">Schéma je založené na modelu určeném v `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="16426-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="16426-206">`InitialCreate`Argument slouží k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="16426-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="16426-207">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="16426-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="16426-208">`update`Příkaz spustí `Up` metodu v migracích, které nebyly aplikovány.</span><span class="sxs-lookup"><span data-stu-id="16426-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="16426-209">V takovém případě `update` spustí `Up` metodu v souboru *migrations/ \<time-stamp> _InitialCreate. cs* , který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="16426-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16426-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16426-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="16426-211">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="16426-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="16426-212">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="16426-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="16426-213">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="16426-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="16426-214">Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="16426-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="16426-215">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="16426-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="16426-216">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="16426-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="16426-217">Projděte si `Startup.ConfigureServices` metodu.</span><span class="sxs-lookup"><span data-stu-id="16426-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="16426-218">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="16426-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="16426-219">`RazorPagesMovieContext`Koordinuje funkce EF Core (vytváření, čtení, aktualizace, odstranění atd.) pro `Movie` model.</span><span class="sxs-lookup"><span data-stu-id="16426-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="16426-220">Kontext dat ( `RazorPagesMovieContext` ) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="16426-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="16426-221">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="16426-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="16426-222">Předchozí kód vytvoří vlastnost [negenerickými \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="16426-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="16426-223">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="16426-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="16426-224">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="16426-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="16426-225">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="16426-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="16426-226">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="16426-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="16426-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16426-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="16426-228">Projděte si `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="16426-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16426-229">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="16426-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="16426-230">Projděte si `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="16426-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="16426-231">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="16426-231">Test the app</span></span>

* <span data-ttu-id="16426-232">Spusťte aplikaci a přidejte ji `/Movies` k adrese URL v prohlížeči ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="16426-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="16426-233">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="16426-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="16426-234">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="16426-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="16426-235">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="16426-235">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="16426-237">V poli možná nebudete moct zadat desítkové čárky `Price` .</span><span class="sxs-lookup"><span data-stu-id="16426-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="16426-238">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="16426-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="16426-239">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="16426-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="16426-240">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="16426-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="16426-241">Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="16426-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16426-242">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="16426-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="16426-243">[Předchozí: začátek](xref:tutorials/razor-pages/razor-pages-start) 
>  [Další: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="16426-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="16426-244">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="16426-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="16426-245">Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="16426-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="16426-246">Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="16426-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="16426-247">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="16426-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="16426-248">Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="16426-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="16426-249">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="16426-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="16426-250">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="16426-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16426-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16426-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16426-252">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="16426-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="16426-253">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="16426-253">Name the folder *Models*.</span></span>

<span data-ttu-id="16426-254">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="16426-254">Right click the *Models* folder.</span></span> <span data-ttu-id="16426-255">Vyberte **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="16426-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="16426-256">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="16426-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="16426-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16426-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="16426-258">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="16426-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="16426-259">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="16426-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16426-260">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="16426-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="16426-261">V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a pak vyberte **Přidat**  >  **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="16426-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="16426-262">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="16426-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="16426-263">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="16426-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="16426-264">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="16426-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="16426-265">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="16426-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="16426-266">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="16426-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="16426-267">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="16426-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="16426-268">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.</span><span class="sxs-lookup"><span data-stu-id="16426-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="16426-269">Generování uživatelského rozhraní modelu filmů</span><span class="sxs-lookup"><span data-stu-id="16426-269">Scaffold the movie model</span></span>

<span data-ttu-id="16426-270">V této části je model filmu vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="16426-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="16426-271">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="16426-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16426-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16426-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16426-273">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="16426-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="16426-274">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="16426-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="16426-275">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="16426-275">Name the folder *Movies*</span></span>

<span data-ttu-id="16426-276">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="16426-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

<span data-ttu-id="16426-278">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte \*\* Razor stránky pomocí Entity Framework (CRUD)\*\* > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="16426-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

<span data-ttu-id="16426-280">Dokončete dialog **Přidat Razor stránky pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="16426-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="16426-281">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="16426-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="16426-282">V řádku **Třída kontextu dat** vyberte **+** znaménko (plus) a přijměte vygenerovaný název **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="16426-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="16426-283">Vyberte možnost **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="16426-283">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/arp.png)

<span data-ttu-id="16426-285">*appsettings.jsv* souboru se aktualizuje pomocí připojovacího řetězce použitého pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="16426-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="16426-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16426-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="16426-287">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="16426-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="16426-288">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="16426-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="16426-289">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="16426-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16426-290">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="16426-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="16426-291">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="16426-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="16426-292">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="16426-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="16426-293">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="16426-293">Name the folder *Movies*</span></span>

<span data-ttu-id="16426-294">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="16426-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/scaMac.png)

<span data-ttu-id="16426-296">V dialogovém okně **Přidat nové generování uživatelského rozhraní** vyberte \*\* Razor stránky pomocí Entity Framework (CRUD)\*\* > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="16426-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="16426-298">Dokončete dialog **Přidat Razor stránky pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="16426-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="16426-299">V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video**.</span><span class="sxs-lookup"><span data-stu-id="16426-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="16426-300">V řádku **třídy kontextu dat** zadejte vyberte **RazorPagesMovieContext** . tím se vytvoří nová třída kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="16426-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="16426-301">V takovém případě bude **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="16426-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="16426-302">Vyberte možnost **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="16426-302">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/arpMac.png)

<span data-ttu-id="16426-304">*appsettings.jsv* souboru se aktualizuje pomocí připojovacího řetězce použitého pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="16426-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="16426-305">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="16426-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="16426-306">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="16426-306">Files created</span></span>

* <span data-ttu-id="16426-307">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="16426-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="16426-308">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="16426-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="16426-309">Soubor aktualizován</span><span class="sxs-lookup"><span data-stu-id="16426-309">File updated</span></span>

* <span data-ttu-id="16426-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="16426-310">*Startup.cs*</span></span>

<span data-ttu-id="16426-311">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="16426-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="16426-312">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="16426-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16426-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16426-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16426-314">V této části se používá konzola správce balíčků (PMC) k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="16426-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="16426-315">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="16426-315">Add an initial migration.</span></span>
* <span data-ttu-id="16426-316">Aktualizujte databázi pomocí prvotní migrace.</span><span class="sxs-lookup"><span data-stu-id="16426-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="16426-317">V nabídce **nástroje** vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="16426-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="16426-319">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="16426-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="16426-320">`Add-Migration`Příkaz vygeneruje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="16426-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="16426-321">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="16426-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="16426-322">`InitialCreate`Argument slouží k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="16426-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="16426-323">Můžete použít libovolný název, ale podle konvence je použit název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="16426-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="16426-324">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="16426-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="16426-325">`Update-Database`Příkaz spustí `Up` metodu v souboru *migrations/ \<time-stamp> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="16426-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="16426-326">`Up`Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="16426-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="16426-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16426-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16426-328">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="16426-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="16426-329">Předchozí příkazy generují následující upozornění:*pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.* Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="16426-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="16426-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16426-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="16426-331">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="16426-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="16426-332">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="16426-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="16426-333">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="16426-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="16426-334">Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="16426-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="16426-335">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="16426-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="16426-336">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="16426-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="16426-337">Projděte si `Startup.ConfigureServices` metodu.</span><span class="sxs-lookup"><span data-stu-id="16426-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="16426-338">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="16426-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="16426-339">`RazorPagesMovieContext`Koordinuje funkce EF Core (vytváření, čtení, aktualizace, odstranění atd.) pro `Movie` model.</span><span class="sxs-lookup"><span data-stu-id="16426-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="16426-340">Kontext dat ( `RazorPagesMovieContext` ) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="16426-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="16426-341">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="16426-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="16426-342">Předchozí kód vytvoří vlastnost [negenerickými \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="16426-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="16426-343">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="16426-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="16426-344">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="16426-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="16426-345">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="16426-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="16426-346">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="16426-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="16426-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16426-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="16426-348">Projděte si `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="16426-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16426-349">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="16426-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="16426-350">Projděte si `Up` metodu.</span><span class="sxs-lookup"><span data-stu-id="16426-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="16426-351">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="16426-351">Test the app</span></span>

* <span data-ttu-id="16426-352">Spusťte aplikaci a přidejte ji `/Movies` k adrese URL v prohlížeči ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="16426-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="16426-353">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="16426-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="16426-354">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="16426-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="16426-355">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="16426-355">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="16426-357">V poli možná nebudete moct zadat desítkové čárky `Price` .</span><span class="sxs-lookup"><span data-stu-id="16426-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="16426-358">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="16426-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="16426-359">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="16426-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="16426-360">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="16426-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="16426-361">Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="16426-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16426-362">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="16426-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="16426-363">[Předchozí: začátek](xref:tutorials/razor-pages/razor-pages-start) 
>  [Další: vygenerované uživatelské rozhraní Razor Stránky](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="16426-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

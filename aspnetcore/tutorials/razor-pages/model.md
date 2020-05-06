---
title: Přidání modelu do aplikace Razor stránky v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat třídy pro správu filmů v databázi pomocí Entity Framework Core (EF Core).
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/model
ms.openlocfilehash: d093d7c4dc1b355c0042f300f70a830653b168c0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769829"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="cc82c-103">Přidání modelu do aplikace Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc82c-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="cc82c-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cc82c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="cc82c-105">V této části jsou přidány třídy pro správu filmů.</span><span class="sxs-lookup"><span data-stu-id="cc82c-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="cc82c-106">Třídy modelu aplikace používají [Entity Framework Core (EF Core)](/ef/core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="cc82c-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="cc82c-107">EF Core je objektově-relační Mapovač (O/RM), který zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="cc82c-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="cc82c-108">Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="cc82c-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="cc82c-109">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="cc82c-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="cc82c-110">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="cc82c-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc82c-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc82c-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc82c-112">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="cc82c-113">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="cc82c-113">Name the folder *Models*.</span></span>

<span data-ttu-id="cc82c-114">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="cc82c-114">Right click the *Models* folder.</span></span> <span data-ttu-id="cc82c-115">Vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="cc82c-116">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="cc82c-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc82c-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc82c-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cc82c-118">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="cc82c-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="cc82c-119">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="cc82c-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc82c-120">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cc82c-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cc82c-121">V oblast řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a pak vyberte **Přidat** > **novou složku...**. Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="cc82c-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="cc82c-122">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor...**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="cc82c-123">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="cc82c-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="cc82c-124">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="cc82c-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="cc82c-125">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="cc82c-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="cc82c-126">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="cc82c-127">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.</span><span class="sxs-lookup"><span data-stu-id="cc82c-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="cc82c-128">Generování uživatelského rozhraní modelu filmů</span><span class="sxs-lookup"><span data-stu-id="cc82c-128">Scaffold the movie model</span></span>

<span data-ttu-id="cc82c-129">V této části je model filmu vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="cc82c-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="cc82c-130">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc82c-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc82c-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc82c-132">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="cc82c-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cc82c-133">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cc82c-134">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="cc82c-134">Name the folder *Movies*</span></span>

<span data-ttu-id="cc82c-135">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

<span data-ttu-id="cc82c-137">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

<span data-ttu-id="cc82c-139">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="cc82c-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="cc82c-140">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cc82c-141">V řádku **třídy kontextu dat** vyberte znaménko **+** (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="cc82c-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="cc82c-142">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="cc82c-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="cc82c-143">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="cc82c-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="cc82c-144">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-144">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/3/arp.png)

<span data-ttu-id="cc82c-146">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="cc82c-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc82c-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc82c-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="cc82c-148">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="cc82c-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="cc82c-149">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="cc82c-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="cc82c-150">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cc82c-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="cc82c-151">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cc82c-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc82c-152">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cc82c-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cc82c-153">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="cc82c-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cc82c-154">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cc82c-155">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="cc82c-155">Name the folder *Movies*</span></span>

<span data-ttu-id="cc82c-156">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **nové generování uživatelského rozhraní...**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/scaMac.png)

<span data-ttu-id="cc82c-158">V dialogovém okně **nové generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="cc82c-160">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="cc82c-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="cc82c-161">V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video (RazorPagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cc82c-162">Do řádku **Třída kontextu dat** zadejte název nové třídy, RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="cc82c-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="cc82c-163">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="cc82c-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="cc82c-164">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="cc82c-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="cc82c-165">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-165">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/arpMac.png)

<span data-ttu-id="cc82c-167">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="cc82c-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="cc82c-168">Přidat nástroje EF</span><span class="sxs-lookup"><span data-stu-id="cc82c-168">Add EF tools</span></span>

<span data-ttu-id="cc82c-169">Spusťte následující příkaz .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="cc82c-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="cc82c-170">Předchozí příkaz přidá nástroje Entity Framework Core pro .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="cc82c-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="cc82c-171">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="cc82c-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc82c-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc82c-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc82c-173">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="cc82c-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="cc82c-174">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="cc82c-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cc82c-175">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="cc82c-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="cc82c-176">Aktualizováno</span><span class="sxs-lookup"><span data-stu-id="cc82c-176">Updated</span></span>

* <span data-ttu-id="cc82c-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cc82c-177">*Startup.cs*</span></span>

<span data-ttu-id="cc82c-178">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="cc82c-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc82c-179">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cc82c-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cc82c-180">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="cc82c-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="cc82c-181">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="cc82c-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cc82c-182">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="cc82c-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="cc82c-183">Aktualizováno</span><span class="sxs-lookup"><span data-stu-id="cc82c-183">Updated</span></span>

* <span data-ttu-id="cc82c-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cc82c-184">*Startup.cs*</span></span>

<span data-ttu-id="cc82c-185">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="cc82c-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc82c-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc82c-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cc82c-187">Proces generování uživatelského rozhraní vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="cc82c-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="cc82c-188">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="cc82c-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="cc82c-189">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="cc82c-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="cc82c-190">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="cc82c-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc82c-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc82c-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc82c-192">V této části se používá konzola správce balíčků (PMC) k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="cc82c-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="cc82c-193">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="cc82c-193">Add an initial migration.</span></span>
* <span data-ttu-id="cc82c-194">Aktualizujte databázi pomocí prvotní migrace.</span><span class="sxs-lookup"><span data-stu-id="cc82c-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="cc82c-195">V nabídce **nástroje** vyberte možnost > **Správce balíčků NuGet** **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="cc82c-197">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="cc82c-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc82c-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc82c-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc82c-199">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cc82c-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="cc82c-200">Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '.</span><span class="sxs-lookup"><span data-stu-id="cc82c-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="cc82c-201">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="cc82c-202">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="cc82c-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="cc82c-203">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="cc82c-204">Příkaz migrace generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="cc82c-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="cc82c-205">Schéma je založené na modelu určeném v `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="cc82c-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="cc82c-206">`InitialCreate` Argument slouží k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="cc82c-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="cc82c-207">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="cc82c-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="cc82c-208">`update` Příkaz spustí `Up` metodu v migracích, které nebyly aplikovány.</span><span class="sxs-lookup"><span data-stu-id="cc82c-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="cc82c-209">V takovém případě `update` spustí `Up` metodu v souboru *. cs s\<časovým razítkem a>_InitialCreate* , který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="cc82c-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc82c-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc82c-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="cc82c-211">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="cc82c-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="cc82c-212">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cc82c-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cc82c-213">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="cc82c-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="cc82c-214">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="cc82c-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="cc82c-215">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="cc82c-216">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="cc82c-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="cc82c-217">Projděte `Startup.ConfigureServices` si metodu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cc82c-218">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="cc82c-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="cc82c-219">`RazorPagesMovieContext` Koordinuje funkce EF Core (vytváření, čtení, aktualizace, odstranění atd.) pro `Movie` model.</span><span class="sxs-lookup"><span data-stu-id="cc82c-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="cc82c-220">Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="cc82c-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="cc82c-221">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="cc82c-222">Předchozí kód vytvoří vlastnost [\<negenerickými Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="cc82c-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="cc82c-223">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="cc82c-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="cc82c-224">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="cc82c-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="cc82c-225">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="cc82c-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="cc82c-226">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="cc82c-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc82c-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc82c-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cc82c-228">Projděte `Up` si metodu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc82c-229">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cc82c-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cc82c-230">Projděte `Up` si metodu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="cc82c-231">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="cc82c-231">Test the app</span></span>

* <span data-ttu-id="cc82c-232">Spusťte aplikaci a přidejte `/Movies` ji k adrese URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="cc82c-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="cc82c-233">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="cc82c-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="cc82c-234">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="cc82c-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="cc82c-235">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="cc82c-235">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="cc82c-237">V `Price` poli možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="cc82c-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="cc82c-238">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="cc82c-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="cc82c-239">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="cc82c-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="cc82c-240">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="cc82c-241">Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cc82c-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc82c-242">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="cc82c-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cc82c-243">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Next: lešení Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="cc82c-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cc82c-244">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="cc82c-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="cc82c-245">Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="cc82c-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="cc82c-246">Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="cc82c-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="cc82c-247">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="cc82c-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="cc82c-248">Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="cc82c-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="cc82c-249">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="cc82c-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="cc82c-250">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="cc82c-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc82c-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc82c-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc82c-252">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="cc82c-253">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="cc82c-253">Name the folder *Models*.</span></span>

<span data-ttu-id="cc82c-254">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="cc82c-254">Right click the *Models* folder.</span></span> <span data-ttu-id="cc82c-255">Vyberte **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="cc82c-256">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="cc82c-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc82c-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc82c-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cc82c-258">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="cc82c-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="cc82c-259">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="cc82c-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc82c-260">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cc82c-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cc82c-261">V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a pak vyberte **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="cc82c-262">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="cc82c-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="cc82c-263">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="cc82c-264">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="cc82c-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="cc82c-265">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="cc82c-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="cc82c-266">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="cc82c-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="cc82c-267">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="cc82c-268">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.</span><span class="sxs-lookup"><span data-stu-id="cc82c-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="cc82c-269">Generování uživatelského rozhraní modelu filmů</span><span class="sxs-lookup"><span data-stu-id="cc82c-269">Scaffold the movie model</span></span>

<span data-ttu-id="cc82c-270">V této části je model filmu vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="cc82c-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="cc82c-271">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc82c-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc82c-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc82c-273">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="cc82c-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cc82c-274">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cc82c-275">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="cc82c-275">Name the folder *Movies*</span></span>

<span data-ttu-id="cc82c-276">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

<span data-ttu-id="cc82c-278">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

<span data-ttu-id="cc82c-280">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="cc82c-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="cc82c-281">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cc82c-282">V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a přijměte vygenerovaný název **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="cc82c-283">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-283">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/arp.png)

<span data-ttu-id="cc82c-285">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="cc82c-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc82c-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc82c-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="cc82c-287">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="cc82c-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="cc82c-288">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cc82c-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="cc82c-289">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cc82c-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc82c-290">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cc82c-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cc82c-291">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="cc82c-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cc82c-292">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cc82c-293">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="cc82c-293">Name the folder *Movies*</span></span>

<span data-ttu-id="cc82c-294">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/scaMac.png)

<span data-ttu-id="cc82c-296">V dialogovém okně **Přidat nové uživatelské rozhraní** vyberte možnost **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="cc82c-298">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="cc82c-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="cc82c-299">V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="cc82c-300">V řádku **třídy kontextu dat** zadejte vyberte **RazorPagesMovieContext** . tím se vytvoří nová třída kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="cc82c-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="cc82c-301">V takovém případě bude **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="cc82c-302">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-302">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/arpMac.png)

<span data-ttu-id="cc82c-304">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="cc82c-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="cc82c-305">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="cc82c-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="cc82c-306">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="cc82c-306">Files created</span></span>

* <span data-ttu-id="cc82c-307">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="cc82c-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cc82c-308">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="cc82c-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="cc82c-309">Soubor aktualizován</span><span class="sxs-lookup"><span data-stu-id="cc82c-309">File updated</span></span>

* <span data-ttu-id="cc82c-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cc82c-310">*Startup.cs*</span></span>

<span data-ttu-id="cc82c-311">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="cc82c-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="cc82c-312">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="cc82c-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc82c-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc82c-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cc82c-314">V této části se používá konzola správce balíčků (PMC) k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="cc82c-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="cc82c-315">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="cc82c-315">Add an initial migration.</span></span>
* <span data-ttu-id="cc82c-316">Aktualizujte databázi pomocí prvotní migrace.</span><span class="sxs-lookup"><span data-stu-id="cc82c-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="cc82c-317">V nabídce **nástroje** vyberte možnost > **Správce balíčků NuGet** **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="cc82c-319">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="cc82c-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="cc82c-320">`Add-Migration` Příkaz vygeneruje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="cc82c-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="cc82c-321">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="cc82c-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="cc82c-322">`InitialCreate` Argument slouží k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="cc82c-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="cc82c-323">Můžete použít libovolný název, ale podle konvence je použit název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="cc82c-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="cc82c-324">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="cc82c-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="cc82c-325">`Update-Database` Příkaz spustí `Up` metodu v souboru *. cs pro migrace a\<časové>_InitialCreate razítko* .</span><span class="sxs-lookup"><span data-stu-id="cc82c-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="cc82c-326">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="cc82c-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc82c-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc82c-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc82c-328">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cc82c-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="cc82c-329">Předchozí příkazy generují následující upozornění:*pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.* Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc82c-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc82c-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="cc82c-331">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="cc82c-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="cc82c-332">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cc82c-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cc82c-333">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="cc82c-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="cc82c-334">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="cc82c-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="cc82c-335">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="cc82c-336">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="cc82c-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="cc82c-337">Projděte `Startup.ConfigureServices` si metodu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cc82c-338">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="cc82c-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="cc82c-339">`RazorPagesMovieContext` Koordinuje funkce EF Core (vytváření, čtení, aktualizace, odstranění atd.) pro `Movie` model.</span><span class="sxs-lookup"><span data-stu-id="cc82c-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="cc82c-340">Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="cc82c-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="cc82c-341">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="cc82c-342">Předchozí kód vytvoří vlastnost [\<negenerickými Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="cc82c-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="cc82c-343">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="cc82c-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="cc82c-344">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="cc82c-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="cc82c-345">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="cc82c-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="cc82c-346">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="cc82c-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="cc82c-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cc82c-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cc82c-348">Projděte `Up` si metodu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc82c-349">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="cc82c-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="cc82c-350">Projděte `Up` si metodu.</span><span class="sxs-lookup"><span data-stu-id="cc82c-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="cc82c-351">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="cc82c-351">Test the app</span></span>

* <span data-ttu-id="cc82c-352">Spusťte aplikaci a přidejte `/Movies` ji k adrese URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="cc82c-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="cc82c-353">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="cc82c-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="cc82c-354">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="cc82c-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="cc82c-355">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="cc82c-355">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="cc82c-357">V `Price` poli možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="cc82c-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="cc82c-358">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="cc82c-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="cc82c-359">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="cc82c-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="cc82c-360">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="cc82c-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="cc82c-361">Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cc82c-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc82c-362">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="cc82c-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cc82c-363">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: Razor vygenerované stránky](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="cc82c-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

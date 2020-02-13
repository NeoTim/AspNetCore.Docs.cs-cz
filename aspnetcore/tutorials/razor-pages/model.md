---
title: Přidání modelu do aplikace v ASP.NET Core Razor Pages
author: rick-anderson
description: Objevte, jak přidat třídy pro správu filmy v databázi pomocí Entity Framework Core (jádro EF).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: d2f9a64c77d76702004b94cdf36e558b33d7e19a
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172577"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="d8968-103">Přidání modelu do aplikace v ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d8968-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="d8968-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d8968-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="d8968-105">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="d8968-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="d8968-106">Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="d8968-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="d8968-107">Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="d8968-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="d8968-108">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="d8968-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="d8968-109">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="d8968-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="d8968-110">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="d8968-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="d8968-111">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="d8968-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d8968-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8968-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8968-113">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="d8968-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="d8968-114">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="d8968-114">Name the folder *Models*.</span></span>

<span data-ttu-id="d8968-115">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="d8968-115">Right click the *Models* folder.</span></span> <span data-ttu-id="d8968-116">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="d8968-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="d8968-117">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="d8968-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d8968-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8968-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d8968-119">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="d8968-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="d8968-120">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8968-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d8968-121">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d8968-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d8968-122">V Oblast řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **Přidat** > **Nová složka...** . Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="d8968-122">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="d8968-123">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor...** .</span><span class="sxs-lookup"><span data-stu-id="d8968-123">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="d8968-124">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="d8968-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="d8968-125">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="d8968-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="d8968-126">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="d8968-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="d8968-127">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="d8968-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="d8968-128">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="d8968-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="d8968-129">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="d8968-129">Scaffold the movie model</span></span>

<span data-ttu-id="d8968-130">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="d8968-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="d8968-131">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="d8968-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d8968-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8968-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8968-133">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="d8968-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d8968-134">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="d8968-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d8968-135">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="d8968-135">Name the folder *Movies*</span></span>

<span data-ttu-id="d8968-136">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="d8968-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="d8968-138">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d8968-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="d8968-140">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="d8968-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="d8968-141">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="d8968-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="d8968-142">V řádku **Třída kontextu dat** vyberte symbol **+** (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="d8968-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="d8968-143">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="d8968-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="d8968-144">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="d8968-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="d8968-145">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d8968-145">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/3/arp.png)

<span data-ttu-id="d8968-147">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="d8968-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d8968-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8968-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="d8968-149">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="d8968-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="d8968-150">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="d8968-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="d8968-151">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d8968-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="d8968-152">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d8968-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d8968-153">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d8968-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d8968-154">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="d8968-154">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d8968-155">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="d8968-155">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d8968-156">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="d8968-156">Name the folder *Movies*</span></span>

<span data-ttu-id="d8968-157">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **nové generování uživatelského rozhraní...** .</span><span class="sxs-lookup"><span data-stu-id="d8968-157">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Image z předchozích kroků.](model/_static/scaMac.png)

<span data-ttu-id="d8968-159">V dialogovém okně **nové generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="d8968-159">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="d8968-161">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="d8968-161">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="d8968-162">V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video (RazorPagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="d8968-162">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="d8968-163">Do řádku **Třída kontextu dat** zadejte název nové třídy, RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="d8968-163">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="d8968-164">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="d8968-164">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="d8968-165">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="d8968-165">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="d8968-166">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d8968-166">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arpMac.png)

<span data-ttu-id="d8968-168">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="d8968-168">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="d8968-169">Přidat nástroje EF</span><span class="sxs-lookup"><span data-stu-id="d8968-169">Add EF tools</span></span>

<span data-ttu-id="d8968-170">Spusťte následující příkaz .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="d8968-170">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="d8968-171">Předchozí příkaz přidá nástroje Entity Framework Core pro .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="d8968-171">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="d8968-172">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="d8968-172">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d8968-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8968-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8968-174">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="d8968-174">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="d8968-175">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="d8968-175">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d8968-176">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="d8968-176">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="d8968-177">Datum aktualizace</span><span class="sxs-lookup"><span data-stu-id="d8968-177">Updated</span></span>

* <span data-ttu-id="d8968-178">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d8968-178">*Startup.cs*</span></span>

<span data-ttu-id="d8968-179">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="d8968-179">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d8968-180">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d8968-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d8968-181">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="d8968-181">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="d8968-182">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="d8968-182">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d8968-183">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="d8968-183">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="d8968-184">Datum aktualizace</span><span class="sxs-lookup"><span data-stu-id="d8968-184">Updated</span></span>

* <span data-ttu-id="d8968-185">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d8968-185">*Startup.cs*</span></span>

<span data-ttu-id="d8968-186">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="d8968-186">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d8968-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8968-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8968-188">Proces generování uživatelského rozhraní vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="d8968-188">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="d8968-189">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="d8968-189">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="d8968-190">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="d8968-190">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="d8968-191">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="d8968-191">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d8968-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8968-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8968-193">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="d8968-193">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="d8968-194">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="d8968-194">Add an initial migration.</span></span>
* <span data-ttu-id="d8968-195">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="d8968-195">Update the database with the initial migration.</span></span>

<span data-ttu-id="d8968-196">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="d8968-196">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d8968-198">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d8968-198">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d8968-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8968-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d8968-200">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d8968-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="d8968-201">Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '.</span><span class="sxs-lookup"><span data-stu-id="d8968-201">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="d8968-202">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="d8968-202">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="d8968-203">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="d8968-203">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="d8968-204">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="d8968-204">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="d8968-205">Příkaz migrace generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="d8968-205">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="d8968-206">Schéma je založené na modelu určeném v `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="d8968-206">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="d8968-207">Argument `InitialCreate` slouží k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="d8968-207">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="d8968-208">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="d8968-208">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="d8968-209">Příkaz `update` spustí metodu `Up` v migracích, které nebyly aplikovány.</span><span class="sxs-lookup"><span data-stu-id="d8968-209">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="d8968-210">V takovém případě `update` spustí metodu `Up` v části *migrace/\<časového razítka > souboru _InitialCreate. cs* , který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="d8968-210">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d8968-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8968-211">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d8968-212">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="d8968-212">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d8968-213">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d8968-213">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d8968-214">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8968-214">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d8968-215">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="d8968-215">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d8968-216">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="d8968-216">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="d8968-217">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="d8968-217">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="d8968-218">Projděte si metodu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d8968-218">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d8968-219">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="d8968-219">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="d8968-220">`RazorPagesMovieContext` koordinuje funkce EF Core (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`.</span><span class="sxs-lookup"><span data-stu-id="d8968-220">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="d8968-221">Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="d8968-221">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="d8968-222">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="d8968-222">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d8968-223">Předchozí kód vytvoří vlastnost [negenerickými\<Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="d8968-223">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d8968-224">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="d8968-224">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d8968-225">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="d8968-225">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d8968-226">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="d8968-226">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="d8968-227">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d8968-227">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d8968-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8968-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8968-229">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="d8968-229">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d8968-230">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d8968-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d8968-231">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="d8968-231">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="d8968-232">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="d8968-232">Test the app</span></span>

* <span data-ttu-id="d8968-233">Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="d8968-233">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="d8968-234">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="d8968-234">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="d8968-235">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="d8968-235">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="d8968-236">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="d8968-236">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="d8968-238">V poli `Price` možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="d8968-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d8968-239">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="d8968-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d8968-240">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="d8968-240">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d8968-241">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="d8968-241">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="d8968-242">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d8968-242">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8968-243">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d8968-243">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d8968-244">[Předchozí: začátek](xref:tutorials/razor-pages/razor-pages-start)
> [Next: vygenerované Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="d8968-244">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d8968-245">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="d8968-245">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="d8968-246">Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="d8968-246">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="d8968-247">Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="d8968-247">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="d8968-248">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="d8968-248">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="d8968-249">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="d8968-249">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="d8968-250">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="d8968-250">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="d8968-251">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="d8968-251">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d8968-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8968-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8968-253">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="d8968-253">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="d8968-254">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="d8968-254">Name the folder *Models*.</span></span>

<span data-ttu-id="d8968-255">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="d8968-255">Right click the *Models* folder.</span></span> <span data-ttu-id="d8968-256">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="d8968-256">Select **Add** > **Class**.</span></span> <span data-ttu-id="d8968-257">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="d8968-257">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d8968-258">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8968-258">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d8968-259">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="d8968-259">Add a folder named *Models*.</span></span>
* <span data-ttu-id="d8968-260">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8968-260">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d8968-261">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d8968-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d8968-262">V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **Přidat** > **Nová složka**.</span><span class="sxs-lookup"><span data-stu-id="d8968-262">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="d8968-263">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="d8968-263">Name the folder *Models*.</span></span>
* <span data-ttu-id="d8968-264">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="d8968-264">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="d8968-265">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="d8968-265">In the **New File** dialog:</span></span>

  * <span data-ttu-id="d8968-266">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="d8968-266">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="d8968-267">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="d8968-267">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="d8968-268">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="d8968-268">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="d8968-269">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="d8968-269">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="d8968-270">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="d8968-270">Scaffold the movie model</span></span>

<span data-ttu-id="d8968-271">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="d8968-271">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="d8968-272">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="d8968-272">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d8968-273">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8968-273">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8968-274">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="d8968-274">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d8968-275">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="d8968-275">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d8968-276">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="d8968-276">Name the folder *Movies*</span></span>

<span data-ttu-id="d8968-277">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="d8968-277">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="d8968-279">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d8968-279">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="d8968-281">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="d8968-281">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="d8968-282">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="d8968-282">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="d8968-283">V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a přijměte vygenerovaný název **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="d8968-283">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="d8968-284">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d8968-284">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arp.png)

<span data-ttu-id="d8968-286">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="d8968-286">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d8968-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8968-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="d8968-288">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="d8968-288">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="d8968-289">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d8968-289">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="d8968-290">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d8968-290">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d8968-291">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d8968-291">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d8968-292">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="d8968-292">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="d8968-293">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="d8968-293">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="d8968-294">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="d8968-294">Name the folder *Movies*</span></span>

<span data-ttu-id="d8968-295">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="d8968-295">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/scaMac.png)

<span data-ttu-id="d8968-297">V dialogovém okně **Přidat nové uživatelské rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d8968-297">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="d8968-299">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="d8968-299">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="d8968-300">V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video**.</span><span class="sxs-lookup"><span data-stu-id="d8968-300">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="d8968-301">V řádku **třídy kontextu dat** zadejte vyberte **RazorPagesMovieContext** . tím se vytvoří nová třída kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="d8968-301">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="d8968-302">V takovém případě bude **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="d8968-302">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="d8968-303">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d8968-303">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arpMac.png)

<span data-ttu-id="d8968-305">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="d8968-305">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="d8968-306">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="d8968-306">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="d8968-307">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="d8968-307">Files created</span></span>

* <span data-ttu-id="d8968-308">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="d8968-308">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="d8968-309">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="d8968-309">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="d8968-310">Aktualizovat soubor</span><span class="sxs-lookup"><span data-stu-id="d8968-310">File updated</span></span>

* <span data-ttu-id="d8968-311">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="d8968-311">*Startup.cs*</span></span>

<span data-ttu-id="d8968-312">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="d8968-312">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="d8968-313">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="d8968-313">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d8968-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8968-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8968-315">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="d8968-315">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="d8968-316">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="d8968-316">Add an initial migration.</span></span>
* <span data-ttu-id="d8968-317">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="d8968-317">Update the database with the initial migration.</span></span>

<span data-ttu-id="d8968-318">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="d8968-318">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d8968-320">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d8968-320">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="d8968-321">Příkaz `Add-Migration` generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="d8968-321">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="d8968-322">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="d8968-322">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="d8968-323">K pojmenování migrace se používá argument `InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="d8968-323">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="d8968-324">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="d8968-324">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="d8968-325">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="d8968-325">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="d8968-326">Příkaz `Update-Database` spustí metodu `Up` v souboru s *časovou známkou Migration/\<> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="d8968-326">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="d8968-327">Metoda `Up` vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="d8968-327">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d8968-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8968-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d8968-329">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d8968-329">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="d8968-330">Předchozí příkazy generují následující upozornění:*pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.* Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="d8968-330">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d8968-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8968-331">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d8968-332">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="d8968-332">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d8968-333">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d8968-333">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d8968-334">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="d8968-334">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d8968-335">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="d8968-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d8968-336">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="d8968-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="d8968-337">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="d8968-337">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="d8968-338">Projděte si metodu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d8968-338">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d8968-339">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="d8968-339">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="d8968-340">`RazorPagesMovieContext` koordinuje funkce EF Core (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`.</span><span class="sxs-lookup"><span data-stu-id="d8968-340">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="d8968-341">Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="d8968-341">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="d8968-342">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="d8968-342">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="d8968-343">Předchozí kód vytvoří vlastnost [negenerickými\<Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="d8968-343">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d8968-344">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="d8968-344">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d8968-345">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="d8968-345">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d8968-346">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="d8968-346">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="d8968-347">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="d8968-347">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d8968-348">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8968-348">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8968-349">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="d8968-349">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d8968-350">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d8968-350">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d8968-351">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="d8968-351">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="d8968-352">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="d8968-352">Test the app</span></span>

* <span data-ttu-id="d8968-353">Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="d8968-353">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="d8968-354">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="d8968-354">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="d8968-355">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="d8968-355">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="d8968-356">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="d8968-356">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="d8968-358">V poli `Price` možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="d8968-358">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d8968-359">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="d8968-359">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d8968-360">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="d8968-360">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d8968-361">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="d8968-361">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="d8968-362">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d8968-362">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8968-363">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d8968-363">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d8968-364">[Předchozí: začátek](xref:tutorials/razor-pages/razor-pages-start)
> [Next: vygenerované Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="d8968-364">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

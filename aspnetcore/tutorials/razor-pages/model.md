---
title: Přidání modelu do aplikace v ASP.NET Core Razor Pages
author: rick-anderson
description: Objevte, jak přidat třídy pro správu filmy v databázi pomocí Entity Framework Core (jádro EF).
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: fa5be8f3a222a7c186409faa2f48e43347df637a
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829293"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="e2c82-103">Přidání modelu do aplikace v ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e2c82-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="e2c82-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e2c82-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="e2c82-105">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="e2c82-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e2c82-106">Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="e2c82-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e2c82-107">Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="e2c82-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e2c82-108">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="e2c82-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e2c82-109">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="e2c82-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e2c82-110">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="e2c82-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="e2c82-111">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="e2c82-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e2c82-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2c82-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2c82-113">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e2c82-114">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="e2c82-114">Name the folder *Models*.</span></span>

<span data-ttu-id="e2c82-115">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="e2c82-115">Right click the *Models* folder.</span></span> <span data-ttu-id="e2c82-116">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="e2c82-117">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e2c82-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2c82-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e2c82-119">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="e2c82-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e2c82-120">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2c82-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e2c82-121">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e2c82-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e2c82-122">V Oblast řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **Přidat** > **Nová složka...** . Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="e2c82-122">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="e2c82-123">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor...** .</span><span class="sxs-lookup"><span data-stu-id="e2c82-123">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="e2c82-124">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="e2c82-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e2c82-125">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="e2c82-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e2c82-126">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="e2c82-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e2c82-127">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="e2c82-128">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="e2c82-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e2c82-129">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="e2c82-129">Scaffold the movie model</span></span>

<span data-ttu-id="e2c82-130">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="e2c82-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e2c82-131">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="e2c82-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e2c82-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2c82-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2c82-133">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="e2c82-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e2c82-134">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e2c82-135">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="e2c82-135">Name the folder *Movies*</span></span>

<span data-ttu-id="e2c82-136">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="e2c82-138">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="e2c82-140">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="e2c82-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e2c82-141">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="e2c82-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e2c82-142">V řádku **Třída kontextu dat** vyberte symbol **+** (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e2c82-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e2c82-143">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="e2c82-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e2c82-144">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="e2c82-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e2c82-145">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-145">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/3/arp.png)

<span data-ttu-id="e2c82-147">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="e2c82-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e2c82-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2c82-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e2c82-149">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="e2c82-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e2c82-150">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="e2c82-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e2c82-151">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e2c82-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e2c82-152">**Pro macOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e2c82-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e2c82-153">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e2c82-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2c82-154">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="e2c82-154">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e2c82-155">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-155">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e2c82-156">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="e2c82-156">Name the folder *Movies*</span></span>

<span data-ttu-id="e2c82-157">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **nové generování uživatelského rozhraní...** .</span><span class="sxs-lookup"><span data-stu-id="e2c82-157">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Image z předchozích kroků.](model/_static/scaMac.png)

<span data-ttu-id="e2c82-159">V dialogovém okně **nové generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Další**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-159">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e2c82-161">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="e2c82-161">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e2c82-162">V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video (RazorPagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="e2c82-162">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e2c82-163">Do řádku **Třída kontextu dat** zadejte název nové třídy, RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e2c82-163">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e2c82-164">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="e2c82-164">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e2c82-165">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="e2c82-165">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e2c82-166">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-166">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arpMac.png)

<span data-ttu-id="e2c82-168">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="e2c82-168">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="e2c82-169">Přidat nástroje EF</span><span class="sxs-lookup"><span data-stu-id="e2c82-169">Add EF tools</span></span>

<span data-ttu-id="e2c82-170">Spusťte následující příkaz .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="e2c82-170">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="e2c82-171">Předchozí příkaz přidá nástroje Entity Framework Core pro .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="e2c82-171">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="e2c82-172">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="e2c82-172">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e2c82-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2c82-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2c82-174">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="e2c82-174">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e2c82-175">*Stránky/filmy*: vytvoření, odstranění, podrobností, úpravy a Index.</span><span class="sxs-lookup"><span data-stu-id="e2c82-175">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e2c82-176">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e2c82-176">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e2c82-177">Datum aktualizace</span><span class="sxs-lookup"><span data-stu-id="e2c82-177">Updated</span></span>

* <span data-ttu-id="e2c82-178">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e2c82-178">*Startup.cs*</span></span>

<span data-ttu-id="e2c82-179">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="e2c82-179">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e2c82-180">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e2c82-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2c82-181">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="e2c82-181">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e2c82-182">*Stránky/filmy*: vytvoření, odstranění, podrobností, úpravy a Index.</span><span class="sxs-lookup"><span data-stu-id="e2c82-182">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e2c82-183">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e2c82-183">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e2c82-184">Datum aktualizace</span><span class="sxs-lookup"><span data-stu-id="e2c82-184">Updated</span></span>

* <span data-ttu-id="e2c82-185">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e2c82-185">*Startup.cs*</span></span>

<span data-ttu-id="e2c82-186">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="e2c82-186">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e2c82-187">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2c82-187">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e2c82-188">Proces generování uživatelského rozhraní vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="e2c82-188">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e2c82-189">*Stránky/filmy*: vytvoření, odstranění, podrobností, úpravy a Index.</span><span class="sxs-lookup"><span data-stu-id="e2c82-189">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="e2c82-190">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="e2c82-190">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e2c82-191">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="e2c82-191">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e2c82-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2c82-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2c82-193">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="e2c82-193">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e2c82-194">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="e2c82-194">Add an initial migration.</span></span>
* <span data-ttu-id="e2c82-195">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="e2c82-195">Update the database with the initial migration.</span></span>

<span data-ttu-id="e2c82-196">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-196">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e2c82-198">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e2c82-198">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e2c82-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2c82-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e2c82-200">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e2c82-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="e2c82-201">Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '.</span><span class="sxs-lookup"><span data-stu-id="e2c82-201">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e2c82-202">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="e2c82-202">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e2c82-203">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="e2c82-203">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e2c82-204">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="e2c82-204">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="e2c82-205">Příkaz migrace generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="e2c82-205">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="e2c82-206">Schéma je založené na modelu určeném v `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e2c82-206">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e2c82-207">`InitialCreate` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="e2c82-207">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="e2c82-208">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="e2c82-208">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e2c82-209">Příkaz `update` spustí metodu `Up` v migracích, které nebyly aplikovány.</span><span class="sxs-lookup"><span data-stu-id="e2c82-209">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e2c82-210">V takovém případě `update` spustí metodu `Up` v části *migrace/\<časového razítka > souboru _InitialCreate. cs* , který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="e2c82-210">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e2c82-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2c82-211">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e2c82-212">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="e2c82-212">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e2c82-213">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e2c82-213">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e2c82-214">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e2c82-214">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e2c82-215">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="e2c82-215">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e2c82-216">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="e2c82-216">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e2c82-217">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="e2c82-217">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e2c82-218">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="e2c82-218">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e2c82-219">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="e2c82-219">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e2c82-220">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="e2c82-220">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="e2c82-221">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="e2c82-221">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e2c82-222">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="e2c82-222">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e2c82-223">Předchozí kód vytvoří vlastnost [negenerickými\<Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="e2c82-223">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e2c82-224">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="e2c82-224">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e2c82-225">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="e2c82-225">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e2c82-226">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="e2c82-226">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e2c82-227">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="e2c82-227">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e2c82-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2c82-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e2c82-229">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="e2c82-229">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e2c82-230">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e2c82-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2c82-231">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="e2c82-231">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e2c82-232">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="e2c82-232">Test the app</span></span>

* <span data-ttu-id="e2c82-233">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e2c82-233">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e2c82-234">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="e2c82-234">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e2c82-235">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e2c82-235">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e2c82-236">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="e2c82-236">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e2c82-238">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="e2c82-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e2c82-239">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="e2c82-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e2c82-240">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e2c82-240">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e2c82-241">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="e2c82-241">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e2c82-242">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e2c82-242">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2c82-243">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="e2c82-243">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e2c82-244">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: generované uživatelské rozhraní pro stránky Razor](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e2c82-244">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e2c82-245">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="e2c82-245">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e2c82-246">Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="e2c82-246">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e2c82-247">Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="e2c82-247">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e2c82-248">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="e2c82-248">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e2c82-249">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="e2c82-249">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e2c82-250">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="e2c82-250">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="e2c82-251">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="e2c82-251">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e2c82-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2c82-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2c82-253">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-253">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e2c82-254">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="e2c82-254">Name the folder *Models*.</span></span>

<span data-ttu-id="e2c82-255">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="e2c82-255">Right click the *Models* folder.</span></span> <span data-ttu-id="e2c82-256">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-256">Select **Add** > **Class**.</span></span> <span data-ttu-id="e2c82-257">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-257">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e2c82-258">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2c82-258">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e2c82-259">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="e2c82-259">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e2c82-260">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e2c82-260">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e2c82-261">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e2c82-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e2c82-262">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-262">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e2c82-263">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="e2c82-263">Name the folder *Models*.</span></span>
* <span data-ttu-id="e2c82-264">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-264">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e2c82-265">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="e2c82-265">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e2c82-266">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="e2c82-266">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e2c82-267">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="e2c82-267">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e2c82-268">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-268">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="e2c82-269">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="e2c82-269">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e2c82-270">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="e2c82-270">Scaffold the movie model</span></span>

<span data-ttu-id="e2c82-271">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="e2c82-271">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e2c82-272">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="e2c82-272">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e2c82-273">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2c82-273">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2c82-274">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="e2c82-274">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e2c82-275">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-275">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e2c82-276">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="e2c82-276">Name the folder *Movies*</span></span>

<span data-ttu-id="e2c82-277">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-277">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="e2c82-279">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-279">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="e2c82-281">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="e2c82-281">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="e2c82-282">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="e2c82-282">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e2c82-283">V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a přijměte vygenerovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-283">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e2c82-284">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-284">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arp.png)

<span data-ttu-id="e2c82-286">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="e2c82-286">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e2c82-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2c82-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e2c82-288">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="e2c82-288">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="e2c82-289">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e2c82-289">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e2c82-290">**Pro macOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e2c82-290">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e2c82-291">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e2c82-291">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2c82-292">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="e2c82-292">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e2c82-293">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-293">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e2c82-294">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="e2c82-294">Name the folder *Movies*</span></span>

<span data-ttu-id="e2c82-295">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-295">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/scaMac.png)

<span data-ttu-id="e2c82-297">V dialogovém okně **Přidat nové uživatelské rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-297">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="e2c82-299">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="e2c82-299">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e2c82-300">V rozevíracím seznamu **třída modelu** vyberte nebo zadejte **video**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-300">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="e2c82-301">V řádku **třídy kontextu dat** zadejte vyberte **RazorPagesMovieContext** . tím se vytvoří nová třída kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="e2c82-301">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="e2c82-302">V takovém případě bude **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-302">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e2c82-303">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-303">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arpMac.png)

<span data-ttu-id="e2c82-305">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="e2c82-305">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="e2c82-306">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="e2c82-306">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="e2c82-307">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="e2c82-307">Files created</span></span>

* <span data-ttu-id="e2c82-308">*Stránky/filmy*: vytvoření, odstranění, podrobností, úpravy a Index.</span><span class="sxs-lookup"><span data-stu-id="e2c82-308">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e2c82-309">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="e2c82-309">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="e2c82-310">Aktualizovat soubor</span><span class="sxs-lookup"><span data-stu-id="e2c82-310">File updated</span></span>

* <span data-ttu-id="e2c82-311">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e2c82-311">*Startup.cs*</span></span>

<span data-ttu-id="e2c82-312">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="e2c82-312">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e2c82-313">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="e2c82-313">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e2c82-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2c82-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e2c82-315">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="e2c82-315">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e2c82-316">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="e2c82-316">Add an initial migration.</span></span>
* <span data-ttu-id="e2c82-317">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="e2c82-317">Update the database with the initial migration.</span></span>

<span data-ttu-id="e2c82-318">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="e2c82-318">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e2c82-320">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e2c82-320">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="e2c82-321">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="e2c82-321">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e2c82-322">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e2c82-322">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="e2c82-323">K pojmenování migrace se používá argument `InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="e2c82-323">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="e2c82-324">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="e2c82-324">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="e2c82-325">Další informace najdete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="e2c82-325">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="e2c82-326">`Update-Database` Příkaz spustí `Up` metoda ve *migrace /\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="e2c82-326">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="e2c82-327">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="e2c82-327">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e2c82-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2c82-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e2c82-329">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e2c82-329">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="e2c82-330">Předchozí příkazy generují následující upozornění:*pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.* Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="e2c82-330">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e2c82-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e2c82-331">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e2c82-332">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="e2c82-332">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e2c82-333">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e2c82-333">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e2c82-334">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e2c82-334">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e2c82-335">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="e2c82-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e2c82-336">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="e2c82-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e2c82-337">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="e2c82-337">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e2c82-338">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="e2c82-338">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e2c82-339">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="e2c82-339">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="e2c82-340">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="e2c82-340">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="e2c82-341">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="e2c82-341">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e2c82-342">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="e2c82-342">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e2c82-343">Předchozí kód vytvoří vlastnost [negenerickými\<Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="e2c82-343">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e2c82-344">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="e2c82-344">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e2c82-345">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="e2c82-345">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e2c82-346">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="e2c82-346">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e2c82-347">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="e2c82-347">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e2c82-348">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e2c82-348">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e2c82-349">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="e2c82-349">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e2c82-350">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e2c82-350">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e2c82-351">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="e2c82-351">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e2c82-352">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="e2c82-352">Test the app</span></span>

* <span data-ttu-id="e2c82-353">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e2c82-353">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e2c82-354">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="e2c82-354">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e2c82-355">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e2c82-355">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e2c82-356">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="e2c82-356">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e2c82-358">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="e2c82-358">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e2c82-359">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="e2c82-359">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e2c82-360">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e2c82-360">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e2c82-361">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="e2c82-361">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e2c82-362">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e2c82-362">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2c82-363">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="e2c82-363">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e2c82-364">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: generované uživatelské rozhraní pro stránky Razor](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e2c82-364">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

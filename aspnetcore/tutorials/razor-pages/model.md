---
title: Přidání modelu do aplikace v ASP.NET Core Razor Pages
author: rick-anderson
description: Objevte, jak přidat třídy pro správu filmy v databázi pomocí Entity Framework Core (jádro EF).
ms.author: riande
ms.date: 11/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: f2c9c2fc8112ef8a1a5afdbe448de6319c43521d
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761220"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="a9edf-103">Přidání modelu do aplikace v ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a9edf-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="a9edf-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9edf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9edf-105">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="a9edf-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="a9edf-106">Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="a9edf-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="a9edf-107">Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="a9edf-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="a9edf-108">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="a9edf-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="a9edf-109">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="a9edf-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="a9edf-110">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="a9edf-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="a9edf-111">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="a9edf-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a9edf-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9edf-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9edf-113">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="a9edf-114">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="a9edf-114">Name the folder *Models*.</span></span>

<span data-ttu-id="a9edf-115">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="a9edf-115">Right click the *Models* folder.</span></span> <span data-ttu-id="a9edf-116">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="a9edf-117">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a9edf-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a9edf-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a9edf-119">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="a9edf-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="a9edf-120">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="a9edf-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a9edf-121">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a9edf-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a9edf-122">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="a9edf-123">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="a9edf-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="a9edf-124">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="a9edf-125">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="a9edf-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="a9edf-126">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="a9edf-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="a9edf-127">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="a9edf-127">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="a9edf-128">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="a9edf-129">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="a9edf-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="a9edf-130">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="a9edf-130">Scaffold the movie model</span></span>

<span data-ttu-id="a9edf-131">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="a9edf-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="a9edf-132">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="a9edf-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a9edf-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9edf-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9edf-134">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="a9edf-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="a9edf-135">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="a9edf-136">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="a9edf-136">Name the folder *Movies*</span></span>

<span data-ttu-id="a9edf-137">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="a9edf-139">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="a9edf-141">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="a9edf-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="a9edf-142">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="a9edf-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="a9edf-143">V řádku **Třída kontextu dat** vyberte symbol **+** (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="a9edf-143">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="a9edf-144">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="a9edf-144">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="a9edf-145">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="a9edf-145">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="a9edf-146">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-146">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/3/arp.png)

<span data-ttu-id="a9edf-148">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="a9edf-148">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a9edf-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a9edf-149">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="a9edf-150">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="a9edf-150">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="a9edf-151">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="a9edf-151">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="a9edf-152">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a9edf-152">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="a9edf-153">**Pro macOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a9edf-153">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a9edf-154">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a9edf-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a9edf-155">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="a9edf-155">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="a9edf-156">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="a9edf-156">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="a9edf-157">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a9edf-157">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a><span data-ttu-id="a9edf-158">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="a9edf-158">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a9edf-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9edf-159">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9edf-160">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="a9edf-160">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="a9edf-161">*Stránky/filmy*: vytvoření, odstranění, podrobností, úpravy a Index.</span><span class="sxs-lookup"><span data-stu-id="a9edf-161">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="a9edf-162">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="a9edf-162">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="a9edf-163">Datum aktualizace</span><span class="sxs-lookup"><span data-stu-id="a9edf-163">Updated</span></span>

* <span data-ttu-id="a9edf-164">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="a9edf-164">*Startup.cs*</span></span>

<span data-ttu-id="a9edf-165">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="a9edf-165">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="a9edf-166">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a9edf-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="a9edf-167">Proces generování uživatelského rozhraní vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="a9edf-167">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="a9edf-168">*Stránky/filmy*: vytvoření, odstranění, podrobností, úpravy a Index.</span><span class="sxs-lookup"><span data-stu-id="a9edf-168">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="a9edf-169">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="a9edf-169">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="a9edf-170">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="a9edf-170">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a9edf-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9edf-171">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9edf-172">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="a9edf-172">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="a9edf-173">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="a9edf-173">Add an initial migration.</span></span>
* <span data-ttu-id="a9edf-174">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="a9edf-174">Update the database with the initial migration.</span></span>

<span data-ttu-id="a9edf-175">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-175">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="a9edf-177">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a9edf-177">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a9edf-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a9edf-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a9edf-179">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a9edf-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="a9edf-180">Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '.</span><span class="sxs-lookup"><span data-stu-id="a9edf-180">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="a9edf-181">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="a9edf-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="a9edf-182">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="a9edf-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="a9edf-183">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="a9edf-183">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="a9edf-184">Příkaz migrace generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="a9edf-184">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="a9edf-185">Schéma je založené na modelu určeném v `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a9edf-185">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="a9edf-186">`InitialCreate` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="a9edf-186">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="a9edf-187">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="a9edf-187">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="a9edf-188">Příkaz `update` spustí metodu `Up` v migracích, které nebyly aplikovány.</span><span class="sxs-lookup"><span data-stu-id="a9edf-188">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="a9edf-189">V takovém případě `update` spustí metodu `Up` v části *migrace/\<časového razítka > souboru _InitialCreate. cs* , který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="a9edf-189">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a9edf-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9edf-190">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="a9edf-191">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="a9edf-191">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="a9edf-192">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a9edf-192">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a9edf-193">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a9edf-193">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="a9edf-194">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="a9edf-194">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="a9edf-195">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="a9edf-195">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="a9edf-196">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="a9edf-196">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="a9edf-197">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="a9edf-197">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a9edf-198">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="a9edf-198">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="a9edf-199">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="a9edf-199">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="a9edf-200">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="a9edf-200">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="a9edf-201">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="a9edf-201">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="a9edf-202">Předchozí kód vytvoří vlastnost [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="a9edf-202">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="a9edf-203">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="a9edf-203">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="a9edf-204">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="a9edf-204">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="a9edf-205">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="a9edf-205">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="a9edf-206">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="a9edf-206">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a9edf-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a9edf-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a9edf-208">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="a9edf-208">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a9edf-209">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a9edf-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a9edf-210">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="a9edf-210">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="a9edf-211">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="a9edf-211">Test the app</span></span>

* <span data-ttu-id="a9edf-212">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="a9edf-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="a9edf-213">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="a9edf-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="a9edf-214">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="a9edf-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="a9edf-215">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="a9edf-215">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="a9edf-217">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="a9edf-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a9edf-218">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="a9edf-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="a9edf-219">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="a9edf-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="a9edf-220">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="a9edf-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="a9edf-221">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a9edf-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9edf-222">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="a9edf-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a9edf-223">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: generované uživatelské rozhraní pro stránky Razor](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="a9edf-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9edf-224">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="a9edf-224">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="a9edf-225">Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="a9edf-225">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="a9edf-226">Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="a9edf-226">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="a9edf-227">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="a9edf-227">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="a9edf-228">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="a9edf-228">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="a9edf-229">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="a9edf-229">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="a9edf-230">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="a9edf-230">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a9edf-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9edf-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9edf-232">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-232">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="a9edf-233">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="a9edf-233">Name the folder *Models*.</span></span>

<span data-ttu-id="a9edf-234">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="a9edf-234">Right click the *Models* folder.</span></span> <span data-ttu-id="a9edf-235">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-235">Select **Add** > **Class**.</span></span> <span data-ttu-id="a9edf-236">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-236">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a9edf-237">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a9edf-237">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a9edf-238">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="a9edf-238">Add a folder named *Models*.</span></span>
* <span data-ttu-id="a9edf-239">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="a9edf-239">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a9edf-240">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a9edf-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a9edf-241">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-241">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="a9edf-242">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="a9edf-242">Name the folder *Models*.</span></span>
* <span data-ttu-id="a9edf-243">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-243">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="a9edf-244">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="a9edf-244">In the **New File** dialog:</span></span>

  * <span data-ttu-id="a9edf-245">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="a9edf-245">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="a9edf-246">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="a9edf-246">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="a9edf-247">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-247">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="a9edf-248">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="a9edf-248">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="a9edf-249">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="a9edf-249">Scaffold the movie model</span></span>

<span data-ttu-id="a9edf-250">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="a9edf-250">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="a9edf-251">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="a9edf-251">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a9edf-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9edf-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9edf-253">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="a9edf-253">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="a9edf-254">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-254">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="a9edf-255">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="a9edf-255">Name the folder *Movies*</span></span>

<span data-ttu-id="a9edf-256">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **Nová vygenerovaná položka**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-256">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="a9edf-258">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-258">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="a9edf-260">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="a9edf-260">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="a9edf-261">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="a9edf-261">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="a9edf-262">V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a přijměte vygenerovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-262">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="a9edf-263">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-263">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arp.png)

<span data-ttu-id="a9edf-265">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="a9edf-265">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a9edf-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a9edf-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="a9edf-267">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="a9edf-267">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="a9edf-268">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="a9edf-268">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="a9edf-269">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a9edf-269">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="a9edf-270">**Pro macOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a9edf-270">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a9edf-271">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a9edf-271">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a9edf-272">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="a9edf-272">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="a9edf-273">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="a9edf-273">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="a9edf-274">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a9edf-274">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="a9edf-275">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="a9edf-275">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="a9edf-276">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="a9edf-276">Files created</span></span>

* <span data-ttu-id="a9edf-277">*Stránky/filmy*: vytvoření, odstranění, podrobností, úpravy a Index.</span><span class="sxs-lookup"><span data-stu-id="a9edf-277">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="a9edf-278">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="a9edf-278">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="a9edf-279">Aktualizovat soubor</span><span class="sxs-lookup"><span data-stu-id="a9edf-279">File updated</span></span>

* <span data-ttu-id="a9edf-280">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="a9edf-280">*Startup.cs*</span></span>

<span data-ttu-id="a9edf-281">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="a9edf-281">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="a9edf-282">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="a9edf-282">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a9edf-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9edf-283">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9edf-284">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="a9edf-284">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="a9edf-285">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="a9edf-285">Add an initial migration.</span></span>
* <span data-ttu-id="a9edf-286">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="a9edf-286">Update the database with the initial migration.</span></span>

<span data-ttu-id="a9edf-287">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="a9edf-287">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="a9edf-289">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a9edf-289">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="a9edf-290">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="a9edf-290">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="a9edf-291">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="a9edf-291">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="a9edf-292">K pojmenování migrace se používá argument `InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="a9edf-292">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="a9edf-293">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="a9edf-293">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="a9edf-294">Další informace najdete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="a9edf-294">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="a9edf-295">`Update-Database` Příkaz spustí `Up` metoda ve *migrace /\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="a9edf-295">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="a9edf-296">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="a9edf-296">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a9edf-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a9edf-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a9edf-298">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a9edf-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="a9edf-299">Předchozí příkazy generují následující upozornění:*pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.* Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="a9edf-299">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a9edf-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9edf-300">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="a9edf-301">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="a9edf-301">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="a9edf-302">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a9edf-302">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a9edf-303">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a9edf-303">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="a9edf-304">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="a9edf-304">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="a9edf-305">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="a9edf-305">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="a9edf-306">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="a9edf-306">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="a9edf-307">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="a9edf-307">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a9edf-308">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="a9edf-308">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="a9edf-309">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="a9edf-309">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="a9edf-310">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="a9edf-310">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="a9edf-311">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="a9edf-311">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="a9edf-312">Předchozí kód vytvoří vlastnost [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="a9edf-312">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="a9edf-313">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="a9edf-313">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="a9edf-314">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="a9edf-314">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="a9edf-315">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="a9edf-315">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="a9edf-316">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="a9edf-316">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a9edf-317">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a9edf-317">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a9edf-318">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="a9edf-318">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a9edf-319">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a9edf-319">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a9edf-320">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="a9edf-320">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="a9edf-321">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="a9edf-321">Test the app</span></span>

* <span data-ttu-id="a9edf-322">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="a9edf-322">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="a9edf-323">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="a9edf-323">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="a9edf-324">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="a9edf-324">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="a9edf-325">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="a9edf-325">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="a9edf-327">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="a9edf-327">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="a9edf-328">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="a9edf-328">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="a9edf-329">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="a9edf-329">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="a9edf-330">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="a9edf-330">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="a9edf-331">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a9edf-331">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9edf-332">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="a9edf-332">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a9edf-333">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: generované uživatelské rozhraní pro stránky Razor](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="a9edf-333">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

---
title: Přidání modelu do aplikace Razor Pages v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat třídy pro správu filmů v databázi pomocí Entity Framework Core (EF Core).
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
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="e6a41-103">Přidání modelu do aplikace Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6a41-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="e6a41-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e6a41-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e6a41-105">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="e6a41-105">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e6a41-106">Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="e6a41-106">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e6a41-107">Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="e6a41-107">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e6a41-108">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="e6a41-108">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e6a41-109">Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="e6a41-109">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e6a41-110">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="e6a41-110">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="e6a41-111">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="e6a41-111">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e6a41-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6a41-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6a41-113">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="e6a41-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e6a41-114">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="e6a41-114">Name the folder *Models*.</span></span>

<span data-ttu-id="e6a41-115">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="e6a41-115">Right click the *Models* folder.</span></span> <span data-ttu-id="e6a41-116">Vyberte **Přidat** **třídu** > .</span><span class="sxs-lookup"><span data-stu-id="e6a41-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="e6a41-117">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="e6a41-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e6a41-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6a41-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e6a41-119">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="e6a41-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e6a41-120">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e6a41-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e6a41-121">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e6a41-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e6a41-122">V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="e6a41-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e6a41-123">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="e6a41-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="e6a41-124">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **přidat** **nový soubor**>.</span><span class="sxs-lookup"><span data-stu-id="e6a41-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e6a41-125">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="e6a41-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e6a41-126">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="e6a41-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e6a41-127">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="e6a41-127">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e6a41-128">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="e6a41-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="e6a41-129">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.</span><span class="sxs-lookup"><span data-stu-id="e6a41-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e6a41-130">Generování uživatelského rozhraní modelu filmů</span><span class="sxs-lookup"><span data-stu-id="e6a41-130">Scaffold the movie model</span></span>

<span data-ttu-id="e6a41-131">V této části je model filmu vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="e6a41-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e6a41-132">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="e6a41-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e6a41-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6a41-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6a41-134">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="e6a41-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e6a41-135">Klikněte pravým tlačítkem na složku *stránky* > **přidat** **novou složku**>.</span><span class="sxs-lookup"><span data-stu-id="e6a41-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e6a41-136">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="e6a41-136">Name the folder *Movies*</span></span>

<span data-ttu-id="e6a41-137">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** **novou vygenerované položky**>.</span><span class="sxs-lookup"><span data-stu-id="e6a41-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

<span data-ttu-id="e6a41-139">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e6a41-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

<span data-ttu-id="e6a41-141">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e6a41-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e6a41-142">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="e6a41-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e6a41-143">V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="e6a41-143">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="e6a41-144">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="e6a41-144">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="e6a41-145">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="e6a41-145">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="e6a41-146">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e6a41-146">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/3/arp.png)

<span data-ttu-id="e6a41-148">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="e6a41-148">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e6a41-149">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6a41-149">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e6a41-150">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="e6a41-150">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e6a41-151">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="e6a41-151">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e6a41-152">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e6a41-152">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e6a41-153">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e6a41-153">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e6a41-154">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e6a41-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e6a41-155">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="e6a41-155">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e6a41-156">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="e6a41-156">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e6a41-157">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e6a41-157">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

---

### <a name="files-created"></a><span data-ttu-id="e6a41-158">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="e6a41-158">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e6a41-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6a41-159">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6a41-160">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="e6a41-160">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="e6a41-161">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="e6a41-161">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e6a41-162">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="e6a41-162">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="e6a41-163">Aktualizuj</span><span class="sxs-lookup"><span data-stu-id="e6a41-163">Updated</span></span>

* <span data-ttu-id="e6a41-164">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e6a41-164">*Startup.cs*</span></span>

<span data-ttu-id="e6a41-165">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="e6a41-165">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="e6a41-166">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e6a41-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="e6a41-167">Proces generování uživatelského rozhraní vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="e6a41-167">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="e6a41-168">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="e6a41-168">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="e6a41-169">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="e6a41-169">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e6a41-170">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="e6a41-170">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e6a41-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6a41-171">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6a41-172">V této části se používá konzola správce balíčků (PMC) k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="e6a41-172">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e6a41-173">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="e6a41-173">Add an initial migration.</span></span>
* <span data-ttu-id="e6a41-174">Aktualizujte databázi pomocí prvotní migrace.</span><span class="sxs-lookup"><span data-stu-id="e6a41-174">Update the database with the initial migration.</span></span>

<span data-ttu-id="e6a41-175">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="e6a41-175">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e6a41-177">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e6a41-177">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e6a41-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6a41-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e6a41-179">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e6a41-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="e6a41-180">Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '.</span><span class="sxs-lookup"><span data-stu-id="e6a41-180">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="e6a41-181">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="e6a41-181">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="e6a41-182">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="e6a41-182">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="e6a41-183">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="e6a41-183">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="e6a41-184">Příkaz migrace generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="e6a41-184">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="e6a41-185">Schéma je založené na modelu určeném v `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="e6a41-185">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="e6a41-186">K pojmenování migrace se používá argument `InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="e6a41-186">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="e6a41-187">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="e6a41-187">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="e6a41-188">Příkaz `update` spustí metodu `Up` v migracích, které nebyly aplikovány.</span><span class="sxs-lookup"><span data-stu-id="e6a41-188">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="e6a41-189">V tomto případě `update` spustí metodu `Up` v části *migrace/\<časového razítka > souboru _InitialCreate. cs* , čímž se vytvoří databáze.</span><span class="sxs-lookup"><span data-stu-id="e6a41-189">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e6a41-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6a41-190">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e6a41-191">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="e6a41-191">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e6a41-192">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e6a41-192">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e6a41-193">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6a41-193">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e6a41-194">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="e6a41-194">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e6a41-195">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="e6a41-195">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e6a41-196">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="e6a41-196">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e6a41-197">Projděte si metodu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e6a41-197">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e6a41-198">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="e6a41-198">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="e6a41-199">Souřadnice `RazorPagesMovieContext` EF Core funkce (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e6a41-199">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="e6a41-200">Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="e6a41-200">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e6a41-201">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="e6a41-201">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e6a41-202">Předchozí kód vytvoří vlastnost [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="e6a41-202">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e6a41-203">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="e6a41-203">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e6a41-204">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="e6a41-204">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e6a41-205">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="e6a41-205">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e6a41-206">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="e6a41-206">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e6a41-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6a41-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e6a41-208">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="e6a41-208">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e6a41-209">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e6a41-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e6a41-210">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="e6a41-210">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e6a41-211">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="e6a41-211">Test the app</span></span>

* <span data-ttu-id="e6a41-212">Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e6a41-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e6a41-213">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="e6a41-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e6a41-214">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e6a41-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e6a41-215">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="e6a41-215">Test the **Create** link.</span></span>

  ![Vytvořit stránku](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e6a41-217">V poli `Price` možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="e6a41-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e6a41-218">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="e6a41-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e6a41-219">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e6a41-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e6a41-220">Otestujte odkazy **Upravit**, **Podrobnosti**a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="e6a41-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e6a41-221">Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e6a41-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6a41-222">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e6a41-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e6a41-223">[Předchozí:](xref:tutorials/razor-pages/razor-pages-start)začátek 
> [Další: vygenerované Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e6a41-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e6a41-224">V této části jsou přidány třídy pro správu filmů v [databázi SQLite](https://www.sqlite.org/index.html)pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="e6a41-224">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="e6a41-225">Aplikace vytvořené pomocí šablony ASP.NET Core používají databázi SQLite.</span><span class="sxs-lookup"><span data-stu-id="e6a41-225">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="e6a41-226">Třídy modelu aplikace se používají s [Entity Framework Core (EF Core)](/ef/core) ([poskytovatel databáze SQLite EF Core](/ef/core/providers/sqlite)) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="e6a41-226">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="e6a41-227">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="e6a41-227">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="e6a41-228">Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="e6a41-228">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="e6a41-229">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="e6a41-229">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="e6a41-230">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="e6a41-230">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e6a41-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6a41-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6a41-232">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="e6a41-232">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="e6a41-233">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="e6a41-233">Name the folder *Models*.</span></span>

<span data-ttu-id="e6a41-234">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="e6a41-234">Right click the *Models* folder.</span></span> <span data-ttu-id="e6a41-235">Vyberte **Přidat** **třídu** > .</span><span class="sxs-lookup"><span data-stu-id="e6a41-235">Select **Add** > **Class**.</span></span> <span data-ttu-id="e6a41-236">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="e6a41-236">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e6a41-237">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6a41-237">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e6a41-238">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="e6a41-238">Add a folder named *Models*.</span></span>
* <span data-ttu-id="e6a41-239">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="e6a41-239">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e6a41-240">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e6a41-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e6a41-241">V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="e6a41-241">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="e6a41-242">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="e6a41-242">Name the folder *Models*.</span></span>
* <span data-ttu-id="e6a41-243">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **přidat** **nový soubor**>.</span><span class="sxs-lookup"><span data-stu-id="e6a41-243">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="e6a41-244">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="e6a41-244">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e6a41-245">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="e6a41-245">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="e6a41-246">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="e6a41-246">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="e6a41-247">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="e6a41-247">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="e6a41-248">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.</span><span class="sxs-lookup"><span data-stu-id="e6a41-248">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="e6a41-249">Generování uživatelského rozhraní modelu filmů</span><span class="sxs-lookup"><span data-stu-id="e6a41-249">Scaffold the movie model</span></span>

<span data-ttu-id="e6a41-250">V této části je model filmu vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="e6a41-250">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="e6a41-251">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="e6a41-251">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e6a41-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6a41-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6a41-253">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="e6a41-253">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="e6a41-254">Klikněte pravým tlačítkem na složku *stránky* > **přidat** **novou složku**>.</span><span class="sxs-lookup"><span data-stu-id="e6a41-254">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="e6a41-255">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="e6a41-255">Name the folder *Movies*</span></span>

<span data-ttu-id="e6a41-256">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** **novou vygenerované položky**>.</span><span class="sxs-lookup"><span data-stu-id="e6a41-256">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

<span data-ttu-id="e6a41-258">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e6a41-258">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

<span data-ttu-id="e6a41-260">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="e6a41-260">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="e6a41-261">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="e6a41-261">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="e6a41-262">V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a přijměte vygenerovaný název **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="e6a41-262">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="e6a41-263">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="e6a41-263">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/arp.png)

<span data-ttu-id="e6a41-265">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="e6a41-265">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e6a41-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6a41-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="e6a41-267">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="e6a41-267">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e6a41-268">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="e6a41-268">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e6a41-269">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e6a41-269">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="e6a41-270">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e6a41-270">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e6a41-271">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e6a41-271">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e6a41-272">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="e6a41-272">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="e6a41-273">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="e6a41-273">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="e6a41-274">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="e6a41-274">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="e6a41-275">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="e6a41-275">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="e6a41-276">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="e6a41-276">Files created</span></span>

* <span data-ttu-id="e6a41-277">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="e6a41-277">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="e6a41-278">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="e6a41-278">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="e6a41-279">Soubor aktualizován</span><span class="sxs-lookup"><span data-stu-id="e6a41-279">File updated</span></span>

* <span data-ttu-id="e6a41-280">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="e6a41-280">*Startup.cs*</span></span>

<span data-ttu-id="e6a41-281">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="e6a41-281">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="e6a41-282">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="e6a41-282">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e6a41-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6a41-283">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6a41-284">V této části se používá konzola správce balíčků (PMC) k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="e6a41-284">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="e6a41-285">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="e6a41-285">Add an initial migration.</span></span>
* <span data-ttu-id="e6a41-286">Aktualizujte databázi pomocí prvotní migrace.</span><span class="sxs-lookup"><span data-stu-id="e6a41-286">Update the database with the initial migration.</span></span>

<span data-ttu-id="e6a41-287">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="e6a41-287">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="e6a41-289">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="e6a41-289">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="e6a41-290">Příkaz `Add-Migration` generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="e6a41-290">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="e6a41-291">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="e6a41-291">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="e6a41-292">K pojmenování migrace se používá argument `InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="e6a41-292">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="e6a41-293">Můžete použít libovolný název, ale podle konvence je použit název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="e6a41-293">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="e6a41-294">Další informace najdete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="e6a41-294">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="e6a41-295">Příkaz `Update-Database` spustí metodu `Up` v souboru s *časovou známkou Migration/\<> _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="e6a41-295">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="e6a41-296">Metoda `Up` vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="e6a41-296">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e6a41-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6a41-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e6a41-298">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e6a41-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="e6a41-299">Předchozí příkazy generují následující upozornění:*pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.* Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="e6a41-299">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e6a41-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6a41-300">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e6a41-301">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="e6a41-301">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e6a41-302">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="e6a41-302">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e6a41-303">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="e6a41-303">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e6a41-304">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="e6a41-304">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e6a41-305">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="e6a41-305">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e6a41-306">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="e6a41-306">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e6a41-307">Projděte si metodu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="e6a41-307">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e6a41-308">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="e6a41-308">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="e6a41-309">Souřadnice `RazorPagesMovieContext` EF Core funkce (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`.</span><span class="sxs-lookup"><span data-stu-id="e6a41-309">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="e6a41-310">Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="e6a41-310">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e6a41-311">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="e6a41-311">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="e6a41-312">Předchozí kód vytvoří vlastnost [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="e6a41-312">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="e6a41-313">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="e6a41-313">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="e6a41-314">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="e6a41-314">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e6a41-315">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="e6a41-315">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e6a41-316">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="e6a41-316">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e6a41-317">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e6a41-317">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e6a41-318">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="e6a41-318">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e6a41-319">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="e6a41-319">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="e6a41-320">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="e6a41-320">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e6a41-321">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="e6a41-321">Test the app</span></span>

* <span data-ttu-id="e6a41-322">Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e6a41-322">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="e6a41-323">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="e6a41-323">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="e6a41-324">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="e6a41-324">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="e6a41-325">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="e6a41-325">Test the **Create** link.</span></span>

  ![Vytvořit stránku](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="e6a41-327">V poli `Price` možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="e6a41-327">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="e6a41-328">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="e6a41-328">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="e6a41-329">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="e6a41-329">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="e6a41-330">Otestujte odkazy **Upravit**, **Podrobnosti**a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="e6a41-330">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e6a41-331">Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e6a41-331">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6a41-332">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e6a41-332">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e6a41-333">[Předchozí:](xref:tutorials/razor-pages/razor-pages-start)začátek 
> [Další: vygenerované Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="e6a41-333">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

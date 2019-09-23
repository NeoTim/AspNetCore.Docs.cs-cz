---
title: Přidání modelu do aplikace v ASP.NET Core Razor Pages
author: rick-anderson
description: Objevte, jak přidat třídy pro správu filmy v databázi pomocí Entity Framework Core (jádro EF).
ms.author: riande
ms.date: 9/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: dcbcf37dfd95d784ebe249ec6e9e4184a8853d3d
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187168"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="5588d-103">Přidání modelu do aplikace v ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5588d-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="5588d-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5588d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5588d-105">V této části jsou třídy přidat pro správu filmy v databázi.</span><span class="sxs-lookup"><span data-stu-id="5588d-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="5588d-106">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="5588d-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="5588d-107">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="5588d-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="5588d-108">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="5588d-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="5588d-109">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="5588d-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="5588d-110">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="5588d-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5588d-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5588d-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5588d-112">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5588d-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="5588d-113">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="5588d-113">Name the folder *Models*.</span></span>

<span data-ttu-id="5588d-114">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="5588d-114">Right click the *Models* folder.</span></span> <span data-ttu-id="5588d-115">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="5588d-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="5588d-116">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="5588d-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5588d-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5588d-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5588d-118">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="5588d-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="5588d-119">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="5588d-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5588d-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5588d-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5588d-121">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5588d-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="5588d-122">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="5588d-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="5588d-123">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="5588d-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="5588d-124">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="5588d-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5588d-125">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="5588d-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="5588d-126">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="5588d-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="5588d-127">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="5588d-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="5588d-128">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="5588d-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="5588d-129">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="5588d-129">Scaffold the movie model</span></span>

<span data-ttu-id="5588d-130">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="5588d-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="5588d-131">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="5588d-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5588d-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5588d-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5588d-133">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="5588d-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5588d-134">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5588d-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5588d-135">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="5588d-135">Name the folder *Movies*</span></span>

<span data-ttu-id="5588d-136">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="5588d-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="5588d-138">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5588d-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="5588d-140">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="5588d-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5588d-141">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="5588d-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="5588d-142">V řádku **třídy kontextu dat** vyberte **+** znaménko (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="5588d-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="5588d-143">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="5588d-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="5588d-144">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="5588d-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="5588d-145">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5588d-145">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/3/arp.png)

<span data-ttu-id="5588d-147">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="5588d-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5588d-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5588d-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="5588d-149">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="5588d-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="5588d-150">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="5588d-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="5588d-151">**Pro Windows**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5588d-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="5588d-152">**Pro MacOS a Linux**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5588d-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5588d-153">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5588d-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5588d-154">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="5588d-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="5588d-155">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="5588d-155">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="5588d-156">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5588d-156">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="5588d-157">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="5588d-157">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5588d-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5588d-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5588d-159">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="5588d-159">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="5588d-160">*Stránky/filmy*: Vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="5588d-160">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5588d-161">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="5588d-161">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="5588d-162">Aktualizuj</span><span class="sxs-lookup"><span data-stu-id="5588d-162">Updated</span></span>

* <span data-ttu-id="5588d-163">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5588d-163">*Startup.cs*</span></span>

<span data-ttu-id="5588d-164">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="5588d-164">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="5588d-165">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5588d-165">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="5588d-166">Proces generování uživatelského rozhraní vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="5588d-166">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="5588d-167">*Stránky/filmy*: Vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="5588d-167">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="5588d-168">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="5588d-168">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="5588d-169">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="5588d-169">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5588d-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5588d-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5588d-171">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="5588d-171">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="5588d-172">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="5588d-172">Add an initial migration.</span></span>
* <span data-ttu-id="5588d-173">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="5588d-173">Update the database with the initial migration.</span></span>

<span data-ttu-id="5588d-174">V nabídce **nástroje** vyberte možnost > **Správce balíčků NuGet** **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="5588d-174">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="5588d-176">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5588d-176">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5588d-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5588d-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5588d-178">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5588d-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="5588d-179">Předchozí příkazy generují následující upozornění: Pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="5588d-179">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="5588d-180">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="5588d-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="5588d-181">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="5588d-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="5588d-182">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="5588d-182">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="5588d-183">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="5588d-183">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="5588d-184">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="5588d-184">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="5588d-185">`InitialCreate` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="5588d-185">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="5588d-186">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="5588d-186">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="5588d-187">`ef database update` Příkaz spustí `Up` metoda ve *migrace /\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="5588d-187">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="5588d-188">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="5588d-188">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5588d-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5588d-189">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5588d-190">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="5588d-190">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5588d-191">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5588d-191">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5588d-192">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="5588d-192">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5588d-193">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="5588d-193">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5588d-194">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="5588d-194">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5588d-195">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="5588d-195">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5588d-196">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="5588d-196">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5588d-197">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="5588d-197">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="5588d-198">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="5588d-198">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="5588d-199">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="5588d-199">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5588d-200">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="5588d-200">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="5588d-201">Předchozí kód vytvoří [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="5588d-201">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="5588d-202">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="5588d-202">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="5588d-203">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="5588d-203">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5588d-204">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="5588d-204">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5588d-205">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="5588d-205">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5588d-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5588d-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5588d-207">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="5588d-207">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5588d-208">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5588d-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5588d-209">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="5588d-209">Examine the `Up` method.</span></span>

---

<span data-ttu-id="5588d-210">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="5588d-210">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="5588d-211">Schéma je založen na zadaném v modelu `RazorPagesMovieContext` (v *Data/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="5588d-211">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="5588d-212">`Initial` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="5588d-212">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="5588d-213">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="5588d-213">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="5588d-214">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="5588d-214">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="5588d-215">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="5588d-215">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="5588d-216">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="5588d-216">Test the app</span></span>

* <span data-ttu-id="5588d-217">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="5588d-217">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="5588d-218">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="5588d-218">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="5588d-219">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="5588d-219">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="5588d-220">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="5588d-220">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="5588d-222">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="5588d-222">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="5588d-223">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="5588d-223">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="5588d-224">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="5588d-224">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="5588d-225">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="5588d-225">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="5588d-226">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5588d-226">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5588d-227">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5588d-227">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5588d-228">[Předchozí Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> další:[ Razor Pages generovaného uživatelského rozhraní](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="5588d-228">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5588d-229">V této části jsou třídy přidat pro správu filmy v databázi.</span><span class="sxs-lookup"><span data-stu-id="5588d-229">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="5588d-230">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="5588d-230">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="5588d-231">EF Core je objektově relační mapování (ORM) platforma, která zjednodušuje kód přístupu k datům.</span><span class="sxs-lookup"><span data-stu-id="5588d-231">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="5588d-232">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="5588d-232">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="5588d-233">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="5588d-233">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="5588d-234">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="5588d-234">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5588d-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5588d-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5588d-236">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5588d-236">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="5588d-237">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="5588d-237">Name the folder *Models*.</span></span>

<span data-ttu-id="5588d-238">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="5588d-238">Right click the *Models* folder.</span></span> <span data-ttu-id="5588d-239">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="5588d-239">Select **Add** > **Class**.</span></span> <span data-ttu-id="5588d-240">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="5588d-240">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5588d-241">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5588d-241">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5588d-242">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="5588d-242">Add a folder named *Models*.</span></span>
* <span data-ttu-id="5588d-243">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="5588d-243">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5588d-244">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5588d-244">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5588d-245">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5588d-245">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="5588d-246">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="5588d-246">Name the folder *Models*.</span></span>
* <span data-ttu-id="5588d-247">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="5588d-247">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="5588d-248">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="5588d-248">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5588d-249">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="5588d-249">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="5588d-250">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="5588d-250">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="5588d-251">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="5588d-251">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="5588d-252">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="5588d-252">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="5588d-253">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="5588d-253">Scaffold the movie model</span></span>

<span data-ttu-id="5588d-254">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="5588d-254">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="5588d-255">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="5588d-255">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5588d-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5588d-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5588d-257">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="5588d-257">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5588d-258">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="5588d-258">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5588d-259">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="5588d-259">Name the folder *Movies*</span></span>

<span data-ttu-id="5588d-260">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="5588d-260">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="5588d-262">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5588d-262">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="5588d-264">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="5588d-264">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="5588d-265">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="5588d-265">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="5588d-266">V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a přijměte vygenerovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="5588d-266">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="5588d-267">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="5588d-267">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arp.png)

<span data-ttu-id="5588d-269">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="5588d-269">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5588d-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5588d-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="5588d-271">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="5588d-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="5588d-272">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="5588d-272">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="5588d-273">**Pro Windows**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5588d-273">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="5588d-274">**Pro MacOS a Linux**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5588d-274">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5588d-275">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5588d-275">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5588d-276">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="5588d-276">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="5588d-277">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="5588d-277">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="5588d-278">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5588d-278">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="5588d-279">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="5588d-279">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="5588d-280">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="5588d-280">Files created</span></span>

* <span data-ttu-id="5588d-281">*Stránky/filmy*: Vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="5588d-281">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5588d-282">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="5588d-282">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="5588d-283">Aktualizovat soubor</span><span class="sxs-lookup"><span data-stu-id="5588d-283">File updated</span></span>

* <span data-ttu-id="5588d-284">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5588d-284">*Startup.cs*</span></span>

<span data-ttu-id="5588d-285">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="5588d-285">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="5588d-286">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="5588d-286">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5588d-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5588d-287">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5588d-288">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="5588d-288">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="5588d-289">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="5588d-289">Add an initial migration.</span></span>
* <span data-ttu-id="5588d-290">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="5588d-290">Update the database with the initial migration.</span></span>

<span data-ttu-id="5588d-291">V nabídce **nástroje** vyberte možnost > **Správce balíčků NuGet** **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="5588d-291">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="5588d-293">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="5588d-293">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5588d-294">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5588d-294">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5588d-295">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5588d-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="5588d-296">Předchozí příkazy generují následující upozornění: Pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="5588d-296">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="5588d-297">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="5588d-297">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="5588d-298">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="5588d-298">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="5588d-299">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="5588d-299">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="5588d-300">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="5588d-300">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="5588d-301">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="5588d-301">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="5588d-302">`InitialCreate` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="5588d-302">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="5588d-303">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="5588d-303">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="5588d-304">`ef database update` Příkaz spustí `Up` metoda ve *migrace /\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="5588d-304">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="5588d-305">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="5588d-305">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5588d-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5588d-306">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5588d-307">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="5588d-307">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5588d-308">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5588d-308">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5588d-309">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="5588d-309">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5588d-310">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="5588d-310">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5588d-311">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="5588d-311">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5588d-312">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="5588d-312">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5588d-313">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="5588d-313">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5588d-314">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="5588d-314">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="5588d-315">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="5588d-315">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="5588d-316">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="5588d-316">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5588d-317">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="5588d-317">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="5588d-318">Předchozí kód vytvoří [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="5588d-318">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="5588d-319">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="5588d-319">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="5588d-320">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="5588d-320">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5588d-321">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="5588d-321">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5588d-322">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="5588d-322">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5588d-323">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5588d-323">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5588d-324">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="5588d-324">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5588d-325">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="5588d-325">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5588d-326">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="5588d-326">Examine the `Up` method.</span></span>

---

<span data-ttu-id="5588d-327">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="5588d-327">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="5588d-328">Schéma je založen na zadaném v modelu `RazorPagesMovieContext` (v *Data/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="5588d-328">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="5588d-329">`Initial` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="5588d-329">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="5588d-330">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="5588d-330">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="5588d-331">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="5588d-331">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="5588d-332">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="5588d-332">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="5588d-333">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="5588d-333">Test the app</span></span>

* <span data-ttu-id="5588d-334">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="5588d-334">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="5588d-335">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="5588d-335">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="5588d-336">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="5588d-336">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="5588d-337">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="5588d-337">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="5588d-339">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="5588d-339">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="5588d-340">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="5588d-340">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="5588d-341">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="5588d-341">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="5588d-342">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="5588d-342">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="5588d-343">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5588d-343">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5588d-344">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5588d-344">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5588d-345">[Předchozí Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> další:[ Razor Pages generovaného uživatelského rozhraní](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="5588d-345">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

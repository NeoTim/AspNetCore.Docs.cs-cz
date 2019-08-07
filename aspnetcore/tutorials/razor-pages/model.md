---
title: Přidání modelu do aplikace v ASP.NET Core Razor Pages
author: rick-anderson
description: Objevte, jak přidat třídy pro správu filmy v databázi pomocí Entity Framework Core (jádro EF).
ms.author: riande
ms.date: 07/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 6132f7b907014b4f57bb9ae0300e00b6ecb23f1a
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820067"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="8b3d9-103">Přidání modelu do aplikace v ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="8b3d9-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="8b3d9-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8b3d9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b3d9-105">V této části jsou třídy přidat pro správu filmy v databázi.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="8b3d9-106">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="8b3d9-107">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="8b3d9-108">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="8b3d9-109">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="8b3d9-110">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="8b3d9-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b3d9-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b3d9-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8b3d9-112">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="8b3d9-113">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-113">Name the folder *Models*.</span></span>

<span data-ttu-id="8b3d9-114">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-114">Right click the *Models* folder.</span></span> <span data-ttu-id="8b3d9-115">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="8b3d9-116">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8b3d9-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b3d9-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8b3d9-118">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="8b3d9-119">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8b3d9-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8b3d9-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8b3d9-121">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="8b3d9-122">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="8b3d9-123">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="8b3d9-124">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="8b3d9-125">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="8b3d9-126">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="8b3d9-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="8b3d9-127">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="8b3d9-128">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8b3d9-129">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="8b3d9-129">Scaffold the movie model</span></span>

<span data-ttu-id="8b3d9-130">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8b3d9-131">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b3d9-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b3d9-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8b3d9-133">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8b3d9-134">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="8b3d9-135">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="8b3d9-135">Name the folder *Movies*</span></span>

<span data-ttu-id="8b3d9-136">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="8b3d9-138">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="8b3d9-140">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="8b3d9-141">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="8b3d9-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="8b3d9-142">V řádku **třídy kontextu dat** vyberte **+** znaménko (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="8b3d9-143">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="8b3d9-144">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="8b3d9-145">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-145">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/3/arp.png)

<span data-ttu-id="8b3d9-147">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8b3d9-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b3d9-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="8b3d9-149">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="8b3d9-150">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-150">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
   ```

* <span data-ttu-id="8b3d9-151">**Pro Windows**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-151">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="8b3d9-152">**Pro MacOS a Linux**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-152">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8b3d9-153">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b3d9-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8b3d9-154">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="8b3d9-155">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-155">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
   ```

* <span data-ttu-id="8b3d9-156">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-156">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="8b3d9-157">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-157">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="8b3d9-158">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="8b3d9-158">Files created</span></span>

* <span data-ttu-id="8b3d9-159">*Stránky/filmy*: Vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-159">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="8b3d9-160">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8b3d9-160">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="8b3d9-161">Aktualizovat soubor</span><span class="sxs-lookup"><span data-stu-id="8b3d9-161">File updated</span></span>

* <span data-ttu-id="8b3d9-162">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8b3d9-162">*Startup.cs*</span></span>

<span data-ttu-id="8b3d9-163">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-163">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="8b3d9-164">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="8b3d9-164">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b3d9-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b3d9-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8b3d9-166">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-166">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="8b3d9-167">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-167">Add an initial migration.</span></span>
* <span data-ttu-id="8b3d9-168">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-168">Update the database with the initial migration.</span></span>

<span data-ttu-id="8b3d9-169">V nabídce **nástroje** vyberte možnost > **Správce balíčků NuGet** **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-169">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8b3d9-171">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-171">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8b3d9-172">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b3d9-172">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8b3d9-173">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b3d9-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="8b3d9-174">Předchozí příkazy generují následující upozornění: Pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-174">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="8b3d9-175">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-175">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="8b3d9-176">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-176">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="8b3d9-177">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-177">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="8b3d9-178">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-178">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="8b3d9-179">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-179">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="8b3d9-180">`InitialCreate` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-180">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="8b3d9-181">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-181">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="8b3d9-182">`ef database update` Příkaz spustí `Up` metoda ve *migrace /\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-182">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="8b3d9-183">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-183">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b3d9-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b3d9-184">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8b3d9-185">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="8b3d9-185">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8b3d9-186">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-186">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8b3d9-187">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-187">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="8b3d9-188">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-188">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8b3d9-189">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-189">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="8b3d9-190">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-190">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="8b3d9-191">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-191">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8b3d9-192">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-192">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="8b3d9-193">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-193">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="8b3d9-194">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-194">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="8b3d9-195">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-195">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="8b3d9-196">Předchozí kód vytvoří [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-196">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8b3d9-197">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-197">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8b3d9-198">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-198">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8b3d9-199">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-199">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8b3d9-200">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-200">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8b3d9-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b3d9-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8b3d9-202">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-202">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8b3d9-203">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b3d9-203">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8b3d9-204">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-204">Examine the `Up` method.</span></span>

---

<span data-ttu-id="8b3d9-205">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-205">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="8b3d9-206">Schéma je založen na zadaném v modelu `RazorPagesMovieContext` (v *Data/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-206">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="8b3d9-207">`Initial` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-207">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="8b3d9-208">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-208">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="8b3d9-209">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-209">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="8b3d9-210">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-210">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="8b3d9-211">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="8b3d9-211">Test the app</span></span>

* <span data-ttu-id="8b3d9-212">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-212">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="8b3d9-213">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-213">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="8b3d9-214">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-214">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="8b3d9-215">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-215">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="8b3d9-217">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-217">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8b3d9-218">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-218">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8b3d9-219">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-219">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8b3d9-220">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-220">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="8b3d9-221">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-221">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b3d9-222">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8b3d9-222">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8b3d9-223">[Předchozí Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> další:[ Razor Pages generovaného uživatelského rozhraní](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="8b3d9-223">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b3d9-224">V této části jsou třídy přidat pro správu filmy v databázi.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-224">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="8b3d9-225">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-225">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="8b3d9-226">EF Core je objektově relační mapování (ORM) platforma, která zjednodušuje kód přístupu k datům.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-226">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="8b3d9-227">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-227">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="8b3d9-228">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-228">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="8b3d9-229">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="8b3d9-229">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b3d9-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b3d9-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8b3d9-231">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-231">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="8b3d9-232">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-232">Name the folder *Models*.</span></span>

<span data-ttu-id="8b3d9-233">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-233">Right click the *Models* folder.</span></span> <span data-ttu-id="8b3d9-234">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-234">Select **Add** > **Class**.</span></span> <span data-ttu-id="8b3d9-235">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-235">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8b3d9-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b3d9-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8b3d9-237">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-237">Add a folder named *Models*.</span></span>
* <span data-ttu-id="8b3d9-238">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-238">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8b3d9-239">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8b3d9-239">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8b3d9-240">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-240">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="8b3d9-241">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-241">Name the folder *Models*.</span></span>
* <span data-ttu-id="8b3d9-242">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-242">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="8b3d9-243">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-243">In the **New File** dialog:</span></span>

  * <span data-ttu-id="8b3d9-244">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-244">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="8b3d9-245">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="8b3d9-245">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="8b3d9-246">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-246">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="8b3d9-247">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-247">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8b3d9-248">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="8b3d9-248">Scaffold the movie model</span></span>

<span data-ttu-id="8b3d9-249">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-249">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8b3d9-250">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-250">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b3d9-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b3d9-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8b3d9-252">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-252">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="8b3d9-253">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-253">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="8b3d9-254">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="8b3d9-254">Name the folder *Movies*</span></span>

<span data-ttu-id="8b3d9-255">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-255">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="8b3d9-257">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-257">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="8b3d9-259">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-259">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="8b3d9-260">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="8b3d9-260">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="8b3d9-261">V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a přijměte vygenerovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-261">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="8b3d9-262">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-262">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arp.png)

<span data-ttu-id="8b3d9-264">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-264">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8b3d9-265">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b3d9-265">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="8b3d9-266">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-266">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="8b3d9-267">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-267">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="8b3d9-268">**Pro Windows**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-268">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="8b3d9-269">**Pro MacOS a Linux**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-269">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8b3d9-270">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b3d9-270">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8b3d9-271">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="8b3d9-272">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-272">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="8b3d9-273">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-273">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="8b3d9-274">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-274">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="8b3d9-275">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="8b3d9-275">Files created</span></span>

* <span data-ttu-id="8b3d9-276">*Stránky/filmy*: Vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-276">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="8b3d9-277">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="8b3d9-277">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="8b3d9-278">Aktualizovat soubor</span><span class="sxs-lookup"><span data-stu-id="8b3d9-278">File updated</span></span>

* <span data-ttu-id="8b3d9-279">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="8b3d9-279">*Startup.cs*</span></span>

<span data-ttu-id="8b3d9-280">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-280">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="8b3d9-281">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="8b3d9-281">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b3d9-282">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b3d9-282">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8b3d9-283">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-283">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="8b3d9-284">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-284">Add an initial migration.</span></span>
* <span data-ttu-id="8b3d9-285">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-285">Update the database with the initial migration.</span></span>

<span data-ttu-id="8b3d9-286">V nabídce **nástroje** vyberte možnost > **Správce balíčků NuGet** **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-286">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8b3d9-288">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-288">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8b3d9-289">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b3d9-289">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8b3d9-290">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b3d9-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="8b3d9-291">Předchozí příkazy generují následující upozornění: Pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-291">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="8b3d9-292">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-292">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="8b3d9-293">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-293">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="8b3d9-294">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-294">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="8b3d9-295">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-295">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="8b3d9-296">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-296">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="8b3d9-297">`InitialCreate` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-297">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="8b3d9-298">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-298">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="8b3d9-299">`ef database update` Příkaz spustí `Up` metoda ve *migrace /\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-299">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="8b3d9-300">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-300">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8b3d9-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b3d9-301">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8b3d9-302">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="8b3d9-302">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8b3d9-303">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-303">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8b3d9-304">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-304">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="8b3d9-305">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-305">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8b3d9-306">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-306">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="8b3d9-307">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-307">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="8b3d9-308">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-308">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8b3d9-309">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-309">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="8b3d9-310">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-310">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="8b3d9-311">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-311">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="8b3d9-312">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-312">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="8b3d9-313">Předchozí kód vytvoří [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-313">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8b3d9-314">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-314">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8b3d9-315">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-315">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8b3d9-316">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-316">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8b3d9-317">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-317">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8b3d9-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8b3d9-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8b3d9-319">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-319">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8b3d9-320">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8b3d9-320">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8b3d9-321">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-321">Examine the `Up` method.</span></span>

---

<span data-ttu-id="8b3d9-322">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-322">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="8b3d9-323">Schéma je založen na zadaném v modelu `RazorPagesMovieContext` (v *Data/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-323">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="8b3d9-324">`Initial` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-324">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="8b3d9-325">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-325">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="8b3d9-326">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-326">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="8b3d9-327">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-327">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="8b3d9-328">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="8b3d9-328">Test the app</span></span>

* <span data-ttu-id="8b3d9-329">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-329">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="8b3d9-330">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="8b3d9-330">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="8b3d9-331">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-331">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="8b3d9-332">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-332">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="8b3d9-334">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-334">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8b3d9-335">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-335">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8b3d9-336">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8b3d9-336">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8b3d9-337">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-337">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="8b3d9-338">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8b3d9-338">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b3d9-339">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8b3d9-339">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="8b3d9-340">[Předchozí Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> další:[ Razor Pages generovaného uživatelského rozhraní](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="8b3d9-340">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

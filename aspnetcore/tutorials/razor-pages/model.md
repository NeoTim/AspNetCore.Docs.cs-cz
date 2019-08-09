---
title: Přidání modelu do aplikace v ASP.NET Core Razor Pages
author: rick-anderson
description: Objevte, jak přidat třídy pro správu filmy v databázi pomocí Entity Framework Core (jádro EF).
ms.author: riande
ms.date: 07/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 39e2a38e0b91b7dbecf05c084ca0be5e312dcb0d
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862875"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="dbeda-103">Přidání modelu do aplikace v ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="dbeda-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="dbeda-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dbeda-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dbeda-105">V této části jsou třídy přidat pro správu filmy v databázi.</span><span class="sxs-lookup"><span data-stu-id="dbeda-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="dbeda-106">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="dbeda-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="dbeda-107">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="dbeda-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="dbeda-108">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="dbeda-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="dbeda-109">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="dbeda-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="dbeda-110">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="dbeda-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dbeda-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbeda-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dbeda-112">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="dbeda-113">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="dbeda-113">Name the folder *Models*.</span></span>

<span data-ttu-id="dbeda-114">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="dbeda-114">Right click the *Models* folder.</span></span> <span data-ttu-id="dbeda-115">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="dbeda-116">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dbeda-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbeda-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dbeda-118">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="dbeda-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="dbeda-119">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbeda-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dbeda-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dbeda-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dbeda-121">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="dbeda-122">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="dbeda-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="dbeda-123">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="dbeda-124">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="dbeda-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="dbeda-125">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="dbeda-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="dbeda-126">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="dbeda-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="dbeda-127">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="dbeda-128">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="dbeda-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="dbeda-129">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="dbeda-129">Scaffold the movie model</span></span>

<span data-ttu-id="dbeda-130">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="dbeda-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="dbeda-131">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="dbeda-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dbeda-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbeda-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dbeda-133">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="dbeda-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dbeda-134">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dbeda-135">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="dbeda-135">Name the folder *Movies*</span></span>

<span data-ttu-id="dbeda-136">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="dbeda-138">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="dbeda-140">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="dbeda-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="dbeda-141">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="dbeda-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="dbeda-142">V řádku **třídy kontextu dat** vyberte **+** znaménko (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="dbeda-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="dbeda-143">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="dbeda-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="dbeda-144">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="dbeda-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="dbeda-145">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-145">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/3/arp.png)

<span data-ttu-id="dbeda-147">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="dbeda-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dbeda-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbeda-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="dbeda-149">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="dbeda-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="dbeda-150">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="dbeda-150">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
   ```

* <span data-ttu-id="dbeda-151">**Pro Windows**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="dbeda-151">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="dbeda-152">**Pro MacOS a Linux**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="dbeda-152">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dbeda-153">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dbeda-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dbeda-154">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="dbeda-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="dbeda-155">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="dbeda-155">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator --version 3.0.0-*
   ```

* <span data-ttu-id="dbeda-156">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="dbeda-156">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="dbeda-157">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="dbeda-157">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dbeda-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbeda-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dbeda-159">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="dbeda-159">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="dbeda-160">*Stránky/filmy*: Vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="dbeda-160">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="dbeda-161">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dbeda-161">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="dbeda-162">Aktualizuj</span><span class="sxs-lookup"><span data-stu-id="dbeda-162">Updated</span></span>

* <span data-ttu-id="dbeda-163">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dbeda-163">*Startup.cs*</span></span>

<span data-ttu-id="dbeda-164">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="dbeda-164">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="dbeda-165">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dbeda-165">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="dbeda-166">Proces generování uživatelského rozhraní vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="dbeda-166">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="dbeda-167">*Stránky/filmy*: Vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="dbeda-167">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="dbeda-168">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="dbeda-168">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="dbeda-169">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="dbeda-169">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dbeda-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbeda-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dbeda-171">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="dbeda-171">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="dbeda-172">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="dbeda-172">Add an initial migration.</span></span>
* <span data-ttu-id="dbeda-173">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="dbeda-173">Update the database with the initial migration.</span></span>

<span data-ttu-id="dbeda-174">V nabídce **nástroje** vyberte možnost > **Správce balíčků NuGet** **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-174">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="dbeda-176">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="dbeda-176">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dbeda-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbeda-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dbeda-178">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dbeda-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="dbeda-179">Předchozí příkazy generují následující upozornění: Pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="dbeda-179">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="dbeda-180">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="dbeda-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="dbeda-181">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="dbeda-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="dbeda-182">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="dbeda-182">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="dbeda-183">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="dbeda-183">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dbeda-184">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="dbeda-184">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="dbeda-185">`InitialCreate` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="dbeda-185">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="dbeda-186">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="dbeda-186">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="dbeda-187">`ef database update` Příkaz spustí `Up` metoda ve *migrace /\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="dbeda-187">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="dbeda-188">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="dbeda-188">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dbeda-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbeda-189">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="dbeda-190">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="dbeda-190">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="dbeda-191">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dbeda-191">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dbeda-192">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="dbeda-192">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="dbeda-193">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="dbeda-193">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="dbeda-194">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="dbeda-194">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="dbeda-195">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="dbeda-195">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="dbeda-196">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="dbeda-196">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="dbeda-197">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="dbeda-197">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="dbeda-198">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="dbeda-198">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="dbeda-199">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="dbeda-199">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="dbeda-200">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="dbeda-200">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="dbeda-201">Předchozí kód vytvoří [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="dbeda-201">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="dbeda-202">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="dbeda-202">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="dbeda-203">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="dbeda-203">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="dbeda-204">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="dbeda-204">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="dbeda-205">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="dbeda-205">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dbeda-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbeda-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dbeda-207">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="dbeda-207">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dbeda-208">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dbeda-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dbeda-209">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="dbeda-209">Examine the `Up` method.</span></span>

---

<span data-ttu-id="dbeda-210">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="dbeda-210">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dbeda-211">Schéma je založen na zadaném v modelu `RazorPagesMovieContext` (v *Data/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="dbeda-211">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="dbeda-212">`Initial` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="dbeda-212">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="dbeda-213">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="dbeda-213">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="dbeda-214">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="dbeda-214">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="dbeda-215">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="dbeda-215">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="dbeda-216">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="dbeda-216">Test the app</span></span>

* <span data-ttu-id="dbeda-217">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="dbeda-217">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="dbeda-218">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="dbeda-218">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="dbeda-219">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="dbeda-219">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="dbeda-220">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="dbeda-220">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="dbeda-222">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="dbeda-222">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="dbeda-223">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="dbeda-223">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="dbeda-224">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="dbeda-224">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="dbeda-225">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="dbeda-225">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="dbeda-226">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="dbeda-226">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbeda-227">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="dbeda-227">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="dbeda-228">[Předchozí Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> další:[ Razor Pages generovaného uživatelského rozhraní](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="dbeda-228">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dbeda-229">V této části jsou třídy přidat pro správu filmy v databázi.</span><span class="sxs-lookup"><span data-stu-id="dbeda-229">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="dbeda-230">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="dbeda-230">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="dbeda-231">EF Core je objektově relační mapování (ORM) platforma, která zjednodušuje kód přístupu k datům.</span><span class="sxs-lookup"><span data-stu-id="dbeda-231">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="dbeda-232">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="dbeda-232">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="dbeda-233">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="dbeda-233">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="dbeda-234">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="dbeda-234">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dbeda-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbeda-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dbeda-236">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-236">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="dbeda-237">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="dbeda-237">Name the folder *Models*.</span></span>

<span data-ttu-id="dbeda-238">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="dbeda-238">Right click the *Models* folder.</span></span> <span data-ttu-id="dbeda-239">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-239">Select **Add** > **Class**.</span></span> <span data-ttu-id="dbeda-240">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-240">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dbeda-241">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbeda-241">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dbeda-242">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="dbeda-242">Add a folder named *Models*.</span></span>
* <span data-ttu-id="dbeda-243">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="dbeda-243">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dbeda-244">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dbeda-244">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dbeda-245">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-245">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="dbeda-246">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="dbeda-246">Name the folder *Models*.</span></span>
* <span data-ttu-id="dbeda-247">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **Přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-247">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="dbeda-248">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="dbeda-248">In the **New File** dialog:</span></span>

  * <span data-ttu-id="dbeda-249">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="dbeda-249">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="dbeda-250">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="dbeda-250">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="dbeda-251">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-251">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="dbeda-252">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="dbeda-252">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="dbeda-253">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="dbeda-253">Scaffold the movie model</span></span>

<span data-ttu-id="dbeda-254">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="dbeda-254">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="dbeda-255">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="dbeda-255">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dbeda-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbeda-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dbeda-257">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="dbeda-257">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="dbeda-258">Klikněte pravým tlačítkem na složku *stránky* > **Přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-258">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="dbeda-259">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="dbeda-259">Name the folder *Movies*</span></span>

<span data-ttu-id="dbeda-260">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-260">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="dbeda-262">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-262">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="dbeda-264">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="dbeda-264">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="dbeda-265">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)** .</span><span class="sxs-lookup"><span data-stu-id="dbeda-265">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="dbeda-266">V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a přijměte vygenerovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-266">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="dbeda-267">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-267">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arp.png)

<span data-ttu-id="dbeda-269">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="dbeda-269">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dbeda-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbeda-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="dbeda-271">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="dbeda-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="dbeda-272">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="dbeda-272">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="dbeda-273">**Pro Windows**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="dbeda-273">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="dbeda-274">**Pro MacOS a Linux**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="dbeda-274">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dbeda-275">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dbeda-275">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dbeda-276">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="dbeda-276">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="dbeda-277">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="dbeda-277">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="dbeda-278">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="dbeda-278">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="dbeda-279">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="dbeda-279">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="dbeda-280">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="dbeda-280">Files created</span></span>

* <span data-ttu-id="dbeda-281">*Stránky/filmy*: Vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="dbeda-281">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="dbeda-282">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="dbeda-282">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="dbeda-283">Aktualizovat soubor</span><span class="sxs-lookup"><span data-stu-id="dbeda-283">File updated</span></span>

* <span data-ttu-id="dbeda-284">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="dbeda-284">*Startup.cs*</span></span>

<span data-ttu-id="dbeda-285">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="dbeda-285">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="dbeda-286">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="dbeda-286">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dbeda-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbeda-287">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dbeda-288">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="dbeda-288">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="dbeda-289">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="dbeda-289">Add an initial migration.</span></span>
* <span data-ttu-id="dbeda-290">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="dbeda-290">Update the database with the initial migration.</span></span>

<span data-ttu-id="dbeda-291">V nabídce **nástroje** vyberte možnost > **Správce balíčků NuGet** **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="dbeda-291">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="dbeda-293">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="dbeda-293">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dbeda-294">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbeda-294">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dbeda-295">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dbeda-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="dbeda-296">Předchozí příkazy generují následující upozornění: Pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="dbeda-296">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="dbeda-297">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="dbeda-297">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="dbeda-298">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="dbeda-298">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="dbeda-299">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="dbeda-299">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="dbeda-300">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="dbeda-300">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dbeda-301">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="dbeda-301">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="dbeda-302">`InitialCreate` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="dbeda-302">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="dbeda-303">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="dbeda-303">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="dbeda-304">`ef database update` Příkaz spustí `Up` metoda ve *migrace /\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="dbeda-304">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="dbeda-305">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="dbeda-305">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="dbeda-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dbeda-306">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="dbeda-307">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="dbeda-307">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="dbeda-308">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="dbeda-308">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="dbeda-309">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="dbeda-309">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="dbeda-310">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="dbeda-310">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="dbeda-311">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="dbeda-311">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="dbeda-312">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="dbeda-312">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="dbeda-313">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="dbeda-313">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="dbeda-314">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="dbeda-314">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="dbeda-315">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="dbeda-315">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="dbeda-316">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="dbeda-316">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="dbeda-317">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="dbeda-317">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="dbeda-318">Předchozí kód vytvoří [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="dbeda-318">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="dbeda-319">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="dbeda-319">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="dbeda-320">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="dbeda-320">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="dbeda-321">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="dbeda-321">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="dbeda-322">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="dbeda-322">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="dbeda-323">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dbeda-323">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dbeda-324">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="dbeda-324">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="dbeda-325">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="dbeda-325">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dbeda-326">Zkontrolujte `Up` metody.</span><span class="sxs-lookup"><span data-stu-id="dbeda-326">Examine the `Up` method.</span></span>

---

<span data-ttu-id="dbeda-327">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="dbeda-327">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="dbeda-328">Schéma je založen na zadaném v modelu `RazorPagesMovieContext` (v *Data/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="dbeda-328">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="dbeda-329">`Initial` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="dbeda-329">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="dbeda-330">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="dbeda-330">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="dbeda-331">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="dbeda-331">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="dbeda-332">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="dbeda-332">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="dbeda-333">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="dbeda-333">Test the app</span></span>

* <span data-ttu-id="dbeda-334">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="dbeda-334">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="dbeda-335">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="dbeda-335">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="dbeda-336">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="dbeda-336">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="dbeda-337">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="dbeda-337">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="dbeda-339">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="dbeda-339">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="dbeda-340">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="dbeda-340">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="dbeda-341">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="dbeda-341">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="dbeda-342">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="dbeda-342">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="dbeda-343">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="dbeda-343">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbeda-344">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="dbeda-344">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="dbeda-345">[Předchozí Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> další:[ Razor Pages generovaného uživatelského rozhraní](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="dbeda-345">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

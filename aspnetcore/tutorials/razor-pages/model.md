---
title: Přidání modelu do aplikace v ASP.NET Core Razor Pages
author: rick-anderson
description: Objevte, jak přidat třídy pro správu filmy v databázi pomocí Entity Framework Core (jádro EF).
ms.author: riande
ms.date: 02/12/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: c7341430e8e2ace7eb04faa308020095139d5b94
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56410229"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="0a761-103">Přidání modelu do aplikace v ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0a761-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="0a761-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0a761-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="0a761-105">V této části jsou třídy přidat pro správu filmy v databázi.</span><span class="sxs-lookup"><span data-stu-id="0a761-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="0a761-106">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="0a761-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="0a761-107">EF Core je objektově relační mapování (ORM) platforma, která zjednodušuje kód přístupu k datům.</span><span class="sxs-lookup"><span data-stu-id="0a761-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="0a761-108">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="0a761-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="0a761-109">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="0a761-109">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="0a761-110">[Zobrazení nebo stažení](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) vzorku.</span><span class="sxs-lookup"><span data-stu-id="0a761-110">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) sample.</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="0a761-111">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="0a761-111">Add a data model</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a761-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a761-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a761-113">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="0a761-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="0a761-114">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="0a761-114">Name the folder *Models*.</span></span>

<span data-ttu-id="0a761-115">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="0a761-115">Right click the *Models* folder.</span></span> <span data-ttu-id="0a761-116">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="0a761-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="0a761-117">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="0a761-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a761-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a761-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0a761-119">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="0a761-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="0a761-120">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="0a761-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0a761-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0a761-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a761-122">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="0a761-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="0a761-123">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="0a761-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="0a761-124">Klikněte pravým tlačítkem myši *modely* složku a pak vyberte **přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="0a761-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="0a761-125">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="0a761-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="0a761-126">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="0a761-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="0a761-127">Vyberte **prázdnou třídu** v prostředním podokně.</span><span class="sxs-lookup"><span data-stu-id="0a761-127">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="0a761-128">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="0a761-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- End of VS tabs -->

---

<span data-ttu-id="0a761-129">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="0a761-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="0a761-130">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="0a761-130">Scaffold the movie model</span></span>

<span data-ttu-id="0a761-131">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="0a761-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="0a761-132">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="0a761-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a761-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a761-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a761-134">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="0a761-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="0a761-135">Klikněte pravým tlačítkem na *stránky* složky > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="0a761-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="0a761-136">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="0a761-136">Name the folder *Movies*</span></span>

<span data-ttu-id="0a761-137">Klikněte pravým tlačítkem na *stránek/filmy* složky > **přidat** > **novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="0a761-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="0a761-139">V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **stránky Razor pomocí Entity Frameworku (CRUD)** > **přidat**.</span><span class="sxs-lookup"><span data-stu-id="0a761-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="0a761-141">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="0a761-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0a761-142">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="0a761-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="0a761-143">V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a přijměte vygenerovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="0a761-143">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="0a761-144">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="0a761-144">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arp.png)

<span data-ttu-id="0a761-146">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="0a761-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a761-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a761-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="0a761-148">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="0a761-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="0a761-149">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="0a761-149">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="0a761-150">**Pro Windows**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0a761-150">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="0a761-151">**Pro macOS a Linux**: Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0a761-151">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0a761-152">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="0a761-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0a761-153">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="0a761-153">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="0a761-154">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="0a761-154">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```
* <span data-ttu-id="0a761-155">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0a761-155">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="0a761-156">Předchozí příkazy Generovat následující upozornění: "Pro desetinných sloupec"Price"na typ entity"Video"nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="0a761-156">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="0a761-157">To způsobí, že hodnoty bylo tiché zkrácení, pokud to není ve výchozí přesnost a měřítko.</span><span class="sxs-lookup"><span data-stu-id="0a761-157">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="0a761-158">Explicitně zadat typ sloupce serveru SQL, který zvládne všech hodnot pomocí "HasColumnType()"."</span><span class="sxs-lookup"><span data-stu-id="0a761-158">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="0a761-159">Můžete tuto upozornění ignorovat, opravíme v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="0a761-159">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="0a761-160">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="0a761-160">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="0a761-161">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="0a761-161">Files created</span></span>

* <span data-ttu-id="0a761-162">*Stránky/filmy*: Vytvoření, odstranění, podrobností, úpravy a Index.</span><span class="sxs-lookup"><span data-stu-id="0a761-162">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="0a761-163">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0a761-163">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="0a761-164">Aktualizovat soubor</span><span class="sxs-lookup"><span data-stu-id="0a761-164">File updated</span></span>

* <span data-ttu-id="0a761-165">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="0a761-165">*Startup.cs*</span></span>

<span data-ttu-id="0a761-166">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="0a761-166">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="0a761-167">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="0a761-167">Initial migration</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a761-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a761-168">Visual Studio</span></span>](#tab/visual-studio)

<!-- VS -------------------------->

<span data-ttu-id="0a761-169">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="0a761-169">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="0a761-170">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="0a761-170">Add an initial migration.</span></span>
* <span data-ttu-id="0a761-171">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="0a761-171">Update the database with the initial migration.</span></span>

<span data-ttu-id="0a761-172">Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="0a761-172">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="0a761-174">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="0a761-174">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a761-175">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a761-175">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0a761-176">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0a761-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="0a761-177">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="0a761-177">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="0a761-178">Schéma je založen na zadaném v modelu `DbContext` (v *RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="0a761-178">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="0a761-179">`InitialCreate` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="0a761-179">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="0a761-180">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="0a761-180">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="0a761-181">`ef database update` Příkaz spustí `Up` metoda ve *migrace /\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="0a761-181">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="0a761-182">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="0a761-182">The `Up` method creates the database.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0a761-183">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a761-183">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0a761-184">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="0a761-184">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0a761-185">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0a761-185">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0a761-186">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a761-186">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0a761-187">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0a761-187">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0a761-188">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="0a761-188">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0a761-189">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0a761-189">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0a761-190">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="0a761-190">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0a761-191">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="0a761-191">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="0a761-192">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="0a761-192">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="0a761-193">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="0a761-193">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0a761-194">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="0a761-194">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="0a761-195">Předchozí kód vytvoří [ `DbSet<Movie>` ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost sady entit.</span><span class="sxs-lookup"><span data-stu-id="0a761-195">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="0a761-196">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="0a761-196">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="0a761-197">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="0a761-197">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0a761-198">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="0a761-198">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0a761-199">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="0a761-199">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>
<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0a761-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a761-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0a761-201">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0a761-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<!-- End of VS tabs -->

---

<span data-ttu-id="0a761-202">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="0a761-202">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="0a761-203">Schéma je založen na zadaném v modelu `RazorPagesMovieContext` (v *Data/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="0a761-203">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="0a761-204">`Initial` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="0a761-204">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="0a761-205">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="0a761-205">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="0a761-206">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="0a761-206">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="0a761-207">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="0a761-207">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="0a761-208">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="0a761-208">Test the app</span></span>

* <span data-ttu-id="0a761-209">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="0a761-209">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="0a761-210">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="0a761-210">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="0a761-211">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="0a761-211">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="0a761-212">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="0a761-212">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)
  
  > [!NOTE]
  > <span data-ttu-id="0a761-214">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="0a761-214">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="0a761-215">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="0a761-215">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="0a761-216">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="0a761-216">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="0a761-217">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="0a761-217">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="0a761-218">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0a761-218">The next tutorial explains the files created by scaffolding.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="0a761-219">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: Vygenerované stránky Razor](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="0a761-219">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

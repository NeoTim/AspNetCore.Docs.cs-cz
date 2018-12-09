---
title: Přidání modelu do aplikace v ASP.NET Core Razor Pages
author: rick-anderson
description: Objevte, jak přidat třídy pro správu filmy v databázi pomocí Entity Framework Core (jádro EF).
ms.author: riande
monikerRange: '>= aspnetcore-2.2'
ms.date: 12/3/2018
uid: tutorials/razor-pages/model
ms.openlocfilehash: 91fee1db820493be671fecaee3cfb4c1b7df8bd3
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121360"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="cff77-103">Přidání modelu do aplikace v ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="cff77-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="cff77-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cff77-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="cff77-105">V této části jsou třídy přidat pro správu filmy v databázi.</span><span class="sxs-lookup"><span data-stu-id="cff77-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="cff77-106">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="cff77-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="cff77-107">EF Core je objektově relační mapování (ORM) platforma, která zjednodušuje kód přístupu k datům.</span><span class="sxs-lookup"><span data-stu-id="cff77-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="cff77-108">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="cff77-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="cff77-109">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="cff77-109">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="cff77-110">[Zobrazení nebo stažení](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages-start/sample/) vzorku.</span><span class="sxs-lookup"><span data-stu-id="cff77-110">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages-start/sample/) sample.</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="cff77-111">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="cff77-111">Add a data model</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cff77-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cff77-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cff77-113">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="cff77-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="cff77-114">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="cff77-114">Name the folder *Models*.</span></span>

<span data-ttu-id="cff77-115">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="cff77-115">Right click the *Models* folder.</span></span> <span data-ttu-id="cff77-116">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="cff77-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="cff77-117">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="cff77-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cff77-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cff77-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cff77-119">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="cff77-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="cff77-120">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="cff77-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cff77-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cff77-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cff77-122">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="cff77-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="cff77-123">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="cff77-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="cff77-124">Klikněte pravým tlačítkem myši *modely* složku a pak vyberte **přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="cff77-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="cff77-125">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="cff77-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="cff77-126">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="cff77-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="cff77-127">Vyberte **prázdnou třídu** v center usnadnit práci.</span><span class="sxs-lookup"><span data-stu-id="cff77-127">Select **Empty Class** in the center pain.</span></span>
  * <span data-ttu-id="cff77-128">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="cff77-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- End of VS tabs -->

---

<span data-ttu-id="cff77-129">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="cff77-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="cff77-130">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="cff77-130">Scaffold the movie model</span></span>

<span data-ttu-id="cff77-131">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="cff77-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="cff77-132">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="cff77-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cff77-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cff77-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cff77-134">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="cff77-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="cff77-135">Klikněte pravým tlačítkem na *stránky* složky > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="cff77-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="cff77-136">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="cff77-136">Name the folder *Movies*</span></span>

<span data-ttu-id="cff77-137">Klikněte pravým tlačítkem na *stránek/filmy* složky > **přidat** > **novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="cff77-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="cff77-139">V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **stránky Razor pomocí Entity Frameworku (CRUD)** > **přidat**.</span><span class="sxs-lookup"><span data-stu-id="cff77-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="cff77-141">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="cff77-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="cff77-142">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="cff77-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="cff77-143">V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a přijměte vygenerovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="cff77-143">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="cff77-144">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="cff77-144">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arp.png)

<span data-ttu-id="cff77-146">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="cff77-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cff77-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cff77-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="cff77-148">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="cff77-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="cff77-149">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="cff77-149">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="cff77-150">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cff77-150">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="cff77-151">**Pro macOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cff77-151">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cff77-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cff77-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="cff77-153">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="cff77-153">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="cff77-154">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="cff77-154">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="cff77-155">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="cff77-155">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="cff77-156">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="cff77-156">Files created</span></span>

* <span data-ttu-id="cff77-157">*Stránky/filmy*: vytvoření, odstranění, podrobností, úpravy a Index.</span><span class="sxs-lookup"><span data-stu-id="cff77-157">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="cff77-158">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="cff77-158">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="cff77-159">Aktualizovat soubor</span><span class="sxs-lookup"><span data-stu-id="cff77-159">File updated</span></span>

* <span data-ttu-id="cff77-160">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="cff77-160">*Startup.cs*</span></span>

<span data-ttu-id="cff77-161">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="cff77-161">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="cff77-162">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="cff77-162">Initial migration</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cff77-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cff77-163">Visual Studio</span></span>](#tab/visual-studio)

<!-- VS -------------------------->

<span data-ttu-id="cff77-164">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="cff77-164">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="cff77-165">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="cff77-165">Add an initial migration.</span></span>
* <span data-ttu-id="cff77-166">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="cff77-166">Update the database with the initial migration.</span></span>

<span data-ttu-id="cff77-167">Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="cff77-167">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="cff77-169">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="cff77-169">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cff77-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cff77-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cff77-171">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cff77-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="cff77-172">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="cff77-172">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="cff77-173">Schéma je založen na zadaném v modelu `DbContext` (v *Models/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="cff77-173">The schema is based on the model specified in the `DbContext` (In the *Models/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="cff77-174">`InitialCreate` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="cff77-174">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="cff77-175">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="cff77-175">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="cff77-176">`ef database update` Příkaz spustí `Up` metoda ve *migrace /\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="cff77-176">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="cff77-177">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="cff77-177">The `Up` method creates the database.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cff77-178">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cff77-178">Visual Studio</span></span>](#tab/visual-studio)

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="cff77-179">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="cff77-179">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="cff77-180">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cff77-180">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cff77-181">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="cff77-181">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="cff77-182">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="cff77-182">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="cff77-183">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="cff77-183">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="cff77-184">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="cff77-184">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="cff77-185">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="cff77-185">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cff77-186">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="cff77-186">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="cff77-187">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="cff77-187">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="cff77-188">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="cff77-188">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="cff77-189">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="cff77-189">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="cff77-190">Předchozí kód vytvoří [DbSet /\<video >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost sady entit.</span><span class="sxs-lookup"><span data-stu-id="cff77-190">The preceding code creates a [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="cff77-191">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="cff77-191">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="cff77-192">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="cff77-192">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="cff77-193">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="cff77-193">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="cff77-194">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="cff77-194">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>
<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cff77-195">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cff77-195">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="cff77-196">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cff77-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<!-- End of VS tabs -->

---

<span data-ttu-id="cff77-197">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="cff77-197">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="cff77-198">Schéma je založen na zadaném v modelu `RazorPagesMovieContext` (v *Data/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="cff77-198">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="cff77-199">`Initial` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="cff77-199">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="cff77-200">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="cff77-200">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="cff77-201">Zobrazit [Úvod do migrace](xref:data/ef-mvc/migrations#introduction-to-migrations) Další informace.</span><span class="sxs-lookup"><span data-stu-id="cff77-201">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="cff77-202">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="cff77-202">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="cff77-203">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="cff77-203">Test the app</span></span>

* <span data-ttu-id="cff77-204">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="cff77-204">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="cff77-205">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="cff77-205">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="cff77-206">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="cff77-206">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="cff77-207">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="cff77-207">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)
  
  > [!NOTE]
  > <span data-ttu-id="cff77-209">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="cff77-209">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="cff77-210">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="cff77-210">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="cff77-211">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="cff77-211">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="cff77-212">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="cff77-212">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="cff77-213">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cff77-213">The next tutorial explains the files created by scaffolding.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cff77-214">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: generované uživatelské rozhraní pro stránky Razor](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="cff77-214">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

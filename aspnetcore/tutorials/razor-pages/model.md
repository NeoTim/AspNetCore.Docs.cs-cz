---
title: Přidání modelu do aplikace v ASP.NET Core Razor Pages
author: rick-anderson
description: Objevte, jak přidat třídy pro správu filmy v databázi pomocí Entity Framework Core (jádro EF).
ms.author: riande
monikerRange: '>= aspnetcore-2.2'
ms.date: 12/3/2018
uid: tutorials/razor-pages/model
ms.openlocfilehash: 3c9b96c8e56672a3c4268d8cd507f30127a812dd
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861469"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="2afb6-103">Přidání modelu do aplikace v ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2afb6-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="2afb6-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2afb6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="2afb6-105">V této části jsou třídy přidat pro správu filmy v databázi.</span><span class="sxs-lookup"><span data-stu-id="2afb6-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="2afb6-106">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="2afb6-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="2afb6-107">EF Core je objektově relační mapování (ORM) platforma, která zjednodušuje kód přístupu k datům.</span><span class="sxs-lookup"><span data-stu-id="2afb6-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="2afb6-108">Modelu třídy jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core.</span><span class="sxs-lookup"><span data-stu-id="2afb6-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2afb6-109">Definují vlastnosti data, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="2afb6-109">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="2afb6-110">[Zobrazení nebo stažení](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages-start/sample/) vzorku.</span><span class="sxs-lookup"><span data-stu-id="2afb6-110">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages-start/sample/) sample.</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="2afb6-111">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="2afb6-111">Add a data model</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2afb6-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2afb6-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2afb6-113">Klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-113">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2afb6-114">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="2afb6-114">Name the folder *Models*.</span></span>

<span data-ttu-id="2afb6-115">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="2afb6-115">Right click the *Models* folder.</span></span> <span data-ttu-id="2afb6-116">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-116">Select **Add** > **Class**.</span></span> <span data-ttu-id="2afb6-117">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2afb6-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2afb6-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2afb6-119">Přidat složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="2afb6-119">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2afb6-120">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="2afb6-120">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2afb6-121">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2afb6-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2afb6-122">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** projektu a pak vyberte **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-122">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="2afb6-123">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="2afb6-123">Name the folder *Models*.</span></span>
* <span data-ttu-id="2afb6-124">Klikněte pravým tlačítkem myši *modely* složku a pak vyberte **přidat** > **nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-124">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="2afb6-125">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="2afb6-125">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2afb6-126">Vyberte **Obecné** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="2afb6-126">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2afb6-127">Vyberte **prázdnou třídu** v center usnadnit práci.</span><span class="sxs-lookup"><span data-stu-id="2afb6-127">Select **Empty Class** in the center pain.</span></span>
  * <span data-ttu-id="2afb6-128">Název třídy **film** a vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-128">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<!-- End of VS tabs -->

---

<span data-ttu-id="2afb6-129">Sestavte projekt a ověřte, že nejsou žádné chyby během kompilace.</span><span class="sxs-lookup"><span data-stu-id="2afb6-129">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2afb6-130">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="2afb6-130">Scaffold the movie model</span></span>

<span data-ttu-id="2afb6-131">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="2afb6-131">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2afb6-132">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="2afb6-132">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2afb6-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2afb6-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2afb6-134">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="2afb6-134">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2afb6-135">Klikněte pravým tlačítkem na *stránky* složky > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-135">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2afb6-136">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="2afb6-136">Name the folder *Movies*</span></span>

<span data-ttu-id="2afb6-137">Klikněte pravým tlačítkem na *stránek/filmy* složky > **přidat** > **novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-137">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="2afb6-139">V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **stránky Razor pomocí Entity Frameworku (CRUD)** > **přidat**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-139">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="2afb6-141">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="2afb6-141">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2afb6-142">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-142">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2afb6-143">V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a přijměte vygenerovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-143">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2afb6-144">Vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-144">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arp.png)

<span data-ttu-id="2afb6-146">*Appsettings.json* souboru aktualizovali připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="2afb6-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2afb6-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2afb6-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2afb6-148">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="2afb6-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="2afb6-149">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2afb6-149">**For Windows**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2afb6-150">**Pro macOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2afb6-150">**For macOS and Linux**: Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc MovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2afb6-151">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2afb6-151">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2afb6-152">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="2afb6-152">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="2afb6-153">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="2afb6-153">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="2afb6-154">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="2afb6-154">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="2afb6-155">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="2afb6-155">Files created</span></span>

* <span data-ttu-id="2afb6-156">*Stránky/filmy*: vytvoření, odstranění, podrobností, úpravy a Index.</span><span class="sxs-lookup"><span data-stu-id="2afb6-156">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2afb6-157">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2afb6-157">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="2afb6-158">Aktualizovat soubor</span><span class="sxs-lookup"><span data-stu-id="2afb6-158">File updated</span></span>

* <span data-ttu-id="2afb6-159">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2afb6-159">*Startup.cs*</span></span>

<span data-ttu-id="2afb6-160">Vytvořený a aktualizované soubory jsou vysvětlené v následující části.</span><span class="sxs-lookup"><span data-stu-id="2afb6-160">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2afb6-161">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="2afb6-161">Initial migration</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2afb6-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2afb6-162">Visual Studio</span></span>](#tab/visual-studio)

<!-- VS -------------------------->

<span data-ttu-id="2afb6-163">V této části konzoly Správce balíčků (PMC) umožňuje:</span><span class="sxs-lookup"><span data-stu-id="2afb6-163">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2afb6-164">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="2afb6-164">Add an initial migration.</span></span>
* <span data-ttu-id="2afb6-165">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="2afb6-165">Update the database with the initial migration.</span></span>

<span data-ttu-id="2afb6-166">Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="2afb6-166">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2afb6-168">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="2afb6-168">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2afb6-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2afb6-169">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2afb6-170">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2afb6-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---  
<!-- End of VS tabs -->

<span data-ttu-id="2afb6-171">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="2afb6-171">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2afb6-172">Schéma je založen na zadaném v modelu `DbContext` (v *Models/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="2afb6-172">The schema is based on the model specified in the `DbContext` (In the *Models/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="2afb6-173">`InitialCreate` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="2afb6-173">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="2afb6-174">Můžete použít libovolný název, ale podle konvence vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="2afb6-174">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="2afb6-175">`ef database update` Příkaz spustí `Up` metoda ve *migrace /\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="2afb6-175">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="2afb6-176">`Up` Metoda vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="2afb6-176">The `Up` method creates the database.</span></span>

<!-- VS -------------------------->

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2afb6-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2afb6-177">Visual Studio</span></span>](#tab/visual-studio)

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2afb6-178">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="2afb6-178">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2afb6-179">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2afb6-179">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2afb6-180">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2afb6-180">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2afb6-181">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="2afb6-181">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2afb6-182">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="2afb6-182">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2afb6-183">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="2afb6-183">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2afb6-184">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="2afb6-184">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2afb6-185">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="2afb6-185">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="2afb6-186">`RazorPagesMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="2afb6-186">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="2afb6-187">Kontext dat (`RazorPagesMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="2afb6-187">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2afb6-188">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="2afb6-188">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2afb6-189">Předchozí kód vytvoří [DbSet /\<video >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost sady entit.</span><span class="sxs-lookup"><span data-stu-id="2afb6-189">The preceding code creates a [DbSet/\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2afb6-190">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="2afb6-190">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2afb6-191">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="2afb6-191">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2afb6-192">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="2afb6-192">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2afb6-193">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="2afb6-193">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>
<!-- Code -------------------------->

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2afb6-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2afb6-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2afb6-195">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2afb6-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<!-- End of VS tabs -->

---

<span data-ttu-id="2afb6-196">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="2afb6-196">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2afb6-197">Schéma je založen na zadaném v modelu `RazorPagesMovieContext` (v *Data/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="2afb6-197">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="2afb6-198">`Initial` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="2afb6-198">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="2afb6-199">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="2afb6-199">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="2afb6-200">Zobrazit [Úvod do migrace](xref:data/ef-mvc/migrations#introduction-to-migrations) Další informace.</span><span class="sxs-lookup"><span data-stu-id="2afb6-200">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="2afb6-201">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="2afb6-201">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2afb6-202">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="2afb6-202">Test the app</span></span>

* <span data-ttu-id="2afb6-203">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="2afb6-203">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2afb6-204">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="2afb6-204">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2afb6-205">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="2afb6-205">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2afb6-206">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="2afb6-206">Test the **Create** link.</span></span>

  ![Vytvoření stránky](model/_static/conan.png)
  
  > [!NOTE]
  > <span data-ttu-id="2afb6-208">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="2afb6-208">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2afb6-209">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="2afb6-209">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2afb6-210">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="2afb6-210">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2afb6-211">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="2afb6-211">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2afb6-212">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2afb6-212">The next tutorial explains the files created by scaffolding.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2afb6-213">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: generované uživatelské rozhraní pro stránky Razor](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2afb6-213">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

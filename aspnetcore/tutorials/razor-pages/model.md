---
title: Přidání modelu do aplikace Razor Pages v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat třídy pro správu filmů v databázi pomocí Entity Framework Core (EF Core).
ms.author: riande
ms.date: 9/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: c101fe4aee9a1fbf28d66a8527e3c199194d73fe
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72334181"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="56e3e-103">Přidání modelu do aplikace Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56e3e-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="56e3e-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="56e3e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="56e3e-105">V této části jsou přidány třídy pro správu filmů v databázi.</span><span class="sxs-lookup"><span data-stu-id="56e3e-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="56e3e-106">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="56e3e-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="56e3e-107">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="56e3e-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="56e3e-108">Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="56e3e-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="56e3e-109">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="56e3e-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="56e3e-110">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="56e3e-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56e3e-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56e3e-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56e3e-112">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="56e3e-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="56e3e-113">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="56e3e-113">Name the folder *Models*.</span></span>

<span data-ttu-id="56e3e-114">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="56e3e-114">Right click the *Models* folder.</span></span> <span data-ttu-id="56e3e-115">Vyberte **Přidat** **třídu** > .</span><span class="sxs-lookup"><span data-stu-id="56e3e-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="56e3e-116">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="56e3e-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56e3e-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e3e-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="56e3e-118">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="56e3e-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="56e3e-119">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="56e3e-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56e3e-120">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="56e3e-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="56e3e-121">V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="56e3e-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="56e3e-122">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="56e3e-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="56e3e-123">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **přidat** **nový soubor**>.</span><span class="sxs-lookup"><span data-stu-id="56e3e-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="56e3e-124">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="56e3e-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="56e3e-125">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="56e3e-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="56e3e-126">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="56e3e-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="56e3e-127">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="56e3e-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="56e3e-128">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.</span><span class="sxs-lookup"><span data-stu-id="56e3e-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="56e3e-129">Generování uživatelského rozhraní modelu filmů</span><span class="sxs-lookup"><span data-stu-id="56e3e-129">Scaffold the movie model</span></span>

<span data-ttu-id="56e3e-130">V této části je model filmu vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="56e3e-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="56e3e-131">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="56e3e-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56e3e-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56e3e-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56e3e-133">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="56e3e-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="56e3e-134">Klikněte pravým tlačítkem na složku *stránky* > **přidat** **novou složku**>.</span><span class="sxs-lookup"><span data-stu-id="56e3e-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="56e3e-135">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="56e3e-135">Name the folder *Movies*</span></span>

<span data-ttu-id="56e3e-136">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** **novou vygenerované položky**>.</span><span class="sxs-lookup"><span data-stu-id="56e3e-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

<span data-ttu-id="56e3e-138">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="56e3e-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

<span data-ttu-id="56e3e-140">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="56e3e-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="56e3e-141">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="56e3e-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="56e3e-142">V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="56e3e-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="56e3e-143">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="56e3e-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="56e3e-144">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="56e3e-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="56e3e-145">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="56e3e-145">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/3/arp.png)

<span data-ttu-id="56e3e-147">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="56e3e-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56e3e-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e3e-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="56e3e-149">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="56e3e-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="56e3e-150">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="56e3e-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="56e3e-151">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="56e3e-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="56e3e-152">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="56e3e-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56e3e-153">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="56e3e-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="56e3e-154">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="56e3e-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="56e3e-155">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="56e3e-155">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="56e3e-156">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="56e3e-156">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="56e3e-157">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="56e3e-157">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56e3e-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56e3e-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56e3e-159">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="56e3e-159">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="56e3e-160">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="56e3e-160">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="56e3e-161">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="56e3e-161">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="56e3e-162">Aktualizuj</span><span class="sxs-lookup"><span data-stu-id="56e3e-162">Updated</span></span>

* <span data-ttu-id="56e3e-163">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="56e3e-163">*Startup.cs*</span></span>

<span data-ttu-id="56e3e-164">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="56e3e-164">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="56e3e-165">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="56e3e-165">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="56e3e-166">Proces generování uživatelského rozhraní vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="56e3e-166">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="56e3e-167">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="56e3e-167">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="56e3e-168">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="56e3e-168">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="56e3e-169">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="56e3e-169">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56e3e-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56e3e-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56e3e-171">V této části se používá konzola správce balíčků (PMC) k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="56e3e-171">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="56e3e-172">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="56e3e-172">Add an initial migration.</span></span>
* <span data-ttu-id="56e3e-173">Aktualizujte databázi pomocí prvotní migrace.</span><span class="sxs-lookup"><span data-stu-id="56e3e-173">Update the database with the initial migration.</span></span>

<span data-ttu-id="56e3e-174">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="56e3e-174">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="56e3e-176">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="56e3e-176">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56e3e-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e3e-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56e3e-178">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="56e3e-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="56e3e-179">Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '.</span><span class="sxs-lookup"><span data-stu-id="56e3e-179">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="56e3e-180">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="56e3e-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="56e3e-181">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="56e3e-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="56e3e-182">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="56e3e-182">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="56e3e-183">Příkaz `ef migrations add InitialCreate` generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="56e3e-183">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="56e3e-184">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="56e3e-184">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="56e3e-185">K pojmenování migrace se používá argument `InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-185">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="56e3e-186">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="56e3e-186">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="56e3e-187">Příkaz `ef database update` spustí metodu `Up` v souboru *migrations/\<time-razítko > _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="56e3e-187">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="56e3e-188">Metoda `Up` vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="56e3e-188">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56e3e-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56e3e-189">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="56e3e-190">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="56e3e-190">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="56e3e-191">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="56e3e-191">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="56e3e-192">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="56e3e-192">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="56e3e-193">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="56e3e-193">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="56e3e-194">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="56e3e-194">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="56e3e-195">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="56e3e-195">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="56e3e-196">Projděte si metodu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-196">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="56e3e-197">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="56e3e-197">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="56e3e-198">Souřadnice `RazorPagesMovieContext` EF Core funkce (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-198">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="56e3e-199">Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="56e3e-199">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="56e3e-200">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="56e3e-200">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="56e3e-201">Předchozí kód vytvoří vlastnost [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="56e3e-201">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="56e3e-202">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="56e3e-202">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="56e3e-203">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="56e3e-203">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="56e3e-204">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="56e3e-204">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="56e3e-205">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="56e3e-205">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56e3e-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e3e-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="56e3e-207">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-207">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56e3e-208">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="56e3e-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="56e3e-209">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-209">Examine the `Up` method.</span></span>

---

<span data-ttu-id="56e3e-210">Příkaz `Add-Migration` generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="56e3e-210">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="56e3e-211">Schéma je založené na modelu určeném v `RazorPagesMovieContext` (v souboru *data/RazorPagesMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="56e3e-211">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="56e3e-212">K pojmenování migrace se používá argument `Initial`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-212">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="56e3e-213">Můžete použít libovolný název, ale podle konvence je použit název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="56e3e-213">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="56e3e-214">Další informace najdete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="56e3e-214">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="56e3e-215">Příkaz `Update-Database` spustí metodu `Up` v souboru *migrations/{Time-razítk} _InitialCreate. cs* , ve kterém se vytvoří databáze.</span><span class="sxs-lookup"><span data-stu-id="56e3e-215">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="56e3e-216">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="56e3e-216">Test the app</span></span>

* <span data-ttu-id="56e3e-217">Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="56e3e-217">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="56e3e-218">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="56e3e-218">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="56e3e-219">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="56e3e-219">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="56e3e-220">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="56e3e-220">Test the **Create** link.</span></span>

  ![Vytvořit stránku](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="56e3e-222">V poli `Price` možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="56e3e-222">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="56e3e-223">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="56e3e-223">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="56e3e-224">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="56e3e-224">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="56e3e-225">Otestujte odkazy **Upravit**, **Podrobnosti**a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="56e3e-225">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="56e3e-226">Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="56e3e-226">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56e3e-227">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="56e3e-227">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="56e3e-228">[Předchozí:](xref:tutorials/razor-pages/razor-pages-start)začátek 
> [Další: vygenerované Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="56e3e-228">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="56e3e-229">V této části jsou přidány třídy pro správu filmů v databázi.</span><span class="sxs-lookup"><span data-stu-id="56e3e-229">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="56e3e-230">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="56e3e-230">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="56e3e-231">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="56e3e-231">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="56e3e-232">Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="56e3e-232">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="56e3e-233">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="56e3e-233">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="56e3e-234">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="56e3e-234">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56e3e-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56e3e-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56e3e-236">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="56e3e-236">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="56e3e-237">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="56e3e-237">Name the folder *Models*.</span></span>

<span data-ttu-id="56e3e-238">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="56e3e-238">Right click the *Models* folder.</span></span> <span data-ttu-id="56e3e-239">Vyberte **Přidat** **třídu** > .</span><span class="sxs-lookup"><span data-stu-id="56e3e-239">Select **Add** > **Class**.</span></span> <span data-ttu-id="56e3e-240">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="56e3e-240">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56e3e-241">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e3e-241">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="56e3e-242">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="56e3e-242">Add a folder named *Models*.</span></span>
* <span data-ttu-id="56e3e-243">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="56e3e-243">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56e3e-244">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="56e3e-244">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="56e3e-245">V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="56e3e-245">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="56e3e-246">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="56e3e-246">Name the folder *Models*.</span></span>
* <span data-ttu-id="56e3e-247">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **přidat** **nový soubor**>.</span><span class="sxs-lookup"><span data-stu-id="56e3e-247">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="56e3e-248">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="56e3e-248">In the **New File** dialog:</span></span>

  * <span data-ttu-id="56e3e-249">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="56e3e-249">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="56e3e-250">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="56e3e-250">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="56e3e-251">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="56e3e-251">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="56e3e-252">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.</span><span class="sxs-lookup"><span data-stu-id="56e3e-252">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="56e3e-253">Generování uživatelského rozhraní modelu filmů</span><span class="sxs-lookup"><span data-stu-id="56e3e-253">Scaffold the movie model</span></span>

<span data-ttu-id="56e3e-254">V této části je model filmu vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="56e3e-254">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="56e3e-255">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="56e3e-255">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56e3e-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56e3e-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56e3e-257">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="56e3e-257">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="56e3e-258">Klikněte pravým tlačítkem na složku *stránky* > **přidat** **novou složku**>.</span><span class="sxs-lookup"><span data-stu-id="56e3e-258">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="56e3e-259">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="56e3e-259">Name the folder *Movies*</span></span>

<span data-ttu-id="56e3e-260">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** **novou vygenerované položky**>.</span><span class="sxs-lookup"><span data-stu-id="56e3e-260">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

<span data-ttu-id="56e3e-262">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="56e3e-262">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

<span data-ttu-id="56e3e-264">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="56e3e-264">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="56e3e-265">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="56e3e-265">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="56e3e-266">V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a přijměte vygenerovaný název **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="56e3e-266">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="56e3e-267">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="56e3e-267">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/arp.png)

<span data-ttu-id="56e3e-269">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="56e3e-269">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56e3e-270">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e3e-270">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="56e3e-271">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="56e3e-271">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="56e3e-272">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="56e3e-272">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="56e3e-273">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="56e3e-273">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="56e3e-274">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="56e3e-274">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56e3e-275">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="56e3e-275">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="56e3e-276">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="56e3e-276">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="56e3e-277">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="56e3e-277">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="56e3e-278">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="56e3e-278">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="56e3e-279">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="56e3e-279">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="56e3e-280">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="56e3e-280">Files created</span></span>

* <span data-ttu-id="56e3e-281">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="56e3e-281">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="56e3e-282">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="56e3e-282">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="56e3e-283">Soubor aktualizován</span><span class="sxs-lookup"><span data-stu-id="56e3e-283">File updated</span></span>

* <span data-ttu-id="56e3e-284">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="56e3e-284">*Startup.cs*</span></span>

<span data-ttu-id="56e3e-285">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="56e3e-285">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="56e3e-286">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="56e3e-286">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56e3e-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56e3e-287">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="56e3e-288">V této části se používá konzola správce balíčků (PMC) k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="56e3e-288">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="56e3e-289">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="56e3e-289">Add an initial migration.</span></span>
* <span data-ttu-id="56e3e-290">Aktualizujte databázi pomocí prvotní migrace.</span><span class="sxs-lookup"><span data-stu-id="56e3e-290">Update the database with the initial migration.</span></span>

<span data-ttu-id="56e3e-291">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="56e3e-291">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="56e3e-293">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="56e3e-293">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56e3e-294">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e3e-294">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56e3e-295">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="56e3e-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="56e3e-296">Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '.</span><span class="sxs-lookup"><span data-stu-id="56e3e-296">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="56e3e-297">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="56e3e-297">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="56e3e-298">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="56e3e-298">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="56e3e-299">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="56e3e-299">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="56e3e-300">Příkaz `ef migrations add InitialCreate` generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="56e3e-300">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span> <span data-ttu-id="56e3e-301">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="56e3e-301">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="56e3e-302">K pojmenování migrace se používá argument `InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-302">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="56e3e-303">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="56e3e-303">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="56e3e-304">Příkaz `ef database update` spustí metodu `Up` v souboru *migrations/\<time-razítko > _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="56e3e-304">The `ef database update` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="56e3e-305">Metoda `Up` vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="56e3e-305">The `Up` method creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56e3e-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56e3e-306">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="56e3e-307">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="56e3e-307">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="56e3e-308">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="56e3e-308">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="56e3e-309">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="56e3e-309">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="56e3e-310">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="56e3e-310">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="56e3e-311">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="56e3e-311">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="56e3e-312">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="56e3e-312">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="56e3e-313">Projděte si metodu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-313">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="56e3e-314">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="56e3e-314">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="56e3e-315">Souřadnice `RazorPagesMovieContext` EF Core funkce (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-315">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="56e3e-316">Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="56e3e-316">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="56e3e-317">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="56e3e-317">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="56e3e-318">Předchozí kód vytvoří vlastnost [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="56e3e-318">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="56e3e-319">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="56e3e-319">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="56e3e-320">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="56e3e-320">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="56e3e-321">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="56e3e-321">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="56e3e-322">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="56e3e-322">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56e3e-323">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e3e-323">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="56e3e-324">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-324">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56e3e-325">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="56e3e-325">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="56e3e-326">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-326">Examine the `Up` method.</span></span>

---

<span data-ttu-id="56e3e-327">Příkaz `Add-Migration` generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="56e3e-327">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="56e3e-328">Schéma je založené na modelu určeném v `RazorPagesMovieContext` (v souboru *data/RazorPagesMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="56e3e-328">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="56e3e-329">K pojmenování migrace se používá argument `Initial`.</span><span class="sxs-lookup"><span data-stu-id="56e3e-329">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="56e3e-330">Můžete použít libovolný název, ale podle konvence je použit název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="56e3e-330">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="56e3e-331">Další informace najdete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="56e3e-331">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="56e3e-332">Příkaz `Update-Database` spustí metodu `Up` v souboru *migrations/{Time-razítk} _InitialCreate. cs* , ve kterém se vytvoří databáze.</span><span class="sxs-lookup"><span data-stu-id="56e3e-332">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="56e3e-333">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="56e3e-333">Test the app</span></span>

* <span data-ttu-id="56e3e-334">Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="56e3e-334">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="56e3e-335">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="56e3e-335">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="56e3e-336">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="56e3e-336">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="56e3e-337">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="56e3e-337">Test the **Create** link.</span></span>

  ![Vytvořit stránku](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="56e3e-339">V poli `Price` možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="56e3e-339">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="56e3e-340">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="56e3e-340">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="56e3e-341">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="56e3e-341">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="56e3e-342">Otestujte odkazy **Upravit**, **Podrobnosti**a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="56e3e-342">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="56e3e-343">Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="56e3e-343">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56e3e-344">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="56e3e-344">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="56e3e-345">[Předchozí:](xref:tutorials/razor-pages/razor-pages-start)začátek 
> [Další: vygenerované Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="56e3e-345">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

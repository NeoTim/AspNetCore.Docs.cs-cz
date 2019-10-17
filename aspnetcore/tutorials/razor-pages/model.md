---
title: Přidání modelu do aplikace Razor Pages v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat třídy pro správu filmů v databázi pomocí Entity Framework Core (EF Core).
ms.author: riande
ms.date: 9/22/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 4f8b80cb51bd10eb3b136a780dc123c41d61c0a5
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519075"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="4b1c5-103">Přidání modelu do aplikace Razor Pages v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4b1c5-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="4b1c5-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4b1c5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4b1c5-105">V této části jsou přidány třídy pro správu filmů v databázi.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-105">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="4b1c5-106">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-106">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="4b1c5-107">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-107">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="4b1c5-108">Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="4b1c5-109">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="4b1c5-110">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="4b1c5-110">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b1c5-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b1c5-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b1c5-112">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="4b1c5-113">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-113">Name the folder *Models*.</span></span>

<span data-ttu-id="4b1c5-114">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-114">Right click the *Models* folder.</span></span> <span data-ttu-id="4b1c5-115">Vyberte **Přidat** **třídu** > .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="4b1c5-116">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b1c5-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b1c5-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4b1c5-118">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="4b1c5-119">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b1c5-120">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4b1c5-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b1c5-121">V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-121">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="4b1c5-122">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-122">Name the folder *Models*.</span></span>
* <span data-ttu-id="4b1c5-123">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **přidat** **nový soubor**>.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-123">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="4b1c5-124">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="4b1c5-124">In the **New File** dialog:</span></span>

  * <span data-ttu-id="4b1c5-125">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-125">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="4b1c5-126">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-126">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="4b1c5-127">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-127">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="4b1c5-128">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-128">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="4b1c5-129">Generování uživatelského rozhraní modelu filmů</span><span class="sxs-lookup"><span data-stu-id="4b1c5-129">Scaffold the movie model</span></span>

<span data-ttu-id="4b1c5-130">V této části je model filmu vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-130">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="4b1c5-131">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-131">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b1c5-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b1c5-132">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b1c5-133">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="4b1c5-133">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="4b1c5-134">Klikněte pravým tlačítkem na složku *stránky* > **přidat** **novou složku**>.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-134">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="4b1c5-135">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="4b1c5-135">Name the folder *Movies*</span></span>

<span data-ttu-id="4b1c5-136">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** **novou vygenerované položky**>.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-136">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

<span data-ttu-id="4b1c5-138">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-138">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

<span data-ttu-id="4b1c5-140">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="4b1c5-140">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="4b1c5-141">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-141">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="4b1c5-142">V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a změňte vygenerovaný název z RazorPagesMovie. **Modely**. RazorPagesMovieContext na RazorPagesMovie. **Data**. RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-142">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="4b1c5-143">[Tato změna](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) se nevyžaduje.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-143">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="4b1c5-144">Vytvoří třídu kontextu databáze se správným oborem názvů.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-144">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="4b1c5-145">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-145">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/3/arp.png)

<span data-ttu-id="4b1c5-147">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-147">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b1c5-148">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b1c5-148">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="4b1c5-149">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-149">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4b1c5-150">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-150">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4b1c5-151">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-151">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="4b1c5-152">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-152">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b1c5-153">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4b1c5-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b1c5-154">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-154">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4b1c5-155">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-155">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4b1c5-156">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-156">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

### <a name="files-created"></a><span data-ttu-id="4b1c5-157">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="4b1c5-157">Files created</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b1c5-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b1c5-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b1c5-159">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-159">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="4b1c5-160">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-160">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="4b1c5-161">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="4b1c5-161">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="4b1c5-162">Aktualizuj</span><span class="sxs-lookup"><span data-stu-id="4b1c5-162">Updated</span></span>

* <span data-ttu-id="4b1c5-163">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="4b1c5-163">*Startup.cs*</span></span>

<span data-ttu-id="4b1c5-164">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-164">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="4b1c5-165">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4b1c5-165">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4b1c5-166">Proces generování uživatelského rozhraní vytvoří následující soubory:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-166">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="4b1c5-167">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-167">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="4b1c5-168">Vytvořené soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-168">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="4b1c5-169">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="4b1c5-169">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b1c5-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b1c5-170">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b1c5-171">V této části se používá konzola správce balíčků (PMC) k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-171">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="4b1c5-172">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-172">Add an initial migration.</span></span>
* <span data-ttu-id="4b1c5-173">Aktualizujte databázi pomocí prvotní migrace.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-173">Update the database with the initial migration.</span></span>

<span data-ttu-id="4b1c5-174">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-174">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="4b1c5-176">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-176">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b1c5-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b1c5-177">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b1c5-178">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4b1c5-178">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="4b1c5-179">Předchozí příkazy generují následující upozornění: pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-179">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="4b1c5-180">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-180">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="4b1c5-181">Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-181">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="4b1c5-182">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-182">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="4b1c5-183">Příkaz migrace generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-183">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="4b1c5-184">Schéma je založené na modelu určeném v `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-184">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="4b1c5-185">K pojmenování migrace se používá argument `InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-185">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="4b1c5-186">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-186">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="4b1c5-187">Příkaz `update` spustí metodu `Up` v migracích, které nebyly aplikovány.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-187">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="4b1c5-188">V tomto případě `update` spustí metodu `Up` v souboru *migrations/\<time >-_InitialCreate. cs* , ve kterém se vytvoří databáze.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-188">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b1c5-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b1c5-189">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="4b1c5-190">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="4b1c5-190">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="4b1c5-191">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-191">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4b1c5-192">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-192">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="4b1c5-193">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-193">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="4b1c5-194">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-194">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="4b1c5-195">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-195">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="4b1c5-196">Projděte si metodu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-196">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4b1c5-197">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-197">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="4b1c5-198">Souřadnice `RazorPagesMovieContext` EF Core funkce (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-198">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="4b1c5-199">Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-199">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="4b1c5-200">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-200">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="4b1c5-201">Předchozí kód vytvoří vlastnost [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-201">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="4b1c5-202">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-202">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="4b1c5-203">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-203">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="4b1c5-204">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-204">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="4b1c5-205">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-205">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b1c5-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b1c5-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4b1c5-207">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-207">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b1c5-208">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4b1c5-208">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4b1c5-209">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-209">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="4b1c5-210">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="4b1c5-210">Test the app</span></span>

* <span data-ttu-id="4b1c5-211">Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-211">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="4b1c5-212">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-212">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="4b1c5-213">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-213">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="4b1c5-214">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-214">Test the **Create** link.</span></span>

  ![Vytvořit stránku](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="4b1c5-216">V poli `Price` možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-216">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="4b1c5-217">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-217">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="4b1c5-218">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-218">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="4b1c5-219">Otestujte odkazy **Upravit**, **Podrobnosti**a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-219">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="4b1c5-220">Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-220">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b1c5-221">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4b1c5-221">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4b1c5-222">[Předchozí:](xref:tutorials/razor-pages/razor-pages-start)začátek 
> [Další: vygenerované Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="4b1c5-222">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4b1c5-223">V této části jsou přidány třídy pro správu filmů v databázi.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-223">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="4b1c5-224">Tyto třídy se používají s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-224">These classes are used with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="4b1c5-225">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje přístup k datům.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-225">EF Core is an object-relational mapping (ORM) framework that simplifies data access code.</span></span>

<span data-ttu-id="4b1c5-226">Třídy modelu se označují jako třídy POCO (z "objektů CLR v prostém Old"), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-226">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="4b1c5-227">Definují vlastnosti dat, která jsou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-227">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="4b1c5-228">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="4b1c5-228">Add a data model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b1c5-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b1c5-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b1c5-230">Klikněte pravým tlačítkem na projekt **RazorPagesMovie** > **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-230">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="4b1c5-231">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-231">Name the folder *Models*.</span></span>

<span data-ttu-id="4b1c5-232">Klikněte pravým tlačítkem na složku *modely* .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-232">Right click the *Models* folder.</span></span> <span data-ttu-id="4b1c5-233">Vyberte **Přidat** **třídu** > .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-233">Select **Add** > **Class**.</span></span> <span data-ttu-id="4b1c5-234">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-234">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b1c5-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b1c5-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4b1c5-236">Přidejte složku s názvem *modely*.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-236">Add a folder named *Models*.</span></span>
* <span data-ttu-id="4b1c5-237">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-237">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b1c5-238">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4b1c5-238">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b1c5-239">V Průzkumník řešení klikněte pravým tlačítkem na projekt **RazorPagesMovie** a vyberte **přidat** **novou složku** > .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-239">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="4b1c5-240">Pojmenujte *modely*složek.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-240">Name the folder *Models*.</span></span>
* <span data-ttu-id="4b1c5-241">Klikněte pravým tlačítkem na složku *modely* a pak vyberte **přidat** **nový soubor**>.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-241">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="4b1c5-242">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="4b1c5-242">In the **New File** dialog:</span></span>

  * <span data-ttu-id="4b1c5-243">V levém podokně vyberte **Obecné** .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-243">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="4b1c5-244">V prostředním podokně vyberte **prázdnou třídu** .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-244">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="4b1c5-245">Pojmenujte **film** třídy a vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-245">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

---

<span data-ttu-id="4b1c5-246">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilace.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-246">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="4b1c5-247">Generování uživatelského rozhraní modelu filmů</span><span class="sxs-lookup"><span data-stu-id="4b1c5-247">Scaffold the movie model</span></span>

<span data-ttu-id="4b1c5-248">V této části je model filmu vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-248">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="4b1c5-249">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-249">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b1c5-250">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b1c5-250">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b1c5-251">Vytvořit *stránky nebo složky filmů* :</span><span class="sxs-lookup"><span data-stu-id="4b1c5-251">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="4b1c5-252">Klikněte pravým tlačítkem na složku *stránky* > **přidat** **novou složku**>.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-252">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="4b1c5-253">Pojmenovat *filmy* ve složce</span><span class="sxs-lookup"><span data-stu-id="4b1c5-253">Name the folder *Movies*</span></span>

<span data-ttu-id="4b1c5-254">Klikněte pravým tlačítkem na složku *stránky nebo filmy* > **Přidat** **novou vygenerované položky**>.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-254">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/sca.png)

<span data-ttu-id="4b1c5-256">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-256">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/add_scaffold.png)

<span data-ttu-id="4b1c5-258">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="4b1c5-258">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="4b1c5-259">V rozevíracím seznamu **třída modelu** vyberte **video (RazorPagesMovie. Models)** .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-259">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="4b1c5-260">V řádku **Třída kontextu dat** vyberte znaménko **+** (plus) a přijměte vygenerovaný název **RazorPagesMovie. Models. RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-260">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="4b1c5-261">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-261">Select **Add**.</span></span>

![Obrázek z předchozích instrukcí.](model/_static/arp.png)

<span data-ttu-id="4b1c5-263">Soubor *appSettings. JSON* se aktualizuje připojovacím řetězcem, který se používá pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-263">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b1c5-264">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b1c5-264">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="4b1c5-265">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-265">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4b1c5-266">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-266">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4b1c5-267">**Pro Windows**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-267">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="4b1c5-268">**Pro MacOS a Linux**: spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-268">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b1c5-269">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4b1c5-269">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="4b1c5-270">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-270">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="4b1c5-271">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-271">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="4b1c5-272">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-272">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<span data-ttu-id="4b1c5-273">Proces generování uživatelského rozhraní vytváří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-273">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="4b1c5-274">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="4b1c5-274">Files created</span></span>

* <span data-ttu-id="4b1c5-275">*Stránky/filmy*: vytvořit, odstranit, podrobnosti, upravit a index.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-275">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="4b1c5-276">*Data/RazorPagesMovieContext. cs*</span><span class="sxs-lookup"><span data-stu-id="4b1c5-276">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="4b1c5-277">Soubor aktualizován</span><span class="sxs-lookup"><span data-stu-id="4b1c5-277">File updated</span></span>

* <span data-ttu-id="4b1c5-278">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="4b1c5-278">*Startup.cs*</span></span>

<span data-ttu-id="4b1c5-279">Vytvořené a aktualizované soubory jsou vysvětleny v další části.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-279">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="4b1c5-280">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="4b1c5-280">Initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b1c5-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b1c5-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4b1c5-282">V této části se používá konzola správce balíčků (PMC) k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-282">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="4b1c5-283">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-283">Add an initial migration.</span></span>
* <span data-ttu-id="4b1c5-284">Aktualizujte databázi pomocí prvotní migrace.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-284">Update the database with the initial migration.</span></span>

<span data-ttu-id="4b1c5-285">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-285">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC – nabídka](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="4b1c5-287">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-287">In the PMC, enter the following commands:</span></span>

```Powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="4b1c5-288">Příkaz `Add-Migration` generuje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-288">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="4b1c5-289">Schéma je založené na modelu určeném v `DbContext` (v souboru *RazorPagesMovieContext.cs* ).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-289">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="4b1c5-290">K pojmenování migrace se používá argument `InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-290">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="4b1c5-291">Můžete použít libovolný název, ale podle konvence je použit název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-291">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="4b1c5-292">Další informace najdete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-292">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="4b1c5-293">Příkaz `Update-Database` spustí metodu `Up` v souboru *migrations/\<time-razítko > _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-293">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="4b1c5-294">Metoda `Up` vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-294">The `Up` method creates the database.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b1c5-295">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b1c5-295">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b1c5-296">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4b1c5-296">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="4b1c5-297">Předchozí příkazy generují následující upozornění:*pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ ' Price '. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.* Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-297">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4b1c5-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4b1c5-298">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="4b1c5-299">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="4b1c5-299">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="4b1c5-300">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-300">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4b1c5-301">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-301">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="4b1c5-302">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-302">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="4b1c5-303">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-303">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="4b1c5-304">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-304">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="4b1c5-305">Projděte si metodu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-305">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4b1c5-306">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-306">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="4b1c5-307">Souřadnice `RazorPagesMovieContext` EF Core funkce (vytváření, čtení, aktualizace, odstranění atd.) pro model `Movie`.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-307">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="4b1c5-308">Kontext dat (`RazorPagesMovieContext`) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-308">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="4b1c5-309">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-309">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="4b1c5-310">Předchozí kód vytvoří vlastnost [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-310">The preceding code creates a [`DbSet<Movie>`](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="4b1c5-311">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-311">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="4b1c5-312">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-312">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="4b1c5-313">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-313">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="4b1c5-314">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-314">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4b1c5-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4b1c5-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4b1c5-316">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-316">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4b1c5-317">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4b1c5-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4b1c5-318">Projděte si metodu `Up`.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-318">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="4b1c5-319">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="4b1c5-319">Test the app</span></span>

* <span data-ttu-id="4b1c5-320">Spusťte aplikaci a přidejte `/Movies` k adrese URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-320">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="4b1c5-321">Pokud se zobrazí chyba:</span><span class="sxs-lookup"><span data-stu-id="4b1c5-321">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="4b1c5-322">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-322">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="4b1c5-323">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-323">Test the **Create** link.</span></span>

  ![Vytvořit stránku](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="4b1c5-325">V poli `Price` možná nebudete moct zadat desítkové čárky.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-325">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="4b1c5-326">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-326">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="4b1c5-327">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="4b1c5-327">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="4b1c5-328">Otestujte odkazy **Upravit**, **Podrobnosti**a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="4b1c5-328">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="4b1c5-329">Další kurz vysvětluje soubory vytvořené pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4b1c5-329">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4b1c5-330">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4b1c5-330">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4b1c5-331">[Předchozí:](xref:tutorials/razor-pages/razor-pages-start)začátek 
> [Další: vygenerované Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="4b1c5-331">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

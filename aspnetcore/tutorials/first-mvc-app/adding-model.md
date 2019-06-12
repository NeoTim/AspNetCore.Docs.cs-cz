---
title: Přidání modelu pro aplikace ASP.NET Core MVC
author: rick-anderson
description: Přidání modelu pro jednoduchou aplikaci ASP.NET Core.
ms.author: riande
ms.date: 02/25/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fcfe97e2228c21ffea8fe7ec5df8e2ea9c398828
ms.sourcegitcommit: 1bb3f3f1905b4e7d4ca1b314f2ce6ee5dd8be75f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2019
ms.locfileid: "66837329"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="14926-103">Přidání modelu pro aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="14926-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="14926-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="14926-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="14926-105">V této části přidáte třídy pro správu filmy v databázi.</span><span class="sxs-lookup"><span data-stu-id="14926-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="14926-106">Tyto třídy bude "**M**odelu" součástí **M**VC aplikace.</span><span class="sxs-lookup"><span data-stu-id="14926-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="14926-107">Použití těchto tříd s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="14926-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="14926-108">EF Core je objektově relační mapování (ORM) platforma, která zjednodušuje kód přístupu k datům, který musíte napsat.</span><span class="sxs-lookup"><span data-stu-id="14926-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="14926-109">Třídy modelu vytvoříte jsou označovány jako POCO třídy (od **P**rostý **O**ld **C**LR **O**bjekty) vzhledem k tomu, že nemají žádné závislosti EF Core.</span><span class="sxs-lookup"><span data-stu-id="14926-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="14926-110">Právě definují vlastnosti data, která se uloží v databázi.</span><span class="sxs-lookup"><span data-stu-id="14926-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="14926-111">V tomto kurzu nejprve napsat tříd modelu a EF Core vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="14926-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="14926-112">Alternativní není součástí tohoto přístupu je k vytvoření tříd modelu z existující databáze.</span><span class="sxs-lookup"><span data-stu-id="14926-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="14926-113">Informace o tento přístup, najdete v části [ASP.NET Core – existující databáze](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="14926-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

## <a name="add-a-data-model-class"></a><span data-ttu-id="14926-114">Přidejte třídu modelu dat</span><span class="sxs-lookup"><span data-stu-id="14926-114">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14926-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14926-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14926-116">Klikněte pravým tlačítkem myši *modely* složky > **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="14926-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="14926-117">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="14926-117">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="14926-118">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="14926-118">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="14926-119">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="14926-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="14926-120">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="14926-120">Scaffold the movie model</span></span>

<span data-ttu-id="14926-121">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="14926-121">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="14926-122">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="14926-122">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14926-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14926-123">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14926-124">V **Průzkumníka řešení**, klikněte pravým tlačítkem myši *řadiče* složky **> Přidat > novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="14926-124">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Přehled nad krok](adding-model/_static/add_controller21.png)

<span data-ttu-id="14926-126">V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **kontroler MVC se zobrazeními, s použitím Entity Framework > Přidat**.</span><span class="sxs-lookup"><span data-stu-id="14926-126">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Přidat vygenerované uživatelské rozhraní – dialogové okno](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="14926-128">Dokončení **přidat kontroler** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="14926-128">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="14926-129">**Třída modelu:** *Video (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="14926-129">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="14926-130">**Třída kontextu dat:** Vyberte **+** ikonu a přidejte výchozí **MvcMovie.Models.MvcMovieContext**</span><span class="sxs-lookup"><span data-stu-id="14926-130">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Přidat kontext dat](adding-model/_static/dc.png)

* <span data-ttu-id="14926-132">**Zobrazení:** Ponechte výchozí hodnotu každého zaškrtnutým políčkem</span><span class="sxs-lookup"><span data-stu-id="14926-132">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="14926-133">**Název kontroleru:** Ponechte výchozí *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="14926-133">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="14926-134">Vyberte **přidat**</span><span class="sxs-lookup"><span data-stu-id="14926-134">Select **Add**</span></span>

![Přidat dialog Kontroleru](adding-model/_static/add_controller2.png)

<span data-ttu-id="14926-136">Visual Studio vytvoří:</span><span class="sxs-lookup"><span data-stu-id="14926-136">Visual Studio creates:</span></span>

* <span data-ttu-id="14926-137">Entity Framework Core [třídy kontextu databáze](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="14926-137">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="14926-138">Kontroler filmy (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="14926-138">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="14926-139">Zobrazení Razor soubory vytvořit, odstranit, podrobností, úpravy a Index stránky (*zobrazení/filmy/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="14926-139">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="14926-140">Automatické vytváření kontext databáze a [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (vytvoření, čtení, aktualizace a odstranění) metody akce a zobrazení se označuje jako *generování uživatelského rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="14926-140">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="14926-141">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="14926-141">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="14926-142">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="14926-142">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="14926-143">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="14926-143">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="14926-144">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="14926-144">Run the following command:</span></span>

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="14926-145">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="14926-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="14926-146">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="14926-146">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="14926-147">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="14926-147">Install the scaffolding tool:</span></span>

  ```console
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="14926-148">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="14926-148">Run the following command:</span></span>

  ```console
     dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="14926-149">Pokud spustíte aplikaci a klikněte na **Mvc Movie** odkaz, dojde k chybě podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="14926-149">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

<span data-ttu-id="14926-150">Je potřeba vytvořit databázi, a pomocí EF Core [migrace](xref:data/ef-mvc/migrations) funkce, které provedete.</span><span class="sxs-lookup"><span data-stu-id="14926-150">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="14926-151">Migrace vám umožní vytvořit databázi, která odpovídá datového modelu a aktualizovat schéma databáze, pokud datový model změny.</span><span class="sxs-lookup"><span data-stu-id="14926-151">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="14926-152">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="14926-152">Initial migration</span></span>

<span data-ttu-id="14926-153">V této části jsou prováděny následující úlohy:</span><span class="sxs-lookup"><span data-stu-id="14926-153">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="14926-154">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="14926-154">Add an initial migration.</span></span>
* <span data-ttu-id="14926-155">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="14926-155">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14926-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14926-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="14926-157">Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="14926-157">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC nabídky](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="14926-159">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="14926-159">In the PMC, enter the following commands:</span></span>

   ```console
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="14926-160">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="14926-160">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="14926-161">Schéma databáze je založeno na zadaném v modelu `MvcMovieContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="14926-161">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="14926-162">`Initial` Argument je název migrace.</span><span class="sxs-lookup"><span data-stu-id="14926-162">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="14926-163">Můžete použít libovolný název, ale podle konvence je název, který popisuje migraci použít.</span><span class="sxs-lookup"><span data-stu-id="14926-163">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="14926-164">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="14926-164">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="14926-165">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="14926-165">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="14926-166">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="14926-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="14926-167">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="14926-167">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="14926-168">Schéma databáze je založeno na zadaném v modelu `MvcMovieContext` třídy (v *Data/MvcMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="14926-168">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="14926-169">`InitialCreate` Argument je název migrace.</span><span class="sxs-lookup"><span data-stu-id="14926-169">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="14926-170">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="14926-170">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="14926-171">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="14926-171">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="14926-172">ASP.NET Core využívá rozhraní [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="14926-172">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="14926-173">Služby (například kontext EF Core databáze) jsou registrovány DI během spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="14926-173">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="14926-174">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="14926-174">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="14926-175">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="14926-175">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14926-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14926-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14926-177">Nástroj pro generování uživatelského rozhraní automaticky vytvořen kontext databáze a kontejnerů DI zaregistrován.</span><span class="sxs-lookup"><span data-stu-id="14926-177">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="14926-178">Zkontrolujte následující `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="14926-178">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="14926-179">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="14926-179">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="14926-180">`MvcMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="14926-180">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="14926-181">Kontext dat (`MvcMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="14926-181">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="14926-182">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu:</span><span class="sxs-lookup"><span data-stu-id="14926-182">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Data/MvcMovieContext.cs)]

<span data-ttu-id="14926-183">Předchozí kód vytvoří [DbSet\<video >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost sady entit.</span><span class="sxs-lookup"><span data-stu-id="14926-183">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="14926-184">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="14926-184">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="14926-185">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="14926-185">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="14926-186">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="14926-186">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="14926-187">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="14926-187">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="14926-188">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="14926-188">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="14926-189">Vytvoří kontext databáze a kontejnerů DI zaregistrován.</span><span class="sxs-lookup"><span data-stu-id="14926-189">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="14926-190">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="14926-190">Test the app</span></span>

* <span data-ttu-id="14926-191">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="14926-191">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="14926-192">Pokud dojde k výjimce databáze podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="14926-192">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="14926-193">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="14926-193">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="14926-194">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="14926-194">Test the **Create** link.</span></span> <span data-ttu-id="14926-195">Zadejte a odesílat data.</span><span class="sxs-lookup"><span data-stu-id="14926-195">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="14926-196">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="14926-196">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="14926-197">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="14926-197">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="14926-198">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="14926-198">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="14926-199">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="14926-199">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="14926-200">Zkontrolujte `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="14926-200">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="14926-201">Předchozí zvýrazněný kód ukazuje kontext databáze filmů přidávaný do [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru:</span><span class="sxs-lookup"><span data-stu-id="14926-201">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="14926-202">`services.AddDbContext<MvcMovieContext>(options =>` Určuje databázi, do použití a připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="14926-202">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="14926-203">`=>` je [lambda – operátor](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="14926-203">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="14926-204">Otevřít *Controllers/MoviesController.cs* souboru a prozkoumání konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="14926-204">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="14926-205">Konstruktor používá [injektáž závislostí](xref:fundamentals/dependency-injection) vkládat kontext databáze (`MvcMovieContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="14926-205">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="14926-206">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="14926-206">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="14926-207">Modely se silnými typy a @model – klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="14926-207">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="14926-208">Dříve v tomto kurzu jste viděli, jak kontroleru můžete předat data nebo objekty zobrazení pomocí `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="14926-208">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="14926-209">`ViewData` Slovník je dynamický objekt, který představuje pohodlný způsob s pozdní vazbou k předávání informací do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="14926-209">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="14926-210">MVC rovněž poskytuje možnost předávání silně typované objekty modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="14926-210">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="14926-211">Tento přístup silného typu umožňuje lepší čas kompilace Kontrola kódu.</span><span class="sxs-lookup"><span data-stu-id="14926-211">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="14926-212">Generování uživatelského rozhraní mechanismus použít tento přístup (který předává model silného typu) se `MoviesController` třídy a zobrazení při vytvoření rovnou metody a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="14926-212">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="14926-213">Zkontrolujte vygenerovaný `Details` metodu *Controllers/MoviesController.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="14926-213">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="14926-214">`id` Jako data trasy, která je obecně předán parametr.</span><span class="sxs-lookup"><span data-stu-id="14926-214">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="14926-215">Například `https://localhost:5001/movies/details/1` nastaví:</span><span class="sxs-lookup"><span data-stu-id="14926-215">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="14926-216">Kontroleru `movies` kontroler (první segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="14926-216">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="14926-217">Akce, která má `details` (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="14926-217">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="14926-218">Id na hodnotu 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="14926-218">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="14926-219">Můžete také předat `id` pomocí dotazu řetězec následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="14926-219">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="14926-220">`id` Parametr je definován jako [typ připouštějící hodnotu Null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) v případě, že je hodnota ID není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="14926-220">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="14926-221">A [výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) předáno pracovnímu `FirstOrDefaultAsync` vyberte film entity, které odpovídají směrování dat nebo dotaz řetězcovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="14926-221">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="14926-222">Pokud se najde videa, instance `Movie` modelu je předán `Details` zobrazení:</span><span class="sxs-lookup"><span data-stu-id="14926-222">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="14926-223">Zkontrolovat obsah *Views/Movies/Details.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="14926-223">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="14926-224">Zahrnutím `@model` příkazu v horní části souboru zobrazení, můžete určit typ objektu, který očekává, že zobrazení.</span><span class="sxs-lookup"><span data-stu-id="14926-224">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="14926-225">Při vytvoření kontroleru film, následující `@model` příkaz byl automaticky zahrnut v horní části *Details.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="14926-225">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="14926-226">To `@model` – direktiva umožňuje přístup k video, které kontroleru předána do zobrazení podle používání `Model` objekt, který je silně typováno.</span><span class="sxs-lookup"><span data-stu-id="14926-226">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="14926-227">Například v *Details.cshtml* zobrazení, že kód předá jednotlivých polí filmů a `DisplayNameFor` a `DisplayFor` pomocných rutin HTML se silnými typy `Model` objektu.</span><span class="sxs-lookup"><span data-stu-id="14926-227">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="14926-228">`Create` a `Edit` metody a zobrazení také předat `Movie` objekt modelu.</span><span class="sxs-lookup"><span data-stu-id="14926-228">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="14926-229">Zkontrolujte *Index.cshtml* zobrazení a `Index` metoda v filmy kontroleru.</span><span class="sxs-lookup"><span data-stu-id="14926-229">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="14926-230">Všimněte si, jak kód vytvoří `List` objektu při volání `View` metody.</span><span class="sxs-lookup"><span data-stu-id="14926-230">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="14926-231">Kód předá to `Movies` ze seznamu `Index` metody akce k zobrazení:</span><span class="sxs-lookup"><span data-stu-id="14926-231">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="14926-232">Když vytvoříte řadič filmy, generování uživatelského rozhraní automaticky zahrnuty následující `@model` příkazu v horní části *Index.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="14926-232">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="14926-233">`@model` – Direktiva umožňuje přístup k seznamu filmy, které kontroleru předána do zobrazení podle používání `Model` objekt, který je silně typováno.</span><span class="sxs-lookup"><span data-stu-id="14926-233">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="14926-234">Například v *Index.cshtml* zobrazíte kód cyklicky projde filmů s `foreach` příkaz přes silného typu `Model` objektu:</span><span class="sxs-lookup"><span data-stu-id="14926-234">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="14926-235">Protože `Model` objektu je silně typováno (jako `IEnumerable<Movie>` objektu), každé položky ve smyčce je zadán jako `Movie`.</span><span class="sxs-lookup"><span data-stu-id="14926-235">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="14926-236">Kromě dalších výhod to znamená, že dostanete kompilace Kontrola kódu:</span><span class="sxs-lookup"><span data-stu-id="14926-236">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14926-237">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="14926-237">Additional resources</span></span>

* [<span data-ttu-id="14926-238">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="14926-238">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="14926-239">Globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="14926-239">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="14926-240">[Předchozím přidávání zobrazení](adding-view.md)
> [další práce s SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="14926-240">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

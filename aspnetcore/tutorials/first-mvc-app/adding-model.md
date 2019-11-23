---
title: Přidání modelu do ASP.NET Core aplikace MVC
author: rick-anderson
description: Přidejte model do jednoduché aplikace ASP.NET Core.
ms.author: riande
ms.date: 8/15/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 2fac37e7069fb2a464d4de1da8912197f7adf8a8
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761088"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="1380d-103">Přidání modelu do ASP.NET Core aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="1380d-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="1380d-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="1380d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="1380d-105">V této části přidáte třídy pro správu filmů v databázi.</span><span class="sxs-lookup"><span data-stu-id="1380d-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="1380d-106">Tyto třídy budou součástí aplikace typu **m**VC jako "**m**Odel".</span><span class="sxs-lookup"><span data-stu-id="1380d-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="1380d-107">Tyto třídy použijete s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="1380d-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="1380d-108">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje kód pro přístup k datům, který je nutné zapsat.</span><span class="sxs-lookup"><span data-stu-id="1380d-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="1380d-109">Třídy modelů, které vytvoříte, jsou známé jako třídy POCO (od **P**Lain **O**ld **C**LR **O**bjekty), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="1380d-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="1380d-110">Pouze definují vlastnosti dat, která budou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="1380d-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="1380d-111">V tomto kurzu napíšete nejprve třídy modelu a EF Core vytvoříte databázi.</span><span class="sxs-lookup"><span data-stu-id="1380d-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="1380d-112">Alternativním způsobem, který není pokrytý, je vygenerování tříd modelu z existující databáze.</span><span class="sxs-lookup"><span data-stu-id="1380d-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="1380d-113">Informace o tomto přístupu najdete v tématu [ASP.NET Core – existující databáze](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="1380d-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="1380d-114">Přidat třídu datového modelu</span><span class="sxs-lookup"><span data-stu-id="1380d-114">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1380d-116">Klikněte pravým tlačítkem na složku *modely* > **Přidat** **třídu** > .</span><span class="sxs-lookup"><span data-stu-id="1380d-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="1380d-117">Název souboru *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="1380d-117">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1380d-118">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-118">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1380d-119">Do složky *modely* přidejte soubor s názvem *Movie.cs* .</span><span class="sxs-lookup"><span data-stu-id="1380d-119">Add a file named *Movie.cs* to the *Models* folder.</span></span>

---

<span data-ttu-id="1380d-120">Aktualizujte soubor *Movie.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="1380d-120">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="1380d-121">Třída `Movie` obsahuje pole `Id`, které databáze vyžaduje pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="1380d-121">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="1380d-122">Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) v `ReleaseDate` určuje typ dat (`Date`).</span><span class="sxs-lookup"><span data-stu-id="1380d-122">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="1380d-123">S tímto atributem:</span><span class="sxs-lookup"><span data-stu-id="1380d-123">With this attribute:</span></span>

  * <span data-ttu-id="1380d-124">Uživatel není požádán o zadání informací o čase do pole datum.</span><span class="sxs-lookup"><span data-stu-id="1380d-124">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="1380d-125">Zobrazí se pouze datum, nejedná se o informace o čase.</span><span class="sxs-lookup"><span data-stu-id="1380d-125">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="1380d-126">V pozdějším kurzu jsou uvedena tato [Anotace](/dotnet/api/system.componentmodel.dataannotations) .</span><span class="sxs-lookup"><span data-stu-id="1380d-126">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="1380d-127">Přidat balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="1380d-127">Add NuGet packages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-128">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1380d-129">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="1380d-129">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC nabídky](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="1380d-131">V PMC spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1380d-131">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="1380d-132">Předchozí příkaz přidá poskytovatele EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="1380d-132">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="1380d-133">Balíček Provider nainstaluje balíček EF Core jako závislost.</span><span class="sxs-lookup"><span data-stu-id="1380d-133">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="1380d-134">Další balíčky jsou automaticky nainstalovány v kroku generování uživatelského rozhraní později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="1380d-134">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1380d-135">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-135">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="1380d-136">Vytvoření třídy kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="1380d-136">Create a database context class</span></span>

<span data-ttu-id="1380d-137">Třída kontextu databáze je nutná ke koordinaci funkcí EF Core (vytvoření, čtení, aktualizace, odstranění) pro model `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1380d-137">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="1380d-138">Kontext databáze je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a určuje entity, které mají být zahrnuty do datového modelu.</span><span class="sxs-lookup"><span data-stu-id="1380d-138">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="1380d-139">Vytvořte složku *dat* .</span><span class="sxs-lookup"><span data-stu-id="1380d-139">Create a *Data* folder.</span></span>

<span data-ttu-id="1380d-140">Přidejte soubor *data/MvcMovieContext. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="1380d-140">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="1380d-141">Předchozí kód vytvoří vlastnost [negenerickými\<Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="1380d-141">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="1380d-142">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="1380d-142">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="1380d-143">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="1380d-143">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="1380d-144">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="1380d-144">Register the database context</span></span>

<span data-ttu-id="1380d-145">ASP.NET Core je sestaven s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1380d-145">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1380d-146">Služby (například kontext EF Core DB) musí být při spuštění aplikace zaregistrované v DI.</span><span class="sxs-lookup"><span data-stu-id="1380d-146">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="1380d-147">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="1380d-147">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1380d-148">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="1380d-148">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="1380d-149">V této části zaregistrujete kontext databáze pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="1380d-149">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="1380d-150">Do horní části *Startup.cs*přidejte následující příkazy `using`:</span><span class="sxs-lookup"><span data-stu-id="1380d-150">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="1380d-151">Do `Startup.ConfigureServices`přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="1380d-151">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-152">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-152">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1380d-153">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-153">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="1380d-154">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="1380d-154">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1380d-155">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="1380d-155">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="1380d-156">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="1380d-156">Add a database connection string</span></span>

<span data-ttu-id="1380d-157">Přidejte do souboru *appSettings. JSON* připojovací řetězec:</span><span class="sxs-lookup"><span data-stu-id="1380d-157">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1380d-159">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="1380d-160">Sestavte projekt jako kontrolu chyb kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="1380d-160">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="1380d-161">Stránky filmového uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="1380d-161">Scaffold movie pages</span></span>

<span data-ttu-id="1380d-162">Použijte nástroj pro generování uživatelského rozhraní k vytvoření stránek pro vytváření, čtení, aktualizaci a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="1380d-162">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1380d-164">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Controllers* **> přidat > novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="1380d-164">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![zobrazení výše uvedeného kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="1380d-166">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **kontroler MVC se zobrazeními a pomocí Entity Framework > Přidat**.</span><span class="sxs-lookup"><span data-stu-id="1380d-166">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogové okno Přidat generování uživatelského rozhraní](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="1380d-168">Dokončete dialog **Přidat řadič** :</span><span class="sxs-lookup"><span data-stu-id="1380d-168">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="1380d-169">**Třída modelu:** *video (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="1380d-169">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="1380d-170">**Třída kontextu dat:** *MvcMovieContext (MvcMovie. data)*</span><span class="sxs-lookup"><span data-stu-id="1380d-170">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Přidat kontext dat](adding-model/_static/dc3.png)

* <span data-ttu-id="1380d-172">**Zobrazení:** Ponechte výchozí hodnotu u každé zaškrtnuté možnosti.</span><span class="sxs-lookup"><span data-stu-id="1380d-172">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="1380d-173">**Název kontroleru:** Zachovat výchozí *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="1380d-173">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="1380d-174">Vyberte **Přidat**</span><span class="sxs-lookup"><span data-stu-id="1380d-174">Select **Add**</span></span>

<span data-ttu-id="1380d-175">Visual Studio vytvoří:</span><span class="sxs-lookup"><span data-stu-id="1380d-175">Visual Studio creates:</span></span>

* <span data-ttu-id="1380d-176">Řadič filmů (*Controllers/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="1380d-176">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="1380d-177">Soubory zobrazení Razor pro stránky vytvořit, odstranit, podrobnosti, upravit a index (*zobrazení/filmy/\*. cshtml*)</span><span class="sxs-lookup"><span data-stu-id="1380d-177">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="1380d-178">Automatické vytváření těchto souborů se říká *generování uživatelského rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="1380d-178">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1380d-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1380d-179">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="1380d-180">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="1380d-180">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="1380d-181">V systému Linux exportujte cestu k nástroji pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="1380d-181">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="1380d-182">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1380d-182">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1380d-183">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1380d-184">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="1380d-184">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="1380d-185">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1380d-185">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="1380d-186">Vygenerované stránky nemůžete zatím použít, protože databáze neexistuje.</span><span class="sxs-lookup"><span data-stu-id="1380d-186">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="1380d-187">Pokud aplikaci spouštíte a kliknete na odkaz **filmové aplikace** , *nemůžete otevřít databázi* nebo *žádnou takovou tabulku:* chybová zpráva videa.</span><span class="sxs-lookup"><span data-stu-id="1380d-187">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="1380d-188">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="1380d-188">Initial migration</span></span>

<span data-ttu-id="1380d-189">K vytvoření databáze použijte funkci [migrace](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="1380d-189">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="1380d-190">Migrace je sada nástrojů, která umožňuje vytvořit a aktualizovat databázi tak, aby odpovídala vašemu datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="1380d-190">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1380d-192">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="1380d-192">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="1380d-193">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="1380d-193">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="1380d-194">`Add-Migration InitialCreate`: vygeneruje migrační soubor *_InitialCreate. cs migrace/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="1380d-194">`Add-Migration InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="1380d-195">Argument `InitialCreate` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="1380d-195">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="1380d-196">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="1380d-196">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="1380d-197">Vzhledem k tomu, že se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="1380d-197">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="1380d-198">Schéma databáze je založené na modelu určeném ve třídě `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="1380d-198">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="1380d-199">`Update-Database`: aktualizuje databázi na nejnovější migraci, která vytvořila předchozí příkaz.</span><span class="sxs-lookup"><span data-stu-id="1380d-199">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="1380d-200">Tento příkaz spustí metodu `Up` v souboru *migrations/{Time-razítk} _InitialCreate. cs* , ve kterém se vytvoří databáze.</span><span class="sxs-lookup"><span data-stu-id="1380d-200">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="1380d-201">Příkaz aktualizace databáze generuje následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="1380d-201">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="1380d-202">Pro desetinný sloupec ' Price ' pro typ entity ' film ' nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="1380d-202">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="1380d-203">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="1380d-203">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="1380d-204">Explicitně zadejte typ sloupce SQL Server, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="1380d-204">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="1380d-205">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="1380d-205">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1380d-206">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-206">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1380d-207">Spusťte následující příkazy .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="1380d-207">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="1380d-208">`ef migrations add InitialCreate`: vygeneruje migrační soubor *_InitialCreate. cs migrace/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="1380d-208">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="1380d-209">Argument `InitialCreate` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="1380d-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="1380d-210">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="1380d-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="1380d-211">Vzhledem k tomu, že se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="1380d-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="1380d-212">Schéma databáze je založené na modelu určeném ve třídě `MvcMovieContext` (v souboru *data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="1380d-212">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="1380d-213">`ef database update`: aktualizuje databázi na nejnovější migraci, která vytvořila předchozí příkaz.</span><span class="sxs-lookup"><span data-stu-id="1380d-213">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="1380d-214">Tento příkaz spustí metodu `Up` v souboru *migrations/{Time-razítk} _InitialCreate. cs* , ve kterém se vytvoří databáze.</span><span class="sxs-lookup"><span data-stu-id="1380d-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [ more information on the CLI tools for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="1380d-215">Třída InitialCreate</span><span class="sxs-lookup"><span data-stu-id="1380d-215">The InitialCreate class</span></span>

<span data-ttu-id="1380d-216">Projděte si soubor migrace */{timestamp} _InitialCreate. cs* :</span><span class="sxs-lookup"><span data-stu-id="1380d-216">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

 <span data-ttu-id="1380d-217">Metoda `Up` vytvoří tabulku filmů a nakonfiguruje `Id` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="1380d-217">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="1380d-218">Metoda `Down` vrátí změny schématu provedené migrací `Up`.</span><span class="sxs-lookup"><span data-stu-id="1380d-218">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="1380d-219">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="1380d-219">Test the app</span></span>

* <span data-ttu-id="1380d-220">Spusťte aplikaci a klikněte na odkaz **video aplikace** .</span><span class="sxs-lookup"><span data-stu-id="1380d-220">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="1380d-221">Pokud se zobrazí výjimka podobná jedné z následujících:</span><span class="sxs-lookup"><span data-stu-id="1380d-221">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-222">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1380d-223">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="1380d-224">Pravděpodobně jste [Krok migrace](#migration)vynechali.</span><span class="sxs-lookup"><span data-stu-id="1380d-224">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="1380d-225">Otestujte stránku **vytvořit** .</span><span class="sxs-lookup"><span data-stu-id="1380d-225">Test the **Create** page.</span></span> <span data-ttu-id="1380d-226">Zadejte a odešlete data.</span><span class="sxs-lookup"><span data-stu-id="1380d-226">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="1380d-227">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="1380d-227">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1380d-228">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="1380d-228">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="1380d-229">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="1380d-229">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="1380d-230">Otestujte stránky **Upravit**, **Podrobnosti**a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="1380d-230">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="1380d-231">Vkládání závislostí v kontroleru</span><span class="sxs-lookup"><span data-stu-id="1380d-231">Dependency injection in the controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-232">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1380d-233">Otevřete soubor *Controllers/MoviesController. cs* a prověřte konstruktor:</span><span class="sxs-lookup"><span data-stu-id="1380d-233">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="1380d-234">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze (`MvcMovieContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1380d-234">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="1380d-235">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1380d-235">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1380d-236">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="1380d-237">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze (`MvcMovieContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1380d-237">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="1380d-238">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1380d-238">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="1380d-239">Modely silného typu a klíčové slovo @model</span><span class="sxs-lookup"><span data-stu-id="1380d-239">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="1380d-240">Dříve v tomto kurzu jste viděli, jak může řadič předat data nebo objekty do zobrazení pomocí `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="1380d-240">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="1380d-241">`ViewData` slovníku je dynamický objekt, který poskytuje pohodlný způsob, jak předat informace zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1380d-241">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="1380d-242">MVC také poskytuje možnost předat objekty modelu silného typu do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1380d-242">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="1380d-243">Tento přístup se silnými typy umožňuje kompilovat kontrolu kódu při kompilaci.</span><span class="sxs-lookup"><span data-stu-id="1380d-243">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="1380d-244">Mechanizmus pro generování uživatelského rozhraní používal tento přístup (to znamená předání modelu silného typu) s `MoviesController` třídou a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="1380d-244">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="1380d-245">Projděte si vygenerovanou metodu `Details` v souboru *Controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="1380d-245">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="1380d-246">Parametr `id` je obecně předán jako data směrování.</span><span class="sxs-lookup"><span data-stu-id="1380d-246">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="1380d-247">Například `https://localhost:5001/movies/details/1` sady:</span><span class="sxs-lookup"><span data-stu-id="1380d-247">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="1380d-248">Kontroler řadiče `movies` (první segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="1380d-248">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="1380d-249">Akce, která se má `details` (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="1380d-249">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="1380d-250">ID na 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="1380d-250">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="1380d-251">`id` můžete předat také pomocí řetězce dotazu následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="1380d-251">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="1380d-252">Parametr `id` je definován jako typ s [možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) pro případ, že není zadána hodnota ID.</span><span class="sxs-lookup"><span data-stu-id="1380d-252">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="1380d-253">[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán do `FirstOrDefaultAsync` pro výběr entit videa, které odpovídají datům směrování nebo hodnotě řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="1380d-253">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="1380d-254">Pokud je nalezen film, instance `Movie`ho modelu je předána do zobrazení `Details`:</span><span class="sxs-lookup"><span data-stu-id="1380d-254">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="1380d-255">Projděte si obsah souboru *views/video/details. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1380d-255">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="1380d-256">Příkaz `@model` v horní části souboru zobrazení určuje typ objektu, který zobrazení očekává.</span><span class="sxs-lookup"><span data-stu-id="1380d-256">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="1380d-257">Po vytvoření kontroleru filmů byl zahrnut následující příkaz `@model`:</span><span class="sxs-lookup"><span data-stu-id="1380d-257">When the movie controller was created, the following `@model` statement was included:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="1380d-258">Tato direktiva `@model` umožňuje přístup k videu, který kontroler předali do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1380d-258">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="1380d-259">Objekt `Model` je silného typu.</span><span class="sxs-lookup"><span data-stu-id="1380d-259">The `Model` object is strongly typed.</span></span> <span data-ttu-id="1380d-260">Například v zobrazení *Details. cshtml* kód předá každé pole videa do `DisplayNameFor` a `DisplayFor` pomocníkům HTML pomocí silně typovaného objektu `Model`.</span><span class="sxs-lookup"><span data-stu-id="1380d-260">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="1380d-261">Metody a zobrazení `Create` a `Edit` také předají objekt `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="1380d-261">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="1380d-262">Prohlédněte si zobrazení *index. cshtml* a `Index` metoda v kontroleru filmů.</span><span class="sxs-lookup"><span data-stu-id="1380d-262">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="1380d-263">Všimněte si, jak kód při volání metody `View` vytvoří objekt `List`.</span><span class="sxs-lookup"><span data-stu-id="1380d-263">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="1380d-264">Kód předá tento seznam `Movies` z metody `Index` akce do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="1380d-264">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="1380d-265">Po vytvoření kontroleru filmů zahrnuje generování uživatelského rozhraní následující příkaz `@model` v horní části souboru *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1380d-265">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="1380d-266">Direktiva `@model` umožňuje přístup k seznamu filmů, které kontroler předává do zobrazení, pomocí silně typovaného objektu `Model`.</span><span class="sxs-lookup"><span data-stu-id="1380d-266">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="1380d-267">Například v zobrazení *index. cshtml* kód cykly projde pomocí příkazu `foreach` v rámci silně typovaného `Model` objektu:</span><span class="sxs-lookup"><span data-stu-id="1380d-267">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="1380d-268">Vzhledem k tomu, že objekt `Model` je silného typu (jako objekt `IEnumerable<Movie>`), jsou všechny položky ve smyčce zadány jako `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1380d-268">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="1380d-269">Kromě jiných výhod to znamená, že se vám bude zobrazovat doba kompilace kódu.</span><span class="sxs-lookup"><span data-stu-id="1380d-269">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1380d-270">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="1380d-270">Additional resources</span></span>

* [<span data-ttu-id="1380d-271">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="1380d-271">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="1380d-272">Globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="1380d-272">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="1380d-273">[Předchozí přidání zobrazení](adding-view.md)
> [Další práce s SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="1380d-273">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="1380d-274">Přidat třídu datového modelu</span><span class="sxs-lookup"><span data-stu-id="1380d-274">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-275">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1380d-276">Klikněte pravým tlačítkem na složku *modely* > **Přidat** **třídu** > .</span><span class="sxs-lookup"><span data-stu-id="1380d-276">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="1380d-277">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="1380d-277">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1380d-278">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="1380d-279">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="1380d-279">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="1380d-280">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="1380d-280">Scaffold the movie model</span></span>

<span data-ttu-id="1380d-281">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="1380d-281">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="1380d-282">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="1380d-282">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-283">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1380d-284">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Controllers* **> přidat > novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="1380d-284">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![zobrazení výše uvedeného kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="1380d-286">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **kontroler MVC se zobrazeními a pomocí Entity Framework > Přidat**.</span><span class="sxs-lookup"><span data-stu-id="1380d-286">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogové okno Přidat generování uživatelského rozhraní](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="1380d-288">Dokončete dialog **Přidat řadič** :</span><span class="sxs-lookup"><span data-stu-id="1380d-288">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="1380d-289">**Třída modelu:** *video (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="1380d-289">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="1380d-290">**Třída kontextu dat:** Vyberte ikonu **+** a přidejte výchozí **MvcMovie. Models. MvcMovieContext** .</span><span class="sxs-lookup"><span data-stu-id="1380d-290">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Přidat kontext dat](adding-model/_static/dc.png)

* <span data-ttu-id="1380d-292">**Zobrazení:** Ponechte výchozí hodnotu u každé zaškrtnuté možnosti.</span><span class="sxs-lookup"><span data-stu-id="1380d-292">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="1380d-293">**Název kontroleru:** Zachovat výchozí *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="1380d-293">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="1380d-294">Vyberte **Přidat**</span><span class="sxs-lookup"><span data-stu-id="1380d-294">Select **Add**</span></span>

![Dialogové okno Přidat řadič](adding-model/_static/add_controller2.png)

<span data-ttu-id="1380d-296">Visual Studio vytvoří:</span><span class="sxs-lookup"><span data-stu-id="1380d-296">Visual Studio creates:</span></span>

* <span data-ttu-id="1380d-297">[Třída kontextu databáze](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*data/MvcMovieContext. cs*)</span><span class="sxs-lookup"><span data-stu-id="1380d-297">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="1380d-298">Řadič filmů (*Controllers/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="1380d-298">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="1380d-299">Soubory zobrazení Razor pro stránky vytvořit, odstranit, podrobnosti, upravit a index (*zobrazení/filmy/\*. cshtml*)</span><span class="sxs-lookup"><span data-stu-id="1380d-299">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="1380d-300">K automatickému vytvoření kontextu databáze a operací [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (vytvoření, čtení, aktualizace a odstranění) se říká *generování uživatelského rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="1380d-300">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1380d-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1380d-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="1380d-302">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="1380d-302">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="1380d-303">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="1380d-303">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="1380d-304">V systému Linux exportujte cestu k nástroji pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="1380d-304">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="1380d-305">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1380d-305">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1380d-306">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-306">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1380d-307">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="1380d-307">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="1380d-308">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="1380d-308">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="1380d-309">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1380d-309">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="1380d-310">Pokud aplikaci spouštíte a kliknete na **filmový odkaz MVC** , zobrazí se chybová zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="1380d-310">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-311">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-311">Visual Studio</span></span>](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1380d-312">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-312">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="1380d-313">Musíte vytvořit databázi a k tomu použijte funkci [migrace](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="1380d-313">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="1380d-314">Migrace vám umožní vytvořit databázi, která odpovídá vašemu datovému modelu, a aktualizovat schéma databáze při změně datového modelu.</span><span class="sxs-lookup"><span data-stu-id="1380d-314">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="1380d-315">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="1380d-315">Initial migration</span></span>

<span data-ttu-id="1380d-316">V této části jsou dokončeny následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="1380d-316">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="1380d-317">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="1380d-317">Add an initial migration.</span></span>
* <span data-ttu-id="1380d-318">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="1380d-318">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-319">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="1380d-320">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="1380d-320">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC nabídky](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="1380d-322">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="1380d-322">In the PMC, enter the following commands:</span></span>

   ```PMC
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="1380d-323">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="1380d-323">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="1380d-324">Schéma databáze je založené na modelu určeném ve třídě `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="1380d-324">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="1380d-325">Argument `Initial` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="1380d-325">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="1380d-326">Můžete použít libovolný název, ale podle konvence se použije název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="1380d-326">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="1380d-327">Další informace najdete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="1380d-327">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="1380d-328">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="1380d-328">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1380d-329">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-329">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="1380d-330">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="1380d-330">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="1380d-331">Schéma databáze je založené na modelu určeném ve třídě `MvcMovieContext` (v souboru *data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="1380d-331">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="1380d-332">Argument `InitialCreate` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="1380d-332">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="1380d-333">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="1380d-333">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="1380d-334">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="1380d-334">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="1380d-335">ASP.NET Core je sestaven s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="1380d-335">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1380d-336">Služby (například kontext EF Core DB) jsou během spuštění aplikace zaregistrované v DI.</span><span class="sxs-lookup"><span data-stu-id="1380d-336">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="1380d-337">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="1380d-337">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1380d-338">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="1380d-338">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1380d-339">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1380d-339">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1380d-340">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="1380d-340">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="1380d-341">Projděte si následující metodu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1380d-341">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1380d-342">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="1380d-342">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="1380d-343">`MvcMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="1380d-343">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="1380d-344">Kontext dat (`MvcMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="1380d-344">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1380d-345">Kontext dat určuje, které entity jsou v datovém modelu zahrnuté:</span><span class="sxs-lookup"><span data-stu-id="1380d-345">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="1380d-346">Předchozí kód vytvoří vlastnost [negenerickými\<Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="1380d-346">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="1380d-347">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="1380d-347">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="1380d-348">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="1380d-348">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1380d-349">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="1380d-349">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1380d-350">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="1380d-350">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1380d-351">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1380d-351">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1380d-352">Vytvořili jste kontext databáze a zaregistrovali jej pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="1380d-352">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="1380d-353">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="1380d-353">Test the app</span></span>

* <span data-ttu-id="1380d-354">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="1380d-354">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="1380d-355">Pokud získáte výjimku databáze podobnou následující:</span><span class="sxs-lookup"><span data-stu-id="1380d-355">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="1380d-356">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="1380d-356">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="1380d-357">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="1380d-357">Test the **Create** link.</span></span> <span data-ttu-id="1380d-358">Zadejte a odešlete data.</span><span class="sxs-lookup"><span data-stu-id="1380d-358">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="1380d-359">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="1380d-359">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="1380d-360">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="1380d-360">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="1380d-361">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="1380d-361">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="1380d-362">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="1380d-362">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="1380d-363">Projděte si třídu `Startup`:</span><span class="sxs-lookup"><span data-stu-id="1380d-363">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="1380d-364">Předchozí zvýrazněný kód ukazuje kontext databáze filmů přidaný do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="1380d-364">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="1380d-365">`services.AddDbContext<MvcMovieContext>(options =>` Určuje databázi, která se má použít, a připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="1380d-365">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="1380d-366">`=>` je [operátor lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="1380d-366">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="1380d-367">Otevřete soubor *Controllers/MoviesController. cs* a prověřte konstruktor:</span><span class="sxs-lookup"><span data-stu-id="1380d-367">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="1380d-368">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze (`MvcMovieContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1380d-368">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="1380d-369">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="1380d-369">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="1380d-370">Modely silného typu a klíčové slovo @model</span><span class="sxs-lookup"><span data-stu-id="1380d-370">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="1380d-371">Dříve v tomto kurzu jste viděli, jak může řadič předat data nebo objekty do zobrazení pomocí `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="1380d-371">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="1380d-372">`ViewData` slovníku je dynamický objekt, který poskytuje pohodlný způsob, jak předat informace zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1380d-372">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="1380d-373">MVC také poskytuje možnost předat objekty modelu silného typu do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1380d-373">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="1380d-374">Tento přístup silného typu umožňuje lepší kompilaci kódu.</span><span class="sxs-lookup"><span data-stu-id="1380d-374">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="1380d-375">Mechanizmus pro generování uživatelského rozhraní používal tento přístup (to znamená předání modelu silného typu) s `MoviesController` třídou a zobrazeními, když vytváří metody a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1380d-375">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="1380d-376">Projděte si vygenerovanou metodu `Details` v souboru *Controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="1380d-376">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="1380d-377">Parametr `id` je obecně předán jako data směrování.</span><span class="sxs-lookup"><span data-stu-id="1380d-377">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="1380d-378">Například `https://localhost:5001/movies/details/1` sady:</span><span class="sxs-lookup"><span data-stu-id="1380d-378">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="1380d-379">Kontroler řadiče `movies` (první segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="1380d-379">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="1380d-380">Akce, která se má `details` (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="1380d-380">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="1380d-381">ID na 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="1380d-381">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="1380d-382">`id` můžete předat také pomocí řetězce dotazu následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="1380d-382">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="1380d-383">Parametr `id` je definován jako typ s [možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) pro případ, že není zadána hodnota ID.</span><span class="sxs-lookup"><span data-stu-id="1380d-383">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="1380d-384">[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán do `FirstOrDefaultAsync` pro výběr entit videa, které odpovídají datům směrování nebo hodnotě řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="1380d-384">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="1380d-385">Pokud je nalezen film, instance `Movie`ho modelu je předána do zobrazení `Details`:</span><span class="sxs-lookup"><span data-stu-id="1380d-385">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="1380d-386">Projděte si obsah souboru *views/video/details. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1380d-386">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="1380d-387">Zahrnutím příkazu `@model` v horní části souboru zobrazení můžete určit typ objektu, který zobrazení očekává.</span><span class="sxs-lookup"><span data-stu-id="1380d-387">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="1380d-388">Když jste vytvořili kontroler filmů, v horní části souboru *Details. cshtml* byl automaticky zahrnut následující příkaz `@model`:</span><span class="sxs-lookup"><span data-stu-id="1380d-388">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="1380d-389">Tato direktiva `@model` umožňuje přístup k videu, který kontroler předává do zobrazení pomocí objektu `Model` se silným typem.</span><span class="sxs-lookup"><span data-stu-id="1380d-389">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="1380d-390">Například v zobrazení *Details. cshtml* kód předá každé pole videa do `DisplayNameFor` a `DisplayFor` pomocníkům HTML pomocí silně typovaného objektu `Model`.</span><span class="sxs-lookup"><span data-stu-id="1380d-390">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="1380d-391">Metody a zobrazení `Create` a `Edit` také předají objekt `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="1380d-391">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="1380d-392">Prohlédněte si zobrazení *index. cshtml* a `Index` metoda v kontroleru filmů.</span><span class="sxs-lookup"><span data-stu-id="1380d-392">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="1380d-393">Všimněte si, jak kód při volání metody `View` vytvoří objekt `List`.</span><span class="sxs-lookup"><span data-stu-id="1380d-393">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="1380d-394">Kód předá tento seznam `Movies` z metody `Index` akce do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="1380d-394">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="1380d-395">Když jste vytvořili kontroler filmů, generování uživatelského rozhraní automaticky obsahuje následující příkaz `@model` v horní části souboru *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1380d-395">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="1380d-396">Direktiva `@model` umožňuje přístup k seznamu filmů, které kontroler předává do zobrazení, pomocí silně typovaného objektu `Model`.</span><span class="sxs-lookup"><span data-stu-id="1380d-396">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="1380d-397">Například v zobrazení *index. cshtml* kód cykly projde pomocí příkazu `foreach` v rámci silně typovaného `Model` objektu:</span><span class="sxs-lookup"><span data-stu-id="1380d-397">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="1380d-398">Vzhledem k tomu, že objekt `Model` je silného typu (jako objekt `IEnumerable<Movie>`), jsou všechny položky ve smyčce zadány jako `Movie`.</span><span class="sxs-lookup"><span data-stu-id="1380d-398">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="1380d-399">Kromě jiných výhod to znamená, že se vám bude zobrazovat doba kompilace kódu:</span><span class="sxs-lookup"><span data-stu-id="1380d-399">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1380d-400">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="1380d-400">Additional resources</span></span>

* [<span data-ttu-id="1380d-401">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="1380d-401">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="1380d-402">Globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="1380d-402">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="1380d-403">[Předchozí přidání zobrazení](adding-view.md)
> [Další práce s SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="1380d-403">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

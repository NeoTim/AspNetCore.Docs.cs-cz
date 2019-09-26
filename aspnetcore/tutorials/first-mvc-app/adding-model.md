---
title: Přidání modelu do ASP.NET Core aplikace MVC
author: rick-anderson
description: Přidejte model do jednoduché aplikace ASP.NET Core.
ms.author: riande
ms.date: 8/15/2019
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 5ad31a2536ad70590eaa767cf20068512241f36b
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71295477"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="c1fcb-103">Přidání modelu do ASP.NET Core aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="c1fcb-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="c1fcb-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="c1fcb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="c1fcb-105">V této části přidáte třídy pro správu filmů v databázi.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="c1fcb-106">Tyto třídy budou součástí aplikace typu **m**VC jako "**m**Odel".</span><span class="sxs-lookup"><span data-stu-id="c1fcb-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="c1fcb-107">Tyto třídy použijete s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="c1fcb-108">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje kód pro přístup k datům, který je nutné zapsat.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="c1fcb-109">Třídy modelů, které vytvoříte, jsou známé jako třídy POCO (od **P**Lain **O**ld **C**LR **O**bjekty), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="c1fcb-110">Pouze definují vlastnosti dat, která budou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="c1fcb-111">V tomto kurzu napíšete nejprve třídy modelu a EF Core vytvoříte databázi.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="c1fcb-112">Alternativním způsobem, který není pokrytý, je vygenerování tříd modelu z existující databáze.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="c1fcb-113">Informace o tomto přístupu najdete v tématu [ASP.NET Core – existující databáze](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="c1fcb-114">Přidat třídu datového modelu</span><span class="sxs-lookup"><span data-stu-id="c1fcb-114">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1fcb-116">Klikněte pravým tlačítkem na složku *modely* > **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="c1fcb-117">Název souboru *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-117">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c1fcb-118">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-118">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c1fcb-119">Do složky *modely* přidejte soubor s názvem *Movie.cs* .</span><span class="sxs-lookup"><span data-stu-id="c1fcb-119">Add a file named *Movie.cs* to the *Models* folder.</span></span>

---

<span data-ttu-id="c1fcb-120">Aktualizujte soubor *Movie.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-120">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="c1fcb-121">`Movie` Třída`Id` obsahuje pole, které je vyžadováno databází pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-121">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="c1fcb-122">Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) v `ReleaseDate` určuje typ dat (`Date`).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-122">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="c1fcb-123">S tímto atributem:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-123">With this attribute:</span></span>

  * <span data-ttu-id="c1fcb-124">Uživatel není požádán o zadání informací o čase do pole datum.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-124">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="c1fcb-125">Zobrazí se pouze datum, nejedná se o informace o čase.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-125">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="c1fcb-126">V pozdějším kurzu jsou uvedena tato [Anotace](/dotnet/api/system.componentmodel.dataannotations) .</span><span class="sxs-lookup"><span data-stu-id="c1fcb-126">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="c1fcb-127">Přidat balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="c1fcb-127">Add NuGet packages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-128">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1fcb-129">V nabídce **nástroje** vyberte > **Správce balíčků NuGet** **Konzola správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-129">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC nabídky](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="c1fcb-131">V PMC spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-131">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="c1fcb-132">Předchozí příkaz přidá poskytovatele EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-132">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="c1fcb-133">Balíček Provider nainstaluje balíček EF Core jako závislost.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-133">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="c1fcb-134">Další balíčky jsou automaticky nainstalovány v kroku generování uživatelského rozhraní později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-134">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c1fcb-135">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-135">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="c1fcb-136">Vytvoření třídy kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="c1fcb-136">Create a database context class</span></span>

<span data-ttu-id="c1fcb-137">Třída kontextu databáze je nutná ke koordinaci funkcí EF Core (vytvoření, čtení, aktualizace, odstranění) `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-137">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="c1fcb-138">Kontext databáze je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a určuje entity, které mají být zahrnuty do datového modelu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-138">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="c1fcb-139">Vytvořte složku *dat* .</span><span class="sxs-lookup"><span data-stu-id="c1fcb-139">Create a *Data* folder.</span></span>

<span data-ttu-id="c1fcb-140">Přidejte soubor *data/MvcMovieContext. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-140">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="c1fcb-141">Předchozí kód vytvoří vlastnost [\<negenerickými Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-141">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="c1fcb-142">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-142">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="c1fcb-143">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-143">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="c1fcb-144">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="c1fcb-144">Register the database context</span></span>

<span data-ttu-id="c1fcb-145">ASP.NET Core je sestaven s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-145">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c1fcb-146">Služby (například kontext EF Core DB) musí být při spuštění aplikace zaregistrované v DI.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-146">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="c1fcb-147">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-147">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="c1fcb-148">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-148">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="c1fcb-149">V této části zaregistrujete kontext databáze pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-149">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="c1fcb-150">Do horní části `using` *Startup.cs*přidejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-150">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="c1fcb-151">Do `Startup.ConfigureServices`následujícího pole přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-151">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-152">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-152">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c1fcb-153">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-153">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="c1fcb-154">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-154">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="c1fcb-155">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-155">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="c1fcb-156">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="c1fcb-156">Add a database connection string</span></span>

<span data-ttu-id="c1fcb-157">Přidejte do souboru *appSettings. JSON* připojovací řetězec:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-157">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c1fcb-159">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="c1fcb-160">Sestavte projekt jako kontrolu chyb kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-160">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="c1fcb-161">Stránky filmového uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="c1fcb-161">Scaffold movie pages</span></span>

<span data-ttu-id="c1fcb-162">Použijte nástroj pro generování uživatelského rozhraní k vytvoření stránek pro vytváření, čtení, aktualizaci a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-162">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1fcb-164">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Controllers* **> přidat > novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-164">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![zobrazení výše uvedeného kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="c1fcb-166">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **kontroler MVC se zobrazeními a pomocí Entity Framework > Přidat**.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-166">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogové okno Přidat generování uživatelského rozhraní](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="c1fcb-168">Dokončete dialog **Přidat řadič** :</span><span class="sxs-lookup"><span data-stu-id="c1fcb-168">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="c1fcb-169">**Třída modelu:** *Video (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="c1fcb-169">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="c1fcb-170">**Třída kontextu dat:** *MvcMovieContext (MvcMovie. data)*</span><span class="sxs-lookup"><span data-stu-id="c1fcb-170">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Přidat kontext dat](adding-model/_static/dc3.png)

* <span data-ttu-id="c1fcb-172">**Náhled** Ponechte výchozí hodnotu u každé zaškrtnuté možnosti.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-172">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="c1fcb-173">**Název kontroleru:** Zachovat výchozí *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="c1fcb-173">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="c1fcb-174">Vyberte **Přidat**</span><span class="sxs-lookup"><span data-stu-id="c1fcb-174">Select **Add**</span></span>

<span data-ttu-id="c1fcb-175">Visual Studio vytvoří:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-175">Visual Studio creates:</span></span>

* <span data-ttu-id="c1fcb-176">Řadič filmů (*Controllers/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="c1fcb-176">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="c1fcb-177">Soubory zobrazení Razor pro stránky vytvořit, odstranit, podrobnosti, upravit a index (*zobrazení/filmy/\*. cshtml*)</span><span class="sxs-lookup"><span data-stu-id="c1fcb-177">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="c1fcb-178">Automatické vytváření těchto souborů se říká *generování uživatelského rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-178">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c1fcb-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1fcb-179">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="c1fcb-180">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-180">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="c1fcb-181">V systému Linux exportujte cestu k nástroji pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-181">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="c1fcb-182">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-182">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c1fcb-183">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c1fcb-184">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-184">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="c1fcb-185">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-185">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="c1fcb-186">Vygenerované stránky nemůžete zatím použít, protože databáze neexistuje.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-186">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="c1fcb-187">Pokud aplikaci spouštíte a kliknete na odkaz **filmové aplikace** , *nemůžete otevřít databázi* ani *žádnou takovou tabulku: Chybová* zpráva videa</span><span class="sxs-lookup"><span data-stu-id="c1fcb-187">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="c1fcb-188">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="c1fcb-188">Initial migration</span></span>

<span data-ttu-id="c1fcb-189">K vytvoření databáze použijte funkci [migrace](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-189">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="c1fcb-190">Migrace je sada nástrojů, která umožňuje vytvořit a aktualizovat databázi tak, aby odpovídala vašemu datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-190">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1fcb-192">V nabídce **nástroje** vyberte > **Správce balíčků NuGet** **Konzola správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-192">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="c1fcb-193">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-193">In the PMC, enter the following commands:</span></span>

```console
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="c1fcb-194">`Add-Migration InitialCreate`: Vygeneruje migrační soubor *_InitialCreate. cs migrace/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="c1fcb-194">`Add-Migration InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="c1fcb-195">`InitialCreate` Argument je název migrace.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-195">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="c1fcb-196">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-196">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="c1fcb-197">Vzhledem k tomu, že se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-197">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="c1fcb-198">Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-198">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="c1fcb-199">`Update-Database`: Aktualizuje databázi na nejnovější migraci, kterou byl vytvořen předchozí příkaz.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-199">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="c1fcb-200">Tento příkaz spustí `Up` metodu v souboru *migrations/{Time-razítk} _InitialCreate. cs* , který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-200">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="c1fcb-201">Příkaz aktualizace databáze generuje následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-201">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="c1fcb-202">Pro desetinný sloupec ' Price ' pro typ entity ' film ' nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-202">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="c1fcb-203">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-203">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="c1fcb-204">Explicitně zadejte typ sloupce SQL Server, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-204">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="c1fcb-205">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-205">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c1fcb-206">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-206">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c1fcb-207">Spusťte následující příkazy .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-207">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="c1fcb-208">`ef migrations add InitialCreate`: Vygeneruje migrační soubor *_InitialCreate. cs migrace/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="c1fcb-208">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="c1fcb-209">`InitialCreate` Argument je název migrace.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="c1fcb-210">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="c1fcb-211">Vzhledem k tomu, že se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="c1fcb-212">Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě (v souboru *data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-212">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="c1fcb-213">`ef database update`: Aktualizuje databázi na nejnovější migraci, kterou byl vytvořen předchozí příkaz.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-213">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="c1fcb-214">Tento příkaz spustí `Up` metodu v souboru *migrations/{Time-razítk} _InitialCreate. cs* , který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [ more information on the CLI tools for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="c1fcb-215">Třída InitialCreate</span><span class="sxs-lookup"><span data-stu-id="c1fcb-215">The InitialCreate class</span></span>

<span data-ttu-id="c1fcb-216">Projděte si migrační soubor *migrace/{timestamp} _InitialCreate. cs* :</span><span class="sxs-lookup"><span data-stu-id="c1fcb-216">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

 <span data-ttu-id="c1fcb-217">Metoda vytvoří tabulku filmů a nakonfiguruje `Id` ji jako primární klíč. `Up`</span><span class="sxs-lookup"><span data-stu-id="c1fcb-217">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="c1fcb-218">Metoda vrátí změny schématu provedené `Up` migrací. `Down`</span><span class="sxs-lookup"><span data-stu-id="c1fcb-218">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="c1fcb-219">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="c1fcb-219">Test the app</span></span>

* <span data-ttu-id="c1fcb-220">Spusťte aplikaci a klikněte na odkaz **video aplikace** .</span><span class="sxs-lookup"><span data-stu-id="c1fcb-220">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="c1fcb-221">Pokud se zobrazí výjimka podobná jedné z následujících:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-221">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-222">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c1fcb-223">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="c1fcb-224">Pravděpodobně jste [Krok migrace](#migration)vynechali.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-224">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="c1fcb-225">Otestujte stránku **vytvořit** .</span><span class="sxs-lookup"><span data-stu-id="c1fcb-225">Test the **Create** page.</span></span> <span data-ttu-id="c1fcb-226">Zadejte a odešlete data.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-226">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="c1fcb-227">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-227">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="c1fcb-228">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-228">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="c1fcb-229">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-229">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="c1fcb-230">Otestujte stránky **Upravit**, **Podrobnosti**a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="c1fcb-230">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="c1fcb-231">Vkládání závislostí v kontroleru</span><span class="sxs-lookup"><span data-stu-id="c1fcb-231">Dependency injection in the controller</span></span>

<span data-ttu-id="c1fcb-232">Otevřete soubor *Controllers/MoviesController. cs* a prověřte konstruktor:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-232">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="c1fcb-233">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze (`MvcMovieContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-233">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="c1fcb-234">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-234">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="c1fcb-235">Modely silného typu a @model klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="c1fcb-235">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="c1fcb-236">Dříve v tomto kurzu jste viděli, jak může řadič předat data nebo objekty do zobrazení pomocí `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-236">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="c1fcb-237">`ViewData` Slovník je dynamický objekt, který poskytuje pohodlný způsob, jak předat informace do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-237">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="c1fcb-238">MVC také poskytuje možnost předat objekty modelu silného typu do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-238">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="c1fcb-239">Tento přístup se silnými typy umožňuje kompilovat kontrolu kódu při kompilaci.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-239">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="c1fcb-240">Mechanizmus pro generování uživatelského rozhraní používal tento přístup (to znamená předání modelu silného typu) s `MoviesController` třídou a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-240">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="c1fcb-241">Prověřte vygenerovanou `Details` metodu v souboru *Controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="c1fcb-241">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="c1fcb-242">`id` Parametr je obvykle předán jako data směrování.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-242">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="c1fcb-243">Například `https://localhost:5001/movies/details/1` sady:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-243">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="c1fcb-244">Kontroler `movies` řadiče (první segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-244">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="c1fcb-245">Akce na `details` (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-245">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="c1fcb-246">ID na 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-246">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="c1fcb-247">Řetězec dotazu můžete také předat `id` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-247">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="c1fcb-248">Parametr je definován jako typ s [možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) pro případ, že není zadána hodnota ID. `id`</span><span class="sxs-lookup"><span data-stu-id="c1fcb-248">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="c1fcb-249">[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán `FirstOrDefaultAsync` do pro výběr entit videa, které odpovídají datům směrování nebo hodnotě řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-249">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="c1fcb-250">Pokud se najde film, `Movie` `Details` do zobrazení se předává instance modelu:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-250">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="c1fcb-251">Projděte si obsah souboru *views/video/details. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c1fcb-251">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="c1fcb-252">`@model` Příkaz v horní části souboru zobrazení určuje typ objektu, který zobrazení očekává.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-252">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="c1fcb-253">Po vytvoření kontroleru filmů byl zahrnut následující `@model` příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-253">When the movie controller was created, the following `@model` statement was included:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="c1fcb-254">Tato `@model` Direktiva umožňuje přístup k videu, který kontroler předali do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-254">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="c1fcb-255">`Model` Objekt je silného typu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-255">The `Model` object is strongly typed.</span></span> <span data-ttu-id="c1fcb-256">Například v zobrazení *Details. cshtml* kód předá každé pole videa do `DisplayNameFor` pomocníků HTML a `DisplayFor` s objektem silného typu `Model` .</span><span class="sxs-lookup"><span data-stu-id="c1fcb-256">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="c1fcb-257">Metody `Create` a `Edit` zobrazení také`Movie` předají objekt modelu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-257">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="c1fcb-258">Prohlédněte si zobrazení *index. cshtml* a `Index` metodu v řadiči filmů.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-258">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="c1fcb-259">Všimněte si, jak kód při `List` `View` volání metody vytvoří objekt.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-259">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="c1fcb-260">Kód předá tento `Movies` seznam `Index` z metody Action do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-260">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="c1fcb-261">Po vytvoření kontroleru filmů zahrnuje `@model` generování uživatelského rozhraní do horní části souboru *index. cshtml* tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-261">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="c1fcb-262">Direktiva umožňuje přístup k seznamu filmů, které kontroler předává do zobrazení `Model` pomocí objektu se silným typem. `@model`</span><span class="sxs-lookup"><span data-stu-id="c1fcb-262">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="c1fcb-263">Například v zobrazení *index. cshtml* přechází kód přes filmy pomocí `foreach` příkazu nad objektem silného typu: `Model`</span><span class="sxs-lookup"><span data-stu-id="c1fcb-263">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="c1fcb-264">Vzhledem k tomu, že `IEnumerable<Movie>` `Movie` objektjesilnéhotypu(jakoobjekt),každápoložkavesmyčcejezapsána`Model` jako.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-264">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="c1fcb-265">Kromě jiných výhod to znamená, že se vám bude zobrazovat doba kompilace kódu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-265">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1fcb-266">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c1fcb-266">Additional resources</span></span>

* [<span data-ttu-id="c1fcb-267">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="c1fcb-267">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="c1fcb-268">Globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="c1fcb-268">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="c1fcb-269">[Předchozí přidání zobrazení](adding-view.md)
> [Next Working with SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="c1fcb-269">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="c1fcb-270">Přidat třídu datového modelu</span><span class="sxs-lookup"><span data-stu-id="c1fcb-270">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-271">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-271">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1fcb-272">Klikněte pravým tlačítkem na složku *modely* > **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-272">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="c1fcb-273">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-273">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c1fcb-274">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-274">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="c1fcb-275">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-275">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="c1fcb-276">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="c1fcb-276">Scaffold the movie model</span></span>

<span data-ttu-id="c1fcb-277">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-277">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="c1fcb-278">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-278">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-279">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1fcb-280">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Controllers* **> přidat > novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-280">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![zobrazení výše uvedeného kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="c1fcb-282">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **kontroler MVC se zobrazeními a pomocí Entity Framework > Přidat**.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-282">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogové okno Přidat generování uživatelského rozhraní](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="c1fcb-284">Dokončete dialog **Přidat řadič** :</span><span class="sxs-lookup"><span data-stu-id="c1fcb-284">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="c1fcb-285">**Třída modelu:** *Video (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="c1fcb-285">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="c1fcb-286">**Třída kontextu dat:** Vyberte ikonu a přidejte výchozí **MvcMovie. Models. MvcMovieContext.** **+**</span><span class="sxs-lookup"><span data-stu-id="c1fcb-286">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Přidat kontext dat](adding-model/_static/dc.png)

* <span data-ttu-id="c1fcb-288">**Náhled** Ponechte výchozí hodnotu u každé zaškrtnuté možnosti.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-288">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="c1fcb-289">**Název kontroleru:** Zachovat výchozí *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="c1fcb-289">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="c1fcb-290">Vyberte **Přidat**</span><span class="sxs-lookup"><span data-stu-id="c1fcb-290">Select **Add**</span></span>

![Dialogové okno Přidat řadič](adding-model/_static/add_controller2.png)

<span data-ttu-id="c1fcb-292">Visual Studio vytvoří:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-292">Visual Studio creates:</span></span>

* <span data-ttu-id="c1fcb-293">[Třída kontextu databáze](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*data/MvcMovieContext. cs*)</span><span class="sxs-lookup"><span data-stu-id="c1fcb-293">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="c1fcb-294">Řadič filmů (*Controllers/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="c1fcb-294">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="c1fcb-295">Soubory zobrazení Razor pro stránky vytvořit, odstranit, podrobnosti, upravit a index (*zobrazení/filmy/\*. cshtml*)</span><span class="sxs-lookup"><span data-stu-id="c1fcb-295">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="c1fcb-296">K automatickému vytvoření kontextu databáze a operací [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (vytvoření, čtení, aktualizace a odstranění) se říká *generování uživatelského rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-296">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c1fcb-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c1fcb-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="c1fcb-298">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-298">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="c1fcb-299">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-299">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="c1fcb-300">V systému Linux exportujte cestu k nástroji pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-300">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="c1fcb-301">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-301">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c1fcb-302">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="c1fcb-303">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-303">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="c1fcb-304">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-304">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="c1fcb-305">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-305">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="c1fcb-306">Pokud aplikaci spouštíte a kliknete na **filmový odkaz MVC** , zobrazí se chybová zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-306">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-307">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-307">Visual Studio</span></span>](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c1fcb-308">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-308">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="c1fcb-309">Musíte vytvořit databázi a k tomu použijte funkci [migrace](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-309">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="c1fcb-310">Migrace vám umožní vytvořit databázi, která odpovídá vašemu datovému modelu, a aktualizovat schéma databáze při změně datového modelu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-310">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="c1fcb-311">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="c1fcb-311">Initial migration</span></span>

<span data-ttu-id="c1fcb-312">V této části jsou dokončeny následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-312">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="c1fcb-313">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-313">Add an initial migration.</span></span>
* <span data-ttu-id="c1fcb-314">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-314">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-315">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-315">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c1fcb-316">V nabídce **nástroje** vyberte > **Správce balíčků NuGet** **Konzola správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-316">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC nabídky](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="c1fcb-318">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-318">In the PMC, enter the following commands:</span></span>

   ```console
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="c1fcb-319">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-319">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="c1fcb-320">Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-320">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="c1fcb-321">`Initial` Argument je název migrace.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-321">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="c1fcb-322">Můžete použít libovolný název, ale podle konvence se použije název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-322">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="c1fcb-323">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-323">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="c1fcb-324">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-324">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c1fcb-325">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-325">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="c1fcb-326">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-326">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="c1fcb-327">Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě (v souboru *data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-327">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="c1fcb-328">`InitialCreate` Argument je název migrace.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-328">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="c1fcb-329">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-329">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="c1fcb-330">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="c1fcb-330">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="c1fcb-331">ASP.NET Core je sestaven s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-331">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="c1fcb-332">Služby (například kontext EF Core DB) jsou během spuštění aplikace zaregistrované v DI.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-332">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="c1fcb-333">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-333">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="c1fcb-334">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-334">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c1fcb-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c1fcb-335">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c1fcb-336">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-336">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="c1fcb-337">Projděte si `Startup.ConfigureServices` následující metodu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-337">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="c1fcb-338">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="c1fcb-339">`MvcMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-339">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="c1fcb-340">Kontext dat (`MvcMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-340">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="c1fcb-341">Kontext dat určuje, které entity jsou v datovém modelu zahrnuté:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-341">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="c1fcb-342">Předchozí kód vytvoří vlastnost [\<negenerickými Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="c1fcb-343">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="c1fcb-344">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="c1fcb-345">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="c1fcb-346">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="c1fcb-347">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c1fcb-347">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c1fcb-348">Vytvořili jste kontext databáze a zaregistrovali jej pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-348">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="c1fcb-349">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="c1fcb-349">Test the app</span></span>

* <span data-ttu-id="c1fcb-350">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-350">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="c1fcb-351">Pokud získáte výjimku databáze podobnou následující:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-351">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="c1fcb-352">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-352">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="c1fcb-353">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-353">Test the **Create** link.</span></span> <span data-ttu-id="c1fcb-354">Zadejte a odešlete data.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-354">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="c1fcb-355">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-355">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="c1fcb-356">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-356">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="c1fcb-357">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-357">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="c1fcb-358">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-358">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="c1fcb-359">Prověřte `Startup` třídu:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-359">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="c1fcb-360">Předchozí zvýrazněný kód ukazuje kontext databáze filmů přidaný do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="c1fcb-360">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="c1fcb-361">`services.AddDbContext<MvcMovieContext>(options =>`Určuje databázi, která se má použít, a připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-361">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="c1fcb-362">`=>`je [operátor lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="c1fcb-362">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="c1fcb-363">Otevřete soubor *Controllers/MoviesController. cs* a prověřte konstruktor:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-363">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="c1fcb-364">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze (`MvcMovieContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-364">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="c1fcb-365">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-365">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="c1fcb-366">Modely silného typu a @model klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="c1fcb-366">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="c1fcb-367">Dříve v tomto kurzu jste viděli, jak může řadič předat data nebo objekty do zobrazení pomocí `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-367">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="c1fcb-368">`ViewData` Slovník je dynamický objekt, který poskytuje pohodlný způsob, jak předat informace do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-368">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="c1fcb-369">MVC také poskytuje možnost předat objekty modelu silného typu do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-369">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="c1fcb-370">Tento přístup silného typu umožňuje lepší kompilaci kódu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-370">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="c1fcb-371">Mechanismus generování uživatelského rozhraní používal tento přístup (to znamená předání modelu silného typu) s `MoviesController` třídou a zobrazeními, když vytváří metody a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-371">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="c1fcb-372">Prověřte vygenerovanou `Details` metodu v souboru *Controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="c1fcb-372">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="c1fcb-373">`id` Parametr je obvykle předán jako data směrování.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-373">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="c1fcb-374">Například `https://localhost:5001/movies/details/1` sady:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-374">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="c1fcb-375">Kontroler `movies` řadiče (první segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-375">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="c1fcb-376">Akce na `details` (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-376">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="c1fcb-377">ID na 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="c1fcb-377">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="c1fcb-378">Řetězec dotazu můžete také předat `id` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-378">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="c1fcb-379">Parametr je definován jako typ s [možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) pro případ, že není zadána hodnota ID. `id`</span><span class="sxs-lookup"><span data-stu-id="c1fcb-379">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="c1fcb-380">[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán `FirstOrDefaultAsync` do pro výběr entit videa, které odpovídají datům směrování nebo hodnotě řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-380">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="c1fcb-381">Pokud se najde film, `Movie` `Details` do zobrazení se předává instance modelu:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-381">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="c1fcb-382">Projděte si obsah souboru *views/video/details. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c1fcb-382">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="c1fcb-383">Zahrnutím `@model` příkazu v horní části souboru zobrazení můžete určit typ objektu, který zobrazení očekává.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-383">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="c1fcb-384">Při vytváření kontroleru filmů byl do horní části souboru `@model` *Details. cshtml* přidán následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-384">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="c1fcb-385">Tato `@model` Direktiva umožňuje přístup k videu, který kontroler předává do zobrazení `Model` pomocí objektu se silným typem.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-385">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="c1fcb-386">Například v zobrazení *Details. cshtml* kód předá každé pole videa do `DisplayNameFor` pomocníků HTML a `DisplayFor` s objektem silného typu `Model` .</span><span class="sxs-lookup"><span data-stu-id="c1fcb-386">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="c1fcb-387">Metody `Create` a `Edit` zobrazení také`Movie` předají objekt modelu.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-387">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="c1fcb-388">Prohlédněte si zobrazení *index. cshtml* a `Index` metodu v řadiči filmů.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-388">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="c1fcb-389">Všimněte si, jak kód při `List` `View` volání metody vytvoří objekt.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-389">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="c1fcb-390">Kód předá tento `Movies` seznam `Index` z metody Action do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-390">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="c1fcb-391">Když jste vytvořili kontroler filmů, generování uživatelského rozhraní automaticky obsahuje následující `@model` příkaz v horní části souboru *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="c1fcb-391">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="c1fcb-392">Direktiva umožňuje přístup k seznamu filmů, které kontroler předává do zobrazení `Model` pomocí objektu se silným typem. `@model`</span><span class="sxs-lookup"><span data-stu-id="c1fcb-392">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="c1fcb-393">Například v zobrazení *index. cshtml* přechází kód přes filmy pomocí `foreach` příkazu nad objektem silného typu: `Model`</span><span class="sxs-lookup"><span data-stu-id="c1fcb-393">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="c1fcb-394">Vzhledem k tomu, že `IEnumerable<Movie>` `Movie` objektjesilnéhotypu(jakoobjekt),každápoložkavesmyčcejezapsána`Model` jako.</span><span class="sxs-lookup"><span data-stu-id="c1fcb-394">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="c1fcb-395">Kromě jiných výhod to znamená, že se vám bude zobrazovat doba kompilace kódu:</span><span class="sxs-lookup"><span data-stu-id="c1fcb-395">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c1fcb-396">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c1fcb-396">Additional resources</span></span>

* [<span data-ttu-id="c1fcb-397">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="c1fcb-397">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="c1fcb-398">Globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="c1fcb-398">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="c1fcb-399">[Předchozí přidání zobrazení](adding-view.md)
> [Next Working with SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="c1fcb-399">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

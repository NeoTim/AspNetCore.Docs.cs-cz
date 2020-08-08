---
title: 4. část Přidání modelu do aplikace ASP.NET Core MVC
author: rick-anderson
description: Část 4 série kurzů na ASP.NET Core MVC
ms.author: riande
ms.date: 01/13/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: cc9c0447dc032ff0a88d379cd9d542e3406777bf
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021962"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="8cc58-103">4. část Přidání modelu do aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="8cc58-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="8cc58-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="8cc58-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="8cc58-105">V této části přidáte třídy pro správu filmů v databázi.</span><span class="sxs-lookup"><span data-stu-id="8cc58-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="8cc58-106">Tyto třídy budou součástí aplikace typu **m**VC jako "**m**Odel".</span><span class="sxs-lookup"><span data-stu-id="8cc58-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="8cc58-107">Tyto třídy použijete s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="8cc58-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="8cc58-108">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje kód pro přístup k datům, který je nutné zapsat.</span><span class="sxs-lookup"><span data-stu-id="8cc58-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="8cc58-109">Třídy modelů, které vytvoříte, jsou známé jako třídy POCO (od **P**Lain **O**ld **C**LR **O**bjekty), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="8cc58-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="8cc58-110">Pouze definují vlastnosti dat, která budou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="8cc58-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="8cc58-111">V tomto kurzu napíšete nejprve třídy modelu a EF Core vytvoříte databázi.</span><span class="sxs-lookup"><span data-stu-id="8cc58-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="8cc58-112">Přidat třídu datového modelu</span><span class="sxs-lookup"><span data-stu-id="8cc58-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8cc58-114">Klikněte pravým tlačítkem na složku *modely* > **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="8cc58-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="8cc58-115">Název souboru *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8cc58-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8cc58-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8cc58-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8cc58-117">Do složky *modely* přidejte soubor s názvem *Movie.cs* .</span><span class="sxs-lookup"><span data-stu-id="8cc58-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-118">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8cc58-119">Klikněte pravým tlačítkem na složku *modely* > **přidejte**  >  **novou třídu**  >  **prázdná**třída.</span><span class="sxs-lookup"><span data-stu-id="8cc58-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="8cc58-120">Název souboru *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8cc58-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="8cc58-121">Aktualizujte soubor *Movie.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="8cc58-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="8cc58-122">`Movie`Třída obsahuje `Id` pole, které je vyžadováno databází pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="8cc58-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="8cc58-123"><xref:System.ComponentModel.DataAnnotations.DataType>Atribut v `ReleaseDate` Určuje typ dat ( `Date` ).</span><span class="sxs-lookup"><span data-stu-id="8cc58-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8cc58-124">S tímto atributem:</span><span class="sxs-lookup"><span data-stu-id="8cc58-124">With this attribute:</span></span>

* <span data-ttu-id="8cc58-125">Uživatel není požádán o zadání informací o čase do pole datum.</span><span class="sxs-lookup"><span data-stu-id="8cc58-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="8cc58-126">Zobrazí se pouze datum, nejedná se o informace o čase.</span><span class="sxs-lookup"><span data-stu-id="8cc58-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="8cc58-127">V pozdějším kurzu jsou uvedena tato [Anotace](/dotnet/api/system.componentmodel.dataannotations) .</span><span class="sxs-lookup"><span data-stu-id="8cc58-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="8cc58-128">Přidání balíčků NuGet</span><span class="sxs-lookup"><span data-stu-id="8cc58-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8cc58-130">V nabídce **nástroje** vyberte **Správce balíčků NuGet** > **Konzola správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="8cc58-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC – nabídka](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8cc58-132">V PMC spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8cc58-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="8cc58-133">Předchozí příkaz přidá poskytovatele EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8cc58-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="8cc58-134">Balíček Provider nainstaluje balíček EF Core jako závislost.</span><span class="sxs-lookup"><span data-stu-id="8cc58-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="8cc58-135">Další balíčky jsou automaticky nainstalovány v kroku generování uživatelského rozhraní později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8cc58-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8cc58-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-137">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8cc58-138">V nabídce **projekt** vyberte možnost **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="8cc58-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="8cc58-139">Do **vyhledávacího pole v** pravém horním rohu zadejte `Microsoft.EntityFrameworkCore.SQLite` a stiskněte **návratový** klíč, který chcete vyhledat.</span><span class="sxs-lookup"><span data-stu-id="8cc58-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="8cc58-140">Vyberte odpovídajícího balíčku NuGet a stiskněte tlačítko **Přidat balíček** .</span><span class="sxs-lookup"><span data-stu-id="8cc58-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Přidat Entity Framework Core balíček NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="8cc58-142">Zobrazí se dialogové okno **Vybrat projekty** s `MvcMovie` vybraným projektem.</span><span class="sxs-lookup"><span data-stu-id="8cc58-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="8cc58-143">Stiskněte tlačítko **OK** .</span><span class="sxs-lookup"><span data-stu-id="8cc58-143">Press the **Ok** button.</span></span>

<span data-ttu-id="8cc58-144">Zobrazí se dialogové okno pro **přijetí licence** .</span><span class="sxs-lookup"><span data-stu-id="8cc58-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="8cc58-145">Zkontrolujte licence podle potřeby a potom klikněte na tlačítko **přijmout** .</span><span class="sxs-lookup"><span data-stu-id="8cc58-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="8cc58-146">Opakujte výše uvedené kroky a nainstalujte následující balíčky NuGet:</span><span class="sxs-lookup"><span data-stu-id="8cc58-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="8cc58-147">Vytvoření třídy kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="8cc58-147">Create a database context class</span></span>

<span data-ttu-id="8cc58-148">Třída kontextu databáze je nutná ke koordinaci funkcí EF Core (vytvoření, čtení, aktualizace, odstranění) `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="8cc58-149">Kontext databáze je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a určuje entity, které mají být zahrnuty do datového modelu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="8cc58-150">Vytvořte složku *dat* .</span><span class="sxs-lookup"><span data-stu-id="8cc58-150">Create a *Data* folder.</span></span>

<span data-ttu-id="8cc58-151">Přidejte soubor *data/MvcMovieContext. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="8cc58-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="8cc58-152">Předchozí kód vytvoří vlastnost [negenerickými \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="8cc58-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8cc58-153">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="8cc58-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8cc58-154">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="8cc58-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="8cc58-155">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="8cc58-155">Register the database context</span></span>

<span data-ttu-id="8cc58-156">ASP.NET Core je sestaven s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8cc58-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8cc58-157">Služby (například kontext EF Core DB) musí být při spuštění aplikace zaregistrované v DI.</span><span class="sxs-lookup"><span data-stu-id="8cc58-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="8cc58-158">Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8cc58-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8cc58-159">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="8cc58-160">V této části zaregistrujete kontext databáze pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="8cc58-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="8cc58-161">Do horní části `using` *Startup.cs*přidejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8cc58-161">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="8cc58-162">Do následujícího pole přidejte následující zvýrazněný kód `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8cc58-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-164">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="8cc58-165">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="8cc58-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8cc58-166">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="8cc58-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="8cc58-167">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="8cc58-167">Add a database connection string</span></span>

<span data-ttu-id="8cc58-168">Přidat připojovací řetězec do *appsettings.jsv* souboru:</span><span class="sxs-lookup"><span data-stu-id="8cc58-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-170">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="8cc58-171">Sestavte projekt jako kontrolu chyb kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="8cc58-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="8cc58-172">Stránky filmového uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="8cc58-172">Scaffold movie pages</span></span>

<span data-ttu-id="8cc58-173">Použijte nástroj pro generování uživatelského rozhraní k vytvoření stránek pro vytváření, čtení, aktualizaci a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8cc58-175">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Controllers* **> přidat > novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="8cc58-175">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![zobrazení výše uvedeného kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="8cc58-177">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **kontroler MVC se zobrazeními a pomocí Entity Framework > přidat**.</span><span class="sxs-lookup"><span data-stu-id="8cc58-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogové okno Přidat generování uživatelského rozhraní](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="8cc58-179">Dokončete dialog **Přidat řadič** :</span><span class="sxs-lookup"><span data-stu-id="8cc58-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="8cc58-180">**Třída modelu:** *video (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="8cc58-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="8cc58-181">**Třída kontextu dat:** *MvcMovieContext (MvcMovie. data)*</span><span class="sxs-lookup"><span data-stu-id="8cc58-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Přidat kontext dat](adding-model/_static/dc3.png)

* <span data-ttu-id="8cc58-183">**Zobrazení:** Ponechte výchozí hodnotu u každé zaškrtnuté možnosti.</span><span class="sxs-lookup"><span data-stu-id="8cc58-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="8cc58-184">**Název kontroleru:** Zachovat výchozí *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="8cc58-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="8cc58-185">Vyberte **Přidat**</span><span class="sxs-lookup"><span data-stu-id="8cc58-185">Select **Add**</span></span>

<span data-ttu-id="8cc58-186">Visual Studio vytvoří:</span><span class="sxs-lookup"><span data-stu-id="8cc58-186">Visual Studio creates:</span></span>

* <span data-ttu-id="8cc58-187">Řadič filmů (*Controllers/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="8cc58-187">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="8cc58-188">Razorzobrazení souborů pro stránky vytvořit, odstranit, podrobnosti, upravit a index (*zobrazení/filmy/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="8cc58-188">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="8cc58-189">Automatické vytváření těchto souborů se říká *generování uživatelského rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="8cc58-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="8cc58-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8cc58-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="8cc58-191">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="8cc58-191">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="8cc58-192">V systému Linux exportujte cestu k nástroji pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="8cc58-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="8cc58-193">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8cc58-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-194">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8cc58-195">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="8cc58-195">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="8cc58-196">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8cc58-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="8cc58-197">Vygenerované stránky nemůžete zatím použít, protože databáze neexistuje.</span><span class="sxs-lookup"><span data-stu-id="8cc58-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="8cc58-198">Pokud aplikaci spouštíte a kliknete na odkaz **filmové aplikace** , *nemůžete otevřít databázi* nebo *žádnou takovou tabulku:* chybová zpráva videa.</span><span class="sxs-lookup"><span data-stu-id="8cc58-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="8cc58-199">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="8cc58-199">Initial migration</span></span>

<span data-ttu-id="8cc58-200">K vytvoření databáze použijte funkci [migrace](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="8cc58-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="8cc58-201">Migrace je sada nástrojů, která umožňuje vytvořit a aktualizovat databázi tak, aby odpovídala vašemu datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8cc58-203">V nabídce **nástroje** vyberte **Správce balíčků NuGet** > **Konzola správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="8cc58-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="8cc58-204">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8cc58-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="8cc58-205">`Add-Migration InitialCreate`: Vygeneruje migrační soubor *_InitialCreate. cs migrace/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="8cc58-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="8cc58-206">`InitialCreate`Argument je název migrace.</span><span class="sxs-lookup"><span data-stu-id="8cc58-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="8cc58-207">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="8cc58-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="8cc58-208">Vzhledem k tomu, že se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="8cc58-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="8cc58-209">Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě.</span><span class="sxs-lookup"><span data-stu-id="8cc58-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="8cc58-210">`Update-Database`: Aktualizuje databázi na nejnovější migraci, která vytvořila předchozí příkaz.</span><span class="sxs-lookup"><span data-stu-id="8cc58-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="8cc58-211">Tento příkaz spustí `Up` metodu v souboru *migrations/{Time-razítk} _InitialCreate. cs* , ve kterém se vytvoří databáze.</span><span class="sxs-lookup"><span data-stu-id="8cc58-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="8cc58-212">Příkaz aktualizace databáze generuje následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="8cc58-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="8cc58-213">Pro desetinný sloupec ' Price ' pro typ entity ' film ' nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="8cc58-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="8cc58-214">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="8cc58-215">Explicitně zadejte typ sloupce SQL Server, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="8cc58-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="8cc58-216">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-217">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="8cc58-218">Spusťte následující příkazy .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="8cc58-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="8cc58-219">`ef migrations add InitialCreate`: Vygeneruje migrační soubor *_InitialCreate. cs migrace/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="8cc58-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="8cc58-220">`InitialCreate`Argument je název migrace.</span><span class="sxs-lookup"><span data-stu-id="8cc58-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="8cc58-221">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="8cc58-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="8cc58-222">Vzhledem k tomu, že se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="8cc58-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="8cc58-223">Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě (v souboru *data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="8cc58-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="8cc58-224">`ef database update`: Aktualizuje databázi na nejnovější migraci, která vytvořila předchozí příkaz.</span><span class="sxs-lookup"><span data-stu-id="8cc58-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="8cc58-225">Tento příkaz spustí `Up` metodu v souboru *migrations/{Time-razítk} _InitialCreate. cs* , ve kterém se vytvoří databáze.</span><span class="sxs-lookup"><span data-stu-id="8cc58-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="8cc58-226">Třída InitialCreate</span><span class="sxs-lookup"><span data-stu-id="8cc58-226">The InitialCreate class</span></span>

<span data-ttu-id="8cc58-227">Projděte si soubor migrace */{timestamp} _InitialCreate. cs* :</span><span class="sxs-lookup"><span data-stu-id="8cc58-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="8cc58-228">`Up`Metoda vytvoří tabulku filmů a nakonfiguruje `Id` ji jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="8cc58-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="8cc58-229">`Down`Metoda vrátí změny schématu provedené `Up` migrací.</span><span class="sxs-lookup"><span data-stu-id="8cc58-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="8cc58-230">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="8cc58-230">Test the app</span></span>

* <span data-ttu-id="8cc58-231">Spusťte aplikaci a klikněte na odkaz **video aplikace** .</span><span class="sxs-lookup"><span data-stu-id="8cc58-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="8cc58-232">Pokud se zobrazí výjimka podobná jedné z následujících:</span><span class="sxs-lookup"><span data-stu-id="8cc58-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-234">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="8cc58-235">Pravděpodobně jste [Krok migrace](#migration)vynechali.</span><span class="sxs-lookup"><span data-stu-id="8cc58-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="8cc58-236">Otestujte stránku **vytvořit** .</span><span class="sxs-lookup"><span data-stu-id="8cc58-236">Test the **Create** page.</span></span> <span data-ttu-id="8cc58-237">Zadejte a odešlete data.</span><span class="sxs-lookup"><span data-stu-id="8cc58-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="8cc58-238">V poli možná nebudete moct zadat desítkové čárky `Price` .</span><span class="sxs-lookup"><span data-stu-id="8cc58-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8cc58-239">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="8cc58-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8cc58-240">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8cc58-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8cc58-241">Otestujte stránky **Upravit**, **Podrobnosti**a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="8cc58-241">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="8cc58-242">Vkládání závislostí v kontroleru</span><span class="sxs-lookup"><span data-stu-id="8cc58-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8cc58-244">Otevřete soubor *Controllers/MoviesController. cs* a prověřte konstruktor:</span><span class="sxs-lookup"><span data-stu-id="8cc58-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="8cc58-245">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze ( `MvcMovieContext` ) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8cc58-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="8cc58-246">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8cc58-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-247">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="8cc58-248">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze ( `MvcMovieContext` ) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8cc58-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="8cc58-249">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8cc58-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="8cc58-250">Použití SQLite pro vývoj, SQL Server pro produkci</span><span class="sxs-lookup"><span data-stu-id="8cc58-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="8cc58-251">Když je vybrána možnost SQLite, je kód vygenerovaný šablonou připraven pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="8cc58-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="8cc58-252">Následující kód ukazuje, jak vložit <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do spouštění.</span><span class="sxs-lookup"><span data-stu-id="8cc58-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="8cc58-253">`IWebHostEnvironment`je vložená, takže `ConfigureServices` může použít SQLite ve vývoji a SQL Server v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="8cc58-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="8cc58-254">Modely silného typu a @model klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="8cc58-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="8cc58-255">Dříve v tomto kurzu jste viděli, jak může řadič předat data nebo objekty do zobrazení pomocí `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="8cc58-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="8cc58-256">`ViewData`Slovník je dynamický objekt, který poskytuje pohodlný způsob, jak předat informace do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8cc58-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="8cc58-257">MVC také poskytuje možnost předat objekty modelu silného typu do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8cc58-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="8cc58-258">Tento přístup se silnými typy umožňuje kompilovat kontrolu kódu při kompilaci.</span><span class="sxs-lookup"><span data-stu-id="8cc58-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="8cc58-259">Mechanizmus pro generování uživatelského rozhraní používal tento přístup (to znamená předání modelu silného typu) s `MoviesController` třídou a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="8cc58-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="8cc58-260">Prověřte vygenerovanou `Details` metodu v souboru *Controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="8cc58-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="8cc58-261">`id`Parametr je obvykle předán jako data směrování.</span><span class="sxs-lookup"><span data-stu-id="8cc58-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="8cc58-262">Například `https://localhost:5001/movies/details/1` sady:</span><span class="sxs-lookup"><span data-stu-id="8cc58-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="8cc58-263">Kontroler řadiče `movies` (první segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="8cc58-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="8cc58-264">Akce na `details` (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="8cc58-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="8cc58-265">ID na 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="8cc58-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="8cc58-266">Řetězec dotazu můžete také předat následujícím `id` způsobem:</span><span class="sxs-lookup"><span data-stu-id="8cc58-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="8cc58-267">`id`Parametr je definován jako typ s [možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) pro případ, že není zadána hodnota ID.</span><span class="sxs-lookup"><span data-stu-id="8cc58-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="8cc58-268">[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán do `FirstOrDefaultAsync` pro výběr entit videa, které odpovídají datům směrování nebo hodnotě řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="8cc58-269">Pokud se najde film, `Movie` do zobrazení se předává instance modelu `Details` :</span><span class="sxs-lookup"><span data-stu-id="8cc58-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="8cc58-270">Projděte si obsah souboru *views/video/details. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8cc58-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="8cc58-271">`@model`Příkaz v horní části souboru zobrazení určuje typ objektu, který zobrazení očekává.</span><span class="sxs-lookup"><span data-stu-id="8cc58-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="8cc58-272">Po vytvoření kontroleru filmů `@model` byl zahrnut následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8cc58-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="8cc58-273">Tato `@model` Direktiva umožňuje přístup k videu, který kontroler předali do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8cc58-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="8cc58-274">`Model`Objekt je silného typu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="8cc58-275">Například v zobrazení *Details. cshtml* kód předá každé pole videa do `DisplayNameFor` `DisplayFor` pomocníků HTML a s objektem silného typu `Model` .</span><span class="sxs-lookup"><span data-stu-id="8cc58-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="8cc58-276">`Create`Metody a `Edit` zobrazení také předají `Movie` objekt modelu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="8cc58-277">Prohlédněte si zobrazení *index. cshtml* a `Index` metodu v řadiči filmů.</span><span class="sxs-lookup"><span data-stu-id="8cc58-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="8cc58-278">Všimněte si, jak kód `List` při volání metody vytvoří objekt `View` .</span><span class="sxs-lookup"><span data-stu-id="8cc58-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="8cc58-279">Kód předá tento `Movies` seznam z `Index` metody Action do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="8cc58-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="8cc58-280">Po vytvoření kontroleru filmů zahrnuje generování uživatelského rozhraní do `@model` horní části souboru *index. cshtml* tento příkaz:</span><span class="sxs-lookup"><span data-stu-id="8cc58-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="8cc58-281">`@model`Direktiva umožňuje přístup k seznamu filmů, které kontroler předává do zobrazení pomocí `Model` objektu se silným typem.</span><span class="sxs-lookup"><span data-stu-id="8cc58-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="8cc58-282">Například v zobrazení *index. cshtml* přechází kód přes filmy pomocí `foreach` příkazu nad objektem silného typu `Model` :</span><span class="sxs-lookup"><span data-stu-id="8cc58-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="8cc58-283">Vzhledem k tomu `Model` , že objekt je silného typu (jako `IEnumerable<Movie>` objekt), každá položka ve smyčce je zapsána jako `Movie` .</span><span class="sxs-lookup"><span data-stu-id="8cc58-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="8cc58-284">Kromě jiných výhod to znamená, že se vám bude zobrazovat doba kompilace kódu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8cc58-285">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8cc58-285">Additional resources</span></span>

* [<span data-ttu-id="8cc58-286">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="8cc58-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="8cc58-287">Globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="8cc58-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="8cc58-288">[Předchozí přidání zobrazení](adding-view.md) 
>  [Další práce s SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="8cc58-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="8cc58-289">Přidat třídu datového modelu</span><span class="sxs-lookup"><span data-stu-id="8cc58-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8cc58-291">Klikněte pravým tlačítkem na složku *modely* > **Přidat**  >  **třídu**.</span><span class="sxs-lookup"><span data-stu-id="8cc58-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="8cc58-292">Pojmenujte **film**třídy.</span><span class="sxs-lookup"><span data-stu-id="8cc58-292">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-293">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8cc58-294">Přidejte třídu do složky *modely* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8cc58-294">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8cc58-295">Generování uživatelského rozhraní modelu filmů</span><span class="sxs-lookup"><span data-stu-id="8cc58-295">Scaffold the movie model</span></span>

<span data-ttu-id="8cc58-296">V této části je model filmu vygenerovaný.</span><span class="sxs-lookup"><span data-stu-id="8cc58-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8cc58-297">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8cc58-299">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Controllers* **> přidat > novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="8cc58-299">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![zobrazení výše uvedeného kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="8cc58-301">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **kontroler MVC se zobrazeními a pomocí Entity Framework > přidat**.</span><span class="sxs-lookup"><span data-stu-id="8cc58-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogové okno Přidat generování uživatelského rozhraní](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="8cc58-303">Dokončete dialog **Přidat řadič** :</span><span class="sxs-lookup"><span data-stu-id="8cc58-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="8cc58-304">**Třída modelu:** *video (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="8cc58-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="8cc58-305">**Třída kontextu dat:** Vyberte **+** ikonu a přidejte výchozí **MvcMovie. Models. MvcMovieContext** .</span><span class="sxs-lookup"><span data-stu-id="8cc58-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Přidat kontext dat](adding-model/_static/dc.png)

* <span data-ttu-id="8cc58-307">**Zobrazení:** Ponechte výchozí hodnotu u každé zaškrtnuté možnosti.</span><span class="sxs-lookup"><span data-stu-id="8cc58-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="8cc58-308">**Název kontroleru:** Zachovat výchozí *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="8cc58-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="8cc58-309">Vyberte **Přidat**</span><span class="sxs-lookup"><span data-stu-id="8cc58-309">Select **Add**</span></span>

![Dialogové okno Přidat řadič](adding-model/_static/add_controller2.png)

<span data-ttu-id="8cc58-311">Visual Studio vytvoří:</span><span class="sxs-lookup"><span data-stu-id="8cc58-311">Visual Studio creates:</span></span>

* <span data-ttu-id="8cc58-312">[Třída kontextu databáze](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*data/MvcMovieContext. cs*)</span><span class="sxs-lookup"><span data-stu-id="8cc58-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="8cc58-313">Řadič filmů (*Controllers/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="8cc58-313">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="8cc58-314">Razorzobrazení souborů pro stránky vytvořit, odstranit, podrobnosti, upravit a index (*zobrazení/filmy/ \* . cshtml*)</span><span class="sxs-lookup"><span data-stu-id="8cc58-314">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="8cc58-315">K automatickému vytvoření kontextu databáze a operací [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (vytvoření, čtení, aktualizace a odstranění) se říká *generování uživatelského rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="8cc58-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8cc58-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8cc58-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="8cc58-317">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="8cc58-317">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="8cc58-318">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="8cc58-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="8cc58-319">V systému Linux exportujte cestu k nástroji pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="8cc58-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="8cc58-320">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8cc58-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-321">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8cc58-322">Otevřete příkazové okno v adresáři projektu (adresář, který obsahuje soubory *program.cs*, *Startup.cs*a *. csproj* ).</span><span class="sxs-lookup"><span data-stu-id="8cc58-322">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="8cc58-323">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="8cc58-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="8cc58-324">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8cc58-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="8cc58-325">Pokud aplikaci spouštíte a kliknete na **filmový odkaz MVC** , zobrazí se chybová zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="8cc58-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-326">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-326">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-327">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="8cc58-328">Musíte vytvořit databázi a k tomu použijte funkci [migrace](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="8cc58-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="8cc58-329">Migrace vám umožní vytvořit databázi, která odpovídá vašemu datovému modelu, a aktualizovat schéma databáze při změně datového modelu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="8cc58-330">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="8cc58-330">Initial migration</span></span>

<span data-ttu-id="8cc58-331">V této části jsou dokončeny následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="8cc58-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="8cc58-332">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="8cc58-332">Add an initial migration.</span></span>
* <span data-ttu-id="8cc58-333">Aktualizujte databázi pomocí prvotní migrace.</span><span class="sxs-lookup"><span data-stu-id="8cc58-333">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8cc58-335">V nabídce **nástroje** vyberte **Správce balíčků NuGet** > **Konzola správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="8cc58-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC – nabídka](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="8cc58-337">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8cc58-337">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="8cc58-338">`Add-Migration`Příkaz vygeneruje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="8cc58-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="8cc58-339">Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě.</span><span class="sxs-lookup"><span data-stu-id="8cc58-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="8cc58-340">`Initial`Argument je název migrace.</span><span class="sxs-lookup"><span data-stu-id="8cc58-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="8cc58-341">Můžete použít libovolný název, ale podle konvence se použije název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="8cc58-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="8cc58-342">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="8cc58-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="8cc58-343">`Update-Database`Příkaz spustí `Up` metodu v souboru *migrations/{Time-razítk} _InitialCreate. cs* , čímž se vytvoří databáze.</span><span class="sxs-lookup"><span data-stu-id="8cc58-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-344">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="8cc58-345">`ef migrations add InitialCreate`Příkaz vygeneruje kód pro vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="8cc58-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="8cc58-346">Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě (v souboru *data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="8cc58-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="8cc58-347">`InitialCreate`Argument je název migrace.</span><span class="sxs-lookup"><span data-stu-id="8cc58-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="8cc58-348">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="8cc58-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8cc58-349">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="8cc58-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8cc58-350">ASP.NET Core je sestaven s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8cc58-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8cc58-351">Služby (například kontext EF Core DB) jsou během spuštění aplikace zaregistrované v DI.</span><span class="sxs-lookup"><span data-stu-id="8cc58-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="8cc58-352">Komponenty, které vyžadují tyto služby (například Razor stránky), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8cc58-352">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8cc58-353">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8cc58-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8cc58-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8cc58-355">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="8cc58-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="8cc58-356">Projděte si následující `Startup.ConfigureServices` metodu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8cc58-357">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="8cc58-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="8cc58-358">`MvcMovieContext`Koordinuje funkce EF Core (vytváření, čtení, aktualizace, odstranění atd.) pro `Movie` model.</span><span class="sxs-lookup"><span data-stu-id="8cc58-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="8cc58-359">Kontext dat ( `MvcMovieContext` ) je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="8cc58-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="8cc58-360">Kontext dat určuje, které entity jsou v datovém modelu zahrnuté:</span><span class="sxs-lookup"><span data-stu-id="8cc58-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="8cc58-361">Předchozí kód vytvoří vlastnost [negenerickými \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="8cc58-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8cc58-362">V Entity Framework terminologii sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="8cc58-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8cc58-363">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="8cc58-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8cc58-364">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="8cc58-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8cc58-365">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) přečte připojovací řetězec z *appsettings.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="8cc58-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8cc58-366">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8cc58-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8cc58-367">Vytvořili jste kontext databáze a zaregistrovali jej pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="8cc58-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="8cc58-368">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="8cc58-368">Test the app</span></span>

* <span data-ttu-id="8cc58-369">Spusťte aplikaci a přidejte ji `/Movies` k adrese URL v prohlížeči ( `http://localhost:port/movies` ).</span><span class="sxs-lookup"><span data-stu-id="8cc58-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="8cc58-370">Pokud získáte výjimku databáze podobnou následující:</span><span class="sxs-lookup"><span data-stu-id="8cc58-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="8cc58-371">Nezmeškali jste [Krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="8cc58-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="8cc58-372">Otestujte odkaz pro **Vytvoření** .</span><span class="sxs-lookup"><span data-stu-id="8cc58-372">Test the **Create** link.</span></span> <span data-ttu-id="8cc58-373">Zadejte a odešlete data.</span><span class="sxs-lookup"><span data-stu-id="8cc58-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="8cc58-374">V poli možná nebudete moct zadat desítkové čárky `Price` .</span><span class="sxs-lookup"><span data-stu-id="8cc58-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8cc58-375">Aby bylo možné podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro jiné než anglické národní prostředí, které používá čárku (",") pro desetinnou čárku a pro formáty kalendářních dat, které nejsou v češtině, musí být aplikace globální.</span><span class="sxs-lookup"><span data-stu-id="8cc58-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8cc58-376">Pokyny k globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8cc58-376">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8cc58-377">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="8cc58-377">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="8cc58-378">Prověřte `Startup` třídu:</span><span class="sxs-lookup"><span data-stu-id="8cc58-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="8cc58-379">Předchozí zvýrazněný kód ukazuje kontext databáze filmů přidaný do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="8cc58-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="8cc58-380">`services.AddDbContext<MvcMovieContext>(options =>`Určuje databázi, která se má použít, a připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="8cc58-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="8cc58-381">`=>`je [operátor lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="8cc58-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="8cc58-382">Otevřete soubor *Controllers/MoviesController. cs* a prověřte konstruktor:</span><span class="sxs-lookup"><span data-stu-id="8cc58-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="8cc58-383">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze ( `MvcMovieContext` ) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8cc58-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="8cc58-384">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="8cc58-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="8cc58-385">Modely silného typu a @model klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="8cc58-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="8cc58-386">Dříve v tomto kurzu jste viděli, jak může řadič předat data nebo objekty do zobrazení pomocí `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="8cc58-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="8cc58-387">`ViewData`Slovník je dynamický objekt, který poskytuje pohodlný způsob, jak předat informace do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8cc58-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="8cc58-388">MVC také poskytuje možnost předat objekty modelu silného typu do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8cc58-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="8cc58-389">Tento přístup silného typu umožňuje lepší kompilaci kódu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="8cc58-390">Mechanismus generování uživatelského rozhraní používal tento přístup (to znamená předání modelu silného typu) s `MoviesController` třídou a zobrazeními, když vytváří metody a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8cc58-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="8cc58-391">Prověřte vygenerovanou `Details` metodu v souboru *Controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="8cc58-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="8cc58-392">`id`Parametr je obvykle předán jako data směrování.</span><span class="sxs-lookup"><span data-stu-id="8cc58-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="8cc58-393">Například `https://localhost:5001/movies/details/1` sady:</span><span class="sxs-lookup"><span data-stu-id="8cc58-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="8cc58-394">Kontroler řadiče `movies` (první segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="8cc58-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="8cc58-395">Akce na `details` (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="8cc58-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="8cc58-396">ID na 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="8cc58-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="8cc58-397">Řetězec dotazu můžete také předat následujícím `id` způsobem:</span><span class="sxs-lookup"><span data-stu-id="8cc58-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="8cc58-398">`id`Parametr je definován jako typ s [možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) ( `int?` ) pro případ, že není zadána hodnota ID.</span><span class="sxs-lookup"><span data-stu-id="8cc58-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="8cc58-399">[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán do `FirstOrDefaultAsync` pro výběr entit videa, které odpovídají datům směrování nebo hodnotě řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="8cc58-400">Pokud se najde film, `Movie` do zobrazení se předává instance modelu `Details` :</span><span class="sxs-lookup"><span data-stu-id="8cc58-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="8cc58-401">Projděte si obsah souboru *views/video/details. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8cc58-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="8cc58-402">Zahrnutím `@model` příkazu v horní části souboru zobrazení můžete určit typ objektu, který zobrazení očekává.</span><span class="sxs-lookup"><span data-stu-id="8cc58-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="8cc58-403">Při vytváření kontroleru filmů `@model` byl do horní části souboru *Details. cshtml* přidán následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8cc58-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="8cc58-404">Tato `@model` Direktiva umožňuje přístup k videu, který kontroler předává do zobrazení pomocí `Model` objektu se silným typem.</span><span class="sxs-lookup"><span data-stu-id="8cc58-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="8cc58-405">Například v zobrazení *Details. cshtml* kód předá každé pole videa do `DisplayNameFor` `DisplayFor` pomocníků HTML a s objektem silného typu `Model` .</span><span class="sxs-lookup"><span data-stu-id="8cc58-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="8cc58-406">`Create`Metody a `Edit` zobrazení také předají `Movie` objekt modelu.</span><span class="sxs-lookup"><span data-stu-id="8cc58-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="8cc58-407">Prohlédněte si zobrazení *index. cshtml* a `Index` metodu v řadiči filmů.</span><span class="sxs-lookup"><span data-stu-id="8cc58-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="8cc58-408">Všimněte si, jak kód `List` při volání metody vytvoří objekt `View` .</span><span class="sxs-lookup"><span data-stu-id="8cc58-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="8cc58-409">Kód předá tento `Movies` seznam z `Index` metody Action do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="8cc58-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="8cc58-410">Když jste vytvořili kontroler filmů, generování uživatelského rozhraní automaticky obsahuje následující `@model` příkaz v horní části souboru *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8cc58-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="8cc58-411">`@model`Direktiva umožňuje přístup k seznamu filmů, které kontroler předává do zobrazení pomocí `Model` objektu se silným typem.</span><span class="sxs-lookup"><span data-stu-id="8cc58-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="8cc58-412">Například v zobrazení *index. cshtml* přechází kód přes filmy pomocí `foreach` příkazu nad objektem silného typu `Model` :</span><span class="sxs-lookup"><span data-stu-id="8cc58-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="8cc58-413">Vzhledem k tomu `Model` , že objekt je silného typu (jako `IEnumerable<Movie>` objekt), každá položka ve smyčce je zapsána jako `Movie` .</span><span class="sxs-lookup"><span data-stu-id="8cc58-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="8cc58-414">Kromě jiných výhod to znamená, že se vám bude zobrazovat doba kompilace kódu:</span><span class="sxs-lookup"><span data-stu-id="8cc58-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8cc58-415">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8cc58-415">Additional resources</span></span>

* [<span data-ttu-id="8cc58-416">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="8cc58-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="8cc58-417">Globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="8cc58-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="8cc58-418">[Předchozí přidání zobrazení](adding-view.md) 
>  [Další práce s databází](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="8cc58-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end

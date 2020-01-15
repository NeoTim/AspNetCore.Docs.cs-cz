---
title: Přidání modelu do ASP.NET Core aplikace MVC
author: rick-anderson
description: Přidejte model do jednoduché aplikace ASP.NET Core.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: adf313418e82cc265304262f7a751273fa0e139f
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75952113"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="f05ed-103">Přidání modelu do ASP.NET Core aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="f05ed-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="f05ed-104">[Rick Anderson](https://twitter.com/RickAndMSFT) a [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f05ed-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="f05ed-105">V této části přidáte třídy pro správu filmů v databázi.</span><span class="sxs-lookup"><span data-stu-id="f05ed-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="f05ed-106">Tyto třídy budou součástí aplikace typu **m**VC jako "**m**Odel".</span><span class="sxs-lookup"><span data-stu-id="f05ed-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="f05ed-107">Tyto třídy použijete s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="f05ed-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="f05ed-108">EF Core je rozhraní pro mapování relačních objektů (ORM), které zjednodušuje kód pro přístup k datům, který je nutné zapsat.</span><span class="sxs-lookup"><span data-stu-id="f05ed-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="f05ed-109">Třídy modelů, které vytvoříte, jsou známé jako třídy POCO (od **P**Lain **O**ld **C**LR **O**bjekty), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="f05ed-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="f05ed-110">Pouze definují vlastnosti dat, která budou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="f05ed-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="f05ed-111">V tomto kurzu napíšete nejprve třídy modelu a EF Core vytvoříte databázi.</span><span class="sxs-lookup"><span data-stu-id="f05ed-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span> <span data-ttu-id="f05ed-112">Alternativním způsobem, který není pokrytý, je vygenerování tříd modelu z existující databáze.</span><span class="sxs-lookup"><span data-stu-id="f05ed-112">An alternate approach not covered here is to generate model classes from an existing database.</span></span> <span data-ttu-id="f05ed-113">Informace o tomto přístupu najdete v tématu [ASP.NET Core – existující databáze](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="f05ed-113">For information about that approach, see [ASP.NET Core - Existing Database](/ef/core/get-started/aspnetcore/existing-db).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="f05ed-114">Přidat třídu datového modelu</span><span class="sxs-lookup"><span data-stu-id="f05ed-114">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f05ed-116">Klikněte pravým tlačítkem na složku *modely* > **Přidat** **třídu** > .</span><span class="sxs-lookup"><span data-stu-id="f05ed-116">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="f05ed-117">Název souboru *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="f05ed-117">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f05ed-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f05ed-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f05ed-119">Do složky *modely* přidejte soubor s názvem *Movie.cs* .</span><span class="sxs-lookup"><span data-stu-id="f05ed-119">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f05ed-120">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f05ed-121">Klikněte pravým tlačítkem na složku *modely* > **Přidat** > **novou třídu** > **prázdnou třídu**.</span><span class="sxs-lookup"><span data-stu-id="f05ed-121">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="f05ed-122">Název souboru *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="f05ed-122">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="f05ed-123">Aktualizujte soubor *Movie.cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="f05ed-123">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="f05ed-124">Třída `Movie` obsahuje pole `Id`, které databáze vyžaduje pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="f05ed-124">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="f05ed-125">Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) v `ReleaseDate` určuje typ dat (`Date`).</span><span class="sxs-lookup"><span data-stu-id="f05ed-125">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="f05ed-126">S tímto atributem:</span><span class="sxs-lookup"><span data-stu-id="f05ed-126">With this attribute:</span></span>

* <span data-ttu-id="f05ed-127">Uživatel není požádán o zadání informací o čase do pole datum.</span><span class="sxs-lookup"><span data-stu-id="f05ed-127">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="f05ed-128">Zobrazí se pouze datum, nejedná se o informace o čase.</span><span class="sxs-lookup"><span data-stu-id="f05ed-128">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="f05ed-129">V pozdějším kurzu jsou uvedena tato [Anotace](/dotnet/api/system.componentmodel.dataannotations) .</span><span class="sxs-lookup"><span data-stu-id="f05ed-129">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="f05ed-130">Přidat balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="f05ed-130">Add NuGet packages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f05ed-132">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="f05ed-132">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC nabídky](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="f05ed-134">V PMC spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f05ed-134">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="f05ed-135">Předchozí příkaz přidá poskytovatele EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f05ed-135">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="f05ed-136">Balíček Provider nainstaluje balíček EF Core jako závislost.</span><span class="sxs-lookup"><span data-stu-id="f05ed-136">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="f05ed-137">Další balíčky jsou automaticky nainstalovány v kroku generování uživatelského rozhraní později v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-137">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f05ed-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f05ed-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f05ed-139">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f05ed-140">V nabídce **projekt** vyberte možnost **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="f05ed-140">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="f05ed-141">Do **vyhledávacího pole v** pravém horním rohu zadejte `Microsoft.EntityFrameworkCore.SQLite` a stiskněte **návratový** klíč, který chcete vyhledat.</span><span class="sxs-lookup"><span data-stu-id="f05ed-141">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="f05ed-142">Vyberte odpovídajícího balíčku NuGet a stiskněte tlačítko **Přidat balíček** .</span><span class="sxs-lookup"><span data-stu-id="f05ed-142">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Přidat Entity Framework Core balíček NuGet](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="f05ed-144">Zobrazí se dialogové okno **Vybrat projekty** se zvoleným projektem `MvcMovie`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-144">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="f05ed-145">Stiskněte tlačítko **OK** .</span><span class="sxs-lookup"><span data-stu-id="f05ed-145">Press the **Ok** button.</span></span>

<span data-ttu-id="f05ed-146">Zobrazí se dialogové okno pro **přijetí licence** .</span><span class="sxs-lookup"><span data-stu-id="f05ed-146">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="f05ed-147">Zkontrolujte licence podle potřeby a potom klikněte na tlačítko **přijmout** .</span><span class="sxs-lookup"><span data-stu-id="f05ed-147">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="f05ed-148">Opakujte výše uvedené kroky a nainstalujte následující balíčky NuGet:</span><span class="sxs-lookup"><span data-stu-id="f05ed-148">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="f05ed-149">Vytvoření třídy kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="f05ed-149">Create a database context class</span></span>

<span data-ttu-id="f05ed-150">Třída kontextu databáze je nutná ke koordinaci funkcí EF Core (vytvoření, čtení, aktualizace, odstranění) pro model `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="f05ed-151">Kontext databáze je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a určuje entity, které mají být zahrnuty do datového modelu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="f05ed-152">Vytvořte složku *dat* .</span><span class="sxs-lookup"><span data-stu-id="f05ed-152">Create a *Data* folder.</span></span>

<span data-ttu-id="f05ed-153">Přidejte soubor *data/MvcMovieContext. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f05ed-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="f05ed-154">Předchozí kód vytvoří vlastnost [negenerickými\<Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="f05ed-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="f05ed-155">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="f05ed-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="f05ed-156">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="f05ed-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="f05ed-157">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="f05ed-157">Register the database context</span></span>

<span data-ttu-id="f05ed-158">ASP.NET Core je sestaven s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f05ed-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f05ed-159">Služby (například kontext EF Core DB) musí být při spuštění aplikace zaregistrované v DI.</span><span class="sxs-lookup"><span data-stu-id="f05ed-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="f05ed-160">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f05ed-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f05ed-161">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="f05ed-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="f05ed-162">V této části zaregistrujete kontext databáze pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="f05ed-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="f05ed-163">Do horní části *Startup.cs*přidejte následující příkazy `using`:</span><span class="sxs-lookup"><span data-stu-id="f05ed-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="f05ed-164">Do `Startup.ConfigureServices`přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="f05ed-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f05ed-166">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="f05ed-167">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f05ed-168">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="f05ed-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="f05ed-169">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="f05ed-169">Add a database connection string</span></span>

<span data-ttu-id="f05ed-170">Přidejte do souboru *appSettings. JSON* připojovací řetězec:</span><span class="sxs-lookup"><span data-stu-id="f05ed-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f05ed-172">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="f05ed-173">Sestavte projekt jako kontrolu chyb kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="f05ed-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="f05ed-174">Stránky filmového uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="f05ed-174">Scaffold movie pages</span></span>

<span data-ttu-id="f05ed-175">Použijte nástroj pro generování uživatelského rozhraní k vytvoření stránek pro vytváření, čtení, aktualizaci a odstranění (CRUD) pro model filmu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f05ed-177">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Controllers* **> přidat > novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="f05ed-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![zobrazení výše uvedeného kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="f05ed-179">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **kontroler MVC se zobrazeními a pomocí Entity Framework > Přidat**.</span><span class="sxs-lookup"><span data-stu-id="f05ed-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogové okno Přidat generování uživatelského rozhraní](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="f05ed-181">Dokončete dialog **Přidat řadič** :</span><span class="sxs-lookup"><span data-stu-id="f05ed-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="f05ed-182">**Třída modelu:** *video (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="f05ed-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="f05ed-183">**Třída kontextu dat:** *MvcMovieContext (MvcMovie. data)*</span><span class="sxs-lookup"><span data-stu-id="f05ed-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Přidat kontext dat](adding-model/_static/dc3.png)

* <span data-ttu-id="f05ed-185">**Zobrazení:** Ponechte výchozí hodnotu u každé zaškrtnuté možnosti.</span><span class="sxs-lookup"><span data-stu-id="f05ed-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="f05ed-186">**Název kontroleru:** Zachovat výchozí *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="f05ed-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="f05ed-187">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="f05ed-187">Select **Add**</span></span>

<span data-ttu-id="f05ed-188">Visual Studio vytvoří:</span><span class="sxs-lookup"><span data-stu-id="f05ed-188">Visual Studio creates:</span></span>

* <span data-ttu-id="f05ed-189">Řadič filmů (*Controllers/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="f05ed-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="f05ed-190">Soubory zobrazení Razor pro stránky vytvořit, odstranit, podrobnosti, upravit a index (*zobrazení/filmy/\*. cshtml*)</span><span class="sxs-lookup"><span data-stu-id="f05ed-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="f05ed-191">Automatické vytváření těchto souborů se říká *generování uživatelského rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="f05ed-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f05ed-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f05ed-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="f05ed-193">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="f05ed-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="f05ed-194">V systému Linux exportujte cestu k nástroji pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f05ed-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="f05ed-195">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f05ed-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f05ed-196">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f05ed-197">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="f05ed-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="f05ed-198">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f05ed-198">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="f05ed-199">Vygenerované stránky nemůžete zatím použít, protože databáze neexistuje.</span><span class="sxs-lookup"><span data-stu-id="f05ed-199">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="f05ed-200">Pokud aplikaci spouštíte a kliknete na odkaz **filmové aplikace** , *nemůžete otevřít databázi* nebo *žádnou takovou tabulku:* chybová zpráva videa.</span><span class="sxs-lookup"><span data-stu-id="f05ed-200">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="f05ed-201">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="f05ed-201">Initial migration</span></span>

<span data-ttu-id="f05ed-202">K vytvoření databáze použijte funkci [migrace](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="f05ed-202">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="f05ed-203">Migrace je sada nástrojů, která umožňuje vytvořit a aktualizovat databázi tak, aby odpovídala vašemu datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-203">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-204">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f05ed-205">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="f05ed-205">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="f05ed-206">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="f05ed-206">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="f05ed-207">`Add-Migration InitialCreate`: vygeneruje migrační soubor *_InitialCreate. cs migrace/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="f05ed-207">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="f05ed-208">Argument `InitialCreate` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="f05ed-208">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="f05ed-209">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="f05ed-209">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="f05ed-210">Vzhledem k tomu, že se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="f05ed-210">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="f05ed-211">Schéma databáze je založené na modelu určeném ve třídě `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-211">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="f05ed-212">`Update-Database`: aktualizuje databázi na nejnovější migraci, která vytvořila předchozí příkaz.</span><span class="sxs-lookup"><span data-stu-id="f05ed-212">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="f05ed-213">Tento příkaz spustí metodu `Up` v souboru *migrations/{Time-razítk} _InitialCreate. cs* , ve kterém se vytvoří databáze.</span><span class="sxs-lookup"><span data-stu-id="f05ed-213">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="f05ed-214">Příkaz aktualizace databáze generuje následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="f05ed-214">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="f05ed-215">Pro desetinný sloupec ' Price ' pro typ entity ' film ' nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="f05ed-215">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="f05ed-216">To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-216">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="f05ed-217">Explicitně zadejte typ sloupce SQL Server, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.</span><span class="sxs-lookup"><span data-stu-id="f05ed-217">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="f05ed-218">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-218">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f05ed-219">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-219">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f05ed-220">Spusťte následující příkazy .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="f05ed-220">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="f05ed-221">`ef migrations add InitialCreate`: vygeneruje migrační soubor *_InitialCreate. cs migrace/{timestamp}* .</span><span class="sxs-lookup"><span data-stu-id="f05ed-221">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="f05ed-222">Argument `InitialCreate` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="f05ed-222">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="f05ed-223">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="f05ed-223">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="f05ed-224">Vzhledem k tomu, že se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="f05ed-224">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="f05ed-225">Schéma databáze je založené na modelu určeném ve třídě `MvcMovieContext` (v souboru *data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="f05ed-225">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="f05ed-226">`ef database update`: aktualizuje databázi na nejnovější migraci, která vytvořila předchozí příkaz.</span><span class="sxs-lookup"><span data-stu-id="f05ed-226">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="f05ed-227">Tento příkaz spustí metodu `Up` v souboru *migrations/{Time-razítk} _InitialCreate. cs* , ve kterém se vytvoří databáze.</span><span class="sxs-lookup"><span data-stu-id="f05ed-227">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [more information on the CLI tools for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="f05ed-228">Třída InitialCreate</span><span class="sxs-lookup"><span data-stu-id="f05ed-228">The InitialCreate class</span></span>

<span data-ttu-id="f05ed-229">Projděte si soubor migrace */{timestamp} _InitialCreate. cs* :</span><span class="sxs-lookup"><span data-stu-id="f05ed-229">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="f05ed-230">Metoda `Up` vytvoří tabulku filmů a nakonfiguruje `Id` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="f05ed-230">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="f05ed-231">Metoda `Down` vrátí změny schématu provedené migrací `Up`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-231">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="f05ed-232">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="f05ed-232">Test the app</span></span>

* <span data-ttu-id="f05ed-233">Spusťte aplikaci a klikněte na odkaz **video aplikace** .</span><span class="sxs-lookup"><span data-stu-id="f05ed-233">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="f05ed-234">Pokud se zobrazí výjimka podobná jedné z následujících:</span><span class="sxs-lookup"><span data-stu-id="f05ed-234">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-235">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f05ed-236">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="f05ed-237">Pravděpodobně jste [Krok migrace](#migration)vynechali.</span><span class="sxs-lookup"><span data-stu-id="f05ed-237">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="f05ed-238">Otestujte stránku **vytvořit** .</span><span class="sxs-lookup"><span data-stu-id="f05ed-238">Test the **Create** page.</span></span> <span data-ttu-id="f05ed-239">Zadejte a odešlete data.</span><span class="sxs-lookup"><span data-stu-id="f05ed-239">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="f05ed-240">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="f05ed-240">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f05ed-241">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="f05ed-241">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="f05ed-242">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="f05ed-242">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="f05ed-243">Otestujte stránky **Upravit**, **Podrobnosti**a **Odstranit** .</span><span class="sxs-lookup"><span data-stu-id="f05ed-243">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="f05ed-244">Vkládání závislostí v kontroleru</span><span class="sxs-lookup"><span data-stu-id="f05ed-244">Dependency injection in the controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f05ed-246">Otevřete soubor *Controllers/MoviesController. cs* a prověřte konstruktor:</span><span class="sxs-lookup"><span data-stu-id="f05ed-246">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="f05ed-247">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze (`MvcMovieContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="f05ed-247">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="f05ed-248">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="f05ed-248">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f05ed-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="f05ed-250">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze (`MvcMovieContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="f05ed-250">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="f05ed-251">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="f05ed-251">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="f05ed-252">Použití SQLite pro vývoj, SQL Server pro produkci</span><span class="sxs-lookup"><span data-stu-id="f05ed-252">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="f05ed-253">Když je vybrána možnost SQLite, je kód vygenerovaný šablonou připraven pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="f05ed-253">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="f05ed-254">Následující kód ukazuje, jak vložit <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> do startupu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-254">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="f05ed-255">`IWebHostEnvironment` je vloženo, aby `ConfigureServices` mohl použít SQLite ve vývoji a SQL Server v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="f05ed-255">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="f05ed-256">Modely silného typu a klíčové slovo @model</span><span class="sxs-lookup"><span data-stu-id="f05ed-256">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="f05ed-257">Dříve v tomto kurzu jste viděli, jak může řadič předat data nebo objekty do zobrazení pomocí `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="f05ed-257">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="f05ed-258">`ViewData` slovníku je dynamický objekt, který poskytuje pohodlný způsob, jak předat informace zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f05ed-258">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="f05ed-259">MVC také poskytuje možnost předat objekty modelu silného typu do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f05ed-259">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="f05ed-260">Tento přístup se silnými typy umožňuje kompilovat kontrolu kódu při kompilaci.</span><span class="sxs-lookup"><span data-stu-id="f05ed-260">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="f05ed-261">Mechanizmus pro generování uživatelského rozhraní používal tento přístup (to znamená předání modelu silného typu) s `MoviesController` třídou a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="f05ed-261">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="f05ed-262">Projděte si vygenerovanou metodu `Details` v souboru *Controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="f05ed-262">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="f05ed-263">Parametr `id` je obecně předán jako data směrování.</span><span class="sxs-lookup"><span data-stu-id="f05ed-263">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="f05ed-264">Například `https://localhost:5001/movies/details/1` sady:</span><span class="sxs-lookup"><span data-stu-id="f05ed-264">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="f05ed-265">Kontroler řadiče `movies` (první segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="f05ed-265">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="f05ed-266">Akce, která se má `details` (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="f05ed-266">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="f05ed-267">ID na 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="f05ed-267">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="f05ed-268">`id` můžete předat také pomocí řetězce dotazu následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="f05ed-268">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="f05ed-269">Parametr `id` je definován jako typ s [možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) pro případ, že není zadána hodnota ID.</span><span class="sxs-lookup"><span data-stu-id="f05ed-269">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="f05ed-270">[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán do `FirstOrDefaultAsync` pro výběr entit videa, které odpovídají datům směrování nebo hodnotě řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-270">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="f05ed-271">Pokud je nalezen film, instance `Movie`ho modelu je předána do zobrazení `Details`:</span><span class="sxs-lookup"><span data-stu-id="f05ed-271">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="f05ed-272">Projděte si obsah souboru *views/video/details. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f05ed-272">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="f05ed-273">Příkaz `@model` v horní části souboru zobrazení určuje typ objektu, který zobrazení očekává.</span><span class="sxs-lookup"><span data-stu-id="f05ed-273">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="f05ed-274">Po vytvoření kontroleru filmů byl zahrnut následující příkaz `@model`:</span><span class="sxs-lookup"><span data-stu-id="f05ed-274">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="f05ed-275">Tato direktiva `@model` umožňuje přístup k videu, který kontroler předali do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f05ed-275">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="f05ed-276">Objekt `Model` je silného typu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-276">The `Model` object is strongly typed.</span></span> <span data-ttu-id="f05ed-277">Například v zobrazení *Details. cshtml* kód předá každé pole videa do `DisplayNameFor` a `DisplayFor` pomocníkům HTML pomocí silně typovaného objektu `Model`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-277">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="f05ed-278">Metody a zobrazení `Create` a `Edit` také předají objekt `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-278">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="f05ed-279">Prohlédněte si zobrazení *index. cshtml* a `Index` metoda v kontroleru filmů.</span><span class="sxs-lookup"><span data-stu-id="f05ed-279">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="f05ed-280">Všimněte si, jak kód při volání metody `View` vytvoří objekt `List`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-280">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="f05ed-281">Kód předá tento seznam `Movies` z metody `Index` akce do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="f05ed-281">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="f05ed-282">Po vytvoření kontroleru filmů zahrnuje generování uživatelského rozhraní následující příkaz `@model` v horní části souboru *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f05ed-282">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="f05ed-283">Direktiva `@model` umožňuje přístup k seznamu filmů, které kontroler předává do zobrazení, pomocí silně typovaného objektu `Model`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-283">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="f05ed-284">Například v zobrazení *index. cshtml* kód cykly projde pomocí příkazu `foreach` v rámci silně typovaného `Model` objektu:</span><span class="sxs-lookup"><span data-stu-id="f05ed-284">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="f05ed-285">Vzhledem k tomu, že objekt `Model` je silného typu (jako objekt `IEnumerable<Movie>`), jsou všechny položky ve smyčce zadány jako `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-285">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="f05ed-286">Kromě jiných výhod to znamená, že se vám bude zobrazovat doba kompilace kódu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-286">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f05ed-287">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f05ed-287">Additional resources</span></span>

* [<span data-ttu-id="f05ed-288">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="f05ed-288">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="f05ed-289">Globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="f05ed-289">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="f05ed-290">[Předchozí přidání zobrazení](adding-view.md)
> [Další práce s SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="f05ed-290">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="f05ed-291">Přidat třídu datového modelu</span><span class="sxs-lookup"><span data-stu-id="f05ed-291">Add a data model class</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-292">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-292">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f05ed-293">Klikněte pravým tlačítkem na složku *modely* > **Přidat** **třídu** > .</span><span class="sxs-lookup"><span data-stu-id="f05ed-293">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="f05ed-294">Název třídy **filmu**.</span><span class="sxs-lookup"><span data-stu-id="f05ed-294">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f05ed-295">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-295">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="f05ed-296">Přidat třídu *modely* složku s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="f05ed-296">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="f05ed-297">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="f05ed-297">Scaffold the movie model</span></span>

<span data-ttu-id="f05ed-298">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="f05ed-298">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="f05ed-299">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="f05ed-299">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f05ed-301">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Controllers* **> přidat > novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="f05ed-301">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![zobrazení výše uvedeného kroku](adding-model/_static/add_controller21.png)

<span data-ttu-id="f05ed-303">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte **kontroler MVC se zobrazeními a pomocí Entity Framework > Přidat**.</span><span class="sxs-lookup"><span data-stu-id="f05ed-303">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogové okno Přidat generování uživatelského rozhraní](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="f05ed-305">Dokončete dialog **Přidat řadič** :</span><span class="sxs-lookup"><span data-stu-id="f05ed-305">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="f05ed-306">**Třída modelu:** *video (MvcMovie. Models)*</span><span class="sxs-lookup"><span data-stu-id="f05ed-306">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="f05ed-307">**Třída kontextu dat:** Vyberte ikonu **+** a přidejte výchozí **MvcMovie. Models. MvcMovieContext** .</span><span class="sxs-lookup"><span data-stu-id="f05ed-307">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Přidat kontext dat](adding-model/_static/dc.png)

* <span data-ttu-id="f05ed-309">**Zobrazení:** Ponechte výchozí hodnotu u každé zaškrtnuté možnosti.</span><span class="sxs-lookup"><span data-stu-id="f05ed-309">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="f05ed-310">**Název kontroleru:** Zachovat výchozí *MoviesController*</span><span class="sxs-lookup"><span data-stu-id="f05ed-310">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="f05ed-311">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="f05ed-311">Select **Add**</span></span>

![Dialogové okno Přidat řadič](adding-model/_static/add_controller2.png)

<span data-ttu-id="f05ed-313">Visual Studio vytvoří:</span><span class="sxs-lookup"><span data-stu-id="f05ed-313">Visual Studio creates:</span></span>

* <span data-ttu-id="f05ed-314">[Třída kontextu databáze](xref:data/ef-mvc/intro#create-the-database-context) Entity Framework Core (*data/MvcMovieContext. cs*)</span><span class="sxs-lookup"><span data-stu-id="f05ed-314">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="f05ed-315">Řadič filmů (*Controllers/MoviesController. cs*)</span><span class="sxs-lookup"><span data-stu-id="f05ed-315">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="f05ed-316">Soubory zobrazení Razor pro stránky vytvořit, odstranit, podrobnosti, upravit a index (*zobrazení/filmy/\*. cshtml*)</span><span class="sxs-lookup"><span data-stu-id="f05ed-316">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="f05ed-317">K automatickému vytvoření kontextu databáze a operací [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (vytvoření, čtení, aktualizace a odstranění) se říká *generování uživatelského rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="f05ed-317">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="f05ed-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f05ed-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="f05ed-319">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="f05ed-319">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="f05ed-320">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f05ed-320">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="f05ed-321">V systému Linux exportujte cestu k nástroji pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f05ed-321">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="f05ed-322">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f05ed-322">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="f05ed-323">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-323">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f05ed-324">Otevřete okno příkazového řádku v adresáři projektu (adresář, který obsahuje *Program.cs*, *Startup.cs*, a *.csproj* soubory).</span><span class="sxs-lookup"><span data-stu-id="f05ed-324">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="f05ed-325">Nainstalujte nástroj pro generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f05ed-325">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="f05ed-326">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f05ed-326">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="f05ed-327">Pokud aplikaci spouštíte a kliknete na **filmový odkaz MVC** , zobrazí se chybová zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="f05ed-327">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-328">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-328">Visual Studio</span></span>](#tab/visual-studio)

``` error
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f05ed-329">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-329">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

``` error
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="f05ed-330">Musíte vytvořit databázi a k tomu použijte funkci [migrace](xref:data/ef-mvc/migrations) EF Core.</span><span class="sxs-lookup"><span data-stu-id="f05ed-330">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="f05ed-331">Migrace vám umožní vytvořit databázi, která odpovídá vašemu datovému modelu, a aktualizovat schéma databáze při změně datového modelu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-331">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="f05ed-332">Počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="f05ed-332">Initial migration</span></span>

<span data-ttu-id="f05ed-333">V této části jsou dokončeny následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="f05ed-333">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="f05ed-334">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="f05ed-334">Add an initial migration.</span></span>
* <span data-ttu-id="f05ed-335">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="f05ed-335">Update the database with the initial migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-336">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-336">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f05ed-337">V nabídce **nástroje** vyberte **správce balíčků NuGet** > **konzolu Správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="f05ed-337">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC nabídky](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="f05ed-339">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="f05ed-339">In the PMC, enter the following commands:</span></span>

   ```PMC
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="f05ed-340">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="f05ed-340">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="f05ed-341">Schéma databáze je založené na modelu určeném ve třídě `MvcMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-341">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="f05ed-342">Argument `Initial` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="f05ed-342">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="f05ed-343">Můžete použít libovolný název, ale podle konvence se použije název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="f05ed-343">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="f05ed-344">Další informace najdete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="f05ed-344">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="f05ed-345">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="f05ed-345">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f05ed-346">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-346">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="f05ed-347">`ef migrations add InitialCreate` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="f05ed-347">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="f05ed-348">Schéma databáze je založené na modelu určeném ve třídě `MvcMovieContext` (v souboru *data/MvcMovieContext. cs* ).</span><span class="sxs-lookup"><span data-stu-id="f05ed-348">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="f05ed-349">Argument `InitialCreate` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="f05ed-349">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="f05ed-350">Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="f05ed-350">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f05ed-351">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="f05ed-351">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f05ed-352">ASP.NET Core je sestaven s [vkládáním závislostí (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="f05ed-352">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f05ed-353">Služby (například kontext EF Core DB) jsou během spuštění aplikace zaregistrované v DI.</span><span class="sxs-lookup"><span data-stu-id="f05ed-353">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="f05ed-354">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f05ed-354">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f05ed-355">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="f05ed-355">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f05ed-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f05ed-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f05ed-357">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="f05ed-357">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="f05ed-358">Projděte si následující metodu `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-358">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f05ed-359">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="f05ed-359">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="f05ed-360">`MvcMovieContext` Souřadnice funkce EF Core (vytvoření, čtení, aktualizace, odstranění atd.) pro `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-360">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="f05ed-361">Kontext dat (`MvcMovieContext`) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="f05ed-361">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="f05ed-362">Kontext dat určuje, které entity jsou v datovém modelu zahrnuté:</span><span class="sxs-lookup"><span data-stu-id="f05ed-362">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="f05ed-363">Předchozí kód vytvoří vlastnost [negenerickými\<Movie >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="f05ed-363">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="f05ed-364">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="f05ed-364">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="f05ed-365">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="f05ed-365">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f05ed-366">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-366">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f05ed-367">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="f05ed-367">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="f05ed-368">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f05ed-368">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="f05ed-369">Vytvořili jste kontext databáze a zaregistrovali jej pomocí kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="f05ed-369">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="f05ed-370">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="f05ed-370">Test the app</span></span>

* <span data-ttu-id="f05ed-371">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="f05ed-371">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="f05ed-372">Pokud získáte výjimku databáze podobnou následující:</span><span class="sxs-lookup"><span data-stu-id="f05ed-372">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="f05ed-373">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="f05ed-373">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="f05ed-374">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="f05ed-374">Test the **Create** link.</span></span> <span data-ttu-id="f05ed-375">Zadejte a odešlete data.</span><span class="sxs-lookup"><span data-stu-id="f05ed-375">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="f05ed-376">Není možné zadat desetinné čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="f05ed-376">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="f05ed-377">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") desetinné čárky a USA retweetovat neanglické formáty kalendářního data, aplikace musí být globalizována.</span><span class="sxs-lookup"><span data-stu-id="f05ed-377">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="f05ed-378">Globalizace pokyny najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="f05ed-378">For globalization instructions, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="f05ed-379">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="f05ed-379">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="f05ed-380">Projděte si třídu `Startup`:</span><span class="sxs-lookup"><span data-stu-id="f05ed-380">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="f05ed-381">Předchozí zvýrazněný kód ukazuje kontext databáze filmů přidaný do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="f05ed-381">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="f05ed-382">`services.AddDbContext<MvcMovieContext>(options =>` Určuje databázi, která se má použít, a připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="f05ed-382">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="f05ed-383">`=>` je [operátor lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="f05ed-383">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="f05ed-384">Otevřete soubor *Controllers/MoviesController. cs* a prověřte konstruktor:</span><span class="sxs-lookup"><span data-stu-id="f05ed-384">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="f05ed-385">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení kontextu databáze (`MvcMovieContext`) do kontroleru.</span><span class="sxs-lookup"><span data-stu-id="f05ed-385">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="f05ed-386">Kontext databáze se používá ve všech [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) metody v kontroleru.</span><span class="sxs-lookup"><span data-stu-id="f05ed-386">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="f05ed-387">Modely silného typu a klíčové slovo @model</span><span class="sxs-lookup"><span data-stu-id="f05ed-387">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="f05ed-388">Dříve v tomto kurzu jste viděli, jak může řadič předat data nebo objekty do zobrazení pomocí `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="f05ed-388">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="f05ed-389">`ViewData` slovníku je dynamický objekt, který poskytuje pohodlný způsob, jak předat informace zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f05ed-389">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="f05ed-390">MVC také poskytuje možnost předat objekty modelu silného typu do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f05ed-390">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="f05ed-391">Tento přístup silného typu umožňuje lepší kompilaci kódu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-391">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="f05ed-392">Mechanizmus pro generování uživatelského rozhraní používal tento přístup (to znamená předání modelu silného typu) s `MoviesController` třídou a zobrazeními, když vytváří metody a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="f05ed-392">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="f05ed-393">Projděte si vygenerovanou metodu `Details` v souboru *Controllers/MoviesController. cs* :</span><span class="sxs-lookup"><span data-stu-id="f05ed-393">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="f05ed-394">Parametr `id` je obecně předán jako data směrování.</span><span class="sxs-lookup"><span data-stu-id="f05ed-394">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="f05ed-395">Například `https://localhost:5001/movies/details/1` sady:</span><span class="sxs-lookup"><span data-stu-id="f05ed-395">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="f05ed-396">Kontroler řadiče `movies` (první segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="f05ed-396">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="f05ed-397">Akce, která se má `details` (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="f05ed-397">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="f05ed-398">ID na 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="f05ed-398">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="f05ed-399">`id` můžete předat také pomocí řetězce dotazu následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="f05ed-399">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="f05ed-400">Parametr `id` je definován jako typ s [možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) pro případ, že není zadána hodnota ID.</span><span class="sxs-lookup"><span data-stu-id="f05ed-400">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="f05ed-401">[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán do `FirstOrDefaultAsync` pro výběr entit videa, které odpovídají datům směrování nebo hodnotě řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-401">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="f05ed-402">Pokud je nalezen film, instance `Movie`ho modelu je předána do zobrazení `Details`:</span><span class="sxs-lookup"><span data-stu-id="f05ed-402">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="f05ed-403">Projděte si obsah souboru *views/video/details. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f05ed-403">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="f05ed-404">Zahrnutím příkazu `@model` v horní části souboru zobrazení můžete určit typ objektu, který zobrazení očekává.</span><span class="sxs-lookup"><span data-stu-id="f05ed-404">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="f05ed-405">Když jste vytvořili kontroler filmů, v horní části souboru *Details. cshtml* byl automaticky zahrnut následující příkaz `@model`:</span><span class="sxs-lookup"><span data-stu-id="f05ed-405">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```HTML
@model MvcMovie.Models.Movie
   ```

<span data-ttu-id="f05ed-406">Tato direktiva `@model` umožňuje přístup k videu, který kontroler předává do zobrazení pomocí objektu `Model` se silným typem.</span><span class="sxs-lookup"><span data-stu-id="f05ed-406">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="f05ed-407">Například v zobrazení *Details. cshtml* kód předá každé pole videa do `DisplayNameFor` a `DisplayFor` pomocníkům HTML pomocí silně typovaného objektu `Model`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-407">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="f05ed-408">Metody a zobrazení `Create` a `Edit` také předají objekt `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="f05ed-408">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="f05ed-409">Prohlédněte si zobrazení *index. cshtml* a `Index` metoda v kontroleru filmů.</span><span class="sxs-lookup"><span data-stu-id="f05ed-409">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="f05ed-410">Všimněte si, jak kód při volání metody `View` vytvoří objekt `List`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-410">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="f05ed-411">Kód předá tento seznam `Movies` z metody `Index` akce do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="f05ed-411">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="f05ed-412">Když jste vytvořili kontroler filmů, generování uživatelského rozhraní automaticky obsahuje následující příkaz `@model` v horní části souboru *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f05ed-412">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="f05ed-413">Direktiva `@model` umožňuje přístup k seznamu filmů, které kontroler předává do zobrazení, pomocí silně typovaného objektu `Model`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-413">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="f05ed-414">Například v zobrazení *index. cshtml* kód cykly projde pomocí příkazu `foreach` v rámci silně typovaného `Model` objektu:</span><span class="sxs-lookup"><span data-stu-id="f05ed-414">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="f05ed-415">Vzhledem k tomu, že objekt `Model` je silného typu (jako objekt `IEnumerable<Movie>`), jsou všechny položky ve smyčce zadány jako `Movie`.</span><span class="sxs-lookup"><span data-stu-id="f05ed-415">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="f05ed-416">Kromě jiných výhod to znamená, že se vám bude zobrazovat doba kompilace kódu:</span><span class="sxs-lookup"><span data-stu-id="f05ed-416">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f05ed-417">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f05ed-417">Additional resources</span></span>

* [<span data-ttu-id="f05ed-418">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="f05ed-418">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="f05ed-419">Globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="f05ed-419">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="f05ed-420">[Předchozí přidání zobrazení](adding-view.md)
> [Další práce s databází](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="f05ed-420">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end

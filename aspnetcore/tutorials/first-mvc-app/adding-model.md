---
title: Přidání modelu do aplikace Core MVC ASP.NET
author: rick-anderson
description: Přidejte model do jednoduché aplikace ASP.NET Core.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: e7fc0496438734e13cfafcecf432da4a94737897
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434509"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="8c579-103">Přidání modelu do aplikace Core MVC ASP.NET</span><span class="sxs-lookup"><span data-stu-id="8c579-103">Add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="8c579-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a Tom [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="8c579-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="8c579-105">V této části přidáte třídy pro správu filmů v databázi.</span><span class="sxs-lookup"><span data-stu-id="8c579-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="8c579-106">Tyto třídy budou součástí aplikace **M**VC **"M**odel".</span><span class="sxs-lookup"><span data-stu-id="8c579-106">These classes will be the "**M**odel" part of the **M**VC app.</span></span>

<span data-ttu-id="8c579-107">Tyto třídy s [jádrem entity frameworku](/ef/core) (EF Core) se používají pro práci s databází.</span><span class="sxs-lookup"><span data-stu-id="8c579-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="8c579-108">EF Core je objekt-relační mapování (ORM) framework, který zjednodušuje přístupový kód dat, který musíte napsat.</span><span class="sxs-lookup"><span data-stu-id="8c579-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="8c579-109">Třídy modelu, které vytvoříte, jsou označovány jako třídy POCO (z **P**lain **O**ld **C**LR **O**bjects), protože nemají žádnou závislost na EF Core.</span><span class="sxs-lookup"><span data-stu-id="8c579-109">The model classes you create are known as POCO classes (from **P**lain **O**ld **C**LR **O**bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="8c579-110">Pouze definovat vlastnosti dat, která budou uložena v databázi.</span><span class="sxs-lookup"><span data-stu-id="8c579-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="8c579-111">V tomto kurzu nejprve napíšete třídy modelu a EF Core vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="8c579-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="8c579-112">Přidání třídy datového modelu</span><span class="sxs-lookup"><span data-stu-id="8c579-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c579-114">Klepněte pravým tlačítkem myši na složku *Modely* > **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="8c579-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="8c579-115">Pojmenujte soubor *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8c579-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c579-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c579-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8c579-117">Přidejte do složky *Modely* soubor s názvem *Movie.cs.*</span><span class="sxs-lookup"><span data-stu-id="8c579-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c579-118">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8c579-119">Klepněte pravým tlačítkem myši na složku *Modely* > **Přidat** > **novou třídu** > **prázdnou třídu**.</span><span class="sxs-lookup"><span data-stu-id="8c579-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="8c579-120">Pojmenujte soubor *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8c579-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="8c579-121">Aktualizujte *soubor Movie.cs* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="8c579-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="8c579-122">Třída `Movie` obsahuje `Id` pole, které je vyžadováno databází pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="8c579-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="8c579-123">Atribut <xref:System.ComponentModel.DataAnnotations.DataType> na `ReleaseDate` určuje typ dat (`Date`).</span><span class="sxs-lookup"><span data-stu-id="8c579-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="8c579-124">S tímto atributem:</span><span class="sxs-lookup"><span data-stu-id="8c579-124">With this attribute:</span></span>

* <span data-ttu-id="8c579-125">Uživatel nemusí zadávat informace o čase do pole data.</span><span class="sxs-lookup"><span data-stu-id="8c579-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="8c579-126">Zobrazí se pouze datum, nikoli informace o čase.</span><span class="sxs-lookup"><span data-stu-id="8c579-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="8c579-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) jsou zahrnuty v pozdější kurz.</span><span class="sxs-lookup"><span data-stu-id="8c579-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="8c579-128">Přidání balíčků NuGet</span><span class="sxs-lookup"><span data-stu-id="8c579-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c579-130">V nabídce **Nástroje** vyberte konzolu Správce balíčků **Aplikace NuGet Správce** > **balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="8c579-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![Nabídka PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="8c579-132">V pmc spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8c579-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="8c579-133">Předchozí příkaz přidá zprostředkovatele EF Core SQL Server.</span><span class="sxs-lookup"><span data-stu-id="8c579-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="8c579-134">Balíček zprostředkovatele nainstaluje balíček EF Core jako závislost.</span><span class="sxs-lookup"><span data-stu-id="8c579-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="8c579-135">Další balíčky jsou nainstalovány automaticky v kroku lešení později v kurzu.</span><span class="sxs-lookup"><span data-stu-id="8c579-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c579-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c579-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c579-137">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8c579-138">V nabídce **Projekt** vyberte **Spravovat balíčky NuGet**.</span><span class="sxs-lookup"><span data-stu-id="8c579-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="8c579-139">Do pole **Hledat** v pravém `Microsoft.EntityFrameworkCore.SQLite` horním horním sejde a prohledání stiskněte klávesu **Return.**</span><span class="sxs-lookup"><span data-stu-id="8c579-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="8c579-140">Vyberte odpovídající balíček NuGet a stiskněte tlačítko **Přidat balíček.**</span><span class="sxs-lookup"><span data-stu-id="8c579-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Přidat balíček Nuget core entity frameworku](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="8c579-142">Zobrazí se dialogové okno **Vybrat projekty** s vybraným projektem. `MvcMovie`</span><span class="sxs-lookup"><span data-stu-id="8c579-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="8c579-143">Stiskněte tlačítko **Ok.**</span><span class="sxs-lookup"><span data-stu-id="8c579-143">Press the **Ok** button.</span></span>

<span data-ttu-id="8c579-144">Zobrazí se dialogové okno **Přijetí licence.**</span><span class="sxs-lookup"><span data-stu-id="8c579-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="8c579-145">Podle potřeby zkontrolujte licence a klikněte na tlačítko **Přijmout.**</span><span class="sxs-lookup"><span data-stu-id="8c579-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="8c579-146">Chcete-li nainstalovat následující balíčky NuGet, opakujte výše uvedené kroky:</span><span class="sxs-lookup"><span data-stu-id="8c579-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="8c579-147">Vytvoření třídy kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="8c579-147">Create a database context class</span></span>

<span data-ttu-id="8c579-148">Třída kontextu databáze je potřeba ke koordinaci funkce EF Core (Vytvořit, Číst, Aktualizovat, Odstranit) pro `Movie` model.</span><span class="sxs-lookup"><span data-stu-id="8c579-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="8c579-149">Kontext databáze je odvozen od [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) a určuje entity, které mají být zahrnuty do datového modelu.</span><span class="sxs-lookup"><span data-stu-id="8c579-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="8c579-150">Vytvořte složku *Data.*</span><span class="sxs-lookup"><span data-stu-id="8c579-150">Create a *Data* folder.</span></span>

<span data-ttu-id="8c579-151">Přidejte soubor *Data/MvcMovieContext.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="8c579-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="8c579-152">Předchozí kód vytvoří [vlastnost\<DbSet Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="8c579-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8c579-153">V terminologii entity framework sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="8c579-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8c579-154">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="8c579-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="8c579-155">Registrace kontextu databáze</span><span class="sxs-lookup"><span data-stu-id="8c579-155">Register the database context</span></span>

<span data-ttu-id="8c579-156">ASP.NET Core je postaven s [vstřikování závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8c579-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8c579-157">Služby (například kontext EF Core DB) musí být registrovány s DI při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8c579-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="8c579-158">Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8c579-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8c579-159">Kód konstruktoru, který získá instanci kontextu DB je zobrazen dále v kurzu.</span><span class="sxs-lookup"><span data-stu-id="8c579-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="8c579-160">V této části zaregistrujete kontext databáze s kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="8c579-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="8c579-161">V horní `using` části *Startup.cs*přidejte následující příkazy :</span><span class="sxs-lookup"><span data-stu-id="8c579-161">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="8c579-162">V kládání `Startup.ConfigureServices`přidejte následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="8c579-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c579-164">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="8c579-165">Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="8c579-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8c579-166">Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="8c579-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="8c579-167">Přidání připojovacího řetězce databáze</span><span class="sxs-lookup"><span data-stu-id="8c579-167">Add a database connection string</span></span>

<span data-ttu-id="8c579-168">Přidejte připojovací řetězec do souboru *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="8c579-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c579-170">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="8c579-171">Sestavení projektu jako kontrola chyb kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="8c579-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="8c579-172">Stránky filmu lešení</span><span class="sxs-lookup"><span data-stu-id="8c579-172">Scaffold movie pages</span></span>

<span data-ttu-id="8c579-173">Nástroj zasychání slouží k vytvoření stránek CRUD pro vytvoření, čtení, aktualizaci a odstranění (CRUD).</span><span class="sxs-lookup"><span data-stu-id="8c579-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c579-175">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku *Řadiče* **> Přidat > novou položku scaffolded item**.</span><span class="sxs-lookup"><span data-stu-id="8c579-175">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![pohled na výše uvedený krok](adding-model/_static/add_controller21.png)

<span data-ttu-id="8c579-177">V dialogovém okně **Přidat pomocí okna Pomocí** entity Framework vyberte řadič **MVC se zobrazeními pomocí entity Framework > Add**.</span><span class="sxs-lookup"><span data-stu-id="8c579-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogové okno Přidat zasaná cíp](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="8c579-179">Dokončete dialogové okno **Přidat řadič:**</span><span class="sxs-lookup"><span data-stu-id="8c579-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="8c579-180">**Třída modelu:** *Film (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="8c579-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="8c579-181">**Třída kontextu dat:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="8c579-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Přidat kontext dat](adding-model/_static/dc3.png)

* <span data-ttu-id="8c579-183">**Zobrazení:** Zachovat výchozí nastavení jednotlivých možností</span><span class="sxs-lookup"><span data-stu-id="8c579-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="8c579-184">**Název řadiče:** Zachovat výchozí *ovladač MoviesController*</span><span class="sxs-lookup"><span data-stu-id="8c579-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="8c579-185">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="8c579-185">Select **Add**</span></span>

<span data-ttu-id="8c579-186">Visual Studio vytváří:</span><span class="sxs-lookup"><span data-stu-id="8c579-186">Visual Studio creates:</span></span>

* <span data-ttu-id="8c579-187">Řadič pro filmy *(Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="8c579-187">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="8c579-188">Soubory zobrazení holicí strojek pro stránky Vytvořit, Odstranit, Podrobnosti, Upravit a Index *(Zobrazení/\*Filmy / .cshtml)*</span><span class="sxs-lookup"><span data-stu-id="8c579-188">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="8c579-189">Automatické vytváření těchto souborů se označuje jako *generování uživatelského a uživatelského zařízení*.</span><span class="sxs-lookup"><span data-stu-id="8c579-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="8c579-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c579-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="8c579-191">Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*</span><span class="sxs-lookup"><span data-stu-id="8c579-191">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="8c579-192">Na Linuxu exportujte cestu nástroje lešení:</span><span class="sxs-lookup"><span data-stu-id="8c579-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="8c579-193">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8c579-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c579-194">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8c579-195">Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*</span><span class="sxs-lookup"><span data-stu-id="8c579-195">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="8c579-196">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8c579-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="8c579-197">Stránky skládaným šetřenou šaškárovou fénou zatím nelze použít, protože databáze neexistuje.</span><span class="sxs-lookup"><span data-stu-id="8c579-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="8c579-198">Pokud aplikaci spustíte a kliknete na odkaz **Aplikace pro film,** zobrazí se *chybová zpráva Aplikace Nelze otevřít* nebo žádná *tabulka: Filmová* chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="8c579-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="8c579-199">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="8c579-199">Initial migration</span></span>

<span data-ttu-id="8c579-200">K vytvoření databáze použijte funkci EF Core [Migrations.](xref:data/ef-mvc/migrations)</span><span class="sxs-lookup"><span data-stu-id="8c579-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="8c579-201">Migrace je sada nástrojů, které umožňují vytvořit a aktualizovat databázi tak, aby odpovídala datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="8c579-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c579-203">V nabídce **Nástroje** vyberte konzolu Správce balíčků **Aplikace NuGet Správce** > **balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="8c579-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="8c579-204">Do pmc zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8c579-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="8c579-205">`Add-Migration InitialCreate`: Generuje migrační soubor *Migrace/{timestamp}_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="8c579-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="8c579-206">Argument `InitialCreate` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="8c579-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="8c579-207">Lze použít libovolný název, ale podle konvence je vybrán název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="8c579-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="8c579-208">Protože se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="8c579-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="8c579-209">Schéma databáze je založeno na modelu zadaném `MvcMovieContext` ve třídě.</span><span class="sxs-lookup"><span data-stu-id="8c579-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="8c579-210">`Update-Database`: Aktualizuje databázi na nejnovější migraci, kterou vytvořil předchozí příkaz.</span><span class="sxs-lookup"><span data-stu-id="8c579-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="8c579-211">Tento příkaz `Up` spustí metodu v souboru *Migrations/{time-stamp}_InitialCreate.cs,* který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="8c579-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="8c579-212">Příkaz Aktualizace databáze generuje následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="8c579-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="8c579-213">Pro desítkový sloupec Cena u typu entity Film nebyl zadán žádný typ.</span><span class="sxs-lookup"><span data-stu-id="8c579-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="8c579-214">To způsobí, že hodnoty, které mají být tiše zkrácena, pokud se nevejdou do výchozí přesnosti a měřítka.</span><span class="sxs-lookup"><span data-stu-id="8c579-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="8c579-215">Explicitně určete typ sloupce serveru SQL, který může pojmout všechny hodnoty pomocí 'HasColumnType()'.</span><span class="sxs-lookup"><span data-stu-id="8c579-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="8c579-216">Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.</span><span class="sxs-lookup"><span data-stu-id="8c579-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c579-217">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8c579-218">Spusťte následující příkazy příkazového příkazu .NET Core CLI:</span><span class="sxs-lookup"><span data-stu-id="8c579-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="8c579-219">`ef migrations add InitialCreate`: Generuje migrační soubor *Migrace/{timestamp}_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="8c579-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="8c579-220">Argument `InitialCreate` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="8c579-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="8c579-221">Lze použít libovolný název, ale podle konvence je vybrán název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="8c579-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="8c579-222">Protože se jedná o první migraci, vygenerovaná třída obsahuje kód pro vytvoření schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="8c579-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="8c579-223">Schéma databáze je založeno na modelu zadaném `MvcMovieContext` ve třídě (v souboru *Data/MvcMovieContext.cs).*</span><span class="sxs-lookup"><span data-stu-id="8c579-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="8c579-224">`ef database update`: Aktualizuje databázi na nejnovější migraci, kterou vytvořil předchozí příkaz.</span><span class="sxs-lookup"><span data-stu-id="8c579-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="8c579-225">Tento příkaz `Up` spustí metodu v souboru *Migrations/{time-stamp}_InitialCreate.cs,* který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="8c579-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="8c579-226">Třída InitialCreate</span><span class="sxs-lookup"><span data-stu-id="8c579-226">The InitialCreate class</span></span>

<span data-ttu-id="8c579-227">Zkontrolujte migrační soubor *Migrace/{timestamp}_InitialCreate.cs:*</span><span class="sxs-lookup"><span data-stu-id="8c579-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="8c579-228">Metoda `Up` vytvoří tabulku Film a `Id` nakonfiguruje se jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="8c579-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="8c579-229">Metoda `Down` vrátí změny schématu provedené migrací. `Up`</span><span class="sxs-lookup"><span data-stu-id="8c579-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="8c579-230">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="8c579-230">Test the app</span></span>

* <span data-ttu-id="8c579-231">Spusťte aplikaci a klikněte na odkaz **Aplikace pro film.**</span><span class="sxs-lookup"><span data-stu-id="8c579-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="8c579-232">Pokud se vám výjimka podobná jedné z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="8c579-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c579-234">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="8c579-235">Pravděpodobně jste vynechal [migrace krok](#migration).</span><span class="sxs-lookup"><span data-stu-id="8c579-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="8c579-236">Otestujte stránku **Vytvořit.**</span><span class="sxs-lookup"><span data-stu-id="8c579-236">Test the **Create** page.</span></span> <span data-ttu-id="8c579-237">Zadejte a odešlete data.</span><span class="sxs-lookup"><span data-stu-id="8c579-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="8c579-238">Je možné, že do pole nebudete moci `Price` zadat desetinná čárka.</span><span class="sxs-lookup"><span data-stu-id="8c579-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8c579-239">Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a pro formáty kalendářních dat mimo americkou angličtinu, musí být aplikace globalizovaná.</span><span class="sxs-lookup"><span data-stu-id="8c579-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8c579-240">Pokyny pro globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8c579-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8c579-241">Otestujte stránky **Úpravy**, **Podrobnosti**a **Odstranit.**</span><span class="sxs-lookup"><span data-stu-id="8c579-241">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="8c579-242">Vkládání závislostí v řadiči</span><span class="sxs-lookup"><span data-stu-id="8c579-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c579-244">Otevřete soubor *Controllers/MoviesController.cs* a zkontrolujte konstruktor:</span><span class="sxs-lookup"><span data-stu-id="8c579-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="8c579-245">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k vložení`MvcMovieContext`kontextu databáze ( ) do řadiče.</span><span class="sxs-lookup"><span data-stu-id="8c579-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="8c579-246">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v řadiči.</span><span class="sxs-lookup"><span data-stu-id="8c579-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c579-247">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="8c579-248">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k vložení`MvcMovieContext`kontextu databáze ( ) do řadiče.</span><span class="sxs-lookup"><span data-stu-id="8c579-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="8c579-249">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v řadiči.</span><span class="sxs-lookup"><span data-stu-id="8c579-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="8c579-250">Použití SQLite pro vývoj, SQL Server pro výrobu</span><span class="sxs-lookup"><span data-stu-id="8c579-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="8c579-251">Když je vybránsQLite, vygenerovaný kód šablony je připraven k vývoji.</span><span class="sxs-lookup"><span data-stu-id="8c579-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="8c579-252">Následující kód ukazuje, <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> jak vložit do spuštění.</span><span class="sxs-lookup"><span data-stu-id="8c579-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="8c579-253">`IWebHostEnvironment`je injektován tak `ConfigureServices` lze použít SQLite ve vývoji a SQL Server v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="8c579-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="8c579-254">Modely silného typu @model a klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="8c579-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="8c579-255">Dříve v tomto kurzu jste viděli, jak řadič může předat `ViewData` data nebo objekty do zobrazení pomocí slovníku.</span><span class="sxs-lookup"><span data-stu-id="8c579-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="8c579-256">Slovník `ViewData` je dynamický objekt, který poskytuje pohodlný způsob pozdní sazby předat informace do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8c579-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="8c579-257">MVC také poskytuje možnost předat objekty modelu silného typu do pohledu.</span><span class="sxs-lookup"><span data-stu-id="8c579-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="8c579-258">Tento přístup silného typu umožňuje kontrolu kódu času kompilace.</span><span class="sxs-lookup"><span data-stu-id="8c579-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="8c579-259">Mechanismus lešení používá tento přístup (to znamená předávání modelu silného typu) s třídou `MoviesController` a zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="8c579-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="8c579-260">Prohlédněte si `Details` vygenerovanou metodu v souboru *Controllers/MoviesController.cs:*</span><span class="sxs-lookup"><span data-stu-id="8c579-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="8c579-261">Parametr `id` je obecně předán jako data trasy.</span><span class="sxs-lookup"><span data-stu-id="8c579-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="8c579-262">Například `https://localhost:5001/movies/details/1` sady:</span><span class="sxs-lookup"><span data-stu-id="8c579-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="8c579-263">Řadič k `movies` řadiči (první segment ADRESY URL).</span><span class="sxs-lookup"><span data-stu-id="8c579-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="8c579-264">Akce `details` do (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="8c579-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="8c579-265">Id na 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="8c579-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="8c579-266">Můžete také předat `id` s řetězcem dotazu takto:</span><span class="sxs-lookup"><span data-stu-id="8c579-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="8c579-267">Parametr `id` je definován jako [typ s možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) v případě, že není zadána hodnota ID.</span><span class="sxs-lookup"><span data-stu-id="8c579-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="8c579-268">[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán `FirstOrDefaultAsync` k výběru filmových entit, které odpovídají datům trasy nebo hodnotě řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="8c579-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="8c579-269">Pokud je film nalezen, je `Movie` do `Details` pohledu předána instance modelu:</span><span class="sxs-lookup"><span data-stu-id="8c579-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="8c579-270">Prohlédněte si obsah souboru *Views/Movies/Details.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="8c579-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="8c579-271">Příkaz `@model` v horní části souboru zobrazení určuje typ objektu, který zobrazení očekává.</span><span class="sxs-lookup"><span data-stu-id="8c579-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="8c579-272">Při vytvoření řadiče filmu `@model` byl zahrnut následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8c579-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="8c579-273">Tato `@model` směrnice umožňuje přístup k filmu, který řadič předán zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8c579-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="8c579-274">Objekt `Model` je silně zadán.</span><span class="sxs-lookup"><span data-stu-id="8c579-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="8c579-275">Například v zobrazení *Details.cshtml* kód předá každé `DisplayNameFor` pole `DisplayFor` filmu pomocníkům HTML s `Model` objektem silného typu.</span><span class="sxs-lookup"><span data-stu-id="8c579-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="8c579-276">Metody `Create` `Edit` a pohledy a `Movie` pohledy také předat objekt modelu.</span><span class="sxs-lookup"><span data-stu-id="8c579-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="8c579-277">Zkontrolujte zobrazení *Index.cshtml* a metodu `Index` v kontroleru filmy.</span><span class="sxs-lookup"><span data-stu-id="8c579-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="8c579-278">Všimněte si, `List` jak kód vytvoří `View` objekt při volání metody.</span><span class="sxs-lookup"><span data-stu-id="8c579-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="8c579-279">Kód předá `Movies` tento `Index` seznam z metody akce do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="8c579-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="8c579-280">Když byl vytvořen řadič filmů, lešení `@model` obsahovalo v horní části souboru *Index.cshtml* následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8c579-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="8c579-281">Směrnice `@model` umožňuje přístup k seznamu filmů, které řadič předán zobrazení `Model` pomocí objektu, který je silně zadali.</span><span class="sxs-lookup"><span data-stu-id="8c579-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="8c579-282">Například v zobrazení *Index.cshtml* kód prochází filmy s `foreach` příkazem přes objekt `Model` silného typu:</span><span class="sxs-lookup"><span data-stu-id="8c579-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="8c579-283">Vzhledem `Model` k tomu, že objekt `IEnumerable<Movie>` je silně zadán (jako objekt), každá položka ve smyčce je zadán jako `Movie`.</span><span class="sxs-lookup"><span data-stu-id="8c579-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="8c579-284">Mezi další výhody to znamená, že získáte kontrolu času kompilace kódu.</span><span class="sxs-lookup"><span data-stu-id="8c579-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8c579-285">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8c579-285">Additional resources</span></span>

* [<span data-ttu-id="8c579-286">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="8c579-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="8c579-287">Globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="8c579-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="8c579-288">[Předchozí přidání zobrazení](adding-view.md)
> [další práce s SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="8c579-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="8c579-289">Přidání třídy datového modelu</span><span class="sxs-lookup"><span data-stu-id="8c579-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c579-291">Klepněte pravým tlačítkem myši na složku *Modely* > **Přidat** > **třídu**.</span><span class="sxs-lookup"><span data-stu-id="8c579-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="8c579-292">Pojmenujte třídu **Film**.</span><span class="sxs-lookup"><span data-stu-id="8c579-292">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c579-293">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-293">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="8c579-294">Přidejte třídu do složky *Models* s názvem *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="8c579-294">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="8c579-295">Lešení filmového modelu</span><span class="sxs-lookup"><span data-stu-id="8c579-295">Scaffold the movie model</span></span>

<span data-ttu-id="8c579-296">V této části je filmový model scaffolded.</span><span class="sxs-lookup"><span data-stu-id="8c579-296">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="8c579-297">To znamená, že nástroj lešení vytváří stránky pro operace Vytvořit, Číst, Aktualizovat a Odstranit (CRUD) pro filmový model.</span><span class="sxs-lookup"><span data-stu-id="8c579-297">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-298">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-298">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c579-299">V **Průzkumníku řešení**klepněte pravým tlačítkem myši na složku *Řadiče* **> Přidat > novou položku scaffolded item**.</span><span class="sxs-lookup"><span data-stu-id="8c579-299">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![pohled na výše uvedený krok](adding-model/_static/add_controller21.png)

<span data-ttu-id="8c579-301">V dialogovém okně **Přidat pomocí okna Pomocí** entity Framework vyberte řadič **MVC se zobrazeními pomocí entity Framework > Add**.</span><span class="sxs-lookup"><span data-stu-id="8c579-301">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogové okno Přidat zasaná cíp](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="8c579-303">Dokončete dialogové okno **Přidat řadič:**</span><span class="sxs-lookup"><span data-stu-id="8c579-303">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="8c579-304">**Třída modelu:** *Film (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="8c579-304">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="8c579-305">**Třída kontextu dat:** Vyberte **+** ikonu a přidejte výchozí **MvcMovie.Models.MvcMovieContext**</span><span class="sxs-lookup"><span data-stu-id="8c579-305">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Přidat kontext dat](adding-model/_static/dc.png)

* <span data-ttu-id="8c579-307">**Zobrazení:** Zachovat výchozí nastavení jednotlivých možností</span><span class="sxs-lookup"><span data-stu-id="8c579-307">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="8c579-308">**Název řadiče:** Zachovat výchozí *ovladač MoviesController*</span><span class="sxs-lookup"><span data-stu-id="8c579-308">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="8c579-309">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="8c579-309">Select **Add**</span></span>

![Dialogové okno Přidat řadič](adding-model/_static/add_controller2.png)

<span data-ttu-id="8c579-311">Visual Studio vytváří:</span><span class="sxs-lookup"><span data-stu-id="8c579-311">Visual Studio creates:</span></span>

* <span data-ttu-id="8c579-312">Třída kontextu [základní databáze](xref:data/ef-mvc/intro#create-the-database-context) entity frameworku *(Data/MvcMovieContext.cs)*</span><span class="sxs-lookup"><span data-stu-id="8c579-312">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="8c579-313">Řadič pro filmy *(Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="8c579-313">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="8c579-314">Soubory zobrazení holicí strojek pro stránky Vytvořit, Odstranit, Podrobnosti, Upravit a Index *(Zobrazení/\*Filmy / .cshtml)*</span><span class="sxs-lookup"><span data-stu-id="8c579-314">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="8c579-315">Automatické vytváření kontextu databáze a metody a zobrazení akce [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (vytvoření, čtení, aktualizace a odstranění) se označují jako *generování uživatelského líačku*.</span><span class="sxs-lookup"><span data-stu-id="8c579-315">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c579-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c579-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="8c579-317">Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*</span><span class="sxs-lookup"><span data-stu-id="8c579-317">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="8c579-318">Nainstalujte nástroj lešení:</span><span class="sxs-lookup"><span data-stu-id="8c579-318">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="8c579-319">Na Linuxu exportujte cestu nástroje lešení:</span><span class="sxs-lookup"><span data-stu-id="8c579-319">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="8c579-320">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8c579-320">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c579-321">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-321">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8c579-322">Otevřete příkazové okno v adresáři projektu (Adresář obsahující soubory *Program.cs*, *Startup.cs*a *.csproj).*</span><span class="sxs-lookup"><span data-stu-id="8c579-322">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="8c579-323">Nainstalujte nástroj lešení:</span><span class="sxs-lookup"><span data-stu-id="8c579-323">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="8c579-324">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8c579-324">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="8c579-325">Pokud aplikaci spustíte a kliknete na odkaz **Mvc Movie,** zobrazí se chyba podobná následující:</span><span class="sxs-lookup"><span data-stu-id="8c579-325">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-326">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-326">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c579-327">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-327">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="8c579-328">Je třeba vytvořit databázi a použít funkci EF core [migrace](xref:data/ef-mvc/migrations) k tomu.</span><span class="sxs-lookup"><span data-stu-id="8c579-328">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="8c579-329">Migrace umožňuje vytvořit databázi, která odpovídá datovému modelu a aktualizovat schéma databáze při změně datového modelu.</span><span class="sxs-lookup"><span data-stu-id="8c579-329">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="8c579-330">Počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="8c579-330">Initial migration</span></span>

<span data-ttu-id="8c579-331">V této části jsou dokončeny následující úkoly:</span><span class="sxs-lookup"><span data-stu-id="8c579-331">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="8c579-332">Přidejte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="8c579-332">Add an initial migration.</span></span>
* <span data-ttu-id="8c579-333">Aktualizujte databázi počáteční migrací.</span><span class="sxs-lookup"><span data-stu-id="8c579-333">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-334">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8c579-335">V nabídce **Nástroje** vyberte konzolu Správce balíčků **Aplikace NuGet Správce** > **balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="8c579-335">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![Nabídka PMC](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="8c579-337">Do pmc zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="8c579-337">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="8c579-338">Příkaz `Add-Migration` generuje kód k vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="8c579-338">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="8c579-339">Schéma databáze je založeno na modelu zadaném `MvcMovieContext` ve třídě.</span><span class="sxs-lookup"><span data-stu-id="8c579-339">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="8c579-340">Argument `Initial` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="8c579-340">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="8c579-341">Lze použít libovolný název, ale podle konvence se používá název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="8c579-341">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="8c579-342">Další informace naleznete v tématu <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="8c579-342">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="8c579-343">Příkaz `Update-Database` spustí `Up` metodu v souboru *Migrations/{time-stamp}_InitialCreate.cs,* který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="8c579-343">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c579-344">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="8c579-345">Příkaz `ef migrations add InitialCreate` generuje kód k vytvoření počátečního schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="8c579-345">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="8c579-346">Schéma databáze je založeno na modelu zadaném `MvcMovieContext` ve třídě (v souboru *Data/MvcMovieContext.cs).*</span><span class="sxs-lookup"><span data-stu-id="8c579-346">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="8c579-347">Argument `InitialCreate` je název migrace.</span><span class="sxs-lookup"><span data-stu-id="8c579-347">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="8c579-348">Lze použít libovolný název, ale podle konvence je vybrán název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="8c579-348">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="8c579-349">Prozkoumejte kontext registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="8c579-349">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="8c579-350">ASP.NET Core je postaven s [vstřikování závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8c579-350">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8c579-351">Služby (například kontext EF Core DB) jsou registrovány s DI při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="8c579-351">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="8c579-352">Součásti, které vyžadují tyto služby (například Razor Pages) jsou poskytovány tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8c579-352">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="8c579-353">Kód konstruktoru, který získá instanci kontextu DB je zobrazen dále v kurzu.</span><span class="sxs-lookup"><span data-stu-id="8c579-353">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c579-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c579-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c579-355">Nástroj lešení automaticky vytvořil kontext DB a zaregistroval jej do kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="8c579-355">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="8c579-356">Prozkoumejte `Startup.ConfigureServices` následující metodu.</span><span class="sxs-lookup"><span data-stu-id="8c579-356">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8c579-357">Zvýrazněný řádek byl přidán lešení:</span><span class="sxs-lookup"><span data-stu-id="8c579-357">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="8c579-358">Souřadnice `MvcMovieContext` EF core funkce (vytvořit, číst, aktualizovat, `Movie` odstranit, atd.) pro model.</span><span class="sxs-lookup"><span data-stu-id="8c579-358">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="8c579-359">Kontext dat`MvcMovieContext`( ) je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="8c579-359">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="8c579-360">Kontext dat určuje, které entity jsou zahrnuty do datového modelu:</span><span class="sxs-lookup"><span data-stu-id="8c579-360">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="8c579-361">Předchozí kód vytvoří [vlastnost\<DbSet Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro sadu entit.</span><span class="sxs-lookup"><span data-stu-id="8c579-361">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="8c579-362">V terminologii entity framework sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="8c579-362">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="8c579-363">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="8c579-363">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="8c579-364">Název připojovacího řetězce je předán do kontextu voláním metody na [dbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="8c579-364">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="8c579-365">Pro místní vývoj [ASP.NET konfiguračnísystém Core](xref:fundamentals/configuration/index) přečte připojovací řetězec ze souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="8c579-365">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c579-366">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="8c579-366">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8c579-367">Vytvořili jste kontext DB a zaregistrovali ho s kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="8c579-367">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="8c579-368">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="8c579-368">Test the app</span></span>

* <span data-ttu-id="8c579-369">Spusťte aplikaci `/Movies` a připojte`http://localhost:port/movies`adresu URL v prohlížeči ( ).</span><span class="sxs-lookup"><span data-stu-id="8c579-369">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="8c579-370">Pokud se vám výjimka databáze podobná následující:</span><span class="sxs-lookup"><span data-stu-id="8c579-370">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="8c579-371">Jste vynechal [krok migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="8c579-371">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="8c579-372">Otestujte odkaz **Vytvořit.**</span><span class="sxs-lookup"><span data-stu-id="8c579-372">Test the **Create** link.</span></span> <span data-ttu-id="8c579-373">Zadejte a odešlete data.</span><span class="sxs-lookup"><span data-stu-id="8c579-373">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="8c579-374">Je možné, že do pole nebudete moci `Price` zadat desetinná čárka.</span><span class="sxs-lookup"><span data-stu-id="8c579-374">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="8c579-375">Chcete-li podporovat [ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, která používají čárku (",") pro desetinnou čárku a pro formáty kalendářních dat mimo americkou angličtinu, musí být aplikace globalizovaná.</span><span class="sxs-lookup"><span data-stu-id="8c579-375">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="8c579-376">Pokyny pro globalizaci najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="8c579-376">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="8c579-377">Otestujte odkazy **Upravit**, **Podrobnosti** a **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="8c579-377">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="8c579-378">Zkontrolujte `Startup` třídu:</span><span class="sxs-lookup"><span data-stu-id="8c579-378">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="8c579-379">Předchozí zvýrazněný kód zobrazuje kontext databáze filmu, který se přidává do [kontejneru vkládání závislostí:](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="8c579-379">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="8c579-380">`services.AddDbContext<MvcMovieContext>(options =>`určuje databázi, která má být používána, a připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="8c579-380">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="8c579-381">`=>`je [provozovatelem lambda](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span><span class="sxs-lookup"><span data-stu-id="8c579-381">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="8c579-382">Otevřete soubor *Controllers/MoviesController.cs* a zkontrolujte konstruktor:</span><span class="sxs-lookup"><span data-stu-id="8c579-382">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="8c579-383">Konstruktor používá [vkládání závislostí](xref:fundamentals/dependency-injection) k vložení`MvcMovieContext`kontextu databáze ( ) do řadiče.</span><span class="sxs-lookup"><span data-stu-id="8c579-383">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="8c579-384">Kontext databáze se používá v každé metodě [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) v řadiči.</span><span class="sxs-lookup"><span data-stu-id="8c579-384">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="8c579-385">Modely silného typu @model a klíčové slovo</span><span class="sxs-lookup"><span data-stu-id="8c579-385">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="8c579-386">Dříve v tomto kurzu jste viděli, jak řadič může předat `ViewData` data nebo objekty do zobrazení pomocí slovníku.</span><span class="sxs-lookup"><span data-stu-id="8c579-386">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="8c579-387">Slovník `ViewData` je dynamický objekt, který poskytuje pohodlný způsob pozdní sazby předat informace do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8c579-387">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="8c579-388">MVC také poskytuje možnost předat objekty modelu silného typu do pohledu.</span><span class="sxs-lookup"><span data-stu-id="8c579-388">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="8c579-389">Tento přístup silného typu umožňuje lepší kontrolu času kompilace kódu.</span><span class="sxs-lookup"><span data-stu-id="8c579-389">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="8c579-390">Mechanismus generování uživatelského líacího a parafingu používá tento přístup `MoviesController` (to znamená předávání modelu silného typu) s třídou a zobrazeními při vytváření metod a pohledů.</span><span class="sxs-lookup"><span data-stu-id="8c579-390">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="8c579-391">Prohlédněte si `Details` vygenerovanou metodu v souboru *Controllers/MoviesController.cs:*</span><span class="sxs-lookup"><span data-stu-id="8c579-391">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="8c579-392">Parametr `id` je obecně předán jako data trasy.</span><span class="sxs-lookup"><span data-stu-id="8c579-392">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="8c579-393">Například `https://localhost:5001/movies/details/1` sady:</span><span class="sxs-lookup"><span data-stu-id="8c579-393">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="8c579-394">Řadič k `movies` řadiči (první segment ADRESY URL).</span><span class="sxs-lookup"><span data-stu-id="8c579-394">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="8c579-395">Akce `details` do (druhý segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="8c579-395">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="8c579-396">Id na 1 (poslední segment adresy URL).</span><span class="sxs-lookup"><span data-stu-id="8c579-396">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="8c579-397">Můžete také předat `id` s řetězcem dotazu takto:</span><span class="sxs-lookup"><span data-stu-id="8c579-397">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="8c579-398">Parametr `id` je definován jako [typ s možnou hodnotou null](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) v případě, že není zadána hodnota ID.</span><span class="sxs-lookup"><span data-stu-id="8c579-398">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="8c579-399">[Výraz lambda](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) je předán `FirstOrDefaultAsync` k výběru filmových entit, které odpovídají datům trasy nebo hodnotě řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="8c579-399">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="8c579-400">Pokud je film nalezen, je `Movie` do `Details` pohledu předána instance modelu:</span><span class="sxs-lookup"><span data-stu-id="8c579-400">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="8c579-401">Prohlédněte si obsah souboru *Views/Movies/Details.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="8c579-401">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="8c579-402">Zahrnutím `@model` příkazu do horní části souboru zobrazení můžete určit typ objektu, který zobrazení očekává.</span><span class="sxs-lookup"><span data-stu-id="8c579-402">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="8c579-403">Když jste vytvořili řadič `@model` filmu, byl v horní části souboru *Details.cshtml* automaticky zahrnut následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8c579-403">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="8c579-404">Tato `@model` směrnice umožňuje přístup k filmu, který řadič předán `Model` zobrazení pomocí objektu, který je silně zadaný.</span><span class="sxs-lookup"><span data-stu-id="8c579-404">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="8c579-405">Například v zobrazení *Details.cshtml* kód předá každé `DisplayNameFor` pole `DisplayFor` filmu pomocníkům HTML s `Model` objektem silného typu.</span><span class="sxs-lookup"><span data-stu-id="8c579-405">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="8c579-406">Metody `Create` `Edit` a pohledy a `Movie` pohledy také předat objekt modelu.</span><span class="sxs-lookup"><span data-stu-id="8c579-406">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="8c579-407">Zkontrolujte zobrazení *Index.cshtml* a metodu `Index` v kontroleru filmy.</span><span class="sxs-lookup"><span data-stu-id="8c579-407">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="8c579-408">Všimněte si, `List` jak kód vytvoří `View` objekt při volání metody.</span><span class="sxs-lookup"><span data-stu-id="8c579-408">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="8c579-409">Kód předá `Movies` tento `Index` seznam z metody akce do zobrazení:</span><span class="sxs-lookup"><span data-stu-id="8c579-409">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="8c579-410">Když jste vytvořili řadič pro filmy, lešení automaticky zahrnulo do horní části souboru `@model` *Index.cshtml* následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8c579-410">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="8c579-411">Směrnice `@model` umožňuje přístup k seznamu filmů, které řadič předán zobrazení `Model` pomocí objektu, který je silně zadali.</span><span class="sxs-lookup"><span data-stu-id="8c579-411">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="8c579-412">Například v zobrazení *Index.cshtml* kód prochází filmy s `foreach` příkazem přes objekt `Model` silného typu:</span><span class="sxs-lookup"><span data-stu-id="8c579-412">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="8c579-413">Vzhledem `Model` k tomu, že objekt `IEnumerable<Movie>` je silně zadán (jako objekt), každá položka ve smyčce je zadán jako `Movie`.</span><span class="sxs-lookup"><span data-stu-id="8c579-413">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="8c579-414">Mezi další výhody, to znamená, že dostanete kompilaci čas kontroly kódu:</span><span class="sxs-lookup"><span data-stu-id="8c579-414">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8c579-415">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8c579-415">Additional resources</span></span>

* [<span data-ttu-id="8c579-416">Pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="8c579-416">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="8c579-417">Globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="8c579-417">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="8c579-418">[Předchozí přidání zobrazení](adding-view.md)
> [další práce s databází](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="8c579-418">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end

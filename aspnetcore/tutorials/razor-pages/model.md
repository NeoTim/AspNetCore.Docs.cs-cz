---
title: Přidání modelu do aplikace v ASP.NET Core Razor Pages
author: rick-anderson
description: Objevte, jak přidat třídy pro správu filmy v databázi pomocí Entity Framework Core (jádro EF).
ms.author: riande
ms.date: 05/30/2018
uid: tutorials/razor-pages/model
ms.openlocfilehash: c4b23f75da298e4ee804f649219c2ce466b6d6ea
ms.sourcegitcommit: 710fc5fcac258cc8415976dc66bdb355b3e061d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52299440"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="2aa11-103">Přidání modelu do aplikace v ASP.NET Core Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2aa11-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.1"

[!INCLUDE [model1](~/includes/RP/model1.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="2aa11-104">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="2aa11-104">Add a data model</span></span>

<span data-ttu-id="2aa11-105">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-105">In Solution Explorer, right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2aa11-106">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="2aa11-106">Name the folder *Models*.</span></span>

<span data-ttu-id="2aa11-107">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="2aa11-107">Right click the *Models* folder.</span></span> <span data-ttu-id="2aa11-108">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-108">Select **Add** > **Class**.</span></span> <span data-ttu-id="2aa11-109">Název třídy **film** a nahraďte obsah `Movie` třídy následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="2aa11-109">Name the class **Movie** and replace the contents of the `Movie` class with the following code:</span></span>

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie21/Models/Movie1.cs?name=snippet)]

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2aa11-110">Vygenerované uživatelské rozhraní Video modelu</span><span class="sxs-lookup"><span data-stu-id="2aa11-110">Scaffold the movie model</span></span>

<span data-ttu-id="2aa11-111">V této části je automaticky generovaný model video.</span><span class="sxs-lookup"><span data-stu-id="2aa11-111">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2aa11-112">To znamená vytvoří nástroj pro generování uživatelského rozhraní stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model video.</span><span class="sxs-lookup"><span data-stu-id="2aa11-112">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

<span data-ttu-id="2aa11-113">Vytvoření *stránek/filmy* složky:</span><span class="sxs-lookup"><span data-stu-id="2aa11-113">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2aa11-114">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *stránky* složky > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-114">In **Solution Explorer**, right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2aa11-115">Název složky *filmy*</span><span class="sxs-lookup"><span data-stu-id="2aa11-115">Name the folder *Movies*</span></span>

<span data-ttu-id="2aa11-116">V **Průzkumníka řešení**, klikněte pravým tlačítkem na *stránek/filmy* složky > **přidat** > **novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-116">In **Solution Explorer**, right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Image z předchozích kroků.](model/_static/sca.png)

<span data-ttu-id="2aa11-118">V **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **stránky Razor pomocí Entity Frameworku (CRUD)** > **přidat**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-118">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Image z předchozích kroků.](model/_static/add_scaffold.png)

<span data-ttu-id="2aa11-120">Dokončení **přidat stránky Razor pomocí Entity Frameworku (CRUD)** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="2aa11-120">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2aa11-121">V **třída modelu** rozevírací seznam, vyberte **Movie (RazorPagesMovie.Models)**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-121">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2aa11-122">V **třída kontextu dat** řádek, vyberte **+** (plus) podepsat a přijměte vygenerovaný název **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-122">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2aa11-123">Vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-123">Select **Add**.</span></span>

![Image z předchozích kroků.](model/_static/arp.png)

<span data-ttu-id="2aa11-125">Vygenerované uživatelské rozhraní proces vytvoří a aktualizuje následující soubory:</span><span class="sxs-lookup"><span data-stu-id="2aa11-125">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="2aa11-126">Soubory vytvořené</span><span class="sxs-lookup"><span data-stu-id="2aa11-126">Files created</span></span>

* <span data-ttu-id="2aa11-127">*Stránky/filmy*: vytvoření, odstranění, podrobností, úpravy, Index.</span><span class="sxs-lookup"><span data-stu-id="2aa11-127">*Pages/Movies*: Create, Delete, Details, Edit, Index.</span></span> <span data-ttu-id="2aa11-128">Tyto stránky je podrobně popsaný v dalším kurzu.</span><span class="sxs-lookup"><span data-stu-id="2aa11-128">These pages are detailed in the next tutorial.</span></span>
* <span data-ttu-id="2aa11-129">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2aa11-129">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="2aa11-130">Aktualizovat soubor</span><span class="sxs-lookup"><span data-stu-id="2aa11-130">File updated</span></span>

* <span data-ttu-id="2aa11-131">*Startup.cs*: změny tohoto souboru jsou podrobně popsané v další části.</span><span class="sxs-lookup"><span data-stu-id="2aa11-131">*Startup.cs*: Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="2aa11-132">*appSettings.JSON*: přidat připojovací řetězec použitý pro připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="2aa11-132">*appsettings.json*: The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2aa11-133">Prozkoumání kontextu registrovaný pomocí vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="2aa11-133">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2aa11-134">ASP.NET Core využívá rozhraní [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="2aa11-134">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2aa11-135">Služby (například kontext EF Core databáze) jsou registrované pomocí vkládání závislostí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2aa11-135">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2aa11-136">Komponenty, které vyžadují tyto služby (například stránky Razor) jsou k dispozici tyto služby prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="2aa11-136">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2aa11-137">Později v tomto kurzu se zobrazí kód konstruktor, který získá instanci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="2aa11-137">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2aa11-138">Nástroj pro generování uživatelského rozhraní automaticky vytvoří kontext databáze a zaregistrovaného kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="2aa11-138">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2aa11-139">Zkontrolujte `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="2aa11-139">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2aa11-140">Zvýrazněný řádek byl přidán modulem scaffolder:</span><span class="sxs-lookup"><span data-stu-id="2aa11-140">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Startup.cs?name=snippet_ConfigureServices&highlight=12-13)]

<span data-ttu-id="2aa11-141">Hlavní třída, která koordinuje EF Core funkce pro daný datový model je třídy kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="2aa11-141">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="2aa11-142">Kontext dat je odvozen z [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="2aa11-142">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="2aa11-143">Kontext dat určuje entit, které jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="2aa11-143">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="2aa11-144">V tomto projektu je s názvem třídy `RazorPagesMovieContext`.</span><span class="sxs-lookup"><span data-stu-id="2aa11-144">In this project, the class is named `RazorPagesMovieContext`.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2aa11-145">Předchozí kód vytvoří [DbSet\<video >](/dotnet/api/microsoft.entityframeworkcore.dbset-1) vlastnost sady entit.</span><span class="sxs-lookup"><span data-stu-id="2aa11-145">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2aa11-146">Terminologie Entity Framework obvykle sadu entit odpovídá databázové tabulky.</span><span class="sxs-lookup"><span data-stu-id="2aa11-146">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2aa11-147">Entita odpovídající řádek v tabulce.</span><span class="sxs-lookup"><span data-stu-id="2aa11-147">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2aa11-148">Název připojovacího řetězce je předán v rámci voláním metody na [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) objektu.</span><span class="sxs-lookup"><span data-stu-id="2aa11-148">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="2aa11-149">Pro místní vývoj [ASP.NET Core konfigurační systém](xref:fundamentals/configuration/index) načte připojovací řetězec z *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="2aa11-149">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="pmc"></a>
## <a name="perform-initial-migration"></a><span data-ttu-id="2aa11-150">Provést počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="2aa11-150">Perform initial migration</span></span>

<span data-ttu-id="2aa11-151">V této části pomocí konzoly Správce balíčků (PMC) na:</span><span class="sxs-lookup"><span data-stu-id="2aa11-151">In this section, you use the Package Manager Console (PMC) to:</span></span>

* <span data-ttu-id="2aa11-152">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="2aa11-152">Add an initial migration.</span></span>
* <span data-ttu-id="2aa11-153">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="2aa11-153">Update the database with the initial migration.</span></span>

<span data-ttu-id="2aa11-154">Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-154">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2aa11-156">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="2aa11-156">In the PMC, enter the following commands:</span></span>

```PMC
Add-Migration Initial
Update-Database
```

<span data-ttu-id="2aa11-157">Alternativně lze použít následující příkazy rozhraní příkazového řádku .NET Core ze složky projektu:</span><span class="sxs-lookup"><span data-stu-id="2aa11-157">Alternatively, the following .NET Core CLI commands can be used from the project folder:</span></span>

```console
dotnet ef migrations add Initial
dotnet ef database update
```

<span data-ttu-id="2aa11-158">Ignorujte následující zpráva upozornění, která můžete opravit v pozdějších kurzech:</span><span class="sxs-lookup"><span data-stu-id="2aa11-158">Ignore the following warning message, which you fix in a later tutorial:</span></span>

```console
Microsoft.EntityFrameworkCore.Model.Validation[30000]
      No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'ForHasColumnType()'.
```

<span data-ttu-id="2aa11-159">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="2aa11-159">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2aa11-160">Schéma je založen na zadaném v modelu `RazorPagesMovieContext` (v *Data/RazorPagesMovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="2aa11-160">The schema is based on the model specified in the `RazorPagesMovieContext` (In the *Data/RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="2aa11-161">`Initial` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="2aa11-161">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="2aa11-162">Můžete použít libovolný název, ale podle konvence zvolte název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="2aa11-162">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="2aa11-163">Zobrazit [Úvod do migrace](xref:data/ef-mvc/migrations#introduction-to-migrations) Další informace.</span><span class="sxs-lookup"><span data-stu-id="2aa11-163">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="2aa11-164">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="2aa11-164">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

<span data-ttu-id="2aa11-165">Pokud dojde k chybě:</span><span class="sxs-lookup"><span data-stu-id="2aa11-165">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2aa11-166">Je provedena [kroku migrace](#pmc).</span><span class="sxs-lookup"><span data-stu-id="2aa11-166">You missed the [migrations step](#pmc).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!INCLUDE [model1](~/includes/RP/model1.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="2aa11-167">Přidání datového modelu</span><span class="sxs-lookup"><span data-stu-id="2aa11-167">Add a data model</span></span>

<span data-ttu-id="2aa11-168">V Průzkumníku řešení klikněte pravým tlačítkem myši **RazorPagesMovie** Projekt > **přidat** > **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-168">In Solution Explorer, right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2aa11-169">Název složky *modely*.</span><span class="sxs-lookup"><span data-stu-id="2aa11-169">Name the folder *Models*.</span></span>

<span data-ttu-id="2aa11-170">Klikněte pravým tlačítkem myši *modely* složky.</span><span class="sxs-lookup"><span data-stu-id="2aa11-170">Right click the *Models* folder.</span></span> <span data-ttu-id="2aa11-171">Vyberte **přidat** > **třídy**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-171">Select **Add** > **Class**.</span></span> <span data-ttu-id="2aa11-172">Název třídy **film** a přidejte následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2aa11-172">Name the class **Movie** and add the following properties:</span></span>

[!INCLUDE [model 2](~/includes/RP/model2.md)]

<a name="cs"></a>
### <a name="add-a-database-connection-string"></a><span data-ttu-id="2aa11-173">Přidat připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="2aa11-173">Add a database connection string</span></span>

<span data-ttu-id="2aa11-174">Přidat připojovací řetězec pro *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="2aa11-174">Add a connection string to the *appsettings.json* file.</span></span>

[!code-json[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings.json?highlight=8-10)]

<a name="reg"></a>
###  <a name="register-the-database-context"></a><span data-ttu-id="2aa11-175">Zaregistrujte kontext databáze</span><span class="sxs-lookup"><span data-stu-id="2aa11-175">Register the database context</span></span>

<span data-ttu-id="2aa11-176">Zaregistrujte kontext databáze s [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru v [ConfigureServices metoda spuštění třídy](xref:fundamentals/startup#the-startup-class) (*Startup.cs*):</span><span class="sxs-lookup"><span data-stu-id="2aa11-176">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in the [ConfigureServices method of the Startup class](xref:fundamentals/startup#the-startup-class) (*Startup.cs*):</span></span>

[!code-csharp[](../../tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/Startup.cs?name=snippet_ConfigureServices&highlight=3-5,7-9)]

<span data-ttu-id="2aa11-177">Sestavte projekt a ověřte, že nemáte žádné chyby.</span><span class="sxs-lookup"><span data-stu-id="2aa11-177">Build the project to verify you don't have any errors.</span></span>

<a name="pmc"></a>
## <a name="add-scaffold-tooling-and-perform-initial-migration"></a><span data-ttu-id="2aa11-178">Přidat vygenerované uživatelské rozhraní nástroje a provádět počáteční migraci</span><span class="sxs-lookup"><span data-stu-id="2aa11-178">Add scaffold tooling and perform initial migration</span></span>

<span data-ttu-id="2aa11-179">V této části pomocí konzoly Správce balíčků (PMC) na:</span><span class="sxs-lookup"><span data-stu-id="2aa11-179">In this section, you use the Package Manager Console (PMC) to:</span></span>

* <span data-ttu-id="2aa11-180">Přidejte balíček generování kódu web Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2aa11-180">Add the Visual Studio web code generation package.</span></span> <span data-ttu-id="2aa11-181">Tento balíček je potřeba ke spouštění modulu generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2aa11-181">This package is required to run the scaffolding engine.</span></span>
* <span data-ttu-id="2aa11-182">Přidáte počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="2aa11-182">Add an initial migration.</span></span>
* <span data-ttu-id="2aa11-183">Aktualizujte počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="2aa11-183">Update the database with the initial migration.</span></span>

<span data-ttu-id="2aa11-184">Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** > **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="2aa11-184">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC nabídky](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2aa11-186">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="2aa11-186">In the PMC, enter the following commands:</span></span>

```powershell
Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design -Version 2.0.3
Add-Migration Initial
Update-Database
```

<span data-ttu-id="2aa11-187">Alternativně můžete použít následující příkazy rozhraní příkazového řádku .NET Core:</span><span class="sxs-lookup"><span data-stu-id="2aa11-187">Alternatively, the following .NET Core CLI commands can be used:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet ef migrations add Initial
dotnet ef database update
```

<span data-ttu-id="2aa11-188">Ignorujte tuto zprávu:</span><span class="sxs-lookup"><span data-stu-id="2aa11-188">Ignore the following message:</span></span>

```console
Microsoft.EntityFrameworkCore.Model.Validation[30000]
      No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'ForHasColumnType()'
```

<span data-ttu-id="2aa11-189">Opravit, který v dalším kurzu.</span><span class="sxs-lookup"><span data-stu-id="2aa11-189">You fix that in the next tutorial.</span></span>

<span data-ttu-id="2aa11-190">`Install-Package` Nainstaluje nástroje potřebné ke spuštění modulu generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2aa11-190">The `Install-Package` command installs the tooling required to run the scaffolding engine.</span></span>

<span data-ttu-id="2aa11-191">`Add-Migration` Příkaz vygeneruje kód pro vytvoření schématu počáteční databáze.</span><span class="sxs-lookup"><span data-stu-id="2aa11-191">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2aa11-192">Schéma je založen na zadaném v modelu `DbContext` (v *Models/MovieContext.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="2aa11-192">The schema is based on the model specified in the `DbContext` (In the *Models/MovieContext.cs* file).</span></span> <span data-ttu-id="2aa11-193">`Initial` Argument se používá k pojmenování migrace.</span><span class="sxs-lookup"><span data-stu-id="2aa11-193">The `Initial` argument is used to name the migrations.</span></span> <span data-ttu-id="2aa11-194">Můžete použít libovolný název, ale podle konvence zvolte název, který popisuje migraci.</span><span class="sxs-lookup"><span data-stu-id="2aa11-194">You can use any name, but by convention you choose a name that describes the migration.</span></span> <span data-ttu-id="2aa11-195">Zobrazit [Úvod do migrace](xref:data/ef-mvc/migrations#introduction-to-migrations) Další informace.</span><span class="sxs-lookup"><span data-stu-id="2aa11-195">See [Introduction to migrations](xref:data/ef-mvc/migrations#introduction-to-migrations) for more information.</span></span>

<span data-ttu-id="2aa11-196">`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="2aa11-196">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

[!INCLUDE [model 4windows](~/includes/RP/model4Win.md)]

[!INCLUDE [model 4](~/includes/RP/model4tbl.md)]

::: moniker-end

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2aa11-197">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="2aa11-197">Test the app</span></span>

* <span data-ttu-id="2aa11-198">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="2aa11-198">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>
* <span data-ttu-id="2aa11-199">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="2aa11-199">Test the **Create** link.</span></span>

  ![Vytvoření stránky](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* <span data-ttu-id="2aa11-201">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="2aa11-201">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2aa11-202">Pokud dojde k výjimce SQL, ověřte máte spusťte migrace a aktualizována v databázi.</span><span class="sxs-lookup"><span data-stu-id="2aa11-202">If you get a SQL exception, verify you have run migrations and updated the database.</span></span>

<span data-ttu-id="2aa11-203">V dalším kurzu vysvětluje souborů vytvořených databázovým generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2aa11-203">The next tutorial explains the files created by scaffolding.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2aa11-204">[Předchozí: Začínáme](xref:tutorials/razor-pages/razor-pages-start)
> [Další: generované uživatelské rozhraní pro stránky Razor](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2aa11-204">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

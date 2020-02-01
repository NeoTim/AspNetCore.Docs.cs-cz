---
title: Razor Pages s EF Core v ASP.NET Core – migrace – 4 z 8
author: rick-anderson
description: V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu v aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: 4246d9d8f6e6ba9e9d735b944ed748720bcf3e16
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928378"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="bf45c-103">Razor Pages s EF Core v ASP.NET Core – migrace – 4 z 8</span><span class="sxs-lookup"><span data-stu-id="bf45c-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

<span data-ttu-id="bf45c-104">Tím, že [Dykstra](https://github.com/tdykstra), [Jan P Smith](https://twitter.com/thereformedprog)a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bf45c-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bf45c-105">V tomto kurzu se seznámíte s funkcí migrace EF Core pro správu změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="bf45c-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="bf45c-106">Při vývoji nové aplikace se datový model často mění.</span><span class="sxs-lookup"><span data-stu-id="bf45c-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="bf45c-107">Pokaždé, když se model změní, model se nesynchronizuje s databází.</span><span class="sxs-lookup"><span data-stu-id="bf45c-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="bf45c-108">Tato série kurzů začala konfigurací Entity Framework vytvořit databázi, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="bf45c-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="bf45c-109">Pokaždé, když se datový model změní, je nutné odstranit databázi.</span><span class="sxs-lookup"><span data-stu-id="bf45c-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="bf45c-110">Při příštím spuštění aplikace volání `EnsureCreated` znovu vytvoří databázi tak, aby odpovídala novému datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="bf45c-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="bf45c-111">Třída `DbInitializer` pak spouští k osazení nové databáze.</span><span class="sxs-lookup"><span data-stu-id="bf45c-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="bf45c-112">Tento přístup k uchování databáze v synchronizaci s datovým modelem funguje dobře, dokud aplikaci nenainstalujete do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="bf45c-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="bf45c-113">Když je aplikace spuštěná v produkčním prostředí, obvykle ukládá data, která je potřeba zachovat.</span><span class="sxs-lookup"><span data-stu-id="bf45c-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="bf45c-114">Aplikace nemůže začít s testovací databází pokaždé, když je provedena změna (například přidání nového sloupce).</span><span class="sxs-lookup"><span data-stu-id="bf45c-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="bf45c-115">Funkce migrace EF Core vyřeší tento problém tím, že umožňuje EF Core aktualizovat schéma databáze místo vytvoření nové databáze.</span><span class="sxs-lookup"><span data-stu-id="bf45c-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="bf45c-116">Místo vyřazení a opětovného vytvoření databáze při změně datového modelu migrace aktualizuje schéma a zachová stávající data.</span><span class="sxs-lookup"><span data-stu-id="bf45c-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="bf45c-117">Odstranit databázi</span><span class="sxs-lookup"><span data-stu-id="bf45c-117">Drop the database</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bf45c-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf45c-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf45c-119">K odstranění databáze použijte **Průzkumník objektů systému SQL Server** (SSOX) nebo spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="bf45c-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bf45c-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf45c-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bf45c-121">Spuštěním následujícího příkazu na příkazovém řádku nainstalujte EF CLI:</span><span class="sxs-lookup"><span data-stu-id="bf45c-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="bf45c-122">Na příkazovém řádku přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="bf45c-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="bf45c-123">Složka projektu obsahuje soubor *ContosoUniversity. csproj* .</span><span class="sxs-lookup"><span data-stu-id="bf45c-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="bf45c-124">Odstraňte soubor *cu. DB* nebo spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="bf45c-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="bf45c-125">Vytvoření prvotní migrace</span><span class="sxs-lookup"><span data-stu-id="bf45c-125">Create an initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bf45c-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf45c-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf45c-127">V PMC spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="bf45c-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bf45c-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf45c-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bf45c-129">Ujistěte se, že je příkazový řádek ve složce projektu, a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="bf45c-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="bf45c-130">Metody nahoru a dolů</span><span class="sxs-lookup"><span data-stu-id="bf45c-130">Up and Down methods</span></span>

<span data-ttu-id="bf45c-131">Příkaz EF Core `migrations add` vygeneroval kód pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="bf45c-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="bf45c-132">Tento kód migrace se nachází v části *migrace\<časové razítko > souboru _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="bf45c-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="bf45c-133">Metoda `Up` třídy `InitialCreate` vytvoří tabulky databáze, které odpovídají sadám entit datového modelu.</span><span class="sxs-lookup"><span data-stu-id="bf45c-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="bf45c-134">Metoda `Down` je odstraní, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bf45c-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="bf45c-135">Předchozí kód je určen pro počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="bf45c-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="bf45c-136">Kód:</span><span class="sxs-lookup"><span data-stu-id="bf45c-136">The code:</span></span>

* <span data-ttu-id="bf45c-137">Byl vygenerován příkazem `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="bf45c-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="bf45c-138">Je proveden pomocí příkazu `database update`.</span><span class="sxs-lookup"><span data-stu-id="bf45c-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="bf45c-139">Vytvoří databázi pro datový model určený třídou kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="bf45c-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="bf45c-140">Pro název souboru se používá parametr názvu migrace (v příkladu "InitialCreate").</span><span class="sxs-lookup"><span data-stu-id="bf45c-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="bf45c-141">Název migrace může být libovolný platný název souboru.</span><span class="sxs-lookup"><span data-stu-id="bf45c-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="bf45c-142">Nejlepší je zvolit slovo nebo frázi, která shrnuje, co se v migraci provádí.</span><span class="sxs-lookup"><span data-stu-id="bf45c-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="bf45c-143">Například migrace, která přidala tabulku oddělení, se může jmenovat "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="bf45c-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="bf45c-144">Tabulka historie migrace</span><span class="sxs-lookup"><span data-stu-id="bf45c-144">The migrations history table</span></span>

* <span data-ttu-id="bf45c-145">K prozkoumání databáze použijte SSOX nebo nástroj SQLite.</span><span class="sxs-lookup"><span data-stu-id="bf45c-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="bf45c-146">Všimněte si přidání `__EFMigrationsHistory` tabulky.</span><span class="sxs-lookup"><span data-stu-id="bf45c-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="bf45c-147">V tabulce `__EFMigrationsHistory` je sledováno, které migrace byly pro databázi aplikovány.</span><span class="sxs-lookup"><span data-stu-id="bf45c-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="bf45c-148">Zobrazit data v tabulce `__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="bf45c-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="bf45c-149">Zobrazuje jeden řádek pro první migraci.</span><span class="sxs-lookup"><span data-stu-id="bf45c-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="bf45c-150">Snímek datového modelu</span><span class="sxs-lookup"><span data-stu-id="bf45c-150">The data model snapshot</span></span>

<span data-ttu-id="bf45c-151">Migrace vytvoří *snímek* aktuálního datového modelu v části *migrace/SchoolContextModelSnapshot. cs*.</span><span class="sxs-lookup"><span data-stu-id="bf45c-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="bf45c-152">Když přidáte migraci, EF určí, co se změnilo, porovnáním s aktuálním datovým modelem se souborem snímku.</span><span class="sxs-lookup"><span data-stu-id="bf45c-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="bf45c-153">Vzhledem k tomu, že soubor snímku sleduje stav datového modelu, nelze migraci odstranit odstraněním souboru `<timestamp>_<migrationname>.cs`.</span><span class="sxs-lookup"><span data-stu-id="bf45c-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="bf45c-154">Chcete-li zálohovat nejnovější migraci, je nutné použít příkaz `migrations remove`.</span><span class="sxs-lookup"><span data-stu-id="bf45c-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="bf45c-155">Tento příkaz odstraní migraci a zajistí správné resetování snímku.</span><span class="sxs-lookup"><span data-stu-id="bf45c-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="bf45c-156">Další informace najdete v tématu [Odebrání migrace pro dotnet EF](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="bf45c-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="bf45c-157">Odebrat EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="bf45c-157">Remove EnsureCreated</span></span>

<span data-ttu-id="bf45c-158">Tato řada kurzů začala pomocí `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="bf45c-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="bf45c-159">`EnsureCreated` nevytvoří tabulku historie migrace a nedá se použít s migracemi.</span><span class="sxs-lookup"><span data-stu-id="bf45c-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="bf45c-160">Je navržená pro testování nebo rychlé vytváření prototypů, kde se databáze vynechává a často se znovu vytvoří.</span><span class="sxs-lookup"><span data-stu-id="bf45c-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="bf45c-161">Od tohoto okamžiku budou kurzy používat migrace.</span><span class="sxs-lookup"><span data-stu-id="bf45c-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="bf45c-162">Do pole *data/DBInitializer. cs*přidejte následující řádek:</span><span class="sxs-lookup"><span data-stu-id="bf45c-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="bf45c-163">Spusťte aplikaci a ověřte, že je databáze osazená.</span><span class="sxs-lookup"><span data-stu-id="bf45c-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="bf45c-164">Použití migrace v produkčním prostředí</span><span class="sxs-lookup"><span data-stu-id="bf45c-164">Applying migrations in production</span></span>

<span data-ttu-id="bf45c-165">Doporučujeme, aby **provozní aplikace** nevolaly funkci [Database. migrace](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="bf45c-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="bf45c-166">`Migrate` by se neměla volat z aplikace, která je nasazená na serverovou farmu.</span><span class="sxs-lookup"><span data-stu-id="bf45c-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="bf45c-167">Pokud je aplikace škálovaná na více instancí serveru, je obtížné zajistit, aby aktualizace schématu databáze neprobíhaly na více serverech nebo byly v konfliktu s přístupem pro čtení a zápis.</span><span class="sxs-lookup"><span data-stu-id="bf45c-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="bf45c-168">Migrace databáze by se měla provádět v rámci nasazení a řízeným způsobem.</span><span class="sxs-lookup"><span data-stu-id="bf45c-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="bf45c-169">Přístupy k migraci do produkční databáze zahrnují:</span><span class="sxs-lookup"><span data-stu-id="bf45c-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="bf45c-170">Použití migrace k vytváření skriptů SQL a používání skriptů SQL v nasazení.</span><span class="sxs-lookup"><span data-stu-id="bf45c-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="bf45c-171">Spuštění `dotnet ef database update` ze kontrolovaného prostředí.</span><span class="sxs-lookup"><span data-stu-id="bf45c-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bf45c-172">Odstraňování problémů</span><span class="sxs-lookup"><span data-stu-id="bf45c-172">Troubleshooting</span></span>

<span data-ttu-id="bf45c-173">Pokud aplikace používá SQL Server LocalDB a zobrazí následující výjimku:</span><span class="sxs-lookup"><span data-stu-id="bf45c-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="bf45c-174">Řešení může být spuštěno `dotnet ef database update` v příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="bf45c-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="bf45c-175">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="bf45c-175">Additional resources</span></span>

* <span data-ttu-id="bf45c-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="bf45c-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="bf45c-177">Konzola Správce balíčků (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="bf45c-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="bf45c-178">Další kroky</span><span class="sxs-lookup"><span data-stu-id="bf45c-178">Next steps</span></span>

<span data-ttu-id="bf45c-179">V dalším kurzu se vytvoří datový model a přidají se vlastnosti entity a nové entity.</span><span class="sxs-lookup"><span data-stu-id="bf45c-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="bf45c-180">[Předchozí kurz](xref:data/ef-rp/sort-filter-page)
> [Další kurz](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="bf45c-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bf45c-181">V tomto kurzu se používá funkce migrace EF Core pro správu změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="bf45c-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="bf45c-182">Pokud narazíte na problémy, které nemůžete vyřešit, stáhněte [dokončenou aplikaci](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="bf45c-182">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="bf45c-183">Při vývoji nové aplikace se datový model často mění.</span><span class="sxs-lookup"><span data-stu-id="bf45c-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="bf45c-184">Pokaždé, když se model změní, model se nesynchronizuje s databází.</span><span class="sxs-lookup"><span data-stu-id="bf45c-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="bf45c-185">Tento kurz se zahájil konfigurací Entity Framework k vytvoření databáze, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="bf45c-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="bf45c-186">Pokaždé, když se změní datový model:</span><span class="sxs-lookup"><span data-stu-id="bf45c-186">Each time the data model changes:</span></span>

* <span data-ttu-id="bf45c-187">DATABÁZE je vyřazena.</span><span class="sxs-lookup"><span data-stu-id="bf45c-187">The DB is dropped.</span></span>
* <span data-ttu-id="bf45c-188">EF vytvoří nový, který odpovídá modelu.</span><span class="sxs-lookup"><span data-stu-id="bf45c-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="bf45c-189">Aplikace vysemena databáze s testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="bf45c-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="bf45c-190">Tento přístup k uchování databáze v synchronizaci s datovým modelem funguje dobře, dokud aplikaci nenainstalujete do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="bf45c-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="bf45c-191">Když je aplikace spuštěná v produkčním prostředí, obvykle ukládá data, která je potřeba zachovat.</span><span class="sxs-lookup"><span data-stu-id="bf45c-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="bf45c-192">Aplikace nemůže začít s testovací databází pokaždé, když je provedena změna (například přidání nového sloupce).</span><span class="sxs-lookup"><span data-stu-id="bf45c-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="bf45c-193">Funkce migrace EF Core vyřeší tento problém tím, že umožňuje EF Core aktualizovat schéma databáze místo vytvoření nové databáze.</span><span class="sxs-lookup"><span data-stu-id="bf45c-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="bf45c-194">Místo vyřazení a opětovného vytvoření databáze při změně datového modelu migrace aktualizuje schéma a zachová stávající data.</span><span class="sxs-lookup"><span data-stu-id="bf45c-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="bf45c-195">Odstranit databázi</span><span class="sxs-lookup"><span data-stu-id="bf45c-195">Drop the database</span></span>

<span data-ttu-id="bf45c-196">Použijte **Průzkumník objektů systému SQL Server** (SSOX) nebo `database drop` příkaz:</span><span class="sxs-lookup"><span data-stu-id="bf45c-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bf45c-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf45c-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf45c-198">V **konzole správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="bf45c-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
```

<span data-ttu-id="bf45c-199">Pokud chcete získat informace o nápovědě, spusťte `Get-Help about_EntityFrameworkCore` z PMC.</span><span class="sxs-lookup"><span data-stu-id="bf45c-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bf45c-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf45c-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bf45c-201">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="bf45c-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="bf45c-202">Složka projektu obsahuje soubor *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="bf45c-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="bf45c-203">V příkazovém okně zadejte následující:</span><span class="sxs-lookup"><span data-stu-id="bf45c-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="bf45c-204">Vytvoření prvotní migrace a aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="bf45c-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="bf45c-205">Sestavte projekt a vytvořte první migraci.</span><span class="sxs-lookup"><span data-stu-id="bf45c-205">Build the project and create the first migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bf45c-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf45c-206">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bf45c-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf45c-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="bf45c-208">Projděte si metody nahoru a dolů.</span><span class="sxs-lookup"><span data-stu-id="bf45c-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="bf45c-209">Příkaz EF Core `migrations add` vygeneroval kód pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="bf45c-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="bf45c-210">Tento kód migrace se nachází v části *migrace\<časové razítko > souboru _InitialCreate. cs* .</span><span class="sxs-lookup"><span data-stu-id="bf45c-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="bf45c-211">Metoda `Up` třídy `InitialCreate` vytvoří tabulky databáze, které odpovídají sadám entit datového modelu.</span><span class="sxs-lookup"><span data-stu-id="bf45c-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="bf45c-212">Metoda `Down` je odstraní, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bf45c-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="bf45c-213">Migrace zavolá metodu `Up` pro implementaci změn datového modelu pro migraci.</span><span class="sxs-lookup"><span data-stu-id="bf45c-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="bf45c-214">Když zadáte příkaz pro vrácení aktualizace, migrace zavolá metodu `Down`.</span><span class="sxs-lookup"><span data-stu-id="bf45c-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="bf45c-215">Předchozí kód je určen pro počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="bf45c-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="bf45c-216">Tento kód byl vytvořen při spuštění příkazu `migrations add InitialCreate`.</span><span class="sxs-lookup"><span data-stu-id="bf45c-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="bf45c-217">Pro název souboru se používá parametr názvu migrace (v příkladu "InitialCreate").</span><span class="sxs-lookup"><span data-stu-id="bf45c-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="bf45c-218">Název migrace může být libovolný platný název souboru.</span><span class="sxs-lookup"><span data-stu-id="bf45c-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="bf45c-219">Nejlepší je zvolit slovo nebo frázi, která shrnuje, co se v migraci provádí.</span><span class="sxs-lookup"><span data-stu-id="bf45c-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="bf45c-220">Například migrace, která přidala tabulku oddělení, se může jmenovat "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="bf45c-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="bf45c-221">Při vytvoření počáteční migrace a existence databáze:</span><span class="sxs-lookup"><span data-stu-id="bf45c-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="bf45c-222">Generuje se kód pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="bf45c-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="bf45c-223">Kód pro vytvoření databáze není nutné spouštět, protože databáze již odpovídá datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="bf45c-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="bf45c-224">Pokud je kód pro vytvoření databáze spuštěn, neprovede žádné změny, protože databáze již odpovídá datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="bf45c-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="bf45c-225">Když se aplikace nasadí do nového prostředí, musí se pro vytvoření databáze spustit kód pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="bf45c-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="bf45c-226">Dříve byla databáze vyřazena a neexistuje, takže migrace vytvoří novou databázi.</span><span class="sxs-lookup"><span data-stu-id="bf45c-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="bf45c-227">Snímek datového modelu</span><span class="sxs-lookup"><span data-stu-id="bf45c-227">The data model snapshot</span></span>

<span data-ttu-id="bf45c-228">Migrace vytvoří *snímek* aktuálního schématu databáze v části *migrations/SchoolContextModelSnapshot. cs*.</span><span class="sxs-lookup"><span data-stu-id="bf45c-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="bf45c-229">Když přidáte migraci, EF určí, co se změnilo, porovnáním datového modelu se souborem snímku.</span><span class="sxs-lookup"><span data-stu-id="bf45c-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="bf45c-230">K odstranění migrace použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="bf45c-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bf45c-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf45c-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bf45c-232">Odebrání – migrace</span><span class="sxs-lookup"><span data-stu-id="bf45c-232">Remove-Migration</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bf45c-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf45c-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="bf45c-234">Další informace najdete v tématu [Odebrání migrace pro dotnet EF](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="bf45c-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="bf45c-235">Příkaz odebrat migrace odstraní migraci a zajistí správné resetování snímku.</span><span class="sxs-lookup"><span data-stu-id="bf45c-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="bf45c-236">Odebrání EnsureCreated a testování aplikace</span><span class="sxs-lookup"><span data-stu-id="bf45c-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="bf45c-237">Pro prvotní vývoj se použil `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="bf45c-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="bf45c-238">V tomto kurzu se používají migrace.</span><span class="sxs-lookup"><span data-stu-id="bf45c-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="bf45c-239">`EnsureCreated` má následující omezení:</span><span class="sxs-lookup"><span data-stu-id="bf45c-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="bf45c-240">Vynechá migrace a vytvoří databázi a schéma.</span><span class="sxs-lookup"><span data-stu-id="bf45c-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="bf45c-241">Nevytváří tabulku migrace.</span><span class="sxs-lookup"><span data-stu-id="bf45c-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="bf45c-242">Nelze *použít* s migracemi.</span><span class="sxs-lookup"><span data-stu-id="bf45c-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="bf45c-243">Je určený pro testování nebo rychlé vytváření prototypů, kde se databáze vynechává a často se znovu vytvoří.</span><span class="sxs-lookup"><span data-stu-id="bf45c-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="bf45c-244">Odebrat `EnsureCreated`:</span><span class="sxs-lookup"><span data-stu-id="bf45c-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="bf45c-245">Spusťte aplikaci a ověřte, že je tato databáze osazená.</span><span class="sxs-lookup"><span data-stu-id="bf45c-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="bf45c-246">Kontrola databáze</span><span class="sxs-lookup"><span data-stu-id="bf45c-246">Inspect the database</span></span>

<span data-ttu-id="bf45c-247">K prozkoumání databáze použijte **Průzkumník objektů systému SQL Server** .</span><span class="sxs-lookup"><span data-stu-id="bf45c-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="bf45c-248">Všimněte si přidání `__EFMigrationsHistory` tabulky.</span><span class="sxs-lookup"><span data-stu-id="bf45c-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="bf45c-249">Tabulka `__EFMigrationsHistory` uchovává přehled o tom, které migrace byly pro databázi aplikovány.</span><span class="sxs-lookup"><span data-stu-id="bf45c-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="bf45c-250">Umožňuje zobrazit data v tabulce `__EFMigrationsHistory`, která pro první migraci zobrazuje jeden řádek.</span><span class="sxs-lookup"><span data-stu-id="bf45c-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="bf45c-251">Poslední přihlášení v předchozím příkladu výstupu CLI ukazuje příkaz INSERT, který tento řádek vytvoří.</span><span class="sxs-lookup"><span data-stu-id="bf45c-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="bf45c-252">Spusťte aplikaci a ověřte, že vše funguje.</span><span class="sxs-lookup"><span data-stu-id="bf45c-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="bf45c-253">Použití migrace v produkčním prostředí</span><span class="sxs-lookup"><span data-stu-id="bf45c-253">Applying migrations in production</span></span>

<span data-ttu-id="bf45c-254">Doporučujeme **, aby produkční** aplikace nevolaly metodu [Database. migrace](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="bf45c-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="bf45c-255">`Migrate` by se neměla volat z aplikace v serverové farmě.</span><span class="sxs-lookup"><span data-stu-id="bf45c-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="bf45c-256">Například pokud je aplikace nasazená v cloudu s možností škálování na více instancí (spouští se víc instancí aplikace).</span><span class="sxs-lookup"><span data-stu-id="bf45c-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="bf45c-257">Migrace databáze by se měla provádět v rámci nasazení a řízeným způsobem.</span><span class="sxs-lookup"><span data-stu-id="bf45c-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="bf45c-258">Přístupy k migraci do produkční databáze zahrnují:</span><span class="sxs-lookup"><span data-stu-id="bf45c-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="bf45c-259">Použití migrace k vytváření skriptů SQL a používání skriptů SQL v nasazení.</span><span class="sxs-lookup"><span data-stu-id="bf45c-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="bf45c-260">Spuštění `dotnet ef database update` ze kontrolovaného prostředí.</span><span class="sxs-lookup"><span data-stu-id="bf45c-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="bf45c-261">EF Core používá tabulku `__MigrationsHistory` a zjistí, jestli je potřeba spustit nějaké migrace.</span><span class="sxs-lookup"><span data-stu-id="bf45c-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="bf45c-262">Pokud je databáze aktuální, nespustí se žádná migrace.</span><span class="sxs-lookup"><span data-stu-id="bf45c-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="bf45c-263">Odstraňování problémů</span><span class="sxs-lookup"><span data-stu-id="bf45c-263">Troubleshooting</span></span>

<span data-ttu-id="bf45c-264">Stáhněte [dokončenou aplikaci](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="bf45c-264">Download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="bf45c-265">Aplikace vygeneruje následující výjimku:</span><span class="sxs-lookup"><span data-stu-id="bf45c-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="bf45c-266">Řešení: Spusťte `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="bf45c-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="bf45c-267">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="bf45c-267">Additional resources</span></span>

* [<span data-ttu-id="bf45c-268">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="bf45c-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="bf45c-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="bf45c-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="bf45c-270">Konzola Správce balíčků (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="bf45c-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="bf45c-271">[Předchozí](xref:data/ef-rp/sort-filter-page)
> [Další](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="bf45c-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end


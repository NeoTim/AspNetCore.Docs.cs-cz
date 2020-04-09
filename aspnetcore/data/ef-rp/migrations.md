---
title: Razor Stránky s EF core v ASP.NET core - Migrace - 4 z 8
author: rick-anderson
description: V tomto kurzu začnete používat funkci migrace EF Core pro správu změn datového modelu v ASP.NET aplikaci Core MVC.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: 86fd83c898fce8e121e4d259aaca12c59591e606
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656532"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="58561-103">Razor Stránky s EF core v ASP.NET core - Migrace - 4 z 8</span><span class="sxs-lookup"><span data-stu-id="58561-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

<span data-ttu-id="58561-104">[Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), a Rick [Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="58561-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="58561-105">Tento kurz představuje funkci migrace EF Core pro správu změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="58561-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="58561-106">Když je vyvinuta nová aplikace, datový model se často mění.</span><span class="sxs-lookup"><span data-stu-id="58561-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="58561-107">Pokaždé, když se model změní, model se synchronizuje s databází.</span><span class="sxs-lookup"><span data-stu-id="58561-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="58561-108">Tato série kurzů byla spuštěna konfigurací entity Framework k vytvoření databáze, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="58561-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="58561-109">Pokaždé, když se změní datový model, budete muset vypustit databázi.</span><span class="sxs-lookup"><span data-stu-id="58561-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="58561-110">Při příštím spuštění aplikace volání `EnsureCreated` znovu vytvoří databázi tak, aby odpovídala novému datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="58561-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="58561-111">Třída `DbInitializer` pak spustí osiva nové databáze.</span><span class="sxs-lookup"><span data-stu-id="58561-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="58561-112">Tento přístup k udržování databáze v synchronizaci s datovým modelem funguje dobře, dokud nenasadíte aplikaci do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="58561-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="58561-113">Když je aplikace spuštěná v produkčním prostředí, obvykle ukládá data, která je potřeba udržovat.</span><span class="sxs-lookup"><span data-stu-id="58561-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="58561-114">Aplikace nemůže začínat testovací databází pokaždé, když je provedena změna (například přidání nového sloupce).</span><span class="sxs-lookup"><span data-stu-id="58561-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="58561-115">Funkce EF Core Migrations tento problém řeší povolením EF Core k aktualizaci schématu databáze namísto vytvoření nové databáze.</span><span class="sxs-lookup"><span data-stu-id="58561-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="58561-116">Místo uvolnění a opětovného vytvoření databáze při změně datového modelu migrace aktualizuje schéma a zachová existující data.</span><span class="sxs-lookup"><span data-stu-id="58561-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="58561-117">Přetažení databáze</span><span class="sxs-lookup"><span data-stu-id="58561-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58561-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58561-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58561-119">Pomocí **Průzkumníka objektů serveru SQL Server** (SSOX) odstraňte databázi nebo spusťte následující příkaz v konzole Správce **balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="58561-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="58561-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58561-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="58561-121">Spusťte na příkazovém řádku následující příkaz k instalaci příkazového řádku EF:</span><span class="sxs-lookup"><span data-stu-id="58561-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="58561-122">V příkazovém řádku přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="58561-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="58561-123">Složka projektu obsahuje soubor *ContosoUniversity.csproj.*</span><span class="sxs-lookup"><span data-stu-id="58561-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="58561-124">Odstraňte soubor *CU.db* nebo spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="58561-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="58561-125">Vytvoření počáteční migrace</span><span class="sxs-lookup"><span data-stu-id="58561-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58561-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58561-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58561-127">Spusťte v pmc následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="58561-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="58561-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58561-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="58561-129">Zkontrolujte, zda je příkazový řádek ve složce projektu, a spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="58561-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="58561-130">Metody nahoru a dolů</span><span class="sxs-lookup"><span data-stu-id="58561-130">Up and Down methods</span></span>

<span data-ttu-id="58561-131">Příkaz EF `migrations add` Core generovaný kód k vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="58561-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="58561-132">Tento kód migrace je v souboru *\<časové razítko migrace>_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="58561-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="58561-133">Metoda `Up` třídy `InitialCreate` vytvoří databázové tabulky, které odpovídají sady entit datového modelu.</span><span class="sxs-lookup"><span data-stu-id="58561-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="58561-134">Metoda `Down` je odstraní, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="58561-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="58561-135">Předchozí kód je pro počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="58561-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="58561-136">Kód:</span><span class="sxs-lookup"><span data-stu-id="58561-136">The code:</span></span>

* <span data-ttu-id="58561-137">Byl generován příkazem. `migrations add InitialCreate`</span><span class="sxs-lookup"><span data-stu-id="58561-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="58561-138">Je proveden příkazem. `database update`</span><span class="sxs-lookup"><span data-stu-id="58561-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="58561-139">Vytvoří databázi pro datový model určený třídou kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="58561-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="58561-140">Parametr názvu migrace ("InitialCreate" v příkladu) se používá pro název souboru.</span><span class="sxs-lookup"><span data-stu-id="58561-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="58561-141">Název migrace může být libovolný platný název souboru.</span><span class="sxs-lookup"><span data-stu-id="58561-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="58561-142">Je nejlepší zvolit slovo nebo frázi, která shrnuje, co se v migraci děje.</span><span class="sxs-lookup"><span data-stu-id="58561-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="58561-143">Například migrace, která přidala tabulku oddělení, může být nazvána "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="58561-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="58561-144">Tabulka historie migrace</span><span class="sxs-lookup"><span data-stu-id="58561-144">The migrations history table</span></span>

* <span data-ttu-id="58561-145">Ke kontrole databáze použijte ssox nebo nástroj SQLite.</span><span class="sxs-lookup"><span data-stu-id="58561-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="58561-146">Všimněte si `__EFMigrationsHistory` přidání tabulky.</span><span class="sxs-lookup"><span data-stu-id="58561-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="58561-147">Tabulka `__EFMigrationsHistory` sleduje, které migrace byly použity do databáze.</span><span class="sxs-lookup"><span data-stu-id="58561-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="58561-148">Zobrazení dat v `__EFMigrationsHistory` tabulce.</span><span class="sxs-lookup"><span data-stu-id="58561-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="58561-149">Zobrazuje jeden řádek pro první migraci.</span><span class="sxs-lookup"><span data-stu-id="58561-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="58561-150">Snímek datového modelu</span><span class="sxs-lookup"><span data-stu-id="58561-150">The data model snapshot</span></span>

<span data-ttu-id="58561-151">Migrace vytvoří *snímek* aktuálního datového modelu v *migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="58561-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="58561-152">Když přidáte migraci, EF určuje, co se změnilo porovnáním aktuálního datového modelu se souborem snímku.</span><span class="sxs-lookup"><span data-stu-id="58561-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="58561-153">Vzhledem k tomu, že soubor snímku sleduje stav datového modelu, `<timestamp>_<migrationname>.cs` nelze migraci odstranit odstraněním souboru.</span><span class="sxs-lookup"><span data-stu-id="58561-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="58561-154">Chcete-li zálohovat poslední migraci, `migrations remove` musíte použít příkaz.</span><span class="sxs-lookup"><span data-stu-id="58561-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="58561-155">Tento příkaz odstraní migraci a zajistí, že snímek je správně resetován.</span><span class="sxs-lookup"><span data-stu-id="58561-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="58561-156">Další informace naleznete [v tématu dotnet ef migrace odebrat](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="58561-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="58561-157">Odebrat ensureCreated</span><span class="sxs-lookup"><span data-stu-id="58561-157">Remove EnsureCreated</span></span>

<span data-ttu-id="58561-158">Tato série kurzů `EnsureCreated`byla spuštěna pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="58561-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="58561-159">`EnsureCreated`nevytvoří tabulku historie migrace, a proto ji nelze s migrací použít.</span><span class="sxs-lookup"><span data-stu-id="58561-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="58561-160">Je určen pro testování nebo rychlé prototypování, kde je databáze vynechána a často znovu vytvořena.</span><span class="sxs-lookup"><span data-stu-id="58561-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="58561-161">Od tohoto okamžiku budou kurzy používat migrace.</span><span class="sxs-lookup"><span data-stu-id="58561-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="58561-162">V *souboru Data/DBInitializer.cs*zakomentujte následující řádek:</span><span class="sxs-lookup"><span data-stu-id="58561-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="58561-163">Spusťte aplikaci a ověřte, zda je databáze nasazena.</span><span class="sxs-lookup"><span data-stu-id="58561-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="58561-164">Použití migrace ve výrobě</span><span class="sxs-lookup"><span data-stu-id="58561-164">Applying migrations in production</span></span>

<span data-ttu-id="58561-165">Doporučujeme, aby produkční aplikace **při** spuštění aplikace [nevolaly Database.Migrate.](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)</span><span class="sxs-lookup"><span data-stu-id="58561-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="58561-166">`Migrate`by neměl být volán z aplikace, která je nasazena do serverové farmy.</span><span class="sxs-lookup"><span data-stu-id="58561-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="58561-167">Pokud je aplikace škálována na více instancí serveru, je těžké zajistit, aby aktualizace schématu databáze nenastaly z více serverů nebo konflikt s přístupem pro čtení a zápis.</span><span class="sxs-lookup"><span data-stu-id="58561-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="58561-168">Migrace databáze by měla být provedena jako součást nasazení a řízeným způsobem.</span><span class="sxs-lookup"><span data-stu-id="58561-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="58561-169">Přístupy migrace produkční databáze zahrnují:</span><span class="sxs-lookup"><span data-stu-id="58561-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="58561-170">Použití migrace k vytvoření skriptů SQL a použití skriptů SQL v nasazení.</span><span class="sxs-lookup"><span data-stu-id="58561-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="58561-171">Běží `dotnet ef database update` z řízeného prostředí.</span><span class="sxs-lookup"><span data-stu-id="58561-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="58561-172">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="58561-172">Troubleshooting</span></span>

<span data-ttu-id="58561-173">Pokud aplikace používá SQL Server LocalDB a zobrazí následující výjimku:</span><span class="sxs-lookup"><span data-stu-id="58561-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="58561-174">Řešením může být `dotnet ef database update` spuštění na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="58561-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="58561-175">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="58561-175">Additional resources</span></span>

* <span data-ttu-id="58561-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="58561-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="58561-177">Konzola Správce balíčků (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="58561-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="58561-178">Další kroky</span><span class="sxs-lookup"><span data-stu-id="58561-178">Next steps</span></span>

<span data-ttu-id="58561-179">Další kurz vytvoří datový model a přidá vlastnosti entit a nové entity.</span><span class="sxs-lookup"><span data-stu-id="58561-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="58561-180">[Předchozí kurz](xref:data/ef-rp/sort-filter-page)
> [Další kurz](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="58561-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="58561-181">V tomto kurzu se používá funkce migrace EF Core pro správu změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="58561-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="58561-182">Pokud narazíte na problémy, které nelze vyřešit, stáhněte si [dokončenou aplikaci](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="58561-182">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="58561-183">Když je vyvinuta nová aplikace, datový model se často mění.</span><span class="sxs-lookup"><span data-stu-id="58561-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="58561-184">Pokaždé, když se model změní, model se synchronizuje s databází.</span><span class="sxs-lookup"><span data-stu-id="58561-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="58561-185">Tento kurz byl zahájen konfigurací entity framework k vytvoření databáze, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="58561-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="58561-186">Pokaždé, když se změní datový model:</span><span class="sxs-lookup"><span data-stu-id="58561-186">Each time the data model changes:</span></span>

* <span data-ttu-id="58561-187">DB je vynechána.</span><span class="sxs-lookup"><span data-stu-id="58561-187">The DB is dropped.</span></span>
* <span data-ttu-id="58561-188">EF vytvoří nový, který odpovídá modelu.</span><span class="sxs-lookup"><span data-stu-id="58561-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="58561-189">Aplikace semena DB s testovací midata.</span><span class="sxs-lookup"><span data-stu-id="58561-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="58561-190">Tento přístup k udržování DB v synchronizaci s datovým modelem funguje dobře, dokud nenasadíte aplikaci do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="58561-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="58561-191">Když je aplikace spuštěná v produkčním prostředí, obvykle ukládá data, která je potřeba udržovat.</span><span class="sxs-lookup"><span data-stu-id="58561-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="58561-192">Aplikace nemůže začínat testovací db pokaždé, když je provedena změna (například přidání nového sloupce).</span><span class="sxs-lookup"><span data-stu-id="58561-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="58561-193">Funkce EF Core Migrations tento problém řeší povolením EF Core k aktualizaci schématu DB namísto vytvoření nové databáze.</span><span class="sxs-lookup"><span data-stu-id="58561-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="58561-194">Místo uvolnění a opětovného vytvoření databáze při změně datového modelu migrace aktualizuje schéma a zachová existující data.</span><span class="sxs-lookup"><span data-stu-id="58561-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="58561-195">Přetažení databáze</span><span class="sxs-lookup"><span data-stu-id="58561-195">Drop the database</span></span>

<span data-ttu-id="58561-196">Použijte **Průzkumník objektů serveru SQL** Server `database drop` (SSOX) nebo příkaz:</span><span class="sxs-lookup"><span data-stu-id="58561-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58561-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58561-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58561-198">V **konzole Správce balíčků** (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="58561-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="58561-199">Spusťte `Get-Help about_EntityFrameworkCore` z PMC a získejte informace nápovědy.</span><span class="sxs-lookup"><span data-stu-id="58561-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="58561-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58561-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="58561-201">Otevřete příkazové okno a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="58561-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="58561-202">Složka projektu obsahuje *soubor Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="58561-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="58561-203">Do příkazového okna zadejte následující:</span><span class="sxs-lookup"><span data-stu-id="58561-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="58561-204">Vytvoření počáteční migrace a aktualizace databáze</span><span class="sxs-lookup"><span data-stu-id="58561-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="58561-205">Sestavte projekt a vytvořte první migraci.</span><span class="sxs-lookup"><span data-stu-id="58561-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58561-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58561-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="58561-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58561-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="58561-208">Prozkoumejte metody nahoru a dolů</span><span class="sxs-lookup"><span data-stu-id="58561-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="58561-209">Příkaz EF `migrations add` Core generovaný kód pro vytvoření DB.</span><span class="sxs-lookup"><span data-stu-id="58561-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="58561-210">Tento kód migrace je v souboru *\<časové razítko migrace>_InitialCreate.cs.*</span><span class="sxs-lookup"><span data-stu-id="58561-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="58561-211">Metoda `Up` třídy `InitialCreate` vytvoří tabulky DB, které odpovídají množině entit datového modelu.</span><span class="sxs-lookup"><span data-stu-id="58561-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="58561-212">Metoda `Down` je odstraní, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="58561-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="58561-213">Migrace volá `Up` metodu k implementaci změn datového modelu pro migraci.</span><span class="sxs-lookup"><span data-stu-id="58561-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="58561-214">Když zadáte příkaz pro vrácení aktualizace zpět, `Down` migrace volá metodu.</span><span class="sxs-lookup"><span data-stu-id="58561-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="58561-215">Předchozí kód je pro počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="58561-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="58561-216">Tento kód byl `migrations add InitialCreate` vytvořen při spuštění příkazu.</span><span class="sxs-lookup"><span data-stu-id="58561-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="58561-217">Parametr názvu migrace ("InitialCreate" v příkladu) se používá pro název souboru.</span><span class="sxs-lookup"><span data-stu-id="58561-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="58561-218">Název migrace může být libovolný platný název souboru.</span><span class="sxs-lookup"><span data-stu-id="58561-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="58561-219">Je nejlepší zvolit slovo nebo frázi, která shrnuje, co se v migraci děje.</span><span class="sxs-lookup"><span data-stu-id="58561-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="58561-220">Například migrace, která přidala tabulku oddělení, může být nazvána "AddDepartmentTable".</span><span class="sxs-lookup"><span data-stu-id="58561-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="58561-221">Pokud je vytvořena počáteční migrace a db existuje:</span><span class="sxs-lookup"><span data-stu-id="58561-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="58561-222">Je generován kód vytvoření DB.</span><span class="sxs-lookup"><span data-stu-id="58561-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="58561-223">Kód vytvoření DB není nutné spustit, protože DB již odpovídá datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="58561-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="58561-224">Pokud je spuštěn kód vytvoření DB, neprovede žádné změny, protože DB již odpovídá datovému modelu.</span><span class="sxs-lookup"><span data-stu-id="58561-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="58561-225">Když je aplikace nasazena do nového prostředí, musí být kód vytvoření DB spuštěn, aby se vytvořila DATABÁZE.</span><span class="sxs-lookup"><span data-stu-id="58561-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="58561-226">Dříve DB byla vynechána a neexistuje, takže migrace vytvoří nové DB.</span><span class="sxs-lookup"><span data-stu-id="58561-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="58561-227">Snímek datového modelu</span><span class="sxs-lookup"><span data-stu-id="58561-227">The data model snapshot</span></span>

<span data-ttu-id="58561-228">Migrace vytvořit *snímek* aktuální schéma databáze v *migrace/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="58561-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="58561-229">Když přidáte migraci, EF určuje, co se změnilo porovnáním datového modelu se souborem snímku.</span><span class="sxs-lookup"><span data-stu-id="58561-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="58561-230">Chcete-li migraci odstranit, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="58561-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58561-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58561-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58561-232">Odebrat-migrace</span><span class="sxs-lookup"><span data-stu-id="58561-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="58561-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58561-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="58561-234">Další informace naleznete [v tématu dotnet ef migrace odebrat](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="58561-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="58561-235">Příkaz Remove migrations odstraní migraci a zajistí správné obnovení snímku.</span><span class="sxs-lookup"><span data-stu-id="58561-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="58561-236">Odebrat EnsureCreated a otestovat aplikaci</span><span class="sxs-lookup"><span data-stu-id="58561-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="58561-237">Pro časný `EnsureCreated` vývoj byl použit.</span><span class="sxs-lookup"><span data-stu-id="58561-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="58561-238">V tomto kurzu se používají migrace.</span><span class="sxs-lookup"><span data-stu-id="58561-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="58561-239">`EnsureCreated`má následující omezení:</span><span class="sxs-lookup"><span data-stu-id="58561-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="58561-240">Obchází migrace a vytvoří DB a schéma.</span><span class="sxs-lookup"><span data-stu-id="58561-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="58561-241">Nevytvoří tabulku migrace.</span><span class="sxs-lookup"><span data-stu-id="58561-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="58561-242">Nelze *not* použít s migrací.</span><span class="sxs-lookup"><span data-stu-id="58561-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="58561-243">Je určen pro testování nebo rychlé prototypování, kde db je vynechána a znovu vytvořit často.</span><span class="sxs-lookup"><span data-stu-id="58561-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="58561-244">Odebrat `EnsureCreated`:</span><span class="sxs-lookup"><span data-stu-id="58561-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="58561-245">Spusťte aplikaci a ověřte, že db je nasazena.</span><span class="sxs-lookup"><span data-stu-id="58561-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="58561-246">Kontrola databáze</span><span class="sxs-lookup"><span data-stu-id="58561-246">Inspect the database</span></span>

<span data-ttu-id="58561-247">Ke kontrole databáze použijte **Průzkumník objektů serveru SQL Server.**</span><span class="sxs-lookup"><span data-stu-id="58561-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="58561-248">Všimněte si `__EFMigrationsHistory` přidání tabulky.</span><span class="sxs-lookup"><span data-stu-id="58561-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="58561-249">Tabulka `__EFMigrationsHistory` sleduje, které migrace byly použity v db.</span><span class="sxs-lookup"><span data-stu-id="58561-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="58561-250">Zobrazit data v `__EFMigrationsHistory` tabulce, zobrazí jeden řádek pro první migraci.</span><span class="sxs-lookup"><span data-stu-id="58561-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="58561-251">Poslední protokol v předchozím příkladu výstupu příkazového řádku zobrazuje příkaz INSERT, který vytvoří tento řádek.</span><span class="sxs-lookup"><span data-stu-id="58561-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="58561-252">Spusťte aplikaci a ověřte, zda vše funguje.</span><span class="sxs-lookup"><span data-stu-id="58561-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="58561-253">Použití migrace ve výrobě</span><span class="sxs-lookup"><span data-stu-id="58561-253">Applying migrations in production</span></span>

<span data-ttu-id="58561-254">Doporučujeme produkční aplikace **by nemělvolat** [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="58561-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="58561-255">`Migrate`by nemělbýt volána z aplikace v serverové farmě.</span><span class="sxs-lookup"><span data-stu-id="58561-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="58561-256">Například pokud aplikace byla cloud nasazený s horizontálnínavýšení kapacity (více instancí aplikace běží).</span><span class="sxs-lookup"><span data-stu-id="58561-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="58561-257">Migrace databáze by měla být provedena jako součást nasazení a řízeným způsobem.</span><span class="sxs-lookup"><span data-stu-id="58561-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="58561-258">Přístupy migrace produkční databáze zahrnují:</span><span class="sxs-lookup"><span data-stu-id="58561-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="58561-259">Použití migrace k vytvoření skriptů SQL a použití skriptů SQL v nasazení.</span><span class="sxs-lookup"><span data-stu-id="58561-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="58561-260">Běží `dotnet ef database update` z řízeného prostředí.</span><span class="sxs-lookup"><span data-stu-id="58561-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="58561-261">EF Core `__MigrationsHistory` používá tabulku, aby zjistil, jestli je třeba spustit všechny migrace.</span><span class="sxs-lookup"><span data-stu-id="58561-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="58561-262">Pokud db je aktuální, je spuštěna žádná migrace.</span><span class="sxs-lookup"><span data-stu-id="58561-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="58561-263">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="58561-263">Troubleshooting</span></span>

<span data-ttu-id="58561-264">Stáhněte si [dokončenou aplikaci](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="58561-264">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="58561-265">Aplikace generuje následující výjimku:</span><span class="sxs-lookup"><span data-stu-id="58561-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="58561-266">Řešení: Spustit`dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="58561-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="58561-267">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="58561-267">Additional resources</span></span>

* [<span data-ttu-id="58561-268">Verze tohoto kurzu pro YouTube</span><span class="sxs-lookup"><span data-stu-id="58561-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="58561-269">[Rozhraní CLI jádra .NET](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="58561-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="58561-270">Konzola Správce balíčků (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="58561-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="58561-271">[Předchozí](xref:data/ef-rp/sort-filter-page)
> [další](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="58561-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end


---
title: Stránky Razor s EF Core v ASP.NET Core – migrace - 4 z 8
author: rick-anderson
description: V tomto kurzu začnete používat funkci migrace EF Core ke správě změn datových modelů v aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 6/31/2017
uid: data/ef-rp/migrations
ms.openlocfilehash: 1803c6d3956121e4e7091f4f951917425e87c335
ms.sourcegitcommit: 7d6019f762fc5b8cbedcd69801e8310f51a17c18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419469"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="338f5-103">Stránky Razor s EF Core v ASP.NET Core – migrace - 4 z 8</span><span class="sxs-lookup"><span data-stu-id="338f5-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="338f5-104">Podle [Petr Dykstra](https://github.com/tdykstra), [Jan Macek P](https://twitter.com/thereformedprog), a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="338f5-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="338f5-105">V tomto kurzu se používá funkce migrace EF Core ke správě změn datových modelů.</span><span class="sxs-lookup"><span data-stu-id="338f5-105">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="338f5-106">Pokud narazíte na potíže nelze vyřešit, stáhněte si [dokončené aplikace](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="338f5-106">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="338f5-107">Když se nová aplikace vyvíjí, model data často změny.</span><span class="sxs-lookup"><span data-stu-id="338f5-107">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="338f5-108">Pokaždé, když změny modelu model získá synchronizován s databází.</span><span class="sxs-lookup"><span data-stu-id="338f5-108">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="338f5-109">Tento kurz se tím, že konfigurace technologie Entity Framework pro vytvoření databáze, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="338f5-109">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="338f5-110">Pokaždé, když datový model změny:</span><span class="sxs-lookup"><span data-stu-id="338f5-110">Each time the data model changes:</span></span>

* <span data-ttu-id="338f5-111">Databáze je vyřazeno.</span><span class="sxs-lookup"><span data-stu-id="338f5-111">The DB is dropped.</span></span>
* <span data-ttu-id="338f5-112">EF vytvoří nový, který odpovídá modelu.</span><span class="sxs-lookup"><span data-stu-id="338f5-112">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="338f5-113">Nasazení aplikace nasazuje databáze se testovací data.</span><span class="sxs-lookup"><span data-stu-id="338f5-113">The app seeds the DB with test data.</span></span>

<span data-ttu-id="338f5-114">Tento přístup k udržování databáze synchronizace s datovým modelem funguje dobře, dokud můžete aplikaci nasadit do produkčního prostředí.</span><span class="sxs-lookup"><span data-stu-id="338f5-114">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="338f5-115">Když je aplikace spuštěna v produkčním prostředí, to je obvykle ukládání dat, která musí být zachovány.</span><span class="sxs-lookup"><span data-stu-id="338f5-115">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="338f5-116">Aplikaci nelze spustit s testem DB pokaždé, když dojde ke změně (například přidá sloupec).</span><span class="sxs-lookup"><span data-stu-id="338f5-116">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="338f5-117">Funkce migrace EF Core tento problém řeší tím, že EF Core aktualizovat schéma databáze místo vytvoření nové databáze.</span><span class="sxs-lookup"><span data-stu-id="338f5-117">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="338f5-118">Místo odstranění a opětovné vytvoření databáze při modelování dat změny, migrace aktualizuje schéma a uchovává existující data.</span><span class="sxs-lookup"><span data-stu-id="338f5-118">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="338f5-119">Odpojení databáze</span><span class="sxs-lookup"><span data-stu-id="338f5-119">Drop the database</span></span>

<span data-ttu-id="338f5-120">Použití **Průzkumník objektů systému SQL Server** (SSOX) nebo `database drop` příkaz:</span><span class="sxs-lookup"><span data-stu-id="338f5-120">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="338f5-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="338f5-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="338f5-122">V **Konzola správce balíčků** (PMC), spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="338f5-122">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
```

<span data-ttu-id="338f5-123">Spustit `Get-Help about_EntityFrameworkCore` z konzole PMC zobrazíte nápovědu.</span><span class="sxs-lookup"><span data-stu-id="338f5-123">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="338f5-124">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="338f5-124">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="338f5-125">Otevřete okno příkazového řádku a přejděte do složky projektu.</span><span class="sxs-lookup"><span data-stu-id="338f5-125">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="338f5-126">Obsahuje složky projektu *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="338f5-126">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="338f5-127">V příkazovém okně zadejte následující:</span><span class="sxs-lookup"><span data-stu-id="338f5-127">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="338f5-128">Vytvoření počáteční migraci a aktualizaci databáze</span><span class="sxs-lookup"><span data-stu-id="338f5-128">Create an initial migration and update the DB</span></span>

<span data-ttu-id="338f5-129">Sestavte projekt a vytvořit první migraci.</span><span class="sxs-lookup"><span data-stu-id="338f5-129">Build the project and create the first migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="338f5-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="338f5-130">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="338f5-131">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="338f5-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="338f5-132">Prozkoumání nahoru a dolů metody</span><span class="sxs-lookup"><span data-stu-id="338f5-132">Examine the Up and Down methods</span></span>

<span data-ttu-id="338f5-133">EF Core `migrations add` příkaz vygeneruje kód pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="338f5-133">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="338f5-134">Tento kód migrace probíhá *migrace\<časové razítko > _InitialCreate.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="338f5-134">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="338f5-135">`Up` Metodu `InitialCreate` třídy vytvoří tabulky databáze, které odpovídají sady entit datového modelu.</span><span class="sxs-lookup"><span data-stu-id="338f5-135">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="338f5-136">`Down` Metoda odstraní, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="338f5-136">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="338f5-137">Migrace volání `Up` metody k implementaci změn datových modelů pro migraci.</span><span class="sxs-lookup"><span data-stu-id="338f5-137">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="338f5-138">Po zadání příkazu vrácení zpět aktualizace, volání migrace `Down` metody.</span><span class="sxs-lookup"><span data-stu-id="338f5-138">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="338f5-139">Předchozí kód je pro počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="338f5-139">The preceding code is for the initial migration.</span></span> <span data-ttu-id="338f5-140">Tento kód byl vytvořen při `migrations add InitialCreate` jste příkaz spustili.</span><span class="sxs-lookup"><span data-stu-id="338f5-140">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="338f5-141">Název parametru migrace ("InitialCreate" v příkladu) se používá pro název souboru.</span><span class="sxs-lookup"><span data-stu-id="338f5-141">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="338f5-142">Název migrace může být libovolný platný název souboru.</span><span class="sxs-lookup"><span data-stu-id="338f5-142">The migration name can be any valid file name.</span></span> <span data-ttu-id="338f5-143">Doporučujeme vybrat slovo nebo slovní spojení, které shrnuje, co se provádí v migraci.</span><span class="sxs-lookup"><span data-stu-id="338f5-143">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="338f5-144">Migraci, která byla přidána tabulka oddělení může například názvem "AddDepartmentTable."</span><span class="sxs-lookup"><span data-stu-id="338f5-144">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="338f5-145">Pokud počáteční migraci je vytvořen a existuje databáze:</span><span class="sxs-lookup"><span data-stu-id="338f5-145">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="338f5-146">Generování kódu vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="338f5-146">The DB creation code is generated.</span></span>
* <span data-ttu-id="338f5-147">Kód pro vytvoření databáze není nutné spustit, protože databáze již odpovídá datového modelu.</span><span class="sxs-lookup"><span data-stu-id="338f5-147">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="338f5-148">Pokud je spuštěn kód pro vytvoření databáze, nebude provést změny, protože databáze již shoduje s datovým modelem.</span><span class="sxs-lookup"><span data-stu-id="338f5-148">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="338f5-149">Když je aplikace nasazena do nového prostředí, musí být spuštěn kód pro vytvoření databáze k vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="338f5-149">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="338f5-150">Dříve databáze byla vyřazena a neexistuje, proto migrace vytvoří nová databáze.</span><span class="sxs-lookup"><span data-stu-id="338f5-150">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="338f5-151">Snímek dat modelu</span><span class="sxs-lookup"><span data-stu-id="338f5-151">The data model snapshot</span></span>

<span data-ttu-id="338f5-152">Vytvoření migrace *snímku* aktuální schéma databáze v *Migrations/SchoolContextModelSnapshot.cs*.</span><span class="sxs-lookup"><span data-stu-id="338f5-152">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="338f5-153">Při přidání migrace EF Určuje, co se změnilo porovnáním datový model, který soubor snímku.</span><span class="sxs-lookup"><span data-stu-id="338f5-153">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="338f5-154">Pokud chcete odstranit migrace, použijte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="338f5-154">To delete a migration, use the following command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="338f5-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="338f5-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="338f5-156">Remove migrace</span><span class="sxs-lookup"><span data-stu-id="338f5-156">Remove-Migration</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="338f5-157">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="338f5-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations remove
```

<span data-ttu-id="338f5-158">Další informace najdete v tématu [migrace ef dotnet odebrat](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="338f5-158">For more information, see  [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="338f5-159">Migrace příkaz remove odstraní migraci a zajistí, že je správně obnovit snímek.</span><span class="sxs-lookup"><span data-stu-id="338f5-159">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="338f5-160">Odebrat EnsureCreated a testování aplikace</span><span class="sxs-lookup"><span data-stu-id="338f5-160">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="338f5-161">Pro vývoj v rané fázi `EnsureCreated` byl použit.</span><span class="sxs-lookup"><span data-stu-id="338f5-161">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="338f5-162">V tomto kurzu se používají migrace.</span><span class="sxs-lookup"><span data-stu-id="338f5-162">In this tutorial, migrations are used.</span></span> <span data-ttu-id="338f5-163">`EnsureCreated` má následující omezení:</span><span class="sxs-lookup"><span data-stu-id="338f5-163">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="338f5-164">Obchází migrace a vytvoří databáze a schéma.</span><span class="sxs-lookup"><span data-stu-id="338f5-164">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="338f5-165">Nelze vytvořit tabulku migrace.</span><span class="sxs-lookup"><span data-stu-id="338f5-165">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="338f5-166">Můžete *není* použít s migrací.</span><span class="sxs-lookup"><span data-stu-id="338f5-166">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="338f5-167">Je určená pro testování nebo rychlé vytváření prototypů ve kterém je databáze vyřadit a znovu vytvořit často.</span><span class="sxs-lookup"><span data-stu-id="338f5-167">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="338f5-168">Odebrat `EnsureCreated`:</span><span class="sxs-lookup"><span data-stu-id="338f5-168">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="338f5-169">Spusťte aplikaci a ověřte, že databáze je nasazený.</span><span class="sxs-lookup"><span data-stu-id="338f5-169">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="338f5-170">Zkontrolovat databázi</span><span class="sxs-lookup"><span data-stu-id="338f5-170">Inspect the database</span></span>

<span data-ttu-id="338f5-171">Použití **Průzkumník objektů systému SQL Server** ke kontrole databáze.</span><span class="sxs-lookup"><span data-stu-id="338f5-171">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="338f5-172">Všimněte si, že přidání `__EFMigrationsHistory` tabulky.</span><span class="sxs-lookup"><span data-stu-id="338f5-172">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="338f5-173">`__EFMigrationsHistory` Tabulka uchovává informace o migraci, které se použily k databázi.</span><span class="sxs-lookup"><span data-stu-id="338f5-173">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="338f5-174">Zobrazení dat v `__EFMigrationsHistory` tabulky, zobrazuje jeden řádek pro první migraci.</span><span class="sxs-lookup"><span data-stu-id="338f5-174">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="338f5-175">Poslední protokol v předchozím příkladu výstupu rozhraní příkazového řádku obsahuje, který vytváří tento řádek příkazu INSERT.</span><span class="sxs-lookup"><span data-stu-id="338f5-175">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="338f5-176">Spusťte aplikaci a ověřte, že vše funguje.</span><span class="sxs-lookup"><span data-stu-id="338f5-176">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="338f5-177">Použití migrace v produkčním prostředí</span><span class="sxs-lookup"><span data-stu-id="338f5-177">Applying migrations in production</span></span>

<span data-ttu-id="338f5-178">Doporučujeme, abyste měli produkčních aplikací **není** volání [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="338f5-178">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="338f5-179">`Migrate` neměla být volána z aplikace v serverové farmě.</span><span class="sxs-lookup"><span data-stu-id="338f5-179">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="338f5-180">Například, pokud tato aplikace je mimo cloud nasadili s horizontálním navýšením (běží více instancí aplikace).</span><span class="sxs-lookup"><span data-stu-id="338f5-180">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="338f5-181">Migrace databáze má počítat jako součást nasazení a řízené způsobem.</span><span class="sxs-lookup"><span data-stu-id="338f5-181">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="338f5-182">Přístupy k migraci produkční databáze patří:</span><span class="sxs-lookup"><span data-stu-id="338f5-182">Production database migration approaches include:</span></span>

* <span data-ttu-id="338f5-183">Pomocí migrace vytvořit skripty SQL a pomocí skriptů SQL v nasazení.</span><span class="sxs-lookup"><span data-stu-id="338f5-183">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="338f5-184">Spuštění `dotnet ef database update` v řízeném prostředí.</span><span class="sxs-lookup"><span data-stu-id="338f5-184">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="338f5-185">EF Core používá `__MigrationsHistory` tabulky zobrazíte, pokud žádné migrace, který je potřeba spustit.</span><span class="sxs-lookup"><span data-stu-id="338f5-185">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="338f5-186">Pokud je aktuální databáze, migrace není spuštěn.</span><span class="sxs-lookup"><span data-stu-id="338f5-186">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="338f5-187">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="338f5-187">Troubleshooting</span></span>

<span data-ttu-id="338f5-188">Stáhněte si [dokončené aplikace](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span><span class="sxs-lookup"><span data-stu-id="338f5-188">Download the [completed app](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span></span>

<span data-ttu-id="338f5-189">Aplikace generuje následující výjimku:</span><span class="sxs-lookup"><span data-stu-id="338f5-189">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="338f5-190">Řešení: Spustit `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="338f5-190">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="338f5-191">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="338f5-191">Additional resources</span></span>

* [<span data-ttu-id="338f5-192">Verzi tohoto kurzu na webu YouTube</span><span class="sxs-lookup"><span data-stu-id="338f5-192">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="338f5-193">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="338f5-193">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="338f5-194">Konzola Správce balíčků (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="338f5-194">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

::: moniker-end

> [!div class="step-by-step"]
> <span data-ttu-id="338f5-195">[Předchozí](xref:data/ef-rp/sort-filter-page)
> [další](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="338f5-195">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

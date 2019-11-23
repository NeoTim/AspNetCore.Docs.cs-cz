---
title: Přidat nové pole na stránku Razor v ASP.NET Core
author: rick-anderson
description: Ukazuje, jak přidat nové pole na stránku Razor pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: b31711eb6f797de2de1559a3303e14b32a88f1ff
ms.sourcegitcommit: b3ebf96560b75b752d0e71161d788da800ad0999
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72822383"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="93815-103">Přidat nové pole na stránku Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="93815-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="93815-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="93815-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="93815-105">V této části se [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migrace Code First používá pro:</span><span class="sxs-lookup"><span data-stu-id="93815-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="93815-106">Přidejte do modelu nové pole.</span><span class="sxs-lookup"><span data-stu-id="93815-106">Add a new field to the model.</span></span>
* <span data-ttu-id="93815-107">Migrujte novou změnu schématu pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="93815-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="93815-108">Při použití Code First EF k automatickému vytvoření databáze Code First:</span><span class="sxs-lookup"><span data-stu-id="93815-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="93815-109">Přidá do databáze `__EFMigrationsHistory`ovou tabulku, která bude sledovat, zda je schéma databáze synchronizováno s třídami modelů, ze kterých byla vygenerována.</span><span class="sxs-lookup"><span data-stu-id="93815-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="93815-110">Pokud třídy modelů nejsou synchronizovány s databází, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="93815-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="93815-111">Automatické ověření schématu nebo modelu v synchronizaci usnadňuje vyhledání nekonzistentních problémů s databází či kódem.</span><span class="sxs-lookup"><span data-stu-id="93815-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="93815-112">Přidání vlastnosti hodnocení do modelu videa</span><span class="sxs-lookup"><span data-stu-id="93815-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="93815-113">Otevřete soubor *Models/Movie. cs* a přidejte vlastnost `Rating`:</span><span class="sxs-lookup"><span data-stu-id="93815-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="93815-114">Sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="93815-114">Build the app.</span></span>

<span data-ttu-id="93815-115">Upravte *stránky/filmy/index. cshtml*a přidejte `Rating` pole:</span><span class="sxs-lookup"><span data-stu-id="93815-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="93815-116">Aktualizujte následující stránky:</span><span class="sxs-lookup"><span data-stu-id="93815-116">Update the following pages:</span></span>

* <span data-ttu-id="93815-117">Přidejte pole `Rating` na stránky odstranit a podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="93815-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="93815-118">Aktualizujte [vytvořit. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) pomocí pole `Rating`.</span><span class="sxs-lookup"><span data-stu-id="93815-118">Update [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="93815-119">Přidejte pole `Rating` na stránku pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="93815-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="93815-120">Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole.</span><span class="sxs-lookup"><span data-stu-id="93815-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="93815-121">Spuštění aplikace bez aktualizace databáze vyvolá `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="93815-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="93815-122">Výjimka `SqlException` je způsobena tím, že aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů v databázi.</span><span class="sxs-lookup"><span data-stu-id="93815-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="93815-123">(V tabulce databáze nejsou žádné `Rating` sloupce.)</span><span class="sxs-lookup"><span data-stu-id="93815-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="93815-124">K řešení této chyby je potřeba několik přístupů:</span><span class="sxs-lookup"><span data-stu-id="93815-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="93815-125">Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nového schématu třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="93815-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="93815-126">Tento přístup je v brzké fázi vývojový cyklus vhodný; umožňuje rychlou vývoj modelu a schématu databáze dohromady.</span><span class="sxs-lookup"><span data-stu-id="93815-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="93815-127">Nevýhodou je, že ztratíte stávající data v databázi.</span><span class="sxs-lookup"><span data-stu-id="93815-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="93815-128">Nepoužívejte tento přístup k provozní databázi.</span><span class="sxs-lookup"><span data-stu-id="93815-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="93815-129">Vyřazení databáze při změnách schématu a použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem, jak vyvíjet aplikace.</span><span class="sxs-lookup"><span data-stu-id="93815-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="93815-130">Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu.</span><span class="sxs-lookup"><span data-stu-id="93815-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="93815-131">Výhodou tohoto přístupu je, že zachováte data.</span><span class="sxs-lookup"><span data-stu-id="93815-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="93815-132">Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.</span><span class="sxs-lookup"><span data-stu-id="93815-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="93815-133">K aktualizaci schématu databáze použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="93815-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="93815-134">Pro tento kurz použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="93815-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="93815-135">Aktualizujte třídu `SeedData` tak, aby poskytovala hodnotu pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="93815-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="93815-136">Níže je uvedená vzorová změna, ale tuto změnu budete chtít udělat pro každý blok `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="93815-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="93815-137">Podívejte se na [dokončený soubor SeedData.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="93815-137">See the [completed SeedData.cs file](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="93815-138">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="93815-138">Build the solution.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="93815-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93815-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="93815-140">Přidání migrace pro pole hodnocení</span><span class="sxs-lookup"><span data-stu-id="93815-140">Add a migration for the rating field</span></span>

<span data-ttu-id="93815-141">V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="93815-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="93815-142">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="93815-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="93815-143">Příkaz `Add-Migration` oznamuje rozhraní:</span><span class="sxs-lookup"><span data-stu-id="93815-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="93815-144">Porovnejte model `Movie` se schématem `Movie` DB.</span><span class="sxs-lookup"><span data-stu-id="93815-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="93815-145">Vytvořte kód pro migraci schématu databáze do nového modelu.</span><span class="sxs-lookup"><span data-stu-id="93815-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="93815-146">Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="93815-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="93815-147">Je užitečné použít pro migrační soubor smysluplný název.</span><span class="sxs-lookup"><span data-stu-id="93815-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="93815-148">Příkaz `Update-Database` instruuje rozhraní, aby se změny schématu projevily v databázi a zachovaly stávající data.</span><span class="sxs-lookup"><span data-stu-id="93815-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="93815-149">Pokud odstraníte všechny záznamy v databázi, inicializátor tuto databázi dosadí a zahrne pole `Rating`.</span><span class="sxs-lookup"><span data-stu-id="93815-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="93815-150">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SQL Server Průzkumník objektů](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="93815-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="93815-151">Další možností je odstranit databázi a použít migrace k opětovnému vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="93815-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="93815-152">Odstranění databáze v SSOX:</span><span class="sxs-lookup"><span data-stu-id="93815-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="93815-153">Vyberte databázi v SSOX.</span><span class="sxs-lookup"><span data-stu-id="93815-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="93815-154">Klikněte pravým tlačítkem na databázi a vyberte *Odstranit*.</span><span class="sxs-lookup"><span data-stu-id="93815-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="93815-155">Podívejte se na **Zavřít existující připojení**.</span><span class="sxs-lookup"><span data-stu-id="93815-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="93815-156">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="93815-156">Select **OK**.</span></span>
* <span data-ttu-id="93815-157">V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="93815-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="93815-158">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="93815-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="93815-159">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="93815-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="93815-160">Odstraňte složku migrace.</span><span class="sxs-lookup"><span data-stu-id="93815-160">Delete the migration folder.</span></span>  <span data-ttu-id="93815-161">K opětovnému vytvoření databáze použijte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="93815-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="93815-162">Spusťte aplikaci a ověřte, že je možné vytvářet, upravovat a zobrazovat filmy pomocí pole `Rating`.</span><span class="sxs-lookup"><span data-stu-id="93815-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="93815-163">Pokud databáze není osazena, nastavte v metodě `SeedData.Initialize` bod přerušení.</span><span class="sxs-lookup"><span data-stu-id="93815-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93815-164">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="93815-164">Additional resources</span></span>

* [<span data-ttu-id="93815-165">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="93815-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="93815-166">[Předchozí: Přidání vyhledávání](xref:tutorials/razor-pages/search)
> [Další: Přidání ověřování](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="93815-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="93815-167">V této části se [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migrace Code First používá pro:</span><span class="sxs-lookup"><span data-stu-id="93815-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="93815-168">Přidejte do modelu nové pole.</span><span class="sxs-lookup"><span data-stu-id="93815-168">Add a new field to the model.</span></span>
* <span data-ttu-id="93815-169">Migrujte novou změnu schématu pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="93815-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="93815-170">Při použití Code First EF k automatickému vytvoření databáze Code First:</span><span class="sxs-lookup"><span data-stu-id="93815-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="93815-171">Přidá do databáze tabulku, která bude sledovat, zda je schéma databáze synchronizováno s třídami modelů, ze kterých byla vygenerována.</span><span class="sxs-lookup"><span data-stu-id="93815-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="93815-172">Pokud třídy modelů nejsou synchronizovány s databází, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="93815-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="93815-173">Automatické ověření schématu nebo modelu v synchronizaci usnadňuje vyhledání nekonzistentních problémů s databází či kódem.</span><span class="sxs-lookup"><span data-stu-id="93815-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="93815-174">Přidání vlastnosti hodnocení do modelu videa</span><span class="sxs-lookup"><span data-stu-id="93815-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="93815-175">Otevřete soubor *Models/Movie. cs* a přidejte vlastnost `Rating`:</span><span class="sxs-lookup"><span data-stu-id="93815-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="93815-176">Sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="93815-176">Build the app.</span></span>

<span data-ttu-id="93815-177">Upravte *stránky/filmy/index. cshtml*a přidejte `Rating` pole:</span><span class="sxs-lookup"><span data-stu-id="93815-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="93815-178">Aktualizujte následující stránky:</span><span class="sxs-lookup"><span data-stu-id="93815-178">Update the following pages:</span></span>

* <span data-ttu-id="93815-179">Přidejte pole `Rating` na stránky odstranit a podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="93815-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="93815-180">Aktualizujte [vytvořit. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) pomocí pole `Rating`.</span><span class="sxs-lookup"><span data-stu-id="93815-180">Update [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="93815-181">Přidejte pole `Rating` na stránku pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="93815-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="93815-182">Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole.</span><span class="sxs-lookup"><span data-stu-id="93815-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="93815-183">Pokud je teď aplikace spuštěná, vyvolá `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="93815-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="93815-184">Tato chyba je způsobena tím, že aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů databáze.</span><span class="sxs-lookup"><span data-stu-id="93815-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="93815-185">(V tabulce databáze nejsou žádné `Rating` sloupce.)</span><span class="sxs-lookup"><span data-stu-id="93815-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="93815-186">K řešení této chyby je potřeba několik přístupů:</span><span class="sxs-lookup"><span data-stu-id="93815-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="93815-187">Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nového schématu třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="93815-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="93815-188">Tento přístup je v brzké fázi vývojový cyklus vhodný; umožňuje rychlou vývoj modelu a schématu databáze dohromady.</span><span class="sxs-lookup"><span data-stu-id="93815-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="93815-189">Nevýhodou je, že ztratíte stávající data v databázi.</span><span class="sxs-lookup"><span data-stu-id="93815-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="93815-190">Nepoužívejte tento přístup k provozní databázi.</span><span class="sxs-lookup"><span data-stu-id="93815-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="93815-191">Vyřazení databáze při změnách schématu a použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem, jak vyvíjet aplikace.</span><span class="sxs-lookup"><span data-stu-id="93815-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="93815-192">Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu.</span><span class="sxs-lookup"><span data-stu-id="93815-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="93815-193">Výhodou tohoto přístupu je, že zachováte data.</span><span class="sxs-lookup"><span data-stu-id="93815-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="93815-194">Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.</span><span class="sxs-lookup"><span data-stu-id="93815-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="93815-195">K aktualizaci schématu databáze použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="93815-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="93815-196">Pro tento kurz použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="93815-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="93815-197">Aktualizujte třídu `SeedData` tak, aby poskytovala hodnotu pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="93815-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="93815-198">Níže je uvedená vzorová změna, ale tuto změnu budete chtít udělat pro každý blok `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="93815-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="93815-199">Podívejte se na [dokončený soubor SeedData.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="93815-199">See the [completed SeedData.cs file](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="93815-200">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="93815-200">Build the solution.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="93815-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93815-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="93815-202">Přidání migrace pro pole hodnocení</span><span class="sxs-lookup"><span data-stu-id="93815-202">Add a migration for the rating field</span></span>

<span data-ttu-id="93815-203">V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="93815-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="93815-204">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="93815-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="93815-205">Příkaz `Add-Migration` oznamuje rozhraní:</span><span class="sxs-lookup"><span data-stu-id="93815-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="93815-206">Porovnejte model `Movie` se schématem `Movie` DB.</span><span class="sxs-lookup"><span data-stu-id="93815-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="93815-207">Vytvořte kód pro migraci schématu databáze do nového modelu.</span><span class="sxs-lookup"><span data-stu-id="93815-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="93815-208">Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="93815-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="93815-209">Je užitečné použít pro migrační soubor smysluplný název.</span><span class="sxs-lookup"><span data-stu-id="93815-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="93815-210">Příkaz `Update-Database` instruuje rozhraní, aby se změny schématu projevily v databázi.</span><span class="sxs-lookup"><span data-stu-id="93815-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="93815-211">Pokud odstraníte všechny záznamy v databázi, inicializátor tuto databázi dosadí a zahrne pole `Rating`.</span><span class="sxs-lookup"><span data-stu-id="93815-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="93815-212">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SQL Server Průzkumník objektů](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="93815-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="93815-213">Další možností je odstranit databázi a použít migrace k opětovnému vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="93815-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="93815-214">Odstranění databáze v SSOX:</span><span class="sxs-lookup"><span data-stu-id="93815-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="93815-215">Vyberte databázi v SSOX.</span><span class="sxs-lookup"><span data-stu-id="93815-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="93815-216">Klikněte pravým tlačítkem na databázi a vyberte *Odstranit*.</span><span class="sxs-lookup"><span data-stu-id="93815-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="93815-217">Podívejte se na **Zavřít existující připojení**.</span><span class="sxs-lookup"><span data-stu-id="93815-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="93815-218">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="93815-218">Select **OK**.</span></span>
* <span data-ttu-id="93815-219">V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="93815-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="93815-220">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="93815-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="93815-221">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="93815-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="93815-222">Odstraňte databázi a pomocí migrace znovu vytvořte databázi.</span><span class="sxs-lookup"><span data-stu-id="93815-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="93815-223">Chcete-li odstranit databázi, odstraňte soubor databáze (*MvcMovie. DB*).</span><span class="sxs-lookup"><span data-stu-id="93815-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="93815-224">Pak spusťte příkaz `ef database update`:</span><span class="sxs-lookup"><span data-stu-id="93815-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="93815-225">Spusťte aplikaci a ověřte, že je možné vytvářet, upravovat a zobrazovat filmy pomocí pole `Rating`.</span><span class="sxs-lookup"><span data-stu-id="93815-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="93815-226">Pokud databáze není osazena, nastavte v metodě `SeedData.Initialize` bod přerušení.</span><span class="sxs-lookup"><span data-stu-id="93815-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93815-227">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="93815-227">Additional resources</span></span>

* [<span data-ttu-id="93815-228">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="93815-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="93815-229">[Předchozí: Přidání vyhledávání](xref:tutorials/razor-pages/search)
> [Další: Přidání ověřování](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="93815-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

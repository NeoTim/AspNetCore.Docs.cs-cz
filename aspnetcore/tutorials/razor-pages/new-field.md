---
title: Přidat nové pole na stránku Razor v ASP.NET Core
author: rick-anderson
description: Ukazuje, jak přidat nové pole na stránku Razor pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 0629605f4d5597a9694cb20ce00b91ff4a768468
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082472"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="d5bb3-103">Přidat nové pole na stránku Razor v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5bb3-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="d5bb3-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d5bb3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="d5bb3-105">V této části se [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migrace Code First používá pro:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="d5bb3-106">Přidejte do modelu nové pole.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-106">Add a new field to the model.</span></span>
* <span data-ttu-id="d5bb3-107">Migrujte novou změnu schématu pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="d5bb3-108">Při použití Code First EF k automatickému vytvoření databáze Code First:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="d5bb3-109">Přidá do databáze tabulku, která bude sledovat, zda je schéma databáze synchronizováno s třídami modelů, ze kterých byla vygenerována.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-109">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="d5bb3-110">Pokud třídy modelů nejsou synchronizovány s databází, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="d5bb3-111">Automatické ověření schématu nebo modelu v synchronizaci usnadňuje vyhledání nekonzistentních problémů s databází či kódem.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="d5bb3-112">Přidání vlastnosti hodnocení do modelu videa</span><span class="sxs-lookup"><span data-stu-id="d5bb3-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="d5bb3-113">Otevřete soubor *Models/video. cs* a přidejte `Rating` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="d5bb3-114">Sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-114">Build the app.</span></span>

<span data-ttu-id="d5bb3-115">Upravit *stránky/filmy/index. cshtml*a přidat `Rating` pole:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="d5bb3-116">Aktualizujte následující stránky:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-116">Update the following pages:</span></span>

* <span data-ttu-id="d5bb3-117">`Rating` Přidejte pole na stránky odstranit a podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="d5bb3-118">Aktualizace [Create. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) s `Rating` polem</span><span class="sxs-lookup"><span data-stu-id="d5bb3-118">Update [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="d5bb3-119">`Rating` Přidejte pole do stránky pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="d5bb3-120">Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="d5bb3-121">Pokud nyní spustí aplikace, vyvolá `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-121">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="d5bb3-122">Tato chyba je způsobena tím, že aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů databáze.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-122">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="d5bb3-123">(V tabulce databáze `Rating` není žádný sloupec.)</span><span class="sxs-lookup"><span data-stu-id="d5bb3-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="d5bb3-124">K řešení této chyby je potřeba několik přístupů:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="d5bb3-125">Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nového schématu třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="d5bb3-126">Tento přístup je v brzké fázi vývojový cyklus vhodný; umožňuje rychlou vývoj modelu a schématu databáze dohromady.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="d5bb3-127">Nevýhodou je, že ztratíte stávající data v databázi.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="d5bb3-128">Nepoužívejte tento přístup k provozní databázi.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="d5bb3-129">Vyřazení databáze při změnách schématu a použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem, jak vyvíjet aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="d5bb3-130">Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="d5bb3-131">Výhodou tohoto přístupu je, že zachováte data.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="d5bb3-132">Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="d5bb3-133">K aktualizaci schématu databáze použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="d5bb3-134">Pro tento kurz použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="d5bb3-135">Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="d5bb3-136">Níže je uvedená vzorová změna, ale tuto změnu budete chtít udělat pro každý `new Movie` blok.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="d5bb3-137">Podívejte se na [dokončený soubor SeedData.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="d5bb3-137">See the [completed SeedData.cs file](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="d5bb3-138">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-138">Build the solution.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d5bb3-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5bb3-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="d5bb3-140">Přidání migrace pro pole hodnocení</span><span class="sxs-lookup"><span data-stu-id="d5bb3-140">Add a migration for the rating field</span></span>

<span data-ttu-id="d5bb3-141">V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="d5bb3-142">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="d5bb3-143">`Add-Migration` Příkaz instruuje rozhraní:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="d5bb3-144">Porovnejte `Movie` model se schématem databáze. `Movie`</span><span class="sxs-lookup"><span data-stu-id="d5bb3-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="d5bb3-145">Vytvořte kód pro migraci schématu databáze do nového modelu.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="d5bb3-146">Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d5bb3-147">Je užitečné použít pro migrační soubor smysluplný název.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="d5bb3-148">`Update-Database` Příkaz instruuje rozhraní, aby se změny schématu projevily v databázi.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-148">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="d5bb3-149">Pokud odstraníte všechny záznamy v databázi, inicializátor tuto databázi dosadí a zahrne `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="d5bb3-150">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SQL Server Průzkumník objektů](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="d5bb3-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="d5bb3-151">Další možností je odstranit databázi a použít migrace k opětovnému vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d5bb3-152">Odstranění databáze v SSOX:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="d5bb3-153">Vyberte databázi v SSOX.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="d5bb3-154">Klikněte pravým tlačítkem na databázi a vyberte *Odstranit*.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="d5bb3-155">Podívejte se na **Zavřít existující připojení**.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="d5bb3-156">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-156">Select **OK**.</span></span>
* <span data-ttu-id="d5bb3-157">V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d5bb3-158">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d5bb3-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="d5bb3-159">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="d5bb3-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="d5bb3-160">Odstraňte složku migrace.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-160">Delete the migration folder.</span></span>  <span data-ttu-id="d5bb3-161">K opětovnému vytvoření databáze použijte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="d5bb3-162">Spusťte aplikaci a ověřte, že můžete vytvářet, upravovat a zobrazovat filmy pomocí `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="d5bb3-163">Pokud databáze není osazena, nastavte v `SeedData.Initialize` metodě bod přerušení.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5bb3-164">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d5bb3-164">Additional resources</span></span>

* [<span data-ttu-id="d5bb3-165">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="d5bb3-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="d5bb3-166">[Předchozí Další přidání](xref:tutorials/razor-pages/search)vyhledávání
> :[ Přidání ověřování](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="d5bb3-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="d5bb3-167">V této části se [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migrace Code First používá pro:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="d5bb3-168">Přidejte do modelu nové pole.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-168">Add a new field to the model.</span></span>
* <span data-ttu-id="d5bb3-169">Migrujte novou změnu schématu pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="d5bb3-170">Při použití Code First EF k automatickému vytvoření databáze Code First:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="d5bb3-171">Přidá do databáze tabulku, která bude sledovat, zda je schéma databáze synchronizováno s třídami modelů, ze kterých byla vygenerována.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="d5bb3-172">Pokud třídy modelů nejsou synchronizovány s databází, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="d5bb3-173">Automatické ověření schématu nebo modelu v synchronizaci usnadňuje vyhledání nekonzistentních problémů s databází či kódem.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="d5bb3-174">Přidání vlastnosti hodnocení do modelu videa</span><span class="sxs-lookup"><span data-stu-id="d5bb3-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="d5bb3-175">Otevřete soubor *Models/video. cs* a přidejte `Rating` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="d5bb3-176">Sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-176">Build the app.</span></span>

<span data-ttu-id="d5bb3-177">Upravit *stránky/filmy/index. cshtml*a přidat `Rating` pole:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="d5bb3-178">Aktualizujte následující stránky:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-178">Update the following pages:</span></span>

* <span data-ttu-id="d5bb3-179">`Rating` Přidejte pole na stránky odstranit a podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="d5bb3-180">Aktualizace [Create. cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) s `Rating` polem</span><span class="sxs-lookup"><span data-stu-id="d5bb3-180">Update [Create.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="d5bb3-181">`Rating` Přidejte pole do stránky pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="d5bb3-182">Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="d5bb3-183">Pokud nyní spustí aplikace, vyvolá `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="d5bb3-184">Tato chyba je způsobena tím, že aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů databáze.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="d5bb3-185">(V tabulce databáze `Rating` není žádný sloupec.)</span><span class="sxs-lookup"><span data-stu-id="d5bb3-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="d5bb3-186">K řešení této chyby je potřeba několik přístupů:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="d5bb3-187">Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nového schématu třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="d5bb3-188">Tento přístup je v brzké fázi vývojový cyklus vhodný; umožňuje rychlou vývoj modelu a schématu databáze dohromady.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="d5bb3-189">Nevýhodou je, že ztratíte stávající data v databázi.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="d5bb3-190">Nepoužívejte tento přístup k provozní databázi.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="d5bb3-191">Vyřazení databáze při změnách schématu a použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem, jak vyvíjet aplikace.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="d5bb3-192">Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="d5bb3-193">Výhodou tohoto přístupu je, že zachováte data.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="d5bb3-194">Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="d5bb3-195">K aktualizaci schématu databáze použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="d5bb3-196">Pro tento kurz použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="d5bb3-197">Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="d5bb3-198">Níže je uvedená vzorová změna, ale tuto změnu budete chtít udělat pro každý `new Movie` blok.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="d5bb3-199">Podívejte se na [dokončený soubor SeedData.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="d5bb3-199">See the [completed SeedData.cs file](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="d5bb3-200">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-200">Build the solution.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d5bb3-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5bb3-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="d5bb3-202">Přidání migrace pro pole hodnocení</span><span class="sxs-lookup"><span data-stu-id="d5bb3-202">Add a migration for the rating field</span></span>

<span data-ttu-id="d5bb3-203">V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="d5bb3-204">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="d5bb3-205">`Add-Migration` Příkaz instruuje rozhraní:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="d5bb3-206">Porovnejte `Movie` model se schématem databáze. `Movie`</span><span class="sxs-lookup"><span data-stu-id="d5bb3-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="d5bb3-207">Vytvořte kód pro migraci schématu databáze do nového modelu.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="d5bb3-208">Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d5bb3-209">Je užitečné použít pro migrační soubor smysluplný název.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="d5bb3-210">`Update-Database` Příkaz instruuje rozhraní, aby se změny schématu projevily v databázi.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="d5bb3-211">Pokud odstraníte všechny záznamy v databázi, inicializátor tuto databázi dosadí a zahrne `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="d5bb3-212">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SQL Server Průzkumník objektů](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="d5bb3-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="d5bb3-213">Další možností je odstranit databázi a použít migrace k opětovnému vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d5bb3-214">Odstranění databáze v SSOX:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="d5bb3-215">Vyberte databázi v SSOX.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="d5bb3-216">Klikněte pravým tlačítkem na databázi a vyberte *Odstranit*.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="d5bb3-217">Podívejte se na **Zavřít existující připojení**.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="d5bb3-218">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-218">Select **OK**.</span></span>
* <span data-ttu-id="d5bb3-219">V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d5bb3-220">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d5bb3-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="d5bb3-221">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="d5bb3-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="d5bb3-222">Odstraňte databázi a pomocí migrace znovu vytvořte databázi.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d5bb3-223">Chcete-li odstranit databázi, odstraňte soubor databáze (*MvcMovie. DB*).</span><span class="sxs-lookup"><span data-stu-id="d5bb3-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="d5bb3-224">Pak spusťte `ef database update` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d5bb3-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="d5bb3-225">Spusťte aplikaci a ověřte, že můžete vytvářet, upravovat a zobrazovat filmy pomocí `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="d5bb3-226">Pokud databáze není osazena, nastavte v `SeedData.Initialize` metodě bod přerušení.</span><span class="sxs-lookup"><span data-stu-id="d5bb3-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d5bb3-227">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d5bb3-227">Additional resources</span></span>

* [<span data-ttu-id="d5bb3-228">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="d5bb3-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="d5bb3-229">[Předchozí Další přidání](xref:tutorials/razor-pages/search)vyhledávání
> :[ Přidání ověřování](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="d5bb3-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

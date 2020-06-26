---
title: Část 7 – přidání nového pole na Razor stránku v ASP.NET Core
author: rick-anderson
description: Část 7 série kurzů na Razor stránkách.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 07350e127e2e529b5ad168c3e26c1ed33cd9b99f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407990"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="c88a0-103">Část 7 – přidání nového pole na Razor stránku v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c88a0-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="c88a0-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c88a0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="c88a0-105">V této části se [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migrace Code First používá pro:</span><span class="sxs-lookup"><span data-stu-id="c88a0-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="c88a0-106">Přidejte do modelu nové pole.</span><span class="sxs-lookup"><span data-stu-id="c88a0-106">Add a new field to the model.</span></span>
* <span data-ttu-id="c88a0-107">Migrujte novou změnu schématu pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="c88a0-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="c88a0-108">Při použití Code First EF k automatickému vytvoření databáze Code First:</span><span class="sxs-lookup"><span data-stu-id="c88a0-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="c88a0-109">Přidá `__EFMigrationsHistory` do databáze tabulku, která bude sledovat, zda je schéma databáze synchronizováno s třídami modelů, ze kterých byla vygenerována.</span><span class="sxs-lookup"><span data-stu-id="c88a0-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="c88a0-110">Pokud třídy modelů nejsou synchronizovány s databází, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="c88a0-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="c88a0-111">Automatické ověření schématu nebo modelu v synchronizaci usnadňuje vyhledání nekonzistentních problémů s databází či kódem.</span><span class="sxs-lookup"><span data-stu-id="c88a0-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="c88a0-112">Přidání vlastnosti hodnocení do modelu videa</span><span class="sxs-lookup"><span data-stu-id="c88a0-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="c88a0-113">Otevřete soubor *Models/video. cs* a přidejte `Rating` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="c88a0-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="c88a0-114">Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="c88a0-114">Build the app.</span></span>

<span data-ttu-id="c88a0-115">Upravit *stránky/filmy/index. cshtml*a přidat `Rating` pole:</span><span class="sxs-lookup"><span data-stu-id="c88a0-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="c88a0-116">Aktualizujte následující stránky:</span><span class="sxs-lookup"><span data-stu-id="c88a0-116">Update the following pages:</span></span>

* <span data-ttu-id="c88a0-117">Přidejte `Rating` pole na stránky odstranit a podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="c88a0-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="c88a0-118">Aktualizace [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) s `Rating` polem</span><span class="sxs-lookup"><span data-stu-id="c88a0-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="c88a0-119">Přidejte `Rating` pole do stránky pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="c88a0-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="c88a0-120">Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole.</span><span class="sxs-lookup"><span data-stu-id="c88a0-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="c88a0-121">Spuštění aplikace bez aktualizace databáze vyvolá `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="c88a0-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="c88a0-122">`SqlException`Výjimka je způsobena tím, že aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů v databázi.</span><span class="sxs-lookup"><span data-stu-id="c88a0-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="c88a0-123">( `Rating` V tabulce databáze není žádný sloupec.)</span><span class="sxs-lookup"><span data-stu-id="c88a0-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="c88a0-124">K řešení této chyby je potřeba několik přístupů:</span><span class="sxs-lookup"><span data-stu-id="c88a0-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="c88a0-125">Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nového schématu třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="c88a0-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="c88a0-126">Tento přístup je v brzké fázi vývojový cyklus vhodný; umožňuje rychlou vývoj modelu a schématu databáze dohromady.</span><span class="sxs-lookup"><span data-stu-id="c88a0-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="c88a0-127">Nevýhodou je, že ztratíte stávající data v databázi.</span><span class="sxs-lookup"><span data-stu-id="c88a0-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="c88a0-128">Nepoužívejte tento přístup k provozní databázi.</span><span class="sxs-lookup"><span data-stu-id="c88a0-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="c88a0-129">Vyřazení databáze při změnách schématu a použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem, jak vyvíjet aplikace.</span><span class="sxs-lookup"><span data-stu-id="c88a0-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="c88a0-130">Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu.</span><span class="sxs-lookup"><span data-stu-id="c88a0-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="c88a0-131">Výhodou tohoto přístupu je, že zachováte data.</span><span class="sxs-lookup"><span data-stu-id="c88a0-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="c88a0-132">Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.</span><span class="sxs-lookup"><span data-stu-id="c88a0-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="c88a0-133">K aktualizaci schématu databáze použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="c88a0-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="c88a0-134">Pro tento kurz použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="c88a0-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="c88a0-135">Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="c88a0-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="c88a0-136">Níže je uvedená vzorová změna, ale tuto změnu budete chtít udělat pro každý `new Movie` blok.</span><span class="sxs-lookup"><span data-stu-id="c88a0-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="c88a0-137">Podívejte se na [dokončený soubor SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="c88a0-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="c88a0-138">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="c88a0-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c88a0-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c88a0-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="c88a0-140">Přidání migrace pro pole hodnocení</span><span class="sxs-lookup"><span data-stu-id="c88a0-140">Add a migration for the rating field</span></span>

<span data-ttu-id="c88a0-141">V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="c88a0-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="c88a0-142">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c88a0-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="c88a0-143">`Add-Migration`Příkaz instruuje rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c88a0-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="c88a0-144">Porovnejte `Movie` model se `Movie` schématem databáze.</span><span class="sxs-lookup"><span data-stu-id="c88a0-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="c88a0-145">Vytvořte kód pro migraci schématu databáze do nového modelu.</span><span class="sxs-lookup"><span data-stu-id="c88a0-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="c88a0-146">Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="c88a0-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="c88a0-147">Je užitečné použít pro migrační soubor smysluplný název.</span><span class="sxs-lookup"><span data-stu-id="c88a0-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="c88a0-148">`Update-Database`Příkaz instruuje rozhraní, aby se změny schématu projevily v databázi a zachovaly stávající data.</span><span class="sxs-lookup"><span data-stu-id="c88a0-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="c88a0-149">Pokud odstraníte všechny záznamy v databázi, inicializátor tuto databázi dosadí a zahrne `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="c88a0-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="c88a0-150">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SQL Server Průzkumník objektů](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c88a0-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="c88a0-151">Další možností je odstranit databázi a použít migrace k opětovnému vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="c88a0-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="c88a0-152">Odstranění databáze v SSOX:</span><span class="sxs-lookup"><span data-stu-id="c88a0-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="c88a0-153">Vyberte databázi v SSOX.</span><span class="sxs-lookup"><span data-stu-id="c88a0-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="c88a0-154">Klikněte pravým tlačítkem na databázi a vyberte *Odstranit*.</span><span class="sxs-lookup"><span data-stu-id="c88a0-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="c88a0-155">Podívejte se na **Zavřít existující připojení**.</span><span class="sxs-lookup"><span data-stu-id="c88a0-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="c88a0-156">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="c88a0-156">Select **OK**.</span></span>
* <span data-ttu-id="c88a0-157">V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="c88a0-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c88a0-158">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c88a0-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="c88a0-159">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="c88a0-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="c88a0-160">Odstraňte složku migrace.</span><span class="sxs-lookup"><span data-stu-id="c88a0-160">Delete the migration folder.</span></span>  <span data-ttu-id="c88a0-161">K opětovnému vytvoření databáze použijte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="c88a0-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="c88a0-162">Spusťte aplikaci a ověřte, že můžete vytvářet, upravovat a zobrazovat filmy pomocí `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="c88a0-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="c88a0-163">Pokud databáze není osazena, nastavte v metodě bod přerušení `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="c88a0-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c88a0-164">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c88a0-164">Additional resources</span></span>

* [<span data-ttu-id="c88a0-165">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="c88a0-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="c88a0-166">[Předchozí: Přidání vyhledávání](xref:tutorials/razor-pages/search) 
>  [Další: Přidání ověřování](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="c88a0-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="c88a0-167">V této části se [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migrace Code First používá pro:</span><span class="sxs-lookup"><span data-stu-id="c88a0-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="c88a0-168">Přidejte do modelu nové pole.</span><span class="sxs-lookup"><span data-stu-id="c88a0-168">Add a new field to the model.</span></span>
* <span data-ttu-id="c88a0-169">Migrujte novou změnu schématu pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="c88a0-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="c88a0-170">Při použití Code First EF k automatickému vytvoření databáze Code First:</span><span class="sxs-lookup"><span data-stu-id="c88a0-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="c88a0-171">Přidá do databáze tabulku, která bude sledovat, zda je schéma databáze synchronizováno s třídami modelů, ze kterých byla vygenerována.</span><span class="sxs-lookup"><span data-stu-id="c88a0-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="c88a0-172">Pokud třídy modelů nejsou synchronizovány s databází, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="c88a0-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="c88a0-173">Automatické ověření schématu nebo modelu v synchronizaci usnadňuje vyhledání nekonzistentních problémů s databází či kódem.</span><span class="sxs-lookup"><span data-stu-id="c88a0-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="c88a0-174">Přidání vlastnosti hodnocení do modelu videa</span><span class="sxs-lookup"><span data-stu-id="c88a0-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="c88a0-175">Otevřete soubor *Models/video. cs* a přidejte `Rating` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="c88a0-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="c88a0-176">Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="c88a0-176">Build the app.</span></span>

<span data-ttu-id="c88a0-177">Upravit *stránky/filmy/index. cshtml*a přidat `Rating` pole:</span><span class="sxs-lookup"><span data-stu-id="c88a0-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="c88a0-178">Aktualizujte následující stránky:</span><span class="sxs-lookup"><span data-stu-id="c88a0-178">Update the following pages:</span></span>

* <span data-ttu-id="c88a0-179">Přidejte `Rating` pole na stránky odstranit a podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="c88a0-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="c88a0-180">Aktualizace [Create. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) s `Rating` polem</span><span class="sxs-lookup"><span data-stu-id="c88a0-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="c88a0-181">Přidejte `Rating` pole do stránky pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="c88a0-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="c88a0-182">Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole.</span><span class="sxs-lookup"><span data-stu-id="c88a0-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="c88a0-183">Pokud nyní spustí aplikace, vyvolá `SqlException` :</span><span class="sxs-lookup"><span data-stu-id="c88a0-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="c88a0-184">Tato chyba je způsobena tím, že aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů databáze.</span><span class="sxs-lookup"><span data-stu-id="c88a0-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="c88a0-185">( `Rating` V tabulce databáze není žádný sloupec.)</span><span class="sxs-lookup"><span data-stu-id="c88a0-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="c88a0-186">K řešení této chyby je potřeba několik přístupů:</span><span class="sxs-lookup"><span data-stu-id="c88a0-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="c88a0-187">Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nového schématu třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="c88a0-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="c88a0-188">Tento přístup je v brzké fázi vývojový cyklus vhodný; umožňuje rychlou vývoj modelu a schématu databáze dohromady.</span><span class="sxs-lookup"><span data-stu-id="c88a0-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="c88a0-189">Nevýhodou je, že ztratíte stávající data v databázi.</span><span class="sxs-lookup"><span data-stu-id="c88a0-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="c88a0-190">Nepoužívejte tento přístup k provozní databázi.</span><span class="sxs-lookup"><span data-stu-id="c88a0-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="c88a0-191">Vyřazení databáze při změnách schématu a použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem, jak vyvíjet aplikace.</span><span class="sxs-lookup"><span data-stu-id="c88a0-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="c88a0-192">Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu.</span><span class="sxs-lookup"><span data-stu-id="c88a0-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="c88a0-193">Výhodou tohoto přístupu je, že zachováte data.</span><span class="sxs-lookup"><span data-stu-id="c88a0-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="c88a0-194">Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.</span><span class="sxs-lookup"><span data-stu-id="c88a0-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="c88a0-195">K aktualizaci schématu databáze použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="c88a0-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="c88a0-196">Pro tento kurz použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="c88a0-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="c88a0-197">Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="c88a0-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="c88a0-198">Níže je uvedená vzorová změna, ale tuto změnu budete chtít udělat pro každý `new Movie` blok.</span><span class="sxs-lookup"><span data-stu-id="c88a0-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="c88a0-199">Podívejte se na [dokončený soubor SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="c88a0-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="c88a0-200">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="c88a0-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c88a0-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c88a0-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="c88a0-202">Přidání migrace pro pole hodnocení</span><span class="sxs-lookup"><span data-stu-id="c88a0-202">Add a migration for the rating field</span></span>

<span data-ttu-id="c88a0-203">V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="c88a0-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="c88a0-204">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="c88a0-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="c88a0-205">`Add-Migration`Příkaz instruuje rozhraní:</span><span class="sxs-lookup"><span data-stu-id="c88a0-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="c88a0-206">Porovnejte `Movie` model se `Movie` schématem databáze.</span><span class="sxs-lookup"><span data-stu-id="c88a0-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="c88a0-207">Vytvořte kód pro migraci schématu databáze do nového modelu.</span><span class="sxs-lookup"><span data-stu-id="c88a0-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="c88a0-208">Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="c88a0-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="c88a0-209">Je užitečné použít pro migrační soubor smysluplný název.</span><span class="sxs-lookup"><span data-stu-id="c88a0-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="c88a0-210">`Update-Database`Příkaz instruuje rozhraní, aby se změny schématu projevily v databázi.</span><span class="sxs-lookup"><span data-stu-id="c88a0-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="c88a0-211">Pokud odstraníte všechny záznamy v databázi, inicializátor tuto databázi dosadí a zahrne `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="c88a0-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="c88a0-212">Můžete to provést pomocí odkazů DELETE v prohlížeči nebo z [SQL Server Průzkumník objektů](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="c88a0-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="c88a0-213">Další možností je odstranit databázi a použít migrace k opětovnému vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="c88a0-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="c88a0-214">Odstranění databáze v SSOX:</span><span class="sxs-lookup"><span data-stu-id="c88a0-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="c88a0-215">Vyberte databázi v SSOX.</span><span class="sxs-lookup"><span data-stu-id="c88a0-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="c88a0-216">Klikněte pravým tlačítkem na databázi a vyberte *Odstranit*.</span><span class="sxs-lookup"><span data-stu-id="c88a0-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="c88a0-217">Podívejte se na **Zavřít existující připojení**.</span><span class="sxs-lookup"><span data-stu-id="c88a0-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="c88a0-218">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="c88a0-218">Select **OK**.</span></span>
* <span data-ttu-id="c88a0-219">V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="c88a0-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c88a0-220">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="c88a0-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="c88a0-221">Vyřazení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="c88a0-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="c88a0-222">Odstraňte databázi a pomocí migrace znovu vytvořte databázi.</span><span class="sxs-lookup"><span data-stu-id="c88a0-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="c88a0-223">Chcete-li odstranit databázi, odstraňte soubor databáze (*MvcMovie. DB*).</span><span class="sxs-lookup"><span data-stu-id="c88a0-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="c88a0-224">Pak spusťte `ef database update` příkaz:</span><span class="sxs-lookup"><span data-stu-id="c88a0-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="c88a0-225">Spusťte aplikaci a ověřte, že můžete vytvářet, upravovat a zobrazovat filmy pomocí `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="c88a0-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="c88a0-226">Pokud databáze není osazena, nastavte v metodě bod přerušení `SeedData.Initialize` .</span><span class="sxs-lookup"><span data-stu-id="c88a0-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c88a0-227">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c88a0-227">Additional resources</span></span>

* [<span data-ttu-id="c88a0-228">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="c88a0-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="c88a0-229">[Předchozí: Přidání vyhledávání](xref:tutorials/razor-pages/search) 
>  [Další: Přidání ověřování](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="c88a0-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

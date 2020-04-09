---
title: Přidání nového pole na stránku Razor v ASP.NET jádru
author: rick-anderson
description: Ukazuje, jak přidat nové pole na stránku Razor s jádrem entity frameworku
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: d34b938dbd1b512ddb167cac0c035837889cd38f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657813"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="f51a9-103">Přidání nového pole na stránku Razor v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="f51a9-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="f51a9-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f51a9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="f51a9-105">V této části [entity framework](/ef/core/get-started/aspnetcore/new-db) code first migrace se používá k:</span><span class="sxs-lookup"><span data-stu-id="f51a9-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="f51a9-106">Přidejte do modelu nové pole.</span><span class="sxs-lookup"><span data-stu-id="f51a9-106">Add a new field to the model.</span></span>
* <span data-ttu-id="f51a9-107">Migrujte změnu nového schématu pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="f51a9-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="f51a9-108">Při použití EF code First k automatickému vytvoření databáze, Code First:</span><span class="sxs-lookup"><span data-stu-id="f51a9-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="f51a9-109">Přidá `__EFMigrationsHistory` do databáze tabulku ke sledování, zda je schéma databáze synchronizováno s třídami modelu, ze kterých byla generována.</span><span class="sxs-lookup"><span data-stu-id="f51a9-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="f51a9-110">Pokud třídy modelu nejsou synchronizovány s DB, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="f51a9-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="f51a9-111">Automatické ověření schématu/modelu v synchronizaci usnadňuje hledání nekonzistentních problémů s databází a kódem.</span><span class="sxs-lookup"><span data-stu-id="f51a9-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="f51a9-112">Přidání vlastnosti hodnocení do filmového modelu</span><span class="sxs-lookup"><span data-stu-id="f51a9-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="f51a9-113">Otevřete soubor *Models/Movie.cs* `Rating` a přidejte vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f51a9-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="f51a9-114">Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="f51a9-114">Build the app.</span></span>

<span data-ttu-id="f51a9-115">Upravte *stránky/filmy/index.cshtml*a `Rating` přidejte pole:</span><span class="sxs-lookup"><span data-stu-id="f51a9-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="f51a9-116">Aktualizujte následující stránky:</span><span class="sxs-lookup"><span data-stu-id="f51a9-116">Update the following pages:</span></span>

* <span data-ttu-id="f51a9-117">Přidejte `Rating` pole na stránky Odstranit a Podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="f51a9-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="f51a9-118">Aktualizujte [create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) s polem. `Rating`</span><span class="sxs-lookup"><span data-stu-id="f51a9-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="f51a9-119">Přidejte `Rating` pole na stránku Úpravy.</span><span class="sxs-lookup"><span data-stu-id="f51a9-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="f51a9-120">Aplikace nebude fungovat, dokud db je aktualizován tak, aby zahrnovala nové pole.</span><span class="sxs-lookup"><span data-stu-id="f51a9-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="f51a9-121">Spuštění aplikace bez aktualizace databáze `SqlException`vyvolá :</span><span class="sxs-lookup"><span data-stu-id="f51a9-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="f51a9-122">Výjimka `SqlException` je způsobena tím, že aktualizovaná třída modelu Movie se liší od schématu tabulky Movie databáze.</span><span class="sxs-lookup"><span data-stu-id="f51a9-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="f51a9-123">(V databázové `Rating` tabulce není žádný sloupec.)</span><span class="sxs-lookup"><span data-stu-id="f51a9-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="f51a9-124">Existuje několik přístupů k řešení chyby:</span><span class="sxs-lookup"><span data-stu-id="f51a9-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="f51a9-125">Mají entity framework automaticky přetažení a znovu vytvořit databázi pomocí nového schématu třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="f51a9-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="f51a9-126">Tento přístup je vhodný na počátku vývojového cyklu; umožňuje rychle vyvíjet schéma modelu a databáze společně.</span><span class="sxs-lookup"><span data-stu-id="f51a9-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="f51a9-127">Nevýhodou je, že ztratíte existující data v databázi.</span><span class="sxs-lookup"><span data-stu-id="f51a9-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="f51a9-128">Nepoužívejte tento přístup v produkční databázi!</span><span class="sxs-lookup"><span data-stu-id="f51a9-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="f51a9-129">Uvolnění DB na změny schématu a pomocí inicializátoru automaticky osiva databáze s testovací data je často produktivní způsob, jak vyvinout aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f51a9-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="f51a9-130">Explicitně upravte schéma existující databáze tak, aby odpovídala třídám modelu.</span><span class="sxs-lookup"><span data-stu-id="f51a9-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="f51a9-131">Výhodou tohoto přístupu je, že uchováváte data.</span><span class="sxs-lookup"><span data-stu-id="f51a9-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="f51a9-132">Tuto změnu můžete provést ručně nebo vytvořením skriptu pro změnu databáze.</span><span class="sxs-lookup"><span data-stu-id="f51a9-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="f51a9-133">K aktualizaci schématu databáze použijte migrace prvního kódu.</span><span class="sxs-lookup"><span data-stu-id="f51a9-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="f51a9-134">V tomto kurzu použijte migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="f51a9-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="f51a9-135">Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="f51a9-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="f51a9-136">Ukázková změna je uvedena níže, ale tuto změnu `new Movie` budete chtít provést pro každý blok.</span><span class="sxs-lookup"><span data-stu-id="f51a9-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="f51a9-137">Podívejte se na [vyplněný soubor SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="f51a9-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="f51a9-138">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="f51a9-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f51a9-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f51a9-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="f51a9-140">Přidání migrace do pole hodnocení</span><span class="sxs-lookup"><span data-stu-id="f51a9-140">Add a migration for the rating field</span></span>

<span data-ttu-id="f51a9-141">V nabídce **Nástroje** vyberte **položku NuGet Package Manager > Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="f51a9-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="f51a9-142">Do pmc zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="f51a9-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="f51a9-143">Příkaz `Add-Migration` říká rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f51a9-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="f51a9-144">Porovnejte `Movie` `Movie` model se schématem DB.</span><span class="sxs-lookup"><span data-stu-id="f51a9-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="f51a9-145">Vytvořte kód pro migraci schématu DB do nového modelu.</span><span class="sxs-lookup"><span data-stu-id="f51a9-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="f51a9-146">Název "Hodnocení" je libovolný a používá se k pojmenování migračního souboru.</span><span class="sxs-lookup"><span data-stu-id="f51a9-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="f51a9-147">Je užitečné použít smysluplný název pro soubor migrace.</span><span class="sxs-lookup"><span data-stu-id="f51a9-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="f51a9-148">Příkaz `Update-Database` říká rozhraní použít změny schématu v databázi a zachovat existující data.</span><span class="sxs-lookup"><span data-stu-id="f51a9-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="f51a9-149">Pokud odstraníte všechny záznamy v DB, inicializátor zaznamená DB a zahrne `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="f51a9-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="f51a9-150">Můžete to provést pomocí odstranění odkazů v prohlížeči nebo z [Průzkumníka objektů sql serveru](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="f51a9-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="f51a9-151">Další možností je odstranění databáze a použití migrace znovu vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="f51a9-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="f51a9-152">Odstranění databáze ve SSOX:</span><span class="sxs-lookup"><span data-stu-id="f51a9-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="f51a9-153">Vyberte databázi ve SSOX.</span><span class="sxs-lookup"><span data-stu-id="f51a9-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="f51a9-154">Klikněte pravým tlačítkem myši na databázi a vyberte *příkaz Odstranit*.</span><span class="sxs-lookup"><span data-stu-id="f51a9-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="f51a9-155">Zaškrtněte **políčko Zavřít existující připojení**.</span><span class="sxs-lookup"><span data-stu-id="f51a9-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="f51a9-156">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="f51a9-156">Select **OK**.</span></span>
* <span data-ttu-id="f51a9-157">V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="f51a9-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f51a9-158">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f51a9-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="f51a9-159">Přetažení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="f51a9-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="f51a9-160">Odstraňte složku migrace.</span><span class="sxs-lookup"><span data-stu-id="f51a9-160">Delete the migration folder.</span></span>  <span data-ttu-id="f51a9-161">K opětovnému vytvoření databáze použijte následující příkazy.</span><span class="sxs-lookup"><span data-stu-id="f51a9-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="f51a9-162">Spusťte aplikaci a ověřte, zda `Rating` můžete vytvářet/ upravovat/zobrazovat filmy pomocí pole.</span><span class="sxs-lookup"><span data-stu-id="f51a9-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="f51a9-163">Pokud databáze není nasazena, nastavte bod přerušení `SeedData.Initialize` v metodě.</span><span class="sxs-lookup"><span data-stu-id="f51a9-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f51a9-164">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f51a9-164">Additional resources</span></span>

* [<span data-ttu-id="f51a9-165">Verze tohoto kurzu pro YouTube</span><span class="sxs-lookup"><span data-stu-id="f51a9-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="f51a9-166">[Předchozí: Přidání hledání](xref:tutorials/razor-pages/search)
> [další: Přidání ověření](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="f51a9-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="f51a9-167">V této části [entity framework](/ef/core/get-started/aspnetcore/new-db) code first migrace se používá k:</span><span class="sxs-lookup"><span data-stu-id="f51a9-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="f51a9-168">Přidejte do modelu nové pole.</span><span class="sxs-lookup"><span data-stu-id="f51a9-168">Add a new field to the model.</span></span>
* <span data-ttu-id="f51a9-169">Migrujte změnu nového schématu pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="f51a9-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="f51a9-170">Při použití EF code First k automatickému vytvoření databáze, Code First:</span><span class="sxs-lookup"><span data-stu-id="f51a9-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="f51a9-171">Přidá do databáze tabulku ke sledování, zda je schéma databáze synchronizováno s třídami modelu, ze kterých byla generována.</span><span class="sxs-lookup"><span data-stu-id="f51a9-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="f51a9-172">Pokud třídy modelu nejsou synchronizovány s DB, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="f51a9-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="f51a9-173">Automatické ověření schématu/modelu v synchronizaci usnadňuje hledání nekonzistentních problémů s databází a kódem.</span><span class="sxs-lookup"><span data-stu-id="f51a9-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="f51a9-174">Přidání vlastnosti hodnocení do filmového modelu</span><span class="sxs-lookup"><span data-stu-id="f51a9-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="f51a9-175">Otevřete soubor *Models/Movie.cs* `Rating` a přidejte vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f51a9-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="f51a9-176">Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="f51a9-176">Build the app.</span></span>

<span data-ttu-id="f51a9-177">Upravte *stránky/filmy/index.cshtml*a `Rating` přidejte pole:</span><span class="sxs-lookup"><span data-stu-id="f51a9-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="f51a9-178">Aktualizujte následující stránky:</span><span class="sxs-lookup"><span data-stu-id="f51a9-178">Update the following pages:</span></span>

* <span data-ttu-id="f51a9-179">Přidejte `Rating` pole na stránky Odstranit a Podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="f51a9-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="f51a9-180">Aktualizujte [create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) s polem. `Rating`</span><span class="sxs-lookup"><span data-stu-id="f51a9-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="f51a9-181">Přidejte `Rating` pole na stránku Úpravy.</span><span class="sxs-lookup"><span data-stu-id="f51a9-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="f51a9-182">Aplikace nebude fungovat, dokud db je aktualizován tak, aby zahrnovala nové pole.</span><span class="sxs-lookup"><span data-stu-id="f51a9-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="f51a9-183">Pokud běží nyní, aplikace `SqlException`vyvolá :</span><span class="sxs-lookup"><span data-stu-id="f51a9-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="f51a9-184">Tato chyba je způsobena tím, že aktualizovaná třída modelu Movie se liší od schématu tabulky Movie databáze.</span><span class="sxs-lookup"><span data-stu-id="f51a9-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="f51a9-185">(V databázové `Rating` tabulce není žádný sloupec.)</span><span class="sxs-lookup"><span data-stu-id="f51a9-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="f51a9-186">Existuje několik přístupů k řešení chyby:</span><span class="sxs-lookup"><span data-stu-id="f51a9-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="f51a9-187">Mají entity framework automaticky přetažení a znovu vytvořit databázi pomocí nového schématu třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="f51a9-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="f51a9-188">Tento přístup je vhodný na počátku vývojového cyklu; umožňuje rychle vyvíjet schéma modelu a databáze společně.</span><span class="sxs-lookup"><span data-stu-id="f51a9-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="f51a9-189">Nevýhodou je, že ztratíte existující data v databázi.</span><span class="sxs-lookup"><span data-stu-id="f51a9-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="f51a9-190">Nepoužívejte tento přístup v produkční databázi!</span><span class="sxs-lookup"><span data-stu-id="f51a9-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="f51a9-191">Uvolnění DB na změny schématu a pomocí inicializátoru automaticky osiva databáze s testovací data je často produktivní způsob, jak vyvinout aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f51a9-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="f51a9-192">Explicitně upravte schéma existující databáze tak, aby odpovídala třídám modelu.</span><span class="sxs-lookup"><span data-stu-id="f51a9-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="f51a9-193">Výhodou tohoto přístupu je, že uchováváte data.</span><span class="sxs-lookup"><span data-stu-id="f51a9-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="f51a9-194">Tuto změnu můžete provést ručně nebo vytvořením skriptu pro změnu databáze.</span><span class="sxs-lookup"><span data-stu-id="f51a9-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="f51a9-195">K aktualizaci schématu databáze použijte migrace prvního kódu.</span><span class="sxs-lookup"><span data-stu-id="f51a9-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="f51a9-196">V tomto kurzu použijte migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="f51a9-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="f51a9-197">Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="f51a9-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="f51a9-198">Ukázková změna je uvedena níže, ale tuto změnu `new Movie` budete chtít provést pro každý blok.</span><span class="sxs-lookup"><span data-stu-id="f51a9-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="f51a9-199">Podívejte se na [vyplněný soubor SeedData.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="f51a9-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="f51a9-200">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="f51a9-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f51a9-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f51a9-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="f51a9-202">Přidání migrace do pole hodnocení</span><span class="sxs-lookup"><span data-stu-id="f51a9-202">Add a migration for the rating field</span></span>

<span data-ttu-id="f51a9-203">V nabídce **Nástroje** vyberte **položku NuGet Package Manager > Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="f51a9-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="f51a9-204">Do pmc zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="f51a9-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="f51a9-205">Příkaz `Add-Migration` říká rozhraní:</span><span class="sxs-lookup"><span data-stu-id="f51a9-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="f51a9-206">Porovnejte `Movie` `Movie` model se schématem DB.</span><span class="sxs-lookup"><span data-stu-id="f51a9-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="f51a9-207">Vytvořte kód pro migraci schématu DB do nového modelu.</span><span class="sxs-lookup"><span data-stu-id="f51a9-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="f51a9-208">Název "Hodnocení" je libovolný a používá se k pojmenování migračního souboru.</span><span class="sxs-lookup"><span data-stu-id="f51a9-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="f51a9-209">Je užitečné použít smysluplný název pro soubor migrace.</span><span class="sxs-lookup"><span data-stu-id="f51a9-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="f51a9-210">Příkaz `Update-Database` říká rozhraní pro použití změny schématu v databázi.</span><span class="sxs-lookup"><span data-stu-id="f51a9-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="f51a9-211">Pokud odstraníte všechny záznamy v DB, inicializátor zaznamená DB a zahrne `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="f51a9-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="f51a9-212">Můžete to provést pomocí odstranění odkazů v prohlížeči nebo z [Průzkumníka objektů sql serveru](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="f51a9-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="f51a9-213">Další možností je odstranění databáze a použití migrace znovu vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="f51a9-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="f51a9-214">Odstranění databáze ve SSOX:</span><span class="sxs-lookup"><span data-stu-id="f51a9-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="f51a9-215">Vyberte databázi ve SSOX.</span><span class="sxs-lookup"><span data-stu-id="f51a9-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="f51a9-216">Klikněte pravým tlačítkem myši na databázi a vyberte *příkaz Odstranit*.</span><span class="sxs-lookup"><span data-stu-id="f51a9-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="f51a9-217">Zaškrtněte **políčko Zavřít existující připojení**.</span><span class="sxs-lookup"><span data-stu-id="f51a9-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="f51a9-218">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="f51a9-218">Select **OK**.</span></span>
* <span data-ttu-id="f51a9-219">V [PMC](xref:tutorials/razor-pages/new-field#pmc)aktualizujte databázi:</span><span class="sxs-lookup"><span data-stu-id="f51a9-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="f51a9-220">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="f51a9-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="f51a9-221">Přetažení a opětovné vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="f51a9-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="f51a9-222">Odstraňte databázi a použijte migrace znovu vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="f51a9-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="f51a9-223">Chcete-li databázi odstranit, odstraňte databázový soubor (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="f51a9-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="f51a9-224">Pak spusťte `ef database update` příkaz:</span><span class="sxs-lookup"><span data-stu-id="f51a9-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="f51a9-225">Spusťte aplikaci a ověřte, zda `Rating` můžete vytvářet/ upravovat/zobrazovat filmy pomocí pole.</span><span class="sxs-lookup"><span data-stu-id="f51a9-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="f51a9-226">Pokud databáze není nasazena, nastavte bod přerušení `SeedData.Initialize` v metodě.</span><span class="sxs-lookup"><span data-stu-id="f51a9-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f51a9-227">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f51a9-227">Additional resources</span></span>

* [<span data-ttu-id="f51a9-228">Verze tohoto kurzu pro YouTube</span><span class="sxs-lookup"><span data-stu-id="f51a9-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="f51a9-229">[Předchozí: Přidání hledání](xref:tutorials/razor-pages/search)
> [další: Přidání ověření](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="f51a9-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

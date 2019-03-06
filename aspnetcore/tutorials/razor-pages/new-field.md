---
title: Přidat nové pole do stránky v ASP.NET Core Razor
author: rick-anderson
description: Ukazuje, jak přidat nové pole do stránky Razor pomocí Entity Framework Core
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: f8661a48ddd6fc616c141435edc603117b4925fb
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345885"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="a1362-103">Přidat nové pole do stránky v ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="a1362-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="a1362-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a1362-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="a1362-105">V této části [Entity Framework](/ef/core/get-started/aspnetcore/new-db) se používá k migrace Code First:</span><span class="sxs-lookup"><span data-stu-id="a1362-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="a1362-106">Přidání nového pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="a1362-106">Add a new field to the model.</span></span>
* <span data-ttu-id="a1362-107">Migrace novou změnu schématu pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="a1362-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="a1362-108">Při použití automaticky vytvořit databázi, Code First EF Code First:</span><span class="sxs-lookup"><span data-stu-id="a1362-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="a1362-109">Přidá do databáze, kterou chcete sledovat, jestli je schéma databáze synchronizované s tříd modelu, které byly vygenerovány z tabulky.</span><span class="sxs-lookup"><span data-stu-id="a1362-109">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="a1362-110">Pokud nejsou synchronizované s databáze třídy modelu, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="a1362-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="a1362-111">Automatické ověření schématu a model synchronizované usnadňuje vyhledání potíží nekonzistentní databáze nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="a1362-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="a1362-112">Přidání vlastnosti do hodnocení filmů modelu</span><span class="sxs-lookup"><span data-stu-id="a1362-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="a1362-113">Otevřít *Models/Movie.cs* a přidejte `Rating` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="a1362-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="a1362-114">Sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="a1362-114">Build the app.</span></span>

<span data-ttu-id="a1362-115">Upravit *Pages/Movies/Index.cshtml*a přidejte `Rating` pole:</span><span class="sxs-lookup"><span data-stu-id="a1362-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml.?highlight=40-42,61-63)]

<span data-ttu-id="a1362-116">Aktualizace na následujících stránkách:</span><span class="sxs-lookup"><span data-stu-id="a1362-116">Update the following pages:</span></span>

* <span data-ttu-id="a1362-117">Přidat `Rating` pole na stránkách Delete a podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="a1362-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="a1362-118">Aktualizace [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) s `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a1362-118">Update [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="a1362-119">Přidat `Rating` pole na stránce Upravit.</span><span class="sxs-lookup"><span data-stu-id="a1362-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="a1362-120">Aplikace nebude fungovat, dokud databáze je aktualizováno, aby zahrnovalo nové pole.</span><span class="sxs-lookup"><span data-stu-id="a1362-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="a1362-121">Je-li spustit, vyvolá aplikaci `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="a1362-121">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="a1362-122">Tato chyba je způsobena se liší od schématu tabulky Movie databáze třídy modelu aktualizované video.</span><span class="sxs-lookup"><span data-stu-id="a1362-122">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="a1362-123">(Neexistuje žádný `Rating` sloupec v tabulce databáze.)</span><span class="sxs-lookup"><span data-stu-id="a1362-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="a1362-124">Řešení chyby několika způsoby:</span><span class="sxs-lookup"><span data-stu-id="a1362-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="a1362-125">Máte rozhraní Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nové schéma třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="a1362-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="a1362-126">Tento přístup je vhodný v rané fázi vývojového cyklu; umožňuje rychlý rozvoj schématu modelu a databáze společně.</span><span class="sxs-lookup"><span data-stu-id="a1362-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="a1362-127">Nevýhodou je, dojít ke ztrátě existujících dat v databázi.</span><span class="sxs-lookup"><span data-stu-id="a1362-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="a1362-128">Nepoužívejte tento postup u provozní databáze.</span><span class="sxs-lookup"><span data-stu-id="a1362-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="a1362-129">Vyřazení databáze na změny schématu a pomocí inicializátoru automaticky naplnit databázi daty testu je často produktivní způsob, jak vyvíjet aplikace.</span><span class="sxs-lookup"><span data-stu-id="a1362-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="a1362-130">Explicitně upravte schéma stávající databázi tak, aby odpovídalo tříd modelu.</span><span class="sxs-lookup"><span data-stu-id="a1362-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="a1362-131">Výhodou tohoto přístupu je, že zachováte vaše data.</span><span class="sxs-lookup"><span data-stu-id="a1362-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="a1362-132">Můžete tuto změnu provést buď ručně, nebo tak, že vytvoříte databázi změnit skript.</span><span class="sxs-lookup"><span data-stu-id="a1362-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="a1362-133">Pomocí migrace Code First aktualizovat schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="a1362-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="a1362-134">V tomto kurzu pomocí migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="a1362-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="a1362-135">Aktualizace `SeedData` třídy tak, že poskytuje hodnoty pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="a1362-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="a1362-136">Ukázka změnu je uveden níže, ale budete chtít tuto změnu pro každou `new Movie` bloku.</span><span class="sxs-lookup"><span data-stu-id="a1362-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="a1362-137">Zobrazit [dokončit soubor SeedData.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="a1362-137">See the [completed SeedData.cs file](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="a1362-138">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="a1362-138">Build the solution.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a1362-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1362-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="a1362-140">Přidejte migraci pro pole hodnocení</span><span class="sxs-lookup"><span data-stu-id="a1362-140">Add a migration for the rating field</span></span>

<span data-ttu-id="a1362-141">Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet > Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="a1362-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="a1362-142">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a1362-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="a1362-143">`Add-Migration` Příkaz říká rozhraní framework:</span><span class="sxs-lookup"><span data-stu-id="a1362-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="a1362-144">Porovnání `Movie` modelů s `Movie` schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="a1362-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="a1362-145">Vytvoření kódu pro migraci schématu databáze na nový model.</span><span class="sxs-lookup"><span data-stu-id="a1362-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="a1362-146">Název "Hodnocení" je volitelný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="a1362-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="a1362-147">Je vhodné použít smysluplný název souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="a1362-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="a1362-148">`Update-Database` Příkaz říká rozhraní framework k použití změn schématu do databáze.</span><span class="sxs-lookup"><span data-stu-id="a1362-148">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="a1362-149">Při odstranění všech záznamů v databázi, bude inicializátoru naplnit databáze a zahrnout `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a1362-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="a1362-150">To lze provést pomocí odstranit odkazy v prohlížeči nebo z [Průzkumník objektů systému Sql Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="a1362-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="a1362-151">Další možností je odstranit databázi a znovu vytvořit databázi pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="a1362-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="a1362-152">Pokud chcete odstranit databázi v SSOX:</span><span class="sxs-lookup"><span data-stu-id="a1362-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="a1362-153">Vyberte databázi v SSOX.</span><span class="sxs-lookup"><span data-stu-id="a1362-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="a1362-154">Klikněte pravým tlačítkem na databázi a vyberte *odstranit*.</span><span class="sxs-lookup"><span data-stu-id="a1362-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="a1362-155">Zkontrolujte **ukončete stávající připojení**.</span><span class="sxs-lookup"><span data-stu-id="a1362-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="a1362-156">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="a1362-156">Select **OK**.</span></span>
* <span data-ttu-id="a1362-157">V [PMC](xref:tutorials/razor-pages/new-field#pmc), aktualizovat databázi:</span><span class="sxs-lookup"><span data-stu-id="a1362-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="a1362-158">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a1362-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<!-- copy/paste this tab to the next. Not worth an include  -->

<span data-ttu-id="a1362-159">Spuštěním následujících příkazů rozhraní příkazového řádku .NET Core:</span><span class="sxs-lookup"><span data-stu-id="a1362-159">Run the following .NET Core CLI commands:</span></span>

```console
dotnet ef migrations add Rating
dotnet ef database update
```

<span data-ttu-id="a1362-160">`ef migrations add` Příkaz říká rozhraní framework:</span><span class="sxs-lookup"><span data-stu-id="a1362-160">The `ef migrations add` command tells the framework to:</span></span>

* <span data-ttu-id="a1362-161">Porovnání `Movie` modelů s `Movie` schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="a1362-161">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="a1362-162">Vytvoření kódu pro migraci schématu databáze na nový model.</span><span class="sxs-lookup"><span data-stu-id="a1362-162">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="a1362-163">Název "Hodnocení" je volitelný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="a1362-163">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="a1362-164">Je vhodné použít smysluplný název souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="a1362-164">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="a1362-165">`ef database update` Příkaz říká rozhraní framework k použití změn schématu do databáze.</span><span class="sxs-lookup"><span data-stu-id="a1362-165">The `ef database update` command tells the framework to apply the schema changes to the database.</span></span>

<span data-ttu-id="a1362-166">Při odstranění všech záznamů v databázi, bude inicializátoru naplnit databáze a zahrnout `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a1362-166">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="a1362-167">Můžete to provést s odstranit odkazy v prohlížeči nebo pomocí nástroje SQLite.</span><span class="sxs-lookup"><span data-stu-id="a1362-167">You can do this with the delete links in the browser or by using a SQLite tool.</span></span>

<span data-ttu-id="a1362-168">Další možností je odstranit databázi a znovu vytvořit databázi pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="a1362-168">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="a1362-169">Pokud chcete odstranit databázi, odstraňte soubor databáze (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="a1362-169">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="a1362-170">Spusťte `ef database update` příkaz:</span><span class="sxs-lookup"><span data-stu-id="a1362-170">Then run the `ef database update` command:</span></span> 

```console
dotnet ef database update
```

> [!NOTE]
> <span data-ttu-id="a1362-171">EF Core SQLite zprostředkovatel nepodporuje mnoho operací změny schématu.</span><span class="sxs-lookup"><span data-stu-id="a1362-171">Many schema change operations are not supported by the EF Core SQLite provider.</span></span> <span data-ttu-id="a1362-172">Například přidáním sloupce se podporuje, ale odebráním sloupce se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="a1362-172">For example, adding a column is supported, but removing a column is not supported.</span></span> <span data-ttu-id="a1362-173">Pokud chcete přidat migrace odebrat sloupce, `ef migrations add` úspěšný příkaz ale `ef database update` příkaz selže.</span><span class="sxs-lookup"><span data-stu-id="a1362-173">If you add a migration to remove a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="a1362-174">Ručně napsáním kódu migrace provést znovu vytvořit tabulku, můžete alternativně vyřešit některá omezení.</span><span class="sxs-lookup"><span data-stu-id="a1362-174">You can work around some of the limitations by manually writing migrations code to perform a table rebuild.</span></span> <span data-ttu-id="a1362-175">Tabulka opětovné sestavení zahrnuje přejmenování existující tabulky, vytvářet nové tabulky, kopírování dat do nové tabulky a vyřazení staré tabulky.</span><span class="sxs-lookup"><span data-stu-id="a1362-175">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="a1362-176">Další informace naleznete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="a1362-176">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="a1362-177">Omezení poskytovatele pro databázi SQLite EF Core</span><span class="sxs-lookup"><span data-stu-id="a1362-177">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="a1362-178">Přizpůsobení migrace kódu</span><span class="sxs-lookup"><span data-stu-id="a1362-178">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="a1362-179">Předvyplnění dat</span><span class="sxs-lookup"><span data-stu-id="a1362-179">Data seeding</span></span>](/ef/core/modeling/data-seeding)

---  
<!-- End of VS tabs -->

<span data-ttu-id="a1362-180">Spusťte aplikaci a ověřit, je možné vytvořit/upravit/zobrazit videa s `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a1362-180">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="a1362-181">Pokud databáze není nasazený, nastavte zarážky `SeedData.Initialize` metody.</span><span class="sxs-lookup"><span data-stu-id="a1362-181">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a1362-182">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a1362-182">Additional resources</span></span>

* [<span data-ttu-id="a1362-183">Verzi tohoto kurzu na webu YouTube</span><span class="sxs-lookup"><span data-stu-id="a1362-183">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="a1362-184">[Předchozí: Přidání vyhledávací funkce](xref:tutorials/razor-pages/search)
> [Další: Přidání ověřování](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="a1362-184">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

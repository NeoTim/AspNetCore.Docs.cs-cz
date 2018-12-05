---
title: Přidat nové pole do stránky v ASP.NET Core Razor
author: rick-anderson
description: Ukazuje, jak přidat nové pole do stránky Razor pomocí Entity Framework Core
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: e280bc9553113982a1f1a77eabab32575c905237
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52862288"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="a974d-103">Přidat nové pole do stránky v ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="a974d-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="a974d-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a974d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="a974d-105">V této části [Entity Framework](/ef/core/get-started/aspnetcore/new-db) se používá k migrace Code First:</span><span class="sxs-lookup"><span data-stu-id="a974d-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="a974d-106">Přidání nového pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="a974d-106">Add a new field to the model.</span></span>
* <span data-ttu-id="a974d-107">Migrace novou změnu schématu pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="a974d-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="a974d-108">Při použití automaticky vytvořit databázi, Code First EF Code First:</span><span class="sxs-lookup"><span data-stu-id="a974d-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="a974d-109">Přidá do databáze, kterou chcete sledovat, jestli je schéma databáze synchronizované s tříd modelu, které byly vygenerovány z tabulky.</span><span class="sxs-lookup"><span data-stu-id="a974d-109">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="a974d-110">Pokud nejsou synchronizované s databáze třídy modelu, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="a974d-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="a974d-111">Automatické ověření schématu a model synchronizované usnadňuje vyhledání potíží nekonzistentní databáze nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="a974d-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="a974d-112">Přidání vlastnosti do hodnocení filmů modelu</span><span class="sxs-lookup"><span data-stu-id="a974d-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="a974d-113">Otevřít *Models/Movie.cs* a přidejte `Rating` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="a974d-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="a974d-114">Sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="a974d-114">Build the app.</span></span>

<span data-ttu-id="a974d-115">Upravit *Pages/Movies/Index.cshtml*a přidejte `Rating` pole:</span><span class="sxs-lookup"><span data-stu-id="a974d-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml.?highlight=40-42,61-63)]

<span data-ttu-id="a974d-116">Aktualizace na následujících stránkách:</span><span class="sxs-lookup"><span data-stu-id="a974d-116">Update the following pages:</span></span>

* <span data-ttu-id="a974d-117">Přidat `Rating` pole na stránkách Delete a podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="a974d-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="a974d-118">Aktualizace [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) s `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a974d-118">Update [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="a974d-119">Přidat `Rating` pole na stránce Upravit.</span><span class="sxs-lookup"><span data-stu-id="a974d-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="a974d-120">Aplikace nebude fungovat, dokud databáze je aktualizováno, aby zahrnovalo nové pole.</span><span class="sxs-lookup"><span data-stu-id="a974d-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="a974d-121">Je-li spustit, vyvolá aplikaci `SqlException`:</span><span class="sxs-lookup"><span data-stu-id="a974d-121">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="a974d-122">Tato chyba je způsobena se liší od schématu tabulky Movie databáze třídy modelu aktualizované video.</span><span class="sxs-lookup"><span data-stu-id="a974d-122">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="a974d-123">(Neexistuje žádný `Rating` sloupec v tabulce databáze.)</span><span class="sxs-lookup"><span data-stu-id="a974d-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="a974d-124">Řešení chyby několika způsoby:</span><span class="sxs-lookup"><span data-stu-id="a974d-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="a974d-125">Máte rozhraní Entity Framework automaticky vyřadit a znovu vytvořit databázi pomocí nové schéma třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="a974d-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="a974d-126">Tento přístup je vhodný v rané fázi vývojového cyklu; umožňuje rychlý rozvoj schématu modelu a databáze společně.</span><span class="sxs-lookup"><span data-stu-id="a974d-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="a974d-127">Nevýhodou je, dojít ke ztrátě existujících dat v databázi.</span><span class="sxs-lookup"><span data-stu-id="a974d-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="a974d-128">Nepoužívejte tento postup u provozní databáze.</span><span class="sxs-lookup"><span data-stu-id="a974d-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="a974d-129">Vyřazení databáze na změny schématu a pomocí inicializátoru automaticky naplnit databázi daty testu je často produktivní způsob, jak vyvíjet aplikace.</span><span class="sxs-lookup"><span data-stu-id="a974d-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="a974d-130">Explicitně upravte schéma stávající databázi tak, aby odpovídalo tříd modelu.</span><span class="sxs-lookup"><span data-stu-id="a974d-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="a974d-131">Výhodou tohoto přístupu je, že zachováte vaše data.</span><span class="sxs-lookup"><span data-stu-id="a974d-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="a974d-132">Můžete tuto změnu provést buď ručně, nebo tak, že vytvoříte databázi změnit skript.</span><span class="sxs-lookup"><span data-stu-id="a974d-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="a974d-133">Pomocí migrace Code First aktualizovat schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="a974d-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="a974d-134">V tomto kurzu pomocí migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="a974d-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="a974d-135">Aktualizace `SeedData` třídy tak, že poskytuje hodnoty pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="a974d-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="a974d-136">Ukázka změnu je uveden níže, ale budete chtít tuto změnu pro každou `new Movie` bloku.</span><span class="sxs-lookup"><span data-stu-id="a974d-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="a974d-137">Zobrazit [dokončit soubor SeedData.cs](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="a974d-137">See the [completed SeedData.cs file](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="a974d-138">Sestavte řešení.</span><span class="sxs-lookup"><span data-stu-id="a974d-138">Build the solution.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a974d-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a974d-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="a974d-140">Přidejte migraci pro pole hodnocení</span><span class="sxs-lookup"><span data-stu-id="a974d-140">Add a migration for the rating field</span></span>

<span data-ttu-id="a974d-141">Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet > Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="a974d-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="a974d-142">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a974d-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="a974d-143">`Add-Migration` Příkaz říká rozhraní framework:</span><span class="sxs-lookup"><span data-stu-id="a974d-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="a974d-144">Porovnání `Movie` modelů s `Movie` schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="a974d-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="a974d-145">Vytvoření kódu pro migraci schématu databáze na nový model.</span><span class="sxs-lookup"><span data-stu-id="a974d-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="a974d-146">Název "Hodnocení" je volitelný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="a974d-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="a974d-147">Je vhodné použít smysluplný název souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="a974d-147">It's helpful to use a meaningful name for the migration file.</span></span>

<a name="ssox"></a>

<span data-ttu-id="a974d-148">Při odstranění všech záznamů v databázi, bude inicializátoru naplnit databáze a zahrnout `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a974d-148">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="a974d-149">To lze provést pomocí odstranit odkazy v prohlížeči nebo z [Průzkumník objektů systému Sql Server](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span><span class="sxs-lookup"><span data-stu-id="a974d-149">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span> <span data-ttu-id="a974d-150">Pokud chcete odstranit databázi z SSOX:</span><span class="sxs-lookup"><span data-stu-id="a974d-150">To delete the database from SSOX:</span></span>

* <span data-ttu-id="a974d-151">Vyberte databázi v SSOX.</span><span class="sxs-lookup"><span data-stu-id="a974d-151">Select the database in SSOX.</span></span>
* <span data-ttu-id="a974d-152">Klikněte pravým tlačítkem na databázi a vyberte *odstranit*.</span><span class="sxs-lookup"><span data-stu-id="a974d-152">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="a974d-153">Zkontrolujte **ukončete stávající připojení**.</span><span class="sxs-lookup"><span data-stu-id="a974d-153">Check **Close existing connections**.</span></span>
* <span data-ttu-id="a974d-154">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="a974d-154">Select **OK**.</span></span>
* <span data-ttu-id="a974d-155">V [PMC](xref:tutorials/razor-pages/new-field#pmc), aktualizovat databázi:</span><span class="sxs-lookup"><span data-stu-id="a974d-155">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a974d-156">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a974d-156">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a974d-157"><!-- copy/paste this tab to the next. Not worth an include  --> SQLite nepodporuje migrace.</span><span class="sxs-lookup"><span data-stu-id="a974d-157"><!-- copy/paste this tab to the next. Not worth an include  --> SQLite doesn't support migrations.</span></span>

* <span data-ttu-id="a974d-158">Odstranění databáze nebo změňte název databáze *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="a974d-158">Delete the database or change the database name in the *appsettings.json* file.</span></span>
* <span data-ttu-id="a974d-159">Odstranit *migrace* složku (a všechny soubory ve složce).</span><span class="sxs-lookup"><span data-stu-id="a974d-159">Delete the *Migrations* folder (and all the files in the folder).</span></span>

<span data-ttu-id="a974d-160">Spuštěním následujících příkazů rozhraní příkazového řádku .NET Core:</span><span class="sxs-lookup"><span data-stu-id="a974d-160">Run the following .NET Core CLI commands:</span></span>

```console
dotnet ef migrations add Rating
dotnet ef database update
```

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a974d-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a974d-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a974d-162">SQLite nepodporuje migrace.</span><span class="sxs-lookup"><span data-stu-id="a974d-162">SQLite doesn't support migrations.</span></span>

* <span data-ttu-id="a974d-163">Odstranění databáze nebo změňte název databáze *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="a974d-163">Delete the database or change the database name in the *appsettings.json* file.</span></span>
* <span data-ttu-id="a974d-164">Odstranit *migrace* složku (a všechny soubory ve složce).</span><span class="sxs-lookup"><span data-stu-id="a974d-164">Delete the *Migrations* folder (and all the files in the folder).</span></span>

<span data-ttu-id="a974d-165">Spuštěním následujících příkazů rozhraní příkazového řádku .NET Core:</span><span class="sxs-lookup"><span data-stu-id="a974d-165">Run the following .NET Core CLI commands:</span></span>

```console
dotnet ef migrations add Rating
dotnet ef database update
```

---  
<!-- End of VS tabs -->

<span data-ttu-id="a974d-166">Spusťte aplikaci a ověřit, je možné vytvořit/upravit/zobrazit videa s `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a974d-166">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="a974d-167">Pokud databáze není nasazený, nastavte zarážky `SeedData.Initialize` metody.</span><span class="sxs-lookup"><span data-stu-id="a974d-167">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a974d-168">[Předchozí: Přidání vyhledávací funkce](xref:tutorials/razor-pages/search)
> [Další: Přidání ověření](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="a974d-168">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

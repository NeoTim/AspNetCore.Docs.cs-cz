---
title: Část 8 – Přidání nového pole do aplikace ASP.NET Core MVC
author: rick-anderson
description: Část 8 řad kurzů na ASP.NET Core MVC
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: a0c53755bd56b6c169437ca9f0ea915e46ad79ec
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606742"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="4d630-103">Část 8 – Přidání nového pole do aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="4d630-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="4d630-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4d630-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4d630-105">V této části se [Entity Framework](/ef/core/get-started/aspnetcore/new-db) migrace Code First používá pro:</span><span class="sxs-lookup"><span data-stu-id="4d630-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="4d630-106">Přidejte do modelu nové pole.</span><span class="sxs-lookup"><span data-stu-id="4d630-106">Add a new field to the model.</span></span>
* <span data-ttu-id="4d630-107">Migrujte nové pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="4d630-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="4d630-108">Když se Code First EF používá k automatickému vytvoření databáze, Code First:</span><span class="sxs-lookup"><span data-stu-id="4d630-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="4d630-109">Přidá tabulku do databáze pro sledování schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="4d630-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="4d630-110">Ověřuje, zda je databáze synchronizována s třídami modelů, ze kterých byla vygenerována.</span><span class="sxs-lookup"><span data-stu-id="4d630-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="4d630-111">Pokud nejsou synchronizovány, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="4d630-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="4d630-112">Díky tomu je snazší najít nekonzistentní problémy s databází či kódem.</span><span class="sxs-lookup"><span data-stu-id="4d630-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="4d630-113">Přidat vlastnost hodnocení do modelu videa</span><span class="sxs-lookup"><span data-stu-id="4d630-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="4d630-114">Přidat `Rating` vlastnost do *modelů/filmu. cs*:</span><span class="sxs-lookup"><span data-stu-id="4d630-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="4d630-115">Vytvoření aplikace</span><span class="sxs-lookup"><span data-stu-id="4d630-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="4d630-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d630-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="4d630-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="4d630-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="4d630-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d630-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4d630-119">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4d630-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4d630-120">Příkaz ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="4d630-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="4d630-121">Vzhledem k tomu, že jste do třídy přidali nové pole `Movie` , musíte aktualizovat seznam vazeb vlastností, aby byla tato nová vlastnost zahrnutá.</span><span class="sxs-lookup"><span data-stu-id="4d630-121">Because you've added a new field to the `Movie` class, you need to update the property binding list so this new property will be included.</span></span> <span data-ttu-id="4d630-122">V *MoviesController.cs*aktualizujte `[Bind]` atribut pro `Create` `Edit` metody a akce tak, aby zahrnovaly `Rating` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="4d630-122">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="4d630-123">Aktualizujte šablony zobrazení, aby bylo možné zobrazit, vytvořit a upravit novou `Rating` vlastnost v zobrazení prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4d630-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="4d630-124">Upravte soubor */views/Movies/index.cshtml* a přidejte `Rating` pole:</span><span class="sxs-lookup"><span data-stu-id="4d630-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="4d630-125">Aktualizujte */views/Movies/Create.cshtml* s `Rating` polem.</span><span class="sxs-lookup"><span data-stu-id="4d630-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="4d630-126">Visual Studio/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4d630-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="4d630-127">Můžete zkopírovat a vložit předchozí "skupinu formulářů" a nechat intelliSense, aby vám aktualizovala pole.</span><span class="sxs-lookup"><span data-stu-id="4d630-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="4d630-128">Technologie IntelliSense spolupracuje s [pomocníky značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="4d630-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Vývojář zadal písmeno R pro hodnotu atributu ASP-for v druhém prvku popisku zobrazení.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="4d630-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4d630-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="4d630-133">Aktualizujte zbývající šablony.</span><span class="sxs-lookup"><span data-stu-id="4d630-133">Update the remaining templates.</span></span>

<span data-ttu-id="4d630-134">Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="4d630-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="4d630-135">Tato změna se zobrazuje níže, ale tuto změnu budete chtít udělat pro každou z nich `new Movie` .</span><span class="sxs-lookup"><span data-stu-id="4d630-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4d630-136">Aplikace nebude fungovat, dokud nebude aktualizována databáze, aby zahrnovala nové pole.</span><span class="sxs-lookup"><span data-stu-id="4d630-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="4d630-137">Pokud je spuštěno nyní, `SqlException` je vyvolána následující:</span><span class="sxs-lookup"><span data-stu-id="4d630-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="4d630-138">K této chybě dochází, protože aktualizovaná třída filmového modelu je odlišná od schématu tabulky filmů existující databáze.</span><span class="sxs-lookup"><span data-stu-id="4d630-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="4d630-139">( `Rating` V tabulce databáze není žádný sloupec.)</span><span class="sxs-lookup"><span data-stu-id="4d630-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="4d630-140">K řešení této chyby je potřeba několik přístupů:</span><span class="sxs-lookup"><span data-stu-id="4d630-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="4d630-141">Entity Framework automaticky vyřadit a znovu vytvořit databázi na základě nového schématu třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="4d630-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="4d630-142">Tento přístup je velmi výhodný v rané fázi vývoje, když provádíte aktivní vývoj na testovací databázi. umožňuje rychlou vývoj modelu a schématu databáze dohromady.</span><span class="sxs-lookup"><span data-stu-id="4d630-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="4d630-143">Nevýhodou, ale je to, že ztratíte stávající data v databázi, takže nechcete tento přístup použít v provozní databázi.</span><span class="sxs-lookup"><span data-stu-id="4d630-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="4d630-144">Použití inicializátoru k automatickému osazení databáze s testovacími daty je často produktivním způsobem pro vývoj aplikace.</span><span class="sxs-lookup"><span data-stu-id="4d630-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="4d630-145">To je dobrý přístup pro prvotní vývoj a při použití SQLite.</span><span class="sxs-lookup"><span data-stu-id="4d630-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="4d630-146">Explicitně upravte schéma existující databáze tak, aby odpovídalo třídám modelu.</span><span class="sxs-lookup"><span data-stu-id="4d630-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="4d630-147">Výhodou tohoto přístupu je, že zachováte data.</span><span class="sxs-lookup"><span data-stu-id="4d630-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="4d630-148">Tuto změnu můžete provést buď ručně, nebo vytvořením skriptu změny databáze.</span><span class="sxs-lookup"><span data-stu-id="4d630-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="4d630-149">K aktualizaci schématu databáze použijte Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="4d630-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="4d630-150">Pro tento kurz se používá Migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="4d630-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4d630-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4d630-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4d630-152">V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="4d630-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC – nabídka](adding-model/_static/pmc.png)

<span data-ttu-id="4d630-154">Do PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="4d630-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="4d630-155">`Add-Migration`Příkaz instruuje rámec migrace, aby kontroloval aktuální `Movie` model s aktuálním `Movie` databázovým schématem a vytvořil potřebný kód pro migraci databáze do nového modelu.</span><span class="sxs-lookup"><span data-stu-id="4d630-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="4d630-156">Název "hodnocení" je libovolný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="4d630-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="4d630-157">Je užitečné použít pro migrační soubor smysluplný název.</span><span class="sxs-lookup"><span data-stu-id="4d630-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="4d630-158">Pokud jsou všechny záznamy v databázi odstraněny, metoda Initialize vytvoří databázi a zahrne `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="4d630-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4d630-159">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="4d630-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="4d630-160">Odstraňte databázi a pomocí migrace znovu vytvořte databázi.</span><span class="sxs-lookup"><span data-stu-id="4d630-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="4d630-161">Chcete-li odstranit databázi, odstraňte soubor databáze *MvcMovie. DB* .</span><span class="sxs-lookup"><span data-stu-id="4d630-161">To delete the database, delete the *MvcMovie.db* database file.</span></span> <span data-ttu-id="4d630-162">Pak spusťte `ef database update` příkaz:</span><span class="sxs-lookup"><span data-stu-id="4d630-162">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="4d630-163">Spusťte aplikaci a ověřte, že je možné vytvářet, upravovat a zobrazovat filmy pomocí `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="4d630-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="4d630-164">[Předchozí](search.md) 
>  [Další](validation.md)</span><span class="sxs-lookup"><span data-stu-id="4d630-164">[Previous](search.md)
[Next](validation.md)</span></span>

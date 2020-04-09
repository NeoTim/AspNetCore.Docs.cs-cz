---
title: Přidání nového pole do aplikace Core MVC ASP.NET
author: rick-anderson
description: Zjistěte, jak pomocí migrace entity framework code first přidat nové pole do modelu a migrovat, které se změní do databáze.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: a5ea9b75cf8bb1f31cb07a2b32f361bdbfd4efa3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662902"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="87ec5-103">Přidání nového pole do aplikace Core MVC ASP.NET</span><span class="sxs-lookup"><span data-stu-id="87ec5-103">Add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="87ec5-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="87ec5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="87ec5-105">V této části [entity framework](/ef/core/get-started/aspnetcore/new-db) code first migrace se používá k:</span><span class="sxs-lookup"><span data-stu-id="87ec5-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="87ec5-106">Přidejte do modelu nové pole.</span><span class="sxs-lookup"><span data-stu-id="87ec5-106">Add a new field to the model.</span></span>
* <span data-ttu-id="87ec5-107">Migrujte nové pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="87ec5-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="87ec5-108">Při EF Code First se používá k automatickému vytvoření databáze, Kód první:</span><span class="sxs-lookup"><span data-stu-id="87ec5-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="87ec5-109">Přidá do databáze tabulku ke sledování schématu databáze.</span><span class="sxs-lookup"><span data-stu-id="87ec5-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="87ec5-110">Ověří, že databáze je synchronizována s třídami modelu, ze kterých byla vygenerována.</span><span class="sxs-lookup"><span data-stu-id="87ec5-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="87ec5-111">Pokud nejsou synchronizovány, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="87ec5-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="87ec5-112">To usnadňuje hledání nekonzistentní chod databáze/kódu.</span><span class="sxs-lookup"><span data-stu-id="87ec5-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="87ec5-113">Přidání vlastnosti hodnocení do filmového modelu</span><span class="sxs-lookup"><span data-stu-id="87ec5-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="87ec5-114">Přidat `Rating` vlastnost *modely/Movie.cs*:</span><span class="sxs-lookup"><span data-stu-id="87ec5-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="87ec5-115">Sestavení aplikace</span><span class="sxs-lookup"><span data-stu-id="87ec5-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="87ec5-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ec5-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="87ec5-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="87ec5-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="87ec5-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87ec5-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="87ec5-119">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="87ec5-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="87ec5-120">Příkaz - + B</span><span class="sxs-lookup"><span data-stu-id="87ec5-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="87ec5-121">Vzhledem k tomu, že `Movie` jste do třídy přidali nové pole, je třeba aktualizovat bílý seznam vazeb, aby byla zahrnuta tato nová vlastnost.</span><span class="sxs-lookup"><span data-stu-id="87ec5-121">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="87ec5-122">V *MoviesController.cs*aktualizujte `[Bind]` atribut `Create` pro `Edit` metody i `Rating` metody akce tak, aby zahrnoval vlastnost:</span><span class="sxs-lookup"><span data-stu-id="87ec5-122">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="87ec5-123">Aktualizujte šablony zobrazení tak, aby zobrazovala, `Rating` vytvářela a upravovala novou vlastnost v zobrazení prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="87ec5-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="87ec5-124">Upravte soubor */Views/Movies/Index.cshtml* `Rating` a přidejte pole:</span><span class="sxs-lookup"><span data-stu-id="87ec5-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="87ec5-125">Aktualizujte *parametr /Views/Movies/Create.cshtml* polem. `Rating`</span><span class="sxs-lookup"><span data-stu-id="87ec5-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="87ec5-126">Visual Studio / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="87ec5-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="87ec5-127">Můžete zkopírovat/vložit předchozí "skupina formulářů" a nechat intelliSense, aby vám pomohla aktualizovat pole.</span><span class="sxs-lookup"><span data-stu-id="87ec5-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="87ec5-128">Technologie IntelliSense pracuje s [pomocníky značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="87ec5-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Vývojář zadal písmeno R pro hodnotu atributu asp-for v druhém prvku popisku zobrazení.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="87ec5-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="87ec5-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="87ec5-133">Aktualizujte zbývající šablony.</span><span class="sxs-lookup"><span data-stu-id="87ec5-133">Update the remaining templates.</span></span>

<span data-ttu-id="87ec5-134">Aktualizujte `SeedData` třídu tak, aby poskytovala hodnotu pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="87ec5-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="87ec5-135">Ukázková změna je uvedena níže, ale tuto změnu `new Movie`budete chtít provést pro každou z nich .</span><span class="sxs-lookup"><span data-stu-id="87ec5-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="87ec5-136">Aplikace nebude fungovat, dokud db je aktualizován tak, aby zahrnovala nové pole.</span><span class="sxs-lookup"><span data-stu-id="87ec5-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="87ec5-137">Pokud je spuštěn nyní, `SqlException` je vyvolána následující:</span><span class="sxs-lookup"><span data-stu-id="87ec5-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="87ec5-138">K této chybě dochází, protože aktualizovaná třída modelu Movie se liší od schématu tabulky Movie existující databáze.</span><span class="sxs-lookup"><span data-stu-id="87ec5-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="87ec5-139">(V databázové `Rating` tabulce není žádný sloupec.)</span><span class="sxs-lookup"><span data-stu-id="87ec5-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="87ec5-140">Existuje několik přístupů k řešení chyby:</span><span class="sxs-lookup"><span data-stu-id="87ec5-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="87ec5-141">Mají entity framework automaticky přetažení a znovu vytvořit databázi na základě nového schématu třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="87ec5-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="87ec5-142">Tento přístup je velmi výhodné na počátku vývojového cyklu, když děláte aktivní vývoj na testovací databázi; umožňuje rychle vyvíjet schéma modelu a databáze společně.</span><span class="sxs-lookup"><span data-stu-id="87ec5-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="87ec5-143">Nevýhodou však je, že ztratíte existující data v databázi - takže nechcete používat tento přístup v produkční databázi!</span><span class="sxs-lookup"><span data-stu-id="87ec5-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="87ec5-144">Použití inicializátoru automaticky osiva databáze s testovací data je často produktivní způsob, jak vyvinout aplikaci.</span><span class="sxs-lookup"><span data-stu-id="87ec5-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="87ec5-145">To je dobrý přístup pro časný vývoj a při použití SQLite.</span><span class="sxs-lookup"><span data-stu-id="87ec5-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="87ec5-146">Explicitně upravte schéma existující databáze tak, aby odpovídala třídám modelu.</span><span class="sxs-lookup"><span data-stu-id="87ec5-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="87ec5-147">Výhodou tohoto přístupu je, že uchováváte data.</span><span class="sxs-lookup"><span data-stu-id="87ec5-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="87ec5-148">Tuto změnu můžete provést ručně nebo vytvořením skriptu pro změnu databáze.</span><span class="sxs-lookup"><span data-stu-id="87ec5-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="87ec5-149">K aktualizaci schématu databáze použijte migrace prvního kódu.</span><span class="sxs-lookup"><span data-stu-id="87ec5-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="87ec5-150">Pro účely tohoto kurzu se používá migrace code first.</span><span class="sxs-lookup"><span data-stu-id="87ec5-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="87ec5-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="87ec5-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="87ec5-152">V nabídce **Nástroje** vyberte **položku NuGet Package Manager > Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="87ec5-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![Nabídka PMC](adding-model/_static/pmc.png)

<span data-ttu-id="87ec5-154">Do pmc zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="87ec5-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="87ec5-155">Příkaz `Add-Migration` sděluje rozhraní pro `Movie` migraci, `Movie` aby prozkoumalaktuální model s aktuálním schématem DB a vytvořil potřebný kód pro migraci DB do nového modelu.</span><span class="sxs-lookup"><span data-stu-id="87ec5-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="87ec5-156">Název "Hodnocení" je libovolný a používá se k pojmenování migračního souboru.</span><span class="sxs-lookup"><span data-stu-id="87ec5-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="87ec5-157">Je užitečné použít smysluplný název pro soubor migrace.</span><span class="sxs-lookup"><span data-stu-id="87ec5-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="87ec5-158">Pokud jsou odstraněny všechny záznamy v DB, inicializovat metoda bude `Rating` osiva DB a zahrnout pole.</span><span class="sxs-lookup"><span data-stu-id="87ec5-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="87ec5-159">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="87ec5-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="87ec5-160">Odstraňte databázi a použijte migrace znovu vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="87ec5-160">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="87ec5-161">Chcete-li databázi odstranit, odstraňte databázový soubor (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="87ec5-161">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="87ec5-162">Pak spusťte `ef database update` příkaz:</span><span class="sxs-lookup"><span data-stu-id="87ec5-162">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="87ec5-163">Spusťte aplikaci a ověřte, zda `Rating` můžete vytvářet, upravovat a zobrazovat filmy pomocí pole.</span><span class="sxs-lookup"><span data-stu-id="87ec5-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span> <span data-ttu-id="87ec5-164">Aktualizace aplikace:</span><span class="sxs-lookup"><span data-stu-id="87ec5-164">Update the app:</span></span>

* <span data-ttu-id="87ec5-165">Přidejte `Rating` pole `Edit`do `Details`šablony `Delete` , a zobrazte je.</span><span class="sxs-lookup"><span data-stu-id="87ec5-165">Add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>
* <span data-ttu-id="87ec5-166">Aktualizujte vazbu v metodě `MoviesController`akce úprav .</span><span class="sxs-lookup"><span data-stu-id="87ec5-166">Update the binding in the edit action method of the `MoviesController`.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="87ec5-167">[Předchozí](search.md)
> [další](validation.md)</span><span class="sxs-lookup"><span data-stu-id="87ec5-167">[Previous](search.md)
[Next](validation.md)</span></span>

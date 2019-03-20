---
ms.openlocfilehash: 088f347cee73e745882455e6882d24a1e949f095
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265067"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="86e5c-101">Přidání nového pole do aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="86e5c-101">Add a new field to an ASP.NET Core MVC app</span></span>

<!-- This include not used by windows version -->

<span data-ttu-id="86e5c-102">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="86e5c-102">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="86e5c-103">V tomto kurzu přidáte nové pole do `Movies` tabulky.</span><span class="sxs-lookup"><span data-stu-id="86e5c-103">This tutorial will add a new field to the `Movies` table.</span></span> <span data-ttu-id="86e5c-104">Vytvoříme vyřaďte databázi a vytvořte novou, když se změní schéma (přidání nového pole).</span><span class="sxs-lookup"><span data-stu-id="86e5c-104">We'll drop the database and create a new one when we change the schema (add a new field).</span></span> <span data-ttu-id="86e5c-105">Tento pracovní postup funguje dobře již v rané fázi při vývoji, když nemáme žádné produkční data a zachovat.</span><span class="sxs-lookup"><span data-stu-id="86e5c-105">This workflow works well early in development when we don't have any production data to preserve.</span></span>

<span data-ttu-id="86e5c-106">Jakmile je aplikace nasazená a máte data, která je potřeba zachovat, nelze vyřadit vaší databáze, pokud je třeba změnit schéma.</span><span class="sxs-lookup"><span data-stu-id="86e5c-106">Once your app is deployed and you have data that you need to preserve, you can't drop your DB when you need to change the schema.</span></span> <span data-ttu-id="86e5c-107">Entity Framework [migrace Code First](/ef/core/get-started/aspnetcore/new-db) vám umožní aktualizovat schéma a migrovat databáze beze ztráty dat.</span><span class="sxs-lookup"><span data-stu-id="86e5c-107">Entity Framework [Code First Migrations](/ef/core/get-started/aspnetcore/new-db) allows you to update your schema and migrate the database without losing data.</span></span> <span data-ttu-id="86e5c-108">Migrace je oblíbené funkce při použití SQL serveru, ale SQLite nepodporuje mnoho operací migrace schématu, tak jenom velmi jednoduše migrace jsou možné.</span><span class="sxs-lookup"><span data-stu-id="86e5c-108">Migrations is a popular feature when using SQL Server, but SQLite doesn't support many migration schema operations, so only very simply migrations are possible.</span></span> <span data-ttu-id="86e5c-109">Zobrazit [omezení SQLite](/ef/core/providers/sqlite/limitations) Další informace.</span><span class="sxs-lookup"><span data-stu-id="86e5c-109">See [SQLite Limitations](/ef/core/providers/sqlite/limitations) for more information.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="86e5c-110">Přidání vlastnosti do hodnocení filmů modelu</span><span class="sxs-lookup"><span data-stu-id="86e5c-110">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="86e5c-111">Otevřít *Models/Movie.cs* a přidejte `Rating` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="86e5c-111">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Models/MovieDateRating.cs?highlight=12&name=snippet)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRating.cs?highlight=11&range=7-18)]

::: moniker-end

<span data-ttu-id="86e5c-112">Protože jsme přidali nové pole do `Movie` třídy, je také potřeba aktualizovat na seznamu povolených elementů vazby tak tuto novou vlastnost budou zahrnuty.</span><span class="sxs-lookup"><span data-stu-id="86e5c-112">Because you've added a new field to the `Movie` class, you also need to update the binding whitelist so this new property will be included.</span></span> <span data-ttu-id="86e5c-113">V *MoviesController.cs*, aktualizovat `[Bind]` atribut pro obě `Create` a `Edit` metody akce, které chcete zahrnout `Rating` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="86e5c-113">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="86e5c-114">Budete také potřebovat aktualizovat zobrazit šablony, aby bylo možné zobrazit, vytvářet a upravovat nové `Rating` vlastností v okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="86e5c-114">You also need to update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="86e5c-115">Upravit */Views/Movies/Index.cshtml* a přidejte `Rating` pole:</span><span class="sxs-lookup"><span data-stu-id="86e5c-115">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexGenreRating.cshtml?highlight=17,39&range=24-64)]

<span data-ttu-id="86e5c-116">Aktualizace */Views/Movies/Create.cshtml* s `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="86e5c-116">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

<span data-ttu-id="86e5c-117">Aplikace nebude fungovat, dokud aktualizujeme DB zahrnout nové pole.</span><span class="sxs-lookup"><span data-stu-id="86e5c-117">The app won't work until we update the DB to include the new field.</span></span> <span data-ttu-id="86e5c-118">Pokud jste ji nyní spustit, zobrazí se následující `SqliteException`:</span><span class="sxs-lookup"><span data-stu-id="86e5c-118">If you run it now, you'll get the following `SqliteException`:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: m.Rating'.
```

<span data-ttu-id="86e5c-119">Tato chyba se zobrazuje, protože aktualizované třídy modelu film se liší od schématu tabulky Movie existující databáze.</span><span class="sxs-lookup"><span data-stu-id="86e5c-119">You're seeing this error because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="86e5c-120">(Neexistuje žádný `Rating` sloupec v tabulce databáze.)</span><span class="sxs-lookup"><span data-stu-id="86e5c-120">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="86e5c-121">Řešení chyby několika způsoby:</span><span class="sxs-lookup"><span data-stu-id="86e5c-121">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="86e5c-122">Vyřaďte databázi a mít automaticky znovu vytvořit databázi založené na nové schéma třídy modelu Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="86e5c-122">Drop the database and have the Entity Framework automatically re-create the database based on the new model class schema.</span></span> <span data-ttu-id="86e5c-123">S tímto přístupem budete dojít ke ztrátě existujících dat v databázi, proto nelze provést s produkční databází.</span><span class="sxs-lookup"><span data-stu-id="86e5c-123">With this approach, you lose existing data in the database — so you can't do this with a production database!</span></span> <span data-ttu-id="86e5c-124">Použití inicializátoru automaticky naplnit databázi daty testu je často produktivní způsob, jak vyvíjet aplikace.</span><span class="sxs-lookup"><span data-stu-id="86e5c-124">Using an initializer to automatically seed a database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="86e5c-125">Ručně upravte schéma stávající databázi tak, aby odpovídalo tříd modelu.</span><span class="sxs-lookup"><span data-stu-id="86e5c-125">Manually modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="86e5c-126">Výhodou tohoto přístupu je, že zachováte vaše data.</span><span class="sxs-lookup"><span data-stu-id="86e5c-126">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="86e5c-127">Můžete tuto změnu provést buď ručně, nebo tak, že vytvoříte databázi změnit skript.</span><span class="sxs-lookup"><span data-stu-id="86e5c-127">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="86e5c-128">Pomocí migrace Code First aktualizovat schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="86e5c-128">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="86e5c-129">V tomto kurzu vytvoříme vyřadit a znovu vytvořit databázi, když se změní schéma.</span><span class="sxs-lookup"><span data-stu-id="86e5c-129">For this tutorial, we'll drop and re-create the database when the schema changes.</span></span> <span data-ttu-id="86e5c-130">Z terminálu vyřazení databáze spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="86e5c-130">Run the following command from a terminal to drop the db:</span></span>

`dotnet ef database drop`

<span data-ttu-id="86e5c-131">Aktualizace `SeedData` třídy tak, že poskytuje hodnoty pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="86e5c-131">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="86e5c-132">Ukázka změnu je uveden níže, ale budete chtít tuto změnu pro každou `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="86e5c-132">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="86e5c-133">Přidat `Rating` pole `Edit`, `Details`, a `Delete` zobrazení.</span><span class="sxs-lookup"><span data-stu-id="86e5c-133">Add the `Rating` field to the `Edit`, `Details`, and `Delete` view.</span></span>

<span data-ttu-id="86e5c-134">Spusťte aplikaci a ověřit, je možné vytvořit/upravit/zobrazit videa s `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="86e5c-134">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="86e5c-135">šablony.</span><span class="sxs-lookup"><span data-stu-id="86e5c-135">templates.</span></span>

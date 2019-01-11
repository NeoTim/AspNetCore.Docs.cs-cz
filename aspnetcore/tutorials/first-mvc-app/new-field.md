---
title: Přidání nového pole do aplikace ASP.NET Core MVC
author: rick-anderson
description: Další informace o použití migrace Entity Framework Code First pro přidání nového pole do modelu a migrovat tuto změnu do databáze.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 7993b36bf9115225e082d2929bb253aba5b18310
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207366"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="a0a07-103">Přidání nového pole do aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="a0a07-103">Add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="a0a07-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a0a07-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a0a07-105">V této části [Entity Framework](/ef/core/get-started/aspnetcore/new-db) se používá k migrace Code First:</span><span class="sxs-lookup"><span data-stu-id="a0a07-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="a0a07-106">Přidání nového pole do modelu.</span><span class="sxs-lookup"><span data-stu-id="a0a07-106">Add a new field to the model.</span></span>
* <span data-ttu-id="a0a07-107">Migrace nové pole do databáze.</span><span class="sxs-lookup"><span data-stu-id="a0a07-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="a0a07-108">Když platforem EF Code First umožňuje automaticky vytvořte databázi, Code First:</span><span class="sxs-lookup"><span data-stu-id="a0a07-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="a0a07-109">Tabulka se přidá do databáze, kterou chcete sledovat schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="a0a07-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="a0a07-110">Ověřte, že je synchronizovaný s tříd modelu, které byly vygenerovány z databáze.</span><span class="sxs-lookup"><span data-stu-id="a0a07-110">Verify the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="a0a07-111">Pokud nejsou synchronizované, EF vyvolá výjimku.</span><span class="sxs-lookup"><span data-stu-id="a0a07-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="a0a07-112">Díky tomu je snazší najít problémy s nekonzistentní databáze nebo kódu.</span><span class="sxs-lookup"><span data-stu-id="a0a07-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="a0a07-113">Přidání vlastnosti hodnocení filmů modelu</span><span class="sxs-lookup"><span data-stu-id="a0a07-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="a0a07-114">Přidat `Rating` vlastnost *Models/Movie.cs*:</span><span class="sxs-lookup"><span data-stu-id="a0a07-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="a0a07-115">Vytvořte aplikaci (Ctrl + Shift + B).</span><span class="sxs-lookup"><span data-stu-id="a0a07-115">Build the app (Ctrl+Shift+B).</span></span>

<span data-ttu-id="a0a07-116">Protože jsme přidali nové pole do `Movie` třídy, budete muset aktualizovat vazbu seznamu povolených, aby tuto novou vlastnost budou zahrnuty.</span><span class="sxs-lookup"><span data-stu-id="a0a07-116">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="a0a07-117">V *MoviesController.cs*, aktualizovat `[Bind]` atribut pro obě `Create` a `Edit` metody akce, které chcete zahrnout `Rating` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="a0a07-117">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("ID,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="a0a07-118">Aktualizace šablon zobrazení, aby bylo možné zobrazit, vytvořit a upravit novou `Rating` vlastností v okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a0a07-118">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="a0a07-119">Upravit */Views/Movies/Index.cshtml* a přidejte `Rating` pole:</span><span class="sxs-lookup"><span data-stu-id="a0a07-119">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="a0a07-120">Aktualizace */Views/Movies/Create.cshtml* s `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a0a07-120">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

<!-- VS -------------------------->
# <a name="visual-studio--visual-studio-for-mactabvisual-studiovisual-studio-mac"></a>[<span data-ttu-id="a0a07-121">Visual Studio nebo Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a0a07-121">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="a0a07-122">Můžete kopírovat/vložit předchozí "formuláře skupinu" a aktualizujte pole pomohou technologie intelliSense.</span><span class="sxs-lookup"><span data-stu-id="a0a07-122">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="a0a07-123">Technologie IntelliSense funguje s [pomocných rutin značek](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="a0a07-123">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Vývojář napsal písmeno R pro hodnotu atributu ASP-pro druhý popisek prvku zobrazení.](new-field/_static/cr.png)

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a0a07-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a0a07-127">Visual Studio Code</span></span>](#tab/visual-studio-code)
<!-- This tab intentionally left blank. -->
---  
<!-- End of VS tabs -->

<span data-ttu-id="a0a07-128">Aktualizace `SeedData` třídy tak, že poskytuje hodnoty pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="a0a07-128">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="a0a07-129">Ukázka změnu je uveden níže, ale budete chtít tuto změnu pro každou `new Movie`.</span><span class="sxs-lookup"><span data-stu-id="a0a07-129">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="a0a07-130">Aplikace nebude fungovat, dokud databáze je aktualizováno, aby zahrnovalo nové pole.</span><span class="sxs-lookup"><span data-stu-id="a0a07-130">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="a0a07-131">Pokud se má spustit nyní následující `SqlException` je vyvolána výjimka:</span><span class="sxs-lookup"><span data-stu-id="a0a07-131">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="a0a07-132">K této chybě dochází, protože se liší od schématu tabulky Movie existující databáze aktualizované třídy modelu video.</span><span class="sxs-lookup"><span data-stu-id="a0a07-132">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="a0a07-133">(Neexistuje žádný `Rating` sloupec v tabulce databáze.)</span><span class="sxs-lookup"><span data-stu-id="a0a07-133">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="a0a07-134">Řešení chyby několika způsoby:</span><span class="sxs-lookup"><span data-stu-id="a0a07-134">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="a0a07-135">Máte rozhraní Entity Framework automaticky vyřadit a znovu vytvořit databázi založené na nové schéma třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="a0a07-135">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="a0a07-136">Tento přístup je velmi vhodné v rané fázi vývojového cyklu, pokud provádíte databázi testu; s aktivním vývojem umožňuje rychlý rozvoj schématu modelu a databáze společně.</span><span class="sxs-lookup"><span data-stu-id="a0a07-136">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="a0a07-137">Nevýhodou, je však dojít ke ztrátě existujících dat v databázi, takže nechcete tuto metodu použijte u provozní databáze.</span><span class="sxs-lookup"><span data-stu-id="a0a07-137">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="a0a07-138">Použití inicializátoru automaticky naplnit databázi daty testu je často produktivní způsob, jak vyvíjet aplikace.</span><span class="sxs-lookup"><span data-stu-id="a0a07-138">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="a0a07-139">To je dobrý nápad pro raného vývoje a při použití SQLite.</span><span class="sxs-lookup"><span data-stu-id="a0a07-139">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="a0a07-140">Explicitně upravte schéma stávající databázi tak, aby odpovídalo tříd modelu.</span><span class="sxs-lookup"><span data-stu-id="a0a07-140">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="a0a07-141">Výhodou tohoto přístupu je, že zachováte vaše data.</span><span class="sxs-lookup"><span data-stu-id="a0a07-141">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="a0a07-142">Můžete tuto změnu provést buď ručně, nebo tak, že vytvoříte databázi změnit skript.</span><span class="sxs-lookup"><span data-stu-id="a0a07-142">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="a0a07-143">Pomocí migrace Code First aktualizovat schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="a0a07-143">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="a0a07-144">V tomto kurzu se používá migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="a0a07-144">For this tutorial, Code First Migrations is used.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a0a07-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0a07-145">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a0a07-146">Z **nástroje** nabídce vyberte možnost **Správce balíčků NuGet > Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="a0a07-146">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC nabídky](adding-model/_static/pmc.png)

<span data-ttu-id="a0a07-148">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="a0a07-148">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="a0a07-149">`Add-Migration` Příkaz říká rozhraní framework migrace ke kontrole aktuální `Movie` modelů s aktuálním `Movie` schématu databáze a vytvářet kód potřebné k migraci databáze na nový model.</span><span class="sxs-lookup"><span data-stu-id="a0a07-149">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="a0a07-150">Visual Studio Code nebo Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a0a07-150">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="a0a07-151">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a0a07-151">Run the following command:</span></span>

```cli
dotnet ef migrations add Rating
dotnet ef database update
```

---  
<!-- End of VS tabs -->

<span data-ttu-id="a0a07-152">Název "Hodnocení" je volitelný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="a0a07-152">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="a0a07-153">Je vhodné použít smysluplný název souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="a0a07-153">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="a0a07-154">Pokud se odstraní všechny záznamy z databáze, Metoda initialize bude počáteční hodnoty databáze a uveďte `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a0a07-154">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

<span data-ttu-id="a0a07-155">Spusťte aplikaci a ověřit, je možné vytvořit/upravit/zobrazit videa s `Rating` pole.</span><span class="sxs-lookup"><span data-stu-id="a0a07-155">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="a0a07-156">Měli byste přidat `Rating` pole `Edit`, `Details`, a `Delete` zobrazení šablon.</span><span class="sxs-lookup"><span data-stu-id="a0a07-156">You should add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a0a07-157">[Předchozí](search.md)
> [další](validation.md)</span><span class="sxs-lookup"><span data-stu-id="a0a07-157">[Previous](search.md)
[Next](validation.md)</span></span>  

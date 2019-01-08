---
uid: mvc/overview/getting-started/introduction/adding-a-new-field
title: Přidání nového pole | Dokumentace Microsoftu
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 10/17/2013
ms.assetid: 4085de68-d243-4378-8a64-86236ea8d2da
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-a-new-field
msc.type: authoredcontent
ms.openlocfilehash: 950ae17ebd6b0f15520c2a4e9372703f5374dfbe
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098685"
---
<a name="adding-a-new-field"></a><span data-ttu-id="21af4-102">Přidání nového pole</span><span class="sxs-lookup"><span data-stu-id="21af4-102">Adding a New Field</span></span>
====================
<span data-ttu-id="21af4-103">Podle [Rick Anderson]((https://twitter.com/RickAndMSFT))</span><span class="sxs-lookup"><span data-stu-id="21af4-103">by [Rick Anderson]((https://twitter.com/RickAndMSFT))</span></span>

[!INCLUDE [Tutorial Note](sample/code-location.md)]

<span data-ttu-id="21af4-104">V této části použijete migrace Entity Framework Code First k migraci některých změn do tříd modelu, tak použití této změny do databáze.</span><span class="sxs-lookup"><span data-stu-id="21af4-104">In this section you'll use Entity Framework Code First Migrations to migrate some changes to the model classes so the change is applied to the database.</span></span>

<span data-ttu-id="21af4-105">Ve výchozím nastavení při použití platformy Entity Framework Code First automaticky vytvořit databázi, jako jste to udělali dříve v tomto kurzu Code First přidá tabulku do databáze pro sledování, zda je synchronizovaný s tříd modelu, které byly vygenerovány z schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="21af4-105">By default, when you use Entity Framework Code First to automatically create a database, as you did earlier in this tutorial, Code First adds a table to the database to help track whether the schema of the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="21af4-106">Pokud nejsou synchronizované, Entity Framework vyvolá chybu.</span><span class="sxs-lookup"><span data-stu-id="21af4-106">If they aren't in sync, the Entity Framework throws an error.</span></span> <span data-ttu-id="21af4-107">Díky tomu je snadněji sledovat problémy při vývoji, který může jinak pouze pro vás (pomocí skrytého chyby) v době běhu.</span><span class="sxs-lookup"><span data-stu-id="21af4-107">This makes it easier to track down issues at development time that you might otherwise only find (by obscure errors) at run time.</span></span>

## <a name="setting-up-code-first-migrations-for-model-changes"></a><span data-ttu-id="21af4-108">Nastavení migrace Code First pro změny modelu</span><span class="sxs-lookup"><span data-stu-id="21af4-108">Setting up Code First Migrations for Model Changes</span></span>

<span data-ttu-id="21af4-109">Přejděte do Průzkumníka řešení.</span><span class="sxs-lookup"><span data-stu-id="21af4-109">Navigate to Solution Explorer.</span></span> <span data-ttu-id="21af4-110">Klikněte pravým tlačítkem na *Movies.mdf* a vyberte možnost **odstranit** odebrání databáze filmů.</span><span class="sxs-lookup"><span data-stu-id="21af4-110">Right click on the *Movies.mdf* file and select **Delete** to remove the movies database.</span></span> <span data-ttu-id="21af4-111">Pokud se nezobrazí *Movies.mdf* souboru, klikněte na **zobrazit všechny soubory** ikonu znázorněném na červené ohraničení.</span><span class="sxs-lookup"><span data-stu-id="21af4-111">If you don't see the *Movies.mdf* file, click on the **Show All Files** icon shown below in the red outline.</span></span>

![](adding-a-new-field/_static/image1.png)

<span data-ttu-id="21af4-112">Vytvoření aplikace, abyste měli jistotu, že nejsou žádné chyby.</span><span class="sxs-lookup"><span data-stu-id="21af4-112">Build the application to make sure there are no errors.</span></span>

<span data-ttu-id="21af4-113">Z **nástroje** nabídky, klikněte na tlačítko **Správce balíčků NuGet** a potom **Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="21af4-113">From the **Tools** menu, click **NuGet Package Manager** and then **Package Manager Console**.</span></span>

![Přidat balíček Man](adding-a-new-field/_static/image2.png)

<span data-ttu-id="21af4-115">V **Konzola správce balíčků** v okna `PM>` řádku zadejte</span><span class="sxs-lookup"><span data-stu-id="21af4-115">In the **Package Manager Console** window at the `PM>` prompt enter</span></span>

<span data-ttu-id="21af4-116">Povolení migrace - ContextTypeName MvcMovie.Models.MovieDBContext</span><span class="sxs-lookup"><span data-stu-id="21af4-116">Enable-Migrations -ContextTypeName MvcMovie.Models.MovieDBContext</span></span>

![](adding-a-new-field/_static/image3.png)

<span data-ttu-id="21af4-117">**Povolení migrace** vytvoří příkaz (popsaný výš) *Configuration.cs* souboru v novém *migrace* složky.</span><span class="sxs-lookup"><span data-stu-id="21af4-117">The **Enable-Migrations** command (shown above) creates a *Configuration.cs* file in a new *Migrations* folder.</span></span>

![](adding-a-new-field/_static/image4.png)

<span data-ttu-id="21af4-118">Visual Studio otevře *Configuration.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="21af4-118">Visual Studio opens the *Configuration.cs* file.</span></span> <span data-ttu-id="21af4-119">Nahradit `Seed` metodu *Configuration.cs* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="21af4-119">Replace the `Seed` method in the *Configuration.cs* file with the following code:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample1.cs)]

<span data-ttu-id="21af4-120">Najeďte myší červená vlnovka čára pod `Movie` a klikněte na tlačítko `Show Potential Fixes` a potom klikněte na tlačítko **pomocí** **MvcMovie.Models;**</span><span class="sxs-lookup"><span data-stu-id="21af4-120">Hover over the red squiggly line under `Movie` and click `Show Potential Fixes` and then click **using** **MvcMovie.Models;**</span></span>

![](adding-a-new-field/_static/image5.png)

<span data-ttu-id="21af4-121">Tím se přidají následující příkaz using:</span><span class="sxs-lookup"><span data-stu-id="21af4-121">Doing so adds the following using statement:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample2.cs)]

> [!NOTE]
> 
> <span data-ttu-id="21af4-122">Kód volá první migrace `Seed` za každou migraci (tedy volání **aktualizace databáze** v konzole Správce balíčků), a tato metoda aktualizuje řádky, které již byl vložen a vloží je, pokud jsou dosud neexistují.</span><span class="sxs-lookup"><span data-stu-id="21af4-122">Code First Migrations calls the `Seed` method after every migration (that is, calling **update-database** in the Package Manager Console), and this method updates rows that have already been inserted, or inserts them if they don't exist yet.</span></span>
> 
> <span data-ttu-id="21af4-123">[AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) v následujícím kódu metoda provádí operaci "upsert":</span><span class="sxs-lookup"><span data-stu-id="21af4-123">The [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) method in the following code performs an "upsert" operation:</span></span>
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample3.cs)]
> 
> <span data-ttu-id="21af4-124">Vzhledem k tomu, [počáteční hodnoty](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) metoda pracuje s každou migraci, data, nelze vložit stejně, protože se snažíte přidat řádky už budou tam po první migrace, který vytvoří databázi.</span><span class="sxs-lookup"><span data-stu-id="21af4-124">Because the [Seed](https://msdn.microsoft.com/library/hh829453(v=vs.103).aspx) method runs with every migration, you can't just insert data, because the rows you are trying to add will already be there after the first migration that creates the database.</span></span> <span data-ttu-id="21af4-125">"[Upsert](http://en.wikipedia.org/wiki/Upsert)" operaci brání chyby, které by mohlo dojít, pokud se pokusíte vložit řádek, který již existuje, ale přepíše všechny změny dat, který může mít provedené při testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="21af4-125">The "[upsert](http://en.wikipedia.org/wiki/Upsert)" operation prevents errors that would happen if you try to insert a row that already exists, but it overrides any changes to data that you may have made while testing the application.</span></span> <span data-ttu-id="21af4-126">Pomocí testovacích dat v některých tabulkách nechcete provést: v některých případech při změně dat při testování chcete své změny zůstat po aktualizacích databáze.</span><span class="sxs-lookup"><span data-stu-id="21af4-126">With test data in some tables you might not want that to happen: in some cases when you change data while testing you want your changes to remain after database updates.</span></span> <span data-ttu-id="21af4-127">V takovém případě ji chcete vložit podmíněné operace: Vložit řádek jenom v případě, že ještě neexistuje.</span><span class="sxs-lookup"><span data-stu-id="21af4-127">In that case you want to do a conditional insert operation: insert a row only if it doesn't already exist.</span></span>   
> 
> <span data-ttu-id="21af4-128">První parametr předána [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) metody určuje vlastnost, která má použít ke kontrole, jestli řádek již existuje.</span><span class="sxs-lookup"><span data-stu-id="21af4-128">The first parameter passed to the [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) method specifies the property to use to check if a row already exists.</span></span> <span data-ttu-id="21af4-129">Pro data o filmech test, který tím `Title` vlastnost lze použít pro tento účel, protože každý název v seznamu je jedinečný:</span><span class="sxs-lookup"><span data-stu-id="21af4-129">For the test movie data that you are providing, the `Title` property can be used for this purpose since each title in the list is unique:</span></span>
> 
> [!code-csharp[Main](adding-a-new-field/samples/sample4.cs)]
> 
> <span data-ttu-id="21af4-130">Tento kód předpokládá, že jsou jedinečné názvy.</span><span class="sxs-lookup"><span data-stu-id="21af4-130">This code assumes that titles are unique.</span></span> <span data-ttu-id="21af4-131">Pokud chcete ručně přidat duplicitní název, zobrazí se následující výjimka při příštím provedení migrace.</span><span class="sxs-lookup"><span data-stu-id="21af4-131">If you manually add a duplicate title, you'll get the following exception the next time you perform a migration.</span></span>   
> 
>  <span data-ttu-id="21af4-132">*Posloupnost obsahuje více než jeden prvek.*</span><span class="sxs-lookup"><span data-stu-id="21af4-132">*Sequence contains more than one element*</span></span>  
> 
> <span data-ttu-id="21af4-133">Další informace o [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) metodu, najdete v článku [postará metodou AddOrUpdate 4.3 EF](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/)...</span><span class="sxs-lookup"><span data-stu-id="21af4-133">For more information about the [AddOrUpdate](https://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx) method, see [Take care with EF 4.3 AddOrUpdate Method](http://thedatafarm.com/blog/data-access/take-care-with-ef-4-3-addorupdate-method/)..</span></span>


<span data-ttu-id="21af4-134">**Stisknutím klávesy CTRL-SHIFT-B a sestavte projekt.** (Následujících kroků selže, pokud není v tomto okamžiku sestavení.)</span><span class="sxs-lookup"><span data-stu-id="21af4-134">**Press CTRL-SHIFT-B to build the project.**(The following steps will fail if you don't build at this point.)</span></span>

<span data-ttu-id="21af4-135">Dalším krokem je vytvoření `DbMigration` třídu pro počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="21af4-135">The next step is to create a `DbMigration` class for the initial migration.</span></span> <span data-ttu-id="21af4-136">Migrace vytvoří novou databázi, proto můžete odstranit *movie.mdf* souboru v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="21af4-136">This migration creates a new database, that's why you deleted the *movie.mdf* file in a previous step.</span></span>

<span data-ttu-id="21af4-137">V **Konzola správce balíčků** okno, zadejte příkaz `add-migration Initial` vytvořit počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="21af4-137">In the **Package Manager Console** window, enter the command `add-migration Initial` to create the initial migration.</span></span> <span data-ttu-id="21af4-138">Název "Počáteční" libovolný a slouží k pojmenování souboru migrace vytvořili.</span><span class="sxs-lookup"><span data-stu-id="21af4-138">The name "Initial" is arbitrary and is used to name the migration file created.</span></span>

![](adding-a-new-field/_static/image6.png)

<span data-ttu-id="21af4-139">Migrace Code First vytvoří další soubor třídy v *migrace* složky (s názvem *{DateStamp}\_Initial.cs* ), a tato třída obsahuje kód, který vytvoří schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="21af4-139">Code First Migrations creates another class file in the *Migrations* folder (with the name *{DateStamp}\_Initial.cs* ), and this class contains code that creates the database schema.</span></span> <span data-ttu-id="21af4-140">Název souboru migraci je pevně předem s časovým razítkem Nápověda k řazení.</span><span class="sxs-lookup"><span data-stu-id="21af4-140">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="21af4-141">Zkontrolujte *{DateStamp}\_Initial.cs* souboru, obsahuje pokyny pro vytvoření `Movies` tabulky pro video DB.</span><span class="sxs-lookup"><span data-stu-id="21af4-141">Examine the *{DateStamp}\_Initial.cs* file, it contains the instructions to create the `Movies` table for the Movie DB.</span></span> <span data-ttu-id="21af4-142">Při aktualizaci databáze v pokynech níže to *{DateStamp}\_Initial.cs* souboru spustí a vytvořit schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="21af4-142">When you update the database in the instructions below, this *{DateStamp}\_Initial.cs* file will run and create the DB schema.</span></span> <span data-ttu-id="21af4-143">Pak bude **počáteční hodnoty** metoda spustí k naplnění databáze se testovací data.</span><span class="sxs-lookup"><span data-stu-id="21af4-143">Then the **Seed** method will run to populate the DB with test data.</span></span>

<span data-ttu-id="21af4-144">V **Konzola správce balíčků**, zadejte příkaz `update-database` vytvořte databázi a spusťte `Seed` metody.</span><span class="sxs-lookup"><span data-stu-id="21af4-144">In the **Package Manager Console**, enter the command `update-database` to create the database and run the `Seed` method.</span></span>

![](adding-a-new-field/_static/image7.png)

<span data-ttu-id="21af4-145">Pokud dojde k chybě, která určuje již existuje a nedá se vytvořit tabulku, je možné, že jste spustili aplikaci po odstranění databáze a předtím, než jste spustili `update-database`.</span><span class="sxs-lookup"><span data-stu-id="21af4-145">If you get an error that indicates a table already exists and can't be created, it is probably because you ran the application after you deleted the database and before you executed `update-database`.</span></span> <span data-ttu-id="21af4-146">V takovém případě odstranit *Movies.mdf* soubor znovu a zkuste to znovu `update-database` příkazu.</span><span class="sxs-lookup"><span data-stu-id="21af4-146">In that case, delete the *Movies.mdf* file again and retry the `update-database` command.</span></span> <span data-ttu-id="21af4-147">Pokud stále dojde k chybě, odstraňte složku migrace a obsah, pak spusťte v pokynech v horní části této stránky (to je odstranit *Movies.mdf* souborů pak přejděte k povolení migrace).</span><span class="sxs-lookup"><span data-stu-id="21af4-147">If you still get an error, delete the migrations folder and contents then start with the instructions at the top of this page (that is delete the *Movies.mdf* file then proceed to Enable-Migrations).</span></span> <span data-ttu-id="21af4-148">Pokud stále dojde k chybě, otevřete Průzkumník objektů systému SQL Server a odeberte databázi ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="21af4-148">If you still get an error, open SQL Server Object Explorer and remove the database from the list.</span></span>

<span data-ttu-id="21af4-149">Spusťte aplikaci a přejděte */Movies* adresy URL.</span><span class="sxs-lookup"><span data-stu-id="21af4-149">Run the application and navigate to the */Movies* URL.</span></span> <span data-ttu-id="21af4-150">Počáteční data se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="21af4-150">The seed data is displayed.</span></span>

![](adding-a-new-field/_static/image8.png)

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="21af4-151">Přidání vlastnosti do hodnocení filmů modelu</span><span class="sxs-lookup"><span data-stu-id="21af4-151">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="21af4-152">Začněte přidáním nového `Rating` vlastnost ke stávající `Movie` třídy.</span><span class="sxs-lookup"><span data-stu-id="21af4-152">Start by adding a new `Rating` property to the existing `Movie` class.</span></span> <span data-ttu-id="21af4-153">Otevřít *Models\Movie.cs* a přidejte `Rating` vlastnost podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="21af4-153">Open the *Models\Movie.cs* file and add the `Rating` property like this one:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample5.cs)]

<span data-ttu-id="21af4-154">Kompletní `Movie` třídy teď vypadá jako v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="21af4-154">The complete `Movie` class now looks like the following code:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample6.cs?highlight=12)]

<span data-ttu-id="21af4-155">Vytvořte aplikaci (Ctrl + Shift + B).</span><span class="sxs-lookup"><span data-stu-id="21af4-155">Build the application (Ctrl+Shift+B).</span></span>

<span data-ttu-id="21af4-156">Protože jsme přidali nové pole do `Movie` třídy, je také potřeba aktualizovat vazbu *seznamu povolených* tak tuto novou vlastnost budou zahrnuty.</span><span class="sxs-lookup"><span data-stu-id="21af4-156">Because you've added a new field to the `Movie` class, you also need to update the binding *white list* so this new property will be included.</span></span> <span data-ttu-id="21af4-157">Aktualizace `bind` atributu `Create` a `Edit` metody akce, které chcete zahrnout `Rating` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="21af4-157">Update the `bind` attribute for `Create` and `Edit` action methods to include the `Rating` property:</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample7.cs?highlight=1)]

<span data-ttu-id="21af4-158">Je také potřeba aktualizovat zobrazit šablony, aby bylo možné zobrazit, vytvořit a upravit novou `Rating` vlastností v okně prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="21af4-158">You also need to update the view templates in order to display, create and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="21af4-159">Otevřít *\Views\Movies\Index.cshtml* a přidejte `<th>Rating</th>` hned za záhlaví sloupce **cena** sloupec.</span><span class="sxs-lookup"><span data-stu-id="21af4-159">Open the *\Views\Movies\Index.cshtml* file and add a `<th>Rating</th>` column heading just after the **Price** column.</span></span> <span data-ttu-id="21af4-160">Pak přidejte `<td>` sloupec blíží ke konci šablonu k vykreslení `@item.Rating` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="21af4-160">Then add a `<td>` column near the end of the template to render the `@item.Rating` value.</span></span> <span data-ttu-id="21af4-161">Níže je co aktualizované *Index.cshtml* zobrazit šablonu vypadá jako:</span><span class="sxs-lookup"><span data-stu-id="21af4-161">Below is what the updated *Index.cshtml* view template looks like:</span></span>

[!code-cshtml[Main](adding-a-new-field/samples/sample8.cshtml?highlight=31-33,52-54)]

<span data-ttu-id="21af4-162">Dále otevřete *\Views\Movies\Create.cshtml* a přidejte `Rating` pole s následující highlighed značky.</span><span class="sxs-lookup"><span data-stu-id="21af4-162">Next, open the *\Views\Movies\Create.cshtml* file and add the `Rating` field with the following highlighed markup.</span></span> <span data-ttu-id="21af4-163">Tím zkopírujete textové pole tak, aby hodnocení můžete zadat, když se vytvoří nová videa.</span><span class="sxs-lookup"><span data-stu-id="21af4-163">This renders a text box so that you can specify a rating when a new movie is created.</span></span>

[!code-cshtml[Main](adding-a-new-field/samples/sample9.cshtml?highlight=9-15)]

<span data-ttu-id="21af4-164">Teď když jste aktualizovali kód aplikace pro podporu nového `Rating` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="21af4-164">You've now updated the application code to support the new `Rating` property.</span></span>

<span data-ttu-id="21af4-165">Spusťte aplikaci a přejděte */Movies* adresy URL.</span><span class="sxs-lookup"><span data-stu-id="21af4-165">Run the application and navigate to the */Movies* URL.</span></span> <span data-ttu-id="21af4-166">Když toto provedete, ale zobrazí se vám některé z následujících chyb:</span><span class="sxs-lookup"><span data-stu-id="21af4-166">When you do this, though, you'll see one of the following errors:</span></span>

![](adding-a-new-field/_static/image9.png)  
  
<span data-ttu-id="21af4-167">Model zálohování kontextu 'MovieDBContext' byl změněn, protože byla vytvořena databáze.</span><span class="sxs-lookup"><span data-stu-id="21af4-167">The model backing the 'MovieDBContext' context has changed since the database was created.</span></span> <span data-ttu-id="21af4-168">Zvažte použití migrace Code First k aktualizaci databáze (https://go.microsoft.com/fwlink/?LinkId=238269).</span><span class="sxs-lookup"><span data-stu-id="21af4-168">Consider using Code First Migrations to update the database (https://go.microsoft.com/fwlink/?LinkId=238269).</span></span>

![](adding-a-new-field/_static/image10.png)

<span data-ttu-id="21af4-169">Tato chyba se zobrazuje, protože aktualizovaný `Movie` třídy modelu v aplikaci je nyní liší od schématu `Movie` tabulky existující databáze.</span><span class="sxs-lookup"><span data-stu-id="21af4-169">You're seeing this error because the updated `Movie` model class in the application is now different than the schema of the `Movie` table of the existing database.</span></span> <span data-ttu-id="21af4-170">(Neexistuje žádný `Rating` sloupec v tabulce databáze.)</span><span class="sxs-lookup"><span data-stu-id="21af4-170">(There's no `Rating` column in the database table.)</span></span>


<span data-ttu-id="21af4-171">Řešení chyby několika způsoby:</span><span class="sxs-lookup"><span data-stu-id="21af4-171">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="21af4-172">Máte rozhraní Entity Framework automaticky vyřadit a znovu vytvořit databázi založené na nové schéma třídy modelu.</span><span class="sxs-lookup"><span data-stu-id="21af4-172">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="21af4-173">Tento přístup je velmi vhodné v rané fázi vývojového cyklu při provádění testu databáze; s aktivním vývojem umožňuje rychlý rozvoj schématu modelu a databáze společně.</span><span class="sxs-lookup"><span data-stu-id="21af4-173">This approach is very convenient early in the development cycle when you are doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="21af4-174">Nevýhodou, je však dojít ke ztrátě existujících dat v databázi, tak můžete *není* chcete použít tento postup u provozní databáze!</span><span class="sxs-lookup"><span data-stu-id="21af4-174">The downside, though, is that you lose existing data in the database — so you *don't* want to use this approach on a production database!</span></span> <span data-ttu-id="21af4-175">Použití inicializátoru automaticky naplnit databázi daty testu je často produktivní způsob, jak vyvíjet aplikace.</span><span class="sxs-lookup"><span data-stu-id="21af4-175">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="21af4-176">Další informace o databázi inicializátory Entity Framework naleznete v tématu [kurz ASP.NET MVC a Entity Framework](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="21af4-176">For more information on Entity Framework database initializers, see [ASP.NET MVC/Entity Framework tutorial](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span>
2. <span data-ttu-id="21af4-177">Explicitně upravte schéma stávající databázi tak, aby odpovídalo tříd modelu.</span><span class="sxs-lookup"><span data-stu-id="21af4-177">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="21af4-178">Výhodou tohoto přístupu je, že zachováte vaše data.</span><span class="sxs-lookup"><span data-stu-id="21af4-178">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="21af4-179">Můžete tuto změnu provést buď ručně, nebo tak, že vytvoříte databázi změnit skript.</span><span class="sxs-lookup"><span data-stu-id="21af4-179">You can make this change either manually or by creating a database change script.</span></span>
3. <span data-ttu-id="21af4-180">Pomocí migrace Code First aktualizovat schéma databáze.</span><span class="sxs-lookup"><span data-stu-id="21af4-180">Use Code First Migrations to update the database schema.</span></span>


<span data-ttu-id="21af4-181">Pro účely tohoto kurzu používáme migrace Code First.</span><span class="sxs-lookup"><span data-stu-id="21af4-181">For this tutorial, we'll use Code First Migrations.</span></span>

<span data-ttu-id="21af4-182">Aktualizujte metodu počáteční hodnoty tak, aby poskytuje hodnoty pro nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="21af4-182">Update the Seed method so that it provides a value for the new column.</span></span> <span data-ttu-id="21af4-183">Otevřete soubor Migrations\Configuration.cs a přidat hodnocení pole do každé video.</span><span class="sxs-lookup"><span data-stu-id="21af4-183">Open Migrations\Configuration.cs file and add a Rating field to each Movie object.</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample10.cs?highlight=6)]

<span data-ttu-id="21af4-184">Sestavte řešení a pak otevřete **Konzola správce balíčků** okna a zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="21af4-184">Build the solution, and then open the **Package Manager Console** window and enter the following command:</span></span>

`add-migration Rating`

<span data-ttu-id="21af4-185">`add-migration` Příkaz říká rozhraní framework migrace sloužící ke zkoumání aktuální model filmů s aktuální schéma databáze filmů a vytvářet kód potřebné k migraci databáze na nový model.</span><span class="sxs-lookup"><span data-stu-id="21af4-185">The `add-migration` command tells the migration framework to examine the current movie model with the current movie DB schema and create the necessary code to migrate the DB to the new model.</span></span> <span data-ttu-id="21af4-186">Název *hodnocení* je volitelný a slouží k pojmenování souboru migrace.</span><span class="sxs-lookup"><span data-stu-id="21af4-186">The name *Rating* is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="21af4-187">Je vhodné použít smysluplný název kroku migrace.</span><span class="sxs-lookup"><span data-stu-id="21af4-187">It's helpful to use a meaningful name for the migration step.</span></span>

<span data-ttu-id="21af4-188">Po dokončení tohoto příkazu, Visual Studio otevře soubor třídy, která definuje nový `DbMigration` odvozené třídy a `Up` metoda se zobrazí kód, který vytvoří nový sloupec.</span><span class="sxs-lookup"><span data-stu-id="21af4-188">When this command finishes, Visual Studio opens the class file that defines the new `DbMigration` derived class, and in the `Up` method you can see the code that creates the new column.</span></span>

[!code-csharp[Main](adding-a-new-field/samples/sample11.cs)]

<span data-ttu-id="21af4-189">Sestavte řešení a pak zadejte `update-database` v příkaz **Konzola správce balíčků** okna.</span><span class="sxs-lookup"><span data-stu-id="21af4-189">Build the solution, and then enter the `update-database` command in the **Package Manager Console** window.</span></span>

<span data-ttu-id="21af4-190">Následující obrázek znázorňuje výstup v **Konzola správce balíčků** okno (datum razítko předřazení *hodnocení* se bude lišit.)</span><span class="sxs-lookup"><span data-stu-id="21af4-190">The following image shows the output in the **Package Manager Console** window (The date stamp prepending *Rating* will be different.)</span></span>

![](adding-a-new-field/_static/image11.png)

<span data-ttu-id="21af4-191">Znovu spusťte aplikaci a přejděte na adresu URL /Movies.</span><span class="sxs-lookup"><span data-stu-id="21af4-191">Re-run the application and navigate to the /Movies URL.</span></span> <span data-ttu-id="21af4-192">Uvidíte nové pole hodnocení.</span><span class="sxs-lookup"><span data-stu-id="21af4-192">You can see the new Rating field.</span></span>

![](adding-a-new-field/_static/image12.png)

<span data-ttu-id="21af4-193">Klikněte na tlačítko **vytvořit nový** odkaz na přidání nového videa.</span><span class="sxs-lookup"><span data-stu-id="21af4-193">Click the **Create New** link to add a new movie.</span></span> <span data-ttu-id="21af4-194">Všimněte si, že můžete přidat hodnocení.</span><span class="sxs-lookup"><span data-stu-id="21af4-194">Note that you can add a rating.</span></span>

![7_CreateRioII](adding-a-new-field/_static/image13.png)

<span data-ttu-id="21af4-196">Klikněte na možnost **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="21af4-196">Click **Create**.</span></span> <span data-ttu-id="21af4-197">Tento nový film, včetně hodnocení, zobrazí se nově ve výpisu:</span><span class="sxs-lookup"><span data-stu-id="21af4-197">The new movie, including the rating, now shows up in the movies listing:</span></span>

![7_ourNewMovie_SM](adding-a-new-field/_static/image14.png)

<span data-ttu-id="21af4-199">Teď, když projekt používá migrace, nebudete muset odpojení databáze při přidání nového pole nebo jinak aktualizovat schéma.</span><span class="sxs-lookup"><span data-stu-id="21af4-199">Now that the project is using migrations, you won't need to drop the database when you add a new field or otherwise update the schema.</span></span> <span data-ttu-id="21af4-200">V další části jsme budete provádět další změny schématu a použití migrace k aktualizaci databáze.</span><span class="sxs-lookup"><span data-stu-id="21af4-200">In the next section, we'll make more schema changes and use migrations to update the database.</span></span>

<span data-ttu-id="21af4-201">Měli byste také přidat `Rating` pole k zobrazení šablony upravit, podrobnosti a Delete.</span><span class="sxs-lookup"><span data-stu-id="21af4-201">You should also add the `Rating` field to the Edit, Details, and Delete view templates.</span></span>

<span data-ttu-id="21af4-202">Můžete například zadat příkaz "aktualizace databáze" v **Konzola správce balíčků** znovu okno a žádný kód migrace by spustit, protože schéma odpovídá schématu modelu.</span><span class="sxs-lookup"><span data-stu-id="21af4-202">You could enter the "update-database" command in the **Package Manager Console** window again and no migration code would run, because the schema matches the model.</span></span> <span data-ttu-id="21af4-203">Však bude fungovat s "aktualizace databází" `Seed` metoda znovu, a pokud jste změnili počáteční hodnoty dat, změny budou ztraceny, protože `Seed` metoda upsertuje data.</span><span class="sxs-lookup"><span data-stu-id="21af4-203">However, running "update-database" will run the `Seed` method again, and if you changed any of the Seed data, the changes will be lost because the `Seed` method upserts data.</span></span> <span data-ttu-id="21af4-204">Další informace o `Seed` metoda v Tom Dykstra oblíbených [kurz ASP.NET MVC a Entity Framework](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="21af4-204">You can read more about the `Seed` method in Tom Dykstra's popular [ASP.NET MVC/Entity Framework tutorial](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).</span></span>

<span data-ttu-id="21af4-205">V této části jste viděli, jak můžete upravit objekty modelu a udržovat synchronizované s změny databáze.</span><span class="sxs-lookup"><span data-stu-id="21af4-205">In this section you saw how you can modify model objects and keep the database in sync with the changes.</span></span> <span data-ttu-id="21af4-206">Také jste se naučili způsob, jak naplnit nově vytvořenou databázi s ukázkovými daty, takže si můžete vyzkoušet scénáře.</span><span class="sxs-lookup"><span data-stu-id="21af4-206">You also learned a way to populate a newly created database with sample data so you can try out scenarios.</span></span> <span data-ttu-id="21af4-207">To je jenom rychlý úvod k Code First, naleznete v tématu [vytváření datového modelu Entity Framework pro aplikaci ASP.NET MVC](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) úplný kurz týkající se předmětu.</span><span class="sxs-lookup"><span data-stu-id="21af4-207">This was just a quick introduction to Code First, see [Creating an Entity Framework Data Model for an ASP.NET MVC Application](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md) for a more complete tutorial on the subject.</span></span> <span data-ttu-id="21af4-208">V dalším kroku Podívejme se na jak můžete přidat bohatší logiku ověřování na třídy modelu a povolit některé obchodní pravidla, která budou vynucena.</span><span class="sxs-lookup"><span data-stu-id="21af4-208">Next, let's look at how you can add richer validation logic to the model classes and enable some business rules to be enforced.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="21af4-209">[Předchozí](adding-search.md)
> [další](adding-validation.md)</span><span class="sxs-lookup"><span data-stu-id="21af4-209">[Previous](adding-search.md)
[Next](adding-validation.md)</span></span>

---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/deleting-data
title: Představení rozhraní ASP.NET Web Pages – odstranění databázových dat | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto kurzu se dozvíte, jak odstranit položku jednotlivých databází. Předpokládá se, že jste dokončili řady prostřednictvím aktualizace dat z databáze v prostředí ASP.NET Pa...
ms.author: riande
ms.date: 01/02/2018
ms.assetid: 75b5c1cf-84bd-434f-8a86-85c568eb5b09
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/deleting-data
msc.type: authoredcontent
ms.openlocfilehash: b2ef8fcc8cc534bd31fea83bf0b085b85995f417
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020439"
---
<a name="introducing-aspnet-web-pages---deleting-database-data"></a><span data-ttu-id="6e06d-104">Úvod do webových stránek ASP.NET – odstranění databázových dat</span><span class="sxs-lookup"><span data-stu-id="6e06d-104">Introducing ASP.NET Web Pages - Deleting Database Data</span></span>
====================
<span data-ttu-id="6e06d-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="6e06d-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="6e06d-106">V tomto kurzu se dozvíte, jak odstranit položku jednotlivých databází.</span><span class="sxs-lookup"><span data-stu-id="6e06d-106">This tutorial shows you how to delete an individual database entry.</span></span> <span data-ttu-id="6e06d-107">Předpokládá, že jste dokončili řady prostřednictvím [aktualizaci dat z databáze v ASP.NET Web Pages](updating-data.md).</span><span class="sxs-lookup"><span data-stu-id="6e06d-107">It assumes you have completed the series through [Updating Database Data in ASP.NET Web Pages](updating-data.md).</span></span>
> 
> <span data-ttu-id="6e06d-108">Co se dozvíte:</span><span class="sxs-lookup"><span data-stu-id="6e06d-108">What you'll learn:</span></span>
> 
> - <span data-ttu-id="6e06d-109">Jak vybrat jednotlivý záznam v seznamu záznamů.</span><span class="sxs-lookup"><span data-stu-id="6e06d-109">How to select an individual record from a listing of records.</span></span>
> - <span data-ttu-id="6e06d-110">Postup odstranění jednoho záznamu z databáze.</span><span class="sxs-lookup"><span data-stu-id="6e06d-110">How to delete a single record from a database.</span></span>
> - <span data-ttu-id="6e06d-111">Návod k ověření, že bylo na určité tlačítko kliknuto ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="6e06d-111">How to check that a specific button was clicked in a form.</span></span>
>   
> 
> <span data-ttu-id="6e06d-112">Popsané funkce a technologie:</span><span class="sxs-lookup"><span data-stu-id="6e06d-112">Features/technologies discussed:</span></span>
> 
> - <span data-ttu-id="6e06d-113">`WebGrid` Pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6e06d-113">The `WebGrid` helper.</span></span>
> - <span data-ttu-id="6e06d-114">SQL `Delete` příkazu.</span><span class="sxs-lookup"><span data-stu-id="6e06d-114">The SQL `Delete` command.</span></span>
> - <span data-ttu-id="6e06d-115">`Database.Execute` Způsob spuštění SQL `Delete` příkazu.</span><span class="sxs-lookup"><span data-stu-id="6e06d-115">The `Database.Execute` method to run a SQL `Delete` command.</span></span>


## <a name="what-youll-build"></a><span data-ttu-id="6e06d-116">Co budete vytvářet</span><span class="sxs-lookup"><span data-stu-id="6e06d-116">What You'll Build</span></span>

<span data-ttu-id="6e06d-117">V předchozím kurzu jste zjistili, jak aktualizovat stávající záznam v databázi.</span><span class="sxs-lookup"><span data-stu-id="6e06d-117">In the previous tutorial, you learned how to update an existing database record.</span></span> <span data-ttu-id="6e06d-118">Tento kurz je podobné, s tím rozdílem, že místo aktualizace záznamu, budete ho odstranit.</span><span class="sxs-lookup"><span data-stu-id="6e06d-118">This tutorial is similar, except that instead of updating the record, you'll delete it.</span></span> <span data-ttu-id="6e06d-119">Procesy, které jsou podobné, s tím rozdílem, že odstranění je jednodušší, takže v tomto kurzu bude krátký.</span><span class="sxs-lookup"><span data-stu-id="6e06d-119">The processes are much the same, except that deleting is simpler, so this tutorial will be short.</span></span>

<span data-ttu-id="6e06d-120">V *filmy* stránky, budete aktualizovat `WebGrid` pomocné rutiny, takže se zobrazí **odstranit** odkaz vedle každý film vyvíjený **upravit** propojení, které jste přidali dříve.</span><span class="sxs-lookup"><span data-stu-id="6e06d-120">In the *Movies* page, you'll update the `WebGrid` helper so that it displays a **Delete** link next to each movie to accompany the **Edit** link you added earlier.</span></span>

![Zobrazuje odkaz pro odstranění pro každý film stránky filmy](deleting-data/_static/image1.png)

<span data-ttu-id="6e06d-122">Stejně jako u úpravy, když kliknete **odstranit** odkaz, tím přejdete na jinou stránku, kde je video informace již ve formě:</span><span class="sxs-lookup"><span data-stu-id="6e06d-122">As with editing, when you click the **Delete** link, it takes you to a different page, where the movie information is already in a form:</span></span>

![Odstranit stránku film se video zobrazí](deleting-data/_static/image2.png)

<span data-ttu-id="6e06d-124">Klikněte na tlačítko se trvale odstranit záznam.</span><span class="sxs-lookup"><span data-stu-id="6e06d-124">You can then click the button to delete the record permanently.</span></span>

## <a name="adding-a-delete-link-to-the-movie-listing"></a><span data-ttu-id="6e06d-125">Přidat odkaz pro odstranění výpis Movie</span><span class="sxs-lookup"><span data-stu-id="6e06d-125">Adding a Delete Link to the Movie Listing</span></span>

<span data-ttu-id="6e06d-126">Začnete tak, že přidáte **odstranit** propojit `WebGrid` pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="6e06d-126">You'll start by adding a **Delete** link to the `WebGrid` helper.</span></span> <span data-ttu-id="6e06d-127">Tento odkaz je podobný **upravit** propojení, které jste přidali v předchozím kurzu.</span><span class="sxs-lookup"><span data-stu-id="6e06d-127">This link is similar to the **Edit** link you added in a previous tutorial.</span></span>

<span data-ttu-id="6e06d-128">Otevřít *Movies.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="6e06d-128">Open the *Movies.cshtml* file.</span></span>

<span data-ttu-id="6e06d-129">Změnit `WebGrid` kód v těle stránky tak, že přidáte sloupec.</span><span class="sxs-lookup"><span data-stu-id="6e06d-129">Change the `WebGrid` markup in the body of the page by adding a column.</span></span> <span data-ttu-id="6e06d-130">Tady je upravený kód:</span><span class="sxs-lookup"><span data-stu-id="6e06d-130">Here's the modified markup:</span></span>

[!code-html[Main](deleting-data/samples/sample1.html?highlight=9-10)]

<span data-ttu-id="6e06d-131">Nový sloupec se tohle:</span><span class="sxs-lookup"><span data-stu-id="6e06d-131">The new column is this one:</span></span>

[!code-html[Main](deleting-data/samples/sample2.html)]

<span data-ttu-id="6e06d-132">Tak, jak je nakonfigurovaný mřížky, **upravit** sloupec je úplně vlevo v mřížce a **odstranit** sloupec je úplně vpravo.</span><span class="sxs-lookup"><span data-stu-id="6e06d-132">The way the grid is configured, the **Edit** column is leftmost in the grid and the **Delete** column is rightmost.</span></span> <span data-ttu-id="6e06d-133">(Není za čárka `Year` sloupec, v případě, že nebyl Všimněte si, že.) Není nic zvláštního kde tyto sloupce odkaz přejděte a by snadno mohlo vedle sebe.</span><span class="sxs-lookup"><span data-stu-id="6e06d-133">(There's a comma after the `Year` column now, in case you didn't notice that.) There's nothing special about where these link columns go, and you could as easily put them next to each other.</span></span> <span data-ttu-id="6e06d-134">V tomto případě jsou samostatné znesnadnit Smíchat se jim.</span><span class="sxs-lookup"><span data-stu-id="6e06d-134">In this case, they're separate to make them harder to get mixed up.</span></span>

![Označené filmy stránka s odkazy pro úpravy a podrobnosti zobrazíte, že nejsou vedle sebe](deleting-data/_static/image3.png)

<span data-ttu-id="6e06d-136">Nový sloupec zobrazuje spojení (`<a>` element) jejichž text říká "Odstranit".</span><span class="sxs-lookup"><span data-stu-id="6e06d-136">The new column shows a link (`<a>` element) whose text says "Delete".</span></span> <span data-ttu-id="6e06d-137">Cíl odkazu (jeho `href` atribut) je kód, který se s podobným tuto adresu URL, takže v konečném důsledku přeloží `id` hodnotu pro každý film:</span><span class="sxs-lookup"><span data-stu-id="6e06d-137">The target of the link (its `href` attribute) is code that ultimately resolves to something like this URL, with the `id` value different for each movie:</span></span>

[!code-css[Main](deleting-data/samples/sample3.css)]

<span data-ttu-id="6e06d-138">Tento odkaz se vyvolat stránku s názvem *DeleteMovie* a předejte jí ID videa, které jste vybrali.</span><span class="sxs-lookup"><span data-stu-id="6e06d-138">This link will invoke a page named *DeleteMovie* and pass it the ID of the movie you've selected.</span></span>

<span data-ttu-id="6e06d-139">V tomto kurzu nezkoumá podrobnosti o tom, jak je vytvořen tento odkaz, protože je téměř stejný jako **upravit** odkaz z předchozí kurz o službě ([aktualizaci dat z databáze v ASP.NET Web Pages](updating-data.md)).</span><span class="sxs-lookup"><span data-stu-id="6e06d-139">This tutorial won't go into detail about how this link is constructed, because it's almost identical to the **Edit** link from the previous tutorial ([Updating Database Data in ASP.NET Web Pages](updating-data.md)).</span></span>

## <a name="creating-the-delete-page"></a><span data-ttu-id="6e06d-140">Vytvoření stránky Delete</span><span class="sxs-lookup"><span data-stu-id="6e06d-140">Creating the Delete Page</span></span>

<span data-ttu-id="6e06d-141">Nyní můžete vytvořit stránku, která budou cílem pro **odstranit** odkaz v mřížce.</span><span class="sxs-lookup"><span data-stu-id="6e06d-141">Now you can create the page that will be the target for the **Delete** link in the grid.</span></span>

> [!NOTE] 
> 
> <span data-ttu-id="6e06d-142">**Důležité** technika nejprve výběru záznam odstranit a pak používá samostatné stránky a tlačítko potvrďte procesu je velmi důležité pro zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="6e06d-142">**Important** The technique of first selecting a record to delete and then using a separate page and button to confirm the process is extremely important for security.</span></span> <span data-ttu-id="6e06d-143">Protože jste si přečetli v předchozích kurzech, což *všechny* druh změn na váš web by měl *vždy* udělat pomocí formuláře &mdash; použití operace HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="6e06d-143">As you've read in previous tutorials, making *any* sort of change to your website should *always* be done using a form &mdash; that is, using an HTTP POST operation.</span></span> <span data-ttu-id="6e06d-144">Pokud jste provedli možné změnit webu jenom klepnutím na odkaz (tj. pomocí operace GET), lidí může provádět jednoduché požadavky vašeho webu a odstraňovat data.</span><span class="sxs-lookup"><span data-stu-id="6e06d-144">If you made it possible to change the site just by clicking a link (that is, using a GET operation), people could make simple requests to your site and delete your data.</span></span> <span data-ttu-id="6e06d-145">Dokonce i vyhledávací web prohledávací modul, který je indexování webu může omylem odstraníte data, stačí získat prostřednictvím následujících odkazů.</span><span class="sxs-lookup"><span data-stu-id="6e06d-145">Even a search-engine crawler that's indexing your site could inadvertently delete data just by following links.</span></span>
> 
> <span data-ttu-id="6e06d-146">Aplikace umožňuje uživatelům změnit záznam, máte k dispozici záznam uživatele pro úpravy přesto.</span><span class="sxs-lookup"><span data-stu-id="6e06d-146">When your app lets people change a record, you have to present the record to the user for editing anyway.</span></span> <span data-ttu-id="6e06d-147">Ale můžete mít tendenci Přeskočit tento krok pro odstranění záznamu.</span><span class="sxs-lookup"><span data-stu-id="6e06d-147">But you might be tempted to skip this step for deleting a record.</span></span> <span data-ttu-id="6e06d-148">Tento krok, není ale přeskočit.</span><span class="sxs-lookup"><span data-stu-id="6e06d-148">Don't skip that step, though.</span></span> <span data-ttu-id="6e06d-149">(Je také užitečné, uživatelé si můžou zobrazit záznam a potvrďte, že se při odstraňování záznamu, která jsou určena.)</span><span class="sxs-lookup"><span data-stu-id="6e06d-149">(It's also helpful for users to see the record and confirm that they're deleting the record that they intended.)</span></span>
> 
> <span data-ttu-id="6e06d-150">V následující sérii kurzů uvidíte, jak přidat funkce přihlášení, aby uživatel musel přihlášení před odstraněním záznamu.</span><span class="sxs-lookup"><span data-stu-id="6e06d-150">In a subsequent tutorial set, you'll see how to add login functionality so a user would have to log in before deleting a record.</span></span>


<span data-ttu-id="6e06d-151">Vytvoření stránky s názvem *DeleteMovie.cshtml* a nahradit, co je v souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="6e06d-151">Create a page named *DeleteMovie.cshtml* and replace what's in the file with the following markup:</span></span>

[!code-cshtml[Main](deleting-data/samples/sample4.cshtml)]

<span data-ttu-id="6e06d-152">Tento kód je jako *EditMovie* stránky, s výjimkou, že místo použití polí (`<input type="text">`), obsahuje kód `<span>` elementy.</span><span class="sxs-lookup"><span data-stu-id="6e06d-152">This markup is like the *EditMovie* pages, except that instead of using text boxes (`<input type="text">`), the markup includes `<span>` elements.</span></span> <span data-ttu-id="6e06d-153">Není nutné nic tady upravit.</span><span class="sxs-lookup"><span data-stu-id="6e06d-153">There's nothing here to edit.</span></span> <span data-ttu-id="6e06d-154">Všechno, co musíte udělat, je zobrazit podrobnosti o filmu mohou uživatelé provádět jistotu, že se při odstraňování správné videa.</span><span class="sxs-lookup"><span data-stu-id="6e06d-154">All you have to do is display the movie details so that users can make sure that they're deleting the right movie.</span></span>

<span data-ttu-id="6e06d-155">Značky již obsahuje odkaz, který umožňuje uživateli vrátit na stránku výpis video.</span><span class="sxs-lookup"><span data-stu-id="6e06d-155">The markup already contains a link that lets the user return to the movie listing page.</span></span>

<span data-ttu-id="6e06d-156">Stejně jako *EditMovie* stránky, ID vybraného videa je uložená ve skrytém poli.</span><span class="sxs-lookup"><span data-stu-id="6e06d-156">As in the *EditMovie* page, the ID of the selected movie is stored in a hidden field.</span></span> <span data-ttu-id="6e06d-157">(Je předána do stránky na prvním místě jako hodnotu řetězce dotazu.) Je `Html.ValidationSummary` volání, které se zobrazí chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="6e06d-157">(It's passed into the page in the first place as a query string value.) There's an `Html.ValidationSummary` call that will display validation errors.</span></span> <span data-ttu-id="6e06d-158">V takovém případě chyba může být, že žádné ID film byla předána na stránku nebo že film ID je neplatné.</span><span class="sxs-lookup"><span data-stu-id="6e06d-158">In this case, the error might be that no movie ID was passed to the page or that the movie ID is invalid.</span></span> <span data-ttu-id="6e06d-159">Tato situace může nastat, pokud někdo běžel na této stránce bez první video ve výběru *filmy* stránky.</span><span class="sxs-lookup"><span data-stu-id="6e06d-159">This situation could occur if someone ran this page without first selecting a movie in the *Movies* page.</span></span>

<span data-ttu-id="6e06d-160">Popisek tlačítka je **video odstranit**, a jeho název atributu je nastavena na `buttonDelete`.</span><span class="sxs-lookup"><span data-stu-id="6e06d-160">The button caption is **Delete Movie**, and its name attribute is set to `buttonDelete`.</span></span> <span data-ttu-id="6e06d-161">`name` Atributu se použije v kódu k identifikaci tlačítko odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="6e06d-161">The `name` attribute will be used in the code to identify the button that submitted the form.</span></span>

<span data-ttu-id="6e06d-162">Budete muset psát kód 1) přečíst podrobnosti o filmu při prvním zobrazení stránky a (2) ve skutečnosti odstranit videa, když uživatel klikne na tlačítko.</span><span class="sxs-lookup"><span data-stu-id="6e06d-162">You'll have to write code to 1) read the movie details when the page is first displayed and 2) actually delete the movie when the user clicks the button.</span></span>

## <a name="adding-code-to-read-a-single-movie"></a><span data-ttu-id="6e06d-163">Přidání kódu pro čtení jednoho videa</span><span class="sxs-lookup"><span data-stu-id="6e06d-163">Adding Code to Read a Single Movie</span></span>

<span data-ttu-id="6e06d-164">V horní části *DeleteMovie.cshtml* stránce, přidejte následující blok kódu:</span><span class="sxs-lookup"><span data-stu-id="6e06d-164">At the top of the *DeleteMovie.cshtml* page, add the following code block:</span></span>

[!code-cshtml[Main](deleting-data/samples/sample5.cshtml)]

<span data-ttu-id="6e06d-165">Tento kód je stejné jako odpovídající kód v *EditMovie* stránky.</span><span class="sxs-lookup"><span data-stu-id="6e06d-165">This markup is the same as the corresponding code in the *EditMovie* page.</span></span> <span data-ttu-id="6e06d-166">Získá ID film mimo řetězec dotazu a používá ID číst záznam z databáze.</span><span class="sxs-lookup"><span data-stu-id="6e06d-166">It gets the movie ID out of the query string and uses the ID to read a record from the database.</span></span> <span data-ttu-id="6e06d-167">Tento kód obsahuje ověřovací test (`IsInt()` a `row != null`) a ujistěte, že ID film předávaný do stránky je platný.</span><span class="sxs-lookup"><span data-stu-id="6e06d-167">The code includes the validation test (`IsInt()` and `row != null`) to make sure that the movie ID being passed to the page is valid.</span></span>

<span data-ttu-id="6e06d-168">Mějte na paměti, že tento kód by měl spustit pouze při prvním spuštění stránky.</span><span class="sxs-lookup"><span data-stu-id="6e06d-168">Remember that this code should only run the first time the page runs.</span></span> <span data-ttu-id="6e06d-169">Chcete znovu načíst video záznam z databáze, když uživatel klikne **video odstranit** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="6e06d-169">You don't want to re-read the movie record from the database when the user clicks the **Delete Movie** button.</span></span> <span data-ttu-id="6e06d-170">Proto kód ke čtení, video se nachází uvnitř test, který říká `if(!IsPost)` &mdash; tedy *Pokud žádost není operace post (odeslání formuláře)*.</span><span class="sxs-lookup"><span data-stu-id="6e06d-170">Therefore, code to read the movie is inside a test that says `if(!IsPost)` &mdash; that is, *if the request is not a post operation (form submission)*.</span></span>

## <a name="adding-code-to-delete-the-selected-movie"></a><span data-ttu-id="6e06d-171">Přidání kódu k odstranění vybraného videa</span><span class="sxs-lookup"><span data-stu-id="6e06d-171">Adding Code to Delete the Selected Movie</span></span>

<span data-ttu-id="6e06d-172">Odstranit video, když uživatel klikne na tlačítko, přidejte následující kód pouze uvnitř složené závorce `@` blok:</span><span class="sxs-lookup"><span data-stu-id="6e06d-172">To delete the movie when the user clicks the button, add the following code just inside the closing brace of the `@` block:</span></span>

[!code-csharp[Main](deleting-data/samples/sample6.cs)]

<span data-ttu-id="6e06d-173">Tento kód je podobný kód pro aktualizaci existující záznam, ale jednodušší.</span><span class="sxs-lookup"><span data-stu-id="6e06d-173">This code is similar to the code for updating an existing record, but simpler.</span></span> <span data-ttu-id="6e06d-174">Kód se spustí v podstatě SQL `Delete` příkazu.</span><span class="sxs-lookup"><span data-stu-id="6e06d-174">The code basically runs a SQL `Delete` statement.</span></span>

 <span data-ttu-id="6e06d-175">Stejně jako v *EditMovie* stránky, kód je v `if(IsPost)` bloku.</span><span class="sxs-lookup"><span data-stu-id="6e06d-175">As in the *EditMovie* page, the code is in an `if(IsPost)` block.</span></span> <span data-ttu-id="6e06d-176">Tentokrát `if()` je o něco složitější podmínky:</span><span class="sxs-lookup"><span data-stu-id="6e06d-176">This time, the `if()` condition is a little more complicated:</span></span> 

[!code-csharp[Main](deleting-data/samples/sample7.cs)]

<span data-ttu-id="6e06d-177">Existují dvě podmínky tady.</span><span class="sxs-lookup"><span data-stu-id="6e06d-177">There are two conditions here.</span></span> <span data-ttu-id="6e06d-178">První možností je, že na stránce se právě odesílá, jak už víte, před &mdash; `if(IsPost)`.</span><span class="sxs-lookup"><span data-stu-id="6e06d-178">The first is that the page is being submitted, as you've seen before &mdash; `if(IsPost)`.</span></span>

<span data-ttu-id="6e06d-179">Druhá podmínka je `!Request["buttonDelete"].IsEmpty()`, což znamená, že žádost má objekt s názvem `buttonDelete`.</span><span class="sxs-lookup"><span data-stu-id="6e06d-179">The second condition is `!Request["buttonDelete"].IsEmpty()`, meaning that the request has an object named `buttonDelete`.</span></span> <span data-ttu-id="6e06d-180">Admittedly je to nepřímé způsob testování, které tlačítko odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="6e06d-180">Admittedly, it's an indirect way of testing which button submitted the form.</span></span> <span data-ttu-id="6e06d-181">Pokud formulář obsahuje více tlačítka pro odeslání dat, zobrazí se pouze název tlačítka, které došlo ke kliknutí na v požadavku.</span><span class="sxs-lookup"><span data-stu-id="6e06d-181">If a form contains multiple submit buttons, only the name of the button that was clicked appears in the request.</span></span> <span data-ttu-id="6e06d-182">Proto, logicky Pokud se zobrazí název konkrétního tlačítka v požadavku &mdash; nebo jak je uvedeno v kódu, pokud toto tlačítko není prázdný &mdash; , který je tlačítko, které odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="6e06d-182">Therefore, logically, if the name of a particular button appears in the request &mdash; or as stated in the code, if that button isn't empty &mdash; that's the button that submitted the form.</span></span>

<span data-ttu-id="6e06d-183">`&&` Znamená, že operátor "a" (logický operátor a).</span><span class="sxs-lookup"><span data-stu-id="6e06d-183">The `&&` operator means "and" (logical AND).</span></span> <span data-ttu-id="6e06d-184">Proto celý `if` podmínka je...</span><span class="sxs-lookup"><span data-stu-id="6e06d-184">Therefore the entire `if` condition is ...</span></span>

<span data-ttu-id="6e06d-185">*Tento požadavek je příspěvek (ne první žádost)*</span><span class="sxs-lookup"><span data-stu-id="6e06d-185">*This request is a post (not a first-time request)*</span></span>  
  
 <span data-ttu-id="6e06d-186">AND</span><span class="sxs-lookup"><span data-stu-id="6e06d-186">AND</span></span>  
  
<span data-ttu-id="6e06d-187">*`buttonDelete`Tlačítko* *byl tlačítko odeslání formuláře.*</span><span class="sxs-lookup"><span data-stu-id="6e06d-187">*The* `buttonDelete`*button was the button that submitted the form.*</span></span>

<span data-ttu-id="6e06d-188">Tento formulář (ve skutečnosti, na této stránce) obsahuje pouze jedno tlačítko, takže další test `buttonDelete` není technicky povinný.</span><span class="sxs-lookup"><span data-stu-id="6e06d-188">This form (in fact, this page) contains only one button, so the additional test for `buttonDelete` is technically not required.</span></span> <span data-ttu-id="6e06d-189">Stále Chystáte se provést operaci, která se trvale odeberou data.</span><span class="sxs-lookup"><span data-stu-id="6e06d-189">Still, you're about to perform an operation that will permanently remove data.</span></span> <span data-ttu-id="6e06d-190">Proto má abyste měli jistotu, jako je to možné, že při provádění operace pouze v případě, že uživatel explicitně požaduje ho.</span><span class="sxs-lookup"><span data-stu-id="6e06d-190">So you want to be as sure as possible that you're performing the operation only when the user has explicitly requested it.</span></span> <span data-ttu-id="6e06d-191">Předpokládejme například, zvětšit později na této stránce a do ní přidat další tlačítka.</span><span class="sxs-lookup"><span data-stu-id="6e06d-191">For example, suppose that you expanded this page later and added other buttons to it.</span></span> <span data-ttu-id="6e06d-192">Dokonce i pak, kód, který odstraní videa se spustí jenom v případě, `buttonDelete` došlo ke kliknutí na tlačítko.</span><span class="sxs-lookup"><span data-stu-id="6e06d-192">Even then, the code that deletes the movie will run only if the `buttonDelete` button was clicked.</span></span>

<span data-ttu-id="6e06d-193">Stejně jako *EditMovie* stránky, získejte ID z skryté pole a poté spusťte příkaz SQL.</span><span class="sxs-lookup"><span data-stu-id="6e06d-193">As in the *EditMovie* page, you get the ID from the hidden field and then run the SQL command.</span></span> <span data-ttu-id="6e06d-194">Syntaxe `Delete` příkaz je:</span><span class="sxs-lookup"><span data-stu-id="6e06d-194">The syntax for the `Delete` statement is:</span></span>

`DELETE FROM table WHERE ID = value`

<span data-ttu-id="6e06d-195">Je důležité zahrnout `WHERE` klauzule a ID.</span><span class="sxs-lookup"><span data-stu-id="6e06d-195">It's vital to include the `WHERE` clause and the ID.</span></span> <span data-ttu-id="6e06d-196">Pokud vynecháte klauzule WHERE *se odstraní všechny záznamy v tabulce*.</span><span class="sxs-lookup"><span data-stu-id="6e06d-196">If you leave out the WHERE clause, *all the records in the table will be deleted*.</span></span> <span data-ttu-id="6e06d-197">Protože jste viděli, předejte hodnotu ID příkazu SQL s použitím zástupného symbolu.</span><span class="sxs-lookup"><span data-stu-id="6e06d-197">As you have seen, you pass the ID value to the SQL command by using a placeholder.</span></span>

## <a name="testing-the-movie-delete-process"></a><span data-ttu-id="6e06d-198">Testování procesu odstranění Movie</span><span class="sxs-lookup"><span data-stu-id="6e06d-198">Testing the Movie Delete Process</span></span>

<span data-ttu-id="6e06d-199">Nyní můžete otestovat.</span><span class="sxs-lookup"><span data-stu-id="6e06d-199">Now you can test.</span></span> <span data-ttu-id="6e06d-200">Spustit *filmy* stránce a klikněte na tlačítko **odstranit** vedle videa.</span><span class="sxs-lookup"><span data-stu-id="6e06d-200">Run the *Movies* page, and click **Delete** next to a movie.</span></span> <span data-ttu-id="6e06d-201">Když *DeleteMovie* stránky se zobrazí, klikněte na tlačítko **video odstranit**.</span><span class="sxs-lookup"><span data-stu-id="6e06d-201">When the *DeleteMovie* page appears, click **Delete Movie**.</span></span>

![Odstranit stránku film se zvýrazněným tlačítkem odstranit video](deleting-data/_static/image4.png)

<span data-ttu-id="6e06d-203">Když kliknete na tlačítko, kód odstraní videa a vrátí na výpis video.</span><span class="sxs-lookup"><span data-stu-id="6e06d-203">When you click the button, the code deletes the movies and returns to the movie listing.</span></span> <span data-ttu-id="6e06d-204">Existuje vyhledejte odstraněné filmů a potvrďte, že byla odstraněna.</span><span class="sxs-lookup"><span data-stu-id="6e06d-204">There you can search for the deleted movie and confirm that it's been deleted.</span></span>

## <a name="coming-up-next"></a><span data-ttu-id="6e06d-205">Chystá se další</span><span class="sxs-lookup"><span data-stu-id="6e06d-205">Coming Up Next</span></span>

<span data-ttu-id="6e06d-206">V dalším kurzu se dozvíte, jak poskytnout všechny stránky na webu běžné vzhledu a rozložení.</span><span class="sxs-lookup"><span data-stu-id="6e06d-206">The next tutorial shows you how to give all the pages on your site a common look and layout.</span></span>

## <a name="complete-listing-for-movie-page-updated-with-delete-links"></a><span data-ttu-id="6e06d-207">Úplný seznam pro stránku Movie (aktualizován odstranění odkazů)</span><span class="sxs-lookup"><span data-stu-id="6e06d-207">Complete Listing for Movie Page (Updated with Delete Links)</span></span>

[!code-cshtml[Main](deleting-data/samples/sample8.cshtml)]

## <a name="complete-listing-for-deletemovie-page"></a><span data-ttu-id="6e06d-208">Úplný seznam DeleteMovie stránky</span><span class="sxs-lookup"><span data-stu-id="6e06d-208">Complete Listing for DeleteMovie Page</span></span>

[!code-cshtml[Main](deleting-data/samples/sample9.cshtml)]

## <a name="additional-resources"></a><span data-ttu-id="6e06d-209">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="6e06d-209">Additional Resources</span></span>

- [<span data-ttu-id="6e06d-210">Úvod k programování v prostředí ASP.NET pomocí syntaxe Razor</span><span class="sxs-lookup"><span data-stu-id="6e06d-210">Introduction to ASP.NET Web Programming by Using the Razor Syntax</span></span>](../introducing-razor-syntax-c.md)
- <span data-ttu-id="6e06d-211">[Příkaz jazyka SQL odstranit](http://www.w3schools.com/sql/sql_delete.asp) na webu W3Schools</span><span class="sxs-lookup"><span data-stu-id="6e06d-211">[SQL DELETE Statement](http://www.w3schools.com/sql/sql_delete.asp) on the W3Schools site</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="6e06d-212">[Předchozí](updating-data.md)
> [další](layouts.md)</span><span class="sxs-lookup"><span data-stu-id="6e06d-212">[Previous](updating-data.md)
[Next](layouts.md)</span></span>

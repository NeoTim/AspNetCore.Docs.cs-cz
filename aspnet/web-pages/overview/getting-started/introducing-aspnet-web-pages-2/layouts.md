---
uid: web-pages/overview/getting-started/introducing-aspnet-web-pages-2/layouts
title: Představení rozhraní ASP.NET Web Pages – vytvoření konzistentního rozložení | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto kurzu se dozvíte, jak k vytvoření konzistentního vzhledu stránky na webu, který používá rozhraní ASP.NET Web Pages použít rozložení. Předpokládá, že jste dokončili...
ms.author: riande
ms.date: 05/28/2015
ms.assetid: c85ec591-f8d7-4882-b763-de6ab9f3df7a
msc.legacyurl: /web-pages/overview/getting-started/introducing-aspnet-web-pages-2/layouts
msc.type: authoredcontent
ms.openlocfilehash: a6a007678d58547e9987ebda46bd08ae8aea66f7
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021176"
---
<a name="introducing-aspnet-web-pages---creating-a-consistent-layout"></a><span data-ttu-id="706af-104">Úvod do webových stránek ASP.NET – vytvoření konzistentního rozložení</span><span class="sxs-lookup"><span data-stu-id="706af-104">Introducing ASP.NET Web Pages - Creating a Consistent Layout</span></span>
====================
<span data-ttu-id="706af-105">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="706af-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="706af-106">V tomto kurzu se dozvíte, jak používat *rozložení* k vytvoření konzistentního vzhledu stránky na webu, který používá rozhraní ASP.NET Web Pages.</span><span class="sxs-lookup"><span data-stu-id="706af-106">This tutorial shows you how to use *layouts* to create a consistent look for the pages on a site that uses ASP.NET Web Pages.</span></span> <span data-ttu-id="706af-107">Předpokládá, že jste dokončili řady prostřednictvím [odstranění databázových dat ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251584).</span><span class="sxs-lookup"><span data-stu-id="706af-107">It assumes you have completed the series through [Deleting Database Data in ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251584).</span></span>
> 
> <span data-ttu-id="706af-108">Co se dozvíte:</span><span class="sxs-lookup"><span data-stu-id="706af-108">What you'll learn:</span></span>
> 
> - <span data-ttu-id="706af-109">Rozložení stránky je.</span><span class="sxs-lookup"><span data-stu-id="706af-109">What a layout page is.</span></span>
> - <span data-ttu-id="706af-110">Jak kombinovat rozložení stránek s dynamickým obsahem.</span><span class="sxs-lookup"><span data-stu-id="706af-110">How to combine layout pages with dynamic content.</span></span>
> - <span data-ttu-id="706af-111">Jak předávat hodnoty na stránku rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-111">How to pass values to a layout page.</span></span>


## <a name="about-layouts"></a><span data-ttu-id="706af-112">Informace o rozložení</span><span class="sxs-lookup"><span data-stu-id="706af-112">About Layouts</span></span>

<span data-ttu-id="706af-113">Na stránkách jsme dosud vytvořili všechny byly dokončení samostatné stránky.</span><span class="sxs-lookup"><span data-stu-id="706af-113">The pages you've created so far have all been complete, standalone pages.</span></span> <span data-ttu-id="706af-114">Všechny patřit do stejné lokality, ale nemají žádné společné prvky nebo standardní vzhled.</span><span class="sxs-lookup"><span data-stu-id="706af-114">They all belong to the same site, but they don't have any common elements or a standard look.</span></span>

<span data-ttu-id="706af-115">Většina serverů mají konzistentní vzhled a rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-115">Most sites do have a consistent look and layout.</span></span> <span data-ttu-id="706af-116">Například, pokud přejdete [http://www.microsoft.com/web/gallery/install.aspx?appid=IISExpress](https://www.microsoft.com/web/) lokality a vyhledejte, uvidíte, že na všech stránkách dodržovat celkové rozložení a vizuální motiv:</span><span class="sxs-lookup"><span data-stu-id="706af-116">For example, if you go to the [Microsoft.com/web](https://www.microsoft.com/web/) site and look around, you see that the pages all adhere to an overall layout and to a visual theme:</span></span>

![Stránka webu http://www.microsoft.com/web/gallery/install.aspx?appid=IISExpress znázorňující rozložení záhlaví, navigační oblast, oblast obsahu a zápatí](layouts/_static/image1.png)

<span data-ttu-id="706af-118">*Neefektivní* způsob, jak vytvořit toto rozložení by k definování záhlaví, v navigačním panelu a v zápatí samostatně na jednotlivých stránkách.</span><span class="sxs-lookup"><span data-stu-id="706af-118">An *inefficient* way to create this layout would be to define a header, navigation bar, and footer separately on each of your pages.</span></span> <span data-ttu-id="706af-119">Jste byste být duplikování stejné značky pokaždé, když.</span><span class="sxs-lookup"><span data-stu-id="706af-119">You'd be duplicating the same markup each time.</span></span> <span data-ttu-id="706af-120">Pokud chcete něco změnit (například aktualizovat zápatí), je třeba změnit každou stránku samostatně.</span><span class="sxs-lookup"><span data-stu-id="706af-120">If you wanted to change something (for example, update the footer), you'd have to change each page separately.</span></span>

<span data-ttu-id="706af-121">Tady *rozložení stránky* se dělí na.</span><span class="sxs-lookup"><span data-stu-id="706af-121">That's where *layout pages* come in.</span></span> <span data-ttu-id="706af-122">ASP.NET Web Pages, můžete definovat rozložení stránky, která poskytuje celkový kontejneru pro stránky na webu.</span><span class="sxs-lookup"><span data-stu-id="706af-122">In ASP.NET Web Pages, you can define a layout page that provides an overall container for pages on your site.</span></span> <span data-ttu-id="706af-123">Rozložení stránky může obsahovat například záhlaví, navigační oblasti a zápatí.</span><span class="sxs-lookup"><span data-stu-id="706af-123">For example, the layout page can contain the header, navigation area, and footer.</span></span> <span data-ttu-id="706af-124">Stránka rozložení obsahuje zástupný symbol, kde se vloží hlavní obsah.</span><span class="sxs-lookup"><span data-stu-id="706af-124">The layout page includes a placeholder where the main content goes.</span></span>

<span data-ttu-id="706af-125">Potom můžete definovat jednotlivé stránky obsahu, které obsahují kód a kód pouze pro danou stránku.</span><span class="sxs-lookup"><span data-stu-id="706af-125">You can then define individual content pages that contain the markup and the code for only that page.</span></span> <span data-ttu-id="706af-126">Stránky obsahu nemusí být kompletní HTML stránky. ještě nemají mít `<body>` elementu.</span><span class="sxs-lookup"><span data-stu-id="706af-126">Content pages don't have to be complete HTML pages; they don't even have to have a `<body>` element.</span></span> <span data-ttu-id="706af-127">Mají také řádku kódu, který určuje jaké rozložení stránky, které chcete zobrazit obsah v ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="706af-127">They also have a line of code that tells ASP.NET what layout page you want to display the content in.</span></span> <span data-ttu-id="706af-128">Tady je obrázek, který zhruba ukazuje, jak tento vztah funguje:</span><span class="sxs-lookup"><span data-stu-id="706af-128">Here's a picture that shows roughly how this relationship works:</span></span>

![Koncepční diagram zobrazující dvě obsahu stránky a rozložení stránky, do kterého se vešly](layouts/_static/image2.png)

<span data-ttu-id="706af-130">Tato interakce je snadno pochopitelný při pozorování v akci.</span><span class="sxs-lookup"><span data-stu-id="706af-130">This interaction is easy to understand when you see it in action.</span></span> <span data-ttu-id="706af-131">V tomto kurzu změníte filmy stránky rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-131">In this tutorial, you'll change your movies pages to use a layout.</span></span>

## <a name="adding-a-layout-page"></a><span data-ttu-id="706af-132">Přidání rozložení stránky</span><span class="sxs-lookup"><span data-stu-id="706af-132">Adding a Layout Page</span></span>

<span data-ttu-id="706af-133">Začnete tím, že vytvoříte stránku rozložení, který definuje rozložení stránky s záhlaví, zápatí nebo oblast pro hlavní obsah.</span><span class="sxs-lookup"><span data-stu-id="706af-133">You'll start by creating a layout page that defines a typical page layout with a header, footer, and an area for the main content.</span></span> <span data-ttu-id="706af-134">V lokalitě WebPagesMovies přidat CSHTML stránku s názvem  *\_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="706af-134">In the WebPagesMovies site, add a CSHTML page named *\_Layout.cshtml*.</span></span>

<span data-ttu-id="706af-135">Počáteční podtržítko ( `_` ) znak je důležité.</span><span class="sxs-lookup"><span data-stu-id="706af-135">The leading underscore ( `_` ) character is significant.</span></span> <span data-ttu-id="706af-136">Pokud na stránce název začíná podtržítkem, nebude ASP.NET přímo odeslat tuto stránku v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="706af-136">If a page's name starts with an underscore, ASP.NET won't directly send that page to the browser.</span></span> <span data-ttu-id="706af-137">Tato konvence umožňuje definovat stránky, které jsou požadovány pro váš web, ale, že uživatelé by neměla mít možnost požádat o přímo.</span><span class="sxs-lookup"><span data-stu-id="706af-137">This convention lets you define pages that are required for your site but that users shouldn't be able to request directly.</span></span>

<span data-ttu-id="706af-138">Nahraďte obsah na stránce s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="706af-138">Replace the content in the page with the following:</span></span>

[!code-html[Main](layouts/samples/sample1.html)]

<span data-ttu-id="706af-139">Jak je vidět, tento kód je právě ve formátu HTML, který používá `<div>` můžete definovat tři oddíly v stránce plus jeden více `<div>` – element pro uložení tři oddíly.</span><span class="sxs-lookup"><span data-stu-id="706af-139">As you can see, this markup is just HTML that uses `<div>` elements to define three sections in the page plus one more `<div>` element to hold the three sections.</span></span> <span data-ttu-id="706af-140">Zápatí obsahuje hodně kódu Razor: `@DateTime.Now.Year`, který bude vykreslení na tomto místě na stránce do aktuálního roku.</span><span class="sxs-lookup"><span data-stu-id="706af-140">The footer contains a bit of Razor code: `@DateTime.Now.Year`, which will render the current year at that location in the page.</span></span>

<span data-ttu-id="706af-141">Všimněte si, že je odkaz s názvem šablony stylů *Movies.css*.</span><span class="sxs-lookup"><span data-stu-id="706af-141">Notice that there's a link to a style sheet named *Movies.css*.</span></span> <span data-ttu-id="706af-142">Šablona stylů je, kde budou podrobnosti fyzické rozložení elementů určené.</span><span class="sxs-lookup"><span data-stu-id="706af-142">The style sheet is where the details of the physical layout of the elements will be defined.</span></span> <span data-ttu-id="706af-143">Který vytvoříte za chvíli.</span><span class="sxs-lookup"><span data-stu-id="706af-143">You'll create that in a moment.</span></span>

<span data-ttu-id="706af-144">Pouze neobvyklé funkce v tomto  *\_Layout.cshtml* stránka je `@Render.Body()` řádku.</span><span class="sxs-lookup"><span data-stu-id="706af-144">The only unusual feature in this *\_Layout.cshtml* page is the `@Render.Body()` line.</span></span> <span data-ttu-id="706af-145">To je zástupný symbol, kde je obsah přejde při toto rozložení je sloučen s jinou stránku.</span><span class="sxs-lookup"><span data-stu-id="706af-145">That's the placeholder where the content will go when this layout is merged with another page.</span></span>

## <a name="adding-a-css-file"></a><span data-ttu-id="706af-146">Přidává se soubor CSS</span><span class="sxs-lookup"><span data-stu-id="706af-146">Adding a .css File</span></span>

<span data-ttu-id="706af-147">Preferovaný způsob, jak definovat skutečné uspořádání prvků (to znamená, vzhled) na stránce je použití pravidla šablony kaskádových stylů (CSS).</span><span class="sxs-lookup"><span data-stu-id="706af-147">The preferred way to define the actual arrangement (that is, appearance) of elements on the page is to use cascading style sheet (CSS) rules.</span></span> <span data-ttu-id="706af-148">Proto vytvoříte *.css* soubor, který obsahuje pravidla pro nové rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-148">So you'll create a *.css* file that has the rules for your new layout.</span></span>

<span data-ttu-id="706af-149">V nástroji WebMatrix vyberte kořenového webu.</span><span class="sxs-lookup"><span data-stu-id="706af-149">In WebMatrix, select the root of your site.</span></span> <span data-ttu-id="706af-150">Pak v **soubory** kartu na pásu karet klikněte na šipku v části **nový** tlačítko a pak klikněte na tlačítko **novou složku**.</span><span class="sxs-lookup"><span data-stu-id="706af-150">Then in the **Files** tab of the ribbon, click the arrow under the **New** button and then click **New Folder**.</span></span>

![Možnost "Nová složka" v rámci New na pásu karet.](layouts/_static/image3.png)

<span data-ttu-id="706af-152">Název nové složky *styly*.</span><span class="sxs-lookup"><span data-stu-id="706af-152">Name the new folder *Styles*.</span></span>

![Pojmenování nové složky "styly.](layouts/_static/image4.png)

<span data-ttu-id="706af-154">Uvnitř nové *styly* složce vytvořte soubor s názvem *Movies.css*.</span><span class="sxs-lookup"><span data-stu-id="706af-154">Inside the new *Styles* folder, create a file named *Movies.css*.</span></span>

![Vytvoření nového souboru Movies.css](layouts/_static/image5.png)

<span data-ttu-id="706af-156">Nahraďte obsah nového *.css* souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="706af-156">Replace the contents of the new *.css* file with the following:</span></span>

[!code-css[Main](layouts/samples/sample2.css)]

<span data-ttu-id="706af-157">Říkáme nebude téměř těchto pravidel šablon stylů CSS, s výjimkou Poznámka: dvě věci.</span><span class="sxs-lookup"><span data-stu-id="706af-157">We won't say much about these CSS rules, except to note two things.</span></span> <span data-ttu-id="706af-158">Jeden je, že kromě nastavení písma a velikosti, pravidla použít absolutní pozici k navázání umístění záhlaví, zápatí a oblast hlavního obsahu.</span><span class="sxs-lookup"><span data-stu-id="706af-158">One is that in addition to setting fonts and sizes, the rules use absolute positioning to establish the location of the header, footer, and main content area.</span></span> <span data-ttu-id="706af-159">Pokud začínáte umístění v jazyce CSS, si můžete přečíst [umístění šablon stylů CSS](http://www.w3schools.com/css/css_positioning.asp) kurz v lokalitě W3Schools.</span><span class="sxs-lookup"><span data-stu-id="706af-159">If you're new to positioning in CSS, you can read the [CSS Positioning](http://www.w3schools.com/css/css_positioning.asp) tutorial at the W3Schools site.</span></span>

<span data-ttu-id="706af-160">Druhou věcí si je, že v dolní části, jsme jste zkopírovali pravidel stylu, které byly původně definována jednotlivě v *Movies.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="706af-160">The other thing to note is that at the bottom, we've copied the style rules that were originally defined individually in the *Movies.cshtml* file.</span></span> <span data-ttu-id="706af-161">Tato pravidla byly použity v [Úvod do zobrazení dat podle pomocí rozhraní ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251580) kurzu provádět `WebGrid` pomocné rutiny vykreslení kódu, který rozděluje přidán do tabulky.</span><span class="sxs-lookup"><span data-stu-id="706af-161">These rules were used in the [Introduction to Displaying Data by Using ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=251580) tutorial to make the `WebGrid` helper render markup that added stripes to the table.</span></span> <span data-ttu-id="706af-162">(Pokud se chystáte používat *.css* souboru pro definice stylů, můžete také ukládat pravidel stylu, které pro celý web v něm.)</span><span class="sxs-lookup"><span data-stu-id="706af-162">(If you're going to use a *.css* file for style definitions, you might as well put the style rules for the whole site in it.)</span></span>

## <a name="updating-the-movies-file-to-use-the-layout"></a><span data-ttu-id="706af-163">Aktualizuje se soubor videa rozložení</span><span class="sxs-lookup"><span data-stu-id="706af-163">Updating the Movies File to Use the Layout</span></span>

<span data-ttu-id="706af-164">Nyní můžete aktualizovat existující soubory v tak, aby používal nové rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-164">Now you can update the existing files in your site to use the new layout.</span></span> <span data-ttu-id="706af-165">Otevřít *Movies.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="706af-165">Open the *Movies.cshtml* file.</span></span> <span data-ttu-id="706af-166">V horní části stránky přidejte jako první řádek kódu, následující:</span><span class="sxs-lookup"><span data-stu-id="706af-166">At the top, as the first line of code, add the following:</span></span>

[!code-csharp[Main](layouts/samples/sample3.cs)]

<span data-ttu-id="706af-167">Na stránce nyní začíná tímto způsobem:</span><span class="sxs-lookup"><span data-stu-id="706af-167">The page now starts out this way:</span></span>

[!code-cshtml[Main](layouts/samples/sample4.cshtml?highlight=2)]

<span data-ttu-id="706af-168">Tento jeden řádek kódu říká technologie ASP.NET, že *filmy* spuštění stránky, by měly být sloučeny s  *\_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="706af-168">This one line of code tells ASP.NET that when the *Movies* page runs, it should be merged with the *\_Layout.cshtml* file.</span></span>

<span data-ttu-id="706af-169">Protože *Movies.cshtml* souboru teď používá rozložení stránky, můžete odebrat značku ze *Movies.cshtml* stránka, která už se postaral o podle  *\_Layout.cshtml*souboru.</span><span class="sxs-lookup"><span data-stu-id="706af-169">Since the *Movies.cshtml* file now uses a layout page, you can remove the markup from the *Movies.cshtml* page that's taken care of by the *\_Layout.cshtml* file.</span></span> <span data-ttu-id="706af-170">Vysypat `<!DOCTYPE>`, `<html>`, a `<body>` počátečních a koncových značek.</span><span class="sxs-lookup"><span data-stu-id="706af-170">Take out the `<!DOCTYPE>`, `<html>`, and `<body>` opening and closing tags.</span></span> <span data-ttu-id="706af-171">Vysypat celý `<head>` elementu a jeho obsah, včetně pravidel stylu mřížky, protože Teď máte tato pravidla *.css* souboru.</span><span class="sxs-lookup"><span data-stu-id="706af-171">Take out the entire `<head>` element and its contents, which includes the style rules for the grid, since you've now got those rules in a *.css* file.</span></span> <span data-ttu-id="706af-172">Ať jste na něj, změňte existující `<h1>` elementu `<h2>` element; můžete mít `<h1>` elementu na stránce rozložení již.</span><span class="sxs-lookup"><span data-stu-id="706af-172">While you're at it, change the existing `<h1>` element to an `<h2>` element; you have an `<h1>` element in the layout page already.</span></span> <span data-ttu-id="706af-173">Změnit `<h2>` text na "Seznamu filmy".</span><span class="sxs-lookup"><span data-stu-id="706af-173">Change the `<h2>` text to "List Movies".</span></span>

<span data-ttu-id="706af-174">Za normálních okolností nemusí provádět tyto druhy změny na stránce obsahu.</span><span class="sxs-lookup"><span data-stu-id="706af-174">Normally you wouldn't have to make these sorts of changes in a content page.</span></span> <span data-ttu-id="706af-175">Při spuštění webu navýšení kapacity s stránku rozložení, vytváření obsahu stránky bez těchto prvků na začátku.</span><span class="sxs-lookup"><span data-stu-id="706af-175">When you start your site out with a layout page, you create content pages without all these elements to begin with.</span></span> <span data-ttu-id="706af-176">V takovém případě však při převádění na samostatné stránce, který se používá rozložení, takže je hodně vyčištění.</span><span class="sxs-lookup"><span data-stu-id="706af-176">In this case, though, you're converting a standalone page to one that uses a layout, so there's a bit of cleanup.</span></span>

<span data-ttu-id="706af-177">Jakmile budete hotovi, *Movies.cshtml* stránka bude vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="706af-177">When you're finished, the *Movies.cshtml* page will look like the following:</span></span>

[!code-cshtml[Main](layouts/samples/sample5.cshtml)]

### <a name="testing-the-layout"></a><span data-ttu-id="706af-178">Testování rozložení</span><span class="sxs-lookup"><span data-stu-id="706af-178">Testing the Layout</span></span>

<span data-ttu-id="706af-179">Nyní uvidíte, jak vypadá rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-179">Now you can see what the layout looks like.</span></span> <span data-ttu-id="706af-180">V nástroji WebMatrix, klikněte pravým tlačítkem myši *Movies.cshtml* stránku a vybrat **spustit v prohlížeči**.</span><span class="sxs-lookup"><span data-stu-id="706af-180">In WebMatrix, right-click the *Movies.cshtml* page and select **Launch in browser**.</span></span> <span data-ttu-id="706af-181">Prohlížeč zobrazí stránky, vypadá jako na této stránce:</span><span class="sxs-lookup"><span data-stu-id="706af-181">When the browser displays the page, it looks like this page:</span></span>

![Stránka filmy vykreslen pomocí rozložení](layouts/_static/image6.png)

<span data-ttu-id="706af-183">ASP.NET se sloučil obsah stránky Movies.cshtml do  *\_Layout.cshtml* stránky, kde vpravo `RenderBody` je metoda.</span><span class="sxs-lookup"><span data-stu-id="706af-183">ASP.NET has merged the content of the Movies.cshtml page into the *\_Layout.cshtml* page right where the `RenderBody` method is.</span></span> <span data-ttu-id="706af-184">A samozřejmě  *\_Layout.cshtml* stránce odkazy *.css* souboru, která definuje vzhled stránky.</span><span class="sxs-lookup"><span data-stu-id="706af-184">And of course the *\_Layout.cshtml* page references a *.css* file that defines the look of the page.</span></span>

## <a name="updating-the-addmovie-page-to-use-the-layout"></a><span data-ttu-id="706af-185">Aktualizuje se AddMovie stránku rozložení</span><span class="sxs-lookup"><span data-stu-id="706af-185">Updating the AddMovie Page to Use the Layout</span></span>

<span data-ttu-id="706af-186">Skutečná výhoda rozložení je, že můžete použít je pro všechny stránky ve vaší lokalitě.</span><span class="sxs-lookup"><span data-stu-id="706af-186">The real benefit of layouts is that you can use them for all the pages in your site.</span></span> <span data-ttu-id="706af-187">Otevřít *AddMovie.cshtml* stránky.</span><span class="sxs-lookup"><span data-stu-id="706af-187">Open the *AddMovie.cshtml* page.</span></span>

<span data-ttu-id="706af-188">Může být nezapomeňte, že *AddMovie.cshtml* stránky původně některá pravidla šablon stylů CSS v něm k definování vzhledu chybových zpráv ověření.</span><span class="sxs-lookup"><span data-stu-id="706af-188">You might remember that the *AddMovie.cshtml* page originally had some CSS rules in it to define the look of validation error messages.</span></span> <span data-ttu-id="706af-189">Vzhledem k tomu, že máte *.css* soubor pro váš webový server, můžete přesunout tyto pravidla, která *.css* souboru.</span><span class="sxs-lookup"><span data-stu-id="706af-189">Since you have a *.css* file for your site now, you can move those rules to the *.css* file.</span></span> <span data-ttu-id="706af-190">Odeberte je ze *AddMovie.cshtml* soubor a přidat je do dolní části *Movies.css* souboru.</span><span class="sxs-lookup"><span data-stu-id="706af-190">Remove them from the *AddMovie.cshtml* file and add them to the bottom of the *Movies.css* file.</span></span> <span data-ttu-id="706af-191">Při přesunutí následující pravidla:</span><span class="sxs-lookup"><span data-stu-id="706af-191">You are moving the following rules:</span></span>

[!code-css[Main](layouts/samples/sample6.css)]

<span data-ttu-id="706af-192">Provést stejnou řadu změn v *AddMovie.cshtml* , jaké jste použili *Movies.cshtml* – přidání `Layout="~/_Layout.cshtml;` a odebrat značka jazyka HTML, který je teď nadbytečné.</span><span class="sxs-lookup"><span data-stu-id="706af-192">Now make the same sorts of changes in *AddMovie.cshtml* that you did for *Movies.cshtml* — add `Layout="~/_Layout.cshtml;` and remove the HTML markup that's now extraneous.</span></span> <span data-ttu-id="706af-193">Změnit `<h1>` elementu `<h2>`.</span><span class="sxs-lookup"><span data-stu-id="706af-193">Change the `<h1>` element to `<h2>`.</span></span> <span data-ttu-id="706af-194">Až budete hotovi, stránka bude vypadat jako v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="706af-194">When you're done, the page will look like this example:</span></span>

[!code-cshtml[Main](layouts/samples/sample7.cshtml)]

<span data-ttu-id="706af-195">Spuštění stránky.</span><span class="sxs-lookup"><span data-stu-id="706af-195">Run the page.</span></span> <span data-ttu-id="706af-196">Teď vypadá jako na tomto obrázku:</span><span class="sxs-lookup"><span data-stu-id="706af-196">Now it looks like this illustration:</span></span>

!["Přidat video" stránku vykreslen pomocí rozložení](layouts/_static/image7.png)

<span data-ttu-id="706af-198">Chcete provést změny podobné změny stránky na webu – *EditMovie.cshtml* a *DeleteMovie.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="706af-198">You want to make similar changes to the pages in the site — *EditMovie.cshtml* and *DeleteMovie.cshtml*.</span></span> <span data-ttu-id="706af-199">Ale předtím, než se pustíte, můžete provádět jiná změna rozložení, které je o něco víc.</span><span class="sxs-lookup"><span data-stu-id="706af-199">However, before you do, you can make another change to the layout that makes it a little more flexible.</span></span>

## <a name="passing-title-information-to-the-layout-page"></a><span data-ttu-id="706af-200">Informace o předávání titulech ke stránce rozložení</span><span class="sxs-lookup"><span data-stu-id="706af-200">Passing Title Information to the Layout Page</span></span>

<span data-ttu-id="706af-201">*\_Layout.cshtml* má stránka, kterou jste vytvořili `<title>` element, který je nastavena na "Můj web video".</span><span class="sxs-lookup"><span data-stu-id="706af-201">The *\_Layout.cshtml* page that you created has a `<title>` element that's set to "My Movie Site".</span></span> <span data-ttu-id="706af-202">Většina prohlížečů zobrazit obsah tohoto prvku jako text na kartě:</span><span class="sxs-lookup"><span data-stu-id="706af-202">Most browsers display the content of this element as the text on a tab:</span></span>

![Na stránce &lt;název&gt; element zobrazí na kartě prohlížeče](layouts/_static/image8.png)

<span data-ttu-id="706af-204">Tyto informace název je obecná.</span><span class="sxs-lookup"><span data-stu-id="706af-204">This title information is generic.</span></span> <span data-ttu-id="706af-205">Předpokládejme, že chcete text nadpisu na konkrétnější na aktuální stránku.</span><span class="sxs-lookup"><span data-stu-id="706af-205">Suppose that you want the title text to be more specific to the current page.</span></span> <span data-ttu-id="706af-206">(Text nadpisu se používá také vyhledávači jak zjistit, co vaše stránka o.) Můžete předat informace z obsahu stránky jako *Movies.cshtml* nebo *AddMovie.cshtml* rozložení stránky a pak pomocí těchto informací k přizpůsobení stránky rozložení vykreslí.</span><span class="sxs-lookup"><span data-stu-id="706af-206">(The title text is also used by search engines to determine what your page is about.) You can pass information from a content page like *Movies.cshtml* or *AddMovie.cshtml* to the layout page, and then use that information to customize what the layout page renders.</span></span>

<span data-ttu-id="706af-207">Otevřít *Movies.cshtml* stránku znovu nezobrazovat.</span><span class="sxs-lookup"><span data-stu-id="706af-207">Open the *Movies.cshtml* page again.</span></span> <span data-ttu-id="706af-208">V kódu v horní části přidejte následující řádek:</span><span class="sxs-lookup"><span data-stu-id="706af-208">In the code at the top, add the following line:</span></span>

[!code-csharp[Main](layouts/samples/sample8.cs)]

<span data-ttu-id="706af-209">`Page` Objekt je k dispozici na všech *.cshtml* stránky a je pro tento účel, a to ke sdílení informací mezi stránky a rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-209">The `Page` object is available on all *.cshtml* pages and is for this purpose, namely to share information between a page and its layout.</span></span>

<span data-ttu-id="706af-210">Otevřít<em>\_Layout.cshtml</em> stránky.</span><span class="sxs-lookup"><span data-stu-id="706af-210">Open the<em>\_Layout.cshtml</em> page.</span></span> <span data-ttu-id="706af-211">Změnit `<title>` element tak, že bude vypadat jako tento kód:</span><span class="sxs-lookup"><span data-stu-id="706af-211">Change the `<title>` element so that it looks like this markup:</span></span>

[!code-html[Main](layouts/samples/sample9.html)]

<span data-ttu-id="706af-212">Tento kód vykreslí, bez ohledu `Page.Title` vlastnost přímo na tomto místě na stránce.</span><span class="sxs-lookup"><span data-stu-id="706af-212">This code renders whatever is in the `Page.Title` property right at that location in the page.</span></span>

<span data-ttu-id="706af-213">Spustit *Movies.cshtml* stránky.</span><span class="sxs-lookup"><span data-stu-id="706af-213">Run the *Movies.cshtml* page.</span></span> <span data-ttu-id="706af-214">Tentokrát kartu prohlížeče ukazuje předané jako hodnotu `Page.Title`:</span><span class="sxs-lookup"><span data-stu-id="706af-214">This time the browser tab shows what you passed as the value of `Page.Title`:</span></span>

![Na kartě prohlížeče, který je zobrazen titulek vytvářet dynamicky](layouts/_static/image9.png)

<span data-ttu-id="706af-216">Pokud chcete, zobrazte zdroj stránky v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="706af-216">If you want, view the page source in the browser.</span></span> <span data-ttu-id="706af-217">Vidíte, že `<title>` prvek je vykreslovaný jako `<title>List Movies</title>`.</span><span class="sxs-lookup"><span data-stu-id="706af-217">You can see that the `<title>` element is rendered as `<title>List Movies</title>`.</span></span>

> [!TIP] 
> 
> <span data-ttu-id="706af-218">**Objekt stránky**</span><span class="sxs-lookup"><span data-stu-id="706af-218">**The Page Object**</span></span>
> 
> <span data-ttu-id="706af-219">Užitečné funkce `Page` je, že se jedná o dynamický objekt, `Title` vlastnost není pevný nebo vyhrazený název.</span><span class="sxs-lookup"><span data-stu-id="706af-219">A useful feature of `Page` is that it's a dynamic object — the `Title` property is not a fixed or reserved name.</span></span> <span data-ttu-id="706af-220">Můžete použít *jakékoli* název hodnotu `Page` objektu.</span><span class="sxs-lookup"><span data-stu-id="706af-220">You can use *any* name for a value of the `Page` object.</span></span> <span data-ttu-id="706af-221">Například, kterou může stejně snadno, jste předali název pomocí vlastnosti s názvem `Page.CurrentName` nebo `Page.MyPage`.</span><span class="sxs-lookup"><span data-stu-id="706af-221">For example, you could as easily have passed the title by using a property named `Page.CurrentName` or `Page.MyPage`.</span></span> <span data-ttu-id="706af-222">Jediným omezením je, že název obsahuje postupy běžných pravidel pro vlastnosti, které může mít název.</span><span class="sxs-lookup"><span data-stu-id="706af-222">The only restriction is that the name has to follow the normal rules for what properties can be named.</span></span> <span data-ttu-id="706af-223">(Například název nesmí obsahovat mezery.)</span><span class="sxs-lookup"><span data-stu-id="706af-223">(For example, the name can't contain a space.)</span></span>
> 
> <span data-ttu-id="706af-224">Můžete předat libovolný počet hodnot s použitím `Page` objektu.</span><span class="sxs-lookup"><span data-stu-id="706af-224">You can pass any number of values by using the `Page` object.</span></span> <span data-ttu-id="706af-225">Pokud chcete předat informace o filmu ke stránce rozložení, mohli předat hodnoty pomocí něco jako `Page.MovieTitle` a `Page.Genre` a `Page.MovieYear`.</span><span class="sxs-lookup"><span data-stu-id="706af-225">If you wanted to pass movie information to the layout page, you could pass values by using something like `Page.MovieTitle` and `Page.Genre` and `Page.MovieYear`.</span></span> <span data-ttu-id="706af-226">(Nebo jiné názvy, které je určena k ukládání informací.) Jediným požadavkem – což je pravděpodobně zřejmé – je, že budete muset použít stejné názvy v obsahu stránky a rozložení stránky.</span><span class="sxs-lookup"><span data-stu-id="706af-226">(Or any other names that you invented to store the information.) The only requirement — which is probably obvious — is that you have to use the same names in the content page and the layout page.</span></span>
> 
> <span data-ttu-id="706af-227">Informace o předávání pomocí `Page` objektu se neomezuje na pouze text se zobrazí na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-227">The information you pass by using the `Page` object isn't limited to just text to display on the layout page.</span></span> <span data-ttu-id="706af-228">Můžete předat hodnotu na stránku rozložení a pak použít hodnotu rozhodnout, jestli se mají zobrazovat oddíl na stránce kódu ve stránce rozložení co *.css* soubor použít a tak dále.</span><span class="sxs-lookup"><span data-stu-id="706af-228">You can pass a value to the layout page, and then code in the layout page can use the value to decide whether to display a section of the page, what *.css* file to use, and so on.</span></span> <span data-ttu-id="706af-229">Předáním hodnoty `Page` objektu jsou stejně jako jakékoli jiné hodnoty, které použijete v kódu.</span><span class="sxs-lookup"><span data-stu-id="706af-229">The values you pass in the `Page` object are like any other values that you use in code.</span></span> <span data-ttu-id="706af-230">Stačí je, že hodnoty pocházejí z obsahu stránky a jsou předány na stránku rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-230">It's just that the values originate in the content page and are passed to the layout page.</span></span>


<span data-ttu-id="706af-231">Otevřít *AddMovie.cshtml* stránce a přidá řádek do horní části kódu, který obsahuje název *AddMovie.cshtml* stránky:</span><span class="sxs-lookup"><span data-stu-id="706af-231">Open the *AddMovie.cshtml* page and add a line to the top of the code that provides a title for the *AddMovie.cshtml* page:</span></span>

[!code-csharp[Main](layouts/samples/sample10.cs)]

<span data-ttu-id="706af-232">Spustit *AddMovie.cshtml* stránky.</span><span class="sxs-lookup"><span data-stu-id="706af-232">Run the *AddMovie.cshtml* page.</span></span> <span data-ttu-id="706af-233">Zobrazí se nový název:</span><span class="sxs-lookup"><span data-stu-id="706af-233">You see the new title there:</span></span>

![Na kartě prohlížeče, který je zobrazen titulek "přidat filmy vytvářet dynamicky](layouts/_static/image10.png)

## <a name="updating-the-remaining-pages-to-use-the-layout"></a><span data-ttu-id="706af-235">Aktualizuje se zbývající stránky rozložení</span><span class="sxs-lookup"><span data-stu-id="706af-235">Updating the Remaining Pages to Use the Layout</span></span>

<span data-ttu-id="706af-236">Teď můžete dokončit zbývající stránky na webu tak, aby používaly nové rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-236">Now you can finish the remaining pages in your site so that they use the new layout.</span></span> <span data-ttu-id="706af-237">Otevřít *EditMovie.cshtml* a *DeleteMovie.cshtml* následně a provést stejné změny v každém.</span><span class="sxs-lookup"><span data-stu-id="706af-237">Open *EditMovie.cshtml* and *DeleteMovie.cshtml* in turn and make the same changes in each.</span></span>

<span data-ttu-id="706af-238">Přidáte řádek kódu, který odkazuje na stránku rozložení:</span><span class="sxs-lookup"><span data-stu-id="706af-238">Add the line of code that links to the layout page:</span></span>

[!code-csharp[Main](layouts/samples/sample11.cs)]

<span data-ttu-id="706af-239">Přidejte řádek nastavit nadpis stránky:</span><span class="sxs-lookup"><span data-stu-id="706af-239">Add a line to set the title of the page:</span></span>

[!code-csharp[Main](layouts/samples/sample12.cs)]

<span data-ttu-id="706af-240">nebo:</span><span class="sxs-lookup"><span data-stu-id="706af-240">or:</span></span>

[!code-csharp[Main](layouts/samples/sample13.cs)]

<span data-ttu-id="706af-241">Odeberte všechny nadbytečné kód HTML – v podstatě, ponechte pouze bity, které jsou uvnitř `<body>` – element (plus bloku kódu v horní části).</span><span class="sxs-lookup"><span data-stu-id="706af-241">Remove all the extraneous HTML markup — basically, leave only the bits that are inside the `<body>` element (plus the code block at the top).</span></span>

<span data-ttu-id="706af-242">Změnit `<h1>` elementu do `<h2>` elementu.</span><span class="sxs-lookup"><span data-stu-id="706af-242">Change the `<h1>` element to be an `<h2>` element.</span></span>

<span data-ttu-id="706af-243">Pokud jste provedli tyto změny, otestujte a ujistěte se, že se správně zobrazuje a zda je správný název.</span><span class="sxs-lookup"><span data-stu-id="706af-243">When you've made these changes, test each and make sure that it's displaying properly and that the title is correct.</span></span>

## <a name="parting-thoughts-about-layout-pages"></a><span data-ttu-id="706af-244">Závěrečný myšlenky o rozložení stránky</span><span class="sxs-lookup"><span data-stu-id="706af-244">Parting Thoughts About Layout Pages</span></span>

<span data-ttu-id="706af-245">V tomto kurzu jste vytvořili  *\_Layout.cshtml* stránce a použít `RenderBody` metoda sloučit obsah z jiné stránky.</span><span class="sxs-lookup"><span data-stu-id="706af-245">In this tutorial you created a *\_Layout.cshtml* page and used the `RenderBody` method to merge content from another page.</span></span> <span data-ttu-id="706af-246">To je základní vzor pro použití rozložení na webových stránkách.</span><span class="sxs-lookup"><span data-stu-id="706af-246">That's the basic pattern for using layouts in Web Pages.</span></span>

<span data-ttu-id="706af-247">Rozložení stránky mají další funkce, které jsme neměli abychom ji tady pokryli.</span><span class="sxs-lookup"><span data-stu-id="706af-247">Layout pages have additional features that we didn't cover here.</span></span> <span data-ttu-id="706af-248">Například je možné vnořovat rozložení stránek – jednu stránku rozložení může odkazovat na zase jiný.</span><span class="sxs-lookup"><span data-stu-id="706af-248">For example, you can nest layout pages — one layout page can in turn reference another.</span></span> <span data-ttu-id="706af-249">Vnořené rozložení může být užitečné, pokud pracujete s dílčí části objektů lokality, které vyžadují různá rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-249">Nested layouts can be useful if you're working with subsections of a site that require different layouts.</span></span> <span data-ttu-id="706af-250">Můžete také použít další metody (například `RenderSection`) k nastavení s názvem části na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="706af-250">You can also use additional methods (for example, `RenderSection`) to set up named sections in the layout page.</span></span>

<span data-ttu-id="706af-251">Kombinace stránkami rozložení a *.css* soubory jsou výkonné.</span><span class="sxs-lookup"><span data-stu-id="706af-251">The combination of layout pages and *.css* files is powerful.</span></span> <span data-ttu-id="706af-252">Jak uvidíte v dalším sérii, ve službě WebMatrix můžete vytvořit web založený na *šablony*, kterým získáte web, který obsahuje předem připravených funkce v ní.</span><span class="sxs-lookup"><span data-stu-id="706af-252">As you'll see in the next tutorial series, in WebMatrix you can create a site based on a *template*, which gives you a site that has prebuilt functionality in it.</span></span> <span data-ttu-id="706af-253">Šablony velice rozložení stránky a šablony stylů CSS vytvářet weby, které vypadají skvěle a, které mají funkce, jako jsou nabídky.</span><span class="sxs-lookup"><span data-stu-id="706af-253">The templates make good use of layout pages and CSS to create sites that look great and that have features like menus.</span></span> <span data-ttu-id="706af-254">Zde je snímek domovské stránce webu na základě šablony, zobrazuje funkce, které používají rozložení stránky a šablony stylů CSS:</span><span class="sxs-lookup"><span data-stu-id="706af-254">Here's a screenshot of the home page from a site based on a template, showing features that use layout pages and CSS:</span></span>

![Rozložení vytvořené šablony webu služby WebMatrix zobrazující záhlaví, navigační oblast, oblasti obsahu, volitelný oddíl a odkazy přihlášení](layouts/_static/image11.png)

## <a name="complete-listing-for-movie-page-updated-to-use-a-layout-page"></a><span data-ttu-id="706af-256">Úplný seznam pro stránku Movie (aktualizované na používání stránku rozložení)</span><span class="sxs-lookup"><span data-stu-id="706af-256">Complete Listing for Movie Page (Updated to Use a Layout Page)</span></span>

[!code-cshtml[Main](layouts/samples/sample14.cshtml)]

## <a name="complete-page-listing-for-add-movie-page-updated-for-layout"></a><span data-ttu-id="706af-257">Dokončení stránku se seznamem pro přidání stránky Movie (aktualizováno pro rozložení)</span><span class="sxs-lookup"><span data-stu-id="706af-257">Complete Page Listing for Add Movie Page (Updated for Layout)</span></span>

[!code-cshtml[Main](layouts/samples/sample15.cshtml)]

## <a name="complete-page-listing-for-delete-movie-page-updated-for-layout"></a><span data-ttu-id="706af-258">Úplný výpis stránky pro stránku odstranit Movie (aktualizováno pro rozložení)</span><span class="sxs-lookup"><span data-stu-id="706af-258">Complete Page Listing for Delete Movie Page (Updated for Layout)</span></span>

[!code-cshtml[Main](layouts/samples/sample16.cshtml)]

## <a name="complete-page-listing-for-edit-movie-page-updated-for-layout"></a><span data-ttu-id="706af-259">Úplný výpis stránky pro stránku video upravit (aktualizováno pro rozložení)</span><span class="sxs-lookup"><span data-stu-id="706af-259">Complete Page Listing for Edit Movie Page (Updated for Layout)</span></span>

[!code-cshtml[Main](layouts/samples/sample17.cshtml)]

## <a name="coming-up-next"></a><span data-ttu-id="706af-260">Chystá se další</span><span class="sxs-lookup"><span data-stu-id="706af-260">Coming Up Next</span></span>

<span data-ttu-id="706af-261">V dalším kurzu se dozvíte, jak publikovat svůj web na Internetu, všichni můžou zobrazit.</span><span class="sxs-lookup"><span data-stu-id="706af-261">In the next tutorial, you'll learn how to publish your site to the Internet so everyone can see it.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="706af-262">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="706af-262">Additional Resources</span></span>

- <span data-ttu-id="706af-263">[Vytvoření konzistentního vypadat](https://go.microsoft.com/fwlink/?LinkID=202891) – článek, který obsahuje některé další podrobné informace o práci s rozložením.</span><span class="sxs-lookup"><span data-stu-id="706af-263">[Creating a Consistent Look](https://go.microsoft.com/fwlink/?LinkID=202891) — An article that provides some more detail on working with layouts.</span></span> <span data-ttu-id="706af-264">Také popisuje, jak předat hodnotu stránku rozložení, který zobrazí nebo skryje část obsahu.</span><span class="sxs-lookup"><span data-stu-id="706af-264">It also describes how to pass a value to a layout page that shows or hides some of the content.</span></span>
- <span data-ttu-id="706af-265">[Vnořené rozložení stránky s jádrem Razor](http://www.mikesdotnetting.com/Article/164/Nested-Layout-Pages-with-Razor) – Mike Brind blogy příklad toho, jak lze vnořit rozložení stránky.</span><span class="sxs-lookup"><span data-stu-id="706af-265">[Nested Layout Pages with Razor](http://www.mikesdotnetting.com/Article/164/Nested-Layout-Pages-with-Razor) — Mike Brind blogs an example of how to nest layout pages.</span></span> <span data-ttu-id="706af-266">(Včetně stažení stránky.)</span><span class="sxs-lookup"><span data-stu-id="706af-266">(Includes a download of the pages.)</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="706af-267">[Předchozí](deleting-data.md)
> [další](publishing.md)</span><span class="sxs-lookup"><span data-stu-id="706af-267">[Previous](deleting-data.md)
[Next](publishing.md)</span></span>

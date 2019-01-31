---
title: Přidání zobrazení do aplikace ASP.NET Core MVC
author: rick-anderson
description: Přidání zobrazení pro jednoduchou aplikaci ASP.NET Core MVC
ms.author: riande
ms.date: 03/04/2017
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 32eddb233a8a6b9b8f480926673d15d568ce6ede
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236429"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="92e58-103">Přidání zobrazení do aplikace ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="92e58-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="92e58-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="92e58-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="92e58-105">V této části můžete změnit `HelloWorldController` třídu použít [Razor](xref:mvc/views/razor) zobrazení soubory k čistě zapouzdření proces generování odpovědi HTML do klienta.</span><span class="sxs-lookup"><span data-stu-id="92e58-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="92e58-106">Vytvoříte soubor šablony zobrazení pomocí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="92e58-106">You create a view template file using Razor.</span></span> <span data-ttu-id="92e58-107">Šablony Razor na základě zobrazení mají *.cshtml* příponu souboru.</span><span class="sxs-lookup"><span data-stu-id="92e58-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="92e58-108">Poskytují elegantní způsob, jak vytvořit výstup ve formátu HTML s C#.</span><span class="sxs-lookup"><span data-stu-id="92e58-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="92e58-109">Aktuálně `Index` metoda vrátí řetězec a zobrazí se zpráva, která je pevně zakódovaný ve třídě controller.</span><span class="sxs-lookup"><span data-stu-id="92e58-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="92e58-110">V `HelloWorldController` třídy, nahraďte `Index` metodu s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="92e58-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="92e58-111">Předchozí kód volá kontroleru <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metody.</span><span class="sxs-lookup"><span data-stu-id="92e58-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="92e58-112">Zobrazit šablonu používá ke generování odpověď jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="92e58-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="92e58-113">Metody kontroleru (označované také jako *metody akce*), například `Index` metody popsané výše, obvykle vracet <xref:Microsoft.AspNetCore.Mvc.IActionResult> (nebo třída odvozená z <xref:Microsoft.AspNetCore.Mvc.ActionResult>), není typem, jako jsou `string`.</span><span class="sxs-lookup"><span data-stu-id="92e58-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="92e58-114">Přidání zobrazení</span><span class="sxs-lookup"><span data-stu-id="92e58-114">Add a view</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="92e58-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92e58-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="92e58-116">Klikněte pravým tlačítkem na *zobrazení* složky a potom **Přidat > Nová složka** a pojmenujte složku *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="92e58-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="92e58-117">Klikněte pravým tlačítkem na *zobrazení/HelloWorld* složky a potom **Přidat > Nová položka**.</span><span class="sxs-lookup"><span data-stu-id="92e58-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="92e58-118">V **přidat novou položku - MvcMovie** dialogového okna</span><span class="sxs-lookup"><span data-stu-id="92e58-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="92e58-119">Do vyhledávacího pole v pravém horním rohu, zadejte *zobrazení*</span><span class="sxs-lookup"><span data-stu-id="92e58-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="92e58-120">Vyberte **zobrazení Razor**</span><span class="sxs-lookup"><span data-stu-id="92e58-120">Select **Razor View**</span></span>

  * <span data-ttu-id="92e58-121">Zachovat **název** pole hodnotu *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="92e58-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="92e58-122">Vyberte **přidat**</span><span class="sxs-lookup"><span data-stu-id="92e58-122">Select **Add**</span></span>

![Přidat novou položku – dialogové okno](adding-view/_static/add_view.png)

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="92e58-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="92e58-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="92e58-125">Přidat `Index` zobrazit `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="92e58-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="92e58-126">Přidat novou složku s názvem *zobrazení/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="92e58-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="92e58-127">Přidat nový soubor, který *zobrazení/HelloWorld* název složky *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="92e58-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="92e58-128">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="92e58-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="92e58-129">Klikněte pravým tlačítkem na *zobrazení* složky a potom **Přidat > Nová složka** a pojmenujte složku *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="92e58-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="92e58-130">Klikněte pravým tlačítkem na *zobrazení/HelloWorld* složky a potom **Přidat > Nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="92e58-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="92e58-131">V **nový soubor** dialogové okno:</span><span class="sxs-lookup"><span data-stu-id="92e58-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="92e58-132">Vyberte **webové** v levém podokně.</span><span class="sxs-lookup"><span data-stu-id="92e58-132">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="92e58-133">Vyberte **prázdný HTML soubor** v prostředním podokně.</span><span class="sxs-lookup"><span data-stu-id="92e58-133">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="92e58-134">Typ *Index.cshtml* v **název** pole.</span><span class="sxs-lookup"><span data-stu-id="92e58-134">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="92e58-135">Vyberte **nové**.</span><span class="sxs-lookup"><span data-stu-id="92e58-135">Select **New**.</span></span>

![Přidat novou položku – dialogové okno](adding-view/_static/add_view.png)

---  
<!-- End of VS tabs -->

<span data-ttu-id="92e58-137">Nahraďte obsah *Views/HelloWorld/Index.cshtml* Razor zobrazení souboru následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="92e58-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="92e58-138">Přejděte na adresu `https://localhost:xxxx/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="92e58-138">Navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="92e58-139">`Index` Metoda ve `HelloWorldController` nedělalo většinu; spustili příkaz `return View();`, která zadané, že metoda by měla používat soubor šablony zobrazení k vykreslení odezva do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="92e58-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="92e58-140">Protože jste nezadali explicitní název souboru šablony zobrazení, MVC na výchozí pomocí *Index.cshtml* zobrazení souboru v */zobrazení/HelloWorld* složky.</span><span class="sxs-lookup"><span data-stu-id="92e58-140">Because you didn't explicitly specify the name of the view template file, MVC defaulted to using the *Index.cshtml* view file in the */Views/HelloWorld* folder.</span></span> <span data-ttu-id="92e58-141">Následující obrázek ukazuje řetězec "Hello z našich zobrazit šablonu!"</span><span class="sxs-lookup"><span data-stu-id="92e58-141">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="92e58-142">pevně zakódované v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="92e58-142">hard-coded in the view.</span></span>

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="92e58-144">Změnit zobrazení a rozložení stránky</span><span class="sxs-lookup"><span data-stu-id="92e58-144">Change views and layout pages</span></span>

<span data-ttu-id="92e58-145">Vyberte nabídky odkazy (**MvcMovie**, **Domů**, a **ochrany osobních údajů**).</span><span class="sxs-lookup"><span data-stu-id="92e58-145">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="92e58-146">Každá stránka zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="92e58-146">Each page shows the same menu layout.</span></span> <span data-ttu-id="92e58-147">Rozložení nabídky je implementována v *Views/Shared/_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="92e58-147">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="92e58-148">Otevřít *Views/Shared/_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="92e58-148">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="92e58-149">[Rozložení](xref:mvc/views/layout) šablony umožňují zadat kontejner rozložení HTML vašeho webu na jednom místě a použijte ji na několika stránkách ve vaší lokalitě.</span><span class="sxs-lookup"><span data-stu-id="92e58-149">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="92e58-150">Najít `@RenderBody()` řádku.</span><span class="sxs-lookup"><span data-stu-id="92e58-150">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="92e58-151">`RenderBody` je zástupný symbol, kde všechny v zobrazení konkrétní stránky, můžete vytvořit, zobrazit *zabalené* stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="92e58-151">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="92e58-152">Například, pokud jste vybrali **ochrany osobních údajů** odkaz, **Views/Home/Privacy.cshtml** je zobrazení vykresleno uvnitř `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="92e58-152">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="92e58-153">Změnit název, nabídek a zápatí odkaz v souboru rozložení</span><span class="sxs-lookup"><span data-stu-id="92e58-153">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="92e58-154">V názvu a zápatí prvky, změňte `MvcMovie` k `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="92e58-154">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="92e58-155">Změnit anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` k `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="92e58-155">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="92e58-156">Následující kód ukazuje zvýrazněné změny:</span><span class="sxs-lookup"><span data-stu-id="92e58-156">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="92e58-157">V předchozím kódu `asp-area` [atribut ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) byla vynechána, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="92e58-157">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="92e58-158">**Poznámka:**: `Movies` Kontroler není implementovaná.</span><span class="sxs-lookup"><span data-stu-id="92e58-158">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="92e58-159">V tomto okamžiku `Movie App` odkaz není funkční.</span><span class="sxs-lookup"><span data-stu-id="92e58-159">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="92e58-160">Uložte změny a vyberte **ochrany osobních údajů** odkaz.</span><span class="sxs-lookup"><span data-stu-id="92e58-160">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="92e58-161">Všimněte si, jak se zobrazuje jako nadpis informace o kartě prohlížeče **zásady ochrany osobních údajů – filmová aplikace** místo **zásady ochrany osobních údajů – Mvc Movie**:</span><span class="sxs-lookup"><span data-stu-id="92e58-161">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Ochrana osobních údajů karty](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="92e58-163">Vyberte **Domů** propojit a Všimněte si také zobrazit text nadpisu a ukotvení **filmová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="92e58-163">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="92e58-164">Jsme byli schopni jednou provést změnu v šabloně rozložení, a mít všechny stránky na webu nový text odkazu a nový název.</span><span class="sxs-lookup"><span data-stu-id="92e58-164">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="92e58-165">Zkontrolujte *Views/_ViewStart.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="92e58-165">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="92e58-166">*Views/_ViewStart.cshtml* souboru přináší *Views/Shared/_Layout.cshtml* soubor pro každé zobrazení.</span><span class="sxs-lookup"><span data-stu-id="92e58-166">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="92e58-167">`Layout` Vlastnost slouží k nastavení zobrazení jiné rozložení, nebo ji nastavte na `null` , použije se žádný soubor rozložení.</span><span class="sxs-lookup"><span data-stu-id="92e58-167">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="92e58-168">Změna názvu a `<h2>` elementu *Views/HelloWorld/Index.cshtml* zobrazení souboru:</span><span class="sxs-lookup"><span data-stu-id="92e58-168">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="92e58-169">Název a `<h2>` element se mírně liší, abyste si mohli zobrazit, které verze kódu změní zobrazení.</span><span class="sxs-lookup"><span data-stu-id="92e58-169">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="92e58-170">`ViewData["Title"] = "Movie List";` v kódu nad sad `Title` vlastnost `ViewData` slovník, který "Seznam video".</span><span class="sxs-lookup"><span data-stu-id="92e58-170">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="92e58-171">`Title` Vlastnost se používá v `<title>` prvek HTML na stránce rozložení:</span><span class="sxs-lookup"><span data-stu-id="92e58-171">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="92e58-172">Uložte změny a přejděte do `https://localhost:xxxx/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="92e58-172">Save the change and navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="92e58-173">Všimněte si, že došlo ke změně názvu prohlížeče, záhlaví primární a sekundární záhlaví.</span><span class="sxs-lookup"><span data-stu-id="92e58-173">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="92e58-174">(Pokud se nezobrazí změny v prohlížeči, pravděpodobně jste zobrazili obsah uložený v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="92e58-174">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="92e58-175">Stisknutím kláves Ctrl + F5 v prohlížeči k vynucení odpověď ze serveru, který se má načíst.) Název prohlížeče se vytvoří s `ViewData["Title"]` jsme si nastavili *Index.cshtml* zobrazit šablony a další "-video aplikace" přidá soubor rozložení.</span><span class="sxs-lookup"><span data-stu-id="92e58-175">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="92e58-176">Všimněte si také způsob, jak v obsahu *Index.cshtml* zobrazit šablonu byl sloučen s *Views/Shared/_Layout.cshtml* zobrazit šablonu a odpověď o jedné HTML byl odeslán do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="92e58-176">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="92e58-177">Rozložení šablony usnadňují skutečně provést změny, které platí pro všechny stránky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="92e58-177">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="92e58-178">Další informace najdete tady [rozložení](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="92e58-178">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Zobrazení seznamu Movie](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="92e58-180">Naše něco "data" (v tomto případě "Hello z našich zobrazit šablonu!"</span><span class="sxs-lookup"><span data-stu-id="92e58-180">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="92e58-181">zpráva) je pevně zakódované, i když.</span><span class="sxs-lookup"><span data-stu-id="92e58-181">message) is hard-coded, though.</span></span> <span data-ttu-id="92e58-182">Aplikace MVC má "V" (Zobrazit) a máte "C" (controller), ale žádné "M" (modelu) ještě.</span><span class="sxs-lookup"><span data-stu-id="92e58-182">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="92e58-183">Předání dat z Kontroleru zobrazení</span><span class="sxs-lookup"><span data-stu-id="92e58-183">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="92e58-184">Akce kontroleru jsou vyvolány v reakci na příchozí adrese URL žádosti.</span><span class="sxs-lookup"><span data-stu-id="92e58-184">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="92e58-185">Třída kontroleru je kód níž je zapsána, který zpracovává příchozí požadavky prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="92e58-185">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="92e58-186">Kontroler načte data ze zdroje dat a rozhodne, jaký typ odpověď k odeslání zpět do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="92e58-186">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="92e58-187">Zobrazit šablony lze použít z kontroleru pro generování a formátovat odpověď ve formátu HTML v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="92e58-187">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="92e58-188">Kontrolery odpovídají za poskytování dat vyžaduje, aby šablona zobrazení k vykreslení odpovědi.</span><span class="sxs-lookup"><span data-stu-id="92e58-188">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="92e58-189">Osvědčený postup: Zobrazit šablony by měl **není** provádění obchodní logiky nebo pracovat přímo s databází.</span><span class="sxs-lookup"><span data-stu-id="92e58-189">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="92e58-190">Zobrazit šablonu místo toho by měla fungovat jenom s data, která je poskytována kontroleru.</span><span class="sxs-lookup"><span data-stu-id="92e58-190">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="92e58-191">Zachování tohoto "oddělené oblasti zájmu" udržet kód čistý, možností intenzivního testování a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="92e58-191">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="92e58-192">V současné době `Welcome` metodu `HelloWorldController` třídy přijímá `name` a `ID` parametr a potom výstupy hodnoty přímo do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="92e58-192">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="92e58-193">Spíše než mít řadič vykreslení této odpovědi jako řetězec, změňte kontroler místo toho použít šablonu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="92e58-193">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="92e58-194">Šablona zobrazení generuje dynamické odpovědi, což znamená, že odpovídající částí dat musí být předán z kontroleru zobrazení k vygenerování odpovědi.</span><span class="sxs-lookup"><span data-stu-id="92e58-194">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="92e58-195">To udělat tak, že kontroler umístit dynamických dat (parametry), která vyžaduje zobrazení šablony `ViewData` slovník, který se pak můžou zobrazit šablonu.</span><span class="sxs-lookup"><span data-stu-id="92e58-195">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="92e58-196">V *HelloWorldController.cs*, změnit `Welcome` metoda pro přidání `Message` a `NumTimes` hodnota, která se `ViewData` slovníku.</span><span class="sxs-lookup"><span data-stu-id="92e58-196">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="92e58-197">`ViewData` Slovník je dynamický objekt, což znamená, že můžete použít libovolný typ; `ViewData` objekt nemá žádné definované vlastnosti, dokud je něco v ji vložíte.</span><span class="sxs-lookup"><span data-stu-id="92e58-197">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="92e58-198">[Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapují pojmenované parametry (`name` a `numTimes`) z řetězce dotazu do adresního řádku parametrům ve své metodě.</span><span class="sxs-lookup"><span data-stu-id="92e58-198">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="92e58-199">Kompletní *HelloWorldController.cs* souboru vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="92e58-199">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="92e58-200">`ViewData` Objekt dictionary, obsahuje data, která bude předána do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="92e58-200">The `ViewData` dictionary object contains data that will be passed to the view.</span></span> 

<span data-ttu-id="92e58-201">Vytvoření šablony úvodní zobrazení s názvem *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="92e58-201">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="92e58-202">Vytvoříte smyčka v *Welcome.cshtml* zobrazit šablonu, která se zobrazí "Hello" `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="92e58-202">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="92e58-203">Nahraďte obsah *Views/HelloWorld/Welcome.cshtml* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="92e58-203">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="92e58-204">Uložte změny a přejděte na následující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="92e58-204">Save your changes and browse to the following URL:</span></span>

`https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="92e58-205">Data přijatá z adresy URL a předat pomocí řadiče [vazač modelu MVC](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="92e58-205">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="92e58-206">Balíčky data do kontroleru `ViewData` slovníku a, které se objekt předá do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="92e58-206">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="92e58-207">Zobrazení pak vykreslí data ve formátu HTML v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="92e58-207">The view then renders the data as HTML to the browser.</span></span>

![Ochrana osobních údajů zobrazení úvodní popisek a frází Hello Rick zobrazí čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="92e58-209">V příkladu výše `ViewData` slovník byla použita k předání dat z kontroleru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="92e58-209">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="92e58-210">Později v tomto kurzu se používá model zobrazení k předání dat z kontroleru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="92e58-210">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="92e58-211">Přístup modelu zobrazení k předávání dat je obvykle mnohem upřednostňované nad `ViewData` slovníku přístup.</span><span class="sxs-lookup"><span data-stu-id="92e58-211">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="92e58-212">Zobrazit [použití položek ViewBag, ViewData a TempData ](http://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) Další informace.</span><span class="sxs-lookup"><span data-stu-id="92e58-212">See [When to use ViewBag, ViewData, or TempData ](http://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="92e58-213">V dalším kurzu se vytvoří databáze filmů.</span><span class="sxs-lookup"><span data-stu-id="92e58-213">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="92e58-214">[Předchozí](adding-controller.md)
> [další](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="92e58-214">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

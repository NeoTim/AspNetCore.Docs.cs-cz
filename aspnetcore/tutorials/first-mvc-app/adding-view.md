---
title: Přidání zobrazení do aplikace core MVC ASP.NET
author: rick-anderson
description: Přidání zobrazení do jednoduché aplikace ASP.NET Core MVC
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 5510fb6844452571ca764e21640f0bd16444c782
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660207"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="d2660-103">Přidání zobrazení do aplikace core MVC ASP.NET</span><span class="sxs-lookup"><span data-stu-id="d2660-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="d2660-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d2660-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d2660-105">V této části `HelloWorldController` upravíte třídu tak, aby používala soubory zobrazení [Razor](xref:mvc/views/razor) k čistému zapouzdření procesu generování odpovědí HTML klientovi.</span><span class="sxs-lookup"><span data-stu-id="d2660-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="d2660-106">Soubor šablony zobrazení vytvoříte pomocí razor.</span><span class="sxs-lookup"><span data-stu-id="d2660-106">You create a view template file using Razor.</span></span> <span data-ttu-id="d2660-107">Šablony zobrazení založené na holicích strojky mají příponu *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="d2660-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="d2660-108">Poskytují elegantní způsob, jak vytvořit výstup HTML s C#.</span><span class="sxs-lookup"><span data-stu-id="d2660-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="d2660-109">V současné `Index` době metoda vrátí řetězec se zprávou, která je pevně zakódována ve třídě kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d2660-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="d2660-110">Ve `HelloWorldController` třídě nahraďte metodu `Index` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d2660-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="d2660-111">Předchozí kód volá <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodu řadiče.</span><span class="sxs-lookup"><span data-stu-id="d2660-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="d2660-112">Používá šablonu zobrazení ke generování odpovědi HTML.</span><span class="sxs-lookup"><span data-stu-id="d2660-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="d2660-113">Metody kontroleru (označované také jako *metody akce*), například výše `Index` uvedená metoda, obecně vrátí (nebo <xref:Microsoft.AspNetCore.Mvc.IActionResult> třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.ActionResult>), nikoli typu. `string`</span><span class="sxs-lookup"><span data-stu-id="d2660-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="d2660-114">Přidání zobrazení</span><span class="sxs-lookup"><span data-stu-id="d2660-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2660-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2660-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d2660-116">Klikněte pravým tlačítkem myši na složku *Zobrazení* a potom **přidejte > novou složku** a pojmenujte složku *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="d2660-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="d2660-117">Klikněte pravým tlačítkem myši na složku *Zobrazení/HelloWorld* a potom **na přidat > novou položku**.</span><span class="sxs-lookup"><span data-stu-id="d2660-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="d2660-118">V dialogovém okně **Přidat novou položku – MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="d2660-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="d2660-119">Do vyhledávacího pole v pravém horním horním poli zadejte *zobrazení*</span><span class="sxs-lookup"><span data-stu-id="d2660-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="d2660-120">Vybrat **zobrazení břitva**</span><span class="sxs-lookup"><span data-stu-id="d2660-120">Select **Razor View**</span></span>

  * <span data-ttu-id="d2660-121">Zachovat hodnotu pole **Název,** *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d2660-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="d2660-122">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d2660-122">Select **Add**</span></span>

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2660-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2660-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d2660-125">Přidejte `Index` zobrazení `HelloWorldController`pro .</span><span class="sxs-lookup"><span data-stu-id="d2660-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="d2660-126">Přidejte novou složku s názvem *Zobrazení/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="d2660-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="d2660-127">Přidejte nový soubor do názvu složky *Views/HelloWorld* *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d2660-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2660-128">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d2660-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d2660-129">Klikněte pravým tlačítkem myši na složku *Zobrazení* a potom **přidejte > novou složku** a pojmenujte složku *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="d2660-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="d2660-130">Klikněte pravým tlačítkem myši na složku *Zobrazení/HelloWorld* a potom **na přidat > nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="d2660-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="d2660-131">V dialogovém okně **Nový soubor:**</span><span class="sxs-lookup"><span data-stu-id="d2660-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="d2660-132">V levém podokně vyberte **jádro technologie ASP .NET** Core.</span><span class="sxs-lookup"><span data-stu-id="d2660-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="d2660-133">V prostředním podokně vyberte **Zobrazit stránku MVC.**</span><span class="sxs-lookup"><span data-stu-id="d2660-133">Select **MVC View Page** in the center pane.</span></span>
  * <span data-ttu-id="d2660-134">Do pole **Název** zadejte *Rejstřík.*</span><span class="sxs-lookup"><span data-stu-id="d2660-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="d2660-135">Vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="d2660-135">Select **New**.</span></span>

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="d2660-137">Nahraďte obsah souboru *zobrazení Zobrazení/HelloWorld/Index.cshtml* holicí strojek následujícím:</span><span class="sxs-lookup"><span data-stu-id="d2660-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="d2660-138">Přejděte na adresu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="d2660-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="d2660-139">Metoda `Index` v `HelloWorldController` neudělal moc; spustil příkaz `return View();`, který specifikoval, že metoda by měla použít soubor šablony zobrazení k vykreslení odpovědi na prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="d2660-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="d2660-140">Vzhledem k tomu, že nebyl zadán název souboru šablony zobrazení, mvc výchozí použití výchozího souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="d2660-141">Výchozí soubor zobrazení má stejný název`Index`jako metoda ( ), takže v */Views/HelloWorld/Index.cshtml* se používá.</span><span class="sxs-lookup"><span data-stu-id="d2660-141">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="d2660-142">Na obrázku níže je zobrazen řetězec "Dobrý den, z našeho zobrazit šablony!"</span><span class="sxs-lookup"><span data-stu-id="d2660-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="d2660-143">pevně zakódované v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-143">hard-coded in the view.</span></span>

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="d2660-145">Změna zobrazení a stránek rozložení</span><span class="sxs-lookup"><span data-stu-id="d2660-145">Change views and layout pages</span></span>

<span data-ttu-id="d2660-146">Vyberte odkazy na nabídky (**MvcMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="d2660-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="d2660-147">Každá stránka zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="d2660-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="d2660-148">Rozložení nabídky je implementováno v souboru *Zobrazení/Sdílené/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="d2660-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="d2660-149">Otevřete soubor *Zobrazení/Sdílené/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="d2660-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="d2660-150">[Šablony rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejnerů HTML webu na jednom místě a pak ho použít na více stránkách webu.</span><span class="sxs-lookup"><span data-stu-id="d2660-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="d2660-151">Najděte `@RenderBody()` linku.</span><span class="sxs-lookup"><span data-stu-id="d2660-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="d2660-152">`RenderBody`je zástupný symbol, ve kterém se zobrazují všechny stránky specifické pro zobrazení, které vytvoříte, *zabalené* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="d2660-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="d2660-153">Pokud například vyberete odkaz **Ochrana osobních údajů,** zobrazení **Zobrazení/Domů/Privacy.cshtml** se vykreslí uvnitř `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="d2660-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="d2660-154">Změna odkazu na název, zápatí a nabídky v souboru rozložení</span><span class="sxs-lookup"><span data-stu-id="d2660-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="d2660-155">Nahraďte obsah souboru *Views/Shared/_Layout.cshtml* následujícími značkami.</span><span class="sxs-lookup"><span data-stu-id="d2660-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="d2660-156">Změny jsou zvýrazněny:</span><span class="sxs-lookup"><span data-stu-id="d2660-156">The changes are highlighted:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="d2660-157">Předchozí značky provedly následující změny:</span><span class="sxs-lookup"><span data-stu-id="d2660-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="d2660-158">3 výskyty `MvcMovie` `Movie App`až .</span><span class="sxs-lookup"><span data-stu-id="d2660-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="d2660-159">Kotevní prvek `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` na `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="d2660-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="d2660-160">V předchozích značkách `asp-area=""` byl vynechán atribut a hodnota atributu [Anchor Tag Helper,](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="d2660-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="d2660-161">**Poznámka:** `Movies` Řadič nebyl implementován.</span><span class="sxs-lookup"><span data-stu-id="d2660-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="d2660-162">V tomto okamžiku `Movie App` odkaz není funkční.</span><span class="sxs-lookup"><span data-stu-id="d2660-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="d2660-163">Uložte změny a vyberte odkaz **Soukromí.**</span><span class="sxs-lookup"><span data-stu-id="d2660-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="d2660-164">Všimněte si, jak název na kartě prohlížeče zobrazuje **Zásady ochrany osobních údajů - Aplikace Film** místo **Zásady ochrany osobních údajů - Mvc Movie**:</span><span class="sxs-lookup"><span data-stu-id="d2660-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Karta Ochrana osobních údajů](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="d2660-166">Vyberte odkaz **Domů** a všimněte si, že název a kotva text také zobrazit **Film App**.</span><span class="sxs-lookup"><span data-stu-id="d2660-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="d2660-167">Byli jsme schopni provést změnu jednou v šabloně rozložení a všechny stránky na webu odrážejí nový text odkazu a nový název.</span><span class="sxs-lookup"><span data-stu-id="d2660-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="d2660-168">Prohlédněte si soubor *Views/_ViewStart.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="d2660-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="d2660-169">Soubor *Views/_ViewStart.cshtml* přináší do každého zobrazení soubor *Zobrazení/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="d2660-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="d2660-170">Vlastnost `Layout` lze použít k nastavení jiného zobrazení rozložení `null` nebo k jeho nastavení tak, aby nebyl použit žádný soubor rozložení.</span><span class="sxs-lookup"><span data-stu-id="d2660-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="d2660-171">Změňte název `<h2>` a prvek souboru *zobrazení/HelloWorld/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="d2660-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="d2660-172">Název a `<h2>` prvek se mírně liší, takže můžete vidět, který bit kódu změní zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="d2660-173">`ViewData["Title"] = "Movie List";`ve výše uvedeném `Title` kódu `ViewData` nastaví vlastnost slovníku na "Seznam filmů".</span><span class="sxs-lookup"><span data-stu-id="d2660-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="d2660-174">Vlastnost `Title` se používá `<title>` v elementu HTML na stránce rozložení:</span><span class="sxs-lookup"><span data-stu-id="d2660-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="d2660-175">Uložte změnu `https://localhost:{PORT}/HelloWorld`a přejděte na .</span><span class="sxs-lookup"><span data-stu-id="d2660-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="d2660-176">Všimněte si, že název prohlížeče, primární nadpis a sekundární nadpisy se změnily.</span><span class="sxs-lookup"><span data-stu-id="d2660-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="d2660-177">(Pokud změny v prohlížeči nevidíte, je možné, že si prohlížíte obsah uložený v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="d2660-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="d2660-178">Stisknutím kláves Ctrl+F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče je `ViewData["Title"]` vytvořen s jsme nastavit v šabloně zobrazení *Index.cshtml* a další "- Movie App" přidán do souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="d2660-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="d2660-179">Obsah v šabloně zobrazení *Index.cshtml* je sloučen se šablonou *zobrazení Zobrazení/Sdílené/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="d2660-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="d2660-180">Do prohlížeče je odeslána jedna odpověď HTML.</span><span class="sxs-lookup"><span data-stu-id="d2660-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="d2660-181">Šablony rozložení usnadňují provádění změn, které se použijí na všech stránkách aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2660-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="d2660-182">Další informace naleznete v [tématu Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="d2660-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="d2660-184">Náš malý kousek "data" (v tomto případě "Dobrý den, z našeho zobrazení šablony!"</span><span class="sxs-lookup"><span data-stu-id="d2660-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="d2660-185">zpráva) je však pevně zakódována.</span><span class="sxs-lookup"><span data-stu-id="d2660-185">message) is hard-coded, though.</span></span> <span data-ttu-id="d2660-186">Aplikace MVC má "V" (zobrazení) a máte "C" (řadič), ale zatím žádné "M" (model).</span><span class="sxs-lookup"><span data-stu-id="d2660-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="d2660-187">Předávání dat z řadiče do zobrazení</span><span class="sxs-lookup"><span data-stu-id="d2660-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="d2660-188">Akce kontroleru jsou vyvolány v reakci na příchozí požadavek url.</span><span class="sxs-lookup"><span data-stu-id="d2660-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="d2660-189">Třída kontroleru je místo, kde je napsán kód, který zpracovává příchozí požadavky prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2660-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="d2660-190">Správce načte data ze zdroje dat a rozhodne, jaký typ odpovědi má být odeslán zpět do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2660-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="d2660-191">Zobrazit šablony lze použít z řadiče pro generování a formátování odpovědi HTML do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2660-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="d2660-192">Správci jsou zodpovědní za poskytnutí údajů požadovaných k vytvoření šablony zobrazení k vykreslení odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d2660-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="d2660-193">Osvědčený postup: Zobrazit šablony by **neměly** provádět obchodní logiku nebo pracovat přímo s databází.</span><span class="sxs-lookup"><span data-stu-id="d2660-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="d2660-194">Šablona zobrazení by měla fungovat pouze s daty, která jí poskytuje kontrolor.</span><span class="sxs-lookup"><span data-stu-id="d2660-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="d2660-195">Udržování tohoto "oddělení obavy" pomáhá udržovat kód čistý, testovatelný a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="d2660-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="d2660-196">V současné `Welcome` době metoda `HelloWorldController` ve `name` třídě `ID` trvá a a parametr a pak výstupy hodnoty přímo do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2660-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="d2660-197">Spíše než mít řadič vykreslit tuto odpověď jako řetězec, změňte řadič použít šablonu zobrazení místo.</span><span class="sxs-lookup"><span data-stu-id="d2660-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="d2660-198">Šablona zobrazení generuje dynamickou odpověď, což znamená, že příslušné bity dat musí být předány z řadiče do zobrazení, aby bylo možné generovat odpověď.</span><span class="sxs-lookup"><span data-stu-id="d2660-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="d2660-199">To tím, že řadič umístit dynamická data (parametry), `ViewData` které šablona zobrazení potřebuje ve slovníku, který šablona zobrazení pak přístup.</span><span class="sxs-lookup"><span data-stu-id="d2660-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="d2660-200">V *HelloWorldController.cs*změňte `Welcome` metodu `Message` `NumTimes` pro přidání `ViewData` a hodnoty do slovníku.</span><span class="sxs-lookup"><span data-stu-id="d2660-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="d2660-201">Slovník `ViewData` je dynamický objekt, což znamená, že lze použít libovolný typ; objekt `ViewData` nemá žádné definované vlastnosti, dokud do něj něco nevložíte.</span><span class="sxs-lookup"><span data-stu-id="d2660-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="d2660-202">[Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry (`name` a `numTimes`) z řetězce dotazu v adresním řádku na parametry ve vaší metodě.</span><span class="sxs-lookup"><span data-stu-id="d2660-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="d2660-203">Kompletní *soubor HelloWorldController.cs* vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="d2660-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="d2660-204">Objekt `ViewData` slovníku obsahuje data, která budou předána do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="d2660-205">Vytvořte úvodní šablonu zobrazení s názvem *Zobrazení/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d2660-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="d2660-206">Vytvoříte smyčku v šabloně zobrazení *Welcome.cshtml,* která zobrazuje "Hello" `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="d2660-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="d2660-207">Nahraďte obsah *zobrazení/HelloWorld/Welcome.cshtml* následujícím:</span><span class="sxs-lookup"><span data-stu-id="d2660-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="d2660-208">Uložte změny a přejděte na následující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="d2660-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="d2660-209">Data jsou převzata z adresy URL a předána kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="d2660-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="d2660-210">Řadič zabalí data `ViewData` do slovníku a předá tento objekt do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="d2660-211">Zobrazení pak vykreslí data jako HTML do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2660-211">The view then renders the data as HTML to the browser.</span></span>

![Zobrazení ochrany osobních údajů zobrazující uvítací štítek a frázi Hello Rick zobrazenou čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="d2660-213">Ve výše uvedeném `ViewData` příkladu byl slovník použit k předání dat z řadiče do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="d2660-214">Později v kurzu se model zobrazení používá k předání dat z řadiče do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="d2660-215">Přístup modelu zobrazení k předávání dat je `ViewData` obecně velmi upřednostňován před slovníkovým přístupem.</span><span class="sxs-lookup"><span data-stu-id="d2660-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="d2660-216">Další informace naleznete v tématu [When to use ViewBag, ViewData nebo TempData.](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)</span><span class="sxs-lookup"><span data-stu-id="d2660-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="d2660-217">V dalším kurzu je vytvořena databáze filmů.</span><span class="sxs-lookup"><span data-stu-id="d2660-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d2660-218">[Předchozí](adding-controller.md)
> [další](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="d2660-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d2660-219">V této části `HelloWorldController` upravíte třídu tak, aby používala soubory zobrazení [Razor](xref:mvc/views/razor) k čistému zapouzdření procesu generování odpovědí HTML klientovi.</span><span class="sxs-lookup"><span data-stu-id="d2660-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="d2660-220">Soubor šablony zobrazení vytvoříte pomocí razor.</span><span class="sxs-lookup"><span data-stu-id="d2660-220">You create a view template file using Razor.</span></span> <span data-ttu-id="d2660-221">Šablony zobrazení založené na holicích strojky mají příponu *.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="d2660-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="d2660-222">Poskytují elegantní způsob, jak vytvořit výstup HTML s C#.</span><span class="sxs-lookup"><span data-stu-id="d2660-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="d2660-223">V současné `Index` době metoda vrátí řetězec se zprávou, která je pevně zakódována ve třídě kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d2660-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="d2660-224">Ve `HelloWorldController` třídě nahraďte metodu `Index` následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d2660-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="d2660-225">Předchozí kód volá <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodu řadiče.</span><span class="sxs-lookup"><span data-stu-id="d2660-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="d2660-226">Používá šablonu zobrazení ke generování odpovědi HTML.</span><span class="sxs-lookup"><span data-stu-id="d2660-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="d2660-227">Metody kontroleru (označované také jako *metody akce*), například výše `Index` uvedená metoda, obecně vrátí (nebo <xref:Microsoft.AspNetCore.Mvc.IActionResult> třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.ActionResult>), nikoli typu. `string`</span><span class="sxs-lookup"><span data-stu-id="d2660-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="d2660-228">Přidání zobrazení</span><span class="sxs-lookup"><span data-stu-id="d2660-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2660-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2660-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d2660-230">Klikněte pravým tlačítkem myši na složku *Zobrazení* a potom **přidejte > novou složku** a pojmenujte složku *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="d2660-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="d2660-231">Klikněte pravým tlačítkem myši na složku *Zobrazení/HelloWorld* a potom **na přidat > novou položku**.</span><span class="sxs-lookup"><span data-stu-id="d2660-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="d2660-232">V dialogovém okně **Přidat novou položku – MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="d2660-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="d2660-233">Do vyhledávacího pole v pravém horním horním poli zadejte *zobrazení*</span><span class="sxs-lookup"><span data-stu-id="d2660-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="d2660-234">Vybrat **zobrazení břitva**</span><span class="sxs-lookup"><span data-stu-id="d2660-234">Select **Razor View**</span></span>

  * <span data-ttu-id="d2660-235">Zachovat hodnotu pole **Název,** *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d2660-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="d2660-236">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="d2660-236">Select **Add**</span></span>

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2660-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2660-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d2660-239">Přidejte `Index` zobrazení `HelloWorldController`pro .</span><span class="sxs-lookup"><span data-stu-id="d2660-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="d2660-240">Přidejte novou složku s názvem *Zobrazení/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="d2660-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="d2660-241">Přidejte nový soubor do názvu složky *Views/HelloWorld* *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d2660-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2660-242">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d2660-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d2660-243">Klikněte pravým tlačítkem myši na složku *Zobrazení* a potom **přidejte > novou složku** a pojmenujte složku *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="d2660-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="d2660-244">Klikněte pravým tlačítkem myši na složku *Zobrazení/HelloWorld* a potom **na přidat > nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="d2660-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="d2660-245">V dialogovém okně **Nový soubor:**</span><span class="sxs-lookup"><span data-stu-id="d2660-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="d2660-246">V levém podokně vyberte **Web.**</span><span class="sxs-lookup"><span data-stu-id="d2660-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="d2660-247">V prostředním podokně vyberte **Vyprázdnit soubor HTML.**</span><span class="sxs-lookup"><span data-stu-id="d2660-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="d2660-248">Do pole Název zadejte *Soubor Index.cshtml.* **Name**</span><span class="sxs-lookup"><span data-stu-id="d2660-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="d2660-249">Vyberte **nový**.</span><span class="sxs-lookup"><span data-stu-id="d2660-249">Select **New**.</span></span>

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="d2660-251">Nahraďte obsah souboru *zobrazení Zobrazení/HelloWorld/Index.cshtml* holicí strojek následujícím:</span><span class="sxs-lookup"><span data-stu-id="d2660-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="d2660-252">Přejděte na adresu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="d2660-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="d2660-253">Metoda `Index` v `HelloWorldController` neudělal moc; spustil příkaz `return View();`, který specifikoval, že metoda by měla použít soubor šablony zobrazení k vykreslení odpovědi na prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="d2660-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="d2660-254">Vzhledem k tomu, že nebyl zadán název souboru šablony zobrazení, mvc výchozí použití výchozího souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="d2660-255">Výchozí soubor zobrazení má stejný název`Index`jako metoda ( ), takže v */Views/HelloWorld/Index.cshtml* se používá.</span><span class="sxs-lookup"><span data-stu-id="d2660-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="d2660-256">Na obrázku níže je zobrazen řetězec "Dobrý den, z našeho zobrazit šablony!"</span><span class="sxs-lookup"><span data-stu-id="d2660-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="d2660-257">pevně zakódované v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-257">hard-coded in the view.</span></span>

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="d2660-259">Změna zobrazení a stránek rozložení</span><span class="sxs-lookup"><span data-stu-id="d2660-259">Change views and layout pages</span></span>

<span data-ttu-id="d2660-260">Vyberte odkazy na nabídky (**MvcMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="d2660-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="d2660-261">Každá stránka zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="d2660-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="d2660-262">Rozložení nabídky je implementováno v souboru *Zobrazení/Sdílené/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="d2660-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="d2660-263">Otevřete soubor *Zobrazení/Sdílené/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="d2660-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="d2660-264">[Šablony rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejnerů HTML webu na jednom místě a pak ho použít na více stránkách webu.</span><span class="sxs-lookup"><span data-stu-id="d2660-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="d2660-265">Najděte `@RenderBody()` linku.</span><span class="sxs-lookup"><span data-stu-id="d2660-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="d2660-266">`RenderBody`je zástupný symbol, ve kterém se zobrazují všechny stránky specifické pro zobrazení, které vytvoříte, *zabalené* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="d2660-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="d2660-267">Pokud například vyberete odkaz **Ochrana osobních údajů,** zobrazení **Zobrazení/Domů/Privacy.cshtml** se vykreslí uvnitř `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="d2660-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="d2660-268">Změna odkazu na název, zápatí a nabídky v souboru rozložení</span><span class="sxs-lookup"><span data-stu-id="d2660-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="d2660-269">V elementech title a `MvcMovie` footer změňte na `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="d2660-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="d2660-270">Změňte prvek `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`ukotvení na .</span><span class="sxs-lookup"><span data-stu-id="d2660-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="d2660-271">Následující značky zobrazují zvýrazněné změny:</span><span class="sxs-lookup"><span data-stu-id="d2660-271">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="d2660-272">V předchozích značkách `asp-area` byl atribut [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) vynechán, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="d2660-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="d2660-273">**Poznámka:** `Movies` Řadič nebyl implementován.</span><span class="sxs-lookup"><span data-stu-id="d2660-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="d2660-274">V tomto okamžiku `Movie App` odkaz není funkční.</span><span class="sxs-lookup"><span data-stu-id="d2660-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="d2660-275">Uložte změny a vyberte odkaz **Soukromí.**</span><span class="sxs-lookup"><span data-stu-id="d2660-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="d2660-276">Všimněte si, jak název na kartě prohlížeče zobrazuje **Zásady ochrany osobních údajů - Aplikace Film** místo **Zásady ochrany osobních údajů - Mvc Movie**:</span><span class="sxs-lookup"><span data-stu-id="d2660-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Karta Ochrana osobních údajů](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="d2660-278">Vyberte odkaz **Domů** a všimněte si, že název a kotva text také zobrazit **Film App**.</span><span class="sxs-lookup"><span data-stu-id="d2660-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="d2660-279">Byli jsme schopni provést změnu jednou v šabloně rozložení a všechny stránky na webu odrážejí nový text odkazu a nový název.</span><span class="sxs-lookup"><span data-stu-id="d2660-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="d2660-280">Prohlédněte si soubor *Views/_ViewStart.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="d2660-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="d2660-281">Soubor *Views/_ViewStart.cshtml* přináší do každého zobrazení soubor *Zobrazení/Shared/_Layout.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="d2660-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="d2660-282">Vlastnost `Layout` lze použít k nastavení jiného zobrazení rozložení `null` nebo k jeho nastavení tak, aby nebyl použit žádný soubor rozložení.</span><span class="sxs-lookup"><span data-stu-id="d2660-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="d2660-283">Změňte název `<h2>` a prvek souboru *zobrazení/HelloWorld/Index.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="d2660-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="d2660-284">Název a `<h2>` prvek se mírně liší, takže můžete vidět, který bit kódu změní zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="d2660-285">`ViewData["Title"] = "Movie List";`ve výše uvedeném `Title` kódu `ViewData` nastaví vlastnost slovníku na "Seznam filmů".</span><span class="sxs-lookup"><span data-stu-id="d2660-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="d2660-286">Vlastnost `Title` se používá `<title>` v elementu HTML na stránce rozložení:</span><span class="sxs-lookup"><span data-stu-id="d2660-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="d2660-287">Uložte změnu `https://localhost:{PORT}/HelloWorld`a přejděte na .</span><span class="sxs-lookup"><span data-stu-id="d2660-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="d2660-288">Všimněte si, že název prohlížeče, primární nadpis a sekundární nadpisy se změnily.</span><span class="sxs-lookup"><span data-stu-id="d2660-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="d2660-289">(Pokud změny v prohlížeči nevidíte, je možné, že si prohlížíte obsah uložený v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="d2660-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="d2660-290">Stisknutím kláves Ctrl+F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče je `ViewData["Title"]` vytvořen s jsme nastavit v šabloně zobrazení *Index.cshtml* a další "- Movie App" přidán do souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="d2660-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="d2660-291">Všimněte si také, jak byl obsah v šabloně zobrazení *Index.cshtml* sloučen se šablonou *zobrazení Zobrazení/Sdílené/_Layout.cshtml* a do prohlížeče byla odeslána jedna odpověď HTML.</span><span class="sxs-lookup"><span data-stu-id="d2660-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="d2660-292">Šablony rozložení usnadňují provádění změn, které se použijí na všech stránkách aplikace.</span><span class="sxs-lookup"><span data-stu-id="d2660-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="d2660-293">Další informace naleznete v [tématu Rozložení](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="d2660-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="d2660-295">Náš malý kousek "data" (v tomto případě "Dobrý den, z našeho zobrazení šablony!"</span><span class="sxs-lookup"><span data-stu-id="d2660-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="d2660-296">zpráva) je však pevně zakódována.</span><span class="sxs-lookup"><span data-stu-id="d2660-296">message) is hard-coded, though.</span></span> <span data-ttu-id="d2660-297">Aplikace MVC má "V" (zobrazení) a máte "C" (řadič), ale zatím žádné "M" (model).</span><span class="sxs-lookup"><span data-stu-id="d2660-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="d2660-298">Předávání dat z řadiče do zobrazení</span><span class="sxs-lookup"><span data-stu-id="d2660-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="d2660-299">Akce kontroleru jsou vyvolány v reakci na příchozí požadavek url.</span><span class="sxs-lookup"><span data-stu-id="d2660-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="d2660-300">Třída kontroleru je místo, kde je napsán kód, který zpracovává příchozí požadavky prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2660-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="d2660-301">Správce načte data ze zdroje dat a rozhodne, jaký typ odpovědi má být odeslán zpět do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2660-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="d2660-302">Zobrazit šablony lze použít z řadiče pro generování a formátování odpovědi HTML do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2660-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="d2660-303">Správci jsou zodpovědní za poskytnutí údajů požadovaných k vytvoření šablony zobrazení k vykreslení odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d2660-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="d2660-304">Osvědčený postup: Zobrazit šablony by **neměly** provádět obchodní logiku nebo pracovat přímo s databází.</span><span class="sxs-lookup"><span data-stu-id="d2660-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="d2660-305">Šablona zobrazení by měla fungovat pouze s daty, která jí poskytuje kontrolor.</span><span class="sxs-lookup"><span data-stu-id="d2660-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="d2660-306">Udržování tohoto "oddělení obavy" pomáhá udržovat kód čistý, testovatelný a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="d2660-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="d2660-307">V současné `Welcome` době metoda `HelloWorldController` ve `name` třídě `ID` trvá a a parametr a pak výstupy hodnoty přímo do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2660-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="d2660-308">Spíše než mít řadič vykreslit tuto odpověď jako řetězec, změňte řadič použít šablonu zobrazení místo.</span><span class="sxs-lookup"><span data-stu-id="d2660-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="d2660-309">Šablona zobrazení generuje dynamickou odpověď, což znamená, že příslušné bity dat musí být předány z řadiče do zobrazení, aby bylo možné generovat odpověď.</span><span class="sxs-lookup"><span data-stu-id="d2660-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="d2660-310">To tím, že řadič umístit dynamická data (parametry), `ViewData` které šablona zobrazení potřebuje ve slovníku, který šablona zobrazení pak přístup.</span><span class="sxs-lookup"><span data-stu-id="d2660-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="d2660-311">V *HelloWorldController.cs*změňte `Welcome` metodu `Message` `NumTimes` pro přidání `ViewData` a hodnoty do slovníku.</span><span class="sxs-lookup"><span data-stu-id="d2660-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="d2660-312">Slovník `ViewData` je dynamický objekt, což znamená, že lze použít libovolný typ; objekt `ViewData` nemá žádné definované vlastnosti, dokud do něj něco nevložíte.</span><span class="sxs-lookup"><span data-stu-id="d2660-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="d2660-313">[Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry (`name` a `numTimes`) z řetězce dotazu v adresním řádku na parametry ve vaší metodě.</span><span class="sxs-lookup"><span data-stu-id="d2660-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="d2660-314">Kompletní *soubor HelloWorldController.cs* vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="d2660-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="d2660-315">Objekt `ViewData` slovníku obsahuje data, která budou předána do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="d2660-316">Vytvořte úvodní šablonu zobrazení s názvem *Zobrazení/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d2660-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="d2660-317">Vytvoříte smyčku v šabloně zobrazení *Welcome.cshtml,* která zobrazuje "Hello" `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="d2660-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="d2660-318">Nahraďte obsah *zobrazení/HelloWorld/Welcome.cshtml* následujícím:</span><span class="sxs-lookup"><span data-stu-id="d2660-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="d2660-319">Uložte změny a přejděte na následující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="d2660-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="d2660-320">Data jsou převzata z adresy URL a předána kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="d2660-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="d2660-321">Řadič zabalí data `ViewData` do slovníku a předá tento objekt do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="d2660-322">Zobrazení pak vykreslí data jako HTML do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2660-322">The view then renders the data as HTML to the browser.</span></span>

![Zobrazení ochrany osobních údajů zobrazující uvítací štítek a frázi Hello Rick zobrazenou čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="d2660-324">Ve výše uvedeném `ViewData` příkladu byl slovník použit k předání dat z řadiče do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="d2660-325">Později v kurzu se model zobrazení používá k předání dat z řadiče do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d2660-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="d2660-326">Přístup modelu zobrazení k předávání dat je `ViewData` obecně velmi upřednostňován před slovníkovým přístupem.</span><span class="sxs-lookup"><span data-stu-id="d2660-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="d2660-327">Další informace naleznete v tématu [When to use ViewBag, ViewData nebo TempData.](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)</span><span class="sxs-lookup"><span data-stu-id="d2660-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="d2660-328">V dalším kurzu je vytvořena databáze filmů.</span><span class="sxs-lookup"><span data-stu-id="d2660-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d2660-329">[Předchozí](adding-controller.md)
> [další](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="d2660-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

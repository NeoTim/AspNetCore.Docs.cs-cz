---
title: Přidání zobrazení do ASP.NET Core aplikace MVC
author: rick-anderson
description: Přidání zobrazení do jednoduché ASP.NET Core aplikace MVC
ms.author: riande
ms.date: 8/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 233d6cc060481c7b2efc9a1babc12f60f457a075
ms.sourcegitcommit: 05490855e0c70565f0c4b509d392b0828bcfd141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507332"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="a6397-103">Přidání zobrazení do ASP.NET Core aplikace MVC</span><span class="sxs-lookup"><span data-stu-id="a6397-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="a6397-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a6397-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a6397-105">V této části upravíte `HelloWorldController` třídu pro použití [Razor](xref:mvc/views/razor) zobrazení souborů k čistě zapouzdření procesu generování odpovědí HTML na klienta.</span><span class="sxs-lookup"><span data-stu-id="a6397-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="a6397-106">Vytvoříte soubor šablony zobrazení pomocí Razor .</span><span class="sxs-lookup"><span data-stu-id="a6397-106">You create a view template file using Razor.</span></span> Razor<span data-ttu-id="a6397-107">šablony zobrazení založené na bázi mají příponu souboru *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6397-107">-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="a6397-108">Poskytují elegantní způsob, jak vytvořit výstup HTML pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="a6397-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="a6397-109">V současné době `Index` metoda vrací řetězec se zprávou, která je pevně zakódována ve třídě Controller.</span><span class="sxs-lookup"><span data-stu-id="a6397-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="a6397-110">Ve `HelloWorldController` třídě nahraďte `Index` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a6397-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="a6397-111">Předchozí kód volá <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a6397-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="a6397-112">Pomocí šablony zobrazení vygeneruje odpověď HTML.</span><span class="sxs-lookup"><span data-stu-id="a6397-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="a6397-113">Metody kontroleru (označované také jako *metody akcí*), jako je například `Index` metoda výše, obecně vracejí výjimku <xref:Microsoft.AspNetCore.Mvc.IActionResult> (nebo třídu odvozenou od <xref:Microsoft.AspNetCore.Mvc.ActionResult> ), nikoli typ, jako `string` je například.</span><span class="sxs-lookup"><span data-stu-id="a6397-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="a6397-114">Přidání zobrazení</span><span class="sxs-lookup"><span data-stu-id="a6397-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6397-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6397-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6397-116">Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a6397-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="a6397-117">Klikněte pravým tlačítkem na složku *views/HelloWorld* a **přidejte > novou položku**.</span><span class="sxs-lookup"><span data-stu-id="a6397-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="a6397-118">V dialogovém okně **Přidat novou položku – MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="a6397-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="a6397-119">Do vyhledávacího pole v pravém horním rohu zadejte *zobrazení.*</span><span class="sxs-lookup"><span data-stu-id="a6397-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="a6397-120">Vybrat \*\* Razor zobrazení\*\*</span><span class="sxs-lookup"><span data-stu-id="a6397-120">Select **Razor View**</span></span>

  * <span data-ttu-id="a6397-121">Ponechejte hodnotu pole **název** , *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a6397-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="a6397-122">Vyberte **Přidat**</span><span class="sxs-lookup"><span data-stu-id="a6397-122">Select **Add**</span></span>

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6397-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6397-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a6397-125">Přidejte `Index` zobrazení pro `HelloWorldController` .</span><span class="sxs-lookup"><span data-stu-id="a6397-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="a6397-126">Přidejte novou složku s názvem *views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a6397-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="a6397-127">Přidejte nový soubor do složky *views/HelloWorld* name *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a6397-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6397-128">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a6397-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a6397-129">Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a6397-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="a6397-130">Klikněte pravým tlačítkem na složku *views/HelloWorld* a pak **přidejte > nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="a6397-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="a6397-131">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="a6397-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="a6397-132">V levém podokně vyberte **ASP .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="a6397-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="a6397-133">V prostředním podokně vyberte **stránku zobrazení MVC** .</span><span class="sxs-lookup"><span data-stu-id="a6397-133">Select **MVC View Page** in the center pane.</span></span>
  * <span data-ttu-id="a6397-134">Do pole **název** zadejte *index* .</span><span class="sxs-lookup"><span data-stu-id="a6397-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="a6397-135">Vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="a6397-135">Select **New**.</span></span>

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="a6397-137">Obsah zobrazení souboru views/ *HelloWorld/index. cshtml* nahraďte Razor následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="a6397-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="a6397-138">Přejděte na adresu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="a6397-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="a6397-139">`Index`Metoda v `HelloWorldController` nepříliš velkém rozsahu spustila příkaz `return View();` , který určuje, že metoda má použít soubor šablony zobrazení k vykreslení odpovědi do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a6397-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="a6397-140">Vzhledem k tomu, že není zadaný název souboru šablony zobrazení, MVC použije výchozí soubor zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="a6397-141">Výchozí soubor zobrazení má stejný název jako metoda ( `Index` ), takže se použije šablona zobrazení v */views/HelloWorld/index.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6397-141">The default view file has the same name as the method (`Index`), so the view template in */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="a6397-142">Následující obrázek ukazuje řetězec "Hello z naší šablony zobrazení".</span><span class="sxs-lookup"><span data-stu-id="a6397-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="a6397-143">pevně zakódovaný v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-143">hard-coded in the view.</span></span>

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="a6397-145">Změnit zobrazení a stránky rozložení</span><span class="sxs-lookup"><span data-stu-id="a6397-145">Change views and layout pages</span></span>

<span data-ttu-id="a6397-146">Vyberte odkazy nabídky (**MvcMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="a6397-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="a6397-147">Na každé stránce se zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="a6397-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="a6397-148">Rozložení nabídky je implementováno v souboru *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6397-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="a6397-149">Otevřete soubor *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6397-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a6397-150">Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu.</span><span class="sxs-lookup"><span data-stu-id="a6397-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="a6397-151">Najděte `@RenderBody()` řádek.</span><span class="sxs-lookup"><span data-stu-id="a6397-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="a6397-152">`RenderBody`je zástupný symbol, ve kterém se zobrazí všechny stránky specifické pro zobrazení, *zabalené* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="a6397-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="a6397-153">Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **/Domů/soukromí. cshtml** se vykreslí v rámci `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="a6397-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="a6397-154">Změna názvu, zápatí a odkazu v nabídce v souboru rozložení</span><span class="sxs-lookup"><span data-stu-id="a6397-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="a6397-155">Nahraďte obsah souborů *views/Shared/_Layout. cshtml* následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="a6397-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="a6397-156">Změny jsou zvýrazněny:</span><span class="sxs-lookup"><span data-stu-id="a6397-156">The changes are highlighted:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="a6397-157">Předchozí kód provedl následující změny:</span><span class="sxs-lookup"><span data-stu-id="a6397-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="a6397-158">3 výskyty `MvcMovie` do `Movie App` .</span><span class="sxs-lookup"><span data-stu-id="a6397-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="a6397-159">Prvek ukotvení `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` k `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` .</span><span class="sxs-lookup"><span data-stu-id="a6397-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="a6397-160">V předchozím kódu `asp-area=""` byl vynechán [atribut pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) a hodnota atributu, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="a6397-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="a6397-161">**Poznámka**: `Movies` kontroler nebyl implementován.</span><span class="sxs-lookup"><span data-stu-id="a6397-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="a6397-162">V tomto okamžiku není `Movie App` odkaz funkční.</span><span class="sxs-lookup"><span data-stu-id="a6397-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="a6397-163">Uložte změny a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="a6397-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="a6397-164">Všimněte si, jak se v nadpisu na kartě prohlížeč zobrazuje **Zásada ochrany osobních údajů – aplikace pro video** namísto **zásad ochrany osobních údajů – film MVC**:</span><span class="sxs-lookup"><span data-stu-id="a6397-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Karta soukromí](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="a6397-166">Vyberte odkaz **Domů** a Všimněte si, že text nadpisu a kotvy také zobrazuje **filmovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="a6397-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="a6397-167">Provedli jsme změnu jednou v šabloně rozložení a všechny stránky v lokalitě odrážely nový text odkazu a nový nadpis.</span><span class="sxs-lookup"><span data-stu-id="a6397-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="a6397-168">Projděte si soubor *views/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a6397-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="a6397-169">Soubor *views/_ViewStart. cshtml* přináší do každého zobrazení soubor views */shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6397-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="a6397-170">`Layout`Vlastnost může být použita k nastavení jiného zobrazení rozložení nebo ji lze nastavit tak, aby se `null` nepoužil žádný soubor rozložení.</span><span class="sxs-lookup"><span data-stu-id="a6397-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="a6397-171">Změňte název a `<h2>` element zobrazení */Hello/index. cshtml* zobrazení souboru:</span><span class="sxs-lookup"><span data-stu-id="a6397-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="a6397-172">Název a `<h2>` element se mírně liší, abyste viděli, který bit kódu se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="a6397-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="a6397-173">`ViewData["Title"] = "Movie List";`ve výše uvedeném kódu nastaví `Title` vlastnost `ViewData` slovníku na "seznam filmů".</span><span class="sxs-lookup"><span data-stu-id="a6397-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="a6397-174">`Title`Vlastnost se používá v `<title>` prvku HTML na stránce rozložení:</span><span class="sxs-lookup"><span data-stu-id="a6397-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="a6397-175">Uložte změnu a přejděte na `https://localhost:{PORT}/HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="a6397-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="a6397-176">Všimněte si, že se změnil název prohlížeče, primární nadpis a sekundární záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a6397-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="a6397-177">(Pokud nevidíte změny v prohlížeči, můžete zobrazit obsah uložený v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a6397-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="a6397-178">Stisknutím kombinace kláves CTRL + F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče se vytvoří se sadou, kterou `ViewData["Title"]` jsme nastavili v šabloně zobrazení *index. cshtml* a další "-filmové aplikace" přidané v souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="a6397-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="a6397-179">Obsah v šabloně zobrazení *index. cshtml* je sloučen s šablonou zobrazení View */shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6397-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="a6397-180">Do prohlížeče se pošle jedna odpověď HTML.</span><span class="sxs-lookup"><span data-stu-id="a6397-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="a6397-181">Šablony rozložení usnadňují provádění změn, které se vztahují na všechny stránky v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a6397-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="a6397-182">Další informace najdete v tématu [rozložení](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="a6397-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="a6397-184">Náš malý bit "data" (v tomto případě "Hello z naší šablony zobrazení"</span><span class="sxs-lookup"><span data-stu-id="a6397-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="a6397-185">zpráva) je pevně zakódována, i když.</span><span class="sxs-lookup"><span data-stu-id="a6397-185">message) is hard-coded, though.</span></span> <span data-ttu-id="a6397-186">Aplikace MVC má "V" (zobrazení) a Vy máte "C" (Controller), ale zatím ne "M" (model).</span><span class="sxs-lookup"><span data-stu-id="a6397-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="a6397-187">Předávání dat z kontroleru do zobrazení</span><span class="sxs-lookup"><span data-stu-id="a6397-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="a6397-188">Akce kontroleru se vyvolají v reakci na příchozí požadavek adresy URL.</span><span class="sxs-lookup"><span data-stu-id="a6397-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="a6397-189">Třída kontroleru je místo, kde je kód vytvořen, který zpracovává příchozí požadavky prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a6397-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="a6397-190">Kontroler načítá data ze zdroje dat a rozhoduje o tom, jaký typ reakce se má zpět do prohlížeče poslat zpátky.</span><span class="sxs-lookup"><span data-stu-id="a6397-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="a6397-191">Šablony zobrazení lze použít z kontroleru k vygenerování a formátování odpovědi HTML do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a6397-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="a6397-192">Řadiče jsou zodpovědné za poskytování dat vyžadovaných pro šablonu zobrazení, aby vygenerovala odpověď.</span><span class="sxs-lookup"><span data-stu-id="a6397-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="a6397-193">Osvědčený postup: šablony **zobrazení by neměly** provádět obchodní logiku ani pracovat s databází přímo.</span><span class="sxs-lookup"><span data-stu-id="a6397-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="a6397-194">Místo toho by šablona zobrazení měla fungovat jenom s daty, která mu poskytl kontroler.</span><span class="sxs-lookup"><span data-stu-id="a6397-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="a6397-195">Udržování tohoto "oddělení obav" pomáhá udržet kód čistě, testovatelné a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="a6397-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="a6397-196">V současné době `Welcome` Metoda ve `HelloWorldController` třídě přebírá `name` a `ID` parametr a následně výstup hodnot přímo do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a6397-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="a6397-197">Místo toho, aby kontroler tuto odpověď vygeneroval jako řetězec, změňte řadič tak, aby místo něj používal šablonu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="a6397-198">Šablona zobrazení vygeneruje dynamickou odpověď, což znamená, že pro vygenerování odpovědi musí být z kontroleru předány vhodné bity dat.</span><span class="sxs-lookup"><span data-stu-id="a6397-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="a6397-199">Provedete to tak, že řadič umístí dynamická data (parametry), která šablona zobrazení potřebuje, ve `ViewData` slovníku, ke kterému může přistupovat šablona zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="a6397-200">V *HelloWorldController.cs*změňte `Welcome` metodu tak, aby se do `Message` slovníku přidala `NumTimes` hodnota a `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="a6397-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="a6397-201">`ViewData`Slovník je dynamický objekt, což znamená, že lze použít jakýkoliv typ; `ViewData` objekt nemá žádné definované vlastnosti, dokud do něj nevložíte nějaký text.</span><span class="sxs-lookup"><span data-stu-id="a6397-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="a6397-202">[Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry ( `name` a `numTimes` ) z řetězce dotazu v adresním řádku na parametry v metodě.</span><span class="sxs-lookup"><span data-stu-id="a6397-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="a6397-203">Úplný soubor *HelloWorldController.cs* vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="a6397-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="a6397-204">`ViewData`Objekt Dictionary obsahuje data, která budou předána zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="a6397-205">Vytvořte šablonu zobrazení Vítejte s názvem *views/HelloWorld/Welcome. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a6397-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="a6397-206">Vytvoříte smyčku v šabloně zobrazení *Welcome. cshtml* , která zobrazí text Hello `NumTimes` .</span><span class="sxs-lookup"><span data-stu-id="a6397-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="a6397-207">Nahraďte obsah *zobrazení/HelloWorld/Welcome. cshtml* následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="a6397-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="a6397-208">Uložte změny a přejděte na následující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="a6397-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="a6397-209">Data jsou přijímána z adresy URL a předána do kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="a6397-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="a6397-210">Kontroler zabalí data do `ViewData` slovníku a předá tento objekt zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="a6397-211">Zobrazení pak data vykreslí jako HTML do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a6397-211">The view then renders the data as HTML to the browser.</span></span>

![Zobrazení osobních údajů zobrazuje úvodní popisek a frázi Hello Rick zobrazené čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="a6397-213">V ukázce výše `ViewData` byl slovník použit k předání dat z kontroleru do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="a6397-214">Později v tomto kurzu se používá model zobrazení k předávání dat z kontroleru do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="a6397-215">Přístup k modelu zobrazení pro předávání dat je obecně mnohem upřednostňovaný nad `ViewData` přístupem ke slovníku.</span><span class="sxs-lookup"><span data-stu-id="a6397-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="a6397-216">Další informace najdete v tématu [kdy použít ViewBag, ViewData nebo TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .</span><span class="sxs-lookup"><span data-stu-id="a6397-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="a6397-217">V dalším kurzu se vytvoří databáze filmů.</span><span class="sxs-lookup"><span data-stu-id="a6397-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a6397-218">[Předchozí](adding-controller.md) 
>  [Další](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="a6397-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a6397-219">V této části upravíte `HelloWorldController` třídu pro použití [Razor](xref:mvc/views/razor) zobrazení souborů k čistě zapouzdření procesu generování odpovědí HTML na klienta.</span><span class="sxs-lookup"><span data-stu-id="a6397-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="a6397-220">Vytvoříte soubor šablony zobrazení pomocí Razor .</span><span class="sxs-lookup"><span data-stu-id="a6397-220">You create a view template file using Razor.</span></span> Razor<span data-ttu-id="a6397-221">šablony zobrazení založené na bázi mají příponu souboru *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6397-221">-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="a6397-222">Poskytují elegantní způsob, jak vytvořit výstup HTML pomocí C#.</span><span class="sxs-lookup"><span data-stu-id="a6397-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="a6397-223">V současné době `Index` metoda vrací řetězec se zprávou, která je pevně zakódována ve třídě Controller.</span><span class="sxs-lookup"><span data-stu-id="a6397-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="a6397-224">Ve `HelloWorldController` třídě nahraďte `Index` metodu následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="a6397-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="a6397-225">Předchozí kód volá <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodu kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a6397-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="a6397-226">Pomocí šablony zobrazení vygeneruje odpověď HTML.</span><span class="sxs-lookup"><span data-stu-id="a6397-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="a6397-227">Metody kontroleru (označované také jako *metody akcí*), jako je například `Index` metoda výše, obecně vracejí výjimku <xref:Microsoft.AspNetCore.Mvc.IActionResult> (nebo třídu odvozenou od <xref:Microsoft.AspNetCore.Mvc.ActionResult> ), nikoli typ, jako `string` je například.</span><span class="sxs-lookup"><span data-stu-id="a6397-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="a6397-228">Přidání zobrazení</span><span class="sxs-lookup"><span data-stu-id="a6397-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6397-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6397-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6397-230">Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a6397-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="a6397-231">Klikněte pravým tlačítkem na složku *views/HelloWorld* a **přidejte > novou položku**.</span><span class="sxs-lookup"><span data-stu-id="a6397-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="a6397-232">V dialogovém okně **Přidat novou položku – MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="a6397-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="a6397-233">Do vyhledávacího pole v pravém horním rohu zadejte *zobrazení.*</span><span class="sxs-lookup"><span data-stu-id="a6397-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="a6397-234">Vybrat \*\* Razor zobrazení\*\*</span><span class="sxs-lookup"><span data-stu-id="a6397-234">Select **Razor View**</span></span>

  * <span data-ttu-id="a6397-235">Ponechejte hodnotu pole **název** , *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a6397-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="a6397-236">Vyberte **Přidat**</span><span class="sxs-lookup"><span data-stu-id="a6397-236">Select **Add**</span></span>

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a6397-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a6397-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a6397-239">Přidejte `Index` zobrazení pro `HelloWorldController` .</span><span class="sxs-lookup"><span data-stu-id="a6397-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="a6397-240">Přidejte novou složku s názvem *views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a6397-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="a6397-241">Přidejte nový soubor do složky *views/HelloWorld* name *index. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a6397-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a6397-242">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a6397-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a6397-243">Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="a6397-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="a6397-244">Klikněte pravým tlačítkem na složku *views/HelloWorld* a pak **přidejte > nový soubor**.</span><span class="sxs-lookup"><span data-stu-id="a6397-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="a6397-245">V dialogovém okně **nový soubor** :</span><span class="sxs-lookup"><span data-stu-id="a6397-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="a6397-246">V levém podokně vyberte **Web** .</span><span class="sxs-lookup"><span data-stu-id="a6397-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="a6397-247">V prostředním podokně vyberte **prázdný soubor HTML** .</span><span class="sxs-lookup"><span data-stu-id="a6397-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="a6397-248">Do pole **název** zadejte *index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6397-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="a6397-249">Vyberte **Nový**.</span><span class="sxs-lookup"><span data-stu-id="a6397-249">Select **New**.</span></span>

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="a6397-251">Obsah zobrazení souboru views/ *HelloWorld/index. cshtml* nahraďte Razor následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="a6397-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="a6397-252">Přejděte na adresu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="a6397-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="a6397-253">`Index`Metoda v `HelloWorldController` nepříliš velkém rozsahu spustila příkaz `return View();` , který určuje, že metoda má použít soubor šablony zobrazení k vykreslení odpovědi do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a6397-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="a6397-254">Vzhledem k tomu, že není zadaný název souboru šablony zobrazení, MVC použije výchozí soubor zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="a6397-255">Výchozí soubor zobrazení má stejný název jako metoda ( `Index` ), takže v */views/HelloWorld/index.cshtml* se používá.</span><span class="sxs-lookup"><span data-stu-id="a6397-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="a6397-256">Následující obrázek ukazuje řetězec "Hello z naší šablony zobrazení".</span><span class="sxs-lookup"><span data-stu-id="a6397-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="a6397-257">pevně zakódovaný v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-257">hard-coded in the view.</span></span>

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="a6397-259">Změnit zobrazení a stránky rozložení</span><span class="sxs-lookup"><span data-stu-id="a6397-259">Change views and layout pages</span></span>

<span data-ttu-id="a6397-260">Vyberte odkazy nabídky (**MvcMovie**, **Home**a **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="a6397-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="a6397-261">Na každé stránce se zobrazuje stejné rozložení nabídky.</span><span class="sxs-lookup"><span data-stu-id="a6397-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="a6397-262">Rozložení nabídky je implementováno v souboru *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6397-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="a6397-263">Otevřete soubor *views/Shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6397-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="a6397-264">Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu.</span><span class="sxs-lookup"><span data-stu-id="a6397-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="a6397-265">Najděte `@RenderBody()` řádek.</span><span class="sxs-lookup"><span data-stu-id="a6397-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="a6397-266">`RenderBody`je zástupný symbol, ve kterém se zobrazí všechny stránky specifické pro zobrazení, *zabalené* na stránce rozložení.</span><span class="sxs-lookup"><span data-stu-id="a6397-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="a6397-267">Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **/Domů/soukromí. cshtml** se vykreslí v rámci `RenderBody` metody.</span><span class="sxs-lookup"><span data-stu-id="a6397-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="a6397-268">Změna názvu, zápatí a odkazu v nabídce v souboru rozložení</span><span class="sxs-lookup"><span data-stu-id="a6397-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="a6397-269">V části nadpis a zápatí změňte `MvcMovie` na `Movie App` .</span><span class="sxs-lookup"><span data-stu-id="a6397-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="a6397-270">Změňte element kotvy `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` na `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` .</span><span class="sxs-lookup"><span data-stu-id="a6397-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="a6397-271">Následující kód ukazuje zvýrazněné změny:</span><span class="sxs-lookup"><span data-stu-id="a6397-271">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="a6397-272">V předchozím kódu `asp-area` byl [atribut pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) vynechán, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="a6397-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="a6397-273">**Poznámka**: `Movies` kontroler nebyl implementován.</span><span class="sxs-lookup"><span data-stu-id="a6397-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="a6397-274">V tomto okamžiku není `Movie App` odkaz funkční.</span><span class="sxs-lookup"><span data-stu-id="a6397-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="a6397-275">Uložte změny a vyberte odkaz na **ochranu osobních údajů** .</span><span class="sxs-lookup"><span data-stu-id="a6397-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="a6397-276">Všimněte si, jak se v nadpisu na kartě prohlížeč zobrazuje **Zásada ochrany osobních údajů – aplikace pro video** namísto **zásad ochrany osobních údajů – film MVC**:</span><span class="sxs-lookup"><span data-stu-id="a6397-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Karta soukromí](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="a6397-278">Vyberte odkaz **Domů** a Všimněte si, že text nadpisu a kotvy také zobrazuje **filmovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="a6397-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="a6397-279">Provedli jsme změnu jednou v šabloně rozložení a všechny stránky v lokalitě odrážely nový text odkazu a nový nadpis.</span><span class="sxs-lookup"><span data-stu-id="a6397-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="a6397-280">Projděte si soubor *views/_ViewStart. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="a6397-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="a6397-281">Soubor *views/_ViewStart. cshtml* přináší do každého zobrazení soubor views */shared/_Layout. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="a6397-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="a6397-282">`Layout`Vlastnost může být použita k nastavení jiného zobrazení rozložení nebo ji lze nastavit tak, aby se `null` nepoužil žádný soubor rozložení.</span><span class="sxs-lookup"><span data-stu-id="a6397-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="a6397-283">Změňte název a `<h2>` element zobrazení */Hello/index. cshtml* zobrazení souboru:</span><span class="sxs-lookup"><span data-stu-id="a6397-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="a6397-284">Název a `<h2>` element se mírně liší, abyste viděli, který bit kódu se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="a6397-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="a6397-285">`ViewData["Title"] = "Movie List";`ve výše uvedeném kódu nastaví `Title` vlastnost `ViewData` slovníku na "seznam filmů".</span><span class="sxs-lookup"><span data-stu-id="a6397-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="a6397-286">`Title`Vlastnost se používá v `<title>` prvku HTML na stránce rozložení:</span><span class="sxs-lookup"><span data-stu-id="a6397-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="a6397-287">Uložte změnu a přejděte na `https://localhost:{PORT}/HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="a6397-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="a6397-288">Všimněte si, že se změnil název prohlížeče, primární nadpis a sekundární záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a6397-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="a6397-289">(Pokud nevidíte změny v prohlížeči, můžete zobrazit obsah uložený v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a6397-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="a6397-290">Stisknutím kombinace kláves CTRL + F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče se vytvoří se sadou, kterou `ViewData["Title"]` jsme nastavili v šabloně zobrazení *index. cshtml* a další "-filmové aplikace" přidané v souboru rozložení.</span><span class="sxs-lookup"><span data-stu-id="a6397-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="a6397-291">Všimněte si také, jak byl obsah v šabloně zobrazení *index. cshtml* sloučen s šablonou zobrazení *zobrazení/shared/_Layout. cshtml* a že byla do prohlížeče odeslána jedna odpověď HTML.</span><span class="sxs-lookup"><span data-stu-id="a6397-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="a6397-292">Šablony rozložení umožňují snadno provádět změny, které se vztahují na všechny stránky aplikace.</span><span class="sxs-lookup"><span data-stu-id="a6397-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="a6397-293">Další informace najdete v tématu [rozložení](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="a6397-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="a6397-295">Náš malý bit "data" (v tomto případě "Hello z naší šablony zobrazení"</span><span class="sxs-lookup"><span data-stu-id="a6397-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="a6397-296">zpráva) je pevně zakódována, i když.</span><span class="sxs-lookup"><span data-stu-id="a6397-296">message) is hard-coded, though.</span></span> <span data-ttu-id="a6397-297">Aplikace MVC má "V" (zobrazení) a Vy máte "C" (Controller), ale zatím ne "M" (model).</span><span class="sxs-lookup"><span data-stu-id="a6397-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="a6397-298">Předávání dat z kontroleru do zobrazení</span><span class="sxs-lookup"><span data-stu-id="a6397-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="a6397-299">Akce kontroleru se vyvolají v reakci na příchozí požadavek adresy URL.</span><span class="sxs-lookup"><span data-stu-id="a6397-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="a6397-300">Třída kontroleru je místo, kde je kód vytvořen, který zpracovává příchozí požadavky prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a6397-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="a6397-301">Kontroler načítá data ze zdroje dat a rozhoduje o tom, jaký typ reakce se má zpět do prohlížeče poslat zpátky.</span><span class="sxs-lookup"><span data-stu-id="a6397-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="a6397-302">Šablony zobrazení lze použít z kontroleru k vygenerování a formátování odpovědi HTML do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a6397-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="a6397-303">Řadiče jsou zodpovědné za poskytování dat vyžadovaných pro šablonu zobrazení, aby vygenerovala odpověď.</span><span class="sxs-lookup"><span data-stu-id="a6397-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="a6397-304">Osvědčený postup: šablony **zobrazení by neměly** provádět obchodní logiku ani pracovat s databází přímo.</span><span class="sxs-lookup"><span data-stu-id="a6397-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="a6397-305">Místo toho by šablona zobrazení měla fungovat jenom s daty, která mu poskytl kontroler.</span><span class="sxs-lookup"><span data-stu-id="a6397-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="a6397-306">Udržování tohoto "oddělení obav" pomáhá udržet kód čistě, testovatelné a udržovatelný.</span><span class="sxs-lookup"><span data-stu-id="a6397-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="a6397-307">V současné době `Welcome` Metoda ve `HelloWorldController` třídě přebírá `name` a `ID` parametr a následně výstup hodnot přímo do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a6397-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="a6397-308">Místo toho, aby kontroler tuto odpověď vygeneroval jako řetězec, změňte řadič tak, aby místo něj používal šablonu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="a6397-309">Šablona zobrazení vygeneruje dynamickou odpověď, což znamená, že pro vygenerování odpovědi musí být z kontroleru předány vhodné bity dat.</span><span class="sxs-lookup"><span data-stu-id="a6397-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="a6397-310">Provedete to tak, že řadič umístí dynamická data (parametry), která šablona zobrazení potřebuje, ve `ViewData` slovníku, ke kterému může přistupovat šablona zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="a6397-311">V *HelloWorldController.cs*změňte `Welcome` metodu tak, aby se do `Message` slovníku přidala `NumTimes` hodnota a `ViewData` .</span><span class="sxs-lookup"><span data-stu-id="a6397-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="a6397-312">`ViewData`Slovník je dynamický objekt, což znamená, že lze použít jakýkoliv typ; `ViewData` objekt nemá žádné definované vlastnosti, dokud do něj nevložíte nějaký text.</span><span class="sxs-lookup"><span data-stu-id="a6397-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="a6397-313">[Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry ( `name` a `numTimes` ) z řetězce dotazu v adresním řádku na parametry v metodě.</span><span class="sxs-lookup"><span data-stu-id="a6397-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="a6397-314">Úplný soubor *HelloWorldController.cs* vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="a6397-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="a6397-315">`ViewData`Objekt Dictionary obsahuje data, která budou předána zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="a6397-316">Vytvořte šablonu zobrazení Vítejte s názvem *views/HelloWorld/Welcome. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a6397-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="a6397-317">Vytvoříte smyčku v šabloně zobrazení *Welcome. cshtml* , která zobrazí text Hello `NumTimes` .</span><span class="sxs-lookup"><span data-stu-id="a6397-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="a6397-318">Nahraďte obsah *zobrazení/HelloWorld/Welcome. cshtml* následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="a6397-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="a6397-319">Uložte změny a přejděte na následující adresu URL:</span><span class="sxs-lookup"><span data-stu-id="a6397-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="a6397-320">Data jsou přijímána z adresy URL a předána do kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="a6397-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="a6397-321">Kontroler zabalí data do `ViewData` slovníku a předá tento objekt zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="a6397-322">Zobrazení pak data vykreslí jako HTML do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a6397-322">The view then renders the data as HTML to the browser.</span></span>

![Zobrazení osobních údajů zobrazuje úvodní popisek a frázi Hello Rick zobrazené čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="a6397-324">V ukázce výše `ViewData` byl slovník použit k předání dat z kontroleru do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="a6397-325">Později v tomto kurzu se používá model zobrazení k předávání dat z kontroleru do zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a6397-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="a6397-326">Přístup k modelu zobrazení pro předávání dat je obecně mnohem upřednostňovaný nad `ViewData` přístupem ke slovníku.</span><span class="sxs-lookup"><span data-stu-id="a6397-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="a6397-327">Další informace najdete v tématu [kdy použít ViewBag, ViewData nebo TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .</span><span class="sxs-lookup"><span data-stu-id="a6397-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="a6397-328">V dalším kurzu se vytvoří databáze filmů.</span><span class="sxs-lookup"><span data-stu-id="a6397-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="a6397-329">[Předchozí](adding-controller.md) 
>  [Další](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="a6397-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

---
title: Tagování pomocníků ve formulářích ve ASP.NET Core
author: rick-anderson
description: Popisuje předdefinované pomocníky značek používané s formuláři.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: mvc/views/working-with-forms
ms.openlocfilehash: 61b50a63bd026f917035f64785d8d3b1956958a6
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880964"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="66ea0-103">Tagování pomocníků ve formulářích ve ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66ea0-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="66ea0-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette)a [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="66ea0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="66ea0-105">Tento dokument ukazuje práci s formuláři a prvky HTML, které se obvykle používají ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="66ea0-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="66ea0-106">Element [formuláře](https://www.w3.org/TR/html401/interact/forms.html) HTML poskytuje primárnímu mechanismu webové aplikace použití k odesílání dat na server.</span><span class="sxs-lookup"><span data-stu-id="66ea0-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="66ea0-107">Většina tohoto dokumentu popisuje [pomocníky značek](tag-helpers/intro.md) a způsob, jak vám může pomáhat s tvorbou robustních formulářů HTML.</span><span class="sxs-lookup"><span data-stu-id="66ea0-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="66ea0-108">Před čtením tohoto dokumentu doporučujeme, abyste si přečetli [Úvod k označení pomocníků](tag-helpers/intro.md) .</span><span class="sxs-lookup"><span data-stu-id="66ea0-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="66ea0-109">V mnoha případech pomocné rutiny HTML poskytují alternativní přístup k konkrétní pomocné rutině značek, ale je důležité rozpoznat, že pomocné rutiny značky nenahrazují nápovědu HTML a že pro každou pomocnou nápovědu HTML není k dispozici pomocný pomocník značek.</span><span class="sxs-lookup"><span data-stu-id="66ea0-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="66ea0-110">Pokud existuje alternativa pomocníka HTML, je uvedena.</span><span class="sxs-lookup"><span data-stu-id="66ea0-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="66ea0-111">Pomocník značek formuláře</span><span class="sxs-lookup"><span data-stu-id="66ea0-111">The Form Tag Helper</span></span>

<span data-ttu-id="66ea0-112">Pomocný objekt značky [formuláře](https://www.w3.org/TR/html401/interact/forms.html) :</span><span class="sxs-lookup"><span data-stu-id="66ea0-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="66ea0-113">Generuje [\<formulář HTML >](https://www.w3.org/TR/html401/interact/forms.html) `action` hodnotu atributu pro akci kontroleru MVC nebo s názvem Route.</span><span class="sxs-lookup"><span data-stu-id="66ea0-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="66ea0-114">Vygeneruje skrytý [token pro ověření žádosti](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) , který zabrání padělání požadavků mezi weby (při použití s atributem `[ValidateAntiForgeryToken]` v metodě http post).</span><span class="sxs-lookup"><span data-stu-id="66ea0-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="66ea0-115">Poskytuje atribut `asp-route-<Parameter Name>`, kde je `<Parameter Name>` přidáno do hodnot tras.</span><span class="sxs-lookup"><span data-stu-id="66ea0-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="66ea0-116">Parametry `routeValues` pro `Html.BeginForm` a `Html.BeginRouteForm` poskytují podobné funkce.</span><span class="sxs-lookup"><span data-stu-id="66ea0-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="66ea0-117">Má alternativní `Html.BeginForm` pomocníka HTML a `Html.BeginRouteForm`</span><span class="sxs-lookup"><span data-stu-id="66ea0-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="66ea0-118">Ukázka:</span><span class="sxs-lookup"><span data-stu-id="66ea0-118">Sample:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="66ea0-119">Pomocník značek Form výše vygeneruje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-119">The Form Tag Helper above generates the following HTML:</span></span>

```HTML
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="66ea0-120">Modul runtime MVC generuje hodnotu atributu `action` z pomocných atributů značek formulářů `asp-controller` a `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="66ea0-121">Pomocník značek formuláře také generuje skrytý token pro [ověření žádosti](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) , který zabrání padělání požadavků mezi weby (při použití s atributem `[ValidateAntiForgeryToken]` v metodě akce HTTP POST).</span><span class="sxs-lookup"><span data-stu-id="66ea0-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="66ea0-122">Ochrana čistě formuláře HTML proti padělání žádostí mezi weby je obtížné, a proto pomocník značek formuláře tuto službu poskytuje za vás.</span><span class="sxs-lookup"><span data-stu-id="66ea0-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="66ea0-123">Použití pojmenované trasy</span><span class="sxs-lookup"><span data-stu-id="66ea0-123">Using a named route</span></span>

<span data-ttu-id="66ea0-124">Pomocný atribut značky `asp-route` může také generovat označení pro atribut `action` HTML.</span><span class="sxs-lookup"><span data-stu-id="66ea0-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="66ea0-125">Aplikace s [trasou](../../fundamentals/routing.md) s názvem `register` může na registrační stránce použít následující značky:</span><span class="sxs-lookup"><span data-stu-id="66ea0-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="66ea0-126">Mnohé z zobrazení ve složce *zobrazení nebo účtu* (vygenerované při vytváření nové webové aplikace s *jednotlivými uživatelskými účty*) obsahují atribut [ASP-Route-ReturnUrl](xref:mvc/views/working-with-forms) :</span><span class="sxs-lookup"><span data-stu-id="66ea0-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="66ea0-127">Pomocí integrovaných šablon se `returnUrl` automaticky naplní pouze při pokusu o přístup k autorizovanému prostředku, ale nejsou ověřeny nebo autorizovány.</span><span class="sxs-lookup"><span data-stu-id="66ea0-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="66ea0-128">Když se pokusíte o neautorizovaný přístup, middleware zabezpečení vás přesměruje na přihlašovací stránku pomocí `returnUrl` sady.</span><span class="sxs-lookup"><span data-stu-id="66ea0-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="66ea0-129">Pomocník značek akce formuláře</span><span class="sxs-lookup"><span data-stu-id="66ea0-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="66ea0-130">Pomocná značka akce formuláře generuje atribut `formaction` u generované značky `<button ...>` nebo `<input type="image" ...>`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="66ea0-131">Atribut `formaction` určuje, kde formulář odesílá data.</span><span class="sxs-lookup"><span data-stu-id="66ea0-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="66ea0-132">Váže se k [\<vstupní >](https://www.w3.org/wiki/HTML/Elements/input) prvky typu `image` a [\<> elementy Button](https://www.w3.org/wiki/HTML/Elements/button) .</span><span class="sxs-lookup"><span data-stu-id="66ea0-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="66ea0-133">Pomocná značka akce formuláře umožňuje použití několika atributů [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` k řízení toho, které `formaction` odkaz je vygenerován pro odpovídající prvek.</span><span class="sxs-lookup"><span data-stu-id="66ea0-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="66ea0-134">Podporované atributy [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) pro řízení hodnoty `formaction`:</span><span class="sxs-lookup"><span data-stu-id="66ea0-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="66ea0-135">Atribut</span><span class="sxs-lookup"><span data-stu-id="66ea0-135">Attribute</span></span>|<span data-ttu-id="66ea0-136">Popis</span><span class="sxs-lookup"><span data-stu-id="66ea0-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="66ea0-137">asp-controller</span><span class="sxs-lookup"><span data-stu-id="66ea0-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="66ea0-138">Název kontroleru</span><span class="sxs-lookup"><span data-stu-id="66ea0-138">The name of the controller.</span></span>|
|[<span data-ttu-id="66ea0-139">asp-action</span><span class="sxs-lookup"><span data-stu-id="66ea0-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="66ea0-140">Název metody akce</span><span class="sxs-lookup"><span data-stu-id="66ea0-140">The name of the action method.</span></span>|
|[<span data-ttu-id="66ea0-141">asp-area</span><span class="sxs-lookup"><span data-stu-id="66ea0-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="66ea0-142">Název oblasti</span><span class="sxs-lookup"><span data-stu-id="66ea0-142">The name of the area.</span></span>|
|[<span data-ttu-id="66ea0-143">asp-page</span><span class="sxs-lookup"><span data-stu-id="66ea0-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="66ea0-144">Název stránky Razor</span><span class="sxs-lookup"><span data-stu-id="66ea0-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="66ea0-145">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="66ea0-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="66ea0-146">Název obslužné rutiny stránky Razor</span><span class="sxs-lookup"><span data-stu-id="66ea0-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="66ea0-147">ASP – trasa</span><span class="sxs-lookup"><span data-stu-id="66ea0-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="66ea0-148">Název trasy.</span><span class="sxs-lookup"><span data-stu-id="66ea0-148">The name of the route.</span></span>|
|[<span data-ttu-id="66ea0-149">ASP-Route-{Value}</span><span class="sxs-lookup"><span data-stu-id="66ea0-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="66ea0-150">Jedna hodnota směrování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="66ea0-150">A single URL route value.</span></span> <span data-ttu-id="66ea0-151">Například `asp-route-id="1234"`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="66ea0-152">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="66ea0-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="66ea0-153">Všechny hodnoty tras.</span><span class="sxs-lookup"><span data-stu-id="66ea0-153">All route values.</span></span>|
|[<span data-ttu-id="66ea0-154">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="66ea0-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="66ea0-155">Fragment adresy URL</span><span class="sxs-lookup"><span data-stu-id="66ea0-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="66ea0-156">Příklad odeslání do kontroleru</span><span class="sxs-lookup"><span data-stu-id="66ea0-156">Submit to controller example</span></span>

<span data-ttu-id="66ea0-157">Následující kód odešle formulář do akce `Index` `HomeController`, když je vybrán vstup nebo tlačítko:</span><span class="sxs-lookup"><span data-stu-id="66ea0-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="66ea0-158">Předchozí kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="66ea0-159">Příklad odeslání na stránku</span><span class="sxs-lookup"><span data-stu-id="66ea0-159">Submit to page example</span></span>

<span data-ttu-id="66ea0-160">Následující kód odešle formulář na stránku `About` Razor:</span><span class="sxs-lookup"><span data-stu-id="66ea0-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="66ea0-161">Předchozí kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="66ea0-162">Příklad odeslání do trasy</span><span class="sxs-lookup"><span data-stu-id="66ea0-162">Submit to route example</span></span>

<span data-ttu-id="66ea0-163">Vezměte v úvahu `/Home/Test` koncový bod:</span><span class="sxs-lookup"><span data-stu-id="66ea0-163">Consider the `/Home/Test` endpoint:</span></span>

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

<span data-ttu-id="66ea0-164">Následující kód odešle formulář do koncového bodu `/Home/Test`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="66ea0-165">Předchozí kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="66ea0-166">Pomocná rutina vstupní značky</span><span class="sxs-lookup"><span data-stu-id="66ea0-166">The Input Tag Helper</span></span>

<span data-ttu-id="66ea0-167">Pomocná rutina vstupní značky váže prvek HTML [\<Input >](https://www.w3.org/wiki/HTML/Elements/input) element na výraz modelu v zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="66ea0-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="66ea0-168">Syntaxe:</span><span class="sxs-lookup"><span data-stu-id="66ea0-168">Syntax:</span></span>

```HTML
<input asp-for="<Expression Name>">
```

<span data-ttu-id="66ea0-169">Pomocná rutina vstupní značky:</span><span class="sxs-lookup"><span data-stu-id="66ea0-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="66ea0-170">Generuje atributy HTML `id` a `name` pro název výrazu zadaný v atributu `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="66ea0-171">`asp-for="Property1.Property2"` je ekvivalentem `m => m.Property1.Property2`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="66ea0-172">Název výrazu je, který se používá pro hodnotu atributu `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="66ea0-173">Další informace najdete v části [názvy výrazů](#expression-names) .</span><span class="sxs-lookup"><span data-stu-id="66ea0-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="66ea0-174">Nastaví hodnotu atributu `type` HTML na základě typu modelu a atributů [poznámek k datům](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) použitým pro vlastnost modelu.</span><span class="sxs-lookup"><span data-stu-id="66ea0-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="66ea0-175">Pokud je zadaná hodnota atributu `type` HTML, nebude přepsána.</span><span class="sxs-lookup"><span data-stu-id="66ea0-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="66ea0-176">Generuje atributy ověřování [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) z atributů [poznámky k datům](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) použitých na vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="66ea0-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="66ea0-177">Má pomocná funkce HTML překryvně `Html.TextBoxFor` a `Html.EditorFor`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="66ea0-178">Podrobnosti najdete v části **alternativy pomocníka HTML pro vstupní nápovědu ke značce** .</span><span class="sxs-lookup"><span data-stu-id="66ea0-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="66ea0-179">Poskytuje silné psaní.</span><span class="sxs-lookup"><span data-stu-id="66ea0-179">Provides strong typing.</span></span> <span data-ttu-id="66ea0-180">Pokud se název vlastnosti změní a pomocník značek neaktualizujete, zobrazí se chybová zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="66ea0-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

<span data-ttu-id="66ea0-181">Pomocná značka `Input` nastaví atribut HTML `type` na základě typu .NET.</span><span class="sxs-lookup"><span data-stu-id="66ea0-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="66ea0-182">Následující tabulka uvádí některé typy běžných typů .NET a generovaný typ HTML (ne každý typ .NET je uveden).</span><span class="sxs-lookup"><span data-stu-id="66ea0-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="66ea0-183">Typ .NET</span><span class="sxs-lookup"><span data-stu-id="66ea0-183">.NET type</span></span>|<span data-ttu-id="66ea0-184">Typ vstupu</span><span class="sxs-lookup"><span data-stu-id="66ea0-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="66ea0-185">Bool</span><span class="sxs-lookup"><span data-stu-id="66ea0-185">Bool</span></span>|<span data-ttu-id="66ea0-186">type="checkbox"</span><span class="sxs-lookup"><span data-stu-id="66ea0-186">type="checkbox"</span></span>|
|<span data-ttu-id="66ea0-187">String</span><span class="sxs-lookup"><span data-stu-id="66ea0-187">String</span></span>|<span data-ttu-id="66ea0-188">type="text"</span><span class="sxs-lookup"><span data-stu-id="66ea0-188">type="text"</span></span>|
|<span data-ttu-id="66ea0-189">Datum a čas</span><span class="sxs-lookup"><span data-stu-id="66ea0-189">DateTime</span></span>|<span data-ttu-id="66ea0-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="66ea0-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="66ea0-191">Byte</span><span class="sxs-lookup"><span data-stu-id="66ea0-191">Byte</span></span>|<span data-ttu-id="66ea0-192">Type = "Number"</span><span class="sxs-lookup"><span data-stu-id="66ea0-192">type="number"</span></span>|
|<span data-ttu-id="66ea0-193">Int</span><span class="sxs-lookup"><span data-stu-id="66ea0-193">Int</span></span>|<span data-ttu-id="66ea0-194">Type = "Number"</span><span class="sxs-lookup"><span data-stu-id="66ea0-194">type="number"</span></span>|
|<span data-ttu-id="66ea0-195">Jednoduchá, Dvojitá</span><span class="sxs-lookup"><span data-stu-id="66ea0-195">Single, Double</span></span>|<span data-ttu-id="66ea0-196">Type = "Number"</span><span class="sxs-lookup"><span data-stu-id="66ea0-196">type="number"</span></span>|

<span data-ttu-id="66ea0-197">V následující tabulce jsou uvedeny některé z běžných atributů [datových poznámek](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) , které bude Pomocník pro vstupní značky mapovat na konkrétní vstupní typy (ne každý atribut ověření je uveden):</span><span class="sxs-lookup"><span data-stu-id="66ea0-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="66ea0-198">Atribut</span><span class="sxs-lookup"><span data-stu-id="66ea0-198">Attribute</span></span>|<span data-ttu-id="66ea0-199">Typ vstupu</span><span class="sxs-lookup"><span data-stu-id="66ea0-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="66ea0-200">EmailAddress</span><span class="sxs-lookup"><span data-stu-id="66ea0-200">[EmailAddress]</span></span>|<span data-ttu-id="66ea0-201">Type = "e-mail"</span><span class="sxs-lookup"><span data-stu-id="66ea0-201">type="email"</span></span>|
|<span data-ttu-id="66ea0-202">Adresa URL</span><span class="sxs-lookup"><span data-stu-id="66ea0-202">[Url]</span></span>|<span data-ttu-id="66ea0-203">type="url"</span><span class="sxs-lookup"><span data-stu-id="66ea0-203">type="url"</span></span>|
|<span data-ttu-id="66ea0-204">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="66ea0-204">[HiddenInput]</span></span>|<span data-ttu-id="66ea0-205">Type = "Hidden"</span><span class="sxs-lookup"><span data-stu-id="66ea0-205">type="hidden"</span></span>|
|<span data-ttu-id="66ea0-206">Hovor</span><span class="sxs-lookup"><span data-stu-id="66ea0-206">[Phone]</span></span>|<span data-ttu-id="66ea0-207">Type = "tel"</span><span class="sxs-lookup"><span data-stu-id="66ea0-207">type="tel"</span></span>|
|<span data-ttu-id="66ea0-208">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="66ea0-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="66ea0-209">type="password"</span><span class="sxs-lookup"><span data-stu-id="66ea0-209">type="password"</span></span>|
|<span data-ttu-id="66ea0-210">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="66ea0-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="66ea0-211">type="date"</span><span class="sxs-lookup"><span data-stu-id="66ea0-211">type="date"</span></span>|
|<span data-ttu-id="66ea0-212">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="66ea0-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="66ea0-213">Type = "čas"</span><span class="sxs-lookup"><span data-stu-id="66ea0-213">type="time"</span></span>|

<span data-ttu-id="66ea0-214">Ukázka:</span><span class="sxs-lookup"><span data-stu-id="66ea0-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="66ea0-215">Výše uvedený kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-215">The code above generates the following HTML:</span></span>

```HTML
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

<span data-ttu-id="66ea0-216">Datové poznámky použité pro vlastnosti `Email` a `Password` generují metadata modelu.</span><span class="sxs-lookup"><span data-stu-id="66ea0-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="66ea0-217">Pomocná rutina vstupní značky spotřebovává metadata modelu a generuje `data-val-*` atributy [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) (viz [ověření modelu](../models/validation.md)).</span><span class="sxs-lookup"><span data-stu-id="66ea0-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="66ea0-218">Tyto atributy popisují validátory, které se mají připojit ke vstupním polím.</span><span class="sxs-lookup"><span data-stu-id="66ea0-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="66ea0-219">To poskytuje nenápadné ověřování HTML5 a [jQuery](https://jquery.com/) .</span><span class="sxs-lookup"><span data-stu-id="66ea0-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="66ea0-220">Nenápadující atributy mají formát `data-val-rule="Error Message"`, kde pravidlo je název pravidla ověření (například `data-val-required`, `data-val-email`, `data-val-maxlength`atd.) Pokud je v atributu uvedena chybová zpráva, je zobrazena jako hodnota atributu `data-val-rule`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="66ea0-221">K dispozici jsou také atributy `data-val-ruleName-argumentName="argumentValue"` formuláře, které poskytují další podrobnosti o pravidle, například `data-val-maxlength-max="1024"`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="66ea0-222">Pomocné možnosti pro pomocníka HTML pro pomocníka vstupní značky</span><span class="sxs-lookup"><span data-stu-id="66ea0-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="66ea0-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` a `Html.EditorFor` mají překrývající se funkce s pomocníkem vstupní značky.</span><span class="sxs-lookup"><span data-stu-id="66ea0-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="66ea0-224">Pomocná rutina vstupní značky automaticky nastaví atribut `type`; `Html.TextBox` a `Html.TextBoxFor` ne.</span><span class="sxs-lookup"><span data-stu-id="66ea0-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="66ea0-225">`Html.Editor` a `Html.EditorFor` zpracovávat kolekce, komplexní objekty a šablony; Pomocná rutina vstupní značky není.</span><span class="sxs-lookup"><span data-stu-id="66ea0-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="66ea0-226">Pomocná rutina vstupní značky `Html.EditorFor` a `Html.TextBoxFor` jsou silně typované (používají lambda výrazy); `Html.TextBox` a `Html.Editor` nejsou (používají názvy výrazů).</span><span class="sxs-lookup"><span data-stu-id="66ea0-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="66ea0-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="66ea0-227">HtmlAttributes</span></span>

<span data-ttu-id="66ea0-228">`@Html.Editor()` a `@Html.EditorFor()` při spouštění jejich výchozích šablon používají speciální položku `ViewDataDictionary` s názvem `htmlAttributes`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="66ea0-229">Toto chování se volitelně rozšiřuje pomocí parametrů `additionalViewData`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="66ea0-230">Klíč "htmlAttributes" rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="66ea0-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="66ea0-231">Klíč "htmlAttributes" se zpracovává podobně jako objekt `htmlAttributes` předaný do vstupních pomocníků, jako je `@Html.TextBox()`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="66ea0-232">Názvy výrazů</span><span class="sxs-lookup"><span data-stu-id="66ea0-232">Expression names</span></span>

<span data-ttu-id="66ea0-233">Hodnota atributu `asp-for` je `ModelExpression` a pravá strana výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="66ea0-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="66ea0-234">Proto se `asp-for="Property1"` `m => m.Property1` ve vygenerovaném kódu, což je důvod, proč nemusíte předponu `Model`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="66ea0-235">Ke spuštění vloženého výrazu a přesunutí před `m.`můžete použít znak "\@":</span><span class="sxs-lookup"><span data-stu-id="66ea0-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe">
```

<span data-ttu-id="66ea0-236">Generuje následující:</span><span class="sxs-lookup"><span data-stu-id="66ea0-236">Generates the following:</span></span>

```HTML
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="66ea0-237">Pomocí vlastností kolekce `asp-for="CollectionProperty[23].Member"` generuje stejný název jako `asp-for="CollectionProperty[i].Member"`, pokud `i` má `23`Value.</span><span class="sxs-lookup"><span data-stu-id="66ea0-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="66ea0-238">Když ASP.NET Core MVC vypočítá hodnotu `ModelExpression`, zkontroluje několik zdrojů, včetně `ModelState`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="66ea0-239">Zvažte `<input type="text" asp-for="@Name">`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="66ea0-240">Vypočtený `value` atribut je první hodnota, která není null od:</span><span class="sxs-lookup"><span data-stu-id="66ea0-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="66ea0-241">položka `ModelState` s klíčem "Name".</span><span class="sxs-lookup"><span data-stu-id="66ea0-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="66ea0-242">Výsledek výrazu `Model.Name`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="66ea0-243">Navigace podřízených vlastností</span><span class="sxs-lookup"><span data-stu-id="66ea0-243">Navigating child properties</span></span>

<span data-ttu-id="66ea0-244">Můžete také přejít k podřízeným vlastnostem pomocí cesty vlastností modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="66ea0-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="66ea0-245">Zvažte složitější třídu modelu, která obsahuje podřízenou vlastnost `Address`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="66ea0-246">V zobrazení se svážeme s `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="66ea0-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="66ea0-247">Následující kód HTML je vygenerován pro `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="66ea0-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="66ea0-248">Názvy a kolekce výrazů</span><span class="sxs-lookup"><span data-stu-id="66ea0-248">Expression names and Collections</span></span>

<span data-ttu-id="66ea0-249">Ukázka modelu obsahujícího pole `Colors`:</span><span class="sxs-lookup"><span data-stu-id="66ea0-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="66ea0-250">Metoda Action:</span><span class="sxs-lookup"><span data-stu-id="66ea0-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

<span data-ttu-id="66ea0-251">Následující syntaxe Razor ukazuje, jak přistupujete k určitému prvku `Color`:</span><span class="sxs-lookup"><span data-stu-id="66ea0-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="66ea0-252">Šablona *views/Shared/EditorTemplates/String. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="66ea0-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="66ea0-253">Ukázka pomocí `List<T>`:</span><span class="sxs-lookup"><span data-stu-id="66ea0-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="66ea0-254">Následující Razor ukazuje, jak iterovat v kolekci:</span><span class="sxs-lookup"><span data-stu-id="66ea0-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="66ea0-255">Šablona *views/Shared/EditorTemplates/ToDoItem. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="66ea0-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="66ea0-256">`foreach` by měla být použita, pokud je to možné, pokud se hodnota bude používat v `asp-for` nebo `Html.DisplayFor` ekvivalentním kontextu.</span><span class="sxs-lookup"><span data-stu-id="66ea0-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="66ea0-257">Obecně je `for` lepší než `foreach` (pokud to scénář umožňuje), protože není nutné přidělovat enumerátor; vyhodnocení indexeru ve výrazu LINQ ale může být nákladné a mělo by být minimalizováno.</span><span class="sxs-lookup"><span data-stu-id="66ea0-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="66ea0-258">Vzorový ukázkový kód uvedený výše ukazuje, jak byste nahradili výraz lambda operátorem `@` pro přístup ke každému `ToDoItem` v seznamu.</span><span class="sxs-lookup"><span data-stu-id="66ea0-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="66ea0-259">Pomocník značek TextArea</span><span class="sxs-lookup"><span data-stu-id="66ea0-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="66ea0-260">Pomocná rutina značky `Textarea Tag Helper` je podobná Pomocníkovi vstupní značky.</span><span class="sxs-lookup"><span data-stu-id="66ea0-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="66ea0-261">Generuje atributy `id` a `name` a atributy ověřování dat z modelu pro [\<prvku textarea >](https://www.w3.org/wiki/HTML/Elements/textarea) .</span><span class="sxs-lookup"><span data-stu-id="66ea0-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="66ea0-262">Poskytuje silné psaní.</span><span class="sxs-lookup"><span data-stu-id="66ea0-262">Provides strong typing.</span></span>

* <span data-ttu-id="66ea0-263">Alternativní pomocník HTML: `Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="66ea0-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="66ea0-264">Ukázka:</span><span class="sxs-lookup"><span data-stu-id="66ea0-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="66ea0-265">Vygeneruje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-265">The following HTML is generated:</span></span>

```HTML
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="66ea0-266">Pomocník značek popisku</span><span class="sxs-lookup"><span data-stu-id="66ea0-266">The Label Tag Helper</span></span>

* <span data-ttu-id="66ea0-267">Vygeneruje popisek popisku a `for` u [\<popisku >](https://www.w3.org/wiki/HTML/Elements/label) elementu pro název výrazu.</span><span class="sxs-lookup"><span data-stu-id="66ea0-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="66ea0-268">Alternativní pomocník HTML: `Html.LabelFor`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="66ea0-269">`Label Tag Helper` poskytuje následující výhody oproti čistě prvku popisku HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="66ea0-270">Z atributu `Display` se automaticky získá popisná hodnota popisku.</span><span class="sxs-lookup"><span data-stu-id="66ea0-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="66ea0-271">Zamýšlené zobrazované jméno se může v průběhu času měnit a kombinace atributu `Display` a pomocníka značek Label bude používat `Display` všude, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="66ea0-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="66ea0-272">Míň značek ve zdrojovém kódu</span><span class="sxs-lookup"><span data-stu-id="66ea0-272">Less markup in source code</span></span>

* <span data-ttu-id="66ea0-273">Silné zadání pomocí vlastnosti modelu</span><span class="sxs-lookup"><span data-stu-id="66ea0-273">Strong typing with the model property.</span></span>

<span data-ttu-id="66ea0-274">Ukázka:</span><span class="sxs-lookup"><span data-stu-id="66ea0-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="66ea0-275">Následující kód HTML je vygenerován pro prvek `<label>`:</span><span class="sxs-lookup"><span data-stu-id="66ea0-275">The following HTML is generated for the `<label>` element:</span></span>

```HTML
<label for="Email">Email Address</label>
```

<span data-ttu-id="66ea0-276">Pomocník značek Label vygeneroval hodnotu atributu `for` "E-mail", což je ID přidružené k elementu `<input>`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="66ea0-277">Pomocník značek generuje konzistentní `id` a `for` prvky, aby mohly být správně přiřazeny.</span><span class="sxs-lookup"><span data-stu-id="66ea0-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="66ea0-278">Titulek v této ukázce pochází z atributu `Display`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="66ea0-279">Pokud model neobsahuje atribut `Display`, popisek by byl názvem vlastnosti výrazu.</span><span class="sxs-lookup"><span data-stu-id="66ea0-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="66ea0-280">Pomocníka značek ověřování</span><span class="sxs-lookup"><span data-stu-id="66ea0-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="66ea0-281">K dispozici jsou dva pomocníky ověřovacích značek.</span><span class="sxs-lookup"><span data-stu-id="66ea0-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="66ea0-282">`Validation Message Tag Helper` (která zobrazuje ověřovací zprávu pro jednu vlastnost v modelu) a `Validation Summary Tag Helper` (která zobrazuje souhrn chyb ověřování).</span><span class="sxs-lookup"><span data-stu-id="66ea0-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="66ea0-283">`Input Tag Helper` přidá atributy ověřování na straně klienta HTML5 do vstupních elementů na základě atributů datových poznámek v třídách modelu.</span><span class="sxs-lookup"><span data-stu-id="66ea0-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="66ea0-284">Ověřování se provádí také na serveru.</span><span class="sxs-lookup"><span data-stu-id="66ea0-284">Validation is also performed on the server.</span></span> <span data-ttu-id="66ea0-285">Pomocník pro ověřování značek zobrazí tyto chybové zprávy, když dojde k chybě ověření.</span><span class="sxs-lookup"><span data-stu-id="66ea0-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="66ea0-286">Pomocný pomocník značek ověřovací zprávy</span><span class="sxs-lookup"><span data-stu-id="66ea0-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="66ea0-287">Přidá atribut [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)`data-valmsg-for="property"` do prvku [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) , který připojí chybové zprávy ověřování ve vstupním poli zadané vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="66ea0-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="66ea0-288">Když dojde k chybě ověřování na straně klienta, [jQuery](https://jquery.com/) zobrazí chybovou zprávu v elementu `<span>`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="66ea0-289">Ověřování probíhá také na serveru.</span><span class="sxs-lookup"><span data-stu-id="66ea0-289">Validation also takes place on the server.</span></span> <span data-ttu-id="66ea0-290">Klienti můžou mít zakázaný JavaScript a některé ověřování se dá dělat jenom na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="66ea0-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="66ea0-291">Alternativní pomocník HTML: `Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="66ea0-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="66ea0-292">`Validation Message Tag Helper` se používá s atributem `asp-validation-for` na elementu [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) HTML.</span><span class="sxs-lookup"><span data-stu-id="66ea0-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```HTML
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="66ea0-293">Pomocný pomocník značek ověřovací zprávy vygeneruje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="66ea0-294">Obecně se používá `Validation Message Tag Helper` po pomocné rutině `Input` značky pro stejnou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="66ea0-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="66ea0-295">Tím se zobrazí všechny chybové zprávy ověřování poblíž vstupu, který způsobil chybu.</span><span class="sxs-lookup"><span data-stu-id="66ea0-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="66ea0-296">Musíte mít zobrazení se správnými odkazy skriptu JavaScript a [jQuery](https://jquery.com/) pro ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="66ea0-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="66ea0-297">Další informace najdete v tématu [ověřování modelu](../models/validation.md) .</span><span class="sxs-lookup"><span data-stu-id="66ea0-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="66ea0-298">Pokud dojde k chybě ověřování na straně serveru (například když máte zakázané vlastní ověřování na straně serveru nebo když je ověřování na straně klienta zakázané), MVC umístí tuto chybovou zprávu jako tělo prvku `<span>`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="66ea0-299">Nápověda k souhrnným značkám ověřování</span><span class="sxs-lookup"><span data-stu-id="66ea0-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="66ea0-300">Cílí na `<div>` elementy s atributem `asp-validation-summary`</span><span class="sxs-lookup"><span data-stu-id="66ea0-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="66ea0-301">Alternativní pomocník HTML: `@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="66ea0-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="66ea0-302">`Validation Summary Tag Helper` slouží k zobrazení souhrnu ověřovacích zpráv.</span><span class="sxs-lookup"><span data-stu-id="66ea0-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="66ea0-303">Hodnota atributu `asp-validation-summary` může být libovolná z následujících:</span><span class="sxs-lookup"><span data-stu-id="66ea0-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="66ea0-304">ASP – ověření – souhrn</span><span class="sxs-lookup"><span data-stu-id="66ea0-304">asp-validation-summary</span></span>|<span data-ttu-id="66ea0-305">Zobrazené ověřovací zprávy</span><span class="sxs-lookup"><span data-stu-id="66ea0-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="66ea0-306">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="66ea0-306">ValidationSummary.All</span></span>|<span data-ttu-id="66ea0-307">Úroveň vlastnosti a modelu</span><span class="sxs-lookup"><span data-stu-id="66ea0-307">Property and model level</span></span>|
|<span data-ttu-id="66ea0-308">Ovládací souhrnu ověření. ModelOnly</span><span class="sxs-lookup"><span data-stu-id="66ea0-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="66ea0-309">Model</span><span class="sxs-lookup"><span data-stu-id="66ea0-309">Model</span></span>|
|<span data-ttu-id="66ea0-310">Ovládací souhrnu ověření. None</span><span class="sxs-lookup"><span data-stu-id="66ea0-310">ValidationSummary.None</span></span>|<span data-ttu-id="66ea0-311">Žádné</span><span class="sxs-lookup"><span data-stu-id="66ea0-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="66ea0-312">Ukázka</span><span class="sxs-lookup"><span data-stu-id="66ea0-312">Sample</span></span>

<span data-ttu-id="66ea0-313">V následujícím příkladu datový model má `DataAnnotation` atributy, které generují chybové zprávy ověřování u elementu `<input>`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-313">In the following example, the data model has `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="66ea0-314">Pokud dojde k chybě ověřování, Pomocník pro ověření značky zobrazí chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="66ea0-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="66ea0-315">Generovaný kód HTML (Pokud je model platný):</span><span class="sxs-lookup"><span data-stu-id="66ea0-315">The generated HTML (when the model is valid):</span></span>

```HTML
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="66ea0-316">Pomocná rutina pro výběr značky</span><span class="sxs-lookup"><span data-stu-id="66ea0-316">The Select Tag Helper</span></span>

* <span data-ttu-id="66ea0-317">Generuje [vybrané](https://www.w3.org/wiki/HTML/Elements/select) a přidružené prvky [možností](https://www.w3.org/wiki/HTML/Elements/option) pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="66ea0-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="66ea0-318">Má alternativní `Html.DropDownListFor` pomocníka HTML a `Html.ListBoxFor`</span><span class="sxs-lookup"><span data-stu-id="66ea0-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="66ea0-319">`Select Tag Helper` `asp-for` Určuje název vlastnosti modelu pro element [Select](https://www.w3.org/wiki/HTML/Elements/select) a `asp-items` Určuje prvky [možností](https://www.w3.org/wiki/HTML/Elements/option) .</span><span class="sxs-lookup"><span data-stu-id="66ea0-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="66ea0-320">Příklad:</span><span class="sxs-lookup"><span data-stu-id="66ea0-320">For example:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="66ea0-321">Ukázka:</span><span class="sxs-lookup"><span data-stu-id="66ea0-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="66ea0-322">Metoda `Index` inicializuje `CountryViewModel`, nastaví vybranou zemi a předá ji do zobrazení `Index`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="66ea0-323">Metoda HTTP POST `Index` zobrazuje výběr:</span><span class="sxs-lookup"><span data-stu-id="66ea0-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="66ea0-324">`Index` zobrazení:</span><span class="sxs-lookup"><span data-stu-id="66ea0-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="66ea0-325">Který generuje následující HTML (s vybraným certifikačním úřadem):</span><span class="sxs-lookup"><span data-stu-id="66ea0-325">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> <span data-ttu-id="66ea0-326">Nedoporučujeme používat `ViewBag` ani `ViewData` s pomocníkem pro výběr značky.</span><span class="sxs-lookup"><span data-stu-id="66ea0-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="66ea0-327">Model zobrazení je robustnější při poskytování metadat MVC a obecně méně problematických.</span><span class="sxs-lookup"><span data-stu-id="66ea0-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="66ea0-328">Hodnota atributu `asp-for` je zvláštní případ a nevyžaduje předponu `Model`, další pomocné atributy značek (například `asp-items`).</span><span class="sxs-lookup"><span data-stu-id="66ea0-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="66ea0-329">Vazba výčtu</span><span class="sxs-lookup"><span data-stu-id="66ea0-329">Enum binding</span></span>

<span data-ttu-id="66ea0-330">Je často vhodné použít `<select>` s vlastností `enum` a generovat prvky `SelectListItem` z hodnot `enum`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="66ea0-331">Ukázka:</span><span class="sxs-lookup"><span data-stu-id="66ea0-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="66ea0-332">Metoda `GetEnumSelectList` generuje objekt `SelectList` pro výčet.</span><span class="sxs-lookup"><span data-stu-id="66ea0-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="66ea0-333">Seznam enumerátorů můžete označit pomocí atributu `Display` a získat tak bohatší uživatelské rozhraní:</span><span class="sxs-lookup"><span data-stu-id="66ea0-333">You can mark your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="66ea0-334">Vygeneruje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-334">The following HTML is generated:</span></span>

```HTML
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a><span data-ttu-id="66ea0-335">Skupina možností</span><span class="sxs-lookup"><span data-stu-id="66ea0-335">Option Group</span></span>

<span data-ttu-id="66ea0-336">Prvek HTML [\<optgroup >](https://www.w3.org/wiki/HTML/Elements/optgroup) je vygenerován, když model zobrazení obsahuje jeden nebo více objektů `SelectListGroup`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="66ea0-337">`CountryViewModelGroup` seskupí `SelectListItem` prvky do skupin "Severní Amerika" a "Evropa":</span><span class="sxs-lookup"><span data-stu-id="66ea0-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="66ea0-338">Níže jsou uvedené dvě skupiny:</span><span class="sxs-lookup"><span data-stu-id="66ea0-338">The two groups are shown below:</span></span>

![příklad skupiny možností](working-with-forms/_static/grp.png)

<span data-ttu-id="66ea0-340">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-340">The generated HTML:</span></span>

```HTML
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="66ea0-341">Vícenásobný výběr</span><span class="sxs-lookup"><span data-stu-id="66ea0-341">Multiple select</span></span>

<span data-ttu-id="66ea0-342">Pokud je vlastnost zadaná v atributu `asp-for` `IEnumerable`, vygeneruje Pomocník pro výběr značky automaticky [více](https://w3c.github.io/html-reference/select.html) než více atributů.</span><span class="sxs-lookup"><span data-stu-id="66ea0-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](https://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="66ea0-343">Například s ohledem na následující model:</span><span class="sxs-lookup"><span data-stu-id="66ea0-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="66ea0-344">S následujícím zobrazením:</span><span class="sxs-lookup"><span data-stu-id="66ea0-344">With the following view:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="66ea0-345">Generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-345">Generates the following HTML:</span></span>

```HTML
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a><span data-ttu-id="66ea0-346">Žádný výběr</span><span class="sxs-lookup"><span data-stu-id="66ea0-346">No selection</span></span>

<span data-ttu-id="66ea0-347">Pokud se vám na více stránkách používá možnost neurčeno, můžete vytvořit šablonu pro odstranění opakujícího se kódu HTML:</span><span class="sxs-lookup"><span data-stu-id="66ea0-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="66ea0-348">Šablona *views/Shared/EditorTemplates/CountryViewModel. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="66ea0-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="66ea0-349">Přidání [možnosti\<HTML >](https://www.w3.org/wiki/HTML/Elements/option) prvků není omezeno na případ *výběru* .</span><span class="sxs-lookup"><span data-stu-id="66ea0-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="66ea0-350">Například následující metoda zobrazení a akce vygeneruje HTML podobný kódu výše:</span><span class="sxs-lookup"><span data-stu-id="66ea0-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="66ea0-351">Bude vybrán správný prvek `<option>` (obsahující atribut `selected="selected"`) v závislosti na aktuální hodnotě `Country`.</span><span class="sxs-lookup"><span data-stu-id="66ea0-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```HTML
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="66ea0-352">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="66ea0-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="66ea0-353">Element HTML Form</span><span class="sxs-lookup"><span data-stu-id="66ea0-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="66ea0-354">Žádost o ověření tokenu</span><span class="sxs-lookup"><span data-stu-id="66ea0-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="66ea0-355">Rozhraní IAttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="66ea0-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="66ea0-356">Fragmenty kódu pro tento dokument</span><span class="sxs-lookup"><span data-stu-id="66ea0-356">Code snippets for this document</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)

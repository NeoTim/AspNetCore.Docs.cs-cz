---
title: Tagování pomocníků ve formulářích ve ASP.NET Core
author: rick-anderson
description: Popisuje předdefinované pomocníky značek používané s formuláři.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/working-with-forms
ms.openlocfilehash: 43a1c408ff1a03468989e5bb0839ca2cd245082b
ms.sourcegitcommit: b5e63714afc26e94be49a92619586df5189ed93a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739497"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="415a0-103">Tagování pomocníků ve formulářích ve ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="415a0-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="415a0-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette)a [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="415a0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="415a0-105">Tento dokument ukazuje práci s formuláři a prvky HTML, které se obvykle používají ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="415a0-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="415a0-106">Element [formuláře](https://www.w3.org/TR/html401/interact/forms.html) HTML poskytuje primárnímu mechanismu webové aplikace použití k odesílání dat na server.</span><span class="sxs-lookup"><span data-stu-id="415a0-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="415a0-107">Většina tohoto dokumentu popisuje [pomocníky značek](tag-helpers/intro.md) a způsob, jak vám může pomáhat s tvorbou robustních formulářů HTML.</span><span class="sxs-lookup"><span data-stu-id="415a0-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="415a0-108">Před čtením tohoto dokumentu doporučujeme, abyste si přečetli [Úvod k označení pomocníků](tag-helpers/intro.md) .</span><span class="sxs-lookup"><span data-stu-id="415a0-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="415a0-109">V mnoha případech pomocné rutiny HTML poskytují alternativní přístup k konkrétní pomocné rutině značek, ale je důležité rozpoznat, že pomocné rutiny značky nenahrazují nápovědu HTML a že pro každou pomocnou nápovědu HTML není k dispozici pomocný pomocník značek.</span><span class="sxs-lookup"><span data-stu-id="415a0-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="415a0-110">Pokud existuje alternativa pomocníka HTML, je uvedena.</span><span class="sxs-lookup"><span data-stu-id="415a0-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="415a0-111">Pomocník značek formuláře</span><span class="sxs-lookup"><span data-stu-id="415a0-111">The Form Tag Helper</span></span>

<span data-ttu-id="415a0-112">Pomocný objekt značky [formuláře](https://www.w3.org/TR/html401/interact/forms.html) :</span><span class="sxs-lookup"><span data-stu-id="415a0-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="415a0-113">Vygeneruje hodnotu atributu HTML [ \<form >](https://www.w3.org/TR/html401/interact/forms.html) `action` pro akci kontroleru MVC nebo s názvem Route.</span><span class="sxs-lookup"><span data-stu-id="415a0-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="415a0-114">Vygeneruje skrytý [token pro ověření žádosti](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) , který zabrání padělání požadavků mezi weby (při použití `[ValidateAntiForgeryToken]` s atributem v metodě akce HTTP POST).</span><span class="sxs-lookup"><span data-stu-id="415a0-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="415a0-115">`asp-route-<Parameter Name>` Poskytuje atribut `<Parameter Name>` , který je přidán do hodnot tras.</span><span class="sxs-lookup"><span data-stu-id="415a0-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="415a0-116">`routeValues` Parametrya`Html.BeginRouteForm`poskytujípodobnéfunkce. `Html.BeginForm`</span><span class="sxs-lookup"><span data-stu-id="415a0-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="415a0-117">Má alternativu `Html.BeginForm` k Pomocníkovi HTML a`Html.BeginRouteForm`</span><span class="sxs-lookup"><span data-stu-id="415a0-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="415a0-118">Vzorku</span><span class="sxs-lookup"><span data-stu-id="415a0-118">Sample:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="415a0-119">Pomocník značek Form výše vygeneruje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-119">The Form Tag Helper above generates the following HTML:</span></span>

```HTML
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="415a0-120">Modul runtime MVC generuje `action` hodnotu atributu z `asp-controller` pomocníka značek formuláře a `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="415a0-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="415a0-121">Pomocník značek formuláře také generuje skrytý token pro [ověření žádosti](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) , který zabrání padělání požadavků mezi weby (při použití s `[ValidateAntiForgeryToken]` atributem v metodě akce HTTP POST).</span><span class="sxs-lookup"><span data-stu-id="415a0-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="415a0-122">Ochrana čistě formuláře HTML proti padělání žádostí mezi weby je obtížné, a proto pomocník značek formuláře tuto službu poskytuje za vás.</span><span class="sxs-lookup"><span data-stu-id="415a0-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="415a0-123">Použití pojmenované trasy</span><span class="sxs-lookup"><span data-stu-id="415a0-123">Using a named route</span></span>

<span data-ttu-id="415a0-124">Atribut pomocník `action` značek může také generovat označení pro atribut HTML. `asp-route`</span><span class="sxs-lookup"><span data-stu-id="415a0-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="415a0-125">Aplikace s názvem `register` v [cestě](../../fundamentals/routing.md) může pro registrační stránku použít následující značky:</span><span class="sxs-lookup"><span data-stu-id="415a0-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="415a0-126">Mnohé z zobrazení ve složce *zobrazení nebo účtu* (vygenerované při vytváření nové webové aplikace s *jednotlivými uživatelskými účty*) obsahují atribut [ASP-Route-ReturnUrl](xref:mvc/views/working-with-forms) :</span><span class="sxs-lookup"><span data-stu-id="415a0-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="415a0-127">Pomocí integrovaných šablon se automaticky `returnUrl` naplní pouze při pokusu o přístup k autorizovanému prostředku, ale nejsou ověřeny nebo autorizovány.</span><span class="sxs-lookup"><span data-stu-id="415a0-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="415a0-128">Když se pokusíte o neautorizovaný přístup, middleware zabezpečení vás přesměruje na přihlašovací stránku se `returnUrl` sadou.</span><span class="sxs-lookup"><span data-stu-id="415a0-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="415a0-129">Pomocník značek akce formuláře</span><span class="sxs-lookup"><span data-stu-id="415a0-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="415a0-130">Pomocník značek akce formuláře vygeneruje `formaction` atribut na vygenerované `<button ...>` nebo `<input type="image" ...>` značky.</span><span class="sxs-lookup"><span data-stu-id="415a0-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="415a0-131">`formaction` Atribut určuje, kam formulář odešle svá data.</span><span class="sxs-lookup"><span data-stu-id="415a0-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="415a0-132">Váže se na [ \<vstupní >](https://www.w3.org/wiki/HTML/Elements/input) prvky typu a [ \<>](https://www.w3.org/wiki/HTML/Elements/button) prvků typu. `image`</span><span class="sxs-lookup"><span data-stu-id="415a0-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="415a0-133">Pomocná značka akce formuláře umožňuje použití několika atributů [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` k řízení toho, které `formaction` propojení je vygenerováno pro odpovídající prvek.</span><span class="sxs-lookup"><span data-stu-id="415a0-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="415a0-134">Podporované atributy [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) pro řízení hodnoty `formaction`:</span><span class="sxs-lookup"><span data-stu-id="415a0-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="415a0-135">Atribut</span><span class="sxs-lookup"><span data-stu-id="415a0-135">Attribute</span></span>|<span data-ttu-id="415a0-136">Popis</span><span class="sxs-lookup"><span data-stu-id="415a0-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="415a0-137">asp-controller</span><span class="sxs-lookup"><span data-stu-id="415a0-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="415a0-138">Název kontroleru.</span><span class="sxs-lookup"><span data-stu-id="415a0-138">The name of the controller.</span></span>|
|[<span data-ttu-id="415a0-139">asp-action</span><span class="sxs-lookup"><span data-stu-id="415a0-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="415a0-140">Název metody akce</span><span class="sxs-lookup"><span data-stu-id="415a0-140">The name of the action method.</span></span>|
|[<span data-ttu-id="415a0-141">asp-area</span><span class="sxs-lookup"><span data-stu-id="415a0-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="415a0-142">Název oblasti.</span><span class="sxs-lookup"><span data-stu-id="415a0-142">The name of the area.</span></span>|
|[<span data-ttu-id="415a0-143">asp-page</span><span class="sxs-lookup"><span data-stu-id="415a0-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="415a0-144">Název stránky Razor</span><span class="sxs-lookup"><span data-stu-id="415a0-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="415a0-145">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="415a0-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="415a0-146">Název obslužné rutiny stránky Razor</span><span class="sxs-lookup"><span data-stu-id="415a0-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="415a0-147">ASP – trasa</span><span class="sxs-lookup"><span data-stu-id="415a0-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="415a0-148">Název trasy.</span><span class="sxs-lookup"><span data-stu-id="415a0-148">The name of the route.</span></span>|
|[<span data-ttu-id="415a0-149">ASP-Route-{Value}</span><span class="sxs-lookup"><span data-stu-id="415a0-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="415a0-150">Jedna hodnota směrování adresy URL.</span><span class="sxs-lookup"><span data-stu-id="415a0-150">A single URL route value.</span></span> <span data-ttu-id="415a0-151">Například, `asp-route-id="1234"`.</span><span class="sxs-lookup"><span data-stu-id="415a0-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="415a0-152">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="415a0-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="415a0-153">Všechny hodnoty tras.</span><span class="sxs-lookup"><span data-stu-id="415a0-153">All route values.</span></span>|
|[<span data-ttu-id="415a0-154">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="415a0-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="415a0-155">Fragment adresy URL</span><span class="sxs-lookup"><span data-stu-id="415a0-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="415a0-156">Příklad odeslání do kontroleru</span><span class="sxs-lookup"><span data-stu-id="415a0-156">Submit to controller example</span></span>

<span data-ttu-id="415a0-157">Následující kód odešle formulář na `Index` akci, `HomeController` když je vybrán vstup nebo tlačítko:</span><span class="sxs-lookup"><span data-stu-id="415a0-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="415a0-158">Předchozí kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="415a0-159">Příklad odeslání na stránku</span><span class="sxs-lookup"><span data-stu-id="415a0-159">Submit to page example</span></span>

<span data-ttu-id="415a0-160">Následující kód odešle formulář na `About` stránku Razor:</span><span class="sxs-lookup"><span data-stu-id="415a0-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="415a0-161">Předchozí kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="415a0-162">Příklad odeslání do trasy</span><span class="sxs-lookup"><span data-stu-id="415a0-162">Submit to route example</span></span>

<span data-ttu-id="415a0-163">Vezměte v `/Home/Test` úvahu koncový bod:</span><span class="sxs-lookup"><span data-stu-id="415a0-163">Consider the `/Home/Test` endpoint:</span></span>

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

<span data-ttu-id="415a0-164">Následující kód odešle formulář do `/Home/Test` koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="415a0-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="415a0-165">Předchozí kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="415a0-166">Pomocná rutina vstupní značky</span><span class="sxs-lookup"><span data-stu-id="415a0-166">The Input Tag Helper</span></span>

<span data-ttu-id="415a0-167">Pomocná rutina vstupní značky váže vstupní prvek HTML [ \<>](https://www.w3.org/wiki/HTML/Elements/input) element na výraz modelu v zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="415a0-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="415a0-168">Syntaktick</span><span class="sxs-lookup"><span data-stu-id="415a0-168">Syntax:</span></span>

```HTML
<input asp-for="<Expression Name>">
```

<span data-ttu-id="415a0-169">Pomocná rutina vstupní značky:</span><span class="sxs-lookup"><span data-stu-id="415a0-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="415a0-170">Generuje atributy jazyka HTML pro název `asp-for` výrazu zadaného v atributu. `name` `id`</span><span class="sxs-lookup"><span data-stu-id="415a0-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="415a0-171">`asp-for="Property1.Property2"`je ekvivalentem `m => m.Property1.Property2`k.</span><span class="sxs-lookup"><span data-stu-id="415a0-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="415a0-172">Název výrazu je, který se používá pro `asp-for` hodnotu atributu.</span><span class="sxs-lookup"><span data-stu-id="415a0-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="415a0-173">Další informace najdete v části [názvy výrazů](#expression-names) .</span><span class="sxs-lookup"><span data-stu-id="415a0-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="415a0-174">Nastaví hodnotu atributu `type` HTML na základě typu modelu a atributů [poznámek k datům](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) použitým pro vlastnost modelu.</span><span class="sxs-lookup"><span data-stu-id="415a0-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="415a0-175">Pokud je zadaná hodnota `type` atributu HTML, nepřepíše se.</span><span class="sxs-lookup"><span data-stu-id="415a0-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="415a0-176">Generuje atributy ověřování [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) z atributů [poznámky k datům](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) použitých na vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="415a0-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="415a0-177">Má pomocná funkce HTML překryv s `Html.TextBoxFor` a `Html.EditorFor`.</span><span class="sxs-lookup"><span data-stu-id="415a0-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="415a0-178">Podrobnosti najdete v části **alternativy pomocníka HTML pro vstupní nápovědu ke značce** .</span><span class="sxs-lookup"><span data-stu-id="415a0-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="415a0-179">Poskytuje silné psaní.</span><span class="sxs-lookup"><span data-stu-id="415a0-179">Provides strong typing.</span></span> <span data-ttu-id="415a0-180">Pokud se název vlastnosti změní a pomocník značek neaktualizujete, zobrazí se chybová zpráva podobná následující:</span><span class="sxs-lookup"><span data-stu-id="415a0-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

<span data-ttu-id="415a0-181">Pomocník značek nastaví atribut HTML `type` na základě typu .NET. `Input`</span><span class="sxs-lookup"><span data-stu-id="415a0-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="415a0-182">Následující tabulka uvádí některé typy běžných typů .NET a generovaný typ HTML (ne každý typ .NET je uveden).</span><span class="sxs-lookup"><span data-stu-id="415a0-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="415a0-183">Typ .NET</span><span class="sxs-lookup"><span data-stu-id="415a0-183">.NET type</span></span>|<span data-ttu-id="415a0-184">Typ vstupu</span><span class="sxs-lookup"><span data-stu-id="415a0-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="415a0-185">Bool</span><span class="sxs-lookup"><span data-stu-id="415a0-185">Bool</span></span>|<span data-ttu-id="415a0-186">type="checkbox"</span><span class="sxs-lookup"><span data-stu-id="415a0-186">type="checkbox"</span></span>|
|<span data-ttu-id="415a0-187">String</span><span class="sxs-lookup"><span data-stu-id="415a0-187">String</span></span>|<span data-ttu-id="415a0-188">type="text"</span><span class="sxs-lookup"><span data-stu-id="415a0-188">type="text"</span></span>|
|<span data-ttu-id="415a0-189">DateTime</span><span class="sxs-lookup"><span data-stu-id="415a0-189">DateTime</span></span>|<span data-ttu-id="415a0-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="415a0-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="415a0-191">Byte</span><span class="sxs-lookup"><span data-stu-id="415a0-191">Byte</span></span>|<span data-ttu-id="415a0-192">Type = "Number"</span><span class="sxs-lookup"><span data-stu-id="415a0-192">type="number"</span></span>|
|<span data-ttu-id="415a0-193">Int</span><span class="sxs-lookup"><span data-stu-id="415a0-193">Int</span></span>|<span data-ttu-id="415a0-194">Type = "Number"</span><span class="sxs-lookup"><span data-stu-id="415a0-194">type="number"</span></span>|
|<span data-ttu-id="415a0-195">Jednoduchá, Dvojitá</span><span class="sxs-lookup"><span data-stu-id="415a0-195">Single, Double</span></span>|<span data-ttu-id="415a0-196">Type = "Number"</span><span class="sxs-lookup"><span data-stu-id="415a0-196">type="number"</span></span>|

<span data-ttu-id="415a0-197">V následující tabulce jsou uvedeny některé z běžných atributů [datových poznámek](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) , které bude Pomocník pro vstupní značky mapovat na konkrétní vstupní typy (ne každý atribut ověření je uveden):</span><span class="sxs-lookup"><span data-stu-id="415a0-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="415a0-198">Atribut</span><span class="sxs-lookup"><span data-stu-id="415a0-198">Attribute</span></span>|<span data-ttu-id="415a0-199">Typ vstupu</span><span class="sxs-lookup"><span data-stu-id="415a0-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="415a0-200">EmailAddress</span><span class="sxs-lookup"><span data-stu-id="415a0-200">[EmailAddress]</span></span>|<span data-ttu-id="415a0-201">Type = "e-mail"</span><span class="sxs-lookup"><span data-stu-id="415a0-201">type="email"</span></span>|
|<span data-ttu-id="415a0-202">Adresa URL</span><span class="sxs-lookup"><span data-stu-id="415a0-202">[Url]</span></span>|<span data-ttu-id="415a0-203">type="url"</span><span class="sxs-lookup"><span data-stu-id="415a0-203">type="url"</span></span>|
|<span data-ttu-id="415a0-204">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="415a0-204">[HiddenInput]</span></span>|<span data-ttu-id="415a0-205">Type = "Hidden"</span><span class="sxs-lookup"><span data-stu-id="415a0-205">type="hidden"</span></span>|
|<span data-ttu-id="415a0-206">Hovor</span><span class="sxs-lookup"><span data-stu-id="415a0-206">[Phone]</span></span>|<span data-ttu-id="415a0-207">Type = "tel"</span><span class="sxs-lookup"><span data-stu-id="415a0-207">type="tel"</span></span>|
|<span data-ttu-id="415a0-208">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="415a0-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="415a0-209">type="password"</span><span class="sxs-lookup"><span data-stu-id="415a0-209">type="password"</span></span>|
|<span data-ttu-id="415a0-210">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="415a0-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="415a0-211">type="date"</span><span class="sxs-lookup"><span data-stu-id="415a0-211">type="date"</span></span>|
|<span data-ttu-id="415a0-212">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="415a0-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="415a0-213">Type = "čas"</span><span class="sxs-lookup"><span data-stu-id="415a0-213">type="time"</span></span>|

<span data-ttu-id="415a0-214">Vzorku</span><span class="sxs-lookup"><span data-stu-id="415a0-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="415a0-215">Výše uvedený kód generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-215">The code above generates the following HTML:</span></span>

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

<span data-ttu-id="415a0-216">Datové poznámky použité pro `Email` vlastnosti a `Password` generují metadata modelu.</span><span class="sxs-lookup"><span data-stu-id="415a0-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="415a0-217">Pomocná rutina vstupní značky spotřebovává metadata modelu a vytváří atributy [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (viz [ověření modelu](../models/validation.md)).</span><span class="sxs-lookup"><span data-stu-id="415a0-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="415a0-218">Tyto atributy popisují validátory, které se mají připojit ke vstupním polím.</span><span class="sxs-lookup"><span data-stu-id="415a0-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="415a0-219">To poskytuje nenápadné ověřování HTML5 a [jQuery](https://jquery.com/) .</span><span class="sxs-lookup"><span data-stu-id="415a0-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="415a0-220">Nenápadující `data-val-rule="Error Message"`atributy mají formát, kde pravidlo je název pravidla ověření ( `data-val-required`například, `data-val-email`, `data-val-maxlength`atd.). Pokud je v atributu uvedena chybová zpráva, je zobrazena jako hodnota `data-val-rule` atributu.</span><span class="sxs-lookup"><span data-stu-id="415a0-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="415a0-221">K dispozici jsou také atributy formuláře `data-val-ruleName-argumentName="argumentValue"` , které poskytují další podrobnosti o pravidlu, `data-val-maxlength-max="1024"` například.</span><span class="sxs-lookup"><span data-stu-id="415a0-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="415a0-222">Pomocné možnosti pro pomocníka HTML pro pomocníka vstupní značky</span><span class="sxs-lookup"><span data-stu-id="415a0-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="415a0-223">`Html.TextBox`, `Html.TextBoxFor`a mají`Html.EditorFor` překrývající se funkce s pomocníkem vstupní značky `Html.Editor` .</span><span class="sxs-lookup"><span data-stu-id="415a0-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="415a0-224">Pomocná rutina vstupní značky automaticky nastaví `type` atribut. `Html.TextBox` a`Html.TextBoxFor` ne.</span><span class="sxs-lookup"><span data-stu-id="415a0-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="415a0-225">`Html.Editor`a `Html.EditorFor` zpracovávají kolekce, komplexní objekty a šablony; pomocná rutina vstupní značky nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="415a0-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="415a0-226">Pomocná rutina `Html.EditorFor` vstupní značky `Html.TextBoxFor` a jsou silného typu (používají lambda výrazy); `Html.TextBox` a`Html.Editor` nejsou (používají názvy výrazů).</span><span class="sxs-lookup"><span data-stu-id="415a0-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="415a0-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="415a0-227">HtmlAttributes</span></span>

<span data-ttu-id="415a0-228">`@Html.Editor()`a `@Html.EditorFor()` při spouštění jejich `ViewDataDictionary` výchozích šablon `htmlAttributes` použijte speciální položku s názvem.</span><span class="sxs-lookup"><span data-stu-id="415a0-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="415a0-229">Toto chování se volitelně rozšiřuje pomocí `additionalViewData` parametrů.</span><span class="sxs-lookup"><span data-stu-id="415a0-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="415a0-230">Klíč "htmlAttributes" rozlišuje malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="415a0-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="415a0-231">Klíč "htmlAttributes" se zpracovává podobně `htmlAttributes` jako objekt předaný do vstupů pomocníků, jako `@Html.TextBox()`je.</span><span class="sxs-lookup"><span data-stu-id="415a0-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="415a0-232">Názvy výrazů</span><span class="sxs-lookup"><span data-stu-id="415a0-232">Expression names</span></span>

<span data-ttu-id="415a0-233">Hodnota `asp-for` atributu `ModelExpression` je a pravá strana výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="415a0-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="415a0-234">Proto se `asp-for="Property1"` `m => m.Property1` ve vygenerovaném kódu zobrazí, což je důvod, proč nemusíte předponu `Model`používat.</span><span class="sxs-lookup"><span data-stu-id="415a0-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="415a0-235">Můžete použít\@znak "" pro spuštění vloženého výrazu a přesunutí `m.`před:</span><span class="sxs-lookup"><span data-stu-id="415a0-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe">
```

<span data-ttu-id="415a0-236">Generuje následující:</span><span class="sxs-lookup"><span data-stu-id="415a0-236">Generates the following:</span></span>

```HTML
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="415a0-237">Pomocí vlastností `asp-for="CollectionProperty[23].Member"` kolekce vygeneruje stejný název, `i` jako `asp-for="CollectionProperty[i].Member"` má hodnota `23`.</span><span class="sxs-lookup"><span data-stu-id="415a0-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="415a0-238">Když ASP.NET Core MVC vypočítá hodnotu `ModelExpression`, zkontroluje několik zdrojů, včetně. `ModelState`</span><span class="sxs-lookup"><span data-stu-id="415a0-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="415a0-239">Vezměte `<input type="text" asp-for="@Name">`v úvahu.</span><span class="sxs-lookup"><span data-stu-id="415a0-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="415a0-240">Počítaný `value` atribut je první hodnota, která není null od:</span><span class="sxs-lookup"><span data-stu-id="415a0-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="415a0-241">`ModelState`položka s klíčem "Name".</span><span class="sxs-lookup"><span data-stu-id="415a0-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="415a0-242">Výsledek výrazu `Model.Name`</span><span class="sxs-lookup"><span data-stu-id="415a0-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="415a0-243">Navigace podřízených vlastností</span><span class="sxs-lookup"><span data-stu-id="415a0-243">Navigating child properties</span></span>

<span data-ttu-id="415a0-244">Můžete také přejít k podřízeným vlastnostem pomocí cesty vlastností modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="415a0-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="415a0-245">Zvažte složitější třídu modelu, která obsahuje podřízenou `Address` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="415a0-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="415a0-246">V zobrazení se svážeme s `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="415a0-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="415a0-247">Následující kód HTML je vygenerován `Address.AddressLine1`pro:</span><span class="sxs-lookup"><span data-stu-id="415a0-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="415a0-248">Názvy a kolekce výrazů</span><span class="sxs-lookup"><span data-stu-id="415a0-248">Expression names and Collections</span></span>

<span data-ttu-id="415a0-249">Ukázka modelu obsahujícího pole `Colors`:</span><span class="sxs-lookup"><span data-stu-id="415a0-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="415a0-250">Metoda Action:</span><span class="sxs-lookup"><span data-stu-id="415a0-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

<span data-ttu-id="415a0-251">Následující syntaxe Razor ukazuje, jak přistupujete `Color` k určitému prvku:</span><span class="sxs-lookup"><span data-stu-id="415a0-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="415a0-252">Šablona *views/Shared/EditorTemplates/String. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="415a0-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="415a0-253">Ukázka pomocí `List<T>`:</span><span class="sxs-lookup"><span data-stu-id="415a0-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="415a0-254">Následující Razor ukazuje, jak iterovat v kolekci:</span><span class="sxs-lookup"><span data-stu-id="415a0-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="415a0-255">Šablona *views/Shared/EditorTemplates/ToDoItem. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="415a0-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="415a0-256">`foreach`by měla být použita, pokud je to možné, pokud se hodnota bude používat `asp-for` v `Html.DisplayFor` kontextu nebo ekvivalentním kontextu.</span><span class="sxs-lookup"><span data-stu-id="415a0-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="415a0-257">Obecně je lepší `for` , než `foreach` (pokud to scénář umožňuje), protože nemusí přidělovat enumerátor. vyhodnocení indexeru ve výrazu LINQ ale může být nákladné a mělo by být minimalizováno.</span><span class="sxs-lookup"><span data-stu-id="415a0-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="415a0-258">Výše uvedený vzorový kód ukazuje, jak byste měli výraz `@` lambda nahradit operátorem pro přístup ke každému `ToDoItem` ze seznamu.</span><span class="sxs-lookup"><span data-stu-id="415a0-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="415a0-259">Pomocník značek TextArea</span><span class="sxs-lookup"><span data-stu-id="415a0-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="415a0-260">Pomocný `Textarea Tag Helper` pomocník značek je podobný jako pomocník pro vstupní značky.</span><span class="sxs-lookup"><span data-stu-id="415a0-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="415a0-261">Vygeneruje atributy `name` [ \<](https://www.w3.org/wiki/HTML/Elements/textarea) a a atributy ověřování dat z modelu pro `id` prvek textarea > element.</span><span class="sxs-lookup"><span data-stu-id="415a0-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="415a0-262">Poskytuje silné psaní.</span><span class="sxs-lookup"><span data-stu-id="415a0-262">Provides strong typing.</span></span>

* <span data-ttu-id="415a0-263">Alternativní pomocník HTML:`Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="415a0-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="415a0-264">Vzorku</span><span class="sxs-lookup"><span data-stu-id="415a0-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="415a0-265">Vygeneruje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-265">The following HTML is generated:</span></span>

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

## <a name="the-label-tag-helper"></a><span data-ttu-id="415a0-266">Pomocník značek popisku</span><span class="sxs-lookup"><span data-stu-id="415a0-266">The Label Tag Helper</span></span>

* <span data-ttu-id="415a0-267">Vygeneruje popisek a `for` atribut [ \<popisku > elementu popisku](https://www.w3.org/wiki/HTML/Elements/label) pro název výrazu.</span><span class="sxs-lookup"><span data-stu-id="415a0-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="415a0-268">Alternativní pomocník HTML: `Html.LabelFor`.</span><span class="sxs-lookup"><span data-stu-id="415a0-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="415a0-269">`Label Tag Helper` Poskytuje následující výhody oproti čistě prvku popisku HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="415a0-270">Z `Display` atributu se automaticky získá popisná hodnota popisku.</span><span class="sxs-lookup"><span data-stu-id="415a0-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="415a0-271">Zamýšlené zobrazované jméno se může v průběhu času měnit a kombinace `Display` atributu a pomocníka značky Label bude `Display` používat všude, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="415a0-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="415a0-272">Míň značek ve zdrojovém kódu</span><span class="sxs-lookup"><span data-stu-id="415a0-272">Less markup in source code</span></span>

* <span data-ttu-id="415a0-273">Silné zadání pomocí vlastnosti modelu</span><span class="sxs-lookup"><span data-stu-id="415a0-273">Strong typing with the model property.</span></span>

<span data-ttu-id="415a0-274">Vzorku</span><span class="sxs-lookup"><span data-stu-id="415a0-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="415a0-275">Pro `<label>` element je vygenerován následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-275">The following HTML is generated for the `<label>` element:</span></span>

```HTML
<label for="Email">Email Address</label>
```

<span data-ttu-id="415a0-276">Pomocník značek Label vygeneroval `for` hodnotu atributu "e-mail", což je ID přidružené `<input>` k elementu.</span><span class="sxs-lookup"><span data-stu-id="415a0-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="415a0-277">Pomocník značek generuje konzistentní `id` prvky a `for` , aby je bylo možné správně přidružit.</span><span class="sxs-lookup"><span data-stu-id="415a0-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="415a0-278">Titulek v této ukázce pochází z `Display` atributu.</span><span class="sxs-lookup"><span data-stu-id="415a0-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="415a0-279">Pokud model neobsahoval `Display` atribut, popisek by byl názvem vlastnosti výrazu.</span><span class="sxs-lookup"><span data-stu-id="415a0-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="415a0-280">Pomocníka značek ověřování</span><span class="sxs-lookup"><span data-stu-id="415a0-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="415a0-281">K dispozici jsou dva pomocníky ověřovacích značek.</span><span class="sxs-lookup"><span data-stu-id="415a0-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="415a0-282">(Který zobrazuje ověřovací zprávu pro jednu vlastnost v modelu) `Validation Summary Tag Helper` a (která zobrazuje souhrn chyb ověřování). `Validation Message Tag Helper`</span><span class="sxs-lookup"><span data-stu-id="415a0-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="415a0-283">`Input Tag Helper` Přidá atributy ověřování na straně klienta HTML5 pro vstupní prvky na základě atributů datových poznámek v třídách modelu.</span><span class="sxs-lookup"><span data-stu-id="415a0-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="415a0-284">Ověřování se provádí také na serveru.</span><span class="sxs-lookup"><span data-stu-id="415a0-284">Validation is also performed on the server.</span></span> <span data-ttu-id="415a0-285">Pomocník pro ověřování značek zobrazí tyto chybové zprávy, když dojde k chybě ověření.</span><span class="sxs-lookup"><span data-stu-id="415a0-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="415a0-286">Pomocný pomocník značek ověřovací zprávy</span><span class="sxs-lookup"><span data-stu-id="415a0-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="415a0-287">Přidá atribut [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` do prvku [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) , který připojí chybové zprávy ověřování ve vstupním poli zadané vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="415a0-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="415a0-288">Když dojde k chybě ověřování na straně klienta, [jQuery](https://jquery.com/) zobrazí chybovou zprávu v `<span>` elementu.</span><span class="sxs-lookup"><span data-stu-id="415a0-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="415a0-289">Ověřování probíhá také na serveru.</span><span class="sxs-lookup"><span data-stu-id="415a0-289">Validation also takes place on the server.</span></span> <span data-ttu-id="415a0-290">Klienti můžou mít zakázaný JavaScript a některé ověřování se dá dělat jenom na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="415a0-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="415a0-291">Alternativní pomocník HTML:`Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="415a0-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="415a0-292">Se používá s atributem elementu span HTML. [](https://developer.mozilla.org/docs/Web/HTML/Element/span) `asp-validation-for` `Validation Message Tag Helper`</span><span class="sxs-lookup"><span data-stu-id="415a0-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```HTML
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="415a0-293">Pomocný pomocník značek ověřovací zprávy vygeneruje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="415a0-294">Obecně použijete `Validation Message Tag Helper` `Input` pomocníka značky pro stejnou vlastnost.</span><span class="sxs-lookup"><span data-stu-id="415a0-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="415a0-295">Tím se zobrazí všechny chybové zprávy ověřování poblíž vstupu, který způsobil chybu.</span><span class="sxs-lookup"><span data-stu-id="415a0-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="415a0-296">Musíte mít zobrazení se správnými odkazy skriptu JavaScript a [jQuery](https://jquery.com/) pro ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="415a0-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="415a0-297">Další informace najdete v tématu [ověřování modelu](../models/validation.md) .</span><span class="sxs-lookup"><span data-stu-id="415a0-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="415a0-298">Pokud dojde k chybě ověřování na straně serveru (například když máte zakázané vlastní ověřování na straně serveru nebo když je ověřování na straně klienta zakázané), MVC umístí tuto chybovou zprávu `<span>` jako tělo elementu.</span><span class="sxs-lookup"><span data-stu-id="415a0-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="415a0-299">Nápověda k souhrnným značkám ověřování</span><span class="sxs-lookup"><span data-stu-id="415a0-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="415a0-300">Cílí `<div>` na`asp-validation-summary` elementy s atributem.</span><span class="sxs-lookup"><span data-stu-id="415a0-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="415a0-301">Alternativní pomocník HTML:`@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="415a0-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="415a0-302">`Validation Summary Tag Helper` Slouží k zobrazení souhrnu ověřovacích zpráv.</span><span class="sxs-lookup"><span data-stu-id="415a0-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="415a0-303">Hodnota `asp-validation-summary` atributu může být libovolná z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="415a0-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="415a0-304">ASP – ověření – souhrn</span><span class="sxs-lookup"><span data-stu-id="415a0-304">asp-validation-summary</span></span>|<span data-ttu-id="415a0-305">Zobrazené ověřovací zprávy</span><span class="sxs-lookup"><span data-stu-id="415a0-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="415a0-306">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="415a0-306">ValidationSummary.All</span></span>|<span data-ttu-id="415a0-307">Úroveň vlastnosti a modelu</span><span class="sxs-lookup"><span data-stu-id="415a0-307">Property and model level</span></span>|
|<span data-ttu-id="415a0-308">Ovládací souhrnu ověření. ModelOnly</span><span class="sxs-lookup"><span data-stu-id="415a0-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="415a0-309">Model</span><span class="sxs-lookup"><span data-stu-id="415a0-309">Model</span></span>|
|<span data-ttu-id="415a0-310">Ovládací souhrnu ověření. None</span><span class="sxs-lookup"><span data-stu-id="415a0-310">ValidationSummary.None</span></span>|<span data-ttu-id="415a0-311">Žádné</span><span class="sxs-lookup"><span data-stu-id="415a0-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="415a0-312">Ukázka</span><span class="sxs-lookup"><span data-stu-id="415a0-312">Sample</span></span>

<span data-ttu-id="415a0-313">V následujícím příkladu je datový model upraven pomocí `DataAnnotation` atributů, které generují chybové zprávy ověřování `<input>` na elementu.</span><span class="sxs-lookup"><span data-stu-id="415a0-313">In the following example, the data model is decorated with `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="415a0-314">Pokud dojde k chybě ověřování, Pomocník pro ověření značky zobrazí chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="415a0-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="415a0-315">Generovaný kód HTML (Pokud je model platný):</span><span class="sxs-lookup"><span data-stu-id="415a0-315">The generated HTML (when the model is valid):</span></span>

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

## <a name="the-select-tag-helper"></a><span data-ttu-id="415a0-316">Pomocná rutina pro výběr značky</span><span class="sxs-lookup"><span data-stu-id="415a0-316">The Select Tag Helper</span></span>

* <span data-ttu-id="415a0-317">Generuje [vybrané](https://www.w3.org/wiki/HTML/Elements/select) a přidružené prvky [možností](https://www.w3.org/wiki/HTML/Elements/option) pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="415a0-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="415a0-318">Má alternativu `Html.DropDownListFor` k Pomocníkovi HTML a`Html.ListBoxFor`</span><span class="sxs-lookup"><span data-stu-id="415a0-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="415a0-319">[](https://www.w3.org/wiki/HTML/Elements/option) [](https://www.w3.org/wiki/HTML/Elements/select) `asp-items` Určuje název vlastnosti modelu pro element SELECT a určuje prvky možností. `Select Tag Helper` `asp-for`</span><span class="sxs-lookup"><span data-stu-id="415a0-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="415a0-320">Příklad:</span><span class="sxs-lookup"><span data-stu-id="415a0-320">For example:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="415a0-321">Vzorku</span><span class="sxs-lookup"><span data-stu-id="415a0-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="415a0-322">Metoda inicializuje, nastaví vybranou zemi `Index` a předá ji do zobrazení. `CountryViewModel` `Index`</span><span class="sxs-lookup"><span data-stu-id="415a0-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="415a0-323">Metoda HTTP Post `Index` zobrazuje výběr:</span><span class="sxs-lookup"><span data-stu-id="415a0-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="415a0-324">`Index` Zobrazení:</span><span class="sxs-lookup"><span data-stu-id="415a0-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="415a0-325">Který generuje následující HTML (s vybraným certifikačním úřadem):</span><span class="sxs-lookup"><span data-stu-id="415a0-325">Which generates the following HTML (with "CA" selected):</span></span>

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
> <span data-ttu-id="415a0-326">Nedoporučujeme používat `ViewBag` nebo `ViewData` s pomocníkem pro výběr značky.</span><span class="sxs-lookup"><span data-stu-id="415a0-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="415a0-327">Model zobrazení je robustnější při poskytování metadat MVC a obecně méně problematických.</span><span class="sxs-lookup"><span data-stu-id="415a0-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="415a0-328">Hodnota atributu je zvláštní případ a `Model` nevyžaduje předponu, ostatní pomocné `asp-items`atributy značek (například). `asp-for`</span><span class="sxs-lookup"><span data-stu-id="415a0-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="415a0-329">Vazba výčtu</span><span class="sxs-lookup"><span data-stu-id="415a0-329">Enum binding</span></span>

<span data-ttu-id="415a0-330">`<select>` Je často vhodné použít `enum` s vlastností a `enum` generovat `SelectListItem` prvky z hodnot.</span><span class="sxs-lookup"><span data-stu-id="415a0-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="415a0-331">Vzorku</span><span class="sxs-lookup"><span data-stu-id="415a0-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="415a0-332">`GetEnumSelectList` Metoda`SelectList` generuje objekt pro výčet.</span><span class="sxs-lookup"><span data-stu-id="415a0-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="415a0-333">Seznam enumerátorů můžete vyupravit pomocí `Display` atributu pro získání bohatšího uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="415a0-333">You can decorate your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="415a0-334">Vygeneruje se následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-334">The following HTML is generated:</span></span>

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

### <a name="option-group"></a><span data-ttu-id="415a0-335">Skupina možností</span><span class="sxs-lookup"><span data-stu-id="415a0-335">Option Group</span></span>

<span data-ttu-id="415a0-336">Prvek HTML [ \<optgroup >](https://www.w3.org/wiki/HTML/Elements/optgroup) je generována, když model zobrazení obsahuje jeden nebo více `SelectListGroup` objektů.</span><span class="sxs-lookup"><span data-stu-id="415a0-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="415a0-337">`CountryViewModelGroup` Seskupíprvkydoskupin"SeverníAmerika"`SelectListItem` a "Evropa":</span><span class="sxs-lookup"><span data-stu-id="415a0-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="415a0-338">Níže jsou uvedené dvě skupiny:</span><span class="sxs-lookup"><span data-stu-id="415a0-338">The two groups are shown below:</span></span>

![příklad skupiny možností](working-with-forms/_static/grp.png)

<span data-ttu-id="415a0-340">Generovaný kód HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-340">The generated HTML:</span></span>

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

### <a name="multiple-select"></a><span data-ttu-id="415a0-341">Vícenásobný výběr</span><span class="sxs-lookup"><span data-stu-id="415a0-341">Multiple select</span></span>

<span data-ttu-id="415a0-342">Pomocník pro výběr značky automaticky vygeneruje atribut [Multiple = Multiple](https://w3c.github.io/html-reference/select.html) , pokud je `asp-for` `IEnumerable`vlastnost zadaná v atributu.</span><span class="sxs-lookup"><span data-stu-id="415a0-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](https://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="415a0-343">Například s ohledem na následující model:</span><span class="sxs-lookup"><span data-stu-id="415a0-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="415a0-344">S následujícím zobrazením:</span><span class="sxs-lookup"><span data-stu-id="415a0-344">With the following view:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="415a0-345">Generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-345">Generates the following HTML:</span></span>

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

### <a name="no-selection"></a><span data-ttu-id="415a0-346">Žádný výběr</span><span class="sxs-lookup"><span data-stu-id="415a0-346">No selection</span></span>

<span data-ttu-id="415a0-347">Pokud se vám na více stránkách používá možnost neurčeno, můžete vytvořit šablonu pro odstranění opakujícího se kódu HTML:</span><span class="sxs-lookup"><span data-stu-id="415a0-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="415a0-348">Šablona *views/Shared/EditorTemplates/CountryViewModel. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="415a0-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="415a0-349">Přidání možnosti jazyka HTML [ \<>](https://www.w3.org/wiki/HTML/Elements/option) prvky není omezeno na případ *výběru* .</span><span class="sxs-lookup"><span data-stu-id="415a0-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="415a0-350">Například následující metoda zobrazení a akce vygeneruje HTML podobný kódu výše:</span><span class="sxs-lookup"><span data-stu-id="415a0-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="415a0-351">V závislosti `<option>` na aktuální `Country` hodnotě bude vybrán správný prvek `selected="selected"` (obsahující atribut).</span><span class="sxs-lookup"><span data-stu-id="415a0-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="415a0-352">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="415a0-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="415a0-353">Element HTML Form</span><span class="sxs-lookup"><span data-stu-id="415a0-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="415a0-354">Žádost o ověření tokenu</span><span class="sxs-lookup"><span data-stu-id="415a0-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="415a0-355">Rozhraní IAttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="415a0-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="415a0-356">Fragmenty kódu pro tento dokument</span><span class="sxs-lookup"><span data-stu-id="415a0-356">Code snippets for this document</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)

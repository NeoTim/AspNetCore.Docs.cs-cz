---
title: Tag Helpers in ASP.NET Core
author: rick-anderson
description: Learn what Tag Helpers are and how to use them in ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 15f94fd1c619e9f69c5783f664eafc9ca28f86f9
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239861"
---
# <a name="tag-helpers-in-aspnet-core"></a><span data-ttu-id="58b23-103">Tag Helpers in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58b23-103">Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="58b23-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="58b23-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="58b23-105">What are Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="58b23-105">What are Tag Helpers</span></span>

<span data-ttu-id="58b23-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.</span><span class="sxs-lookup"><span data-stu-id="58b23-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="58b23-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span><span class="sxs-lookup"><span data-stu-id="58b23-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="58b23-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span><span class="sxs-lookup"><span data-stu-id="58b23-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="58b23-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span><span class="sxs-lookup"><span data-stu-id="58b23-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="58b23-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span><span class="sxs-lookup"><span data-stu-id="58b23-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="58b23-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span><span class="sxs-lookup"><span data-stu-id="58b23-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="58b23-112">If you're familiar with [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.</span><span class="sxs-lookup"><span data-stu-id="58b23-112">If you're familiar with [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.</span></span> <span data-ttu-id="58b23-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span><span class="sxs-lookup"><span data-stu-id="58b23-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="58b23-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span><span class="sxs-lookup"><span data-stu-id="58b23-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="58b23-115">What Tag Helpers provide</span><span class="sxs-lookup"><span data-stu-id="58b23-115">What Tag Helpers provide</span></span>

<span data-ttu-id="58b23-116">**An HTML-friendly development experience** For the most part, Razor markup using Tag Helpers looks like standard HTML.</span><span class="sxs-lookup"><span data-stu-id="58b23-116">**An HTML-friendly development experience** For the most part, Razor markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="58b23-117">Front-end designers conversant with HTML/CSS/JavaScript can edit Razor without learning C# Razor syntax.</span><span class="sxs-lookup"><span data-stu-id="58b23-117">Front-end designers conversant with HTML/CSS/JavaScript can edit Razor without learning C# Razor syntax.</span></span>

<span data-ttu-id="58b23-118">**A rich IntelliSense environment for creating HTML and Razor markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in Razor views.</span><span class="sxs-lookup"><span data-stu-id="58b23-118">**A rich IntelliSense environment for creating HTML and Razor markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in Razor views.</span></span> <span data-ttu-id="58b23-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span><span class="sxs-lookup"><span data-stu-id="58b23-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="58b23-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span><span class="sxs-lookup"><span data-stu-id="58b23-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="58b23-121">Even developers experienced with Razor C# syntax are more productive using Tag Helpers than writing C# Razor markup.</span><span class="sxs-lookup"><span data-stu-id="58b23-121">Even developers experienced with Razor C# syntax are more productive using Tag Helpers than writing C# Razor markup.</span></span>

<span data-ttu-id="58b23-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span><span class="sxs-lookup"><span data-stu-id="58b23-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="58b23-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span><span class="sxs-lookup"><span data-stu-id="58b23-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="58b23-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span><span class="sxs-lookup"><span data-stu-id="58b23-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="58b23-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span><span class="sxs-lookup"><span data-stu-id="58b23-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="58b23-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span><span class="sxs-lookup"><span data-stu-id="58b23-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="58b23-127">Clients are guaranteed to get the current image.</span><span class="sxs-lookup"><span data-stu-id="58b23-127">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="58b23-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="58b23-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="58b23-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span><span class="sxs-lookup"><span data-stu-id="58b23-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="58b23-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span><span class="sxs-lookup"><span data-stu-id="58b23-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span></span> <span data-ttu-id="58b23-131">This attribute extracts the name of the specified model property into the rendered HTML.</span><span class="sxs-lookup"><span data-stu-id="58b23-131">This attribute extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="58b23-132">Consider a Razor view with the following model:</span><span class="sxs-lookup"><span data-stu-id="58b23-132">Consider a Razor view with the following model:</span></span>

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

<span data-ttu-id="58b23-133">The following Razor markup:</span><span class="sxs-lookup"><span data-stu-id="58b23-133">The following Razor markup:</span></span>

```cshtml
<label asp-for="Movie.Title"></label>
```

<span data-ttu-id="58b23-134">Generates the following HTML:</span><span class="sxs-lookup"><span data-stu-id="58b23-134">Generates the following HTML:</span></span>

```html
<label for="Movie_Title">Title</label>
```

<span data-ttu-id="58b23-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="58b23-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span></span> <span data-ttu-id="58b23-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span><span class="sxs-lookup"><span data-stu-id="58b23-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="58b23-137">Managing Tag Helper scope</span><span class="sxs-lookup"><span data-stu-id="58b23-137">Managing Tag Helper scope</span></span>

<span data-ttu-id="58b23-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span><span class="sxs-lookup"><span data-stu-id="58b23-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="58b23-139">`@addTagHelper` makes Tag Helpers available</span><span class="sxs-lookup"><span data-stu-id="58b23-139">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="58b23-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers*, the following *Views/_ViewImports.cshtml* file will be added to your project:</span><span class="sxs-lookup"><span data-stu-id="58b23-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers*, the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="58b23-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span><span class="sxs-lookup"><span data-stu-id="58b23-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="58b23-142">In this case, the view file is *Pages/_ViewImports.cshtml*, which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span><span class="sxs-lookup"><span data-stu-id="58b23-142">In this case, the view file is *Pages/_ViewImports.cshtml*, which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span></span> <span data-ttu-id="58b23-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly (*Microsoft.AspNetCore.Mvc.TagHelpers*) will be available to every view file in the *Views* directory or subdirectory.</span><span class="sxs-lookup"><span data-stu-id="58b23-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly (*Microsoft.AspNetCore.Mvc.TagHelpers*) will be available to every view file in the *Views* directory or subdirectory.</span></span> <span data-ttu-id="58b23-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="58b23-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="58b23-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="58b23-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="58b23-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers*), you would use the following:</span><span class="sxs-lookup"><span data-stu-id="58b23-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers*), you would use the following:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="58b23-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span><span class="sxs-lookup"><span data-stu-id="58b23-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="58b23-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span><span class="sxs-lookup"><span data-stu-id="58b23-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span></span> <span data-ttu-id="58b23-149">Most developers prefer to use the  "\*" wildcard syntax.</span><span class="sxs-lookup"><span data-stu-id="58b23-149">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="58b23-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span><span class="sxs-lookup"><span data-stu-id="58b23-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="58b23-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span><span class="sxs-lookup"><span data-stu-id="58b23-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="58b23-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span><span class="sxs-lookup"><span data-stu-id="58b23-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span></span> <span data-ttu-id="58b23-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span><span class="sxs-lookup"><span data-stu-id="58b23-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="58b23-154">`@removeTagHelper` removes Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="58b23-154">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="58b23-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span><span class="sxs-lookup"><span data-stu-id="58b23-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="58b23-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span><span class="sxs-lookup"><span data-stu-id="58b23-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="58b23-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder*.</span><span class="sxs-lookup"><span data-stu-id="58b23-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder*.</span></span>

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a><span data-ttu-id="58b23-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span><span class="sxs-lookup"><span data-stu-id="58b23-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="58b23-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span><span class="sxs-lookup"><span data-stu-id="58b23-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="58b23-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span><span class="sxs-lookup"><span data-stu-id="58b23-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="58b23-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span><span class="sxs-lookup"><span data-stu-id="58b23-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="58b23-162">Opting out of individual elements</span><span class="sxs-lookup"><span data-stu-id="58b23-162">Opting out of individual elements</span></span>

<span data-ttu-id="58b23-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span><span class="sxs-lookup"><span data-stu-id="58b23-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="58b23-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span><span class="sxs-lookup"><span data-stu-id="58b23-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="58b23-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span><span class="sxs-lookup"><span data-stu-id="58b23-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="58b23-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span><span class="sxs-lookup"><span data-stu-id="58b23-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="58b23-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span><span class="sxs-lookup"><span data-stu-id="58b23-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="58b23-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span><span class="sxs-lookup"><span data-stu-id="58b23-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="58b23-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span><span class="sxs-lookup"><span data-stu-id="58b23-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="58b23-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span><span class="sxs-lookup"><span data-stu-id="58b23-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```cshtml
@tagHelperPrefix th:
```

<span data-ttu-id="58b23-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span><span class="sxs-lookup"><span data-stu-id="58b23-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="58b23-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span><span class="sxs-lookup"><span data-stu-id="58b23-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span></span>

![obrázek](intro/_static/thp.png)

<span data-ttu-id="58b23-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span><span class="sxs-lookup"><span data-stu-id="58b23-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="self-closing-tag-helpers"></a><span data-ttu-id="58b23-175">Self-closing Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="58b23-175">Self-closing Tag Helpers</span></span>

<span data-ttu-id="58b23-176">Many Tag Helpers can't be used as self-closing tags.</span><span class="sxs-lookup"><span data-stu-id="58b23-176">Many Tag Helpers can't be used as self-closing tags.</span></span> <span data-ttu-id="58b23-177">Some Tag Helpers are designed to be self-closing tags.</span><span class="sxs-lookup"><span data-stu-id="58b23-177">Some Tag Helpers are designed to be self-closing tags.</span></span> <span data-ttu-id="58b23-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span><span class="sxs-lookup"><span data-stu-id="58b23-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span></span> <span data-ttu-id="58b23-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span><span class="sxs-lookup"><span data-stu-id="58b23-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span></span> <span data-ttu-id="58b23-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="58b23-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span></span>

## <a name="c-in-tag-helpers-attributedeclaration"></a><span data-ttu-id="58b23-181">C# in Tag Helpers attribute/declaration</span><span class="sxs-lookup"><span data-stu-id="58b23-181">C# in Tag Helpers attribute/declaration</span></span> 

<span data-ttu-id="58b23-182">Tag Helpers do not allow C# in the element's attribute or tag declaration area.</span><span class="sxs-lookup"><span data-stu-id="58b23-182">Tag Helpers do not allow C# in the element's attribute or tag declaration area.</span></span> <span data-ttu-id="58b23-183">For example, the following code is not valid:</span><span class="sxs-lookup"><span data-stu-id="58b23-183">For example, the following code is not valid:</span></span>

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

<span data-ttu-id="58b23-184">The preceding code can be written as:</span><span class="sxs-lookup"><span data-stu-id="58b23-184">The preceding code can be written as:</span></span>

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="58b23-185">IntelliSense support for Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="58b23-185">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="58b23-186">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.Razor.Tools".</span><span class="sxs-lookup"><span data-stu-id="58b23-186">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.Razor.Tools".</span></span> <span data-ttu-id="58b23-187">This is the package that adds Tag Helper tooling.</span><span class="sxs-lookup"><span data-stu-id="58b23-187">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="58b23-188">Consider writing an HTML `<label>` element.</span><span class="sxs-lookup"><span data-stu-id="58b23-188">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="58b23-189">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span><span class="sxs-lookup"><span data-stu-id="58b23-189">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![obrázek](intro/_static/label.png)

<span data-ttu-id="58b23-191">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span><span class="sxs-lookup"><span data-stu-id="58b23-191">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![obrázek](intro/_static/tagSym.png)

<span data-ttu-id="58b23-193">identifies the element as targeted by Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="58b23-193">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="58b23-194">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span><span class="sxs-lookup"><span data-stu-id="58b23-194">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="58b23-195">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span><span class="sxs-lookup"><span data-stu-id="58b23-195">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![obrázek](intro/_static/LableHtmlTag.png)

<span data-ttu-id="58b23-197">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span><span class="sxs-lookup"><span data-stu-id="58b23-197">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![obrázek](intro/_static/labelattr.png)

<span data-ttu-id="58b23-199">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span><span class="sxs-lookup"><span data-stu-id="58b23-199">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![obrázek](intro/_static/stmtcomplete.png)

<span data-ttu-id="58b23-201">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span><span class="sxs-lookup"><span data-stu-id="58b23-201">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="58b23-202">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span><span class="sxs-lookup"><span data-stu-id="58b23-202">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="58b23-203">If you're using the "Dark" theme the font is bold teal.</span><span class="sxs-lookup"><span data-stu-id="58b23-203">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="58b23-204">The images in this document were taken using the default theme.</span><span class="sxs-lookup"><span data-stu-id="58b23-204">The images in this document were taken using the default theme.</span></span>

![obrázek](intro/_static/labelaspfor2.png)

<span data-ttu-id="58b23-206">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span><span class="sxs-lookup"><span data-stu-id="58b23-206">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="58b23-207">IntelliSense displays all the methods and properties on the page model.</span><span class="sxs-lookup"><span data-stu-id="58b23-207">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="58b23-208">The methods and properties are available because the property type is `ModelExpression`.</span><span class="sxs-lookup"><span data-stu-id="58b23-208">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="58b23-209">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span><span class="sxs-lookup"><span data-stu-id="58b23-209">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![obrázek](intro/_static/intellemail.png)

<span data-ttu-id="58b23-211">IntelliSense lists the properties and methods available to the model on the page.</span><span class="sxs-lookup"><span data-stu-id="58b23-211">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="58b23-212">The rich IntelliSense environment helps you select the CSS class:</span><span class="sxs-lookup"><span data-stu-id="58b23-212">The rich IntelliSense environment helps you select the CSS class:</span></span>

![obrázek](intro/_static/iclass.png)

![obrázek](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="58b23-215">Tag Helpers compared to HTML Helpers</span><span class="sxs-lookup"><span data-stu-id="58b23-215">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="58b23-216">Tag Helpers attach to HTML elements in Razor views, while [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in Razor views.</span><span class="sxs-lookup"><span data-stu-id="58b23-216">Tag Helpers attach to HTML elements in Razor views, while [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in Razor views.</span></span> <span data-ttu-id="58b23-217">Consider the following Razor markup, which creates an HTML label with the CSS class "caption":</span><span class="sxs-lookup"><span data-stu-id="58b23-217">Consider the following Razor markup, which creates an HTML label with the CSS class "caption":</span></span>

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="58b23-218">The at (`@`) symbol tells Razor this is the start of code.</span><span class="sxs-lookup"><span data-stu-id="58b23-218">The at (`@`) symbol tells Razor this is the start of code.</span></span> <span data-ttu-id="58b23-219">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span><span class="sxs-lookup"><span data-stu-id="58b23-219">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="58b23-220">The last argument:</span><span class="sxs-lookup"><span data-stu-id="58b23-220">The last argument:</span></span>

```cshtml
new {@class="caption"}
```

<span data-ttu-id="58b23-221">Is an anonymous object used to represent attributes.</span><span class="sxs-lookup"><span data-stu-id="58b23-221">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="58b23-222">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span><span class="sxs-lookup"><span data-stu-id="58b23-222">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span></span> <span data-ttu-id="58b23-223">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign.</span><span class="sxs-lookup"><span data-stu-id="58b23-223">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign.</span></span> <span data-ttu-id="58b23-224">The entire line must be authored with no help from IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="58b23-224">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="58b23-225">Using the `LabelTagHelper`, the same markup can be written as:</span><span class="sxs-lookup"><span data-stu-id="58b23-225">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

<span data-ttu-id="58b23-226">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span><span class="sxs-lookup"><span data-stu-id="58b23-226">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![obrázek](intro/_static/label.png)

<span data-ttu-id="58b23-228">IntelliSense helps you write the entire line.</span><span class="sxs-lookup"><span data-stu-id="58b23-228">IntelliSense helps you write the entire line.</span></span>

<span data-ttu-id="58b23-229">The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="58b23-229">The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span></span>

![obrázek](intro/_static/regCS.png)

<span data-ttu-id="58b23-231">The Visual Studio editor displays C# code with a grey background.</span><span class="sxs-lookup"><span data-stu-id="58b23-231">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="58b23-232">For example, the `AntiForgeryToken` HTML Helper:</span><span class="sxs-lookup"><span data-stu-id="58b23-232">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```cshtml
@Html.AntiForgeryToken()
```

<span data-ttu-id="58b23-233">is displayed with a grey background.</span><span class="sxs-lookup"><span data-stu-id="58b23-233">is displayed with a grey background.</span></span> <span data-ttu-id="58b23-234">Most of the markup in the Register view is C#.</span><span class="sxs-lookup"><span data-stu-id="58b23-234">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="58b23-235">Compare that to the equivalent approach using Tag Helpers:</span><span class="sxs-lookup"><span data-stu-id="58b23-235">Compare that to the equivalent approach using Tag Helpers:</span></span>

![obrázek](intro/_static/regTH.png)

<span data-ttu-id="58b23-237">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span><span class="sxs-lookup"><span data-stu-id="58b23-237">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="58b23-238">The C# code is reduced to the minimum that the server needs to know about.</span><span class="sxs-lookup"><span data-stu-id="58b23-238">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="58b23-239">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span><span class="sxs-lookup"><span data-stu-id="58b23-239">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="58b23-240">Consider the *Email* group:</span><span class="sxs-lookup"><span data-stu-id="58b23-240">Consider the *Email* group:</span></span>

[!code-csharp[](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="58b23-241">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span><span class="sxs-lookup"><span data-stu-id="58b23-241">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span></span> <span data-ttu-id="58b23-242">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span><span class="sxs-lookup"><span data-stu-id="58b23-242">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="58b23-243">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span><span class="sxs-lookup"><span data-stu-id="58b23-243">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="58b23-244">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span><span class="sxs-lookup"><span data-stu-id="58b23-244">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="58b23-245">Tag Helpers compared to Web Server Controls</span><span class="sxs-lookup"><span data-stu-id="58b23-245">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="58b23-246">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span><span class="sxs-lookup"><span data-stu-id="58b23-246">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="58b23-247">ASP.NET [Web Server controls](https://msdn.microsoft.com/library/7698y1f0.aspx) are declared and invoked on a page.</span><span class="sxs-lookup"><span data-stu-id="58b23-247">ASP.NET [Web Server controls](https://msdn.microsoft.com/library/7698y1f0.aspx) are declared and invoked on a page.</span></span>

* <span data-ttu-id="58b23-248">[Web Server controls](https://msdn.microsoft.com/library/zsyt68f1.aspx) have a non-trivial lifecycle that can make developing and debugging difficult.</span><span class="sxs-lookup"><span data-stu-id="58b23-248">[Web Server controls](https://msdn.microsoft.com/library/zsyt68f1.aspx) have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="58b23-249">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span><span class="sxs-lookup"><span data-stu-id="58b23-249">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="58b23-250">Tag Helpers have no DOM.</span><span class="sxs-lookup"><span data-stu-id="58b23-250">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="58b23-251">Web Server controls include automatic browser detection.</span><span class="sxs-lookup"><span data-stu-id="58b23-251">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="58b23-252">Tag Helpers have no knowledge of the browser.</span><span class="sxs-lookup"><span data-stu-id="58b23-252">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="58b23-253">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span><span class="sxs-lookup"><span data-stu-id="58b23-253">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="58b23-254">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span><span class="sxs-lookup"><span data-stu-id="58b23-254">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="58b23-255">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span><span class="sxs-lookup"><span data-stu-id="58b23-255">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="58b23-256">Web Server controls use type converters to convert strings into objects.</span><span class="sxs-lookup"><span data-stu-id="58b23-256">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="58b23-257">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span><span class="sxs-lookup"><span data-stu-id="58b23-257">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="58b23-258">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span><span class="sxs-lookup"><span data-stu-id="58b23-258">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="58b23-259">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span><span class="sxs-lookup"><span data-stu-id="58b23-259">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="58b23-260">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="58b23-260">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="58b23-261">Customizing the Tag Helper element font</span><span class="sxs-lookup"><span data-stu-id="58b23-261">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="58b23-262">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors**:</span><span class="sxs-lookup"><span data-stu-id="58b23-262">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors**:</span></span>

![obrázek](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a><span data-ttu-id="58b23-264">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="58b23-264">Additional resources</span></span>

* [<span data-ttu-id="58b23-265">Author Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="58b23-265">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="58b23-266">Práce s formuláři</span><span class="sxs-lookup"><span data-stu-id="58b23-266">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* <span data-ttu-id="58b23-267">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="58b23-267">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](https://getbootstrap.com/).</span></span>

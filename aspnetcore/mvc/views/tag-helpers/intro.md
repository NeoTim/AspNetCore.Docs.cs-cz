---
title: Pomocné rutiny značek v ASP.NET Core
author: rick-anderson
description: Zjistěte, jaké jsou pomocné rutiny značek a jejich použití v ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 11d2914b5797735fb6a262a31bdb49f58391579f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64899121"
---
# <a name="tag-helpers-in-aspnet-core"></a><span data-ttu-id="d97a1-103">Pomocné rutiny značek v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d97a1-103">Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="d97a1-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d97a1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="d97a1-105">Co jsou pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="d97a1-105">What are Tag Helpers</span></span>

<span data-ttu-id="d97a1-106">Pomocné rutiny značek povolit kód na straně serveru k účasti na vytváření a vykreslování prvků HTML v souborech Razor.</span><span class="sxs-lookup"><span data-stu-id="d97a1-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="d97a1-107">Například předdefinované `ImageTagHelper` číslo verze můžete připojit k názvu image.</span><span class="sxs-lookup"><span data-stu-id="d97a1-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="d97a1-108">Pokaždé, když se změní na obrázku, server vygeneruje nové jedinečné verze pro bitovou kopii, proto je zaručeno, že klienti získat aktuální image (namísto zastaralých bitové kopie v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="d97a1-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="d97a1-109">Existuje mnoho integrovaných pomocných rutin značek pro běžné úlohy – například vytváření formulářů, odkazy, načítání prostředků a další – a ještě větší počet je dostupný ve veřejných úložištích GitHub a jako NuGet balíčky.</span><span class="sxs-lookup"><span data-stu-id="d97a1-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="d97a1-110">Pomocné rutiny značek jsou vytvořené v jazyce C# a cílí na základě název elementu, atributu nebo nadřazené značky elementů HTML.</span><span class="sxs-lookup"><span data-stu-id="d97a1-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="d97a1-111">Například předdefinované `LabelTagHelper` HTML můžete cílit na `<label>` element při `LabelTagHelper` atributy jsou použity.</span><span class="sxs-lookup"><span data-stu-id="d97a1-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="d97a1-112">Pokud jste obeznámeni s [pomocných rutin HTML](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), pomocných rutin značek snížit explicitní přechody mezi HTML a C# v zobrazení syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="d97a1-112">If you're familiar with [HTML Helpers](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.</span></span> <span data-ttu-id="d97a1-113">V mnoha případech pomocných rutin HTML poskytnout alternativní způsob konkrétní pomocné rutiny značky, ale je důležité uvědomit si, že pomocné rutiny značek nemáte nahradit pomocné rutiny HTML a není k dispozici pomocné rutiny značky pro každý pomocné rutiny HTML.</span><span class="sxs-lookup"><span data-stu-id="d97a1-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="d97a1-114">[Ve srovnání s pomocných rutin HTML pomocné rutiny značky](#tag-helpers-compared-to-html-helpers) vysvětluje rozdíly mezi více podrobností.</span><span class="sxs-lookup"><span data-stu-id="d97a1-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="d97a1-115">Co poskytuje pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="d97a1-115">What Tag Helpers provide</span></span>

<span data-ttu-id="d97a1-116">**Vývojové prostředí podporou HTML** pro největší část kódu Razor pomocí pomocných rutin značek vypadá jako standardní HTML.</span><span class="sxs-lookup"><span data-stu-id="d97a1-116">**An HTML-friendly development experience** For the most part, Razor markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="d97a1-117">Front-endu návrháři obeznámen s HTML/CSS a JavaScript můžete upravit Razor bez znalosti syntaxe jazyka C# Razor.</span><span class="sxs-lookup"><span data-stu-id="d97a1-117">Front-end designers conversant with HTML/CSS/JavaScript can edit Razor without learning C# Razor syntax.</span></span>

<span data-ttu-id="d97a1-118">**Bohaté prostředí IntelliSense pro vytváření značky HTML a Razor** jde sharp oproti pomocné rutiny HTML, předchozí přístup k serverové vytváření značky v zobrazení syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="d97a1-118">**A rich IntelliSense environment for creating HTML and Razor markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in Razor views.</span></span> <span data-ttu-id="d97a1-119">[Ve srovnání s pomocných rutin HTML pomocné rutiny značky](#tag-helpers-compared-to-html-helpers) vysvětluje rozdíly mezi více podrobností.</span><span class="sxs-lookup"><span data-stu-id="d97a1-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="d97a1-120">[Podpora IntelliSense pro pomocné rutiny značek](#intellisense-support-for-tag-helpers) vysvětluje prostředí IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="d97a1-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="d97a1-121">Zaznamenal se syntaxí Razor C# i vývojáře zvýší se produktivita použití pomocných rutin značek než psaní kódu jazyka C# Razor.</span><span class="sxs-lookup"><span data-stu-id="d97a1-121">Even developers experienced with Razor C# syntax are more productive using Tag Helpers than writing C# Razor markup.</span></span>

<span data-ttu-id="d97a1-122">**Způsob, jak budete produktivnější a nedokáže vytvořit robustnější a spolehlivé a udržovatelný kód pomocí informace, které jsou k dispozici pouze na serveru** v minulosti mantrou na aktualizace bitových kopií byl například chcete-li změnit název obrázku při změně na obrázku.</span><span class="sxs-lookup"><span data-stu-id="d97a1-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="d97a1-123">Obrázky agresivně do mezipaměti kvůli výkonu a pokud změníte název image, riskujete získat kopii zastaralých klientů.</span><span class="sxs-lookup"><span data-stu-id="d97a1-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="d97a1-124">V minulosti po obrázku se upraví, museli změnit název a každý odkaz na obrázek ve webové aplikaci potřeba aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="d97a1-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="d97a1-125">Nejenže je to velmi práce že náročné, je také náchylné k chybám (vám může přijít o odkaz, neúmyslném nesprávný řetězec atd.) Předdefinované `ImageTagHelper` můžete to udělal za vás automaticky.</span><span class="sxs-lookup"><span data-stu-id="d97a1-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="d97a1-126">`ImageTagHelper` Připojit verze čísla do image s názvem, tak pokaždé, když se změní na obrázku, server automaticky vygeneruje nové jedinečné verze pro bitovou kopii.</span><span class="sxs-lookup"><span data-stu-id="d97a1-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="d97a1-127">Je zaručeno, že klienti získat aktuální image.</span><span class="sxs-lookup"><span data-stu-id="d97a1-127">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="d97a1-128">Tato úspory odolnosti a práci dodává v podstatě bezplatné pomocí `ImageTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="d97a1-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="d97a1-129">Nejvíce integrovaných pomocných rutin značek cílit na standardní elementy jazyka HTML a poskytovat na straně serveru atributy pro element.</span><span class="sxs-lookup"><span data-stu-id="d97a1-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="d97a1-130">Například `<input>` použít v mnoha zobrazení v prvku *zobrazení/účet* složka obsahuje `asp-for` atribut.</span><span class="sxs-lookup"><span data-stu-id="d97a1-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span></span> <span data-ttu-id="d97a1-131">Tento atribut extrahuje název vlastnosti zadaný model zobrazený HTML.</span><span class="sxs-lookup"><span data-stu-id="d97a1-131">This attribute extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="d97a1-132">Vezměte v úvahu zobrazení Razor s modelem následující:</span><span class="sxs-lookup"><span data-stu-id="d97a1-132">Consider a Razor view with the following model:</span></span>

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

<span data-ttu-id="d97a1-133">Následující kód Razor:</span><span class="sxs-lookup"><span data-stu-id="d97a1-133">The following Razor markup:</span></span>

```cshtml
<label asp-for="Movie.Title"></label>
```

<span data-ttu-id="d97a1-134">Generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="d97a1-134">Generates the following HTML:</span></span>

```html
<label for="Movie_Title">Title</label>
```

<span data-ttu-id="d97a1-135">`asp-for` Atribut je k dispozici ve `For` vlastnost [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="d97a1-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span></span> <span data-ttu-id="d97a1-136">Zobrazit [pomocných rutin značek Autor](xref:mvc/views/tag-helpers/authoring) Další informace.</span><span class="sxs-lookup"><span data-stu-id="d97a1-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="d97a1-137">Správa pomocné rutiny značky oboru</span><span class="sxs-lookup"><span data-stu-id="d97a1-137">Managing Tag Helper scope</span></span>

<span data-ttu-id="d97a1-138">Obor pomocné rutiny značky je řízen pomocí kombinace `@addTagHelper`, `@removeTagHelper`a "!" znak odhlásit.</span><span class="sxs-lookup"><span data-stu-id="d97a1-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="d97a1-139">`@addTagHelper` zpřístupňuje pomocných rutin značek</span><span class="sxs-lookup"><span data-stu-id="d97a1-139">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="d97a1-140">Pokud vytvoříte novou webovou aplikaci ASP.NET Core s názvem *AuthoringTagHelpers*, následující *Views/_ViewImports.cshtml* soubor bude přidán do projektu:</span><span class="sxs-lookup"><span data-stu-id="d97a1-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers*, the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="d97a1-141">`@addTagHelper` – Direktiva zpřístupní pomocných rutin značek k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d97a1-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="d97a1-142">V takovém případě je soubor zobrazení *Pages/_ViewImports.cshtml*, která ve výchozím nastavení zdědí všechny soubory v *stránky* složce a jejích podsložkách; zpřístupnění pomocných rutin značek.</span><span class="sxs-lookup"><span data-stu-id="d97a1-142">In this case, the view file is *Pages/_ViewImports.cshtml*, which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span></span> <span data-ttu-id="d97a1-143">Výše uvedený kód používá syntaxe zástupných znaků ("\*") určíte, že všechny pomocných rutin značek v zadaném sestavení (*Microsoft.AspNetCore.Mvc.TagHelpers*) bude k dispozici pro každý soubor zobrazení v *zobrazení* adresáře nebo podadresáře.</span><span class="sxs-lookup"><span data-stu-id="d97a1-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly (*Microsoft.AspNetCore.Mvc.TagHelpers*) will be available to every view file in the *Views* directory or subdirectory.</span></span> <span data-ttu-id="d97a1-144">První parametr po `@addTagHelper` určuje pomocných rutin značek k načtení (používáme "\*" pro všechny pomocných rutin značek), a druhý parametr "Microsoft.AspNetCore.Mvc.TagHelpers" Určuje sestavení, který obsahuje pomocné rutiny značek.</span><span class="sxs-lookup"><span data-stu-id="d97a1-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="d97a1-145">*Microsoft.AspNetCore.Mvc.TagHelpers* je sestavení pro integrované pomocné rutiny značek základní technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="d97a1-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="d97a1-146">K vystavení všechny pomocných rutin značek v tomto projektu (která vytvoří sestavení s názvem *AuthoringTagHelpers*), můžete využít následující:</span><span class="sxs-lookup"><span data-stu-id="d97a1-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers*), you would use the following:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="d97a1-147">Pokud váš projekt obsahuje `EmailTagHelper` s výchozí obor názvů (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), můžete zadat plně kvalifikovaný název (FQN) pomocné rutiny značky:</span><span class="sxs-lookup"><span data-stu-id="d97a1-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="d97a1-148">Pomocné rutiny značky do zobrazení pomocí FQN, nejprve přidáte FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) a potom název sestavení (*AuthoringTagHelpers*).</span><span class="sxs-lookup"><span data-stu-id="d97a1-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span></span> <span data-ttu-id="d97a1-149">Většina vývojářů dávají přednost používání "\*" syntaxe zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="d97a1-149">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="d97a1-150">Syntaxe zástupných znaků umožňuje vložit zástupného znaku "\*" jako v FQN příponu.</span><span class="sxs-lookup"><span data-stu-id="d97a1-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="d97a1-151">Například některé z následující direktivy přinese `EmailTagHelper`:</span><span class="sxs-lookup"><span data-stu-id="d97a1-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="d97a1-152">Jak už bylo zmíněno dříve, přidání `@addTagHelper` direktivu *Views/_ViewImports.cshtml* souboru zpřístupní pomocné rutiny značky na všechny soubory zobrazit v *zobrazení* adresáře a podadresářů.</span><span class="sxs-lookup"><span data-stu-id="d97a1-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span></span> <span data-ttu-id="d97a1-153">Můžete použít `@addTagHelper` direktiv v souborech konkrétního zobrazení, pokud chcete vyjádřit výslovný souhlas pro vystavení pomocné rutiny značky do jenom tato zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d97a1-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="d97a1-154">`@removeTagHelper` Odebere pomocných rutin značek</span><span class="sxs-lookup"><span data-stu-id="d97a1-154">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="d97a1-155">`@removeTagHelper` Má dva stejné parametry jako `@addTagHelper`, a odebere pomocné rutiny značky, který byl dříve přidán.</span><span class="sxs-lookup"><span data-stu-id="d97a1-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="d97a1-156">Například `@removeTagHelper` platí pro konkrétní zobrazení odebere zadané pomocné rutiny značky ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d97a1-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="d97a1-157">Pomocí `@removeTagHelper` v *Views/Folder/_ViewImports.cshtml* soubor odebere ze všech zobrazení v zadané pomocné rutiny značky *složky*.</span><span class="sxs-lookup"><span data-stu-id="d97a1-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder*.</span></span>

### <a name="controlling-tag-helper-scope-with-the-viewimportscshtml-file"></a><span data-ttu-id="d97a1-158">Řízení pomocné rutiny značky oboru *_ViewImports.cshtml* souboru</span><span class="sxs-lookup"><span data-stu-id="d97a1-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="d97a1-159">Můžete přidat *_ViewImports.cshtml* do libovolné složky zobrazení a zobrazení, použije modul direktivy z obou tento soubor a *Views/_ViewImports.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="d97a1-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="d97a1-160">Pokud jste přidali prázdná *Views/Home/_ViewImports.cshtml* souboru *Domů* zobrazení, by existovat žádná změna protože *_ViewImports.cshtml* soubor je sčítání.</span><span class="sxs-lookup"><span data-stu-id="d97a1-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="d97a1-161">Žádné `@addTagHelper` direktivy, které přidáte do *Views/Home/_ViewImports.cshtml* souboru (, které nejsou ve výchozím *Views/_ViewImports.cshtml* souboru) by vystavit tyto pomocné rutiny značek k zobrazení pouze v *Domů* složky.</span><span class="sxs-lookup"><span data-stu-id="d97a1-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="d97a1-162">Výslovné odhlášení z jednotlivých prvků</span><span class="sxs-lookup"><span data-stu-id="d97a1-162">Opting out of individual elements</span></span>

<span data-ttu-id="d97a1-163">Můžete zakázat pomocné rutiny značky na úrovni prvku odhlásit znakem pomocné rutiny značky ("!").</span><span class="sxs-lookup"><span data-stu-id="d97a1-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="d97a1-164">Například `Email` ověření je zakázané v `<span>` odhlásit znakem pomocné rutiny značky:</span><span class="sxs-lookup"><span data-stu-id="d97a1-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="d97a1-165">Musíte použít znak odhlásit pomocné rutiny značky pro počáteční a koncovou značku.</span><span class="sxs-lookup"><span data-stu-id="d97a1-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="d97a1-166">(Editoru sady Visual Studio automaticky přidá znak výslovného nesouhlasu s uzavírací značku při přidejte jej do počáteční značka).</span><span class="sxs-lookup"><span data-stu-id="d97a1-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="d97a1-167">Po přidání znak výslovného nesouhlasu s prvkem a pomocné rutiny značky atributy se už nezobrazují rozlišovací písmem.</span><span class="sxs-lookup"><span data-stu-id="d97a1-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="d97a1-168">Pomocí `@tagHelperPrefix` provést explicitní použití pomocné rutiny značky</span><span class="sxs-lookup"><span data-stu-id="d97a1-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="d97a1-169">`@tagHelperPrefix` – Direktiva umožňuje zadat řetězec předpony značky k povolení podpory pomocné rutiny značky a aby explicitní použití pomocné rutiny značky.</span><span class="sxs-lookup"><span data-stu-id="d97a1-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="d97a1-170">Můžete například přidat následující kód k *Views/_ViewImports.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="d97a1-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```cshtml
@tagHelperPrefix th:
```

<span data-ttu-id="d97a1-171">Na následujícím obrázku kód předpony pomocné rutiny značky nastavena na `th:`, takže pouze elementy pomocí předpony `th:` podporují pomocné rutiny značek (pomocné rutiny značky povolena elementy mají zvláštní písma).</span><span class="sxs-lookup"><span data-stu-id="d97a1-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="d97a1-172">`<label>` a `<input>` elementy mají předponu pomocné rutiny značky a povolenými pomocné rutiny značky při `<span>` není element.</span><span class="sxs-lookup"><span data-stu-id="d97a1-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span></span>

![obrázek](intro/_static/thp.png)

<span data-ttu-id="d97a1-174">Stejná pravidla hierarchie, které se vztahují `@addTagHelper` platí také pro `@tagHelperPrefix`.</span><span class="sxs-lookup"><span data-stu-id="d97a1-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="self-closing-tag-helpers"></a><span data-ttu-id="d97a1-175">Samouzavírací pomocných rutin značek</span><span class="sxs-lookup"><span data-stu-id="d97a1-175">Self-closing Tag Helpers</span></span>

<span data-ttu-id="d97a1-176">Mnoho pomocných rutin značek nelze použít jako samouzavírací značky.</span><span class="sxs-lookup"><span data-stu-id="d97a1-176">Many Tag Helpers can't be used as self-closing tags.</span></span> <span data-ttu-id="d97a1-177">Několik pomocných rutin značek jsou navržené tak být samouzavírací značky.</span><span class="sxs-lookup"><span data-stu-id="d97a1-177">Some Tag Helpers are designed to be self-closing tags.</span></span> <span data-ttu-id="d97a1-178">Použití pomocné rutiny značky, který nebyl navržen, aby se samouzavírací potlačí vykresleného výstupu.</span><span class="sxs-lookup"><span data-stu-id="d97a1-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span></span> <span data-ttu-id="d97a1-179">Samouzavírací pomocné rutiny značky výsledkem samouzavírací značky v vykresleného výstupu.</span><span class="sxs-lookup"><span data-stu-id="d97a1-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span></span> <span data-ttu-id="d97a1-180">Další informace najdete v tématu [tato poznámka](xref:mvc/views/tag-helpers/authoring#self-closing) v [vytváření pomocných rutin značek](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="d97a1-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span></span>

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="d97a1-181">Podpora IntelliSense pro pomocné rutiny značek</span><span class="sxs-lookup"><span data-stu-id="d97a1-181">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="d97a1-182">Když vytvoříte novou webovou aplikaci ASP.NET Core v sadě Visual Studio, přidá balíček NuGet "Microsoft.AspNetCore.Razor.Tools".</span><span class="sxs-lookup"><span data-stu-id="d97a1-182">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.Razor.Tools".</span></span> <span data-ttu-id="d97a1-183">Toto je balíček, který přidá nástroje pomocné rutiny značky.</span><span class="sxs-lookup"><span data-stu-id="d97a1-183">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="d97a1-184">Zvažte vytvoření HTML `<label>` elementu.</span><span class="sxs-lookup"><span data-stu-id="d97a1-184">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="d97a1-185">Jakmile zadáte `<l` IntelliSense v editoru sady Visual Studio zobrazí odpovídající prvky:</span><span class="sxs-lookup"><span data-stu-id="d97a1-185">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![obrázek](intro/_static/label.png)

<span data-ttu-id="d97a1-187">Nejen že získáte Nápověda jazyka HTML, ale na ikonu ("@" symbol with "<>" je pod ním).</span><span class="sxs-lookup"><span data-stu-id="d97a1-187">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![obrázek](intro/_static/tagSym.png)

<span data-ttu-id="d97a1-189">identifikuje prvek jako cílem pomocných rutin značek.</span><span class="sxs-lookup"><span data-stu-id="d97a1-189">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="d97a1-190">Čistě prvky jazyka HTML (například `fieldset`) zobrazuje ikona "<>".</span><span class="sxs-lookup"><span data-stu-id="d97a1-190">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="d97a1-191">Prostý jazyk HTML `<label>` značky zobrazí značky HTML (s výchozí barevný motiv sady Visual Studio) Hnědý písmem, atributy červeně, a atribut hodnoty modrou barvu.</span><span class="sxs-lookup"><span data-stu-id="d97a1-191">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![obrázek](intro/_static/LableHtmlTag.png)

<span data-ttu-id="d97a1-193">Po zadání `<label`, IntelliSense vypíše dostupné atributy HTML/CSS a atributy cílené pomocné rutiny značky:</span><span class="sxs-lookup"><span data-stu-id="d97a1-193">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![obrázek](intro/_static/labelattr.png)

<span data-ttu-id="d97a1-195">Doplňování technologie IntelliSense umožňuje zadat klávesu tab k dokončení příkazu s vybranou hodnotu:</span><span class="sxs-lookup"><span data-stu-id="d97a1-195">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![obrázek](intro/_static/stmtcomplete.png)

<span data-ttu-id="d97a1-197">Poté, co je zadán atribut pomocné rutiny značky, změňte značku a atribut písma.</span><span class="sxs-lookup"><span data-stu-id="d97a1-197">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="d97a1-198">Použití výchozí sady Visual Studio "Blue" nebo "výraz Light" barevný motiv, je písmo tučné nachová.</span><span class="sxs-lookup"><span data-stu-id="d97a1-198">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="d97a1-199">Pokud používáte "Tmavý" motiv je písmo tučné šedozelená.</span><span class="sxs-lookup"><span data-stu-id="d97a1-199">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="d97a1-200">Obrázky v tomto dokumentu se nevytvořily pomocí výchozí motiv.</span><span class="sxs-lookup"><span data-stu-id="d97a1-200">The images in this document were taken using the default theme.</span></span>

![obrázek](intro/_static/labelaspfor2.png)

<span data-ttu-id="d97a1-202">Visual Studio můžete zadat *CompleteWord* místní (Ctrl + mezerník je [výchozí](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) do dvojitých uvozovek (""), a je nyní v jazyce C#, stejně jako by měly být v třída jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="d97a1-202">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="d97a1-203">Technologie IntelliSense zobrazí všechny metody a vlastnosti v modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="d97a1-203">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="d97a1-204">Metody a vlastnosti jsou k dispozici, protože je vlastnost typu `ModelExpression`.</span><span class="sxs-lookup"><span data-stu-id="d97a1-204">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="d97a1-205">Na obrázku níže, můžu jsem úpravy `Register` zobrazení, takže `RegisterViewModel` je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="d97a1-205">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![obrázek](intro/_static/intellemail.png)

<span data-ttu-id="d97a1-207">Technologie IntelliSense jsou uvedené vlastnosti a metody dostupné pro model na stránce.</span><span class="sxs-lookup"><span data-stu-id="d97a1-207">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="d97a1-208">Bohaté prostředí IntelliSense vám pomůže vybrat třídu šablony stylů CSS:</span><span class="sxs-lookup"><span data-stu-id="d97a1-208">The rich IntelliSense environment helps you select the CSS class:</span></span>

![obrázek](intro/_static/iclass.png)

![obrázek](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="d97a1-211">Pomocných rutin značek ve srovnání s pomocných rutin HTML</span><span class="sxs-lookup"><span data-stu-id="d97a1-211">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="d97a1-212">Pomocné rutiny značek připojit k elementů HTML v zobrazení syntaxe Razor, zatímco [pomocných rutin HTML](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) jsou vyvolány jako metody spolu s HTML v zobrazení syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="d97a1-212">Tag Helpers attach to HTML elements in Razor views, while [HTML Helpers](http://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in Razor views.</span></span> <span data-ttu-id="d97a1-213">Vezměte v úvahu následující kód Razor, která vytvoří popisek HTML pomocí třídy šablony stylů CSS "titulek":</span><span class="sxs-lookup"><span data-stu-id="d97a1-213">Consider the following Razor markup, which creates an HTML label with the CSS class "caption":</span></span>

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="d97a1-214">Na (`@`) symbol říká Razor, je toto začátek kódu.</span><span class="sxs-lookup"><span data-stu-id="d97a1-214">The at (`@`) symbol tells Razor this is the start of code.</span></span> <span data-ttu-id="d97a1-215">Následující dva parametry ("FirstName" a "jméno:") jsou řetězce, takže [IntelliSense](/visualstudio/ide/using-intellisense) nemůže pomoct.</span><span class="sxs-lookup"><span data-stu-id="d97a1-215">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="d97a1-216">Poslední argument:</span><span class="sxs-lookup"><span data-stu-id="d97a1-216">The last argument:</span></span>

```cshtml
new {@class="caption"}
```

<span data-ttu-id="d97a1-217">Je anonymní objekt používá k reprezentování atributy.</span><span class="sxs-lookup"><span data-stu-id="d97a1-217">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="d97a1-218">Protože `class` je vyhrazené klíčové slovo v C#, můžete použít `@` symbolů pro vynucení C# interpretovat `@class=` jako symbol (název vlastnosti).</span><span class="sxs-lookup"><span data-stu-id="d97a1-218">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span></span> <span data-ttu-id="d97a1-219">Pro front-endu návrháře (někdo zkušenosti s HTML/CSS a JavaScript a dalších technologií klienta, ale nejsou obeznámeni s C# a Razor), většina řádku je cizí.</span><span class="sxs-lookup"><span data-stu-id="d97a1-219">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign.</span></span> <span data-ttu-id="d97a1-220">Celý řádek musí být vytvořen žádný pomocí technologie IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="d97a1-220">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="d97a1-221">Použití `LabelTagHelper`, stejné značky lze zapsat jako:</span><span class="sxs-lookup"><span data-stu-id="d97a1-221">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

<span data-ttu-id="d97a1-222">Pomocná rutina značky verzi, jakmile zadáte `<l` IntelliSense v editoru sady Visual Studio zobrazí odpovídající prvky:</span><span class="sxs-lookup"><span data-stu-id="d97a1-222">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![obrázek](intro/_static/label.png)

<span data-ttu-id="d97a1-224">Technologie IntelliSense umožňuje napsat celý řádek.</span><span class="sxs-lookup"><span data-stu-id="d97a1-224">IntelliSense helps you write the entire line.</span></span>

<span data-ttu-id="d97a1-225">Následující kód obrázek ukazuje část formuláře *Views/Account/Register.cshtml* zobrazení Razor vygenerovaná z této šablony MVC ASP.NET 4.5.x součástí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d97a1-225">The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span></span>

![obrázek](intro/_static/regCS.png)

<span data-ttu-id="d97a1-227">Editor sady Visual Studio zobrazuje šedé pozadí kód C#.</span><span class="sxs-lookup"><span data-stu-id="d97a1-227">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="d97a1-228">Například `AntiForgeryToken` pomocné rutiny HTML:</span><span class="sxs-lookup"><span data-stu-id="d97a1-228">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```cshtml
@Html.AntiForgeryToken()
```

<span data-ttu-id="d97a1-229">Zobrazí se šedé pozadí.</span><span class="sxs-lookup"><span data-stu-id="d97a1-229">is displayed with a grey background.</span></span> <span data-ttu-id="d97a1-230">Většina kódu v registru zobrazení je C#.</span><span class="sxs-lookup"><span data-stu-id="d97a1-230">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="d97a1-231">Porovnejte ji s ekvivalentní postup použití pomocných rutin značek:</span><span class="sxs-lookup"><span data-stu-id="d97a1-231">Compare that to the equivalent approach using Tag Helpers:</span></span>

![obrázek](intro/_static/regTH.png)

<span data-ttu-id="d97a1-233">Kód je mnohem přehlednější a čitelnější, upravit a udržovat než pomocných rutin HTML.</span><span class="sxs-lookup"><span data-stu-id="d97a1-233">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="d97a1-234">Kód jazyka C# je snížit na minimum, kterou je potřeba vědět o serveru.</span><span class="sxs-lookup"><span data-stu-id="d97a1-234">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="d97a1-235">Editor sady Visual Studio zobrazí značky cílem pomocné rutiny značky rozlišovací písmem.</span><span class="sxs-lookup"><span data-stu-id="d97a1-235">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="d97a1-236">Vezměte v úvahu *e-mailu* skupiny:</span><span class="sxs-lookup"><span data-stu-id="d97a1-236">Consider the *Email* group:</span></span>

[!code-csharp[](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="d97a1-237">Každý atribut "asp-" má hodnotu "Email", ale není řetězce "E-mail".</span><span class="sxs-lookup"><span data-stu-id="d97a1-237">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span></span> <span data-ttu-id="d97a1-238">V tomto kontextu "Email" je C# model výrazu vlastnost `RegisterViewModel`.</span><span class="sxs-lookup"><span data-stu-id="d97a1-238">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="d97a1-239">Editor sady Visual Studio vám pomůže psát **všechny** značek v pomocné rutiny značky přístup register formuláře, zatímco Visual Studio poskytuje většinu kódu v metodě pomocných rutin HTML není žádná nápověda.</span><span class="sxs-lookup"><span data-stu-id="d97a1-239">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="d97a1-240">[Podpora IntelliSense pro pomocné rutiny značek](#intellisense-support-for-tag-helpers) obsahuje podrobnosti o práci s pomocných rutin značek v editoru sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d97a1-240">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="d97a1-241">Pomocných rutin značek ve srovnání s ovládací prvky webového serveru</span><span class="sxs-lookup"><span data-stu-id="d97a1-241">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="d97a1-242">Pomocné rutiny značek nevlastníte prvek, na který jste spojené s; jednoduše účast při vykreslování element a obsahu.</span><span class="sxs-lookup"><span data-stu-id="d97a1-242">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="d97a1-243">ASP.NET [ovládací prvky webového serveru](https://msdn.microsoft.com/library/7698y1f0.aspx) deklarovány a vyvolána na stránce.</span><span class="sxs-lookup"><span data-stu-id="d97a1-243">ASP.NET [Web Server controls](https://msdn.microsoft.com/library/7698y1f0.aspx) are declared and invoked on a page.</span></span>

* <span data-ttu-id="d97a1-244">[Ovládací prvky webového serveru](https://msdn.microsoft.com/library/zsyt68f1.aspx) mají netriviální životní cyklus, který může ztěžovat vývoji a ladění.</span><span class="sxs-lookup"><span data-stu-id="d97a1-244">[Web Server controls](https://msdn.microsoft.com/library/zsyt68f1.aspx) have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="d97a1-245">Ovládací prvky webového serveru umožňují přidat funkce na prvky Document Object Model (DOM) klient pomocí ovládacího prvku klienta.</span><span class="sxs-lookup"><span data-stu-id="d97a1-245">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="d97a1-246">Pomocné rutiny značek mít žádné modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="d97a1-246">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="d97a1-247">Ovládací prvky webového serveru zahrnují automatickou detekci prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d97a1-247">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="d97a1-248">Pomocné rutiny značek nemají žádné informace o prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d97a1-248">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="d97a1-249">Několik pomocných rutin značek může fungovat u stejného elementu (naleznete v tématu [vyhnout pomocné rutiny značky konfliktů](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) zatímco obvykle nelze vytvořit ovládací prvky webového serveru.</span><span class="sxs-lookup"><span data-stu-id="d97a1-249">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="d97a1-250">Můžete upravit značky a obsah prvků HTML, která jsou omezená na pomocných rutin značek, ale nemusíte upravovat přímo cokoli, na stránce.</span><span class="sxs-lookup"><span data-stu-id="d97a1-250">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="d97a1-251">Ovládací prvky webového serveru mají méně konkrétní rozsah a můžete provádět akce, které ovlivňují ostatní části stránky; povolení nezamýšlenými vedlejšími účinky.</span><span class="sxs-lookup"><span data-stu-id="d97a1-251">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="d97a1-252">Ovládací prvky webového serveru pomocí převaděče typů pro převod řetězců na objekty.</span><span class="sxs-lookup"><span data-stu-id="d97a1-252">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="d97a1-253">Pomocné rutiny značek můžete pracovat nativně v jazyce C#, takže není nutné pro převod typu.</span><span class="sxs-lookup"><span data-stu-id="d97a1-253">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="d97a1-254">Použijte ovládací prvky webového serveru [System.ComponentModel](/dotnet/api/system.componentmodel) k implementaci chování za běhu a návrhu komponent a ovládacích prvků.</span><span class="sxs-lookup"><span data-stu-id="d97a1-254">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="d97a1-255">`System.ComponentModel` obsahuje základní třídy a rozhraní pro implementaci atributy a převaděče typů vazeb na zdroje dat a součásti licencování.</span><span class="sxs-lookup"><span data-stu-id="d97a1-255">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="d97a1-256">Kontrast, který pomocných rutin značek, které obvykle jsou odvozeny z `TagHelper`a `TagHelper` základní třída poskytuje pouze dvě metody `Process` a `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="d97a1-256">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="d97a1-257">Přizpůsobení písma elementu pomocné rutiny značky</span><span class="sxs-lookup"><span data-stu-id="d97a1-257">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="d97a1-258">Můžete přizpůsobit písma a barevné zvýraznění z **nástroje** > **možnosti** > **prostředí** > **písma a barvy**:</span><span class="sxs-lookup"><span data-stu-id="d97a1-258">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors**:</span></span>

![obrázek](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a><span data-ttu-id="d97a1-260">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d97a1-260">Additional resources</span></span>

* [<span data-ttu-id="d97a1-261">Autor pomocných rutin značek</span><span class="sxs-lookup"><span data-stu-id="d97a1-261">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="d97a1-262">Práce s formuláři</span><span class="sxs-lookup"><span data-stu-id="d97a1-262">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* <span data-ttu-id="d97a1-263">[TagHelperSamples na Githubu](https://github.com/dpaquette/TagHelperSamples) obsahuje pomocné rutiny značky ukázky pro práci s [Bootstrap](http://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="d97a1-263">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](http://getbootstrap.com/).</span></span>

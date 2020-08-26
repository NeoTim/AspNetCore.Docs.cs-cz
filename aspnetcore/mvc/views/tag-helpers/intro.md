---
title: Tagování pomocníků v ASP.NET Core
author: rick-anderson
description: Přečtěte si, co jsou pomocníky značek a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 345d20494111b808dac9678637de060169730a53
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865347"
---
# <a name="tag-helpers-in-aspnet-core"></a><span data-ttu-id="eb0ee-103">Tagování pomocníků v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb0ee-103">Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="eb0ee-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eb0ee-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="eb0ee-105">Co jsou pomocníky značek</span><span class="sxs-lookup"><span data-stu-id="eb0ee-105">What are Tag Helpers</span></span>

<span data-ttu-id="eb0ee-106">Pomáhat pomocníkům při vytváření kódu a vykreslování prvků HTML v souborech, které umožňují kód na straně serveru Razor</span><span class="sxs-lookup"><span data-stu-id="eb0ee-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="eb0ee-107">Například předdefinovaná `ImageTagHelper` verze může k názvu image připojit číslo verze.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="eb0ee-108">Pokaždé, když se obrázek změní, server vygeneruje pro Image novou jedinečnou verzi, takže klienti budou mít zaručený přístup k aktuální imagi (místo k zastaralému obrázku v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="eb0ee-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="eb0ee-109">K dispozici je mnoho vestavěných pomocníků značek pro běžné úkoly, jako je vytváření formulářů, odkazů, načítání assetů a ještě více dostupných ve veřejných úložištích GitHub a jako balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="eb0ee-110">Pomocníky značek jsou vytvořeny v jazyce C# a jsou cíleny na prvky HTML na základě názvu elementu, názvu atributu nebo nadřazené značky.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="eb0ee-111">Například Vestavěná `LabelTagHelper` možnost může cílit na prvek jazyka HTML `<label>` při `LabelTagHelper` použití atributů.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="eb0ee-112">Pokud jste obeznámeni s [pomocníky HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), značky pomocníků vám omezí explicitní přechody mezi HTML a C# v Razor zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-112">If you're familiar with [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.</span></span> <span data-ttu-id="eb0ee-113">V mnoha případech pomocné rutiny HTML poskytují alternativní přístup k konkrétní pomocné rutině značek, ale je důležité rozpoznat, že pomocné rutiny značky nenahrazují nápovědu HTML a že pro každou pomocnou nápovědu HTML není k dispozici pomocný pomocník značek.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="eb0ee-114">[Pomocník značek v porovnání s pomocníky HTML](#tag-helpers-compared-to-html-helpers) vysvětluje rozdíly podrobněji.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="eb0ee-115">Co poskytují pomocníky značek</span><span class="sxs-lookup"><span data-stu-id="eb0ee-115">What Tag Helpers provide</span></span>

<span data-ttu-id="eb0ee-116">**Prostředí pro vývoj s uživatelsky přívětivými HTML** Ve většině případů Razor značky pomocí pomocníků značek vypadají jako standardní HTML.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-116">**An HTML-friendly development experience** For the most part, Razor markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="eb0ee-117">Návrháři front-end Conversant s HTML/CSS/JavaScriptem mohou upravovat Razor bez výuky Razor syntaxe jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-117">Front-end designers conversant with HTML/CSS/JavaScript can edit Razor without learning C# Razor syntax.</span></span>

<span data-ttu-id="eb0ee-118">**Bohatý prostředí IntelliSense pro vytváření kódu HTML a Razor značení** je ostrého kontrastu s pomocníky HTML, předchozím přístupem k vytváření značek v zobrazeních na straně serveru Razor .</span><span class="sxs-lookup"><span data-stu-id="eb0ee-118">**A rich IntelliSense environment for creating HTML and Razor markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in Razor views.</span></span> <span data-ttu-id="eb0ee-119">[Pomocník značek v porovnání s pomocníky HTML](#tag-helpers-compared-to-html-helpers) vysvětluje rozdíly podrobněji.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="eb0ee-120">[Podpora technologie IntelliSense pro pomocníci značek](#intellisense-support-for-tag-helpers) vysvětluje prostředí IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="eb0ee-121">I vývojáři, kteří se s Razor syntaxí jazyka c# setkali, mají vyšší produktivitu pomocí pomocníků značek než psaní kódu jazyka c# Razor .</span><span class="sxs-lookup"><span data-stu-id="eb0ee-121">Even developers experienced with Razor C# syntax are more productive using Tag Helpers than writing C# Razor markup.</span></span>

<span data-ttu-id="eb0ee-122">**Způsob, jak zvýšit produktivitu a umožnit vám poskytovat robustnější, spolehlivý a udržovatelný kód s využitím informací, které jsou k dispozici pouze na serveru** Například historicky heslo při aktualizaci imagí bylo změnit název obrázku při změně obrázku.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="eb0ee-123">Obrázky by měly být agresivní v mezipaměti z důvodu výkonu, a pokud nezměníte název obrázku, riskujete, že klienti získávají starou kopii.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="eb0ee-124">V minulosti se po úpravě obrázku změnil název a každý odkaz na obrázek ve webové aplikaci, který je potřeba aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="eb0ee-125">Nejenom je to velmi náročné na práci, je to také náchylné k chybám (může se stát, že nechtěně zadáte špatný řetězec atd.). Integrovaná `ImageTagHelper` možnost může to provést automaticky.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="eb0ee-126">Rozhraní `ImageTagHelper` může k názvu image připojit číslo verze, takže když se obrázek změní, server automaticky vygeneruje novou jedinečnou verzi image.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="eb0ee-127">Klientům je zaručeno získat aktuální bitovou kopii.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-127">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="eb0ee-128">Tato odolnost a úspory na práci jsou v podstatě bezplatné pomocí `ImageTagHelper` .</span><span class="sxs-lookup"><span data-stu-id="eb0ee-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="eb0ee-129">Většina vestavěných pomocníků značek cílí na standardní prvky HTML a poskytují atributy na straně serveru pro element.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="eb0ee-130">Například `<input>` prvek použitý v mnoha zobrazeních ve složce *views/Account* obsahuje `asp-for` atribut.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span></span> <span data-ttu-id="eb0ee-131">Tento atribut extrahuje název zadané vlastnosti modelu do vykresleného HTML.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-131">This attribute extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="eb0ee-132">Vezměte v úvahu Razor zobrazení s následujícím modelem:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-132">Consider a Razor view with the following model:</span></span>

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

<span data-ttu-id="eb0ee-133">Následující Razor kód:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-133">The following Razor markup:</span></span>

```cshtml
<label asp-for="Movie.Title"></label>
```

<span data-ttu-id="eb0ee-134">Generuje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-134">Generates the following HTML:</span></span>

```html
<label for="Movie_Title">Title</label>
```

<span data-ttu-id="eb0ee-135">`asp-for`Atribut je zpřístupněn `For` vlastností v [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="eb0ee-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span></span> <span data-ttu-id="eb0ee-136">Další informace najdete v tématu [autora značek pomocníka](xref:mvc/views/tag-helpers/authoring) .</span><span class="sxs-lookup"><span data-stu-id="eb0ee-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="eb0ee-137">Správa oboru pomocníka značek</span><span class="sxs-lookup"><span data-stu-id="eb0ee-137">Managing Tag Helper scope</span></span>

<span data-ttu-id="eb0ee-138">Obor pomocníků značek je ovládán kombinací `@addTagHelper` , `@removeTagHelper` a znaku "!" pro odhlášení.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="eb0ee-139">`@addTagHelper` zpřístupňuje pomocníkům značky</span><span class="sxs-lookup"><span data-stu-id="eb0ee-139">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="eb0ee-140">Pokud vytvoříte novou ASP.NET Core webovou aplikaci s názvem *AuthoringTagHelpers*, budou do projektu přidány následující soubory *zobrazení nebo _ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eb0ee-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers*, the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="eb0ee-141">`@addTagHelper`Direktiva zpřístupňuje pomocníkům značky pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="eb0ee-142">V tomto případě je soubor zobrazení *stránky/_ViewImports. cshtml*, který je ve výchozím nastavení děděn všemi soubory ve složce a složkách *stránky* . zpřístupňují se pomocníkům značek.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-142">In this case, the view file is *Pages/_ViewImports.cshtml*, which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span></span> <span data-ttu-id="eb0ee-143">Výše uvedený kód používá syntaxi zástupných znaků (" \* ") k určení, že všechny pomocníky značek v zadaném sestavení (*Microsoft. AspNetCore. Mvc. TagHelpers*) budou k dispozici pro každý soubor zobrazení v adresáři *zobrazení* nebo v podadresáři.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly (*Microsoft.AspNetCore.Mvc.TagHelpers*) will be available to every view file in the *Views* directory or subdirectory.</span></span> <span data-ttu-id="eb0ee-144">První parametr po `@addTagHelper` určení pomocníka značek pro načtení (používáme " \* " pro všechny pomocníky značky) a druhý parametr "Microsoft. AspNetCore. Mvc. TagHelpers" Určuje sestavení obsahující pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="eb0ee-145">*Microsoft. AspNetCore. Mvc. TagHelpers* je sestavení pro předdefinované ASP.NET Core pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="eb0ee-146">Chcete-li zveřejnit všechny pomocníky značek v tomto projektu (který vytvoří sestavení s názvem *AuthoringTagHelpers*), použijte následující:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers*), you would use the following:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="eb0ee-147">Pokud projekt obsahuje `EmailTagHelper` s výchozím oborem názvů ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ), můžete zadat plně kvalifikovaný název (FQN) pomocné rutiny značky:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="eb0ee-148">Chcete-li přidat pomocníka značek pro zobrazení pomocí FQN, nejprve přidejte FQN ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ) a potom název sestavení (*AuthoringTagHelpers*).</span><span class="sxs-lookup"><span data-stu-id="eb0ee-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span></span> <span data-ttu-id="eb0ee-149">Většina vývojářů upřednostňuje použití \* Syntaxe zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-149">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="eb0ee-150">Syntaxe zástupných znaků umožňuje vložit zástupný znak " \* " jako příponu do FQN.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="eb0ee-151">Například kterákoli z následujících direktiv bude přinášet do `EmailTagHelper` :</span><span class="sxs-lookup"><span data-stu-id="eb0ee-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="eb0ee-152">Jak bylo zmíněno dříve, přidání `@addTagHelper` direktivy do souboru views */_ViewImports. cshtml* zpřístupní pomocníka značek pro všechny soubory zobrazení v adresáři *zobrazení* a podadresářích.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span></span> <span data-ttu-id="eb0ee-153">Direktivu můžete použít pro `@addTagHelper` konkrétní soubory zobrazení, pokud se chcete přihlásit k vystavení pomocníka značek jenom pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="eb0ee-154">`@removeTagHelper` Odebere pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-154">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="eb0ee-155">`@removeTagHelper`Má stejné dva parametry jako `@addTagHelper` a odebere pomocníka značek, který byl dříve přidán.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="eb0ee-156">Například `@removeTagHelper` použití na konkrétní zobrazení odebere ze zobrazení určenou pomocnou značku.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="eb0ee-157">Použití `@removeTagHelper` v souboru *views/Folder/_ViewImports. cshtml* odstraní určenou pomocnou značku ze všech zobrazení ve *složce*.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder*.</span></span>

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a><span data-ttu-id="eb0ee-158">Řízení oboru pomocné rutiny značek pomocí souboru *_ViewImports. cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb0ee-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="eb0ee-159">*_ViewImports. cshtml* můžete přidat do libovolné složky zobrazení a modul zobrazení použije direktivy z obou souborů i ze souboru *views/_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="eb0ee-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="eb0ee-160">Pokud jste přidali prázdné *zobrazení/soubor _ViewImports. cshtml* pro *domovskou* zobrazení, nebude by žádná změna, protože *_ViewImports soubor. cshtml* je aditivní.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="eb0ee-161">Jakékoli `@addTagHelper` direktivy, které přidáte do souboru *views/Home/_ViewImports. cshtml* (které nejsou ve výchozím *zobrazení/_ViewImports. cshtml* ), by tyto pomocníky značek mohli vystavit pouze pro zobrazení v *domovské* složce.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="eb0ee-162">Zrušení individuálních prvků</span><span class="sxs-lookup"><span data-stu-id="eb0ee-162">Opting out of individual elements</span></span>

<span data-ttu-id="eb0ee-163">Pomocníka značek můžete zakázat na úrovni elementu pomocí znaku pro výslovný odkaz na značku ("!").</span><span class="sxs-lookup"><span data-stu-id="eb0ee-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="eb0ee-164">`Email`Ověřování je například zakázáno v `<span>` nástroji pomocí znaku pro výslovný souhlas s pomocníkem značek:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="eb0ee-165">Pro otevírací a uzavírací značku musíte použít znak výslovného souhlasu s tagem.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="eb0ee-166">(Editor sady Visual Studio automaticky přidá znak pro odhlášení do uzavírací značky, když ho přidáte do počáteční značky).</span><span class="sxs-lookup"><span data-stu-id="eb0ee-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="eb0ee-167">Po přidání znaku pro odhlášení se prvky a pomocné atributy značky již nebudou zobrazovat v charakteristickém písmu.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="eb0ee-168">Použití `@tagHelperPrefix` k nastavení explicitního použití pomocníka značek</span><span class="sxs-lookup"><span data-stu-id="eb0ee-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="eb0ee-169">`@tagHelperPrefix`Direktiva umožňuje zadat řetězec předpony značky pro povolení podpory značek a nastavit explicitní použití pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="eb0ee-170">Například můžete přidat následující kód do souboru *views/_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eb0ee-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```cshtml
@tagHelperPrefix th:
```

<span data-ttu-id="eb0ee-171">V níže uvedeném obrázku kódu je předpona pomocníka značek nastavena na hodnotu `th:` , takže pouze ty prvky, které používají předpony `th:` značky support, mají pro pomocná pole značka (s podporou značek).</span><span class="sxs-lookup"><span data-stu-id="eb0ee-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="eb0ee-172">`<label>`Elementy a `<input>` mají předponu pomocné rutiny značky a jsou zapnuty značkou pomocníka značek, zatímco `<span>` element není.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span></span>

![image](intro/_static/thp.png)

<span data-ttu-id="eb0ee-174">Stejná pravidla hierarchie, která platí `@addTagHelper` také pro platí pro `@tagHelperPrefix` .</span><span class="sxs-lookup"><span data-stu-id="eb0ee-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="self-closing-tag-helpers"></a><span data-ttu-id="eb0ee-175">Samoobslužné pomocníky značek</span><span class="sxs-lookup"><span data-stu-id="eb0ee-175">Self-closing Tag Helpers</span></span>

<span data-ttu-id="eb0ee-176">Mnoho pomocníků značek se nedá použít jako samouzavírací značky.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-176">Many Tag Helpers can't be used as self-closing tags.</span></span> <span data-ttu-id="eb0ee-177">Některé pomocníky s značkami jsou navržené tak, aby se samočinně uzavírající značky.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-177">Some Tag Helpers are designed to be self-closing tags.</span></span> <span data-ttu-id="eb0ee-178">Použití pomocníka značek, který nebyl navržen jako samouzavírací, potlačí Vykreslený výstup.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span></span> <span data-ttu-id="eb0ee-179">Samostatný uzavírací pomocník značek má za následek samouzavírací značku ve vykresleném výstupu.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span></span> <span data-ttu-id="eb0ee-180">Další informace najdete v [této poznámce](xref:mvc/views/tag-helpers/authoring#self-closing) při [vytváření pomocníků značek](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="eb0ee-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span></span>

## <a name="c-in-tag-helpers-attributedeclaration"></a><span data-ttu-id="eb0ee-181">C# v atributu nebo deklaraci pomocníka značek</span><span class="sxs-lookup"><span data-stu-id="eb0ee-181">C# in Tag Helpers attribute/declaration</span></span> 

<span data-ttu-id="eb0ee-182">Pomocníka značek nepovoluje C# v oblasti atributu nebo deklarace značky elementu.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-182">Tag Helpers do not allow C# in the element's attribute or tag declaration area.</span></span> <span data-ttu-id="eb0ee-183">Například následující kód není platný:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-183">For example, the following code is not valid:</span></span>

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

<span data-ttu-id="eb0ee-184">Předchozí kód lze zapsat jako:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-184">The preceding code can be written as:</span></span>

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="eb0ee-185">Podpora technologie IntelliSense pro pomocné pomocníky značek</span><span class="sxs-lookup"><span data-stu-id="eb0ee-185">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="eb0ee-186">Když vytvoříte novou ASP.NET Core webovou aplikaci v aplikaci Visual Studio, přidá balíček NuGet "Microsoft. AspNetCore. Razor . Nástroje ".</span><span class="sxs-lookup"><span data-stu-id="eb0ee-186">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.Razor.Tools".</span></span> <span data-ttu-id="eb0ee-187">Toto je balíček, který přidává nástroje pomocníka značek.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-187">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="eb0ee-188">Zvažte zápis elementu HTML `<label>` .</span><span class="sxs-lookup"><span data-stu-id="eb0ee-188">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="eb0ee-189">Jakmile zadáte `<l` v editoru sady Visual Studio, IntelliSense zobrazí vyhovující prvky:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-189">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="eb0ee-191">Nejenom získáte nápovědu HTML, ale ikonu (" @" symbol with "<>" pod ní).</span><span class="sxs-lookup"><span data-stu-id="eb0ee-191">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![image](intro/_static/tagSym.png)

<span data-ttu-id="eb0ee-193">identifikuje element jako cílený pomocníky značek.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-193">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="eb0ee-194">Čistě prvky HTML (například `fieldset` ) zobrazí ikonu "<>".</span><span class="sxs-lookup"><span data-stu-id="eb0ee-194">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="eb0ee-195">Čistě značka HTML `<label>` zobrazuje značku HTML (s výchozím barevným motivem sady Visual Studio) v hnědém písmu, atributy červeně a hodnoty atributu modrou.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-195">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![image](intro/_static/LableHtmlTag.png)

<span data-ttu-id="eb0ee-197">Po zadání `<label` technologie IntelliSense zobrazí seznam dostupných atributů HTML/CSS a atributy cílené na pomocnou značku:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-197">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![image](intro/_static/labelattr.png)

<span data-ttu-id="eb0ee-199">Dokončení příkazu IntelliSense umožňuje zadat klávesu TAB k dokončení příkazu s vybranou hodnotou:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-199">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![image](intro/_static/stmtcomplete.png)

<span data-ttu-id="eb0ee-201">Jakmile je atribut pomocník značek zadán, změní se písma značky a atributu.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-201">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="eb0ee-202">Když použijete výchozí barevný motiv sady Visual Studio "Blue" nebo "Light", písmo je tučně fialové.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-202">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="eb0ee-203">Pokud používáte motiv "tmavý", písmo je tučně šedozelená.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-203">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="eb0ee-204">Obrázky v tomto dokumentu byly pořízeny pomocí výchozího motivu.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-204">The images in this document were taken using the default theme.</span></span>

![image](intro/_static/labelaspfor2.png)

<span data-ttu-id="eb0ee-206">Můžete zadat zástupce sady Visual Studio *CompleteWord* (CTRL + MEZERNÍK je [Výchozí hodnota](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) uvnitř dvojitých uvozovek ("") a teď jste v jazyce c#, stejně jako byste byli ve třídě jazyka c#.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-206">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="eb0ee-207">IntelliSense zobrazí všechny metody a vlastnosti v modelu stránky.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-207">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="eb0ee-208">Metody a vlastnosti jsou k dispozici, protože typ vlastnosti je `ModelExpression` .</span><span class="sxs-lookup"><span data-stu-id="eb0ee-208">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="eb0ee-209">Na obrázku níže upravujeme `Register` zobrazení, takže `RegisterViewModel` je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-209">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![image](intro/_static/intellemail.png)

<span data-ttu-id="eb0ee-211">IntelliSense zobrazí seznam vlastností a metod, které jsou k dispozici pro model na stránce.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-211">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="eb0ee-212">Bohatá prostředí IntelliSense vám pomůže vybrat třídu CSS:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-212">The rich IntelliSense environment helps you select the CSS class:</span></span>

![image](intro/_static/iclass.png)

![image](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="eb0ee-215">Tagování pomocníků v porovnání s pomocníky HTML</span><span class="sxs-lookup"><span data-stu-id="eb0ee-215">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="eb0ee-216">Přihlaste se k prvkům jazyka HTML v Razor zobrazeních, zatímco [pomocníky HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) jsou vyvolány jako metody s HTML v Razor zobrazeních.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-216">Tag Helpers attach to HTML elements in Razor views, while [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in Razor views.</span></span> <span data-ttu-id="eb0ee-217">Vezměte v úvahu následující Razor kód, který vytvoří popisek HTML s nadpisem CSS třídy "titulek":</span><span class="sxs-lookup"><span data-stu-id="eb0ee-217">Consider the following Razor markup, which creates an HTML label with the CSS class "caption":</span></span>

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="eb0ee-218">Symbol on ( `@` ) oznamuje, Razor že se jedná o začátek kódu.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-218">The at (`@`) symbol tells Razor this is the start of code.</span></span> <span data-ttu-id="eb0ee-219">Následující dva parametry ("FirstName" a "first name": ") jsou řetězce, takže [IntelliSense](/visualstudio/ide/using-intellisense) nemůže pomáhat.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-219">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="eb0ee-220">Poslední argument:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-220">The last argument:</span></span>

```cshtml
new {@class="caption"}
```

<span data-ttu-id="eb0ee-221">Je anonymní objekt, který slouží k reprezentaci atributů.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-221">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="eb0ee-222">Protože `class` je klíčové slovo rezervované v jazyce C#, použijete `@` symbol k vynucení interpretace jazyka c# `@class=` jako symbolu (název vlastnosti).</span><span class="sxs-lookup"><span data-stu-id="eb0ee-222">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span></span> <span data-ttu-id="eb0ee-223">Do předkoncového návrháře (někdo známý s HTML/CSS/JavaScriptem a jinými klientskými technologiemi, ale není známý v jazyce C# a Razor ), je většina řádku cizí.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-223">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign.</span></span> <span data-ttu-id="eb0ee-224">Celý řádek musí být vytvořen bez usnadnění z IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-224">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="eb0ee-225">Pomocí `LabelTagHelper` lze napsat stejné označení jako:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-225">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

<span data-ttu-id="eb0ee-226">V případě verze pomocné rutiny značek, jakmile zadáte `<l` v editoru sady Visual Studio, IntelliSense zobrazí vyhovující prvky:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-226">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="eb0ee-228">Technologie IntelliSense vám pomůže napsat celý řádek.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-228">IntelliSense helps you write the entire line.</span></span>

<span data-ttu-id="eb0ee-229">Následující obrázek kódu ukazuje formu zobrazení */účtu/registru. cshtml* Razor vygenerovaného ze šablony ASP.NET 4.5. x MVC, která je součástí sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-229">The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span></span>

![image](intro/_static/regCS.png)

<span data-ttu-id="eb0ee-231">Editor sady Visual Studio zobrazí kód jazyka C# s šedým pozadím.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-231">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="eb0ee-232">Například `AntiForgeryToken` Pomocník HTML:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-232">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```cshtml
@Html.AntiForgeryToken()
```

<span data-ttu-id="eb0ee-233">se zobrazí se šedým pozadím.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-233">is displayed with a grey background.</span></span> <span data-ttu-id="eb0ee-234">Většina značek v zobrazení registrů je C#.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-234">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="eb0ee-235">Porovnejte je s ekvivalentním přístupem pomocí pomocníků značek:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-235">Compare that to the equivalent approach using Tag Helpers:</span></span>

![image](intro/_static/regTH.png)

<span data-ttu-id="eb0ee-237">Značky jsou mnohem čisticí a snadněji se čtou, upravují a udržují než přístup k HTML pomocníkům.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-237">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="eb0ee-238">Kód jazyka C# se zmenší na minimum, o které server potřebuje znát.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-238">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="eb0ee-239">V editoru sady Visual Studio se zobrazí označení cílené pomocí pomocníka značek v rozlišujícím písmu.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-239">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="eb0ee-240">Vezměte v úvahu tuto *e-mailovou* skupinu:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-240">Consider the *Email* group:</span></span>

[!code-cshtml[](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="eb0ee-241">Každý z atributů ASP-má hodnotu "E-mail", ale "E-mail" není řetězec.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-241">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span></span> <span data-ttu-id="eb0ee-242">V tomto kontextu je "e-mailem" vlastnost výrazu modelu C# pro `RegisterViewModel` .</span><span class="sxs-lookup"><span data-stu-id="eb0ee-242">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="eb0ee-243">Editor sady Visual Studio vám pomůže napsat **všechny** značky v pomocné metodě příznaku značky ve formuláři registru, zatímco Visual Studio neposkytuje nápovědu pro většinu kódu v přístupu k HTML pomocníkům.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-243">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="eb0ee-244">[Podpora technologie IntelliSense pro pomocné pomocníky značek](#intellisense-support-for-tag-helpers) se dohlíží na podrobnosti o práci s pomocníky značek v editoru sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-244">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="eb0ee-245">Porovnávání pomocníků značek v porovnání s ovládacími prvky webového serveru</span><span class="sxs-lookup"><span data-stu-id="eb0ee-245">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="eb0ee-246">Pomocníka značek nevlastní prvek, ke kterému jsou přidruženy; jednoduše se účastní vykreslování prvku a obsahu.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-246">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="eb0ee-247">ASP.NET <https://docs.microsoft.com/previous-versions/dotnet/netframework-3.0/7698y1f0(v=vs.85)> jsou deklarovány a vyvolány na stránce.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-247">ASP.NET <https://docs.microsoft.com/previous-versions/dotnet/netframework-3.0/7698y1f0(v=vs.85)> are declared and invoked on a page.</span></span>

* <span data-ttu-id="eb0ee-248"><https://docs.microsoft.com/previous-versions/zsyt68f1(v=vs.140)> mít netriviální životní cyklus, který může provádět vývoj a ladění obtížné.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-248"><https://docs.microsoft.com/previous-versions/zsyt68f1(v=vs.140)> have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="eb0ee-249">Ovládací prvky webového serveru umožňují přidat funkce do prvků model DOM (Document Object Model) klienta (DOM) pomocí ovládacího prvku klienta.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-249">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="eb0ee-250">Pomocník značek nemá žádný model DOM.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-250">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="eb0ee-251">Ovládací prvky webového serveru zahrnují automatické zjišťování prohlížečů.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-251">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="eb0ee-252">Pomocník značek nemá žádné znalosti o prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-252">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="eb0ee-253">Více pomocných rutin značek může působit na stejný prvek (viz téma [zamezení konfliktů pomocníka značek](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ), zatímco obvykle nelze vytvářet ovládací prvky webového serveru.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-253">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="eb0ee-254">Pomocník značek může upravit značku a obsah prvků HTML, na které jsou vymezeny, ale přímo na stránku nemění nic jiného.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-254">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="eb0ee-255">Ovládací prvky webového serveru mají méně konkrétní rozsah a mohou provádět akce, které mají vliv na ostatní části stránky. povolení nezamýšlených vedlejších účinků.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-255">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="eb0ee-256">Ovládací prvky webového serveru používají k převodu řetězců do objektů převaděče typů.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-256">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="eb0ee-257">U pomocníků se značkami pracujete nativně v jazyce C#, takže nemusíte provádět převod typu.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-257">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="eb0ee-258">Ovládací prvky webového serveru používají [System. ComponentModel](/dotnet/api/system.componentmodel) k implementaci chování komponent a ovládacích prvků za běhu a v době návrhu.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-258">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="eb0ee-259">`System.ComponentModel` zahrnuje základní třídy a rozhraní pro implementaci atributů a převaděčů typů, vázání na zdroje dat a součásti licencování.</span><span class="sxs-lookup"><span data-stu-id="eb0ee-259">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="eb0ee-260">Na rozdíl od toho, že chcete označit pomocníky, které jsou obvykle odvozeny z `TagHelper` a `TagHelper` základní třída zpřístupňuje pouze dvě metody `Process` a `ProcessAsync` .</span><span class="sxs-lookup"><span data-stu-id="eb0ee-260">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="eb0ee-261">Přizpůsobení písma prvku pomocné značky</span><span class="sxs-lookup"><span data-stu-id="eb0ee-261">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="eb0ee-262">Písma a barvy můžete přizpůsobit v **nabídce nástroje**  >  **Možnosti**  >  **prostředí**  >  **a barvy**:</span><span class="sxs-lookup"><span data-stu-id="eb0ee-262">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors**:</span></span>

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a><span data-ttu-id="eb0ee-264">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="eb0ee-264">Additional resources</span></span>

* [<span data-ttu-id="eb0ee-265">Vytváření pomocníků se značkami</span><span class="sxs-lookup"><span data-stu-id="eb0ee-265">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="eb0ee-266">Práce s formuláři</span><span class="sxs-lookup"><span data-stu-id="eb0ee-266">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* <span data-ttu-id="eb0ee-267">[TagHelperSamples na GitHubu](https://github.com/dpaquette/TagHelperSamples) obsahuje ukázky pomocníka značek pro práci s [bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="eb0ee-267">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](https://getbootstrap.com/).</span></span>

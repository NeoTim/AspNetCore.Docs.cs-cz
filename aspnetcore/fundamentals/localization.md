---
title: Globalizace a lokalizace v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak ASP.NET Core poskytuje služby a middleware pro lokalizaci obsahu do různých jazyků a kultur.
ms.author: riande
ms.date: 11/30/2019
uid: fundamentals/localization
ms.openlocfilehash: 91db83eb8685c7bee5e976f386c2a12c4090b734
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440815"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="534a6-103">Globalizace a lokalizace v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="534a6-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="534a6-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), Bart [Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), a [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="534a6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="534a6-105">Vytvoření vícejazyčné webové stránky s ASP.NET Core umožní vaše stránky oslovit širší publikum.</span><span class="sxs-lookup"><span data-stu-id="534a6-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="534a6-106">ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.</span><span class="sxs-lookup"><span data-stu-id="534a6-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="534a6-107">Internacionalizace zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="534a6-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="534a6-108">Globalizace je proces navrhování aplikací, které podporují různé jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="534a6-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="534a6-109">Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují k určitým zeměpisným oblastem.</span><span class="sxs-lookup"><span data-stu-id="534a6-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="534a6-110">Lokalizace je proces přizpůsobení globalizované aplikace, kterou jste již zpracovali pro lokalizovatelnost, na konkrétní jazykovou verzi nebo národní prostředí.</span><span class="sxs-lookup"><span data-stu-id="534a6-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="534a6-111">Další informace naleznete v **tématu Globalizace a podmínky lokalizace** na konci tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="534a6-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="534a6-112">Lokalizace aplikace zahrnuje následující:</span><span class="sxs-lookup"><span data-stu-id="534a6-112">App localization involves the following:</span></span>

1. <span data-ttu-id="534a6-113">Nastavení obsahu aplikace jako lokalizovatelného</span><span class="sxs-lookup"><span data-stu-id="534a6-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="534a6-114">Poskytněte lokalizované prostředky pro jazyky a jazykové verze, které podporujete</span><span class="sxs-lookup"><span data-stu-id="534a6-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="534a6-115">Implementace strategie pro výběr jazyka/jazykové verze pro každý požadavek</span><span class="sxs-lookup"><span data-stu-id="534a6-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="534a6-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="534a6-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="534a6-117">Nastavení obsahu aplikace jako lokalizovatelného</span><span class="sxs-lookup"><span data-stu-id="534a6-117">Make the app's content localizable</span></span>

<span data-ttu-id="534a6-118">Představený v ASP.NET `IStringLocalizer` `IStringLocalizer<T>` Core a byl navržen tak, aby zlepšil produktivitu při vývoji lokalizovaných aplikací.</span><span class="sxs-lookup"><span data-stu-id="534a6-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="534a6-119">`IStringLocalizer`Používá [ResourceManager](/dotnet/api/system.resources.resourcemanager) a [ResourceReader](/dotnet/api/system.resources.resourcereader) k poskytování prostředků specifických pro jazykovou verzi za běhu.</span><span class="sxs-lookup"><span data-stu-id="534a6-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="534a6-120">Jednoduché rozhraní má indexer `IEnumerable` a pro vrácení lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="534a6-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="534a6-121">`IStringLocalizer`nevyžaduje uložení výchozích jazykových řetězců do souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="534a6-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="534a6-122">Můžete vyvinout aplikaci určenou pro lokalizaci a není nutné vytvářet soubory prostředků v rané fázi vývoje.</span><span class="sxs-lookup"><span data-stu-id="534a6-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="534a6-123">Níže uvedený kód ukazuje, jak zabalit řetězec "O názvu" pro lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="534a6-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="534a6-124">Ve výše uvedeném `IStringLocalizer<T>` kódu implementace pochází z [vkládání závislostí](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="534a6-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="534a6-125">Pokud není nalezena lokalizovaná hodnota "O titulu", je vrácen klíč indexeru, to znamená řetězec "O titulu".</span><span class="sxs-lookup"><span data-stu-id="534a6-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="534a6-126">Můžete ponechat výchozí jazyk literál řetězce v aplikaci a zabalit je v localizer, takže se můžete soustředit na vývoj aplikace.</span><span class="sxs-lookup"><span data-stu-id="534a6-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="534a6-127">Aplikaci vyvíjíte s výchozím jazykem a připravujete ji na krok lokalizace, aniž byste nejprve vytvořili výchozí soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="534a6-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="534a6-128">Alternativně můžete použít tradiční přístup a poskytnout klíč k načtení výchozího řetězce jazyka.</span><span class="sxs-lookup"><span data-stu-id="534a6-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="534a6-129">Pro mnoho vývojářů nový pracovní postup, který nemá výchozí soubor *Resx* a jednoduše zalomení literály řetězce může snížit režii lokalizace aplikace.</span><span class="sxs-lookup"><span data-stu-id="534a6-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="534a6-130">Ostatní vývojáři budou upřednostňovat tradiční pracovní tok, protože může usnadnit práci s delšími řetězcovými literály a usnadnit aktualizaci lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="534a6-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="534a6-131">Použijte `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují HTML.</span><span class="sxs-lookup"><span data-stu-id="534a6-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="534a6-132">`IHtmlLocalizer`HTML kóduje argumenty, které jsou formátovány v řetězci prostředku, ale nekóduje samotný řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="534a6-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="534a6-133">V níže zvýrazněné ukázce `name` je kódována pouze hodnota parametru HTML.</span><span class="sxs-lookup"><span data-stu-id="534a6-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="534a6-134">**Poznámka:** Obecně chcete pouze lokalizovat text a ne HTML.</span><span class="sxs-lookup"><span data-stu-id="534a6-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="534a6-135">Na nejnižší úrovni se `IStringLocalizerFactory` můžete dostat z [vkládání závislostí](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="534a6-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="534a6-136">Výše uvedený kód ukazuje každou ze dvou metod vytvoření z výroby.</span><span class="sxs-lookup"><span data-stu-id="534a6-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="534a6-137">Lokalizované řetězce můžete rozdělit podle řadiče, oblasti nebo mít pouze jeden kontejner.</span><span class="sxs-lookup"><span data-stu-id="534a6-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="534a6-138">V ukázkové aplikaci se `SharedResource` pro sdílené prostředky používá fiktivní třída s názvem.</span><span class="sxs-lookup"><span data-stu-id="534a6-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="534a6-139">Někteří vývojáři `Startup` používají třídu obsahovat globální nebo sdílené řetězce.</span><span class="sxs-lookup"><span data-stu-id="534a6-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="534a6-140">V níže uvedeném `InfoController` vzorku `SharedResource` se používají lokalizátory a lokalizátory:</span><span class="sxs-lookup"><span data-stu-id="534a6-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="534a6-141">Zobrazit lokalizaci</span><span class="sxs-lookup"><span data-stu-id="534a6-141">View localization</span></span>

<span data-ttu-id="534a6-142">Služba `IViewLocalizer` poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="534a6-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="534a6-143">Třída `ViewLocalizer` implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="534a6-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="534a6-144">Následující kód ukazuje, jak používat `IViewLocalizer`výchozí implementaci aplikace :</span><span class="sxs-lookup"><span data-stu-id="534a6-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="534a6-145">Výchozí implementace `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="534a6-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="534a6-146">Neexistuje žádná možnost použít globální soubor sdílených prostředků.</span><span class="sxs-lookup"><span data-stu-id="534a6-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="534a6-147">`ViewLocalizer`implementuje lokalizátor pomocí `IHtmlLocalizer`, takže Razor není HTML kódovat lokalizovaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="534a6-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="534a6-148">Můžete parametrizovat řetězce prostředků `IViewLocalizer` a html kódovat parametry, ale ne řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="534a6-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="534a6-149">Zvažte následující značky Razor:</span><span class="sxs-lookup"><span data-stu-id="534a6-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="534a6-150">Francouzský soubor prostředků může obsahovat následující:</span><span class="sxs-lookup"><span data-stu-id="534a6-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="534a6-151">Klíč</span><span class="sxs-lookup"><span data-stu-id="534a6-151">Key</span></span> | <span data-ttu-id="534a6-152">Hodnota</span><span class="sxs-lookup"><span data-stu-id="534a6-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="534a6-153">Vykreslené zobrazení by obsahovalo značky HTML ze souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="534a6-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="534a6-154">**Poznámka:** Obecně chcete pouze lokalizovat text a ne HTML.</span><span class="sxs-lookup"><span data-stu-id="534a6-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="534a6-155">Chcete-li použít sdílený soubor prostředků `IHtmlLocalizer<T>`v zobrazení, vložte :</span><span class="sxs-lookup"><span data-stu-id="534a6-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="534a6-156">Lokalizace datových anotací</span><span class="sxs-lookup"><span data-stu-id="534a6-156">DataAnnotations localization</span></span>

<span data-ttu-id="534a6-157">DataAnnotations chybové zprávy `IStringLocalizer<T>`jsou lokalizovány s .</span><span class="sxs-lookup"><span data-stu-id="534a6-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="534a6-158">Pomocí této `ResourcesPath = "Resources"`možnosti mohou `RegisterViewModel` být chybové zprávy v aplikaci uloženy v jedné z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="534a6-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="534a6-159">*Zdroje/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="534a6-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="534a6-160">*Zdroje/ViewModels/Účet/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="534a6-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="534a6-161">V ASP.NET core MVC 1.1.0 a vyšší, jsou lokalizovány atributy bez ověření.</span><span class="sxs-lookup"><span data-stu-id="534a6-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="534a6-162">ASP.NET Core MVC 1.0 **nevyhledává** lokalizované řetězce pro atributy bez ověření.</span><span class="sxs-lookup"><span data-stu-id="534a6-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="534a6-163">Použití jednoho řetězce prostředků pro více tříd</span><span class="sxs-lookup"><span data-stu-id="534a6-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="534a6-164">Následující kód ukazuje, jak použít jeden řetězec prostředků pro atributy ověření s více třídami:</span><span class="sxs-lookup"><span data-stu-id="534a6-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="534a6-165">V předchozím kódu `SharedResource` je třída odpovídající resx, kde jsou uloženy ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="534a6-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="534a6-166">S tímto přístupem DataAnnotations `SharedResource`bude používat pouze , nikoli prostředek pro každou třídu.</span><span class="sxs-lookup"><span data-stu-id="534a6-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="534a6-167">Poskytněte lokalizované prostředky pro jazyky a jazykové verze, které podporujete</span><span class="sxs-lookup"><span data-stu-id="534a6-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="534a6-168">Podporované kultury a podporované uikultury</span><span class="sxs-lookup"><span data-stu-id="534a6-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="534a6-169">ASP.NET Core umožňuje zadat dvě `SupportedCultures` hodnoty `SupportedUICultures`jazykové verze a .</span><span class="sxs-lookup"><span data-stu-id="534a6-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="534a6-170">[Objekt CultureInfo](/dotnet/api/system.globalization.cultureinfo) `SupportedCultures` pro určuje výsledky funkcí závislých na jazykové verzi, jako je například datum, čas, číslo a formátování měny.</span><span class="sxs-lookup"><span data-stu-id="534a6-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="534a6-171">`SupportedCultures`také určuje pořadí řazení textu, konvencí písmen a porovnávání řetězců.</span><span class="sxs-lookup"><span data-stu-id="534a6-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="534a6-172">Viz [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) další informace o tom, jak server získá jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="534a6-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="534a6-173">Určuje, `SupportedUICultures` které přeložené řetězce (ze souborů *.resx)* jsou vyhledány [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="534a6-173">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="534a6-174">Jednoduše `ResourceManager` vyhledá řetězce specifické pro jazykovou verzi, které jsou určeny . `CurrentUICulture`</span><span class="sxs-lookup"><span data-stu-id="534a6-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="534a6-175">Každé vlákno v `CurrentCulture` rozhraní `CurrentUICulture` .NET má a objekty.</span><span class="sxs-lookup"><span data-stu-id="534a6-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="534a6-176">ASP.NET Core kontroluje tyto hodnoty při vykreslování funkcí závislých na jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="534a6-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="534a6-177">Například pokud je jazyková verze aktuálního vlákna nastavena na "en US" (angličtina, Spojené státy), `DateTime.Now.ToLongDateString()` zobrazí `CurrentCulture` se "Čtvrtek, únor 18, 2016", ale pokud je nastavena na "es-ES" (španělština, Španělsko), výstup bude "jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="534a6-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="534a6-178">Soubory prostředků</span><span class="sxs-lookup"><span data-stu-id="534a6-178">Resource files</span></span>

<span data-ttu-id="534a6-179">Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců od kódu.</span><span class="sxs-lookup"><span data-stu-id="534a6-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="534a6-180">Přeložené řetězce pro nevýchozí jazyk jsou izolovány v souborech prostředků *.resx.*</span><span class="sxs-lookup"><span data-stu-id="534a6-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="534a6-181">Můžete například vytvořit španělský soubor prostředků s názvem *Welcome.es.resx* obsahující přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="534a6-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="534a6-182">"es" je kód jazyka pro španělštinu.</span><span class="sxs-lookup"><span data-stu-id="534a6-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="534a6-183">Vytvoření tohoto souboru prostředků v sadě Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="534a6-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="534a6-184">V **Průzkumníku řešení**klikněte pravým tlačítkem myši na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="534a6-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Vnořená kontextová nabídka: V Průzkumníku řešení je pro prostředky otevřena kontextová nabídka.](localization/_static/newi.png)

2. <span data-ttu-id="534a6-187">Do pole **Hledat nainstalované šablony** zadejte "zdroj" a pojmenujte soubor.</span><span class="sxs-lookup"><span data-stu-id="534a6-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Dialogové okno Přidat novou položku](localization/_static/res.png)

3. <span data-ttu-id="534a6-189">Zadejte hodnotu klíče (nativní řetězec) do sloupce **Název** a přeložený řetězec ve sloupci **Hodnota.**</span><span class="sxs-lookup"><span data-stu-id="534a6-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx soubor (uvítací soubor prostředků pro španělštinu) se slovem Dobrý den ve sloupci Název a slovo Hola (Hello ve španělštině) ve sloupci Hodnota](localization/_static/hola.png)

    <span data-ttu-id="534a6-191">Visual Studio zobrazuje soubor *Welcome.es.resx.*</span><span class="sxs-lookup"><span data-stu-id="534a6-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Průzkumník řešení zobrazující uvítací španělský (es) soubor prostředků](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="534a6-193">Pojmenování souboru prostředků</span><span class="sxs-lookup"><span data-stu-id="534a6-193">Resource file naming</span></span>

<span data-ttu-id="534a6-194">Prostředky jsou pojmenovány pro úplný název typu své třídy mínus název sestavení.</span><span class="sxs-lookup"><span data-stu-id="534a6-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="534a6-195">Například francouzský zdroj v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu, `LocalizationWebsite.Web.Startup` by se jmenoval *Startup.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="534a6-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="534a6-196">Prostředek pro třídu `LocalizationWebsite.Web.Controllers.HomeController` by se jmenoval *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="534a6-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="534a6-197">Pokud obor názvů cílové třídy není stejný jako název sestavení, budete potřebovat úplný název typu.</span><span class="sxs-lookup"><span data-stu-id="534a6-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="534a6-198">Například v ukázkovém projektu by `ExtraNamespace.Tools` zdroj pro daný typ měl název *ExtraNamespace.Tools.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="534a6-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="534a6-199">V ukázkovém `ConfigureServices` projektu metoda `ResourcesPath` nastaví na "Zdroje", takže relativní cesta projektu pro soubor prostředků domácího řadiče je *Resources/Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="534a6-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="534a6-200">Případně můžete použít složky k uspořádání souborů prostředků.</span><span class="sxs-lookup"><span data-stu-id="534a6-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="534a6-201">Pro domácí řadič by cesta byla *Resources/Controllers/HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="534a6-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="534a6-202">Pokud `ResourcesPath` tuto možnost nepoužijete, soubor *Resx* by se přešel do základního adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="534a6-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="534a6-203">Soubor prostředků `HomeController` pro by se jmenoval *Controllers.HomeController.fr.resx*.</span><span class="sxs-lookup"><span data-stu-id="534a6-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="534a6-204">Volba použití tečky nebo konvence pojmenování cesty závisí na tom, jak chcete uspořádat soubory prostředků.</span><span class="sxs-lookup"><span data-stu-id="534a6-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="534a6-205">Název prostředku</span><span class="sxs-lookup"><span data-stu-id="534a6-205">Resource name</span></span> | <span data-ttu-id="534a6-206">Pojmenování tečky nebo cesty</span><span class="sxs-lookup"><span data-stu-id="534a6-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="534a6-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="534a6-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="534a6-208">Dot</span><span class="sxs-lookup"><span data-stu-id="534a6-208">Dot</span></span>  |
| <span data-ttu-id="534a6-209">Zdroje/kontroléry/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="534a6-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="534a6-210">Cesta</span><span class="sxs-lookup"><span data-stu-id="534a6-210">Path</span></span> |
|    |     |

<span data-ttu-id="534a6-211">Soubory prostředků `@inject IViewLocalizer` používající v zobrazení razor postupujte podle podobného vzoru.</span><span class="sxs-lookup"><span data-stu-id="534a6-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="534a6-212">Soubor prostředků pro zobrazení lze pojmenovat pomocí pojmenování tečky nebo pojmenování cesty.</span><span class="sxs-lookup"><span data-stu-id="534a6-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="534a6-213">Soubory prostředků zobrazení holicí strojek napodobují cestu k jejich přidruženému souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="534a6-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="534a6-214">Za předpokladu, `ResourcesPath` že nastavíme na "Zdroje", může být francouzský soubor prostředků přidružený *k zobrazení Zobrazení/Domů/About.cshtml* některou z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="534a6-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="534a6-215">Zdroje/Zobrazení/Domů/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="534a6-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="534a6-216">Zdroje/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="534a6-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="534a6-217">Pokud `ResourcesPath` tuto možnost nepoužijete, soubor *Resx* pro zobrazení bude umístěn ve stejné složce jako zobrazení.</span><span class="sxs-lookup"><span data-stu-id="534a6-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="534a6-218">Atribut rootnamespace</span><span class="sxs-lookup"><span data-stu-id="534a6-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="534a6-219">Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení.</span><span class="sxs-lookup"><span data-stu-id="534a6-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="534a6-220">Tato situace může nastat, pokud název projektu není platný identifikátor .NET.</span><span class="sxs-lookup"><span data-stu-id="534a6-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="534a6-221">Například `my-project-name.csproj` bude používat kořenový obor `my_project_name` `my-project-name` názvů a název sestavení vedoucí k této chybě.</span><span class="sxs-lookup"><span data-stu-id="534a6-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="534a6-222">Pokud kořenový obor názvů sestavení se liší od názvu sestavení:</span><span class="sxs-lookup"><span data-stu-id="534a6-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="534a6-223">Lokalizace nefunguje ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="534a6-223">Localization does not work by default.</span></span>
* <span data-ttu-id="534a6-224">Lokalizace se nezdaří z důvodu způsobu, jakým jsou vyhledávány prostředky v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="534a6-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="534a6-225">`RootNamespace`je hodnota v době sestavení, která není k dispozici pro vykonávající proces.</span><span class="sxs-lookup"><span data-stu-id="534a6-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="534a6-226">Pokud `RootNamespace` se liší `AssemblyName`od , uveďte do *AssemblyInfo.cs* (s hodnotami parametrů nahrazenými skutečnými hodnotami):</span><span class="sxs-lookup"><span data-stu-id="534a6-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="534a6-227">Předchozí kód umožňuje úspěšné rozlišení souborů resx.</span><span class="sxs-lookup"><span data-stu-id="534a6-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="534a6-228">Záložní chování jazykové verze</span><span class="sxs-lookup"><span data-stu-id="534a6-228">Culture fallback behavior</span></span>

<span data-ttu-id="534a6-229">Při hledání prostředku se lokalizace zabývá "záložní kulturou".</span><span class="sxs-lookup"><span data-stu-id="534a6-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="534a6-230">Počínaje požadovanou jazykovou verzi, pokud nebyl nalezen, vrátí se do nadřazené jazykové verze této jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="534a6-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="534a6-231">Jako stranou [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) vlastnost představuje nadřazenou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="534a6-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="534a6-232">To obvykle (ale ne vždy) znamená odstranění národního signifier z ISO.</span><span class="sxs-lookup"><span data-stu-id="534a6-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="534a6-233">Například dialekt španělštiny mluvený v Mexiku je "es-MX".</span><span class="sxs-lookup"><span data-stu-id="534a6-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="534a6-234">Má mateřské "es"&mdash;španělština non-specifické pro všechny země.</span><span class="sxs-lookup"><span data-stu-id="534a6-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="534a6-235">Představte si, že váš web obdrží žádost o "Uvítací" prostředek pomocí jazykové verze "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="534a6-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="534a6-236">Lokalizační systém vyhledá následující prostředky v pořadí a vybere první shodu:</span><span class="sxs-lookup"><span data-stu-id="534a6-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="534a6-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="534a6-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="534a6-238">*Vítejte.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="534a6-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="534a6-239">*Welcome.resx* (pokud `NeutralResourcesLanguage` je "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="534a6-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="534a6-240">Jako příklad pokud odeberete označení jazykové verze ".fr" a máte jazykovou verzi nastavenou na francouzštinu, výchozí soubor prostředků se čte a řetězce jsou lokalizovány.</span><span class="sxs-lookup"><span data-stu-id="534a6-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="534a6-241">Správce prostředků určuje výchozí nebo záložní prostředek, pokud se nic nesetká s požadovanou jazykovou verzí.</span><span class="sxs-lookup"><span data-stu-id="534a6-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="534a6-242">Pokud chcete pouze vrátit klíč při chybě prostředku pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="534a6-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="534a6-243">Generování souborů prostředků pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="534a6-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="534a6-244">Pokud vytvoříte soubor prostředků v sadě Visual Studio bez jazykové verze v názvu souboru (například *Welcome.resx*), Visual Studio vytvoří třídu C# s vlastností pro každý řetězec.</span><span class="sxs-lookup"><span data-stu-id="534a6-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="534a6-245">To obvykle není to, co chcete s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="534a6-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="534a6-246">Obvykle nemáte výchozí soubor prostředků *Resx* (soubor *Resx* bez názvu jazykové verze).</span><span class="sxs-lookup"><span data-stu-id="534a6-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="534a6-247">Doporučujeme vytvořit soubor *Resx* s názvem jazykové verze (například *Welcome.fr.resx*).</span><span class="sxs-lookup"><span data-stu-id="534a6-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="534a6-248">Když vytvoříte soubor *Resx* s názvem jazykové verze, Visual Studio nebude generovat soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="534a6-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="534a6-249">Přidání dalších kultur</span><span class="sxs-lookup"><span data-stu-id="534a6-249">Add other cultures</span></span>

<span data-ttu-id="534a6-250">Každá kombinace jazyka a jazykové verze (jiná než výchozí jazyk) vyžaduje jedinečný soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="534a6-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="534a6-251">Soubory prostředků pro různé jazykové verze a národní prostředí vytvoříte vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-us**, **fr-ca**a **en-gb**).</span><span class="sxs-lookup"><span data-stu-id="534a6-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="534a6-252">Tyto kódy ISO jsou umístěny mezi názvem souboru a příponou *.resx,* jako v *Welcome.es-MX.resx* (španělština / Mexiko).</span><span class="sxs-lookup"><span data-stu-id="534a6-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="534a6-253">Implementace strategie pro výběr jazyka/jazykové verze pro každý požadavek</span><span class="sxs-lookup"><span data-stu-id="534a6-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="534a6-254">Konfigurace lokalizace</span><span class="sxs-lookup"><span data-stu-id="534a6-254">Configure localization</span></span>

<span data-ttu-id="534a6-255">Lokalizace je `Startup.ConfigureServices` konfigurována v metodě:</span><span class="sxs-lookup"><span data-stu-id="534a6-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="534a6-256">`AddLocalization`Přidá lokalizační služby do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="534a6-256">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="534a6-257">Výše uvedený kód také nastaví cestu prostředků na "Zdroje".</span><span class="sxs-lookup"><span data-stu-id="534a6-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="534a6-258">`AddViewLocalization`Přidá podporu pro lokalizované soubory zobrazení.</span><span class="sxs-lookup"><span data-stu-id="534a6-258">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="534a6-259">V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="534a6-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="534a6-260">Například "fr" v souboru *Index.fr.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="534a6-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="534a6-261">`AddDataAnnotationsLocalization`Přidává podporu pro `DataAnnotations` lokalizované ověřovací zprávy prostřednictvím `IStringLocalizer` abstrakcí.</span><span class="sxs-lookup"><span data-stu-id="534a6-261">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="534a6-262">Middleware lokalizace</span><span class="sxs-lookup"><span data-stu-id="534a6-262">Localization middleware</span></span>

<span data-ttu-id="534a6-263">Aktuální jazyková verze na požadavek je nastavena v [middlewaru](xref:fundamentals/middleware/index)lokalizace .</span><span class="sxs-lookup"><span data-stu-id="534a6-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="534a6-264">Middleware lokalizace je `Startup.Configure` povolena v metodě.</span><span class="sxs-lookup"><span data-stu-id="534a6-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="534a6-265">Middleware lokalizace musí být nakonfigurován před jakýmkoli middlewarem, `app.UseMvcWithDefaultRoute()`který může zkontrolovat jazykovou verzi požadavku (například ).</span><span class="sxs-lookup"><span data-stu-id="534a6-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="534a6-266">`UseRequestLocalization`inicializuje `RequestLocalizationOptions` objekt.</span><span class="sxs-lookup"><span data-stu-id="534a6-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="534a6-267">Na každém požadavku `RequestCultureProvider` `RequestLocalizationOptions` je uveden seznam v je uveden a první zprostředkovatel, který lze úspěšně určit jazykovou verzi požadavku se používá.</span><span class="sxs-lookup"><span data-stu-id="534a6-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="534a6-268">Výchozí zprostředkovatelé pocházejí `RequestLocalizationOptions` z třídy:</span><span class="sxs-lookup"><span data-stu-id="534a6-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="534a6-269">Výchozí seznam přejde z nejkonkrétnějších na nejméně konkrétní.</span><span class="sxs-lookup"><span data-stu-id="534a6-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="534a6-270">Dále v článku uvidíme, jak můžete změnit pořadí a dokonce přidat vlastní poskytovatele jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="534a6-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="534a6-271">Pokud žádný z zprostředkovatelů může určit `DefaultRequestCulture` jazykovou verzi požadavku, použije se.</span><span class="sxs-lookup"><span data-stu-id="534a6-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="534a6-272">Zprostředkovatel QueryStringRequestCulture</span><span class="sxs-lookup"><span data-stu-id="534a6-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="534a6-273">Některé aplikace budou používat řetězec dotazu k nastavení [jazykové verze a jazykové verze uznané .](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)</span><span class="sxs-lookup"><span data-stu-id="534a6-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="534a6-274">Pro aplikace, které používají přístup záhlaví cookie nebo Přijmout jazyk, je přidání řetězce dotazu do adresy URL užitečné pro ladění a testování kódu.</span><span class="sxs-lookup"><span data-stu-id="534a6-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="534a6-275">Ve výchozím `QueryStringRequestCultureProvider` nastavení je registrován jako první `RequestCultureProvider` zprostředkovatel lokalizace v seznamu.</span><span class="sxs-lookup"><span data-stu-id="534a6-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="534a6-276">Předáte parametry `culture` řetězce `ui-culture`dotazu a .</span><span class="sxs-lookup"><span data-stu-id="534a6-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="534a6-277">Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělštinu/Mexiko:</span><span class="sxs-lookup"><span data-stu-id="534a6-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="534a6-278">Pokud předáte pouze v jednom`culture` `ui-culture`ze dvou ( nebo ), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí jedné, kterou jste předali.</span><span class="sxs-lookup"><span data-stu-id="534a6-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="534a6-279">Například nastavení pouze jazykové verze `Culture` nastaví `UICulture`a :</span><span class="sxs-lookup"><span data-stu-id="534a6-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="534a6-280">Zprostředkovatel cookieRequestCulture</span><span class="sxs-lookup"><span data-stu-id="534a6-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="534a6-281">Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze pomocí souboru cookie jazykové verze ASP.NET jádra.</span><span class="sxs-lookup"><span data-stu-id="534a6-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="534a6-282">Pomocí `MakeCookieValue` metody vytvořte soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="534a6-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="534a6-283">Vrátí `CookieRequestCultureProvider` `DefaultCookieName` výchozí název souboru cookie používaný ke sledování informací o jazykové verzi upřednostňované uživatele.</span><span class="sxs-lookup"><span data-stu-id="534a6-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="534a6-284">Výchozí název souboru cookie je `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="534a6-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="534a6-285">Formát souboru `c=%LANGCODE%|uic=%LANGCODE%`cookie `c` `Culture` je `uic` `UICulture`, kde je a je , například:</span><span class="sxs-lookup"><span data-stu-id="534a6-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="534a6-286">Pokud zadáte pouze jeden z informací o jazykové verzi a jazykové verze uj.</span><span class="sxs-lookup"><span data-stu-id="534a6-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="534a6-287">Hlavička HTTP v jazyce přijetí jazyka</span><span class="sxs-lookup"><span data-stu-id="534a6-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="534a6-288">Hlavička [Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) je ve většině prohlížečů nastavena a původně byla určena k určení jazyka uživatele.</span><span class="sxs-lookup"><span data-stu-id="534a6-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="534a6-289">Toto nastavení označuje, co byl prohlížeč nastaven na odeslání nebo zděděné z podkladového operačního systému.</span><span class="sxs-lookup"><span data-stu-id="534a6-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="534a6-290">Hlavička HTTP v jazyce přijetí z požadavku prohlížeče není neomylným způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči).](https://www.w3.org/International/questions/qa-lang-priorities.en.php)</span><span class="sxs-lookup"><span data-stu-id="534a6-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="534a6-291">Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="534a6-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="534a6-292">Nastavení hlavičky HTTP v jazyce HTTP v jazyce IE</span><span class="sxs-lookup"><span data-stu-id="534a6-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="534a6-293">Na ikoně ozubeného kola klepněte na **Možnosti Internetu**.</span><span class="sxs-lookup"><span data-stu-id="534a6-293">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="534a6-294">Klepněte na **Jazyky**.</span><span class="sxs-lookup"><span data-stu-id="534a6-294">Tap **Languages**.</span></span>

    ![Možnosti Internetu](localization/_static/lang.png)

3. <span data-ttu-id="534a6-296">Klepněte na **Nastavit jazykové předvolby**.</span><span class="sxs-lookup"><span data-stu-id="534a6-296">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="534a6-297">Klepněte **na Přidat jazyk**.</span><span class="sxs-lookup"><span data-stu-id="534a6-297">Tap **Add a language**.</span></span>

5. <span data-ttu-id="534a6-298">Přidejte jazyk.</span><span class="sxs-lookup"><span data-stu-id="534a6-298">Add the language.</span></span>

6. <span data-ttu-id="534a6-299">Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.</span><span class="sxs-lookup"><span data-stu-id="534a6-299">Tap the language, then tap **Move Up**.</span></span>

::: moniker range="> aspnetcore-3.1"
### <a name="the-content-language-http-header"></a><span data-ttu-id="534a6-300">Hlavička HTTP v jazyce Content-Language</span><span class="sxs-lookup"><span data-stu-id="534a6-300">The Content-Language HTTP header</span></span>

<span data-ttu-id="534a6-301">Záhlaví entity [Obsah-Jazyk:](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language)</span><span class="sxs-lookup"><span data-stu-id="534a6-301">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="534a6-302">Používá se k popisu jazyka (jazyků) určených pro publikum.</span><span class="sxs-lookup"><span data-stu-id="534a6-302">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="534a6-303">Umožňuje uživateli rozlišovat podle vlastního preferovaného jazyka uživatele.</span><span class="sxs-lookup"><span data-stu-id="534a6-303">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="534a6-304">Hlavičky entit se používají v požadavcích HTTP i odpovědích.</span><span class="sxs-lookup"><span data-stu-id="534a6-304">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="534a6-305">Hlavičku `Content-Language` lze přidat nastavením `ApplyCurrentCultureToResponseHeaders`vlastnosti .</span><span class="sxs-lookup"><span data-stu-id="534a6-305">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="534a6-306">Přidání `Content-Language` záhlaví:</span><span class="sxs-lookup"><span data-stu-id="534a6-306">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="534a6-307">Umožňuje RequestLocalizationMiddleware nastavit `Content-Language` záhlaví s `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="534a6-307">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="534a6-308">Eliminuje nutnost explicitně nastavit `Content-Language` hlavičku odpovědi.</span><span class="sxs-lookup"><span data-stu-id="534a6-308">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="534a6-309">Použití vlastního zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="534a6-309">Use a custom provider</span></span>

<span data-ttu-id="534a6-310">Předpokládejme, že chcete, aby vaši zákazníci ukládat své jazyky a jazykové verze ve vašich databázích.</span><span class="sxs-lookup"><span data-stu-id="534a6-310">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="534a6-311">Můžete napsat zprostředkovatele vyhledat tyto hodnoty pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="534a6-311">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="534a6-312">Následující kód ukazuje, jak přidat vlastního zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="534a6-312">The following code shows how to add a custom provider:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```
::: moniker-end

<span data-ttu-id="534a6-313">Slouží `RequestLocalizationOptions` k přidání nebo odebrání zprostředkovatelů lokalizace.</span><span class="sxs-lookup"><span data-stu-id="534a6-313">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="534a6-314">Programové nastavení kultury</span><span class="sxs-lookup"><span data-stu-id="534a6-314">Set the culture programmatically</span></span>

<span data-ttu-id="534a6-315">Tato ukázka **Localization.StarterWeb** projektu na [GitHub](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture`.</span><span class="sxs-lookup"><span data-stu-id="534a6-315">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="534a6-316">*Soubor Zobrazení/Sdílené/_SelectLanguagePartial.cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných kultur:</span><span class="sxs-lookup"><span data-stu-id="534a6-316">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="534a6-317">Soubor *Zobrazení/Sdílený/_SelectLanguagePartial.cshtml* je přidán `footer` do části souboru rozložení, takže bude k dispozici všem zobrazením:</span><span class="sxs-lookup"><span data-stu-id="534a6-317">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="534a6-318">Metoda `SetLanguage` nastaví soubor cookie jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="534a6-318">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="534a6-319">Nelze připojit *_SelectLanguagePartial.cshtml* ukázkový kód pro tento projekt.</span><span class="sxs-lookup"><span data-stu-id="534a6-319">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="534a6-320">Projekt **Localization.StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) má `RequestLocalizationOptions` kód pro tok razor částečné prostřednictvím kontejneru [vkládání závislostí.](dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="534a6-320">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="534a6-321">Data vazby modelu a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="534a6-321">Model binding route data and query strings</span></span>

<span data-ttu-id="534a6-322">Viz [Chování globalizace dat trasy vazby modelu a řetězců dotazu](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="534a6-322">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="534a6-323">Podmínky globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="534a6-323">Globalization and localization terms</span></span>

<span data-ttu-id="534a6-324">Proces lokalizace aplikace také vyžaduje základní znalosti relevantních znakových sad běžně používaných při vývoji moderního softwaru a pochopení problémů s nimi spojených.</span><span class="sxs-lookup"><span data-stu-id="534a6-324">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="534a6-325">Přestože všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text pomocí různých čísel.</span><span class="sxs-lookup"><span data-stu-id="534a6-325">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="534a6-326">Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.</span><span class="sxs-lookup"><span data-stu-id="534a6-326">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="534a6-327">[Lokalizovatelnost](/dotnet/standard/globalization-localization/localizability-review) je zprostředkující proces pro ověření, že globalizovaná aplikace je připravena k lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="534a6-327">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="534a6-328">Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název `<languagecode2>-<country/regioncode2>`jazykové `<languagecode2>` verze je `<country/regioncode2>` , kde je kód jazyka a je kód subkultury.</span><span class="sxs-lookup"><span data-stu-id="534a6-328">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="534a6-329">Například `es-CL` pro španělštinu `en-US` (Chile), pro angličtinu (Spojené státy) a `en-AU` pro angličtinu (Austrálie).</span><span class="sxs-lookup"><span data-stu-id="534a6-329">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="534a6-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinace dvoupísmenného kódu jazykové verze ISO 639 s malou písmena přidruženou k jazyku a dvoupísmenného kódu subkultury ISO 3166 s velkými písmeny přidruženými k zemi nebo oblasti.</span><span class="sxs-lookup"><span data-stu-id="534a6-330">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="534a6-331">Viz [Název jazykové verze](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="534a6-331">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="534a6-332">Internacionalizace je často zkrácena na "I18N".</span><span class="sxs-lookup"><span data-stu-id="534a6-332">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="534a6-333">Zkratka má první a poslední písmeno a počet písmen mezi nimi, takže 18 znamená počet písmen mezi prvním "I" a posledním "N".</span><span class="sxs-lookup"><span data-stu-id="534a6-333">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="534a6-334">Totéž platí pro globalizaci (G11N) a lokalizaci (L10N).</span><span class="sxs-lookup"><span data-stu-id="534a6-334">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="534a6-335">Podmínky:</span><span class="sxs-lookup"><span data-stu-id="534a6-335">Terms:</span></span>

* <span data-ttu-id="534a6-336">Globalizace (G11N): Proces vytváření aplikace podporuje různé jazyky a oblasti.</span><span class="sxs-lookup"><span data-stu-id="534a6-336">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="534a6-337">Lokalizace (L10N): Proces přizpůsobení aplikace pro daný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="534a6-337">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="534a6-338">Internacionalizace (I18N): Popisuje globalizaci i lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="534a6-338">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="534a6-339">Kultura: Je to jazyk a volitelně region.</span><span class="sxs-lookup"><span data-stu-id="534a6-339">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="534a6-340">Neutrální jazyková verze: Jazyková verze, která má zadaný jazyk, ale ne oblast.</span><span class="sxs-lookup"><span data-stu-id="534a6-340">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="534a6-341">(například "en", "es")</span><span class="sxs-lookup"><span data-stu-id="534a6-341">(for example "en", "es")</span></span>
* <span data-ttu-id="534a6-342">Konkrétní jazyková verze: Jazyková verze, která má zadaný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="534a6-342">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="534a6-343">(například "en US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="534a6-343">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="534a6-344">Nadřazená jazyková verze: Neutrální jazyková verze, která obsahuje konkrétní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="534a6-344">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="534a6-345">(například "en" je nadřazená jazyková verze "en US" a "en-GB")</span><span class="sxs-lookup"><span data-stu-id="534a6-345">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="534a6-346">Národní prostředí: Národní prostředí je stejné jako jazyková verze.</span><span class="sxs-lookup"><span data-stu-id="534a6-346">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="534a6-347">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="534a6-347">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="534a6-348">[Localization.StarterWeb projekt](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) použitý v článku.</span><span class="sxs-lookup"><span data-stu-id="534a6-348">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="534a6-349">Globalizace a lokalizace aplikací .NET</span><span class="sxs-lookup"><span data-stu-id="534a6-349">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="534a6-350">Zdroje v souborech Resx</span><span class="sxs-lookup"><span data-stu-id="534a6-350">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="534a6-351">Sada nástrojů aplikace Microsoft pro vícejazyčných aplikací</span><span class="sxs-lookup"><span data-stu-id="534a6-351">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="534a6-352">Lokalizace & obecných typů</span><span class="sxs-lookup"><span data-stu-id="534a6-352">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

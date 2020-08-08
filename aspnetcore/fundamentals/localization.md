---
title: Globalizace a lokalizace v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core poskytuje služby a middleware pro lokalizaci obsahu do různých jazyků a kultur.
ms.author: riande
ms.date: 11/30/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization
ms.openlocfilehash: 9fd68d3b412c2cef6125c657653f605689ca6e70
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017217"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="54cbe-103">Globalizace a lokalizace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54cbe-103">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="54cbe-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)a [Hisham bin](https://twitter.com/hishambinateya) Ateya</span><span class="sxs-lookup"><span data-stu-id="54cbe-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="54cbe-105">Vícejazyčný web umožňuje webu oslovit širší cílovou skupinu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-105">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="54cbe-106">ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.</span><span class="sxs-lookup"><span data-stu-id="54cbe-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="54cbe-107">Mezinárodní využití zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="54cbe-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="54cbe-108">Globalizace je proces návrhu aplikací, které podporují různé jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="54cbe-109">Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují na konkrétní geografické oblasti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="54cbe-110">Lokalizace je proces přizpůsobení globální aplikace, kterou jste již zpracovali pro lokalizaci, do konkrétní jazykové verze nebo národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="54cbe-111">Další informace najdete v tématu **předpoklady globalizace a lokalizace** poblíž konce tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="54cbe-112">Lokalizace aplikace zahrnuje následující:</span><span class="sxs-lookup"><span data-stu-id="54cbe-112">App localization involves the following:</span></span>

1. <span data-ttu-id="54cbe-113">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="54cbe-113">Make the app's content localizable</span></span>
1. <span data-ttu-id="54cbe-114">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="54cbe-114">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="54cbe-115">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="54cbe-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="54cbe-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54cbe-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="54cbe-117">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="54cbe-117">Make the app's content localizable</span></span>

<span data-ttu-id="54cbe-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer>a <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> bylo navrženo pro zlepšení produktivity při vývoji lokalizovaných aplikací.</span><span class="sxs-lookup"><span data-stu-id="54cbe-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="54cbe-119">`IStringLocalizer`používá <xref:System.Resources.ResourceManager> a <xref:System.Resources.ResourceReader> k poskytnutí prostředků specifických pro jazykovou verzi v době běhu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-119">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="54cbe-120">Rozhraní má indexer a `IEnumerable` pro vracení lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-120">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="54cbe-121">`IStringLocalizer`nevyžaduje uložení výchozích řetězců jazyka v souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-121">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="54cbe-122">Můžete vyvíjet aplikaci zaměřenou na lokalizaci a nemusíte vytvářet soubory prostředků na začátku ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="54cbe-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="54cbe-123">Následující kód ukazuje, jak zabalit řetězec "About title" pro lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="54cbe-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="54cbe-124">V předchozím kódu `IStringLocalizer<T>` implementace pochází z [Injektáže závislosti](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="54cbe-124">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="54cbe-125">Pokud se nenalezne lokalizovaná hodnota "o titulku", vrátí se klíč indexeru, tj. řetězec "About title".</span><span class="sxs-lookup"><span data-stu-id="54cbe-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="54cbe-126">Můžete ponechat výchozí řetězcové literály v aplikaci a zabalit je do lokalizátora, abyste se mohli soustředit na vývoj aplikace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="54cbe-127">Vyvíjíte svou aplikaci pomocí výchozího jazyka a připravíte ji pro krok lokalizace bez prvotního vytvoření výchozího souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="54cbe-128">Alternativně můžete použít tradiční přístup a zadat klíč pro načtení výchozího řetězce jazyka.</span><span class="sxs-lookup"><span data-stu-id="54cbe-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="54cbe-129">Pro mnoho vývojářů nový pracovní postup, který nemá výchozí jazyk *. resx* soubor, a jednoduše zabalí řetězcové literály může snížit režii lokalizace aplikace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="54cbe-130">Ostatní vývojáři budou preferovat tradiční pracovní postup, protože mohou usnadnit práci s delšími řetězcovými literály a usnadňují aktualizaci lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="54cbe-131">Použijte `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují kód HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="54cbe-132">`IHtmlLocalizer`HTML kóduje argumenty, které jsou formátovány v řetězci prostředků, ale nekóduje kód HTML samotný řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="54cbe-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="54cbe-133">V ukázce zvýrazněné níže je pouze hodnota `name` parametru kódována HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="54cbe-134">Obecně pouze lokalizovat text, ne HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-134">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="54cbe-135">Na nejnižší úrovni můžete obdržet zástupné `IStringLocalizerFactory` [vkládání závislostí](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="54cbe-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="54cbe-136">Výše uvedený kód ukazuje každou ze dvou metod Create Factory.</span><span class="sxs-lookup"><span data-stu-id="54cbe-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="54cbe-137">Lokalizované řetězce můžete rozdělit do oddílů podle řadiče, oblasti nebo pouze jednoho kontejneru.</span><span class="sxs-lookup"><span data-stu-id="54cbe-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="54cbe-138">V ukázkové aplikaci se `SharedResource` pro sdílené prostředky používá fiktivní třída s názvem.</span><span class="sxs-lookup"><span data-stu-id="54cbe-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="54cbe-139">Někteří vývojáři používají `Startup` třídu pro zahrnutí globálních nebo sdílených řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="54cbe-140">V níže uvedené ukázce se `InfoController` `SharedResource` používají a Localize:</span><span class="sxs-lookup"><span data-stu-id="54cbe-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="54cbe-141">Lokalizace zobrazení</span><span class="sxs-lookup"><span data-stu-id="54cbe-141">View localization</span></span>

<span data-ttu-id="54cbe-142">`IViewLocalizer`Služba poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="54cbe-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="54cbe-143">`ViewLocalizer`Třída implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="54cbe-144">Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="54cbe-145">Výchozí implementace nástroje `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="54cbe-146">Neexistuje možnost použít globální sdílený soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="54cbe-147">`ViewLocalizer`implementuje lokalizátora pomocí `IHtmlLocalizer` , takže Razor nekóduje kód HTML jako lokalizovaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="54cbe-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="54cbe-148">Můžete parametrizovat řetězce prostředků a `IViewLocalizer` zakódovat parametry HTML, ale ne řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="54cbe-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="54cbe-149">Vezměte v úvahu následující Razor značky:</span><span class="sxs-lookup"><span data-stu-id="54cbe-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="54cbe-150">Soubor prostředků francouzštiny může obsahovat následující:</span><span class="sxs-lookup"><span data-stu-id="54cbe-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="54cbe-151">Klíč</span><span class="sxs-lookup"><span data-stu-id="54cbe-151">Key</span></span> | <span data-ttu-id="54cbe-152">Hodnota</span><span class="sxs-lookup"><span data-stu-id="54cbe-152">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="54cbe-153">Vykreslené zobrazení obsahuje značku HTML ze souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-153">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="54cbe-154">Obecně pouze lokalizovat text, ne HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-154">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="54cbe-155">Chcete-li použít sdílený soubor prostředků v zobrazení, vložení `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="54cbe-156">Lokalizace DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="54cbe-156">DataAnnotations localization</span></span>

<span data-ttu-id="54cbe-157">Chybové zprávy pro dataanotace jsou lokalizovány pomocí `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="54cbe-158">Pomocí možnosti `ResourcesPath = "Resources"` mohou být chybové zprávy v nástroji `RegisterViewModel` uloženy v jedné z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="54cbe-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="54cbe-159">*Resources/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="54cbe-160">*Prostředky/ViewModels/účet/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="54cbe-161">V ASP.NET Core MVC 1.1.0 a vyšší jsou lokalizovány neověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="54cbe-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="54cbe-162">ASP.NET Core MVC 1,0 **nehledá lokalizované** řetězce pro atributy, které nejsou ověřovány.</span><span class="sxs-lookup"><span data-stu-id="54cbe-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="54cbe-163">Použití jednoho řetězce prostředku pro více tříd</span><span class="sxs-lookup"><span data-stu-id="54cbe-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="54cbe-164">Následující kód ukazuje, jak použít jeden řetězec prostředku pro atributy ověřování s více třídami:</span><span class="sxs-lookup"><span data-stu-id="54cbe-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="54cbe-165">V předchozím kódu `SharedResource` je třída odpovídající RESX, kde jsou uloženy ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="54cbe-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="54cbe-166">S tímto přístupem budou dataanotace používat pouze `SharedResource` místo prostředků pro každou třídu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="54cbe-167">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="54cbe-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="54cbe-168">SupportedCultures a SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="54cbe-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="54cbe-169">ASP.NET Core umožňuje zadat dvě hodnoty jazykové verze `SupportedCultures` a `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="54cbe-170">Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) pro `SupportedCultures` určuje výsledky funkcí závislých na jazykové verzi, jako je datum, čas, číslo a formátování měny.</span><span class="sxs-lookup"><span data-stu-id="54cbe-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="54cbe-171">`SupportedCultures`Určuje také pořadí řazení textu, konvencí velikosti písmen a porovnávání řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="54cbe-172">Další informace o tom, jak server získá jazykovou verzi, naleznete v tématu [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="54cbe-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="54cbe-173">`SupportedUICultures`Určuje, které přeložené řetězce (ze souborů *. resx* ) jsou vyhledány správcem [prostředků](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="54cbe-173">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="54cbe-174">`ResourceManager`Jednoduše vyhledá řetězce specifické pro jazykovou verzi, které určuje `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="54cbe-175">Každé vlákno v rozhraní .NET má `CurrentCulture` a `CurrentUICulture` objekty.</span><span class="sxs-lookup"><span data-stu-id="54cbe-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="54cbe-176">ASP.NET Core tyto hodnoty kontroluje při vykreslování funkcí závislých na jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="54cbe-177">Pokud je například jazyková verze aktuálního vlákna nastavena na "en-US" (angličtina, USA), `DateTime.Now.ToLongDateString()` zobrazí "čtvrtek, 18. února 2016", ale pokud `CurrentCulture` je nastavená na "ES-ES" (španělština, Španělsko), bude výstup "Jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="54cbe-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="54cbe-178">Soubory prostředků</span><span class="sxs-lookup"><span data-stu-id="54cbe-178">Resource files</span></span>

<span data-ttu-id="54cbe-179">Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců z kódu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="54cbe-180">Přeložené řetězce pro jazyk, který není výchozí, jsou izolované v souborech prostředků *. resx* .</span><span class="sxs-lookup"><span data-stu-id="54cbe-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="54cbe-181">Například může být vhodné vytvořit soubor prostředků španělštiny s názvem *Welcome. ES. resx* obsahující přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="54cbe-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="54cbe-182">"ES" je kód jazyka pro španělštinu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="54cbe-183">Postup vytvoření tohoto souboru prostředků v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="54cbe-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="54cbe-184">V **Průzkumník řešení**klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![Vnořená kontextová nabídka: v Průzkumník řešení je místní nabídka pro prostředky otevřená.](localization/_static/newi.png)

1. <span data-ttu-id="54cbe-187">V poli **Vyhledat nainstalované šablony** zadejte "Resource" a soubor pojmenujte.</span><span class="sxs-lookup"><span data-stu-id="54cbe-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![Dialogové okno Přidat novou položku](localization/_static/res.png)

1. <span data-ttu-id="54cbe-189">Do sloupce **název** zadejte hodnotu klíče (nativní řetězec) a přeložený řetězec ve sloupci Value ( **hodnota** ).</span><span class="sxs-lookup"><span data-stu-id="54cbe-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome. ES. resx soubor (soubor prostředků Welcome pro španělštinu) se slovem Hello ve sloupci název a slovo Hola (text Hello v španělštině) ve sloupci Hodnota](localization/_static/hola.png)

   <span data-ttu-id="54cbe-191">Sada Visual Studio zobrazí *uvítací soubor. ES. resx* .</span><span class="sxs-lookup"><span data-stu-id="54cbe-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Průzkumník řešení se zobrazuje soubor prostředků nástroje Welcome španělština (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="54cbe-193">Pojmenovávání souborů prostředků</span><span class="sxs-lookup"><span data-stu-id="54cbe-193">Resource file naming</span></span>

<span data-ttu-id="54cbe-194">Prostředky jsou pojmenovány pro úplný název typu své třídy minus název sestavení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="54cbe-195">Například francouzská prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu, `LocalizationWebsite.Web.Startup` by měla být pojmenována *Startup. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="54cbe-196">Prostředek pro třídu by měl `LocalizationWebsite.Web.Controllers.HomeController` pojmenovat *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-197">Pokud cílový obor názvů třídy není stejný jako název sestavení, budete potřebovat úplný název typu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="54cbe-198">Například v ukázkovém projektu by měl prostředek pro typ `ExtraNamespace.Tools` název *ExtraNamespace. Tools. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="54cbe-199">V ukázkovém projektu `ConfigureServices` Metoda nastaví `ResourcesPath` na "prostředky", takže relativní cesta projektu pro francouzský soubor prostředků domovského řadiče je *Resources/Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-200">Případně můžete použít složky k uspořádání souborů prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="54cbe-201">V případě domovského kontroleru by tato cesta byla *Resources/Controllers/HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-202">Pokud tuto možnost nepoužijete `ResourcesPath` , soubor *. resx* by přešel do základního adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="54cbe-203">Soubor prostředků pro `HomeController` by měl mít název *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-204">Volba použití konvence pojmenování teček nebo Path závisí na tom, jak chcete uspořádat soubory prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="54cbe-205">Název prostředku</span><span class="sxs-lookup"><span data-stu-id="54cbe-205">Resource name</span></span> | <span data-ttu-id="54cbe-206">Pojmenování teček nebo Path</span><span class="sxs-lookup"><span data-stu-id="54cbe-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="54cbe-207">Prostředky/řadiče. HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="54cbe-208">Tečka</span><span class="sxs-lookup"><span data-stu-id="54cbe-208">Dot</span></span>  |
| <span data-ttu-id="54cbe-209">Prostředky/řadiče/HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="54cbe-210">Cesta</span><span class="sxs-lookup"><span data-stu-id="54cbe-210">Path</span></span> |

<span data-ttu-id="54cbe-211">Soubory prostředků používané `@inject IViewLocalizer` v Razor zobrazeních následují podobně jako vzor.</span><span class="sxs-lookup"><span data-stu-id="54cbe-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="54cbe-212">Soubor prostředků pro zobrazení může být pojmenován buď pomocí názvu tečky, nebo pojmenování cesty.</span><span class="sxs-lookup"><span data-stu-id="54cbe-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="54cbe-213">Razorzobrazení soubory prostředků napodobá cestě k jejich přidruženému souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="54cbe-214">Za předpokladu, že nastavíme `ResourcesPath` "prostředky", soubor francouzského prostředku přidružený k zobrazením */domů/o zobrazení. cshtml* může být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="54cbe-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="54cbe-215">Prostředky/zobrazení/domů/o. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="54cbe-216">Prostředky/zobrazení. domů. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="54cbe-217">Pokud tuto možnost nepoužijete `ResourcesPath` , bude soubor *. resx* pro zobrazení umístěn ve stejné složce jako zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="54cbe-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="54cbe-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="54cbe-219">Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="54cbe-220">K tomu může dojít, když název projektu není platným identifikátorem .NET.</span><span class="sxs-lookup"><span data-stu-id="54cbe-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="54cbe-221">V případě instance `my-project-name.csproj` bude použit kořenový obor názvů `my_project_name` a název sestavení, `my-project-name` který vede k této chybě.</span><span class="sxs-lookup"><span data-stu-id="54cbe-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="54cbe-222">Pokud se kořenový obor názvů sestavení liší od názvu sestavení:</span><span class="sxs-lookup"><span data-stu-id="54cbe-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="54cbe-223">Lokalizace ve výchozím nastavení nefunguje.</span><span class="sxs-lookup"><span data-stu-id="54cbe-223">Localization does not work by default.</span></span>
* <span data-ttu-id="54cbe-224">Lokalizace se nezdařila z důvodu způsobu hledání prostředků v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="54cbe-225">`RootNamespace`je hodnota doby sestavení, která není k dispozici pro spuštěný proces.</span><span class="sxs-lookup"><span data-stu-id="54cbe-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="54cbe-226">Pokud se `RootNamespace` liší od z `AssemblyName` , zahrňte následující v *AssemblyInfo.cs* (s hodnotami parametrů nahrazenými skutečnými hodnotami):</span><span class="sxs-lookup"><span data-stu-id="54cbe-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="54cbe-227">Předchozí kód umožňuje úspěšné vyřešení souborů RESX.</span><span class="sxs-lookup"><span data-stu-id="54cbe-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="54cbe-228">Chování záložního kultivačního prostředí</span><span class="sxs-lookup"><span data-stu-id="54cbe-228">Culture fallback behavior</span></span>

<span data-ttu-id="54cbe-229">Při hledání prostředku se lokalizace zapojit do "záložní kultury".</span><span class="sxs-lookup"><span data-stu-id="54cbe-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="54cbe-230">Od požadované jazykové verze, pokud se nenajde, se vrátí k nadřazené jazykové verzi této jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="54cbe-231">Kromě toho, vlastnost [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) představuje nadřazenou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="54cbe-232">To je obvykle (ale ne vždy) znamená odebrání národního přízpůsobu z ISO.</span><span class="sxs-lookup"><span data-stu-id="54cbe-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="54cbe-233">Například dialekt španělštiny ve španělštině, který se používá v Mexiku, je ES-MX.</span><span class="sxs-lookup"><span data-stu-id="54cbe-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="54cbe-234">Má nadřízenou španělštinu ES, která &mdash; není specifická pro žádnou zemi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="54cbe-235">Představte si, že váš web obdrží žádost o "úvodní" prostředek pomocí kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="54cbe-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="54cbe-236">Systém lokalizace vyhledá následující prostředky v uvedeném pořadí a vybere první shodu:</span><span class="sxs-lookup"><span data-stu-id="54cbe-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="54cbe-237">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="54cbe-238">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="54cbe-239">*Welcome. resx* (Pokud `NeutralResourcesLanguage` je to "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="54cbe-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="54cbe-240">Pokud například odeberete označení jazykové verze ". fr" a máte nastavenou jazykovou verzi na francouzštinu, je výchozí soubor prostředků přečten a jsou lokalizovány řetězce.</span><span class="sxs-lookup"><span data-stu-id="54cbe-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="54cbe-241">Správce prostředků určí výchozí nebo záložní prostředek, pokud nic nevyhovuje vaší požadované jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="54cbe-242">Pokud chcete vrátit klíč pouze v případě, že chybí prostředek pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="54cbe-243">Generování souborů prostředků pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54cbe-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="54cbe-244">Vytvoříte-li soubor prostředků v aplikaci Visual Studio bez jazykové verze v názvu souboru (například *Welcome. resx*), sada Visual Studio vytvoří třídu jazyka C# s vlastností pro každý řetězec.</span><span class="sxs-lookup"><span data-stu-id="54cbe-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="54cbe-245">To obvykle není to, co chcete s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54cbe-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="54cbe-246">Obvykle nemáte výchozí soubor prostředků *. resx* (soubor *. resx* bez názvu jazykové verze).</span><span class="sxs-lookup"><span data-stu-id="54cbe-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="54cbe-247">Návrh souboru *. resx* doporučujeme vytvořit s názvem jazykové verze (například *Welcome. fr. resx*).</span><span class="sxs-lookup"><span data-stu-id="54cbe-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="54cbe-248">Při vytváření souboru *. resx* s názvem jazykové verze aplikace Visual Studio negeneruje soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="54cbe-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="54cbe-249">Přidat další jazykové verze</span><span class="sxs-lookup"><span data-stu-id="54cbe-249">Add other cultures</span></span>

<span data-ttu-id="54cbe-250">Každá kombinace jazyka a jazykové verze (Kromě výchozího jazyka) vyžaduje jedinečný soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="54cbe-251">Soubory prostředků můžete vytvořit pro různé kultury a národní prostředí vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-US**, **fr-CA**a **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="54cbe-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="54cbe-252">Tyto kódy ISO jsou umístěné mezi názvem souboru a příponou souboru *. resx* , jako v *Welcome.ES-MX. resx* (španělština/Mexiko).</span><span class="sxs-lookup"><span data-stu-id="54cbe-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="54cbe-253">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="54cbe-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="54cbe-254">Konfigurace lokalizace</span><span class="sxs-lookup"><span data-stu-id="54cbe-254">Configure localization</span></span>

<span data-ttu-id="54cbe-255">Lokalizace je nakonfigurovaná v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="54cbe-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="54cbe-256">`AddLocalization`Přidá služby lokalizace do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="54cbe-256">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="54cbe-257">Výše uvedený kód také nastaví cestu prostředků na prostředky.</span><span class="sxs-lookup"><span data-stu-id="54cbe-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="54cbe-258">`AddViewLocalization`Přidá podporu pro lokalizované soubory zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-258">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="54cbe-259">V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="54cbe-260">Například "fr" v souboru *index. fr. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="54cbe-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="54cbe-261">`AddDataAnnotationsLocalization`přidává podporu pro lokalizované `DataAnnotations` ověřovací zprávy prostřednictvím `IStringLocalizer` abstrakcí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-261">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="54cbe-262">Middleware lokalizace</span><span class="sxs-lookup"><span data-stu-id="54cbe-262">Localization middleware</span></span>

<span data-ttu-id="54cbe-263">Aktuální jazyková verze v požadavku je nastavena v [middleware](xref:fundamentals/middleware/index)lokalizace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="54cbe-264">Middleware lokalizace je povolená v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="54cbe-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="54cbe-265">Middleware lokalizace musí být konfigurovány před jakýmkoli middlewarem, který by mohl kontrolovat jazykovou verzi žádosti (například `app.UseMvcWithDefaultRoute()` ).</span><span class="sxs-lookup"><span data-stu-id="54cbe-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="54cbe-266">`UseRequestLocalization`Inicializuje `RequestLocalizationOptions` objekt.</span><span class="sxs-lookup"><span data-stu-id="54cbe-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="54cbe-267">Při každém požadavku se vypíše seznam `RequestCultureProvider` ve `RequestLocalizationOptions` výčtu a první zprostředkovatel, který dokáže úspěšně určit jazykovou verzi žádosti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="54cbe-268">Výchozí zprostředkovatelé přicházejí z `RequestLocalizationOptions` třídy:</span><span class="sxs-lookup"><span data-stu-id="54cbe-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="54cbe-269">Výchozí seznam bude z nejpřesnější, aby byl nejméně specifický.</span><span class="sxs-lookup"><span data-stu-id="54cbe-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="54cbe-270">Později v článku se dozvíte, jak můžete změnit pořadí a dokonce přidat vlastního poskytovatele jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="54cbe-271">Pokud žádný z poskytovatelů nemůže určit jazykovou verzi žádosti, `DefaultRequestCulture` je použita.</span><span class="sxs-lookup"><span data-stu-id="54cbe-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="54cbe-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="54cbe-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="54cbe-273">Některé aplikace budou používat řetězec dotazu k nastavení jazykové verze [a jazykové verze uživatelského rozhraní](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="54cbe-273">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="54cbe-274">Pro aplikace, které používají cookie přístup k hlavičkám nebo Accept-Language, je přidání řetězce dotazu na adresu URL užitečné pro ladění a testování kódu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="54cbe-275">Ve výchozím nastavení `QueryStringRequestCultureProvider` je jako první poskytovatel lokalizace v `RequestCultureProvider` seznamu zaregistrován.</span><span class="sxs-lookup"><span data-stu-id="54cbe-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="54cbe-276">Předáte parametry řetězce dotazu `culture` a `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="54cbe-277">Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělština/Mexiko:</span><span class="sxs-lookup"><span data-stu-id="54cbe-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="54cbe-278">Pokud předáte pouze jeden z těchto dvou ( `culture` nebo `ui-culture` ), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí toho, kterou jste předali.</span><span class="sxs-lookup"><span data-stu-id="54cbe-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="54cbe-279">Například nastavení pouze jazyková verze nastaví jak `Culture` a `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a><span data-ttu-id="54cbe-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="54cbe-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="54cbe-281">Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze s ASP.NET Coreovou kulturou cookie .</span><span class="sxs-lookup"><span data-stu-id="54cbe-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="54cbe-282">Použijte `MakeCookieValue` metodu k vytvoření cookie .</span><span class="sxs-lookup"><span data-stu-id="54cbe-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="54cbe-283">`CookieRequestCultureProvider` `DefaultCookieName` Vrátí výchozí název, který cookie slouží ke sledování informací o preferované jazykové verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="54cbe-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="54cbe-284">Výchozí cookie název je `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="54cbe-285">cookieFormát je `c=%LANGCODE%|uic=%LANGCODE%` , kde `c` je `Culture` a je například `uic` `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="54cbe-286">Pokud zadáte pouze jednu z informací o jazykové verzi a jazykovou verzi uživatelského rozhraní, zadaná jazyková verze bude použita pro informace o jazykové verzi i jazyková verze uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="54cbe-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="54cbe-287">Hlavička protokolu HTTP pro přijetí – jazyk</span><span class="sxs-lookup"><span data-stu-id="54cbe-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="54cbe-288">[Záhlaví Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) lze nastavit ve většině prohlížečů a původně bylo určeno pro určení jazyka uživatele.</span><span class="sxs-lookup"><span data-stu-id="54cbe-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="54cbe-289">Toto nastavení indikuje, co je v prohlížeči nastavené na Odeslat nebo zděděné z podkladového operačního systému.</span><span class="sxs-lookup"><span data-stu-id="54cbe-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="54cbe-290">Hlavička protokolu HTTP Accept-Language z požadavku prohlížeče není infallible způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="54cbe-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="54cbe-291">Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu kultury.</span><span class="sxs-lookup"><span data-stu-id="54cbe-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="54cbe-292">Nastavení hlavičky protokolu HTTP Accept-Language v IE</span><span class="sxs-lookup"><span data-stu-id="54cbe-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="54cbe-293">Z ikony ozubeného kolečka klepněte na **Možnosti Internetu**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-293">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="54cbe-294">Klepněte na **jazyky**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-294">Tap **Languages**.</span></span>

   ![Možnosti Internetu](localization/_static/lang.png)

1. <span data-ttu-id="54cbe-296">Klepněte na **nastavit jazykové předvolby**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-296">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="54cbe-297">Klepněte na **Přidat jazyk**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-297">Tap **Add a language**.</span></span>

1. <span data-ttu-id="54cbe-298">Přidejte jazyk.</span><span class="sxs-lookup"><span data-stu-id="54cbe-298">Add the language.</span></span>

1. <span data-ttu-id="54cbe-299">Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-299">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="54cbe-300">Použití vlastního zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="54cbe-300">Use a custom provider</span></span>

<span data-ttu-id="54cbe-301">Předpokládejme, že chcete, aby vaši zákazníci mohli ukládat svůj jazyk a jazykovou verzi do databází.</span><span class="sxs-lookup"><span data-stu-id="54cbe-301">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="54cbe-302">Můžete napsat poskytovatele a vyhledat tyto hodnoty pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="54cbe-302">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="54cbe-303">Následující kód ukazuje, jak přidat vlastního zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="54cbe-303">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="54cbe-304">Použijte `RequestLocalizationOptions` k přidání nebo odebrání poskytovatelů lokalizace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-304">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="54cbe-305">Programové nastavení kultury</span><span class="sxs-lookup"><span data-stu-id="54cbe-305">Set the culture programmatically</span></span>

<span data-ttu-id="54cbe-306">Tento ukázkový projekt **Localization. StarterWeb** na [GITHUBU](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-306">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="54cbe-307">Soubor *views/Shared/_SelectLanguagePartial. cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných jazykových verzí:</span><span class="sxs-lookup"><span data-stu-id="54cbe-307">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="54cbe-308">Soubor *views/Shared/_SelectLanguagePartial. cshtml* se přidá do `footer` oddílu souboru rozložení, takže bude k dispozici pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="54cbe-308">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="54cbe-309">`SetLanguage`Metoda nastaví jazykovou verzi cookie .</span><span class="sxs-lookup"><span data-stu-id="54cbe-309">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="54cbe-310">Nemůžete připojit *_SelectLanguagePartial. cshtml* k ukázkovému kódu pro tento projekt.</span><span class="sxs-lookup"><span data-stu-id="54cbe-310">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="54cbe-311">Projekt **Localization. StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) obsahuje kód pro tok `RequestLocalizationOptions` do Razor částečného toku prostřednictvím kontejneru pro [vkládání závislostí](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="54cbe-311">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="54cbe-312">Vazba modelu data směrování a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="54cbe-312">Model binding route data and query strings</span></span>

<span data-ttu-id="54cbe-313">Podívejte [se na téma chování globalizace modelu vazby dat trasy a řetězce dotazu](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="54cbe-313">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="54cbe-314">Výrazy globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="54cbe-314">Globalization and localization terms</span></span>

<span data-ttu-id="54cbe-315">Proces lokalizace vaší aplikace také vyžaduje základní znalosti relevantních znakových sad, které se běžně používají při vývoji moderního softwaru, a porozumění problémům, které s nimi souvisejí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-315">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="54cbe-316">I když všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text s různými čísly.</span><span class="sxs-lookup"><span data-stu-id="54cbe-316">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="54cbe-317">Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-317">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="54cbe-318">[Lokalizace](/dotnet/standard/globalization-localization/localizability-review) je přechodný proces pro ověření, že globální aplikace je připravená na lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="54cbe-318">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="54cbe-319">Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název jazykové verze je `<languagecode2>-<country/regioncode2>` , kde `<languagecode2>` je kód jazyka a `<country/regioncode2>` je kód subkultury.</span><span class="sxs-lookup"><span data-stu-id="54cbe-319">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="54cbe-320">Například `es-CL` pro španělštinu (Chile), `en-US` pro angličtinu (USA) a `en-AU` pro angličtinu (Austrálie).</span><span class="sxs-lookup"><span data-stu-id="54cbe-320">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="54cbe-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinací kódu kultury ISO 639 2 s malým písmenem, který je přidružený k jazyku, a kódu subkultury na velká písmena ISO 3166 2, který je přidružený k zemi nebo oblasti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="54cbe-322">Viz [název jazykové verze](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="54cbe-322">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="54cbe-323">Mezinárodní využití se často zkracuje na "I18N".</span><span class="sxs-lookup"><span data-stu-id="54cbe-323">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="54cbe-324">Zkratka používá první a poslední písmena a počet písmen mezi nimi, takže 18 představuje počet písmen mezi první I a poslední znak "N".</span><span class="sxs-lookup"><span data-stu-id="54cbe-324">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="54cbe-325">Totéž platí pro globalizaci (G11N) a lokalizaci (L10N).</span><span class="sxs-lookup"><span data-stu-id="54cbe-325">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="54cbe-326">Uvedenými</span><span class="sxs-lookup"><span data-stu-id="54cbe-326">Terms:</span></span>

* <span data-ttu-id="54cbe-327">Globalizace (G11N): proces vytvoření aplikace, která podporuje různé jazyky a oblasti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-327">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="54cbe-328">Lokalizace (L10N): proces přizpůsobení aplikace pro daný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-328">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="54cbe-329">Mezinárodní (I18N): popisuje globalizaci a lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="54cbe-329">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="54cbe-330">Jazyková verze: Jedná se o jazyk a volitelně i oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-330">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="54cbe-331">Neutrální jazyková verze: jazyková verze, která má zadaný jazyk, ale ne oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-331">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="54cbe-332">(například "en", "ES")</span><span class="sxs-lookup"><span data-stu-id="54cbe-332">(for example "en", "es")</span></span>
* <span data-ttu-id="54cbe-333">Specifická jazyková verze: jazyková verze, která má zadaný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-333">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="54cbe-334">(například "en-US", "en-GB", "ES-CL")</span><span class="sxs-lookup"><span data-stu-id="54cbe-334">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="54cbe-335">Nadřazená jazyková verze: neutrální jazyková verze, která obsahuje konkrétní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-335">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="54cbe-336">(například "en" je nadřazená jazyková verze "en-US" a "en-GB")</span><span class="sxs-lookup"><span data-stu-id="54cbe-336">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="54cbe-337">Národní prostředí: národní prostředí je stejné jako jazyková verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-337">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="54cbe-338">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="54cbe-338">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="54cbe-339">[StarterWeb projekt Localization](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) , který se používá v článku.</span><span class="sxs-lookup"><span data-stu-id="54cbe-339">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="54cbe-340">Globalizace a lokalizace aplikací .NET</span><span class="sxs-lookup"><span data-stu-id="54cbe-340">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="54cbe-341">Prostředky v souborech. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-341">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="54cbe-342">Sada nástrojů pro vícejazyčné aplikace od Microsoftu</span><span class="sxs-lookup"><span data-stu-id="54cbe-342">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="54cbe-343">Lokalizace & – obecné typy</span><span class="sxs-lookup"><span data-stu-id="54cbe-343">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="54cbe-344">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)a [Hisham bin](https://twitter.com/hishambinateya) Ateya</span><span class="sxs-lookup"><span data-stu-id="54cbe-344">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="54cbe-345">Vícejazyčný web umožňuje webu oslovit širší cílovou skupinu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-345">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="54cbe-346">ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.</span><span class="sxs-lookup"><span data-stu-id="54cbe-346">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="54cbe-347">Mezinárodní využití zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="54cbe-347">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="54cbe-348">Globalizace je proces návrhu aplikací, které podporují různé jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-348">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="54cbe-349">Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují na konkrétní geografické oblasti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-349">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="54cbe-350">Lokalizace je proces přizpůsobení globální aplikace, kterou jste již zpracovali pro lokalizaci, do konkrétní jazykové verze nebo národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-350">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="54cbe-351">Další informace najdete v tématu **předpoklady globalizace a lokalizace** poblíž konce tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-351">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="54cbe-352">Lokalizace aplikace zahrnuje následující:</span><span class="sxs-lookup"><span data-stu-id="54cbe-352">App localization involves the following:</span></span>

1. <span data-ttu-id="54cbe-353">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="54cbe-353">Make the app's content localizable</span></span>
1. <span data-ttu-id="54cbe-354">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="54cbe-354">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="54cbe-355">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="54cbe-355">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="54cbe-356">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54cbe-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="54cbe-357">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="54cbe-357">Make the app's content localizable</span></span>

<span data-ttu-id="54cbe-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer>a <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> bylo navrženo pro zlepšení produktivity při vývoji lokalizovaných aplikací.</span><span class="sxs-lookup"><span data-stu-id="54cbe-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="54cbe-359">`IStringLocalizer`používá <xref:System.Resources.ResourceManager> a <xref:System.Resources.ResourceReader> k poskytnutí prostředků specifických pro jazykovou verzi v době běhu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-359">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="54cbe-360">Rozhraní má indexer a `IEnumerable` pro vracení lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-360">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="54cbe-361">`IStringLocalizer`nevyžaduje uložení výchozích řetězců jazyka v souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-361">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="54cbe-362">Můžete vyvíjet aplikaci zaměřenou na lokalizaci a nemusíte vytvářet soubory prostředků na začátku ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="54cbe-362">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="54cbe-363">Následující kód ukazuje, jak zabalit řetězec "About title" pro lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="54cbe-363">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="54cbe-364">V předchozím kódu `IStringLocalizer<T>` implementace pochází z [Injektáže závislosti](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="54cbe-364">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="54cbe-365">Pokud se nenalezne lokalizovaná hodnota "o titulku", vrátí se klíč indexeru, tj. řetězec "About title".</span><span class="sxs-lookup"><span data-stu-id="54cbe-365">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="54cbe-366">Můžete ponechat výchozí řetězcové literály v aplikaci a zabalit je do lokalizátora, abyste se mohli soustředit na vývoj aplikace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-366">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="54cbe-367">Vyvíjíte svou aplikaci pomocí výchozího jazyka a připravíte ji pro krok lokalizace bez prvotního vytvoření výchozího souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-367">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="54cbe-368">Alternativně můžete použít tradiční přístup a zadat klíč pro načtení výchozího řetězce jazyka.</span><span class="sxs-lookup"><span data-stu-id="54cbe-368">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="54cbe-369">Pro mnoho vývojářů nový pracovní postup, který nemá výchozí jazyk *. resx* soubor, a jednoduše zabalí řetězcové literály může snížit režii lokalizace aplikace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-369">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="54cbe-370">Ostatní vývojáři budou preferovat tradiční pracovní postup, protože mohou usnadnit práci s delšími řetězcovými literály a usnadňují aktualizaci lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-370">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="54cbe-371">Použijte `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují kód HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-371">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="54cbe-372">`IHtmlLocalizer`HTML kóduje argumenty, které jsou formátovány v řetězci prostředků, ale nekóduje kód HTML samotný řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="54cbe-372">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="54cbe-373">V ukázce zvýrazněné níže je pouze hodnota `name` parametru kódována HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-373">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="54cbe-374">Obecně pouze lokalizovat text, ne HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-374">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="54cbe-375">Na nejnižší úrovni můžete obdržet zástupné `IStringLocalizerFactory` [vkládání závislostí](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="54cbe-375">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="54cbe-376">Výše uvedený kód ukazuje každou ze dvou metod Create Factory.</span><span class="sxs-lookup"><span data-stu-id="54cbe-376">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="54cbe-377">Lokalizované řetězce můžete rozdělit do oddílů podle řadiče, oblasti nebo pouze jednoho kontejneru.</span><span class="sxs-lookup"><span data-stu-id="54cbe-377">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="54cbe-378">V ukázkové aplikaci se `SharedResource` pro sdílené prostředky používá fiktivní třída s názvem.</span><span class="sxs-lookup"><span data-stu-id="54cbe-378">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="54cbe-379">Někteří vývojáři používají `Startup` třídu pro zahrnutí globálních nebo sdílených řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-379">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="54cbe-380">V níže uvedené ukázce se `InfoController` `SharedResource` používají a Localize:</span><span class="sxs-lookup"><span data-stu-id="54cbe-380">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="54cbe-381">Lokalizace zobrazení</span><span class="sxs-lookup"><span data-stu-id="54cbe-381">View localization</span></span>

<span data-ttu-id="54cbe-382">`IViewLocalizer`Služba poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="54cbe-382">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="54cbe-383">`ViewLocalizer`Třída implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-383">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="54cbe-384">Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-384">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="54cbe-385">Výchozí implementace nástroje `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-385">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="54cbe-386">Neexistuje možnost použít globální sdílený soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-386">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="54cbe-387">`ViewLocalizer`implementuje lokalizátora pomocí `IHtmlLocalizer` , takže Razor nekóduje kód HTML jako lokalizovaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="54cbe-387">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="54cbe-388">Můžete parametrizovat řetězce prostředků a `IViewLocalizer` zakódovat parametry HTML, ale ne řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="54cbe-388">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="54cbe-389">Vezměte v úvahu následující Razor značky:</span><span class="sxs-lookup"><span data-stu-id="54cbe-389">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="54cbe-390">Soubor prostředků francouzštiny může obsahovat následující:</span><span class="sxs-lookup"><span data-stu-id="54cbe-390">A French resource file could contain the following:</span></span>

| <span data-ttu-id="54cbe-391">Klíč</span><span class="sxs-lookup"><span data-stu-id="54cbe-391">Key</span></span> | <span data-ttu-id="54cbe-392">Hodnota</span><span class="sxs-lookup"><span data-stu-id="54cbe-392">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="54cbe-393">Vykreslené zobrazení obsahuje značku HTML ze souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-393">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="54cbe-394">Obecně pouze lokalizovat text, ne HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-394">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="54cbe-395">Chcete-li použít sdílený soubor prostředků v zobrazení, vložení `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-395">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="54cbe-396">Lokalizace DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="54cbe-396">DataAnnotations localization</span></span>

<span data-ttu-id="54cbe-397">Chybové zprávy pro dataanotace jsou lokalizovány pomocí `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-397">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="54cbe-398">Pomocí možnosti `ResourcesPath = "Resources"` mohou být chybové zprávy v nástroji `RegisterViewModel` uloženy v jedné z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="54cbe-398">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="54cbe-399">*Resources/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-399">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="54cbe-400">*Prostředky/ViewModels/účet/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="54cbe-401">V ASP.NET Core MVC 1.1.0 a vyšší jsou lokalizovány neověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="54cbe-401">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="54cbe-402">ASP.NET Core MVC 1,0 **nehledá lokalizované** řetězce pro atributy, které nejsou ověřovány.</span><span class="sxs-lookup"><span data-stu-id="54cbe-402">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="54cbe-403">Použití jednoho řetězce prostředku pro více tříd</span><span class="sxs-lookup"><span data-stu-id="54cbe-403">Using one resource string for multiple classes</span></span>

<span data-ttu-id="54cbe-404">Následující kód ukazuje, jak použít jeden řetězec prostředku pro atributy ověřování s více třídami:</span><span class="sxs-lookup"><span data-stu-id="54cbe-404">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="54cbe-405">V předchozím kódu `SharedResource` je třída odpovídající RESX, kde jsou uloženy ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="54cbe-405">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="54cbe-406">S tímto přístupem budou dataanotace používat pouze `SharedResource` místo prostředků pro každou třídu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-406">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="54cbe-407">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="54cbe-407">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="54cbe-408">SupportedCultures a SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="54cbe-408">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="54cbe-409">ASP.NET Core umožňuje zadat dvě hodnoty jazykové verze `SupportedCultures` a `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-409">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="54cbe-410">Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) pro `SupportedCultures` určuje výsledky funkcí závislých na jazykové verzi, jako je datum, čas, číslo a formátování měny.</span><span class="sxs-lookup"><span data-stu-id="54cbe-410">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="54cbe-411">`SupportedCultures`Určuje také pořadí řazení textu, konvencí velikosti písmen a porovnávání řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-411">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="54cbe-412">Další informace o tom, jak server získá jazykovou verzi, naleznete v tématu [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="54cbe-412">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="54cbe-413">`SupportedUICultures`Určuje, které přeložené řetězce (ze souborů *. resx* ) jsou vyhledány správcem [prostředků](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="54cbe-413">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="54cbe-414">`ResourceManager`Jednoduše vyhledá řetězce specifické pro jazykovou verzi, které určuje `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-414">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="54cbe-415">Každé vlákno v rozhraní .NET má `CurrentCulture` a `CurrentUICulture` objekty.</span><span class="sxs-lookup"><span data-stu-id="54cbe-415">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="54cbe-416">ASP.NET Core tyto hodnoty kontroluje při vykreslování funkcí závislých na jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-416">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="54cbe-417">Pokud je například jazyková verze aktuálního vlákna nastavena na "en-US" (angličtina, USA), `DateTime.Now.ToLongDateString()` zobrazí "čtvrtek, 18. února 2016", ale pokud `CurrentCulture` je nastavená na "ES-ES" (španělština, Španělsko), bude výstup "Jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="54cbe-417">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="54cbe-418">Soubory prostředků</span><span class="sxs-lookup"><span data-stu-id="54cbe-418">Resource files</span></span>

<span data-ttu-id="54cbe-419">Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců z kódu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-419">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="54cbe-420">Přeložené řetězce pro jazyk, který není výchozí, jsou izolované v souborech prostředků *. resx* .</span><span class="sxs-lookup"><span data-stu-id="54cbe-420">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="54cbe-421">Například může být vhodné vytvořit soubor prostředků španělštiny s názvem *Welcome. ES. resx* obsahující přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="54cbe-421">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="54cbe-422">"ES" je kód jazyka pro španělštinu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-422">"es" is the language code for Spanish.</span></span> <span data-ttu-id="54cbe-423">Postup vytvoření tohoto souboru prostředků v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="54cbe-423">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="54cbe-424">V **Průzkumník řešení**klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-424">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![Vnořená kontextová nabídka: v Průzkumník řešení je místní nabídka pro prostředky otevřená.](localization/_static/newi.png)

1. <span data-ttu-id="54cbe-427">V poli **Vyhledat nainstalované šablony** zadejte "Resource" a soubor pojmenujte.</span><span class="sxs-lookup"><span data-stu-id="54cbe-427">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![Dialogové okno Přidat novou položku](localization/_static/res.png)

1. <span data-ttu-id="54cbe-429">Do sloupce **název** zadejte hodnotu klíče (nativní řetězec) a přeložený řetězec ve sloupci Value ( **hodnota** ).</span><span class="sxs-lookup"><span data-stu-id="54cbe-429">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome. ES. resx soubor (soubor prostředků Welcome pro španělštinu) se slovem Hello ve sloupci název a slovo Hola (text Hello v španělštině) ve sloupci Hodnota](localization/_static/hola.png)

   <span data-ttu-id="54cbe-431">Sada Visual Studio zobrazí *uvítací soubor. ES. resx* .</span><span class="sxs-lookup"><span data-stu-id="54cbe-431">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Průzkumník řešení se zobrazuje soubor prostředků nástroje Welcome španělština (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="54cbe-433">Pojmenovávání souborů prostředků</span><span class="sxs-lookup"><span data-stu-id="54cbe-433">Resource file naming</span></span>

<span data-ttu-id="54cbe-434">Prostředky jsou pojmenovány pro úplný název typu své třídy minus název sestavení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-434">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="54cbe-435">Například francouzská prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu, `LocalizationWebsite.Web.Startup` by měla být pojmenována *Startup. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-435">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="54cbe-436">Prostředek pro třídu by měl `LocalizationWebsite.Web.Controllers.HomeController` pojmenovat *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-436">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-437">Pokud cílový obor názvů třídy není stejný jako název sestavení, budete potřebovat úplný název typu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-437">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="54cbe-438">Například v ukázkovém projektu by měl prostředek pro typ `ExtraNamespace.Tools` název *ExtraNamespace. Tools. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-438">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="54cbe-439">V ukázkovém projektu `ConfigureServices` Metoda nastaví `ResourcesPath` na "prostředky", takže relativní cesta projektu pro francouzský soubor prostředků domovského řadiče je *Resources/Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-439">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-440">Případně můžete použít složky k uspořádání souborů prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-440">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="54cbe-441">V případě domovského kontroleru by tato cesta byla *Resources/Controllers/HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-441">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-442">Pokud tuto možnost nepoužijete `ResourcesPath` , soubor *. resx* by přešel do základního adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-442">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="54cbe-443">Soubor prostředků pro `HomeController` by měl mít název *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-443">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-444">Volba použití konvence pojmenování teček nebo Path závisí na tom, jak chcete uspořádat soubory prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-444">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="54cbe-445">Název prostředku</span><span class="sxs-lookup"><span data-stu-id="54cbe-445">Resource name</span></span> | <span data-ttu-id="54cbe-446">Pojmenování teček nebo Path</span><span class="sxs-lookup"><span data-stu-id="54cbe-446">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="54cbe-447">Prostředky/řadiče. HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-447">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="54cbe-448">Tečka</span><span class="sxs-lookup"><span data-stu-id="54cbe-448">Dot</span></span>  |
| <span data-ttu-id="54cbe-449">Prostředky/řadiče/HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-449">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="54cbe-450">Cesta</span><span class="sxs-lookup"><span data-stu-id="54cbe-450">Path</span></span> |

<span data-ttu-id="54cbe-451">Soubory prostředků používané `@inject IViewLocalizer` v Razor zobrazeních následují podobně jako vzor.</span><span class="sxs-lookup"><span data-stu-id="54cbe-451">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="54cbe-452">Soubor prostředků pro zobrazení může být pojmenován buď pomocí názvu tečky, nebo pojmenování cesty.</span><span class="sxs-lookup"><span data-stu-id="54cbe-452">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="54cbe-453">Razorzobrazení soubory prostředků napodobá cestě k jejich přidruženému souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-453">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="54cbe-454">Za předpokladu, že nastavíme `ResourcesPath` "prostředky", soubor francouzského prostředku přidružený k zobrazením */domů/o zobrazení. cshtml* může být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="54cbe-454">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="54cbe-455">Prostředky/zobrazení/domů/o. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-455">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="54cbe-456">Prostředky/zobrazení. domů. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-456">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="54cbe-457">Pokud tuto možnost nepoužijete `ResourcesPath` , bude soubor *. resx* pro zobrazení umístěn ve stejné složce jako zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-457">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="54cbe-458">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="54cbe-458">RootNamespaceAttribute</span></span> 

<span data-ttu-id="54cbe-459">Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-459">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="54cbe-460">K tomu může dojít, když název projektu není platným identifikátorem .NET.</span><span class="sxs-lookup"><span data-stu-id="54cbe-460">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="54cbe-461">V případě instance `my-project-name.csproj` bude použit kořenový obor názvů `my_project_name` a název sestavení, `my-project-name` který vede k této chybě.</span><span class="sxs-lookup"><span data-stu-id="54cbe-461">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="54cbe-462">Pokud se kořenový obor názvů sestavení liší od názvu sestavení:</span><span class="sxs-lookup"><span data-stu-id="54cbe-462">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="54cbe-463">Lokalizace ve výchozím nastavení nefunguje.</span><span class="sxs-lookup"><span data-stu-id="54cbe-463">Localization does not work by default.</span></span>
* <span data-ttu-id="54cbe-464">Lokalizace se nezdařila z důvodu způsobu hledání prostředků v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-464">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="54cbe-465">`RootNamespace`je hodnota doby sestavení, která není k dispozici pro spuštěný proces.</span><span class="sxs-lookup"><span data-stu-id="54cbe-465">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="54cbe-466">Pokud se `RootNamespace` liší od z `AssemblyName` , zahrňte následující v *AssemblyInfo.cs* (s hodnotami parametrů nahrazenými skutečnými hodnotami):</span><span class="sxs-lookup"><span data-stu-id="54cbe-466">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="54cbe-467">Předchozí kód umožňuje úspěšné vyřešení souborů RESX.</span><span class="sxs-lookup"><span data-stu-id="54cbe-467">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="54cbe-468">Chování záložního kultivačního prostředí</span><span class="sxs-lookup"><span data-stu-id="54cbe-468">Culture fallback behavior</span></span>

<span data-ttu-id="54cbe-469">Při hledání prostředku se lokalizace zapojit do "záložní kultury".</span><span class="sxs-lookup"><span data-stu-id="54cbe-469">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="54cbe-470">Od požadované jazykové verze, pokud se nenajde, se vrátí k nadřazené jazykové verzi této jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-470">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="54cbe-471">Kromě toho, vlastnost [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) představuje nadřazenou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-471">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="54cbe-472">To je obvykle (ale ne vždy) znamená odebrání národního přízpůsobu z ISO.</span><span class="sxs-lookup"><span data-stu-id="54cbe-472">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="54cbe-473">Například dialekt španělštiny ve španělštině, který se používá v Mexiku, je ES-MX.</span><span class="sxs-lookup"><span data-stu-id="54cbe-473">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="54cbe-474">Má nadřízenou španělštinu ES, která &mdash; není specifická pro žádnou zemi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-474">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="54cbe-475">Představte si, že váš web obdrží žádost o "úvodní" prostředek pomocí kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="54cbe-475">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="54cbe-476">Systém lokalizace vyhledá následující prostředky v uvedeném pořadí a vybere první shodu:</span><span class="sxs-lookup"><span data-stu-id="54cbe-476">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="54cbe-477">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-477">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="54cbe-478">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-478">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="54cbe-479">*Welcome. resx* (Pokud `NeutralResourcesLanguage` je to "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="54cbe-479">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="54cbe-480">Pokud například odeberete označení jazykové verze ". fr" a máte nastavenou jazykovou verzi na francouzštinu, je výchozí soubor prostředků přečten a jsou lokalizovány řetězce.</span><span class="sxs-lookup"><span data-stu-id="54cbe-480">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="54cbe-481">Správce prostředků určí výchozí nebo záložní prostředek, pokud nic nevyhovuje vaší požadované jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-481">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="54cbe-482">Pokud chcete vrátit klíč pouze v případě, že chybí prostředek pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-482">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="54cbe-483">Generování souborů prostředků pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54cbe-483">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="54cbe-484">Vytvoříte-li soubor prostředků v aplikaci Visual Studio bez jazykové verze v názvu souboru (například *Welcome. resx*), sada Visual Studio vytvoří třídu jazyka C# s vlastností pro každý řetězec.</span><span class="sxs-lookup"><span data-stu-id="54cbe-484">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="54cbe-485">To obvykle není to, co chcete s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54cbe-485">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="54cbe-486">Obvykle nemáte výchozí soubor prostředků *. resx* (soubor *. resx* bez názvu jazykové verze).</span><span class="sxs-lookup"><span data-stu-id="54cbe-486">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="54cbe-487">Návrh souboru *. resx* doporučujeme vytvořit s názvem jazykové verze (například *Welcome. fr. resx*).</span><span class="sxs-lookup"><span data-stu-id="54cbe-487">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="54cbe-488">Při vytváření souboru *. resx* s názvem jazykové verze aplikace Visual Studio negeneruje soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="54cbe-488">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="54cbe-489">Přidat další jazykové verze</span><span class="sxs-lookup"><span data-stu-id="54cbe-489">Add other cultures</span></span>

<span data-ttu-id="54cbe-490">Každá kombinace jazyka a jazykové verze (Kromě výchozího jazyka) vyžaduje jedinečný soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-490">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="54cbe-491">Soubory prostředků můžete vytvořit pro různé kultury a národní prostředí vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-US**, **fr-CA**a **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="54cbe-491">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="54cbe-492">Tyto kódy ISO jsou umístěné mezi názvem souboru a příponou souboru *. resx* , jako v *Welcome.ES-MX. resx* (španělština/Mexiko).</span><span class="sxs-lookup"><span data-stu-id="54cbe-492">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="54cbe-493">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="54cbe-493">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="54cbe-494">Konfigurace lokalizace</span><span class="sxs-lookup"><span data-stu-id="54cbe-494">Configure localization</span></span>

<span data-ttu-id="54cbe-495">Lokalizace je nakonfigurovaná v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="54cbe-495">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="54cbe-496">`AddLocalization`Přidá služby lokalizace do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="54cbe-496">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="54cbe-497">Výše uvedený kód také nastaví cestu prostředků na prostředky.</span><span class="sxs-lookup"><span data-stu-id="54cbe-497">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="54cbe-498">`AddViewLocalization`Přidá podporu pro lokalizované soubory zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-498">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="54cbe-499">V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-499">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="54cbe-500">Například "fr" v souboru *index. fr. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="54cbe-500">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="54cbe-501">`AddDataAnnotationsLocalization`přidává podporu pro lokalizované `DataAnnotations` ověřovací zprávy prostřednictvím `IStringLocalizer` abstrakcí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-501">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="54cbe-502">Middleware lokalizace</span><span class="sxs-lookup"><span data-stu-id="54cbe-502">Localization middleware</span></span>

<span data-ttu-id="54cbe-503">Aktuální jazyková verze v požadavku je nastavena v [middleware](xref:fundamentals/middleware/index)lokalizace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-503">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="54cbe-504">Middleware lokalizace je povolená v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="54cbe-504">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="54cbe-505">Middleware lokalizace musí být konfigurovány před jakýmkoli middlewarem, který by mohl kontrolovat jazykovou verzi žádosti (například `app.UseMvcWithDefaultRoute()` ).</span><span class="sxs-lookup"><span data-stu-id="54cbe-505">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="54cbe-506">`UseRequestLocalization`Inicializuje `RequestLocalizationOptions` objekt.</span><span class="sxs-lookup"><span data-stu-id="54cbe-506">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="54cbe-507">Při každém požadavku se vypíše seznam `RequestCultureProvider` ve `RequestLocalizationOptions` výčtu a první zprostředkovatel, který dokáže úspěšně určit jazykovou verzi žádosti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-507">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="54cbe-508">Výchozí zprostředkovatelé přicházejí z `RequestLocalizationOptions` třídy:</span><span class="sxs-lookup"><span data-stu-id="54cbe-508">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="54cbe-509">Výchozí seznam bude z nejpřesnější, aby byl nejméně specifický.</span><span class="sxs-lookup"><span data-stu-id="54cbe-509">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="54cbe-510">Později v článku se dozvíte, jak můžete změnit pořadí a dokonce přidat vlastního poskytovatele jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-510">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="54cbe-511">Pokud žádný z poskytovatelů nemůže určit jazykovou verzi žádosti, `DefaultRequestCulture` je použita.</span><span class="sxs-lookup"><span data-stu-id="54cbe-511">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="54cbe-512">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="54cbe-512">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="54cbe-513">Některé aplikace budou používat řetězec dotazu k nastavení jazykové verze [a jazykové verze uživatelského rozhraní](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="54cbe-513">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="54cbe-514">Pro aplikace, které používají cookie přístup k hlavičkám nebo Accept-Language, je přidání řetězce dotazu na adresu URL užitečné pro ladění a testování kódu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-514">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="54cbe-515">Ve výchozím nastavení `QueryStringRequestCultureProvider` je jako první poskytovatel lokalizace v `RequestCultureProvider` seznamu zaregistrován.</span><span class="sxs-lookup"><span data-stu-id="54cbe-515">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="54cbe-516">Předáte parametry řetězce dotazu `culture` a `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-516">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="54cbe-517">Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělština/Mexiko:</span><span class="sxs-lookup"><span data-stu-id="54cbe-517">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="54cbe-518">Pokud předáte pouze jeden z těchto dvou ( `culture` nebo `ui-culture` ), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí toho, kterou jste předali.</span><span class="sxs-lookup"><span data-stu-id="54cbe-518">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="54cbe-519">Například nastavení pouze jazyková verze nastaví jak `Culture` a `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-519">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a><span data-ttu-id="54cbe-520">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="54cbe-520">CookieRequestCultureProvider</span></span>

<span data-ttu-id="54cbe-521">Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze s ASP.NET Coreovou kulturou cookie .</span><span class="sxs-lookup"><span data-stu-id="54cbe-521">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="54cbe-522">Použijte `MakeCookieValue` metodu k vytvoření cookie .</span><span class="sxs-lookup"><span data-stu-id="54cbe-522">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="54cbe-523">`CookieRequestCultureProvider` `DefaultCookieName` Vrátí výchozí název, který cookie slouží ke sledování informací o preferované jazykové verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="54cbe-523">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="54cbe-524">Výchozí cookie název je `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-524">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="54cbe-525">cookieFormát je `c=%LANGCODE%|uic=%LANGCODE%` , kde `c` je `Culture` a je například `uic` `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-525">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="54cbe-526">Pokud zadáte pouze jednu z informací o jazykové verzi a jazykovou verzi uživatelského rozhraní, zadaná jazyková verze bude použita pro informace o jazykové verzi i jazyková verze uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="54cbe-526">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="54cbe-527">Hlavička protokolu HTTP pro přijetí – jazyk</span><span class="sxs-lookup"><span data-stu-id="54cbe-527">The Accept-Language HTTP header</span></span>

<span data-ttu-id="54cbe-528">[Záhlaví Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) lze nastavit ve většině prohlížečů a původně bylo určeno pro určení jazyka uživatele.</span><span class="sxs-lookup"><span data-stu-id="54cbe-528">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="54cbe-529">Toto nastavení indikuje, co je v prohlížeči nastavené na Odeslat nebo zděděné z podkladového operačního systému.</span><span class="sxs-lookup"><span data-stu-id="54cbe-529">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="54cbe-530">Hlavička protokolu HTTP Accept-Language z požadavku prohlížeče není infallible způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="54cbe-530">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="54cbe-531">Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu kultury.</span><span class="sxs-lookup"><span data-stu-id="54cbe-531">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="54cbe-532">Nastavení hlavičky protokolu HTTP Accept-Language v IE</span><span class="sxs-lookup"><span data-stu-id="54cbe-532">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="54cbe-533">Z ikony ozubeného kolečka klepněte na **Možnosti Internetu**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-533">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="54cbe-534">Klepněte na **jazyky**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-534">Tap **Languages**.</span></span>

   ![Možnosti Internetu](localization/_static/lang.png)

1. <span data-ttu-id="54cbe-536">Klepněte na **nastavit jazykové předvolby**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-536">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="54cbe-537">Klepněte na **Přidat jazyk**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-537">Tap **Add a language**.</span></span>

1. <span data-ttu-id="54cbe-538">Přidejte jazyk.</span><span class="sxs-lookup"><span data-stu-id="54cbe-538">Add the language.</span></span>

1. <span data-ttu-id="54cbe-539">Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-539">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="54cbe-540">Použití vlastního zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="54cbe-540">Use a custom provider</span></span>

<span data-ttu-id="54cbe-541">Předpokládejme, že chcete, aby vaši zákazníci mohli ukládat svůj jazyk a jazykovou verzi do databází.</span><span class="sxs-lookup"><span data-stu-id="54cbe-541">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="54cbe-542">Můžete napsat poskytovatele a vyhledat tyto hodnoty pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="54cbe-542">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="54cbe-543">Následující kód ukazuje, jak přidat vlastního zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="54cbe-543">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="54cbe-544">Použijte `RequestLocalizationOptions` k přidání nebo odebrání poskytovatelů lokalizace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-544">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="54cbe-545">Programové nastavení kultury</span><span class="sxs-lookup"><span data-stu-id="54cbe-545">Set the culture programmatically</span></span>

<span data-ttu-id="54cbe-546">Tento ukázkový projekt **Localization. StarterWeb** na [GITHUBU](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-546">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="54cbe-547">Soubor *views/Shared/_SelectLanguagePartial. cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných jazykových verzí:</span><span class="sxs-lookup"><span data-stu-id="54cbe-547">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="54cbe-548">Soubor *views/Shared/_SelectLanguagePartial. cshtml* se přidá do `footer` oddílu souboru rozložení, takže bude k dispozici pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="54cbe-548">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="54cbe-549">`SetLanguage`Metoda nastaví jazykovou verzi cookie .</span><span class="sxs-lookup"><span data-stu-id="54cbe-549">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="54cbe-550">Nemůžete připojit *_SelectLanguagePartial. cshtml* k ukázkovému kódu pro tento projekt.</span><span class="sxs-lookup"><span data-stu-id="54cbe-550">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="54cbe-551">Projekt **Localization. StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) obsahuje kód pro tok `RequestLocalizationOptions` do Razor částečného toku prostřednictvím kontejneru pro [vkládání závislostí](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="54cbe-551">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="54cbe-552">Vazba modelu data směrování a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="54cbe-552">Model binding route data and query strings</span></span>

<span data-ttu-id="54cbe-553">Podívejte [se na téma chování globalizace modelu vazby dat trasy a řetězce dotazu](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="54cbe-553">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="54cbe-554">Výrazy globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="54cbe-554">Globalization and localization terms</span></span>

<span data-ttu-id="54cbe-555">Proces lokalizace vaší aplikace také vyžaduje základní znalosti relevantních znakových sad, které se běžně používají při vývoji moderního softwaru, a porozumění problémům, které s nimi souvisejí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-555">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="54cbe-556">I když všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text s různými čísly.</span><span class="sxs-lookup"><span data-stu-id="54cbe-556">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="54cbe-557">Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-557">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="54cbe-558">[Lokalizace](/dotnet/standard/globalization-localization/localizability-review) je přechodný proces pro ověření, že globální aplikace je připravená na lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="54cbe-558">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="54cbe-559">Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název jazykové verze je `<languagecode2>-<country/regioncode2>` , kde `<languagecode2>` je kód jazyka a `<country/regioncode2>` je kód subkultury.</span><span class="sxs-lookup"><span data-stu-id="54cbe-559">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="54cbe-560">Například `es-CL` pro španělštinu (Chile), `en-US` pro angličtinu (USA) a `en-AU` pro angličtinu (Austrálie).</span><span class="sxs-lookup"><span data-stu-id="54cbe-560">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="54cbe-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinací kódu kultury ISO 639 2 s malým písmenem, který je přidružený k jazyku, a kódu subkultury na velká písmena ISO 3166 2, který je přidružený k zemi nebo oblasti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="54cbe-562">Viz [název jazykové verze](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="54cbe-562">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="54cbe-563">Mezinárodní využití se často zkracuje na "I18N".</span><span class="sxs-lookup"><span data-stu-id="54cbe-563">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="54cbe-564">Zkratka používá první a poslední písmena a počet písmen mezi nimi, takže 18 představuje počet písmen mezi první I a poslední znak "N".</span><span class="sxs-lookup"><span data-stu-id="54cbe-564">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="54cbe-565">Totéž platí pro globalizaci (G11N) a lokalizaci (L10N).</span><span class="sxs-lookup"><span data-stu-id="54cbe-565">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="54cbe-566">Uvedenými</span><span class="sxs-lookup"><span data-stu-id="54cbe-566">Terms:</span></span>

* <span data-ttu-id="54cbe-567">Globalizace (G11N): proces vytvoření aplikace, která podporuje různé jazyky a oblasti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-567">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="54cbe-568">Lokalizace (L10N): proces přizpůsobení aplikace pro daný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-568">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="54cbe-569">Mezinárodní (I18N): popisuje globalizaci a lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="54cbe-569">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="54cbe-570">Jazyková verze: Jedná se o jazyk a volitelně i oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-570">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="54cbe-571">Neutrální jazyková verze: jazyková verze, která má zadaný jazyk, ale ne oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-571">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="54cbe-572">(například "en", "ES")</span><span class="sxs-lookup"><span data-stu-id="54cbe-572">(for example "en", "es")</span></span>
* <span data-ttu-id="54cbe-573">Specifická jazyková verze: jazyková verze, která má zadaný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-573">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="54cbe-574">(například "en-US", "en-GB", "ES-CL")</span><span class="sxs-lookup"><span data-stu-id="54cbe-574">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="54cbe-575">Nadřazená jazyková verze: neutrální jazyková verze, která obsahuje konkrétní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-575">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="54cbe-576">(například "en" je nadřazená jazyková verze "en-US" a "en-GB")</span><span class="sxs-lookup"><span data-stu-id="54cbe-576">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="54cbe-577">Národní prostředí: národní prostředí je stejné jako jazyková verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-577">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="54cbe-578">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="54cbe-578">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="54cbe-579">[StarterWeb projekt Localization](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) , který se používá v článku.</span><span class="sxs-lookup"><span data-stu-id="54cbe-579">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="54cbe-580">Globalizace a lokalizace aplikací .NET</span><span class="sxs-lookup"><span data-stu-id="54cbe-580">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="54cbe-581">Prostředky v souborech. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-581">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="54cbe-582">Sada nástrojů pro vícejazyčné aplikace od Microsoftu</span><span class="sxs-lookup"><span data-stu-id="54cbe-582">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="54cbe-583">Lokalizace & – obecné typy</span><span class="sxs-lookup"><span data-stu-id="54cbe-583">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="54cbe-584">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)a [Hisham bin](https://twitter.com/hishambinateya) Ateya</span><span class="sxs-lookup"><span data-stu-id="54cbe-584">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="54cbe-585">Vícejazyčný web umožňuje webu oslovit širší cílovou skupinu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-585">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="54cbe-586">ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.</span><span class="sxs-lookup"><span data-stu-id="54cbe-586">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="54cbe-587">Mezinárodní využití zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="54cbe-587">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="54cbe-588">Globalizace je proces návrhu aplikací, které podporují různé jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-588">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="54cbe-589">Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují na konkrétní geografické oblasti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-589">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="54cbe-590">Lokalizace je proces přizpůsobení globální aplikace, kterou jste již zpracovali pro lokalizaci, do konkrétní jazykové verze nebo národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-590">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="54cbe-591">Další informace najdete v tématu **předpoklady globalizace a lokalizace** poblíž konce tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-591">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="54cbe-592">Lokalizace aplikace zahrnuje následující:</span><span class="sxs-lookup"><span data-stu-id="54cbe-592">App localization involves the following:</span></span>

1. <span data-ttu-id="54cbe-593">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="54cbe-593">Make the app's content localizable</span></span>
1. <span data-ttu-id="54cbe-594">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="54cbe-594">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="54cbe-595">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="54cbe-595">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="54cbe-596">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54cbe-596">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="54cbe-597">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="54cbe-597">Make the app's content localizable</span></span>

<span data-ttu-id="54cbe-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer>a <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> bylo navrženo pro zlepšení produktivity při vývoji lokalizovaných aplikací.</span><span class="sxs-lookup"><span data-stu-id="54cbe-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="54cbe-599">`IStringLocalizer`používá <xref:System.Resources.ResourceManager> a <xref:System.Resources.ResourceReader> k poskytnutí prostředků specifických pro jazykovou verzi v době běhu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-599">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="54cbe-600">Rozhraní má indexer a `IEnumerable` pro vracení lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-600">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="54cbe-601">`IStringLocalizer`nevyžaduje uložení výchozích řetězců jazyka v souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-601">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="54cbe-602">Můžete vyvíjet aplikaci zaměřenou na lokalizaci a nemusíte vytvářet soubory prostředků na začátku ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="54cbe-602">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="54cbe-603">Následující kód ukazuje, jak zabalit řetězec "About title" pro lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="54cbe-603">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="54cbe-604">V předchozím kódu `IStringLocalizer<T>` implementace pochází z [Injektáže závislosti](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="54cbe-604">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="54cbe-605">Pokud se nenalezne lokalizovaná hodnota "o titulku", vrátí se klíč indexeru, tj. řetězec "About title".</span><span class="sxs-lookup"><span data-stu-id="54cbe-605">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="54cbe-606">Můžete ponechat výchozí řetězcové literály v aplikaci a zabalit je do lokalizátora, abyste se mohli soustředit na vývoj aplikace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-606">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="54cbe-607">Vyvíjíte svou aplikaci pomocí výchozího jazyka a připravíte ji pro krok lokalizace bez prvotního vytvoření výchozího souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-607">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="54cbe-608">Alternativně můžete použít tradiční přístup a zadat klíč pro načtení výchozího řetězce jazyka.</span><span class="sxs-lookup"><span data-stu-id="54cbe-608">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="54cbe-609">Pro mnoho vývojářů nový pracovní postup, který nemá výchozí jazyk *. resx* soubor, a jednoduše zabalí řetězcové literály může snížit režii lokalizace aplikace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-609">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="54cbe-610">Ostatní vývojáři budou preferovat tradiční pracovní postup, protože mohou usnadnit práci s delšími řetězcovými literály a usnadňují aktualizaci lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-610">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="54cbe-611">Použijte `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují kód HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-611">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="54cbe-612">`IHtmlLocalizer`HTML kóduje argumenty, které jsou formátovány v řetězci prostředků, ale nekóduje kód HTML samotný řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="54cbe-612">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="54cbe-613">V ukázce zvýrazněné níže je pouze hodnota `name` parametru kódována HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-613">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="54cbe-614">Obecně pouze lokalizovat text, ne HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-614">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="54cbe-615">Na nejnižší úrovni můžete obdržet zástupné `IStringLocalizerFactory` [vkládání závislostí](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="54cbe-615">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="54cbe-616">Výše uvedený kód ukazuje každou ze dvou metod Create Factory.</span><span class="sxs-lookup"><span data-stu-id="54cbe-616">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="54cbe-617">Lokalizované řetězce můžete rozdělit do oddílů podle řadiče, oblasti nebo pouze jednoho kontejneru.</span><span class="sxs-lookup"><span data-stu-id="54cbe-617">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="54cbe-618">V ukázkové aplikaci se `SharedResource` pro sdílené prostředky používá fiktivní třída s názvem.</span><span class="sxs-lookup"><span data-stu-id="54cbe-618">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="54cbe-619">Někteří vývojáři používají `Startup` třídu pro zahrnutí globálních nebo sdílených řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-619">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="54cbe-620">V níže uvedené ukázce se `InfoController` `SharedResource` používají a Localize:</span><span class="sxs-lookup"><span data-stu-id="54cbe-620">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="54cbe-621">Lokalizace zobrazení</span><span class="sxs-lookup"><span data-stu-id="54cbe-621">View localization</span></span>

<span data-ttu-id="54cbe-622">`IViewLocalizer`Služba poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="54cbe-622">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="54cbe-623">`ViewLocalizer`Třída implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-623">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="54cbe-624">Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-624">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="54cbe-625">Výchozí implementace nástroje `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-625">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="54cbe-626">Neexistuje možnost použít globální sdílený soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-626">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="54cbe-627">`ViewLocalizer`implementuje lokalizátora pomocí `IHtmlLocalizer` , takže Razor nekóduje kód HTML jako lokalizovaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="54cbe-627">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="54cbe-628">Můžete parametrizovat řetězce prostředků a `IViewLocalizer` zakódovat parametry HTML, ale ne řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="54cbe-628">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="54cbe-629">Vezměte v úvahu následující Razor značky:</span><span class="sxs-lookup"><span data-stu-id="54cbe-629">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="54cbe-630">Soubor prostředků francouzštiny může obsahovat následující:</span><span class="sxs-lookup"><span data-stu-id="54cbe-630">A French resource file could contain the following:</span></span>

| <span data-ttu-id="54cbe-631">Klíč</span><span class="sxs-lookup"><span data-stu-id="54cbe-631">Key</span></span> | <span data-ttu-id="54cbe-632">Hodnota</span><span class="sxs-lookup"><span data-stu-id="54cbe-632">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="54cbe-633">Vykreslené zobrazení obsahuje značku HTML ze souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-633">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="54cbe-634">Obecně pouze lokalizovat text, ne HTML.</span><span class="sxs-lookup"><span data-stu-id="54cbe-634">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="54cbe-635">Chcete-li použít sdílený soubor prostředků v zobrazení, vložení `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-635">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="54cbe-636">Lokalizace DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="54cbe-636">DataAnnotations localization</span></span>

<span data-ttu-id="54cbe-637">Chybové zprávy pro dataanotace jsou lokalizovány pomocí `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-637">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="54cbe-638">Pomocí možnosti `ResourcesPath = "Resources"` mohou být chybové zprávy v nástroji `RegisterViewModel` uloženy v jedné z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="54cbe-638">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="54cbe-639">*Resources/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-639">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="54cbe-640">*Prostředky/ViewModels/účet/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="54cbe-641">V ASP.NET Core MVC 1.1.0 a vyšší jsou lokalizovány neověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="54cbe-641">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="54cbe-642">ASP.NET Core MVC 1,0 **nehledá lokalizované** řetězce pro atributy, které nejsou ověřovány.</span><span class="sxs-lookup"><span data-stu-id="54cbe-642">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="54cbe-643">Použití jednoho řetězce prostředku pro více tříd</span><span class="sxs-lookup"><span data-stu-id="54cbe-643">Using one resource string for multiple classes</span></span>

<span data-ttu-id="54cbe-644">Následující kód ukazuje, jak použít jeden řetězec prostředku pro atributy ověřování s více třídami:</span><span class="sxs-lookup"><span data-stu-id="54cbe-644">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="54cbe-645">V předchozím kódu `SharedResource` je třída odpovídající RESX, kde jsou uloženy ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="54cbe-645">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="54cbe-646">S tímto přístupem budou dataanotace používat pouze `SharedResource` místo prostředků pro každou třídu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-646">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="54cbe-647">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="54cbe-647">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="54cbe-648">SupportedCultures a SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="54cbe-648">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="54cbe-649">ASP.NET Core umožňuje zadat dvě hodnoty jazykové verze `SupportedCultures` a `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-649">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="54cbe-650">Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) pro `SupportedCultures` určuje výsledky funkcí závislých na jazykové verzi, jako je datum, čas, číslo a formátování měny.</span><span class="sxs-lookup"><span data-stu-id="54cbe-650">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="54cbe-651">`SupportedCultures`Určuje také pořadí řazení textu, konvencí velikosti písmen a porovnávání řetězců.</span><span class="sxs-lookup"><span data-stu-id="54cbe-651">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="54cbe-652">Další informace o tom, jak server získá jazykovou verzi, naleznete v tématu [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="54cbe-652">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="54cbe-653">`SupportedUICultures`Určuje, které přeložené řetězce (ze souborů *. resx* ) jsou vyhledány správcem [prostředků](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="54cbe-653">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="54cbe-654">`ResourceManager`Jednoduše vyhledá řetězce specifické pro jazykovou verzi, které určuje `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-654">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="54cbe-655">Každé vlákno v rozhraní .NET má `CurrentCulture` a `CurrentUICulture` objekty.</span><span class="sxs-lookup"><span data-stu-id="54cbe-655">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="54cbe-656">ASP.NET Core tyto hodnoty kontroluje při vykreslování funkcí závislých na jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-656">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="54cbe-657">Pokud je například jazyková verze aktuálního vlákna nastavena na "en-US" (angličtina, USA), `DateTime.Now.ToLongDateString()` zobrazí "čtvrtek, 18. února 2016", ale pokud `CurrentCulture` je nastavená na "ES-ES" (španělština, Španělsko), bude výstup "Jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="54cbe-657">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="54cbe-658">Soubory prostředků</span><span class="sxs-lookup"><span data-stu-id="54cbe-658">Resource files</span></span>

<span data-ttu-id="54cbe-659">Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců z kódu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-659">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="54cbe-660">Přeložené řetězce pro jazyk, který není výchozí, jsou izolované v souborech prostředků *. resx* .</span><span class="sxs-lookup"><span data-stu-id="54cbe-660">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="54cbe-661">Například může být vhodné vytvořit soubor prostředků španělštiny s názvem *Welcome. ES. resx* obsahující přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="54cbe-661">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="54cbe-662">"ES" je kód jazyka pro španělštinu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-662">"es" is the language code for Spanish.</span></span> <span data-ttu-id="54cbe-663">Postup vytvoření tohoto souboru prostředků v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="54cbe-663">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="54cbe-664">V **Průzkumník řešení**klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-664">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![Vnořená kontextová nabídka: v Průzkumník řešení je místní nabídka pro prostředky otevřená.](localization/_static/newi.png)

1. <span data-ttu-id="54cbe-667">V poli **Vyhledat nainstalované šablony** zadejte "Resource" a soubor pojmenujte.</span><span class="sxs-lookup"><span data-stu-id="54cbe-667">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![Dialogové okno Přidat novou položku](localization/_static/res.png)

1. <span data-ttu-id="54cbe-669">Do sloupce **název** zadejte hodnotu klíče (nativní řetězec) a přeložený řetězec ve sloupci Value ( **hodnota** ).</span><span class="sxs-lookup"><span data-stu-id="54cbe-669">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome. ES. resx soubor (soubor prostředků Welcome pro španělštinu) se slovem Hello ve sloupci název a slovo Hola (text Hello v španělštině) ve sloupci Hodnota](localization/_static/hola.png)

   <span data-ttu-id="54cbe-671">Sada Visual Studio zobrazí *uvítací soubor. ES. resx* .</span><span class="sxs-lookup"><span data-stu-id="54cbe-671">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![Průzkumník řešení se zobrazuje soubor prostředků nástroje Welcome španělština (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="54cbe-673">Pojmenovávání souborů prostředků</span><span class="sxs-lookup"><span data-stu-id="54cbe-673">Resource file naming</span></span>

<span data-ttu-id="54cbe-674">Prostředky jsou pojmenovány pro úplný název typu své třídy minus název sestavení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-674">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="54cbe-675">Například francouzská prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu, `LocalizationWebsite.Web.Startup` by měla být pojmenována *Startup. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-675">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="54cbe-676">Prostředek pro třídu by měl `LocalizationWebsite.Web.Controllers.HomeController` pojmenovat *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-676">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-677">Pokud cílový obor názvů třídy není stejný jako název sestavení, budete potřebovat úplný název typu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-677">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="54cbe-678">Například v ukázkovém projektu by měl prostředek pro typ `ExtraNamespace.Tools` název *ExtraNamespace. Tools. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-678">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="54cbe-679">V ukázkovém projektu `ConfigureServices` Metoda nastaví `ResourcesPath` na "prostředky", takže relativní cesta projektu pro francouzský soubor prostředků domovského řadiče je *Resources/Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-679">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-680">Případně můžete použít složky k uspořádání souborů prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-680">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="54cbe-681">V případě domovského kontroleru by tato cesta byla *Resources/Controllers/HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-681">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-682">Pokud tuto možnost nepoužijete `ResourcesPath` , soubor *. resx* by přešel do základního adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-682">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="54cbe-683">Soubor prostředků pro `HomeController` by měl mít název *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="54cbe-683">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="54cbe-684">Volba použití konvence pojmenování teček nebo Path závisí na tom, jak chcete uspořádat soubory prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-684">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="54cbe-685">Název prostředku</span><span class="sxs-lookup"><span data-stu-id="54cbe-685">Resource name</span></span> | <span data-ttu-id="54cbe-686">Pojmenování teček nebo Path</span><span class="sxs-lookup"><span data-stu-id="54cbe-686">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="54cbe-687">Prostředky/řadiče. HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-687">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="54cbe-688">Tečka</span><span class="sxs-lookup"><span data-stu-id="54cbe-688">Dot</span></span>  |
| <span data-ttu-id="54cbe-689">Prostředky/řadiče/HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-689">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="54cbe-690">Cesta</span><span class="sxs-lookup"><span data-stu-id="54cbe-690">Path</span></span> |

<span data-ttu-id="54cbe-691">Soubory prostředků používané `@inject IViewLocalizer` v Razor zobrazeních následují podobně jako vzor.</span><span class="sxs-lookup"><span data-stu-id="54cbe-691">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="54cbe-692">Soubor prostředků pro zobrazení může být pojmenován buď pomocí názvu tečky, nebo pojmenování cesty.</span><span class="sxs-lookup"><span data-stu-id="54cbe-692">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="54cbe-693">Razorzobrazení soubory prostředků napodobá cestě k jejich přidruženému souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-693">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="54cbe-694">Za předpokladu, že nastavíme `ResourcesPath` "prostředky", soubor francouzského prostředku přidružený k zobrazením */domů/o zobrazení. cshtml* může být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="54cbe-694">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="54cbe-695">Prostředky/zobrazení/domů/o. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-695">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="54cbe-696">Prostředky/zobrazení. domů. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-696">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="54cbe-697">Pokud tuto možnost nepoužijete `ResourcesPath` , bude soubor *. resx* pro zobrazení umístěn ve stejné složce jako zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-697">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="54cbe-698">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="54cbe-698">RootNamespaceAttribute</span></span> 

<span data-ttu-id="54cbe-699">Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-699">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="54cbe-700">K tomu může dojít, když název projektu není platným identifikátorem .NET.</span><span class="sxs-lookup"><span data-stu-id="54cbe-700">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="54cbe-701">V případě instance `my-project-name.csproj` bude použit kořenový obor názvů `my_project_name` a název sestavení, `my-project-name` který vede k této chybě.</span><span class="sxs-lookup"><span data-stu-id="54cbe-701">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="54cbe-702">Pokud se kořenový obor názvů sestavení liší od názvu sestavení:</span><span class="sxs-lookup"><span data-stu-id="54cbe-702">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="54cbe-703">Lokalizace ve výchozím nastavení nefunguje.</span><span class="sxs-lookup"><span data-stu-id="54cbe-703">Localization does not work by default.</span></span>
* <span data-ttu-id="54cbe-704">Lokalizace se nezdařila z důvodu způsobu hledání prostředků v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-704">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="54cbe-705">`RootNamespace`je hodnota doby sestavení, která není k dispozici pro spuštěný proces.</span><span class="sxs-lookup"><span data-stu-id="54cbe-705">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="54cbe-706">Pokud se `RootNamespace` liší od z `AssemblyName` , zahrňte následující v *AssemblyInfo.cs* (s hodnotami parametrů nahrazenými skutečnými hodnotami):</span><span class="sxs-lookup"><span data-stu-id="54cbe-706">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="54cbe-707">Předchozí kód umožňuje úspěšné vyřešení souborů RESX.</span><span class="sxs-lookup"><span data-stu-id="54cbe-707">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="54cbe-708">Chování záložního kultivačního prostředí</span><span class="sxs-lookup"><span data-stu-id="54cbe-708">Culture fallback behavior</span></span>

<span data-ttu-id="54cbe-709">Při hledání prostředku se lokalizace zapojit do "záložní kultury".</span><span class="sxs-lookup"><span data-stu-id="54cbe-709">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="54cbe-710">Od požadované jazykové verze, pokud se nenajde, se vrátí k nadřazené jazykové verzi této jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-710">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="54cbe-711">Kromě toho, vlastnost [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) představuje nadřazenou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-711">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="54cbe-712">To je obvykle (ale ne vždy) znamená odebrání národního přízpůsobu z ISO.</span><span class="sxs-lookup"><span data-stu-id="54cbe-712">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="54cbe-713">Například dialekt španělštiny ve španělštině, který se používá v Mexiku, je ES-MX.</span><span class="sxs-lookup"><span data-stu-id="54cbe-713">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="54cbe-714">Má nadřízenou španělštinu ES, která &mdash; není specifická pro žádnou zemi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-714">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="54cbe-715">Představte si, že váš web obdrží žádost o "úvodní" prostředek pomocí kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="54cbe-715">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="54cbe-716">Systém lokalizace vyhledá následující prostředky v uvedeném pořadí a vybere první shodu:</span><span class="sxs-lookup"><span data-stu-id="54cbe-716">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="54cbe-717">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-717">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="54cbe-718">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="54cbe-718">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="54cbe-719">*Welcome. resx* (Pokud `NeutralResourcesLanguage` je to "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="54cbe-719">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="54cbe-720">Pokud například odeberete označení jazykové verze ". fr" a máte nastavenou jazykovou verzi na francouzštinu, je výchozí soubor prostředků přečten a jsou lokalizovány řetězce.</span><span class="sxs-lookup"><span data-stu-id="54cbe-720">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="54cbe-721">Správce prostředků určí výchozí nebo záložní prostředek, pokud nic nevyhovuje vaší požadované jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-721">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="54cbe-722">Pokud chcete vrátit klíč pouze v případě, že chybí prostředek pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-722">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="54cbe-723">Generování souborů prostředků pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="54cbe-723">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="54cbe-724">Vytvoříte-li soubor prostředků v aplikaci Visual Studio bez jazykové verze v názvu souboru (například *Welcome. resx*), sada Visual Studio vytvoří třídu jazyka C# s vlastností pro každý řetězec.</span><span class="sxs-lookup"><span data-stu-id="54cbe-724">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="54cbe-725">To obvykle není to, co chcete s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="54cbe-725">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="54cbe-726">Obvykle nemáte výchozí soubor prostředků *. resx* (soubor *. resx* bez názvu jazykové verze).</span><span class="sxs-lookup"><span data-stu-id="54cbe-726">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="54cbe-727">Návrh souboru *. resx* doporučujeme vytvořit s názvem jazykové verze (například *Welcome. fr. resx*).</span><span class="sxs-lookup"><span data-stu-id="54cbe-727">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="54cbe-728">Při vytváření souboru *. resx* s názvem jazykové verze aplikace Visual Studio negeneruje soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="54cbe-728">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="54cbe-729">Přidat další jazykové verze</span><span class="sxs-lookup"><span data-stu-id="54cbe-729">Add other cultures</span></span>

<span data-ttu-id="54cbe-730">Každá kombinace jazyka a jazykové verze (Kromě výchozího jazyka) vyžaduje jedinečný soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="54cbe-730">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="54cbe-731">Soubory prostředků můžete vytvořit pro různé kultury a národní prostředí vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-US**, **fr-CA**a **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="54cbe-731">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="54cbe-732">Tyto kódy ISO jsou umístěné mezi názvem souboru a příponou souboru *. resx* , jako v *Welcome.ES-MX. resx* (španělština/Mexiko).</span><span class="sxs-lookup"><span data-stu-id="54cbe-732">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="54cbe-733">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="54cbe-733">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="54cbe-734">Konfigurace lokalizace</span><span class="sxs-lookup"><span data-stu-id="54cbe-734">Configure localization</span></span>

<span data-ttu-id="54cbe-735">Lokalizace je nakonfigurovaná v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="54cbe-735">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="54cbe-736">`AddLocalization`Přidá služby lokalizace do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="54cbe-736">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="54cbe-737">Výše uvedený kód také nastaví cestu prostředků na prostředky.</span><span class="sxs-lookup"><span data-stu-id="54cbe-737">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="54cbe-738">`AddViewLocalization`Přidá podporu pro lokalizované soubory zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-738">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="54cbe-739">V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="54cbe-739">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="54cbe-740">Například "fr" v souboru *index. fr. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="54cbe-740">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="54cbe-741">`AddDataAnnotationsLocalization`přidává podporu pro lokalizované `DataAnnotations` ověřovací zprávy prostřednictvím `IStringLocalizer` abstrakcí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-741">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="54cbe-742">Middleware lokalizace</span><span class="sxs-lookup"><span data-stu-id="54cbe-742">Localization middleware</span></span>

<span data-ttu-id="54cbe-743">Aktuální jazyková verze v požadavku je nastavena v [middleware](xref:fundamentals/middleware/index)lokalizace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-743">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="54cbe-744">Middleware lokalizace je povolená v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="54cbe-744">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="54cbe-745">Middleware lokalizace musí být konfigurovány před jakýmkoli middlewarem, který by mohl kontrolovat jazykovou verzi žádosti (například `app.UseMvcWithDefaultRoute()` ).</span><span class="sxs-lookup"><span data-stu-id="54cbe-745">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="54cbe-746">`UseRequestLocalization`Inicializuje `RequestLocalizationOptions` objekt.</span><span class="sxs-lookup"><span data-stu-id="54cbe-746">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="54cbe-747">Při každém požadavku se vypíše seznam `RequestCultureProvider` ve `RequestLocalizationOptions` výčtu a první zprostředkovatel, který dokáže úspěšně určit jazykovou verzi žádosti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-747">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="54cbe-748">Výchozí zprostředkovatelé přicházejí z `RequestLocalizationOptions` třídy:</span><span class="sxs-lookup"><span data-stu-id="54cbe-748">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="54cbe-749">Výchozí seznam bude z nejpřesnější, aby byl nejméně specifický.</span><span class="sxs-lookup"><span data-stu-id="54cbe-749">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="54cbe-750">Později v článku se dozvíte, jak můžete změnit pořadí a dokonce přidat vlastního poskytovatele jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-750">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="54cbe-751">Pokud žádný z poskytovatelů nemůže určit jazykovou verzi žádosti, `DefaultRequestCulture` je použita.</span><span class="sxs-lookup"><span data-stu-id="54cbe-751">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="54cbe-752">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="54cbe-752">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="54cbe-753">Některé aplikace budou používat řetězec dotazu k nastavení jazykové verze [a jazykové verze uživatelského rozhraní](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="54cbe-753">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="54cbe-754">Pro aplikace, které používají cookie přístup k hlavičkám nebo Accept-Language, je přidání řetězce dotazu na adresu URL užitečné pro ladění a testování kódu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-754">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="54cbe-755">Ve výchozím nastavení `QueryStringRequestCultureProvider` je jako první poskytovatel lokalizace v `RequestCultureProvider` seznamu zaregistrován.</span><span class="sxs-lookup"><span data-stu-id="54cbe-755">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="54cbe-756">Předáte parametry řetězce dotazu `culture` a `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-756">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="54cbe-757">Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělština/Mexiko:</span><span class="sxs-lookup"><span data-stu-id="54cbe-757">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="54cbe-758">Pokud předáte pouze jeden z těchto dvou ( `culture` nebo `ui-culture` ), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí toho, kterou jste předali.</span><span class="sxs-lookup"><span data-stu-id="54cbe-758">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="54cbe-759">Například nastavení pouze jazyková verze nastaví jak `Culture` a `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-759">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a><span data-ttu-id="54cbe-760">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="54cbe-760">CookieRequestCultureProvider</span></span>

<span data-ttu-id="54cbe-761">Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze s ASP.NET Coreovou kulturou cookie .</span><span class="sxs-lookup"><span data-stu-id="54cbe-761">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="54cbe-762">Použijte `MakeCookieValue` metodu k vytvoření cookie .</span><span class="sxs-lookup"><span data-stu-id="54cbe-762">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="54cbe-763">`CookieRequestCultureProvider` `DefaultCookieName` Vrátí výchozí název, který cookie slouží ke sledování informací o preferované jazykové verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="54cbe-763">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="54cbe-764">Výchozí cookie název je `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-764">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="54cbe-765">cookieFormát je `c=%LANGCODE%|uic=%LANGCODE%` , kde `c` je `Culture` a je například `uic` `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="54cbe-765">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="54cbe-766">Pokud zadáte pouze jednu z informací o jazykové verzi a jazykovou verzi uživatelského rozhraní, zadaná jazyková verze bude použita pro informace o jazykové verzi i jazyková verze uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="54cbe-766">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="54cbe-767">Hlavička protokolu HTTP pro přijetí – jazyk</span><span class="sxs-lookup"><span data-stu-id="54cbe-767">The Accept-Language HTTP header</span></span>

<span data-ttu-id="54cbe-768">[Záhlaví Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) lze nastavit ve většině prohlížečů a původně bylo určeno pro určení jazyka uživatele.</span><span class="sxs-lookup"><span data-stu-id="54cbe-768">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="54cbe-769">Toto nastavení indikuje, co je v prohlížeči nastavené na Odeslat nebo zděděné z podkladového operačního systému.</span><span class="sxs-lookup"><span data-stu-id="54cbe-769">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="54cbe-770">Hlavička protokolu HTTP Accept-Language z požadavku prohlížeče není infallible způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="54cbe-770">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="54cbe-771">Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu kultury.</span><span class="sxs-lookup"><span data-stu-id="54cbe-771">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="54cbe-772">Nastavení hlavičky protokolu HTTP Accept-Language v IE</span><span class="sxs-lookup"><span data-stu-id="54cbe-772">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="54cbe-773">Z ikony ozubeného kolečka klepněte na **Možnosti Internetu**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-773">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="54cbe-774">Klepněte na **jazyky**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-774">Tap **Languages**.</span></span>

   ![Možnosti Internetu](localization/_static/lang.png)

1. <span data-ttu-id="54cbe-776">Klepněte na **nastavit jazykové předvolby**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-776">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="54cbe-777">Klepněte na **Přidat jazyk**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-777">Tap **Add a language**.</span></span>

1. <span data-ttu-id="54cbe-778">Přidejte jazyk.</span><span class="sxs-lookup"><span data-stu-id="54cbe-778">Add the language.</span></span>

1. <span data-ttu-id="54cbe-779">Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.</span><span class="sxs-lookup"><span data-stu-id="54cbe-779">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="54cbe-780">Hlavička protokolu HTTP v jazykovém obsahu</span><span class="sxs-lookup"><span data-stu-id="54cbe-780">The Content-Language HTTP header</span></span>

<span data-ttu-id="54cbe-781">Hlavička entity [jazyka obsahu](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :</span><span class="sxs-lookup"><span data-stu-id="54cbe-781">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

* <span data-ttu-id="54cbe-782">Slouží k popisu jazyků, které jsou určené pro cílovou skupinu.</span><span class="sxs-lookup"><span data-stu-id="54cbe-782">Is used to describe the language(s) intended for the audience.</span></span>
* <span data-ttu-id="54cbe-783">Umožňuje uživateli rozlišovat podle vlastního preferovaného jazyka uživatelů.</span><span class="sxs-lookup"><span data-stu-id="54cbe-783">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="54cbe-784">Záhlaví entit se používají v požadavcích HTTP i v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="54cbe-784">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="54cbe-785">`Content-Language`Záhlaví lze přidat nastavením vlastnosti `ApplyCurrentCultureToResponseHeaders` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-785">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="54cbe-786">Přidání `Content-Language` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="54cbe-786">Adding the `Content-Language` header:</span></span>

* <span data-ttu-id="54cbe-787">Umožňuje RequestLocalizationMiddleware nastavit `Content-Language` hlavičku pomocí `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-787">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
* <span data-ttu-id="54cbe-788">Eliminuje nutnost explicitně nastavit hlavičku odpovědi `Content-Language` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-788">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="54cbe-789">Použití vlastního zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="54cbe-789">Use a custom provider</span></span>

<span data-ttu-id="54cbe-790">Předpokládejme, že chcete, aby vaši zákazníci mohli ukládat svůj jazyk a jazykovou verzi do databází.</span><span class="sxs-lookup"><span data-stu-id="54cbe-790">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="54cbe-791">Můžete napsat poskytovatele a vyhledat tyto hodnoty pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="54cbe-791">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="54cbe-792">Následující kód ukazuje, jak přidat vlastního zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="54cbe-792">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="54cbe-793">Použijte `RequestLocalizationOptions` k přidání nebo odebrání poskytovatelů lokalizace.</span><span class="sxs-lookup"><span data-stu-id="54cbe-793">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="54cbe-794">Programové nastavení kultury</span><span class="sxs-lookup"><span data-stu-id="54cbe-794">Set the culture programmatically</span></span>

<span data-ttu-id="54cbe-795">Tento ukázkový projekt **Localization. StarterWeb** na [GITHUBU](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture` .</span><span class="sxs-lookup"><span data-stu-id="54cbe-795">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="54cbe-796">Soubor *views/Shared/_SelectLanguagePartial. cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných jazykových verzí:</span><span class="sxs-lookup"><span data-stu-id="54cbe-796">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="54cbe-797">Soubor *views/Shared/_SelectLanguagePartial. cshtml* se přidá do `footer` oddílu souboru rozložení, takže bude k dispozici pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="54cbe-797">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="54cbe-798">`SetLanguage`Metoda nastaví jazykovou verzi cookie .</span><span class="sxs-lookup"><span data-stu-id="54cbe-798">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="54cbe-799">Nemůžete připojit *_SelectLanguagePartial. cshtml* k ukázkovému kódu pro tento projekt.</span><span class="sxs-lookup"><span data-stu-id="54cbe-799">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="54cbe-800">Projekt **Localization. StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) obsahuje kód pro tok `RequestLocalizationOptions` do Razor částečného toku prostřednictvím kontejneru pro [vkládání závislostí](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="54cbe-800">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="54cbe-801">Vazba modelu data směrování a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="54cbe-801">Model binding route data and query strings</span></span>

<span data-ttu-id="54cbe-802">Podívejte [se na téma chování globalizace modelu vazby dat trasy a řetězce dotazu](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="54cbe-802">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="54cbe-803">Výrazy globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="54cbe-803">Globalization and localization terms</span></span>

<span data-ttu-id="54cbe-804">Proces lokalizace vaší aplikace také vyžaduje základní znalosti relevantních znakových sad, které se běžně používají při vývoji moderního softwaru, a porozumění problémům, které s nimi souvisejí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-804">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="54cbe-805">I když všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text s různými čísly.</span><span class="sxs-lookup"><span data-stu-id="54cbe-805">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="54cbe-806">Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.</span><span class="sxs-lookup"><span data-stu-id="54cbe-806">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="54cbe-807">[Lokalizace](/dotnet/standard/globalization-localization/localizability-review) je přechodný proces pro ověření, že globální aplikace je připravená na lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="54cbe-807">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="54cbe-808">Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název jazykové verze je `<languagecode2>-<country/regioncode2>` , kde `<languagecode2>` je kód jazyka a `<country/regioncode2>` je kód subkultury.</span><span class="sxs-lookup"><span data-stu-id="54cbe-808">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="54cbe-809">Například `es-CL` pro španělštinu (Chile), `en-US` pro angličtinu (USA) a `en-AU` pro angličtinu (Austrálie).</span><span class="sxs-lookup"><span data-stu-id="54cbe-809">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="54cbe-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinací kódu kultury ISO 639 2 s malým písmenem, který je přidružený k jazyku, a kódu subkultury na velká písmena ISO 3166 2, který je přidružený k zemi nebo oblasti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="54cbe-811">Viz [název jazykové verze](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="54cbe-811">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="54cbe-812">Mezinárodní využití se často zkracuje na "I18N".</span><span class="sxs-lookup"><span data-stu-id="54cbe-812">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="54cbe-813">Zkratka používá první a poslední písmena a počet písmen mezi nimi, takže 18 představuje počet písmen mezi první I a poslední znak "N".</span><span class="sxs-lookup"><span data-stu-id="54cbe-813">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="54cbe-814">Totéž platí pro globalizaci (G11N) a lokalizaci (L10N).</span><span class="sxs-lookup"><span data-stu-id="54cbe-814">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="54cbe-815">Uvedenými</span><span class="sxs-lookup"><span data-stu-id="54cbe-815">Terms:</span></span>

* <span data-ttu-id="54cbe-816">Globalizace (G11N): proces vytvoření aplikace, která podporuje různé jazyky a oblasti.</span><span class="sxs-lookup"><span data-stu-id="54cbe-816">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="54cbe-817">Lokalizace (L10N): proces přizpůsobení aplikace pro daný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-817">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="54cbe-818">Mezinárodní (I18N): popisuje globalizaci a lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="54cbe-818">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="54cbe-819">Jazyková verze: Jedná se o jazyk a volitelně i oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-819">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="54cbe-820">Neutrální jazyková verze: jazyková verze, která má zadaný jazyk, ale ne oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-820">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="54cbe-821">(například "en", "ES")</span><span class="sxs-lookup"><span data-stu-id="54cbe-821">(for example "en", "es")</span></span>
* <span data-ttu-id="54cbe-822">Specifická jazyková verze: jazyková verze, která má zadaný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="54cbe-822">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="54cbe-823">(například "en-US", "en-GB", "ES-CL")</span><span class="sxs-lookup"><span data-stu-id="54cbe-823">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="54cbe-824">Nadřazená jazyková verze: neutrální jazyková verze, která obsahuje konkrétní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="54cbe-824">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="54cbe-825">(například "en" je nadřazená jazyková verze "en-US" a "en-GB")</span><span class="sxs-lookup"><span data-stu-id="54cbe-825">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="54cbe-826">Národní prostředí: národní prostředí je stejné jako jazyková verze.</span><span class="sxs-lookup"><span data-stu-id="54cbe-826">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="54cbe-827">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="54cbe-827">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="54cbe-828">[StarterWeb projekt Localization](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) , který se používá v článku.</span><span class="sxs-lookup"><span data-stu-id="54cbe-828">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="54cbe-829">Globalizace a lokalizace aplikací .NET</span><span class="sxs-lookup"><span data-stu-id="54cbe-829">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="54cbe-830">Prostředky v souborech. resx</span><span class="sxs-lookup"><span data-stu-id="54cbe-830">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="54cbe-831">Sada nástrojů pro vícejazyčné aplikace od Microsoftu</span><span class="sxs-lookup"><span data-stu-id="54cbe-831">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="54cbe-832">Lokalizace & – obecné typy</span><span class="sxs-lookup"><span data-stu-id="54cbe-832">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

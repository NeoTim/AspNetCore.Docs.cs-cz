---
title: Globalizace a lokalizace v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core poskytuje služby a middleware pro lokalizaci obsahu do různých jazyků a kultur.
ms.author: riande
ms.date: 01/14/2017
uid: fundamentals/localization
ms.openlocfilehash: 0cf6e5d391242322aa4c7b1a0b6a20dd484c80f6
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896889"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="39772-103">Globalizace a lokalizace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39772-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="39772-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)a [Hisham bin](https://twitter.com/hishambinateya) Ateya</span><span class="sxs-lookup"><span data-stu-id="39772-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="39772-105">Vytvoření vícejazyčného webu pomocí ASP.NET Core umožní vašemu webu oslovit širší cílovou skupinu.</span><span class="sxs-lookup"><span data-stu-id="39772-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="39772-106">ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.</span><span class="sxs-lookup"><span data-stu-id="39772-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="39772-107">Mezinárodní využití zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="39772-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="39772-108">Globalizace je proces návrhu aplikací, které podporují různé jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="39772-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="39772-109">Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují na konkrétní geografické oblasti.</span><span class="sxs-lookup"><span data-stu-id="39772-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="39772-110">Lokalizace je proces přizpůsobení globální aplikace, kterou jste již zpracovali pro lokalizaci, do konkrétní jazykové verze nebo národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="39772-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="39772-111">Další informace najdete v tématu **předpoklady globalizace a lokalizace** poblíž konce tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="39772-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="39772-112">Lokalizace aplikace zahrnuje následující:</span><span class="sxs-lookup"><span data-stu-id="39772-112">App localization involves the following:</span></span>

1. <span data-ttu-id="39772-113">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="39772-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="39772-114">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="39772-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="39772-115">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="39772-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="39772-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="39772-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="39772-117">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="39772-117">Make the app's content localizable</span></span>

<span data-ttu-id="39772-118">Zavedeno v ASP.NET Core `IStringLocalizer` a `IStringLocalizer<T>` bylo navrženo pro zlepšení produktivity při vývoji lokalizovaných aplikací.</span><span class="sxs-lookup"><span data-stu-id="39772-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="39772-119">`IStringLocalizer` používá ke zpracování prostředků specifických pro jazykovou verzi v době běhu prostředky [ResourceManager](/dotnet/api/system.resources.resourcemanager) a [ResourceReader](/dotnet/api/system.resources.resourcereader) .</span><span class="sxs-lookup"><span data-stu-id="39772-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="39772-120">Jednoduché rozhraní má indexer a `IEnumerable` pro vracení lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="39772-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="39772-121">`IStringLocalizer` nevyžaduje uložení výchozích řetězců jazyka do souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="39772-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="39772-122">Můžete vyvíjet aplikaci zaměřenou na lokalizaci a nemusíte vytvářet soubory prostředků na začátku ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="39772-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="39772-123">Následující kód ukazuje, jak zabalit řetězec "About title" pro lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="39772-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="39772-124">Ve výše uvedeném kódu je implementace `IStringLocalizer<T>` ze [Injektáže závislosti](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="39772-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="39772-125">Pokud se nenalezne lokalizovaná hodnota "o titulku", vrátí se klíč indexeru, tj. řetězec "About title".</span><span class="sxs-lookup"><span data-stu-id="39772-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="39772-126">Můžete ponechat výchozí řetězcové literály v aplikaci a zabalit je do lokalizátora, abyste se mohli soustředit na vývoj aplikace.</span><span class="sxs-lookup"><span data-stu-id="39772-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="39772-127">Vyvíjíte svou aplikaci pomocí výchozího jazyka a připravíte ji pro krok lokalizace bez prvotního vytvoření výchozího souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="39772-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="39772-128">Alternativně můžete použít tradiční přístup a zadat klíč pro načtení výchozího řetězce jazyka.</span><span class="sxs-lookup"><span data-stu-id="39772-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="39772-129">Pro mnoho vývojářů nový pracovní postup, který nemá výchozí jazyk *. resx* soubor, a jednoduše zabalí řetězcové literály může snížit režii lokalizace aplikace.</span><span class="sxs-lookup"><span data-stu-id="39772-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="39772-130">Ostatní vývojáři budou preferovat tradiční pracovní postup, protože mohou usnadnit práci s delšími řetězcovými literály a usnadňují aktualizaci lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="39772-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="39772-131">Pro prostředky, které obsahují kód HTML, použijte implementaci `IHtmlLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="39772-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="39772-132">`IHtmlLocalizer` kódování HTML kóduje argumenty, které jsou formátovány v řetězci prostředků, ale nekóduje kód HTML samotný řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="39772-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="39772-133">V ukázce zvýrazněné níže je hodnota parametru `name` kódována jako HTML.</span><span class="sxs-lookup"><span data-stu-id="39772-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="39772-134">**Poznámka:** Obecně chcete lokalizovat pouze text a nikoli HTML.</span><span class="sxs-lookup"><span data-stu-id="39772-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="39772-135">Na nejnižší úrovni můžete získat `IStringLocalizerFactory` z [Injektáže závislosti](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="39772-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="39772-136">Výše uvedený kód ukazuje každou ze dvou metod Create Factory.</span><span class="sxs-lookup"><span data-stu-id="39772-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="39772-137">Lokalizované řetězce můžete rozdělit do oddílů podle řadiče, oblasti nebo pouze jednoho kontejneru.</span><span class="sxs-lookup"><span data-stu-id="39772-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="39772-138">V ukázkové aplikaci se pro sdílené prostředky používá fiktivní třída s názvem `SharedResource`.</span><span class="sxs-lookup"><span data-stu-id="39772-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="39772-139">Někteří vývojáři používají třídu `Startup` k zahrnutí globálních nebo sdílených řetězců.</span><span class="sxs-lookup"><span data-stu-id="39772-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="39772-140">V níže uvedené ukázce se používají `InfoController` a `SharedResource`.</span><span class="sxs-lookup"><span data-stu-id="39772-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="39772-141">Lokalizace zobrazení</span><span class="sxs-lookup"><span data-stu-id="39772-141">View localization</span></span>

<span data-ttu-id="39772-142">Služba `IViewLocalizer` poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="39772-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="39772-143">Třída `ViewLocalizer` implementuje toto rozhraní a nalezne umístění prostředku z cesty k souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="39772-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="39772-144">Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer`:</span><span class="sxs-lookup"><span data-stu-id="39772-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="39772-145">Výchozí implementace `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="39772-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="39772-146">Neexistuje možnost použít globální sdílený soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="39772-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="39772-147">`ViewLocalizer` implementuje lokalizátora pomocí `IHtmlLocalizer`, takže Razor nekóduje lokalizovaný řetězec ve formátu HTML.</span><span class="sxs-lookup"><span data-stu-id="39772-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="39772-148">Můžete parametrizovat řetězce prostředků a `IViewLocalizer`, budou zakódovány parametry HTML, ale ne řetězcem prostředku.</span><span class="sxs-lookup"><span data-stu-id="39772-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="39772-149">Vezměte v úvahu následující značky Razor:</span><span class="sxs-lookup"><span data-stu-id="39772-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="39772-150">Soubor prostředků francouzštiny může obsahovat následující:</span><span class="sxs-lookup"><span data-stu-id="39772-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="39772-151">Key</span><span class="sxs-lookup"><span data-stu-id="39772-151">Key</span></span> | <span data-ttu-id="39772-152">Hodnota</span><span class="sxs-lookup"><span data-stu-id="39772-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="39772-153">Vykreslené zobrazení obsahuje značku HTML ze souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="39772-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="39772-154">**Poznámka:** Obecně chcete lokalizovat pouze text a nikoli HTML.</span><span class="sxs-lookup"><span data-stu-id="39772-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="39772-155">Chcete-li použít sdílený soubor prostředků v zobrazení, zadejte `IHtmlLocalizer<T>`:</span><span class="sxs-lookup"><span data-stu-id="39772-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="39772-156">Lokalizace DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="39772-156">DataAnnotations localization</span></span>

<span data-ttu-id="39772-157">Chybové zprávy pro dataanotace jsou lokalizovány s `IStringLocalizer<T>`.</span><span class="sxs-lookup"><span data-stu-id="39772-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="39772-158">Když použijete možnost `ResourcesPath = "Resources"`, chybové zprávy v `RegisterViewModel` lze uložit na jednu z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="39772-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="39772-159">*Resources/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="39772-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="39772-160">*Prostředky/ViewModels/účet/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="39772-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="39772-161">V ASP.NET Core MVC 1.1.0 a vyšší jsou lokalizovány neověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="39772-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="39772-162">ASP.NET Core MVC 1,0 **nehledá lokalizované** řetězce pro atributy, které nejsou ověřovány.</span><span class="sxs-lookup"><span data-stu-id="39772-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="39772-163">Použití jednoho řetězce prostředku pro více tříd</span><span class="sxs-lookup"><span data-stu-id="39772-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="39772-164">Následující kód ukazuje, jak použít jeden řetězec prostředku pro atributy ověřování s více třídami:</span><span class="sxs-lookup"><span data-stu-id="39772-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="39772-165">V předchozím kódu je `SharedResource` třídou odpovídající RESX, kde jsou uloženy ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="39772-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="39772-166">S tímto přístupem budou dataanotace používat místo prostředků pro každou třídu jenom `SharedResource`.</span><span class="sxs-lookup"><span data-stu-id="39772-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="39772-167">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="39772-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="39772-168">SupportedCultures a SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="39772-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="39772-169">ASP.NET Core umožňuje zadat dvě hodnoty jazykové verze, `SupportedCultures` a `SupportedUICultures`.</span><span class="sxs-lookup"><span data-stu-id="39772-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="39772-170">Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) pro `SupportedCultures` určuje výsledky funkcí závislých na jazykové verzi, jako je například datum, čas, číslo a formátování měny.</span><span class="sxs-lookup"><span data-stu-id="39772-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="39772-171">`SupportedCultures` také určuje pořadí řazení textu, konvencí velikosti písmen a porovnávání řetězců.</span><span class="sxs-lookup"><span data-stu-id="39772-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="39772-172">Další informace o tom, jak server získá jazykovou verzi, naleznete v tématu [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="39772-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="39772-173">`SupportedUICultures` určuje, které překládá řetězce (ze souborů *. resx* ) jsou vyhledány správcem [prostředků](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="39772-173">The `SupportedUICultures` determines which translates strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="39772-174">`ResourceManager` jednoduše vyhledává řetězce specifické pro jazykovou verzi, které jsou určeny `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="39772-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="39772-175">Každé vlákno v rozhraní .NET má `CurrentCulture` a objekty `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="39772-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="39772-176">ASP.NET Core tyto hodnoty kontroluje při vykreslování funkcí závislých na jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="39772-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="39772-177">Pokud je například jazyková verze aktuálního vlákna nastavena na "en-US" (Čeština, USA), `DateTime.Now.ToLongDateString()` zobrazí "čtvrtek, Únor 18, 2016", ale pokud je `CurrentCulture` nastavena na "ES-ES" (španělština, Španělsko), bude výstup "Jueves, 18 de Febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="39772-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="39772-178">Soubory prostředků</span><span class="sxs-lookup"><span data-stu-id="39772-178">Resource files</span></span>

<span data-ttu-id="39772-179">Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců z kódu.</span><span class="sxs-lookup"><span data-stu-id="39772-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="39772-180">Přeložené řetězce pro jazyk, který není výchozí, jsou izolované soubory prostředků *. resx* .</span><span class="sxs-lookup"><span data-stu-id="39772-180">Translated strings for the non-default language are isolated *.resx* resource files.</span></span> <span data-ttu-id="39772-181">Například může být vhodné vytvořit soubor prostředků španělštiny s názvem *Welcome. ES. resx* obsahující přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="39772-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="39772-182">"ES" je kód jazyka pro španělštinu.</span><span class="sxs-lookup"><span data-stu-id="39772-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="39772-183">Postup vytvoření tohoto souboru prostředků v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="39772-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="39772-184">V **Průzkumník řešení**klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **přidat** **novou položku** > .</span><span class="sxs-lookup"><span data-stu-id="39772-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Vnořená kontextová nabídka: v Průzkumník řešení je místní nabídka pro prostředky otevřená.](localization/_static/newi.png)

2. <span data-ttu-id="39772-187">V poli **Vyhledat nainstalované šablony** zadejte "Resource" a soubor pojmenujte.</span><span class="sxs-lookup"><span data-stu-id="39772-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Dialogové okno Přidat novou položku](localization/_static/res.png)

3. <span data-ttu-id="39772-189">Do sloupce **název** zadejte hodnotu klíče (nativní řetězec) a přeložený řetězec ve sloupci Value ( **hodnota** ).</span><span class="sxs-lookup"><span data-stu-id="39772-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome. ES. resx soubor (soubor prostředků Welcome pro španělštinu) se slovem Hello ve sloupci název a slovo Hola (text Hello v španělštině) ve sloupci Hodnota](localization/_static/hola.png)

    <span data-ttu-id="39772-191">Sada Visual Studio zobrazí *uvítací soubor. ES. resx* .</span><span class="sxs-lookup"><span data-stu-id="39772-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Průzkumník řešení se zobrazuje soubor prostředků nástroje Welcome španělština (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="39772-193">Pojmenovávání souborů prostředků</span><span class="sxs-lookup"><span data-stu-id="39772-193">Resource file naming</span></span>

<span data-ttu-id="39772-194">Prostředky jsou pojmenovány pro úplný název typu své třídy minus název sestavení.</span><span class="sxs-lookup"><span data-stu-id="39772-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="39772-195">Například francouzská prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu `LocalizationWebsite.Web.Startup` by se jmenovala *Startup. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="39772-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="39772-196">Prostředek pro třídu `LocalizationWebsite.Web.Controllers.HomeController` by měl pojmenovat *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="39772-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39772-197">Pokud cílový obor názvů třídy není stejný jako název sestavení, budete potřebovat úplný název typu.</span><span class="sxs-lookup"><span data-stu-id="39772-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="39772-198">Například v ukázce projektu je prostředek pro typ `ExtraNamespace.Tools` pojmenovaný *ExtraNamespace. Tools. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="39772-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="39772-199">V ukázkovém projektu metoda `ConfigureServices` nastaví `ResourcesPath` na "Resources", takže relativní cesta projektu pro francouzský soubor prostředků domovského řadiče je *Resources/Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="39772-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39772-200">Případně můžete použít složky k uspořádání souborů prostředků.</span><span class="sxs-lookup"><span data-stu-id="39772-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="39772-201">V případě domovského kontroleru by tato cesta byla *Resources/Controllers/HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="39772-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="39772-202">Pokud nepoužijete možnost `ResourcesPath`, soubor *. resx* by přešel do základního adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="39772-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="39772-203">Soubor prostředků pro `HomeController` by měl pojmenovat *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="39772-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="39772-204">Volba použití konvence pojmenování teček nebo Path závisí na tom, jak chcete uspořádat soubory prostředků.</span><span class="sxs-lookup"><span data-stu-id="39772-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="39772-205">Název prostředku</span><span class="sxs-lookup"><span data-stu-id="39772-205">Resource name</span></span> | <span data-ttu-id="39772-206">Pojmenování teček nebo Path</span><span class="sxs-lookup"><span data-stu-id="39772-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="39772-207">Prostředky/řadiče. HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="39772-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="39772-208">Tečka</span><span class="sxs-lookup"><span data-stu-id="39772-208">Dot</span></span>  |
| <span data-ttu-id="39772-209">Prostředky/řadiče/HomeController. fr. resx</span><span class="sxs-lookup"><span data-stu-id="39772-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="39772-210">Cesta</span><span class="sxs-lookup"><span data-stu-id="39772-210">Path</span></span> |
|    |     |

<span data-ttu-id="39772-211">Soubory prostředků používající `@inject IViewLocalizer` v zobrazení Razor následují podobně jako vzor.</span><span class="sxs-lookup"><span data-stu-id="39772-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="39772-212">Soubor prostředků pro zobrazení může být pojmenován buď pomocí názvu tečky, nebo pojmenování cesty.</span><span class="sxs-lookup"><span data-stu-id="39772-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="39772-213">Soubory prostředků zobrazení Razor napodobují cestu k souboru přidruženého zobrazení.</span><span class="sxs-lookup"><span data-stu-id="39772-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="39772-214">Za předpokladu, že nastavíme `ResourcesPath` na "prostředky", soubor francouzského prostředku přidružený k zobrazením */domů/o zobrazení. cshtml* může být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="39772-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="39772-215">Prostředky/zobrazení/domů/o. fr. resx</span><span class="sxs-lookup"><span data-stu-id="39772-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="39772-216">Prostředky/zobrazení. domů. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="39772-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="39772-217">Pokud nepoužijete možnost `ResourcesPath`, soubor *. resx* pro zobrazení by byl umístěn ve stejné složce jako zobrazení.</span><span class="sxs-lookup"><span data-stu-id="39772-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="39772-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="39772-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="39772-219">Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení.</span><span class="sxs-lookup"><span data-stu-id="39772-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

<span data-ttu-id="39772-220">Pokud se kořenový obor názvů sestavení liší od názvu sestavení:</span><span class="sxs-lookup"><span data-stu-id="39772-220">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="39772-221">Lokalizace ve výchozím nastavení nefunguje.</span><span class="sxs-lookup"><span data-stu-id="39772-221">Localization does not work by default.</span></span>
* <span data-ttu-id="39772-222">Lokalizace se nezdařila z důvodu způsobu hledání prostředků v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="39772-222">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="39772-223">`RootNamespace` je hodnota doby sestavení, která není k dispozici pro spuštěný proces.</span><span class="sxs-lookup"><span data-stu-id="39772-223">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="39772-224">Pokud se `RootNamespace` liší od `AssemblyName`, zahrňte do *AssemblyInfo.cs* následující hodnoty (s hodnotami parametrů nahrazenými skutečnými hodnotami):</span><span class="sxs-lookup"><span data-stu-id="39772-224">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="39772-225">Předchozí kód umožňuje úspěšné vyřešení souborů RESX.</span><span class="sxs-lookup"><span data-stu-id="39772-225">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="39772-226">Chování záložního kultivačního prostředí</span><span class="sxs-lookup"><span data-stu-id="39772-226">Culture fallback behavior</span></span>

<span data-ttu-id="39772-227">Při hledání prostředku se lokalizace zapojit do "záložní kultury".</span><span class="sxs-lookup"><span data-stu-id="39772-227">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="39772-228">Od požadované jazykové verze, pokud se nenajde, se vrátí k nadřazené jazykové verzi této jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="39772-228">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="39772-229">Kromě toho, vlastnost [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) představuje nadřazenou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="39772-229">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="39772-230">To je obvykle (ale ne vždy) znamená odebrání národního přízpůsobu z ISO.</span><span class="sxs-lookup"><span data-stu-id="39772-230">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="39772-231">Například dialekt španělštiny ve španělštině, který se používá v Mexiku, je ES-MX.</span><span class="sxs-lookup"><span data-stu-id="39772-231">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="39772-232">Má nadřazené "ES" &mdash;Spanish nespecifické pro žádnou zemi.</span><span class="sxs-lookup"><span data-stu-id="39772-232">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="39772-233">Představte si, že váš web obdrží žádost o "úvodní" prostředek pomocí kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="39772-233">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="39772-234">Systém lokalizace vyhledá následující prostředky v uvedeném pořadí a vybere první shodu:</span><span class="sxs-lookup"><span data-stu-id="39772-234">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="39772-235">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="39772-235">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="39772-236">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="39772-236">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="39772-237">*Welcome. resx* (Pokud `NeutralResourcesLanguage` je "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="39772-237">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="39772-238">Pokud například odeberete označení jazykové verze ". fr" a máte nastavenou jazykovou verzi na francouzštinu, je výchozí soubor prostředků přečten a jsou lokalizovány řetězce.</span><span class="sxs-lookup"><span data-stu-id="39772-238">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="39772-239">Správce prostředků určí výchozí nebo záložní prostředek, pokud nic nevyhovuje vaší požadované jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="39772-239">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="39772-240">Pokud chcete vrátit klíč pouze v případě, že chybí prostředek pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="39772-240">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="39772-241">Generování souborů prostředků pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39772-241">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="39772-242">Vytvoříte-li soubor prostředků v aplikaci Visual Studio bez jazykové verze v názvu souboru (například *Welcome. resx*), sada C# Visual Studio vytvoří třídu s vlastností pro každý řetězec.</span><span class="sxs-lookup"><span data-stu-id="39772-242">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="39772-243">To obvykle není to, co chcete s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="39772-243">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="39772-244">Obvykle nemáte výchozí soubor prostředků *. resx* (soubor *. resx* bez názvu jazykové verze).</span><span class="sxs-lookup"><span data-stu-id="39772-244">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="39772-245">Návrh souboru *. resx* doporučujeme vytvořit s názvem jazykové verze (například *Welcome. fr. resx*).</span><span class="sxs-lookup"><span data-stu-id="39772-245">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="39772-246">Při vytváření souboru *. resx* s názvem jazykové verze aplikace Visual Studio negeneruje soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="39772-246">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="39772-247">Přidat další jazykové verze</span><span class="sxs-lookup"><span data-stu-id="39772-247">Add other cultures</span></span>

<span data-ttu-id="39772-248">Každá kombinace jazyka a jazykové verze (Kromě výchozího jazyka) vyžaduje jedinečný soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="39772-248">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="39772-249">Soubory prostředků můžete vytvořit pro různé kultury a národní prostředí vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-US**, **fr-CA**a **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="39772-249">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="39772-250">Tyto kódy ISO jsou umístěné mezi názvem souboru a příponou souboru *. resx* , jako v *Welcome.ES-MX. resx* (španělština/Mexiko).</span><span class="sxs-lookup"><span data-stu-id="39772-250">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="39772-251">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="39772-251">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="39772-252">Konfigurace lokalizace</span><span class="sxs-lookup"><span data-stu-id="39772-252">Configure localization</span></span>

<span data-ttu-id="39772-253">Lokalizace je nakonfigurovaná v metodě `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="39772-253">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="39772-254">`AddLocalization` přidá služby lokalizace do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="39772-254">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="39772-255">Výše uvedený kód také nastaví cestu prostředků na prostředky.</span><span class="sxs-lookup"><span data-stu-id="39772-255">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="39772-256">`AddViewLocalization` přidá podporu pro lokalizované soubory zobrazení.</span><span class="sxs-lookup"><span data-stu-id="39772-256">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="39772-257">V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="39772-257">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="39772-258">Například "fr" v souboru *index. fr. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="39772-258">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="39772-259">`AddDataAnnotationsLocalization` přidá podporu lokalizovaných zpráv ověřování `DataAnnotations` prostřednictvím abstrakcí `IStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="39772-259">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="39772-260">Middleware lokalizace</span><span class="sxs-lookup"><span data-stu-id="39772-260">Localization middleware</span></span>

<span data-ttu-id="39772-261">Aktuální jazyková verze v požadavku je nastavena v [middleware](xref:fundamentals/middleware/index)lokalizace.</span><span class="sxs-lookup"><span data-stu-id="39772-261">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="39772-262">Middleware lokalizace je povolena v metodě `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="39772-262">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="39772-263">Middleware lokalizace musí být nakonfigurované před jakýmkoli middlewarem, který by mohl kontrolovat jazykovou verzi žádosti (například `app.UseMvcWithDefaultRoute()`).</span><span class="sxs-lookup"><span data-stu-id="39772-263">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]

<span data-ttu-id="39772-264">`UseRequestLocalization` inicializuje objekt `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="39772-264">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="39772-265">Při každém požadavku se vypíše seznam `RequestCultureProvider` v `RequestLocalizationOptions` a prvním poskytovatelem, který dokáže úspěšně určit jazykovou verzi žádosti.</span><span class="sxs-lookup"><span data-stu-id="39772-265">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="39772-266">Výchozí zprostředkovatelé pocházejí z třídy `RequestLocalizationOptions`:</span><span class="sxs-lookup"><span data-stu-id="39772-266">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="39772-267">Výchozí seznam bude z nejpřesnější, aby byl nejméně specifický.</span><span class="sxs-lookup"><span data-stu-id="39772-267">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="39772-268">Později v článku se dozvíte, jak můžete změnit pořadí a dokonce přidat vlastního poskytovatele jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="39772-268">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="39772-269">Pokud žádný z poskytovatelů nemůže určit jazykovou verzi žádosti, je použita `DefaultRequestCulture`.</span><span class="sxs-lookup"><span data-stu-id="39772-269">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="39772-270">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="39772-270">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="39772-271">Některé aplikace budou používat řetězec dotazu k nastavení jazykové verze [a jazykové verze uživatelského rozhraní](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="39772-271">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="39772-272">Pro aplikace, které používají přístup k hlavičkám souborů cookie nebo Accept-Language, je přidání řetězce dotazu na adresu URL užitečné pro ladění a testování kódu.</span><span class="sxs-lookup"><span data-stu-id="39772-272">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="39772-273">Ve výchozím nastavení je `QueryStringRequestCultureProvider` registrován jako první poskytovatel lokalizace v seznamu `RequestCultureProvider`.</span><span class="sxs-lookup"><span data-stu-id="39772-273">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="39772-274">Parametry řetězce dotazu předáte `culture` a `ui-culture`.</span><span class="sxs-lookup"><span data-stu-id="39772-274">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="39772-275">Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělština/Mexiko:</span><span class="sxs-lookup"><span data-stu-id="39772-275">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="39772-276">Pokud předáte jenom jednu z těchto dvou (`culture` nebo `ui-culture`), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí toho, kterou jste předali.</span><span class="sxs-lookup"><span data-stu-id="39772-276">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="39772-277">Například nastavení pouze jazyková verze nastaví `Culture` i `UICulture`:</span><span class="sxs-lookup"><span data-stu-id="39772-277">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="39772-278">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="39772-278">CookieRequestCultureProvider</span></span>

<span data-ttu-id="39772-279">Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze pomocí souboru cookie ASP.NET Core jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="39772-279">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="39772-280">K vytvoření souboru cookie použijte metodu `MakeCookieValue`.</span><span class="sxs-lookup"><span data-stu-id="39772-280">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="39772-281">`CookieRequestCultureProvider` `DefaultCookieName` vrátí výchozí název souboru cookie, který se používá ke sledování preferovaných informací o jazykové verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="39772-281">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="39772-282">Výchozí název souboru cookie je `.AspNetCore.Culture`.</span><span class="sxs-lookup"><span data-stu-id="39772-282">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="39772-283">Formát souboru cookie je `c=%LANGCODE%|uic=%LANGCODE%`, kde `c` je `Culture` a `uic` `UICulture`například:</span><span class="sxs-lookup"><span data-stu-id="39772-283">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="39772-284">Pokud zadáte pouze jednu z informací o jazykové verzi a jazykovou verzi uživatelského rozhraní, zadaná jazyková verze bude použita pro informace o jazykové verzi i jazyková verze uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="39772-284">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="39772-285">Hlavička protokolu HTTP pro přijetí – jazyk</span><span class="sxs-lookup"><span data-stu-id="39772-285">The Accept-Language HTTP header</span></span>

<span data-ttu-id="39772-286">[Záhlaví Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) lze nastavit ve většině prohlížečů a původně bylo určeno pro určení jazyka uživatele.</span><span class="sxs-lookup"><span data-stu-id="39772-286">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="39772-287">Toto nastavení indikuje, co je v prohlížeči nastavené na Odeslat nebo zděděné z podkladového operačního systému.</span><span class="sxs-lookup"><span data-stu-id="39772-287">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="39772-288">Hlavička protokolu HTTP Accept-Language z požadavku prohlížeče není infallible způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="39772-288">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="39772-289">Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu kultury.</span><span class="sxs-lookup"><span data-stu-id="39772-289">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="39772-290">Nastavení hlavičky protokolu HTTP Accept-Language v IE</span><span class="sxs-lookup"><span data-stu-id="39772-290">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="39772-291">Z ikony ozubeného kolečka klepněte na **Možnosti Internetu**.</span><span class="sxs-lookup"><span data-stu-id="39772-291">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="39772-292">Klepněte na **jazyky**.</span><span class="sxs-lookup"><span data-stu-id="39772-292">Tap **Languages**.</span></span>

    ![Možnosti Internetu](localization/_static/lang.png)

3. <span data-ttu-id="39772-294">Klepněte na **nastavit jazykové předvolby**.</span><span class="sxs-lookup"><span data-stu-id="39772-294">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="39772-295">Klepněte na **Přidat jazyk**.</span><span class="sxs-lookup"><span data-stu-id="39772-295">Tap **Add a language**.</span></span>

5. <span data-ttu-id="39772-296">Přidejte jazyk.</span><span class="sxs-lookup"><span data-stu-id="39772-296">Add the language.</span></span>

6. <span data-ttu-id="39772-297">Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.</span><span class="sxs-lookup"><span data-stu-id="39772-297">Tap the language, then tap **Move Up**.</span></span>

::: moniker range=">= aspnetcore-3.0"
### <a name="the-content-language-http-header"></a><span data-ttu-id="39772-298">Hlavička protokolu HTTP v jazykovém obsahu</span><span class="sxs-lookup"><span data-stu-id="39772-298">The Content-Language HTTP header</span></span>

<span data-ttu-id="39772-299">Hlavička entity [jazyka obsahu](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :</span><span class="sxs-lookup"><span data-stu-id="39772-299">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="39772-300">Slouží k popisu jazyků, které jsou určené pro cílovou skupinu.</span><span class="sxs-lookup"><span data-stu-id="39772-300">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="39772-301">Umožňuje uživateli rozlišovat podle vlastního preferovaného jazyka uživatelů.</span><span class="sxs-lookup"><span data-stu-id="39772-301">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="39772-302">Záhlaví entit se používají v požadavcích HTTP i v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="39772-302">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="39772-303">V ASP.NET Core 3,0 lze přidat záhlaví `Content-Language` nastavením vlastnosti `ApplyCurrentCultureToResponseHeaders`.</span><span class="sxs-lookup"><span data-stu-id="39772-303">In ASP.NET Core 3.0 the `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="39772-304">Přidává se hlavička `Content-Language`:</span><span class="sxs-lookup"><span data-stu-id="39772-304">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="39772-305">Umožňuje RequestLocalizationMiddleware nastavit hlavičku `Content-Language` pomocí `CurrentUICulture`.</span><span class="sxs-lookup"><span data-stu-id="39772-305">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="39772-306">Eliminuje nutnost nastavit hlavičku odpovědi `Content-Language` explicitně.</span><span class="sxs-lookup"><span data-stu-id="39772-306">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```
::: moniker-end

### <a name="use-a-custom-provider"></a><span data-ttu-id="39772-307">Použití vlastního zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="39772-307">Use a custom provider</span></span>

<span data-ttu-id="39772-308">Předpokládejme, že chcete, aby vaši zákazníci mohli ukládat svůj jazyk a jazykovou verzi do databází.</span><span class="sxs-lookup"><span data-stu-id="39772-308">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="39772-309">Můžete napsat poskytovatele a vyhledat tyto hodnoty pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="39772-309">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="39772-310">Následující kód ukazuje, jak přidat vlastního zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="39772-310">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="39772-311">Chcete-li přidat nebo odebrat poskytovatele lokalizace, použijte `RequestLocalizationOptions`.</span><span class="sxs-lookup"><span data-stu-id="39772-311">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="39772-312">Programové nastavení kultury</span><span class="sxs-lookup"><span data-stu-id="39772-312">Set the culture programmatically</span></span>

<span data-ttu-id="39772-313">Tento ukázkový projekt **Localization. StarterWeb** na [GITHUBU](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture`.</span><span class="sxs-lookup"><span data-stu-id="39772-313">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="39772-314">Soubor *views/Shared/_SelectLanguagePartial. cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných jazykových verzí:</span><span class="sxs-lookup"><span data-stu-id="39772-314">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="39772-315">Soubor *views/Shared/_SelectLanguagePartial. cshtml* se přidá do části `footer` souboru rozložení, takže bude k dispozici pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="39772-315">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="39772-316">Metoda `SetLanguage` nastaví soubor cookie jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="39772-316">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="39772-317">Nemůžete připojit *_SelectLanguagePartial. cshtml* k ukázkovému kódu pro tento projekt.</span><span class="sxs-lookup"><span data-stu-id="39772-317">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="39772-318">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span><span class="sxs-lookup"><span data-stu-id="39772-318">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="39772-319">Globalization and localization terms</span><span class="sxs-lookup"><span data-stu-id="39772-319">Globalization and localization terms</span></span>

<span data-ttu-id="39772-320">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span><span class="sxs-lookup"><span data-stu-id="39772-320">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="39772-321">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span><span class="sxs-lookup"><span data-stu-id="39772-321">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="39772-322">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span><span class="sxs-lookup"><span data-stu-id="39772-322">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="39772-323">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span><span class="sxs-lookup"><span data-stu-id="39772-323">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="39772-324">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span><span class="sxs-lookup"><span data-stu-id="39772-324">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="39772-325">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span><span class="sxs-lookup"><span data-stu-id="39772-325">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="39772-326">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span><span class="sxs-lookup"><span data-stu-id="39772-326">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="39772-327">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="39772-327">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="39772-328">Internationalization is often abbreviated to "I18N".</span><span class="sxs-lookup"><span data-stu-id="39772-328">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="39772-329">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span><span class="sxs-lookup"><span data-stu-id="39772-329">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="39772-330">The same applies to Globalization (G11N), and Localization (L10N).</span><span class="sxs-lookup"><span data-stu-id="39772-330">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="39772-331">Terms:</span><span class="sxs-lookup"><span data-stu-id="39772-331">Terms:</span></span>

* <span data-ttu-id="39772-332">Globalization (G11N): The process of making an app support different languages and regions.</span><span class="sxs-lookup"><span data-stu-id="39772-332">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="39772-333">Localization (L10N): The process of customizing an app for a given language and region.</span><span class="sxs-lookup"><span data-stu-id="39772-333">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="39772-334">Internationalization (I18N): Describes both globalization and localization.</span><span class="sxs-lookup"><span data-stu-id="39772-334">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="39772-335">Culture: It's a language and, optionally, a region.</span><span class="sxs-lookup"><span data-stu-id="39772-335">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="39772-336">Neutral culture: A culture that has a specified language, but not a region.</span><span class="sxs-lookup"><span data-stu-id="39772-336">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="39772-337">(for example "en", "es")</span><span class="sxs-lookup"><span data-stu-id="39772-337">(for example "en", "es")</span></span>
* <span data-ttu-id="39772-338">Specific culture: A culture that has a specified language and region.</span><span class="sxs-lookup"><span data-stu-id="39772-338">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="39772-339">(for example "en-US", "en-GB", "es-CL")</span><span class="sxs-lookup"><span data-stu-id="39772-339">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="39772-340">Parent culture: The neutral culture that contains a specific culture.</span><span class="sxs-lookup"><span data-stu-id="39772-340">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="39772-341">(for example, "en" is the parent culture of "en-US" and "en-GB")</span><span class="sxs-lookup"><span data-stu-id="39772-341">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="39772-342">Locale: A locale is the same as a culture.</span><span class="sxs-lookup"><span data-stu-id="39772-342">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

::: moniker range=">= aspnetcore-3.0"
[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="39772-343">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="39772-343">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="39772-344">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span><span class="sxs-lookup"><span data-stu-id="39772-344">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="39772-345">Globalizing and localizing .NET applications</span><span class="sxs-lookup"><span data-stu-id="39772-345">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="39772-346">Resources in .resx Files</span><span class="sxs-lookup"><span data-stu-id="39772-346">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="39772-347">Microsoft Multilingual App Toolkit</span><span class="sxs-lookup"><span data-stu-id="39772-347">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="39772-348">Localization & Generics</span><span class="sxs-lookup"><span data-stu-id="39772-348">Localization & Generics</span></span>](https://github.com/hishamco/hishambinateya.com/blob/master/Posts/localization-and-generics.md)
* [<span data-ttu-id="39772-349">What is new in Localization in ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="39772-349">What is new in Localization in ASP.NET Core 3.0</span></span>](http://hishambinateya.com/what-is-new-in-localization-in-asp.net-core-3.0)

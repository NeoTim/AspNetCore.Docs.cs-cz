---
<span data-ttu-id="60dbb-101">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-102">'Blazor'</span></span>
- <span data-ttu-id="60dbb-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-103">'Identity'</span></span>
- <span data-ttu-id="60dbb-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-105">'Razor'</span></span>
- <span data-ttu-id="60dbb-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-106">'SignalR' uid:</span></span> 

---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="60dbb-107">Globalizace a lokalizace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="60dbb-107">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="60dbb-108">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)a [Hisham bin](https://twitter.com/hishambinateya) Ateya</span><span class="sxs-lookup"><span data-stu-id="60dbb-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="60dbb-109">Vícejazyčný web umožňuje webu oslovit širší cílovou skupinu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-109">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="60dbb-110">ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.</span><span class="sxs-lookup"><span data-stu-id="60dbb-110">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="60dbb-111">Mezinárodní využití zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="60dbb-111">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="60dbb-112">Globalizace je proces návrhu aplikací, které podporují různé jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-112">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="60dbb-113">Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují na konkrétní geografické oblasti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-113">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="60dbb-114">Lokalizace je proces přizpůsobení globální aplikace, kterou jste již zpracovali pro lokalizaci, do konkrétní jazykové verze nebo národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-114">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="60dbb-115">Další informace najdete v tématu **předpoklady globalizace a lokalizace** poblíž konce tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-115">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="60dbb-116">Lokalizace aplikace zahrnuje následující:</span><span class="sxs-lookup"><span data-stu-id="60dbb-116">App localization involves the following:</span></span>

1. <span data-ttu-id="60dbb-117">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="60dbb-117">Make the app's content localizable</span></span>
1. <span data-ttu-id="60dbb-118">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="60dbb-118">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="60dbb-119">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="60dbb-119">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="60dbb-120">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="60dbb-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="60dbb-121">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="60dbb-121">Make the app's content localizable</span></span>

<span data-ttu-id="60dbb-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' nevyžaduje uložení výchozích řetězců jazyka do souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="60dbb-123">Můžete vyvíjet aplikaci zaměřenou na lokalizaci a nemusíte vytvářet soubory prostředků na začátku ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="60dbb-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="60dbb-124">Následující kód ukazuje, jak zabalit řetězec "About title" pro lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="60dbb-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="60dbb-125">V předchozím kódu `IStringLocalizer<T>` implementace pochází z [Injektáže závislosti](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="60dbb-125">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="60dbb-126">Pokud se nenalezne lokalizovaná hodnota "o titulku", vrátí se klíč indexeru, tj. řetězec "About title".</span><span class="sxs-lookup"><span data-stu-id="60dbb-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="60dbb-127">Můžete ponechat výchozí řetězcové literály v aplikaci a zabalit je do lokalizátora, abyste se mohli soustředit na vývoj aplikace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="60dbb-128">Vyvíjíte svou aplikaci pomocí výchozího jazyka a připravíte ji pro krok lokalizace bez prvotního vytvoření výchozího souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="60dbb-129">Alternativně můžete použít tradiční přístup a zadat klíč pro načtení výchozího řetězce jazyka.</span><span class="sxs-lookup"><span data-stu-id="60dbb-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="60dbb-130">Pro mnoho vývojářů nový pracovní postup, který nemá výchozí jazyk *. resx* soubor, a jednoduše zabalí řetězcové literály může snížit režii lokalizace aplikace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="60dbb-131">Ostatní vývojáři budou preferovat tradiční pracovní postup, protože mohou usnadnit práci s delšími řetězcovými literály a usnadňují aktualizaci lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="60dbb-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="60dbb-132">Použijte `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují kód HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="60dbb-133">`IHtmlLocalizer`HTML kóduje argumenty, které jsou formátovány v řetězci prostředků, ale nekóduje kód HTML samotný řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="60dbb-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="60dbb-134">V ukázce zvýrazněné níže je pouze hodnota `name` parametru kódována HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="60dbb-135">**Poznámka:** Obecně chcete lokalizovat pouze text a nikoli HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="60dbb-136">Na nejnižší úrovni můžete obdržet zástupné `IStringLocalizerFactory` [vkládání závislostí](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="60dbb-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="60dbb-137">Výše uvedený kód ukazuje každou ze dvou metod Create Factory.</span><span class="sxs-lookup"><span data-stu-id="60dbb-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="60dbb-138">Lokalizované řetězce můžete rozdělit do oddílů podle řadiče, oblasti nebo pouze jednoho kontejneru.</span><span class="sxs-lookup"><span data-stu-id="60dbb-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="60dbb-139">V ukázkové aplikaci se `SharedResource` pro sdílené prostředky používá fiktivní třída s názvem.</span><span class="sxs-lookup"><span data-stu-id="60dbb-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="60dbb-140">Někteří vývojáři používají `Startup` třídu pro zahrnutí globálních nebo sdílených řetězců.</span><span class="sxs-lookup"><span data-stu-id="60dbb-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="60dbb-141">V níže uvedené ukázce se `InfoController` `SharedResource` používají a Localize:</span><span class="sxs-lookup"><span data-stu-id="60dbb-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="60dbb-142">Lokalizace zobrazení</span><span class="sxs-lookup"><span data-stu-id="60dbb-142">View localization</span></span>

<span data-ttu-id="60dbb-143">`IViewLocalizer`Služba poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="60dbb-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="60dbb-144">`ViewLocalizer`Třída implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="60dbb-145">Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="60dbb-146">Výchozí implementace nástroje `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="60dbb-147">Neexistuje možnost použít globální sdílený soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="60dbb-148">`ViewLocalizer`implementuje lokalizátora pomocí `IHtmlLocalizer` , takže Razor nekóduje kód HTML jako lokalizovaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="60dbb-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="60dbb-149">Můžete parametrizovat řetězce prostředků a `IViewLocalizer` zakódovat parametry HTML, ale ne řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="60dbb-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="60dbb-150">Vezměte v úvahu následující Razor značky:</span><span class="sxs-lookup"><span data-stu-id="60dbb-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="60dbb-151">Soubor prostředků francouzštiny může obsahovat následující:</span><span class="sxs-lookup"><span data-stu-id="60dbb-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="60dbb-152">Klíč</span><span class="sxs-lookup"><span data-stu-id="60dbb-152">Key</span></span> | <span data-ttu-id="60dbb-153">Hodnota</span><span class="sxs-lookup"><span data-stu-id="60dbb-153">Value</span></span> |
| ----- | ---
<span data-ttu-id="60dbb-154">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-154">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-155">'Blazor'</span></span>
- <span data-ttu-id="60dbb-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-156">'Identity'</span></span>
- <span data-ttu-id="60dbb-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-158">'Razor'</span></span>
- <span data-ttu-id="60dbb-159">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-159">'SignalR' uid:</span></span> 

<span data-ttu-id="60dbb-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="60dbb-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="60dbb-161">Vykreslené zobrazení obsahuje značku HTML ze souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-161">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="60dbb-162">**Poznámka:** Obecně chcete lokalizovat pouze text a nikoli HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-162">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="60dbb-163">Chcete-li použít sdílený soubor prostředků v zobrazení, vložení `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-163">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="60dbb-164">Lokalizace DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="60dbb-164">DataAnnotations localization</span></span>

<span data-ttu-id="60dbb-165">Chybové zprávy pro dataanotace jsou lokalizovány pomocí `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="60dbb-166">Pomocí možnosti `ResourcesPath = "Resources"` mohou být chybové zprávy v nástroji `RegisterViewModel` uloženy v jedné z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="60dbb-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="60dbb-167">*Resources/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="60dbb-168">*Prostředky/ViewModels/účet/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="60dbb-169">V ASP.NET Core MVC 1.1.0 a vyšší jsou lokalizovány neověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="60dbb-169">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="60dbb-170">ASP.NET Core MVC 1,0 **nehledá lokalizované** řetězce pro atributy, které nejsou ověřovány.</span><span class="sxs-lookup"><span data-stu-id="60dbb-170">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="60dbb-171">Použití jednoho řetězce prostředku pro více tříd</span><span class="sxs-lookup"><span data-stu-id="60dbb-171">Using one resource string for multiple classes</span></span>

<span data-ttu-id="60dbb-172">Následující kód ukazuje, jak použít jeden řetězec prostředku pro atributy ověřování s více třídami:</span><span class="sxs-lookup"><span data-stu-id="60dbb-172">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="60dbb-173">V předchozím kódu `SharedResource` je třída odpovídající RESX, kde jsou uloženy ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="60dbb-173">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="60dbb-174">S tímto přístupem budou dataanotace používat pouze `SharedResource` místo prostředků pro každou třídu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-174">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="60dbb-175">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="60dbb-175">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="60dbb-176">SupportedCultures a SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="60dbb-176">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="60dbb-177">ASP.NET Core umožňuje zadat dvě hodnoty jazykové verze `SupportedCultures` a `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-177">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="60dbb-178">Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) pro `SupportedCultures` určuje výsledky funkcí závislých na jazykové verzi, jako je datum, čas, číslo a formátování měny.</span><span class="sxs-lookup"><span data-stu-id="60dbb-178">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="60dbb-179">`SupportedCultures`Určuje také pořadí řazení textu, konvencí velikosti písmen a porovnávání řetězců.</span><span class="sxs-lookup"><span data-stu-id="60dbb-179">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="60dbb-180">Další informace o tom, jak server získá jazykovou verzi, naleznete v tématu [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="60dbb-180">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="60dbb-181">`SupportedUICultures`Určuje, které přeložené řetězce (ze souborů *. resx* ) jsou vyhledány správcem [prostředků](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="60dbb-181">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="60dbb-182">`ResourceManager`Jednoduše vyhledá řetězce specifické pro jazykovou verzi, které určuje `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-182">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="60dbb-183">Každé vlákno v rozhraní .NET má `CurrentCulture` a `CurrentUICulture` objekty.</span><span class="sxs-lookup"><span data-stu-id="60dbb-183">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="60dbb-184">ASP.NET Core tyto hodnoty kontroluje při vykreslování funkcí závislých na jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-184">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="60dbb-185">Pokud je například jazyková verze aktuálního vlákna nastavena na "en-US" (angličtina, USA), `DateTime.Now.ToLongDateString()` zobrazí "čtvrtek, 18. února 2016", ale pokud `CurrentCulture` je nastavená na "ES-ES" (španělština, Španělsko), bude výstup "Jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="60dbb-185">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="60dbb-186">Soubory prostředků</span><span class="sxs-lookup"><span data-stu-id="60dbb-186">Resource files</span></span>

<span data-ttu-id="60dbb-187">Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců z kódu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-187">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="60dbb-188">Přeložené řetězce pro jazyk, který není výchozí, jsou izolované v souborech prostředků *. resx* .</span><span class="sxs-lookup"><span data-stu-id="60dbb-188">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="60dbb-189">Například může být vhodné vytvořit soubor prostředků španělštiny s názvem *Welcome. ES. resx* obsahující přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="60dbb-189">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="60dbb-190">"ES" je kód jazyka pro španělštinu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-190">"es" is the language code for Spanish.</span></span> <span data-ttu-id="60dbb-191">Postup vytvoření tohoto souboru prostředků v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="60dbb-191">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="60dbb-192">V **Průzkumník řešení**klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-192">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Vnořená kontextová nabídka: v Průzkumník řešení je místní nabídka pro prostředky otevřená.](localization/_static/newi.png)

2. <span data-ttu-id="60dbb-195">V poli **Vyhledat nainstalované šablony** zadejte "Resource" a soubor pojmenujte.</span><span class="sxs-lookup"><span data-stu-id="60dbb-195">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Dialogové okno Přidat novou položku](localization/_static/res.png)

3. <span data-ttu-id="60dbb-197">Do sloupce **název** zadejte hodnotu klíče (nativní řetězec) a přeložený řetězec ve sloupci Value ( **hodnota** ).</span><span class="sxs-lookup"><span data-stu-id="60dbb-197">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome. ES. resx soubor (soubor prostředků Welcome pro španělštinu) se slovem Hello ve sloupci název a slovo Hola (text Hello v španělštině) ve sloupci Hodnota](localization/_static/hola.png)

    <span data-ttu-id="60dbb-199">Sada Visual Studio zobrazí *uvítací soubor. ES. resx* .</span><span class="sxs-lookup"><span data-stu-id="60dbb-199">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Průzkumník řešení se zobrazuje soubor prostředků nástroje Welcome španělština (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="60dbb-201">Pojmenovávání souborů prostředků</span><span class="sxs-lookup"><span data-stu-id="60dbb-201">Resource file naming</span></span>

<span data-ttu-id="60dbb-202">Prostředky jsou pojmenovány pro úplný název typu své třídy minus název sestavení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-202">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="60dbb-203">Například francouzská prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu, `LocalizationWebsite.Web.Startup` by měla být pojmenována *Startup. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-203">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="60dbb-204">Prostředek pro třídu by měl `LocalizationWebsite.Web.Controllers.HomeController` pojmenovat *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-204">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-205">Pokud cílový obor názvů třídy není stejný jako název sestavení, budete potřebovat úplný název typu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-205">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="60dbb-206">Například v ukázkovém projektu by měl prostředek pro typ `ExtraNamespace.Tools` název *ExtraNamespace. Tools. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-206">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="60dbb-207">V ukázkovém projektu `ConfigureServices` Metoda nastaví `ResourcesPath` na "prostředky", takže relativní cesta projektu pro francouzský soubor prostředků domovského řadiče je *Resources/Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-207">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-208">Případně můžete použít složky k uspořádání souborů prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-208">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="60dbb-209">V případě domovského kontroleru by tato cesta byla *Resources/Controllers/HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-209">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-210">Pokud tuto možnost nepoužijete `ResourcesPath` , soubor *. resx* by přešel do základního adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-210">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="60dbb-211">Soubor prostředků pro `HomeController` by měl mít název *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-211">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-212">Volba použití konvence pojmenování teček nebo Path závisí na tom, jak chcete uspořádat soubory prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-212">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="60dbb-213">Název prostředku</span><span class="sxs-lookup"><span data-stu-id="60dbb-213">Resource name</span></span> | <span data-ttu-id="60dbb-214">Pojmenování teček nebo Path</span><span class="sxs-lookup"><span data-stu-id="60dbb-214">Dot or path naming</span></span> |
| ---
<span data-ttu-id="60dbb-215">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-215">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-216">'Blazor'</span></span>
- <span data-ttu-id="60dbb-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-217">'Identity'</span></span>
- <span data-ttu-id="60dbb-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-219">'Razor'</span></span>
- <span data-ttu-id="60dbb-220">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-221">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-221">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-222">'Blazor'</span></span>
- <span data-ttu-id="60dbb-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-223">'Identity'</span></span>
- <span data-ttu-id="60dbb-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-225">'Razor'</span></span>
- <span data-ttu-id="60dbb-226">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-227">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-227">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-228">'Blazor'</span></span>
- <span data-ttu-id="60dbb-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-229">'Identity'</span></span>
- <span data-ttu-id="60dbb-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-231">'Razor'</span></span>
- <span data-ttu-id="60dbb-232">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-233">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-233">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-234">'Blazor'</span></span>
- <span data-ttu-id="60dbb-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-235">'Identity'</span></span>
- <span data-ttu-id="60dbb-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-237">'Razor'</span></span>
- <span data-ttu-id="60dbb-238">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-238">'SignalR' uid:</span></span> 

<span data-ttu-id="60dbb-239">------   | ---Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-240">'Blazor'</span></span>
- <span data-ttu-id="60dbb-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-241">'Identity'</span></span>
- <span data-ttu-id="60dbb-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-243">'Razor'</span></span>
- <span data-ttu-id="60dbb-244">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-245">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-245">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-246">'Blazor'</span></span>
- <span data-ttu-id="60dbb-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-247">'Identity'</span></span>
- <span data-ttu-id="60dbb-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-249">'Razor'</span></span>
- <span data-ttu-id="60dbb-250">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-251">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-251">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-252">'Blazor'</span></span>
- <span data-ttu-id="60dbb-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-253">'Identity'</span></span>
- <span data-ttu-id="60dbb-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-255">'Razor'</span></span>
- <span data-ttu-id="60dbb-256">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-257">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-257">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-258">'Blazor'</span></span>
- <span data-ttu-id="60dbb-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-259">'Identity'</span></span>
- <span data-ttu-id="60dbb-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-261">'Razor'</span></span>
- <span data-ttu-id="60dbb-262">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-262">'SignalR' uid:</span></span> 

<span data-ttu-id="60dbb-263">------- | | Prostředky/řadiče. HomeController. fr. resx | Tečka | | Prostředky/řadiče/HomeController. fr. resx | Cesta | |    |     |</span><span class="sxs-lookup"><span data-stu-id="60dbb-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="60dbb-264">Soubory prostředků používané `@inject IViewLocalizer` v Razor zobrazeních následují podobně jako vzor.</span><span class="sxs-lookup"><span data-stu-id="60dbb-264">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="60dbb-265">Soubor prostředků pro zobrazení může být pojmenován buď pomocí názvu tečky, nebo pojmenování cesty.</span><span class="sxs-lookup"><span data-stu-id="60dbb-265">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="60dbb-266">zobrazení soubory prostředků napodobá cestě k jejich přidruženému souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-266"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="60dbb-267">Za předpokladu, že nastavíme `ResourcesPath` "prostředky", soubor francouzského prostředku přidružený k zobrazením */domů/o zobrazení. cshtml* může být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="60dbb-267">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="60dbb-268">Prostředky/zobrazení/domů/o. fr. resx</span><span class="sxs-lookup"><span data-stu-id="60dbb-268">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="60dbb-269">Prostředky/zobrazení. domů. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="60dbb-269">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="60dbb-270">Pokud tuto možnost nepoužijete `ResourcesPath` , bude soubor *. resx* pro zobrazení umístěn ve stejné složce jako zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-270">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="60dbb-271">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="60dbb-271">RootNamespaceAttribute</span></span> 

<span data-ttu-id="60dbb-272">Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-272">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="60dbb-273">K tomu může dojít, když název projektu není platným identifikátorem .NET.</span><span class="sxs-lookup"><span data-stu-id="60dbb-273">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="60dbb-274">V případě instance `my-project-name.csproj` bude použit kořenový obor názvů `my_project_name` a název sestavení, `my-project-name` který vede k této chybě.</span><span class="sxs-lookup"><span data-stu-id="60dbb-274">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="60dbb-275">Pokud se kořenový obor názvů sestavení liší od názvu sestavení:</span><span class="sxs-lookup"><span data-stu-id="60dbb-275">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="60dbb-276">Lokalizace ve výchozím nastavení nefunguje.</span><span class="sxs-lookup"><span data-stu-id="60dbb-276">Localization does not work by default.</span></span>
* <span data-ttu-id="60dbb-277">Lokalizace se nezdařila z důvodu způsobu hledání prostředků v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-277">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="60dbb-278">`RootNamespace`je hodnota doby sestavení, která není k dispozici pro spuštěný proces.</span><span class="sxs-lookup"><span data-stu-id="60dbb-278">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="60dbb-279">Pokud se `RootNamespace` liší od z `AssemblyName` , zahrňte následující v *AssemblyInfo.cs* (s hodnotami parametrů nahrazenými skutečnými hodnotami):</span><span class="sxs-lookup"><span data-stu-id="60dbb-279">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="60dbb-280">Předchozí kód umožňuje úspěšné vyřešení souborů RESX.</span><span class="sxs-lookup"><span data-stu-id="60dbb-280">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="60dbb-281">Chování záložního kultivačního prostředí</span><span class="sxs-lookup"><span data-stu-id="60dbb-281">Culture fallback behavior</span></span>

<span data-ttu-id="60dbb-282">Při hledání prostředku se lokalizace zapojit do "záložní kultury".</span><span class="sxs-lookup"><span data-stu-id="60dbb-282">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="60dbb-283">Od požadované jazykové verze, pokud se nenajde, se vrátí k nadřazené jazykové verzi této jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-283">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="60dbb-284">Kromě toho, vlastnost [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) představuje nadřazenou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-284">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="60dbb-285">To je obvykle (ale ne vždy) znamená odebrání národního přízpůsobu z ISO.</span><span class="sxs-lookup"><span data-stu-id="60dbb-285">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="60dbb-286">Například dialekt španělštiny ve španělštině, který se používá v Mexiku, je ES-MX.</span><span class="sxs-lookup"><span data-stu-id="60dbb-286">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="60dbb-287">Má nadřízenou španělštinu ES, která &mdash; není specifická pro žádnou zemi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-287">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="60dbb-288">Představte si, že váš web obdrží žádost o "úvodní" prostředek pomocí kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="60dbb-288">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="60dbb-289">Systém lokalizace vyhledá následující prostředky v uvedeném pořadí a vybere první shodu:</span><span class="sxs-lookup"><span data-stu-id="60dbb-289">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="60dbb-290">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-290">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="60dbb-291">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-291">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="60dbb-292">*Welcome. resx* (Pokud `NeutralResourcesLanguage` je to "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="60dbb-292">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="60dbb-293">Pokud například odeberete označení jazykové verze ". fr" a máte nastavenou jazykovou verzi na francouzštinu, je výchozí soubor prostředků přečten a jsou lokalizovány řetězce.</span><span class="sxs-lookup"><span data-stu-id="60dbb-293">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="60dbb-294">Správce prostředků určí výchozí nebo záložní prostředek, pokud nic nevyhovuje vaší požadované jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-294">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="60dbb-295">Pokud chcete vrátit klíč pouze v případě, že chybí prostředek pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-295">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="60dbb-296">Generování souborů prostředků pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60dbb-296">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="60dbb-297">Vytvoříte-li soubor prostředků v aplikaci Visual Studio bez jazykové verze v názvu souboru (například *Welcome. resx*), sada Visual Studio vytvoří třídu jazyka C# s vlastností pro každý řetězec.</span><span class="sxs-lookup"><span data-stu-id="60dbb-297">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="60dbb-298">To obvykle není to, co chcete s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60dbb-298">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="60dbb-299">Obvykle nemáte výchozí soubor prostředků *. resx* (soubor *. resx* bez názvu jazykové verze).</span><span class="sxs-lookup"><span data-stu-id="60dbb-299">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="60dbb-300">Návrh souboru *. resx* doporučujeme vytvořit s názvem jazykové verze (například *Welcome. fr. resx*).</span><span class="sxs-lookup"><span data-stu-id="60dbb-300">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="60dbb-301">Při vytváření souboru *. resx* s názvem jazykové verze aplikace Visual Studio negeneruje soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="60dbb-301">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="60dbb-302">Přidat další jazykové verze</span><span class="sxs-lookup"><span data-stu-id="60dbb-302">Add other cultures</span></span>

<span data-ttu-id="60dbb-303">Každá kombinace jazyka a jazykové verze (Kromě výchozího jazyka) vyžaduje jedinečný soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-303">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="60dbb-304">Soubory prostředků můžete vytvořit pro různé kultury a národní prostředí vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-US**, **fr-CA**a **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="60dbb-304">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="60dbb-305">Tyto kódy ISO jsou umístěné mezi názvem souboru a příponou souboru *. resx* , jako v *Welcome.ES-MX. resx* (španělština/Mexiko).</span><span class="sxs-lookup"><span data-stu-id="60dbb-305">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="60dbb-306">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="60dbb-306">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="60dbb-307">Konfigurace lokalizace</span><span class="sxs-lookup"><span data-stu-id="60dbb-307">Configure localization</span></span>

<span data-ttu-id="60dbb-308">Lokalizace je nakonfigurovaná v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="60dbb-308">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="60dbb-309">`AddLocalization`Přidá služby lokalizace do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="60dbb-309">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="60dbb-310">Výše uvedený kód také nastaví cestu prostředků na prostředky.</span><span class="sxs-lookup"><span data-stu-id="60dbb-310">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="60dbb-311">`AddViewLocalization`Přidá podporu pro lokalizované soubory zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-311">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="60dbb-312">V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-312">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="60dbb-313">Například "fr" v souboru *index. fr. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="60dbb-313">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="60dbb-314">`AddDataAnnotationsLocalization`Přidává podporu pro lokalizované `DataAnnotations` ověřovací zprávy prostřednictvím `IStringLocalizer` abstrakcí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-314">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="60dbb-315">Middleware lokalizace</span><span class="sxs-lookup"><span data-stu-id="60dbb-315">Localization middleware</span></span>

<span data-ttu-id="60dbb-316">Aktuální jazyková verze v požadavku je nastavena v [middleware](xref:fundamentals/middleware/index)lokalizace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-316">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="60dbb-317">Middleware lokalizace je povolená v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="60dbb-317">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="60dbb-318">Middleware lokalizace musí být konfigurovány před jakýmkoli middlewarem, který by mohl kontrolovat jazykovou verzi žádosti (například `app.UseMvcWithDefaultRoute()` ).</span><span class="sxs-lookup"><span data-stu-id="60dbb-318">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="60dbb-319">`UseRequestLocalization`Inicializuje `RequestLocalizationOptions` objekt.</span><span class="sxs-lookup"><span data-stu-id="60dbb-319">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="60dbb-320">Při každém požadavku se vypíše seznam `RequestCultureProvider` ve `RequestLocalizationOptions` výčtu a první zprostředkovatel, který dokáže úspěšně určit jazykovou verzi žádosti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-320">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="60dbb-321">Výchozí zprostředkovatelé přicházejí z `RequestLocalizationOptions` třídy:</span><span class="sxs-lookup"><span data-stu-id="60dbb-321">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="60dbb-322">Výchozí seznam bude z nejpřesnější, aby byl nejméně specifický.</span><span class="sxs-lookup"><span data-stu-id="60dbb-322">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="60dbb-323">Později v článku se dozvíte, jak můžete změnit pořadí a dokonce přidat vlastního poskytovatele jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-323">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="60dbb-324">Pokud žádný z poskytovatelů nemůže určit jazykovou verzi žádosti, `DefaultRequestCulture` je použita.</span><span class="sxs-lookup"><span data-stu-id="60dbb-324">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="60dbb-325">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="60dbb-325">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="60dbb-326">Některé aplikace budou používat řetězec dotazu k nastavení jazykové verze [a jazykové verze uživatelského rozhraní](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="60dbb-326">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="60dbb-327">Pro aplikace, které používají přístup k hlavičkám souborů cookie nebo Accept-Language, je přidání řetězce dotazu na adresu URL užitečné pro ladění a testování kódu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-327">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="60dbb-328">Ve výchozím nastavení `QueryStringRequestCultureProvider` je jako první poskytovatel lokalizace v `RequestCultureProvider` seznamu zaregistrován.</span><span class="sxs-lookup"><span data-stu-id="60dbb-328">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="60dbb-329">Předáte parametry řetězce dotazu `culture` a `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-329">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="60dbb-330">Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělština/Mexiko:</span><span class="sxs-lookup"><span data-stu-id="60dbb-330">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="60dbb-331">Pokud předáte pouze jeden z těchto dvou ( `culture` nebo `ui-culture` ), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí toho, kterou jste předali.</span><span class="sxs-lookup"><span data-stu-id="60dbb-331">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="60dbb-332">Například nastavení pouze jazyková verze nastaví jak `Culture` a `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-332">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="60dbb-333">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="60dbb-333">CookieRequestCultureProvider</span></span>

<span data-ttu-id="60dbb-334">Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze pomocí souboru cookie ASP.NET Core jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-334">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="60dbb-335">Pomocí `MakeCookieValue` metody vytvořte soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="60dbb-335">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="60dbb-336">`CookieRequestCultureProvider` `DefaultCookieName` Vrátí výchozí název souboru cookie, který se používá ke sledování informací o preferované jazykové verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="60dbb-336">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="60dbb-337">Výchozí název souboru cookie je `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-337">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="60dbb-338">Formát souboru cookie je `c=%LANGCODE%|uic=%LANGCODE%` , kde `c` je `Culture` a `uic` je například `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-338">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="60dbb-339">Pokud zadáte pouze jednu z informací o jazykové verzi a jazykovou verzi uživatelského rozhraní, zadaná jazyková verze bude použita pro informace o jazykové verzi i jazyková verze uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="60dbb-339">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="60dbb-340">Hlavička protokolu HTTP pro přijetí – jazyk</span><span class="sxs-lookup"><span data-stu-id="60dbb-340">The Accept-Language HTTP header</span></span>

<span data-ttu-id="60dbb-341">[Záhlaví Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) lze nastavit ve většině prohlížečů a původně bylo určeno pro určení jazyka uživatele.</span><span class="sxs-lookup"><span data-stu-id="60dbb-341">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="60dbb-342">Toto nastavení indikuje, co je v prohlížeči nastavené na Odeslat nebo zděděné z podkladového operačního systému.</span><span class="sxs-lookup"><span data-stu-id="60dbb-342">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="60dbb-343">Hlavička protokolu HTTP Accept-Language z požadavku prohlížeče není infallible způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="60dbb-343">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="60dbb-344">Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu kultury.</span><span class="sxs-lookup"><span data-stu-id="60dbb-344">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="60dbb-345">Nastavení hlavičky protokolu HTTP Accept-Language v IE</span><span class="sxs-lookup"><span data-stu-id="60dbb-345">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="60dbb-346">Z ikony ozubeného kolečka klepněte na **Možnosti Internetu**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-346">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="60dbb-347">Klepněte na **jazyky**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-347">Tap **Languages**.</span></span>

    ![Možnosti Internetu](localization/_static/lang.png)

3. <span data-ttu-id="60dbb-349">Klepněte na **nastavit jazykové předvolby**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-349">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="60dbb-350">Klepněte na **Přidat jazyk**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-350">Tap **Add a language**.</span></span>

5. <span data-ttu-id="60dbb-351">Přidejte jazyk.</span><span class="sxs-lookup"><span data-stu-id="60dbb-351">Add the language.</span></span>

6. <span data-ttu-id="60dbb-352">Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-352">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="60dbb-353">Použití vlastního zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="60dbb-353">Use a custom provider</span></span>

<span data-ttu-id="60dbb-354">Předpokládejme, že chcete, aby vaši zákazníci mohli ukládat svůj jazyk a jazykovou verzi do databází.</span><span class="sxs-lookup"><span data-stu-id="60dbb-354">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="60dbb-355">Můžete napsat poskytovatele a vyhledat tyto hodnoty pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="60dbb-355">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="60dbb-356">Následující kód ukazuje, jak přidat vlastního zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="60dbb-356">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="60dbb-357">Použijte `RequestLocalizationOptions` k přidání nebo odebrání poskytovatelů lokalizace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-357">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="60dbb-358">Programové nastavení kultury</span><span class="sxs-lookup"><span data-stu-id="60dbb-358">Set the culture programmatically</span></span>

<span data-ttu-id="60dbb-359">Tento ukázkový projekt **Localization. StarterWeb** na [GITHUBU](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-359">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="60dbb-360">Soubor *views/Shared/_SelectLanguagePartial. cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných jazykových verzí:</span><span class="sxs-lookup"><span data-stu-id="60dbb-360">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="60dbb-361">Soubor *views/Shared/_SelectLanguagePartial. cshtml* se přidá do `footer` oddílu souboru rozložení, takže bude k dispozici pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="60dbb-361">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="60dbb-362">`SetLanguage`Metoda nastaví soubor cookie jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-362">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="60dbb-363">Nemůžete připojit *_SelectLanguagePartial. cshtml* k ukázkovému kódu pro tento projekt.</span><span class="sxs-lookup"><span data-stu-id="60dbb-363">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="60dbb-364">Projekt **Localization. StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) obsahuje kód pro tok `RequestLocalizationOptions` do Razor částečného toku prostřednictvím kontejneru pro [vkládání závislostí](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="60dbb-364">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="60dbb-365">Vazba modelu data směrování a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="60dbb-365">Model binding route data and query strings</span></span>

<span data-ttu-id="60dbb-366">Podívejte [se na téma chování globalizace modelu vazby dat trasy a řetězce dotazu](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="60dbb-366">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="60dbb-367">Výrazy globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="60dbb-367">Globalization and localization terms</span></span>

<span data-ttu-id="60dbb-368">Proces lokalizace vaší aplikace také vyžaduje základní znalosti relevantních znakových sad, které se běžně používají při vývoji moderního softwaru, a porozumění problémům, které s nimi souvisejí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-368">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="60dbb-369">I když všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text s různými čísly.</span><span class="sxs-lookup"><span data-stu-id="60dbb-369">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="60dbb-370">Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-370">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="60dbb-371">[Lokalizace](/dotnet/standard/globalization-localization/localizability-review) je přechodný proces pro ověření, že globální aplikace je připravená na lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="60dbb-371">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="60dbb-372">Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název jazykové verze je `<languagecode2>-<country/regioncode2>` , kde `<languagecode2>` je kód jazyka a `<country/regioncode2>` je kód subkultury.</span><span class="sxs-lookup"><span data-stu-id="60dbb-372">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="60dbb-373">Například `es-CL` pro španělštinu (Chile), `en-US` pro angličtinu (USA) a `en-AU` pro angličtinu (Austrálie).</span><span class="sxs-lookup"><span data-stu-id="60dbb-373">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="60dbb-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinací kódu kultury ISO 639 2 s malým písmenem, který je přidružený k jazyku, a kódu subkultury na velká písmena ISO 3166 2, který je přidružený k zemi nebo oblasti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="60dbb-375">Viz [název jazykové verze](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="60dbb-375">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="60dbb-376">Mezinárodní využití se často zkracuje na "I18N".</span><span class="sxs-lookup"><span data-stu-id="60dbb-376">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="60dbb-377">Zkratka používá první a poslední písmena a počet písmen mezi nimi, takže 18 představuje počet písmen mezi první I a poslední znak "N".</span><span class="sxs-lookup"><span data-stu-id="60dbb-377">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="60dbb-378">Totéž platí pro globalizaci (G11N) a lokalizaci (L10N).</span><span class="sxs-lookup"><span data-stu-id="60dbb-378">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="60dbb-379">Uvedenými</span><span class="sxs-lookup"><span data-stu-id="60dbb-379">Terms:</span></span>

* <span data-ttu-id="60dbb-380">Globalizace (G11N): proces vytvoření aplikace, která podporuje různé jazyky a oblasti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-380">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="60dbb-381">Lokalizace (L10N): proces přizpůsobení aplikace pro daný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-381">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="60dbb-382">Mezinárodní (I18N): popisuje globalizaci a lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="60dbb-382">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="60dbb-383">Jazyková verze: Jedná se o jazyk a volitelně i oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-383">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="60dbb-384">Neutrální jazyková verze: jazyková verze, která má zadaný jazyk, ale ne oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-384">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="60dbb-385">(například "en", "ES")</span><span class="sxs-lookup"><span data-stu-id="60dbb-385">(for example "en", "es")</span></span>
* <span data-ttu-id="60dbb-386">Specifická jazyková verze: jazyková verze, která má zadaný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-386">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="60dbb-387">(například "en-US", "en-GB", "ES-CL")</span><span class="sxs-lookup"><span data-stu-id="60dbb-387">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="60dbb-388">Nadřazená jazyková verze: neutrální jazyková verze, která obsahuje konkrétní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-388">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="60dbb-389">(například "en" je nadřazená jazyková verze "en-US" a "en-GB")</span><span class="sxs-lookup"><span data-stu-id="60dbb-389">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="60dbb-390">Národní prostředí: národní prostředí je stejné jako jazyková verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-390">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="60dbb-391">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="60dbb-391">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="60dbb-392">[StarterWeb projekt Localization](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) , který se používá v článku.</span><span class="sxs-lookup"><span data-stu-id="60dbb-392">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="60dbb-393">Globalizace a lokalizace aplikací .NET</span><span class="sxs-lookup"><span data-stu-id="60dbb-393">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="60dbb-394">Prostředky v souborech. resx</span><span class="sxs-lookup"><span data-stu-id="60dbb-394">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="60dbb-395">Sada nástrojů pro vícejazyčné aplikace od Microsoftu</span><span class="sxs-lookup"><span data-stu-id="60dbb-395">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="60dbb-396">Lokalizace & – obecné typy</span><span class="sxs-lookup"><span data-stu-id="60dbb-396">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="60dbb-397">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)a [Hisham bin](https://twitter.com/hishambinateya) Ateya</span><span class="sxs-lookup"><span data-stu-id="60dbb-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="60dbb-398">Vícejazyčný web umožňuje webu oslovit širší cílovou skupinu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-398">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="60dbb-399">ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.</span><span class="sxs-lookup"><span data-stu-id="60dbb-399">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="60dbb-400">Mezinárodní využití zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="60dbb-400">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="60dbb-401">Globalizace je proces návrhu aplikací, které podporují různé jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-401">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="60dbb-402">Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují na konkrétní geografické oblasti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-402">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="60dbb-403">Lokalizace je proces přizpůsobení globální aplikace, kterou jste již zpracovali pro lokalizaci, do konkrétní jazykové verze nebo národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-403">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="60dbb-404">Další informace najdete v tématu **předpoklady globalizace a lokalizace** poblíž konce tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-404">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="60dbb-405">Lokalizace aplikace zahrnuje následující:</span><span class="sxs-lookup"><span data-stu-id="60dbb-405">App localization involves the following:</span></span>

1. <span data-ttu-id="60dbb-406">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="60dbb-406">Make the app's content localizable</span></span>
1. <span data-ttu-id="60dbb-407">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="60dbb-407">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="60dbb-408">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="60dbb-408">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="60dbb-409">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="60dbb-409">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="60dbb-410">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="60dbb-410">Make the app's content localizable</span></span>

<span data-ttu-id="60dbb-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' nevyžaduje uložení výchozích řetězců jazyka do souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="60dbb-412">Můžete vyvíjet aplikaci zaměřenou na lokalizaci a nemusíte vytvářet soubory prostředků na začátku ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="60dbb-412">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="60dbb-413">Následující kód ukazuje, jak zabalit řetězec "About title" pro lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="60dbb-413">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="60dbb-414">V předchozím kódu `IStringLocalizer<T>` implementace pochází z [Injektáže závislosti](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="60dbb-414">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="60dbb-415">Pokud se nenalezne lokalizovaná hodnota "o titulku", vrátí se klíč indexeru, tj. řetězec "About title".</span><span class="sxs-lookup"><span data-stu-id="60dbb-415">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="60dbb-416">Můžete ponechat výchozí řetězcové literály v aplikaci a zabalit je do lokalizátora, abyste se mohli soustředit na vývoj aplikace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-416">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="60dbb-417">Vyvíjíte svou aplikaci pomocí výchozího jazyka a připravíte ji pro krok lokalizace bez prvotního vytvoření výchozího souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-417">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="60dbb-418">Alternativně můžete použít tradiční přístup a zadat klíč pro načtení výchozího řetězce jazyka.</span><span class="sxs-lookup"><span data-stu-id="60dbb-418">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="60dbb-419">Pro mnoho vývojářů nový pracovní postup, který nemá výchozí jazyk *. resx* soubor, a jednoduše zabalí řetězcové literály může snížit režii lokalizace aplikace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-419">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="60dbb-420">Ostatní vývojáři budou preferovat tradiční pracovní postup, protože mohou usnadnit práci s delšími řetězcovými literály a usnadňují aktualizaci lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="60dbb-420">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="60dbb-421">Použijte `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují kód HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-421">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="60dbb-422">`IHtmlLocalizer`HTML kóduje argumenty, které jsou formátovány v řetězci prostředků, ale nekóduje kód HTML samotný řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="60dbb-422">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="60dbb-423">V ukázce zvýrazněné níže je pouze hodnota `name` parametru kódována HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-423">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="60dbb-424">**Poznámka:** Obecně chcete lokalizovat pouze text a nikoli HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-424">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="60dbb-425">Na nejnižší úrovni můžete obdržet zástupné `IStringLocalizerFactory` [vkládání závislostí](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="60dbb-425">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="60dbb-426">Výše uvedený kód ukazuje každou ze dvou metod Create Factory.</span><span class="sxs-lookup"><span data-stu-id="60dbb-426">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="60dbb-427">Lokalizované řetězce můžete rozdělit do oddílů podle řadiče, oblasti nebo pouze jednoho kontejneru.</span><span class="sxs-lookup"><span data-stu-id="60dbb-427">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="60dbb-428">V ukázkové aplikaci se `SharedResource` pro sdílené prostředky používá fiktivní třída s názvem.</span><span class="sxs-lookup"><span data-stu-id="60dbb-428">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="60dbb-429">Někteří vývojáři používají `Startup` třídu pro zahrnutí globálních nebo sdílených řetězců.</span><span class="sxs-lookup"><span data-stu-id="60dbb-429">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="60dbb-430">V níže uvedené ukázce se `InfoController` `SharedResource` používají a Localize:</span><span class="sxs-lookup"><span data-stu-id="60dbb-430">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="60dbb-431">Lokalizace zobrazení</span><span class="sxs-lookup"><span data-stu-id="60dbb-431">View localization</span></span>

<span data-ttu-id="60dbb-432">`IViewLocalizer`Služba poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="60dbb-432">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="60dbb-433">`ViewLocalizer`Třída implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-433">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="60dbb-434">Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-434">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="60dbb-435">Výchozí implementace nástroje `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-435">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="60dbb-436">Neexistuje možnost použít globální sdílený soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-436">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="60dbb-437">`ViewLocalizer`implementuje lokalizátora pomocí `IHtmlLocalizer` , takže Razor nekóduje kód HTML jako lokalizovaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="60dbb-437">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="60dbb-438">Můžete parametrizovat řetězce prostředků a `IViewLocalizer` zakódovat parametry HTML, ale ne řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="60dbb-438">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="60dbb-439">Vezměte v úvahu následující Razor značky:</span><span class="sxs-lookup"><span data-stu-id="60dbb-439">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="60dbb-440">Soubor prostředků francouzštiny může obsahovat následující:</span><span class="sxs-lookup"><span data-stu-id="60dbb-440">A French resource file could contain the following:</span></span>

| <span data-ttu-id="60dbb-441">Klíč</span><span class="sxs-lookup"><span data-stu-id="60dbb-441">Key</span></span> | <span data-ttu-id="60dbb-442">Hodnota</span><span class="sxs-lookup"><span data-stu-id="60dbb-442">Value</span></span> |
| ----- | ---
<span data-ttu-id="60dbb-443">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-443">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-444">'Blazor'</span></span>
- <span data-ttu-id="60dbb-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-445">'Identity'</span></span>
- <span data-ttu-id="60dbb-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-447">'Razor'</span></span>
- <span data-ttu-id="60dbb-448">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-448">'SignalR' uid:</span></span> 

<span data-ttu-id="60dbb-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="60dbb-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="60dbb-450">Vykreslené zobrazení obsahuje značku HTML ze souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-450">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="60dbb-451">**Poznámka:** Obecně chcete lokalizovat pouze text a nikoli HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-451">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="60dbb-452">Chcete-li použít sdílený soubor prostředků v zobrazení, vložení `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-452">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="60dbb-453">Lokalizace DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="60dbb-453">DataAnnotations localization</span></span>

<span data-ttu-id="60dbb-454">Chybové zprávy pro dataanotace jsou lokalizovány pomocí `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-454">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="60dbb-455">Pomocí možnosti `ResourcesPath = "Resources"` mohou být chybové zprávy v nástroji `RegisterViewModel` uloženy v jedné z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="60dbb-455">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="60dbb-456">*Resources/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="60dbb-457">*Prostředky/ViewModels/účet/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="60dbb-458">V ASP.NET Core MVC 1.1.0 a vyšší jsou lokalizovány neověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="60dbb-458">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="60dbb-459">ASP.NET Core MVC 1,0 **nehledá lokalizované** řetězce pro atributy, které nejsou ověřovány.</span><span class="sxs-lookup"><span data-stu-id="60dbb-459">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="60dbb-460">Použití jednoho řetězce prostředku pro více tříd</span><span class="sxs-lookup"><span data-stu-id="60dbb-460">Using one resource string for multiple classes</span></span>

<span data-ttu-id="60dbb-461">Následující kód ukazuje, jak použít jeden řetězec prostředku pro atributy ověřování s více třídami:</span><span class="sxs-lookup"><span data-stu-id="60dbb-461">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="60dbb-462">V předchozím kódu `SharedResource` je třída odpovídající RESX, kde jsou uloženy ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="60dbb-462">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="60dbb-463">S tímto přístupem budou dataanotace používat pouze `SharedResource` místo prostředků pro každou třídu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-463">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="60dbb-464">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="60dbb-464">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="60dbb-465">SupportedCultures a SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="60dbb-465">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="60dbb-466">ASP.NET Core umožňuje zadat dvě hodnoty jazykové verze `SupportedCultures` a `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-466">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="60dbb-467">Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) pro `SupportedCultures` určuje výsledky funkcí závislých na jazykové verzi, jako je datum, čas, číslo a formátování měny.</span><span class="sxs-lookup"><span data-stu-id="60dbb-467">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="60dbb-468">`SupportedCultures`Určuje také pořadí řazení textu, konvencí velikosti písmen a porovnávání řetězců.</span><span class="sxs-lookup"><span data-stu-id="60dbb-468">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="60dbb-469">Další informace o tom, jak server získá jazykovou verzi, naleznete v tématu [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="60dbb-469">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="60dbb-470">`SupportedUICultures`Určuje, které přeložené řetězce (ze souborů *. resx* ) jsou vyhledány správcem [prostředků](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="60dbb-470">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="60dbb-471">`ResourceManager`Jednoduše vyhledá řetězce specifické pro jazykovou verzi, které určuje `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-471">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="60dbb-472">Každé vlákno v rozhraní .NET má `CurrentCulture` a `CurrentUICulture` objekty.</span><span class="sxs-lookup"><span data-stu-id="60dbb-472">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="60dbb-473">ASP.NET Core tyto hodnoty kontroluje při vykreslování funkcí závislých na jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-473">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="60dbb-474">Pokud je například jazyková verze aktuálního vlákna nastavena na "en-US" (angličtina, USA), `DateTime.Now.ToLongDateString()` zobrazí "čtvrtek, 18. února 2016", ale pokud `CurrentCulture` je nastavená na "ES-ES" (španělština, Španělsko), bude výstup "Jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="60dbb-474">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="60dbb-475">Soubory prostředků</span><span class="sxs-lookup"><span data-stu-id="60dbb-475">Resource files</span></span>

<span data-ttu-id="60dbb-476">Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců z kódu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-476">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="60dbb-477">Přeložené řetězce pro jazyk, který není výchozí, jsou izolované v souborech prostředků *. resx* .</span><span class="sxs-lookup"><span data-stu-id="60dbb-477">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="60dbb-478">Například může být vhodné vytvořit soubor prostředků španělštiny s názvem *Welcome. ES. resx* obsahující přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="60dbb-478">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="60dbb-479">"ES" je kód jazyka pro španělštinu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-479">"es" is the language code for Spanish.</span></span> <span data-ttu-id="60dbb-480">Postup vytvoření tohoto souboru prostředků v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="60dbb-480">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="60dbb-481">V **Průzkumník řešení**klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-481">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Vnořená kontextová nabídka: v Průzkumník řešení je místní nabídka pro prostředky otevřená.](localization/_static/newi.png)

2. <span data-ttu-id="60dbb-484">V poli **Vyhledat nainstalované šablony** zadejte "Resource" a soubor pojmenujte.</span><span class="sxs-lookup"><span data-stu-id="60dbb-484">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Dialogové okno Přidat novou položku](localization/_static/res.png)

3. <span data-ttu-id="60dbb-486">Do sloupce **název** zadejte hodnotu klíče (nativní řetězec) a přeložený řetězec ve sloupci Value ( **hodnota** ).</span><span class="sxs-lookup"><span data-stu-id="60dbb-486">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome. ES. resx soubor (soubor prostředků Welcome pro španělštinu) se slovem Hello ve sloupci název a slovo Hola (text Hello v španělštině) ve sloupci Hodnota](localization/_static/hola.png)

    <span data-ttu-id="60dbb-488">Sada Visual Studio zobrazí *uvítací soubor. ES. resx* .</span><span class="sxs-lookup"><span data-stu-id="60dbb-488">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Průzkumník řešení se zobrazuje soubor prostředků nástroje Welcome španělština (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="60dbb-490">Pojmenovávání souborů prostředků</span><span class="sxs-lookup"><span data-stu-id="60dbb-490">Resource file naming</span></span>

<span data-ttu-id="60dbb-491">Prostředky jsou pojmenovány pro úplný název typu své třídy minus název sestavení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-491">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="60dbb-492">Například francouzská prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu, `LocalizationWebsite.Web.Startup` by měla být pojmenována *Startup. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-492">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="60dbb-493">Prostředek pro třídu by měl `LocalizationWebsite.Web.Controllers.HomeController` pojmenovat *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-493">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-494">Pokud cílový obor názvů třídy není stejný jako název sestavení, budete potřebovat úplný název typu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-494">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="60dbb-495">Například v ukázkovém projektu by měl prostředek pro typ `ExtraNamespace.Tools` název *ExtraNamespace. Tools. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-495">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="60dbb-496">V ukázkovém projektu `ConfigureServices` Metoda nastaví `ResourcesPath` na "prostředky", takže relativní cesta projektu pro francouzský soubor prostředků domovského řadiče je *Resources/Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-496">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-497">Případně můžete použít složky k uspořádání souborů prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-497">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="60dbb-498">V případě domovského kontroleru by tato cesta byla *Resources/Controllers/HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-498">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-499">Pokud tuto možnost nepoužijete `ResourcesPath` , soubor *. resx* by přešel do základního adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-499">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="60dbb-500">Soubor prostředků pro `HomeController` by měl mít název *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-500">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-501">Volba použití konvence pojmenování teček nebo Path závisí na tom, jak chcete uspořádat soubory prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-501">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="60dbb-502">Název prostředku</span><span class="sxs-lookup"><span data-stu-id="60dbb-502">Resource name</span></span> | <span data-ttu-id="60dbb-503">Pojmenování teček nebo Path</span><span class="sxs-lookup"><span data-stu-id="60dbb-503">Dot or path naming</span></span> |
| ---
<span data-ttu-id="60dbb-504">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-504">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-505">'Blazor'</span></span>
- <span data-ttu-id="60dbb-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-506">'Identity'</span></span>
- <span data-ttu-id="60dbb-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-508">'Razor'</span></span>
- <span data-ttu-id="60dbb-509">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-510">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-510">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-511">'Blazor'</span></span>
- <span data-ttu-id="60dbb-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-512">'Identity'</span></span>
- <span data-ttu-id="60dbb-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-514">'Razor'</span></span>
- <span data-ttu-id="60dbb-515">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-516">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-516">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-517">'Blazor'</span></span>
- <span data-ttu-id="60dbb-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-518">'Identity'</span></span>
- <span data-ttu-id="60dbb-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-520">'Razor'</span></span>
- <span data-ttu-id="60dbb-521">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-522">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-522">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-523">'Blazor'</span></span>
- <span data-ttu-id="60dbb-524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-524">'Identity'</span></span>
- <span data-ttu-id="60dbb-525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-525">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-526">'Razor'</span></span>
- <span data-ttu-id="60dbb-527">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-527">'SignalR' uid:</span></span> 

<span data-ttu-id="60dbb-528">------   | ---Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-529">'Blazor'</span></span>
- <span data-ttu-id="60dbb-530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-530">'Identity'</span></span>
- <span data-ttu-id="60dbb-531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-531">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-532">'Razor'</span></span>
- <span data-ttu-id="60dbb-533">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-533">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-534">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-534">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-535">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-535">'Blazor'</span></span>
- <span data-ttu-id="60dbb-536">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-536">'Identity'</span></span>
- <span data-ttu-id="60dbb-537">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-537">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-538">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-538">'Razor'</span></span>
- <span data-ttu-id="60dbb-539">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-539">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-540">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-540">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-541">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-541">'Blazor'</span></span>
- <span data-ttu-id="60dbb-542">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-542">'Identity'</span></span>
- <span data-ttu-id="60dbb-543">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-543">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-544">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-544">'Razor'</span></span>
- <span data-ttu-id="60dbb-545">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-545">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-546">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-546">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-547">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-547">'Blazor'</span></span>
- <span data-ttu-id="60dbb-548">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-548">'Identity'</span></span>
- <span data-ttu-id="60dbb-549">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-549">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-550">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-550">'Razor'</span></span>
- <span data-ttu-id="60dbb-551">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-551">'SignalR' uid:</span></span> 

<span data-ttu-id="60dbb-552">------- | | Prostředky/řadiče. HomeController. fr. resx | Tečka | | Prostředky/řadiče/HomeController. fr. resx | Cesta | |    |     |</span><span class="sxs-lookup"><span data-stu-id="60dbb-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="60dbb-553">Soubory prostředků používané `@inject IViewLocalizer` v Razor zobrazeních následují podobně jako vzor.</span><span class="sxs-lookup"><span data-stu-id="60dbb-553">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="60dbb-554">Soubor prostředků pro zobrazení může být pojmenován buď pomocí názvu tečky, nebo pojmenování cesty.</span><span class="sxs-lookup"><span data-stu-id="60dbb-554">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="60dbb-555">zobrazení soubory prostředků napodobá cestě k jejich přidruženému souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-555"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="60dbb-556">Za předpokladu, že nastavíme `ResourcesPath` "prostředky", soubor francouzského prostředku přidružený k zobrazením */domů/o zobrazení. cshtml* může být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="60dbb-556">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="60dbb-557">Prostředky/zobrazení/domů/o. fr. resx</span><span class="sxs-lookup"><span data-stu-id="60dbb-557">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="60dbb-558">Prostředky/zobrazení. domů. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="60dbb-558">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="60dbb-559">Pokud tuto možnost nepoužijete `ResourcesPath` , bude soubor *. resx* pro zobrazení umístěn ve stejné složce jako zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-559">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="60dbb-560">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="60dbb-560">RootNamespaceAttribute</span></span> 

<span data-ttu-id="60dbb-561">Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-561">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="60dbb-562">K tomu může dojít, když název projektu není platným identifikátorem .NET.</span><span class="sxs-lookup"><span data-stu-id="60dbb-562">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="60dbb-563">V případě instance `my-project-name.csproj` bude použit kořenový obor názvů `my_project_name` a název sestavení, `my-project-name` který vede k této chybě.</span><span class="sxs-lookup"><span data-stu-id="60dbb-563">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="60dbb-564">Pokud se kořenový obor názvů sestavení liší od názvu sestavení:</span><span class="sxs-lookup"><span data-stu-id="60dbb-564">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="60dbb-565">Lokalizace ve výchozím nastavení nefunguje.</span><span class="sxs-lookup"><span data-stu-id="60dbb-565">Localization does not work by default.</span></span>
* <span data-ttu-id="60dbb-566">Lokalizace se nezdařila z důvodu způsobu hledání prostředků v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-566">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="60dbb-567">`RootNamespace`je hodnota doby sestavení, která není k dispozici pro spuštěný proces.</span><span class="sxs-lookup"><span data-stu-id="60dbb-567">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="60dbb-568">Pokud se `RootNamespace` liší od z `AssemblyName` , zahrňte následující v *AssemblyInfo.cs* (s hodnotami parametrů nahrazenými skutečnými hodnotami):</span><span class="sxs-lookup"><span data-stu-id="60dbb-568">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="60dbb-569">Předchozí kód umožňuje úspěšné vyřešení souborů RESX.</span><span class="sxs-lookup"><span data-stu-id="60dbb-569">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="60dbb-570">Chování záložního kultivačního prostředí</span><span class="sxs-lookup"><span data-stu-id="60dbb-570">Culture fallback behavior</span></span>

<span data-ttu-id="60dbb-571">Při hledání prostředku se lokalizace zapojit do "záložní kultury".</span><span class="sxs-lookup"><span data-stu-id="60dbb-571">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="60dbb-572">Od požadované jazykové verze, pokud se nenajde, se vrátí k nadřazené jazykové verzi této jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-572">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="60dbb-573">Kromě toho, vlastnost [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) představuje nadřazenou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-573">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="60dbb-574">To je obvykle (ale ne vždy) znamená odebrání národního přízpůsobu z ISO.</span><span class="sxs-lookup"><span data-stu-id="60dbb-574">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="60dbb-575">Například dialekt španělštiny ve španělštině, který se používá v Mexiku, je ES-MX.</span><span class="sxs-lookup"><span data-stu-id="60dbb-575">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="60dbb-576">Má nadřízenou španělštinu ES, která &mdash; není specifická pro žádnou zemi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-576">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="60dbb-577">Představte si, že váš web obdrží žádost o "úvodní" prostředek pomocí kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="60dbb-577">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="60dbb-578">Systém lokalizace vyhledá následující prostředky v uvedeném pořadí a vybere první shodu:</span><span class="sxs-lookup"><span data-stu-id="60dbb-578">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="60dbb-579">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-579">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="60dbb-580">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-580">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="60dbb-581">*Welcome. resx* (Pokud `NeutralResourcesLanguage` je to "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="60dbb-581">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="60dbb-582">Pokud například odeberete označení jazykové verze ". fr" a máte nastavenou jazykovou verzi na francouzštinu, je výchozí soubor prostředků přečten a jsou lokalizovány řetězce.</span><span class="sxs-lookup"><span data-stu-id="60dbb-582">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="60dbb-583">Správce prostředků určí výchozí nebo záložní prostředek, pokud nic nevyhovuje vaší požadované jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-583">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="60dbb-584">Pokud chcete vrátit klíč pouze v případě, že chybí prostředek pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-584">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="60dbb-585">Generování souborů prostředků pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60dbb-585">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="60dbb-586">Vytvoříte-li soubor prostředků v aplikaci Visual Studio bez jazykové verze v názvu souboru (například *Welcome. resx*), sada Visual Studio vytvoří třídu jazyka C# s vlastností pro každý řetězec.</span><span class="sxs-lookup"><span data-stu-id="60dbb-586">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="60dbb-587">To obvykle není to, co chcete s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60dbb-587">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="60dbb-588">Obvykle nemáte výchozí soubor prostředků *. resx* (soubor *. resx* bez názvu jazykové verze).</span><span class="sxs-lookup"><span data-stu-id="60dbb-588">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="60dbb-589">Návrh souboru *. resx* doporučujeme vytvořit s názvem jazykové verze (například *Welcome. fr. resx*).</span><span class="sxs-lookup"><span data-stu-id="60dbb-589">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="60dbb-590">Při vytváření souboru *. resx* s názvem jazykové verze aplikace Visual Studio negeneruje soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="60dbb-590">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="60dbb-591">Přidat další jazykové verze</span><span class="sxs-lookup"><span data-stu-id="60dbb-591">Add other cultures</span></span>

<span data-ttu-id="60dbb-592">Každá kombinace jazyka a jazykové verze (Kromě výchozího jazyka) vyžaduje jedinečný soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-592">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="60dbb-593">Soubory prostředků můžete vytvořit pro různé kultury a národní prostředí vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-US**, **fr-CA**a **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="60dbb-593">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="60dbb-594">Tyto kódy ISO jsou umístěné mezi názvem souboru a příponou souboru *. resx* , jako v *Welcome.ES-MX. resx* (španělština/Mexiko).</span><span class="sxs-lookup"><span data-stu-id="60dbb-594">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="60dbb-595">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="60dbb-595">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="60dbb-596">Konfigurace lokalizace</span><span class="sxs-lookup"><span data-stu-id="60dbb-596">Configure localization</span></span>

<span data-ttu-id="60dbb-597">Lokalizace je nakonfigurovaná v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="60dbb-597">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="60dbb-598">`AddLocalization`Přidá služby lokalizace do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="60dbb-598">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="60dbb-599">Výše uvedený kód také nastaví cestu prostředků na prostředky.</span><span class="sxs-lookup"><span data-stu-id="60dbb-599">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="60dbb-600">`AddViewLocalization`Přidá podporu pro lokalizované soubory zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-600">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="60dbb-601">V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-601">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="60dbb-602">Například "fr" v souboru *index. fr. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="60dbb-602">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="60dbb-603">`AddDataAnnotationsLocalization`Přidává podporu pro lokalizované `DataAnnotations` ověřovací zprávy prostřednictvím `IStringLocalizer` abstrakcí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-603">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="60dbb-604">Middleware lokalizace</span><span class="sxs-lookup"><span data-stu-id="60dbb-604">Localization middleware</span></span>

<span data-ttu-id="60dbb-605">Aktuální jazyková verze v požadavku je nastavena v [middleware](xref:fundamentals/middleware/index)lokalizace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-605">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="60dbb-606">Middleware lokalizace je povolená v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="60dbb-606">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="60dbb-607">Middleware lokalizace musí být konfigurovány před jakýmkoli middlewarem, který by mohl kontrolovat jazykovou verzi žádosti (například `app.UseMvcWithDefaultRoute()` ).</span><span class="sxs-lookup"><span data-stu-id="60dbb-607">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="60dbb-608">`UseRequestLocalization`Inicializuje `RequestLocalizationOptions` objekt.</span><span class="sxs-lookup"><span data-stu-id="60dbb-608">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="60dbb-609">Při každém požadavku se vypíše seznam `RequestCultureProvider` ve `RequestLocalizationOptions` výčtu a první zprostředkovatel, který dokáže úspěšně určit jazykovou verzi žádosti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-609">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="60dbb-610">Výchozí zprostředkovatelé přicházejí z `RequestLocalizationOptions` třídy:</span><span class="sxs-lookup"><span data-stu-id="60dbb-610">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="60dbb-611">Výchozí seznam bude z nejpřesnější, aby byl nejméně specifický.</span><span class="sxs-lookup"><span data-stu-id="60dbb-611">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="60dbb-612">Později v článku se dozvíte, jak můžete změnit pořadí a dokonce přidat vlastního poskytovatele jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-612">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="60dbb-613">Pokud žádný z poskytovatelů nemůže určit jazykovou verzi žádosti, `DefaultRequestCulture` je použita.</span><span class="sxs-lookup"><span data-stu-id="60dbb-613">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="60dbb-614">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="60dbb-614">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="60dbb-615">Některé aplikace budou používat řetězec dotazu k nastavení jazykové verze [a jazykové verze uživatelského rozhraní](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="60dbb-615">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="60dbb-616">Pro aplikace, které používají přístup k hlavičkám souborů cookie nebo Accept-Language, je přidání řetězce dotazu na adresu URL užitečné pro ladění a testování kódu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-616">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="60dbb-617">Ve výchozím nastavení `QueryStringRequestCultureProvider` je jako první poskytovatel lokalizace v `RequestCultureProvider` seznamu zaregistrován.</span><span class="sxs-lookup"><span data-stu-id="60dbb-617">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="60dbb-618">Předáte parametry řetězce dotazu `culture` a `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-618">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="60dbb-619">Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělština/Mexiko:</span><span class="sxs-lookup"><span data-stu-id="60dbb-619">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="60dbb-620">Pokud předáte pouze jeden z těchto dvou ( `culture` nebo `ui-culture` ), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí toho, kterou jste předali.</span><span class="sxs-lookup"><span data-stu-id="60dbb-620">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="60dbb-621">Například nastavení pouze jazyková verze nastaví jak `Culture` a `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-621">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="60dbb-622">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="60dbb-622">CookieRequestCultureProvider</span></span>

<span data-ttu-id="60dbb-623">Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze pomocí souboru cookie ASP.NET Core jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-623">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="60dbb-624">Pomocí `MakeCookieValue` metody vytvořte soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="60dbb-624">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="60dbb-625">`CookieRequestCultureProvider` `DefaultCookieName` Vrátí výchozí název souboru cookie, který se používá ke sledování informací o preferované jazykové verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="60dbb-625">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="60dbb-626">Výchozí název souboru cookie je `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-626">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="60dbb-627">Formát souboru cookie je `c=%LANGCODE%|uic=%LANGCODE%` , kde `c` je `Culture` a `uic` je například `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-627">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="60dbb-628">Pokud zadáte pouze jednu z informací o jazykové verzi a jazykovou verzi uživatelského rozhraní, zadaná jazyková verze bude použita pro informace o jazykové verzi i jazyková verze uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="60dbb-628">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="60dbb-629">Hlavička protokolu HTTP pro přijetí – jazyk</span><span class="sxs-lookup"><span data-stu-id="60dbb-629">The Accept-Language HTTP header</span></span>

<span data-ttu-id="60dbb-630">[Záhlaví Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) lze nastavit ve většině prohlížečů a původně bylo určeno pro určení jazyka uživatele.</span><span class="sxs-lookup"><span data-stu-id="60dbb-630">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="60dbb-631">Toto nastavení indikuje, co je v prohlížeči nastavené na Odeslat nebo zděděné z podkladového operačního systému.</span><span class="sxs-lookup"><span data-stu-id="60dbb-631">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="60dbb-632">Hlavička protokolu HTTP Accept-Language z požadavku prohlížeče není infallible způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="60dbb-632">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="60dbb-633">Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu kultury.</span><span class="sxs-lookup"><span data-stu-id="60dbb-633">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="60dbb-634">Nastavení hlavičky protokolu HTTP Accept-Language v IE</span><span class="sxs-lookup"><span data-stu-id="60dbb-634">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="60dbb-635">Z ikony ozubeného kolečka klepněte na **Možnosti Internetu**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-635">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="60dbb-636">Klepněte na **jazyky**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-636">Tap **Languages**.</span></span>

    ![Možnosti Internetu](localization/_static/lang.png)

3. <span data-ttu-id="60dbb-638">Klepněte na **nastavit jazykové předvolby**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-638">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="60dbb-639">Klepněte na **Přidat jazyk**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-639">Tap **Add a language**.</span></span>

5. <span data-ttu-id="60dbb-640">Přidejte jazyk.</span><span class="sxs-lookup"><span data-stu-id="60dbb-640">Add the language.</span></span>

6. <span data-ttu-id="60dbb-641">Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-641">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="60dbb-642">Použití vlastního zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="60dbb-642">Use a custom provider</span></span>

<span data-ttu-id="60dbb-643">Předpokládejme, že chcete, aby vaši zákazníci mohli ukládat svůj jazyk a jazykovou verzi do databází.</span><span class="sxs-lookup"><span data-stu-id="60dbb-643">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="60dbb-644">Můžete napsat poskytovatele a vyhledat tyto hodnoty pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="60dbb-644">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="60dbb-645">Následující kód ukazuje, jak přidat vlastního zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="60dbb-645">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="60dbb-646">Použijte `RequestLocalizationOptions` k přidání nebo odebrání poskytovatelů lokalizace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-646">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="60dbb-647">Programové nastavení kultury</span><span class="sxs-lookup"><span data-stu-id="60dbb-647">Set the culture programmatically</span></span>

<span data-ttu-id="60dbb-648">Tento ukázkový projekt **Localization. StarterWeb** na [GITHUBU](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-648">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="60dbb-649">Soubor *views/Shared/_SelectLanguagePartial. cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných jazykových verzí:</span><span class="sxs-lookup"><span data-stu-id="60dbb-649">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="60dbb-650">Soubor *views/Shared/_SelectLanguagePartial. cshtml* se přidá do `footer` oddílu souboru rozložení, takže bude k dispozici pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="60dbb-650">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="60dbb-651">`SetLanguage`Metoda nastaví soubor cookie jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-651">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="60dbb-652">Nemůžete připojit *_SelectLanguagePartial. cshtml* k ukázkovému kódu pro tento projekt.</span><span class="sxs-lookup"><span data-stu-id="60dbb-652">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="60dbb-653">Projekt **Localization. StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) obsahuje kód pro tok `RequestLocalizationOptions` do Razor částečného toku prostřednictvím kontejneru pro [vkládání závislostí](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="60dbb-653">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="60dbb-654">Vazba modelu data směrování a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="60dbb-654">Model binding route data and query strings</span></span>

<span data-ttu-id="60dbb-655">Podívejte [se na téma chování globalizace modelu vazby dat trasy a řetězce dotazu](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="60dbb-655">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="60dbb-656">Výrazy globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="60dbb-656">Globalization and localization terms</span></span>

<span data-ttu-id="60dbb-657">Proces lokalizace vaší aplikace také vyžaduje základní znalosti relevantních znakových sad, které se běžně používají při vývoji moderního softwaru, a porozumění problémům, které s nimi souvisejí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-657">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="60dbb-658">I když všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text s různými čísly.</span><span class="sxs-lookup"><span data-stu-id="60dbb-658">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="60dbb-659">Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-659">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="60dbb-660">[Lokalizace](/dotnet/standard/globalization-localization/localizability-review) je přechodný proces pro ověření, že globální aplikace je připravená na lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="60dbb-660">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="60dbb-661">Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název jazykové verze je `<languagecode2>-<country/regioncode2>` , kde `<languagecode2>` je kód jazyka a `<country/regioncode2>` je kód subkultury.</span><span class="sxs-lookup"><span data-stu-id="60dbb-661">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="60dbb-662">Například `es-CL` pro španělštinu (Chile), `en-US` pro angličtinu (USA) a `en-AU` pro angličtinu (Austrálie).</span><span class="sxs-lookup"><span data-stu-id="60dbb-662">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="60dbb-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinací kódu kultury ISO 639 2 s malým písmenem, který je přidružený k jazyku, a kódu subkultury na velká písmena ISO 3166 2, který je přidružený k zemi nebo oblasti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="60dbb-664">Viz [název jazykové verze](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="60dbb-664">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="60dbb-665">Mezinárodní využití se často zkracuje na "I18N".</span><span class="sxs-lookup"><span data-stu-id="60dbb-665">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="60dbb-666">Zkratka používá první a poslední písmena a počet písmen mezi nimi, takže 18 představuje počet písmen mezi první I a poslední znak "N".</span><span class="sxs-lookup"><span data-stu-id="60dbb-666">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="60dbb-667">Totéž platí pro globalizaci (G11N) a lokalizaci (L10N).</span><span class="sxs-lookup"><span data-stu-id="60dbb-667">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="60dbb-668">Uvedenými</span><span class="sxs-lookup"><span data-stu-id="60dbb-668">Terms:</span></span>

* <span data-ttu-id="60dbb-669">Globalizace (G11N): proces vytvoření aplikace, která podporuje různé jazyky a oblasti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-669">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="60dbb-670">Lokalizace (L10N): proces přizpůsobení aplikace pro daný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-670">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="60dbb-671">Mezinárodní (I18N): popisuje globalizaci a lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="60dbb-671">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="60dbb-672">Jazyková verze: Jedná se o jazyk a volitelně i oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-672">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="60dbb-673">Neutrální jazyková verze: jazyková verze, která má zadaný jazyk, ale ne oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-673">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="60dbb-674">(například "en", "ES")</span><span class="sxs-lookup"><span data-stu-id="60dbb-674">(for example "en", "es")</span></span>
* <span data-ttu-id="60dbb-675">Specifická jazyková verze: jazyková verze, která má zadaný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-675">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="60dbb-676">(například "en-US", "en-GB", "ES-CL")</span><span class="sxs-lookup"><span data-stu-id="60dbb-676">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="60dbb-677">Nadřazená jazyková verze: neutrální jazyková verze, která obsahuje konkrétní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-677">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="60dbb-678">(například "en" je nadřazená jazyková verze "en-US" a "en-GB")</span><span class="sxs-lookup"><span data-stu-id="60dbb-678">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="60dbb-679">Národní prostředí: národní prostředí je stejné jako jazyková verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-679">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="60dbb-680">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="60dbb-680">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="60dbb-681">[StarterWeb projekt Localization](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) , který se používá v článku.</span><span class="sxs-lookup"><span data-stu-id="60dbb-681">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="60dbb-682">Globalizace a lokalizace aplikací .NET</span><span class="sxs-lookup"><span data-stu-id="60dbb-682">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="60dbb-683">Prostředky v souborech. resx</span><span class="sxs-lookup"><span data-stu-id="60dbb-683">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="60dbb-684">Sada nástrojů pro vícejazyčné aplikace od Microsoftu</span><span class="sxs-lookup"><span data-stu-id="60dbb-684">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="60dbb-685">Lokalizace & – obecné typy</span><span class="sxs-lookup"><span data-stu-id="60dbb-685">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="60dbb-686">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/)a [Hisham bin](https://twitter.com/hishambinateya) Ateya</span><span class="sxs-lookup"><span data-stu-id="60dbb-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="60dbb-687">Vícejazyčný web umožňuje webu oslovit širší cílovou skupinu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-687">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="60dbb-688">ASP.NET Core poskytuje služby a middleware pro lokalizaci do různých jazyků a kultur.</span><span class="sxs-lookup"><span data-stu-id="60dbb-688">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="60dbb-689">Mezinárodní využití zahrnuje [globalizaci](/dotnet/api/system.globalization) a [lokalizaci](/dotnet/standard/globalization-localization/localization).</span><span class="sxs-lookup"><span data-stu-id="60dbb-689">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="60dbb-690">Globalizace je proces návrhu aplikací, které podporují různé jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-690">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="60dbb-691">Globalizace přidává podporu pro vstup, zobrazení a výstup definované sady jazykových skriptů, které se vztahují na konkrétní geografické oblasti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-691">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="60dbb-692">Lokalizace je proces přizpůsobení globální aplikace, kterou jste již zpracovali pro lokalizaci, do konkrétní jazykové verze nebo národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-692">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="60dbb-693">Další informace najdete v tématu **předpoklady globalizace a lokalizace** poblíž konce tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-693">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="60dbb-694">Lokalizace aplikace zahrnuje následující:</span><span class="sxs-lookup"><span data-stu-id="60dbb-694">App localization involves the following:</span></span>

1. <span data-ttu-id="60dbb-695">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="60dbb-695">Make the app's content localizable</span></span>
1. <span data-ttu-id="60dbb-696">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="60dbb-696">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="60dbb-697">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="60dbb-697">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="60dbb-698">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="60dbb-698">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="60dbb-699">Nastavit lokalizaci obsahu aplikace</span><span class="sxs-lookup"><span data-stu-id="60dbb-699">Make the app's content localizable</span></span>

<span data-ttu-id="60dbb-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer ` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an ` IEnumerable ` for returning localized strings. ` IStringLocalizer ' nevyžaduje uložení výchozích řetězců jazyka do souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="60dbb-701">Můžete vyvíjet aplikaci zaměřenou na lokalizaci a nemusíte vytvářet soubory prostředků na začátku ve vývoji.</span><span class="sxs-lookup"><span data-stu-id="60dbb-701">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="60dbb-702">Následující kód ukazuje, jak zabalit řetězec "About title" pro lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="60dbb-702">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="60dbb-703">V předchozím kódu `IStringLocalizer<T>` implementace pochází z [Injektáže závislosti](dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="60dbb-703">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="60dbb-704">Pokud se nenalezne lokalizovaná hodnota "o titulku", vrátí se klíč indexeru, tj. řetězec "About title".</span><span class="sxs-lookup"><span data-stu-id="60dbb-704">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="60dbb-705">Můžete ponechat výchozí řetězcové literály v aplikaci a zabalit je do lokalizátora, abyste se mohli soustředit na vývoj aplikace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-705">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="60dbb-706">Vyvíjíte svou aplikaci pomocí výchozího jazyka a připravíte ji pro krok lokalizace bez prvotního vytvoření výchozího souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-706">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="60dbb-707">Alternativně můžete použít tradiční přístup a zadat klíč pro načtení výchozího řetězce jazyka.</span><span class="sxs-lookup"><span data-stu-id="60dbb-707">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="60dbb-708">Pro mnoho vývojářů nový pracovní postup, který nemá výchozí jazyk *. resx* soubor, a jednoduše zabalí řetězcové literály může snížit režii lokalizace aplikace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-708">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="60dbb-709">Ostatní vývojáři budou preferovat tradiční pracovní postup, protože mohou usnadnit práci s delšími řetězcovými literály a usnadňují aktualizaci lokalizovaných řetězců.</span><span class="sxs-lookup"><span data-stu-id="60dbb-709">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="60dbb-710">Použijte `IHtmlLocalizer<T>` implementaci pro prostředky, které obsahují kód HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-710">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="60dbb-711">`IHtmlLocalizer`HTML kóduje argumenty, které jsou formátovány v řetězci prostředků, ale nekóduje kód HTML samotný řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="60dbb-711">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="60dbb-712">V ukázce zvýrazněné níže je pouze hodnota `name` parametru kódována HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-712">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="60dbb-713">**Poznámka:** Obecně chcete lokalizovat pouze text a nikoli HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-713">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="60dbb-714">Na nejnižší úrovni můžete obdržet zástupné `IStringLocalizerFactory` [vkládání závislostí](dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="60dbb-714">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="60dbb-715">Výše uvedený kód ukazuje každou ze dvou metod Create Factory.</span><span class="sxs-lookup"><span data-stu-id="60dbb-715">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="60dbb-716">Lokalizované řetězce můžete rozdělit do oddílů podle řadiče, oblasti nebo pouze jednoho kontejneru.</span><span class="sxs-lookup"><span data-stu-id="60dbb-716">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="60dbb-717">V ukázkové aplikaci se `SharedResource` pro sdílené prostředky používá fiktivní třída s názvem.</span><span class="sxs-lookup"><span data-stu-id="60dbb-717">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="60dbb-718">Někteří vývojáři používají `Startup` třídu pro zahrnutí globálních nebo sdílených řetězců.</span><span class="sxs-lookup"><span data-stu-id="60dbb-718">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="60dbb-719">V níže uvedené ukázce se `InfoController` `SharedResource` používají a Localize:</span><span class="sxs-lookup"><span data-stu-id="60dbb-719">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="60dbb-720">Lokalizace zobrazení</span><span class="sxs-lookup"><span data-stu-id="60dbb-720">View localization</span></span>

<span data-ttu-id="60dbb-721">`IViewLocalizer`Služba poskytuje lokalizované řetězce pro [zobrazení](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="60dbb-721">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="60dbb-722">`ViewLocalizer`Třída implementuje toto rozhraní a vyhledá umístění prostředku z cesty k souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-722">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="60dbb-723">Následující kód ukazuje, jak použít výchozí implementaci `IViewLocalizer` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-723">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="60dbb-724">Výchozí implementace nástroje `IViewLocalizer` vyhledá soubor prostředků na základě názvu souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-724">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="60dbb-725">Neexistuje možnost použít globální sdílený soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-725">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="60dbb-726">`ViewLocalizer`implementuje lokalizátora pomocí `IHtmlLocalizer` , takže Razor nekóduje kód HTML jako lokalizovaný řetězec.</span><span class="sxs-lookup"><span data-stu-id="60dbb-726">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="60dbb-727">Můžete parametrizovat řetězce prostředků a `IViewLocalizer` zakódovat parametry HTML, ale ne řetězec prostředku.</span><span class="sxs-lookup"><span data-stu-id="60dbb-727">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="60dbb-728">Vezměte v úvahu následující Razor značky:</span><span class="sxs-lookup"><span data-stu-id="60dbb-728">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="60dbb-729">Soubor prostředků francouzštiny může obsahovat následující:</span><span class="sxs-lookup"><span data-stu-id="60dbb-729">A French resource file could contain the following:</span></span>

| <span data-ttu-id="60dbb-730">Klíč</span><span class="sxs-lookup"><span data-stu-id="60dbb-730">Key</span></span> | <span data-ttu-id="60dbb-731">Hodnota</span><span class="sxs-lookup"><span data-stu-id="60dbb-731">Value</span></span> |
| ----- | ---
<span data-ttu-id="60dbb-732">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-732">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-733">'Blazor'</span></span>
- <span data-ttu-id="60dbb-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-734">'Identity'</span></span>
- <span data-ttu-id="60dbb-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-736">'Razor'</span></span>
- <span data-ttu-id="60dbb-737">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-737">'SignalR' uid:</span></span> 

<span data-ttu-id="60dbb-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="60dbb-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="60dbb-739">Vykreslené zobrazení obsahuje značku HTML ze souboru prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-739">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="60dbb-740">**Poznámka:** Obecně chcete lokalizovat pouze text a nikoli HTML.</span><span class="sxs-lookup"><span data-stu-id="60dbb-740">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="60dbb-741">Chcete-li použít sdílený soubor prostředků v zobrazení, vložení `IHtmlLocalizer<T>` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-741">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="60dbb-742">Lokalizace DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="60dbb-742">DataAnnotations localization</span></span>

<span data-ttu-id="60dbb-743">Chybové zprávy pro dataanotace jsou lokalizovány pomocí `IStringLocalizer<T>` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-743">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="60dbb-744">Pomocí možnosti `ResourcesPath = "Resources"` mohou být chybové zprávy v nástroji `RegisterViewModel` uloženy v jedné z následujících cest:</span><span class="sxs-lookup"><span data-stu-id="60dbb-744">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="60dbb-745">*Resources/ViewModels. Account. RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="60dbb-746">*Prostředky/ViewModels/účet/RegisterViewModel. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="60dbb-747">V ASP.NET Core MVC 1.1.0 a vyšší jsou lokalizovány neověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="60dbb-747">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="60dbb-748">ASP.NET Core MVC 1,0 **nehledá lokalizované** řetězce pro atributy, které nejsou ověřovány.</span><span class="sxs-lookup"><span data-stu-id="60dbb-748">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="60dbb-749">Použití jednoho řetězce prostředku pro více tříd</span><span class="sxs-lookup"><span data-stu-id="60dbb-749">Using one resource string for multiple classes</span></span>

<span data-ttu-id="60dbb-750">Následující kód ukazuje, jak použít jeden řetězec prostředku pro atributy ověřování s více třídami:</span><span class="sxs-lookup"><span data-stu-id="60dbb-750">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="60dbb-751">V předchozím kódu `SharedResource` je třída odpovídající RESX, kde jsou uloženy ověřovací zprávy.</span><span class="sxs-lookup"><span data-stu-id="60dbb-751">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="60dbb-752">S tímto přístupem budou dataanotace používat pouze `SharedResource` místo prostředků pro každou třídu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-752">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="60dbb-753">Poskytněte lokalizované prostředky pro jazyky a kultury, které podporujete.</span><span class="sxs-lookup"><span data-stu-id="60dbb-753">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="60dbb-754">SupportedCultures a SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="60dbb-754">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="60dbb-755">ASP.NET Core umožňuje zadat dvě hodnoty jazykové verze `SupportedCultures` a `SupportedUICultures` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-755">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="60dbb-756">Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) pro `SupportedCultures` určuje výsledky funkcí závislých na jazykové verzi, jako je datum, čas, číslo a formátování měny.</span><span class="sxs-lookup"><span data-stu-id="60dbb-756">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="60dbb-757">`SupportedCultures`Určuje také pořadí řazení textu, konvencí velikosti písmen a porovnávání řetězců.</span><span class="sxs-lookup"><span data-stu-id="60dbb-757">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="60dbb-758">Další informace o tom, jak server získá jazykovou verzi, naleznete v tématu [CultureInfo. CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) .</span><span class="sxs-lookup"><span data-stu-id="60dbb-758">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="60dbb-759">`SupportedUICultures`Určuje, které přeložené řetězce (ze souborů *. resx* ) jsou vyhledány správcem [prostředků](/dotnet/api/system.resources.resourcemanager).</span><span class="sxs-lookup"><span data-stu-id="60dbb-759">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="60dbb-760">`ResourceManager`Jednoduše vyhledá řetězce specifické pro jazykovou verzi, které určuje `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-760">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="60dbb-761">Každé vlákno v rozhraní .NET má `CurrentCulture` a `CurrentUICulture` objekty.</span><span class="sxs-lookup"><span data-stu-id="60dbb-761">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="60dbb-762">ASP.NET Core tyto hodnoty kontroluje při vykreslování funkcí závislých na jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-762">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="60dbb-763">Pokud je například jazyková verze aktuálního vlákna nastavena na "en-US" (angličtina, USA), `DateTime.Now.ToLongDateString()` zobrazí "čtvrtek, 18. února 2016", ale pokud `CurrentCulture` je nastavená na "ES-ES" (španělština, Španělsko), bude výstup "Jueves, 18 de febrero de 2016".</span><span class="sxs-lookup"><span data-stu-id="60dbb-763">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="60dbb-764">Soubory prostředků</span><span class="sxs-lookup"><span data-stu-id="60dbb-764">Resource files</span></span>

<span data-ttu-id="60dbb-765">Soubor prostředků je užitečný mechanismus pro oddělení lokalizovatelných řetězců z kódu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-765">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="60dbb-766">Přeložené řetězce pro jazyk, který není výchozí, jsou izolované v souborech prostředků *. resx* .</span><span class="sxs-lookup"><span data-stu-id="60dbb-766">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="60dbb-767">Například může být vhodné vytvořit soubor prostředků španělštiny s názvem *Welcome. ES. resx* obsahující přeložené řetězce.</span><span class="sxs-lookup"><span data-stu-id="60dbb-767">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="60dbb-768">"ES" je kód jazyka pro španělštinu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-768">"es" is the language code for Spanish.</span></span> <span data-ttu-id="60dbb-769">Postup vytvoření tohoto souboru prostředků v aplikaci Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="60dbb-769">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="60dbb-770">V **Průzkumník řešení**klikněte pravým tlačítkem na složku, která bude obsahovat soubor prostředků > **Přidat** > **novou položku**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-770">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![Vnořená kontextová nabídka: v Průzkumník řešení je místní nabídka pro prostředky otevřená.](localization/_static/newi.png)

2. <span data-ttu-id="60dbb-773">V poli **Vyhledat nainstalované šablony** zadejte "Resource" a soubor pojmenujte.</span><span class="sxs-lookup"><span data-stu-id="60dbb-773">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![Dialogové okno Přidat novou položku](localization/_static/res.png)

3. <span data-ttu-id="60dbb-775">Do sloupce **název** zadejte hodnotu klíče (nativní řetězec) a přeložený řetězec ve sloupci Value ( **hodnota** ).</span><span class="sxs-lookup"><span data-stu-id="60dbb-775">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome. ES. resx soubor (soubor prostředků Welcome pro španělštinu) se slovem Hello ve sloupci název a slovo Hola (text Hello v španělštině) ve sloupci Hodnota](localization/_static/hola.png)

    <span data-ttu-id="60dbb-777">Sada Visual Studio zobrazí *uvítací soubor. ES. resx* .</span><span class="sxs-lookup"><span data-stu-id="60dbb-777">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![Průzkumník řešení se zobrazuje soubor prostředků nástroje Welcome španělština (ES)](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="60dbb-779">Pojmenovávání souborů prostředků</span><span class="sxs-lookup"><span data-stu-id="60dbb-779">Resource file naming</span></span>

<span data-ttu-id="60dbb-780">Prostředky jsou pojmenovány pro úplný název typu své třídy minus název sestavení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-780">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="60dbb-781">Například francouzská prostředků v projektu, jehož hlavní sestavení je `LocalizationWebsite.Web.dll` pro třídu, `LocalizationWebsite.Web.Startup` by měla být pojmenována *Startup. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-781">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="60dbb-782">Prostředek pro třídu by měl `LocalizationWebsite.Web.Controllers.HomeController` pojmenovat *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-782">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-783">Pokud cílový obor názvů třídy není stejný jako název sestavení, budete potřebovat úplný název typu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-783">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="60dbb-784">Například v ukázkovém projektu by měl prostředek pro typ `ExtraNamespace.Tools` název *ExtraNamespace. Tools. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-784">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="60dbb-785">V ukázkovém projektu `ConfigureServices` Metoda nastaví `ResourcesPath` na "prostředky", takže relativní cesta projektu pro francouzský soubor prostředků domovského řadiče je *Resources/Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-785">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-786">Případně můžete použít složky k uspořádání souborů prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-786">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="60dbb-787">V případě domovského kontroleru by tato cesta byla *Resources/Controllers/HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-787">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-788">Pokud tuto možnost nepoužijete `ResourcesPath` , soubor *. resx* by přešel do základního adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-788">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="60dbb-789">Soubor prostředků pro `HomeController` by měl mít název *Controllers. HomeController. fr. resx*.</span><span class="sxs-lookup"><span data-stu-id="60dbb-789">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="60dbb-790">Volba použití konvence pojmenování teček nebo Path závisí na tom, jak chcete uspořádat soubory prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-790">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="60dbb-791">Název prostředku</span><span class="sxs-lookup"><span data-stu-id="60dbb-791">Resource name</span></span> | <span data-ttu-id="60dbb-792">Pojmenování teček nebo Path</span><span class="sxs-lookup"><span data-stu-id="60dbb-792">Dot or path naming</span></span> |
| ---
<span data-ttu-id="60dbb-793">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-793">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-794">'Blazor'</span></span>
- <span data-ttu-id="60dbb-795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-795">'Identity'</span></span>
- <span data-ttu-id="60dbb-796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-796">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-797">'Razor'</span></span>
- <span data-ttu-id="60dbb-798">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-799">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-799">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-800">'Blazor'</span></span>
- <span data-ttu-id="60dbb-801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-801">'Identity'</span></span>
- <span data-ttu-id="60dbb-802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-802">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-803">'Razor'</span></span>
- <span data-ttu-id="60dbb-804">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-805">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-805">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-806">'Blazor'</span></span>
- <span data-ttu-id="60dbb-807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-807">'Identity'</span></span>
- <span data-ttu-id="60dbb-808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-808">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-809">'Razor'</span></span>
- <span data-ttu-id="60dbb-810">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-811">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-811">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-812">'Blazor'</span></span>
- <span data-ttu-id="60dbb-813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-813">'Identity'</span></span>
- <span data-ttu-id="60dbb-814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-814">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-815">'Razor'</span></span>
- <span data-ttu-id="60dbb-816">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-816">'SignalR' uid:</span></span> 

<span data-ttu-id="60dbb-817">------   | ---Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-818">'Blazor'</span></span>
- <span data-ttu-id="60dbb-819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-819">'Identity'</span></span>
- <span data-ttu-id="60dbb-820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-820">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-821">'Razor'</span></span>
- <span data-ttu-id="60dbb-822">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-823">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-823">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-824">'Blazor'</span></span>
- <span data-ttu-id="60dbb-825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-825">'Identity'</span></span>
- <span data-ttu-id="60dbb-826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-826">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-827">'Razor'</span></span>
- <span data-ttu-id="60dbb-828">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-829">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-829">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-830">'Blazor'</span></span>
- <span data-ttu-id="60dbb-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-831">'Identity'</span></span>
- <span data-ttu-id="60dbb-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-833">'Razor'</span></span>
- <span data-ttu-id="60dbb-834">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="60dbb-835">Název: Autor: Popis: MS. Author: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="60dbb-835">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="60dbb-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-836">'Blazor'</span></span>
- <span data-ttu-id="60dbb-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="60dbb-837">'Identity'</span></span>
- <span data-ttu-id="60dbb-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="60dbb-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="60dbb-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="60dbb-839">'Razor'</span></span>
- <span data-ttu-id="60dbb-840">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="60dbb-840">'SignalR' uid:</span></span> 

<span data-ttu-id="60dbb-841">------- | | Prostředky/řadiče. HomeController. fr. resx | Tečka | | Prostředky/řadiče/HomeController. fr. resx | Cesta | |    |     |</span><span class="sxs-lookup"><span data-stu-id="60dbb-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="60dbb-842">Soubory prostředků používané `@inject IViewLocalizer` v Razor zobrazeních následují podobně jako vzor.</span><span class="sxs-lookup"><span data-stu-id="60dbb-842">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="60dbb-843">Soubor prostředků pro zobrazení může být pojmenován buď pomocí názvu tečky, nebo pojmenování cesty.</span><span class="sxs-lookup"><span data-stu-id="60dbb-843">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="60dbb-844">zobrazení soubory prostředků napodobá cestě k jejich přidruženému souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-844"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="60dbb-845">Za předpokladu, že nastavíme `ResourcesPath` "prostředky", soubor francouzského prostředku přidružený k zobrazením */domů/o zobrazení. cshtml* může být jedna z následujících:</span><span class="sxs-lookup"><span data-stu-id="60dbb-845">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="60dbb-846">Prostředky/zobrazení/domů/o. fr. resx</span><span class="sxs-lookup"><span data-stu-id="60dbb-846">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="60dbb-847">Prostředky/zobrazení. domů. about. fr. resx</span><span class="sxs-lookup"><span data-stu-id="60dbb-847">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="60dbb-848">Pokud tuto možnost nepoužijete `ResourcesPath` , bude soubor *. resx* pro zobrazení umístěn ve stejné složce jako zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-848">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="60dbb-849">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="60dbb-849">RootNamespaceAttribute</span></span> 

<span data-ttu-id="60dbb-850">Atribut [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) poskytuje kořenový obor názvů sestavení, pokud se kořenový obor názvů sestavení liší od názvu sestavení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-850">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="60dbb-851">K tomu může dojít, když název projektu není platným identifikátorem .NET.</span><span class="sxs-lookup"><span data-stu-id="60dbb-851">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="60dbb-852">V případě instance `my-project-name.csproj` bude použit kořenový obor názvů `my_project_name` a název sestavení, `my-project-name` který vede k této chybě.</span><span class="sxs-lookup"><span data-stu-id="60dbb-852">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="60dbb-853">Pokud se kořenový obor názvů sestavení liší od názvu sestavení:</span><span class="sxs-lookup"><span data-stu-id="60dbb-853">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="60dbb-854">Lokalizace ve výchozím nastavení nefunguje.</span><span class="sxs-lookup"><span data-stu-id="60dbb-854">Localization does not work by default.</span></span>
* <span data-ttu-id="60dbb-855">Lokalizace se nezdařila z důvodu způsobu hledání prostředků v rámci sestavení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-855">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="60dbb-856">`RootNamespace`je hodnota doby sestavení, která není k dispozici pro spuštěný proces.</span><span class="sxs-lookup"><span data-stu-id="60dbb-856">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="60dbb-857">Pokud se `RootNamespace` liší od z `AssemblyName` , zahrňte následující v *AssemblyInfo.cs* (s hodnotami parametrů nahrazenými skutečnými hodnotami):</span><span class="sxs-lookup"><span data-stu-id="60dbb-857">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="60dbb-858">Předchozí kód umožňuje úspěšné vyřešení souborů RESX.</span><span class="sxs-lookup"><span data-stu-id="60dbb-858">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="60dbb-859">Chování záložního kultivačního prostředí</span><span class="sxs-lookup"><span data-stu-id="60dbb-859">Culture fallback behavior</span></span>

<span data-ttu-id="60dbb-860">Při hledání prostředku se lokalizace zapojit do "záložní kultury".</span><span class="sxs-lookup"><span data-stu-id="60dbb-860">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="60dbb-861">Od požadované jazykové verze, pokud se nenajde, se vrátí k nadřazené jazykové verzi této jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-861">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="60dbb-862">Kromě toho, vlastnost [CultureInfo. Parent](/dotnet/api/system.globalization.cultureinfo.parent) představuje nadřazenou jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-862">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="60dbb-863">To je obvykle (ale ne vždy) znamená odebrání národního přízpůsobu z ISO.</span><span class="sxs-lookup"><span data-stu-id="60dbb-863">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="60dbb-864">Například dialekt španělštiny ve španělštině, který se používá v Mexiku, je ES-MX.</span><span class="sxs-lookup"><span data-stu-id="60dbb-864">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="60dbb-865">Má nadřízenou španělštinu ES, která &mdash; není specifická pro žádnou zemi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-865">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="60dbb-866">Představte si, že váš web obdrží žádost o "úvodní" prostředek pomocí kultury "fr-CA".</span><span class="sxs-lookup"><span data-stu-id="60dbb-866">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="60dbb-867">Systém lokalizace vyhledá následující prostředky v uvedeném pořadí a vybere první shodu:</span><span class="sxs-lookup"><span data-stu-id="60dbb-867">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="60dbb-868">*Welcome.fr-CA. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-868">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="60dbb-869">*Welcome. fr. resx*</span><span class="sxs-lookup"><span data-stu-id="60dbb-869">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="60dbb-870">*Welcome. resx* (Pokud `NeutralResourcesLanguage` je to "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="60dbb-870">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="60dbb-871">Pokud například odeberete označení jazykové verze ". fr" a máte nastavenou jazykovou verzi na francouzštinu, je výchozí soubor prostředků přečten a jsou lokalizovány řetězce.</span><span class="sxs-lookup"><span data-stu-id="60dbb-871">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="60dbb-872">Správce prostředků určí výchozí nebo záložní prostředek, pokud nic nevyhovuje vaší požadované jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-872">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="60dbb-873">Pokud chcete vrátit klíč pouze v případě, že chybí prostředek pro požadovanou jazykovou verzi, nesmíte mít výchozí soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-873">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="60dbb-874">Generování souborů prostředků pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="60dbb-874">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="60dbb-875">Vytvoříte-li soubor prostředků v aplikaci Visual Studio bez jazykové verze v názvu souboru (například *Welcome. resx*), sada Visual Studio vytvoří třídu jazyka C# s vlastností pro každý řetězec.</span><span class="sxs-lookup"><span data-stu-id="60dbb-875">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="60dbb-876">To obvykle není to, co chcete s ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="60dbb-876">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="60dbb-877">Obvykle nemáte výchozí soubor prostředků *. resx* (soubor *. resx* bez názvu jazykové verze).</span><span class="sxs-lookup"><span data-stu-id="60dbb-877">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="60dbb-878">Návrh souboru *. resx* doporučujeme vytvořit s názvem jazykové verze (například *Welcome. fr. resx*).</span><span class="sxs-lookup"><span data-stu-id="60dbb-878">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="60dbb-879">Při vytváření souboru *. resx* s názvem jazykové verze aplikace Visual Studio negeneruje soubor třídy.</span><span class="sxs-lookup"><span data-stu-id="60dbb-879">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="60dbb-880">Přidat další jazykové verze</span><span class="sxs-lookup"><span data-stu-id="60dbb-880">Add other cultures</span></span>

<span data-ttu-id="60dbb-881">Každá kombinace jazyka a jazykové verze (Kromě výchozího jazyka) vyžaduje jedinečný soubor prostředků.</span><span class="sxs-lookup"><span data-stu-id="60dbb-881">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="60dbb-882">Soubory prostředků můžete vytvořit pro různé kultury a národní prostředí vytvořením nových souborů prostředků, ve kterých jsou kódy jazyka ISO součástí názvu souboru (například **en-US**, **fr-CA**a **en-GB**).</span><span class="sxs-lookup"><span data-stu-id="60dbb-882">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="60dbb-883">Tyto kódy ISO jsou umístěné mezi názvem souboru a příponou souboru *. resx* , jako v *Welcome.ES-MX. resx* (španělština/Mexiko).</span><span class="sxs-lookup"><span data-stu-id="60dbb-883">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="60dbb-884">Implementujte strategii pro výběr jazyka nebo kultury pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="60dbb-884">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="60dbb-885">Konfigurace lokalizace</span><span class="sxs-lookup"><span data-stu-id="60dbb-885">Configure localization</span></span>

<span data-ttu-id="60dbb-886">Lokalizace je nakonfigurovaná v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="60dbb-886">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="60dbb-887">`AddLocalization`Přidá služby lokalizace do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="60dbb-887">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="60dbb-888">Výše uvedený kód také nastaví cestu prostředků na prostředky.</span><span class="sxs-lookup"><span data-stu-id="60dbb-888">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="60dbb-889">`AddViewLocalization`Přidá podporu pro lokalizované soubory zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-889">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="60dbb-890">V tomto ukázkovém zobrazení je lokalizace založena na příponě souboru zobrazení.</span><span class="sxs-lookup"><span data-stu-id="60dbb-890">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="60dbb-891">Například "fr" v souboru *index. fr. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="60dbb-891">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="60dbb-892">`AddDataAnnotationsLocalization`Přidává podporu pro lokalizované `DataAnnotations` ověřovací zprávy prostřednictvím `IStringLocalizer` abstrakcí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-892">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="60dbb-893">Middleware lokalizace</span><span class="sxs-lookup"><span data-stu-id="60dbb-893">Localization middleware</span></span>

<span data-ttu-id="60dbb-894">Aktuální jazyková verze v požadavku je nastavena v [middleware](xref:fundamentals/middleware/index)lokalizace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-894">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="60dbb-895">Middleware lokalizace je povolená v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="60dbb-895">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="60dbb-896">Middleware lokalizace musí být konfigurovány před jakýmkoli middlewarem, který by mohl kontrolovat jazykovou verzi žádosti (například `app.UseMvcWithDefaultRoute()` ).</span><span class="sxs-lookup"><span data-stu-id="60dbb-896">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="60dbb-897">`UseRequestLocalization`Inicializuje `RequestLocalizationOptions` objekt.</span><span class="sxs-lookup"><span data-stu-id="60dbb-897">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="60dbb-898">Při každém požadavku se vypíše seznam `RequestCultureProvider` ve `RequestLocalizationOptions` výčtu a první zprostředkovatel, který dokáže úspěšně určit jazykovou verzi žádosti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-898">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="60dbb-899">Výchozí zprostředkovatelé přicházejí z `RequestLocalizationOptions` třídy:</span><span class="sxs-lookup"><span data-stu-id="60dbb-899">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="60dbb-900">Výchozí seznam bude z nejpřesnější, aby byl nejméně specifický.</span><span class="sxs-lookup"><span data-stu-id="60dbb-900">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="60dbb-901">Později v článku se dozvíte, jak můžete změnit pořadí a dokonce přidat vlastního poskytovatele jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-901">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="60dbb-902">Pokud žádný z poskytovatelů nemůže určit jazykovou verzi žádosti, `DefaultRequestCulture` je použita.</span><span class="sxs-lookup"><span data-stu-id="60dbb-902">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="60dbb-903">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="60dbb-903">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="60dbb-904">Některé aplikace budou používat řetězec dotazu k nastavení jazykové verze [a jazykové verze uživatelského rozhraní](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span><span class="sxs-lookup"><span data-stu-id="60dbb-904">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="60dbb-905">Pro aplikace, které používají přístup k hlavičkám souborů cookie nebo Accept-Language, je přidání řetězce dotazu na adresu URL užitečné pro ladění a testování kódu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-905">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="60dbb-906">Ve výchozím nastavení `QueryStringRequestCultureProvider` je jako první poskytovatel lokalizace v `RequestCultureProvider` seznamu zaregistrován.</span><span class="sxs-lookup"><span data-stu-id="60dbb-906">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="60dbb-907">Předáte parametry řetězce dotazu `culture` a `ui-culture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-907">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="60dbb-908">Následující příklad nastaví konkrétní jazykovou verzi (jazyk a oblast) na španělština/Mexiko:</span><span class="sxs-lookup"><span data-stu-id="60dbb-908">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="60dbb-909">Pokud předáte pouze jeden z těchto dvou ( `culture` nebo `ui-culture` ), zprostředkovatel řetězce dotazu nastaví obě hodnoty pomocí toho, kterou jste předali.</span><span class="sxs-lookup"><span data-stu-id="60dbb-909">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="60dbb-910">Například nastavení pouze jazyková verze nastaví jak `Culture` a `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-910">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="60dbb-911">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="60dbb-911">CookieRequestCultureProvider</span></span>

<span data-ttu-id="60dbb-912">Produkční aplikace často poskytují mechanismus pro nastavení jazykové verze pomocí souboru cookie ASP.NET Core jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-912">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="60dbb-913">Pomocí `MakeCookieValue` metody vytvořte soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="60dbb-913">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="60dbb-914">`CookieRequestCultureProvider` `DefaultCookieName` Vrátí výchozí název souboru cookie, který se používá ke sledování informací o preferované jazykové verzi uživatele.</span><span class="sxs-lookup"><span data-stu-id="60dbb-914">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="60dbb-915">Výchozí název souboru cookie je `.AspNetCore.Culture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-915">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="60dbb-916">Formát souboru cookie je `c=%LANGCODE%|uic=%LANGCODE%` , kde `c` je `Culture` a `uic` je například `UICulture` :</span><span class="sxs-lookup"><span data-stu-id="60dbb-916">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="60dbb-917">Pokud zadáte pouze jednu z informací o jazykové verzi a jazykovou verzi uživatelského rozhraní, zadaná jazyková verze bude použita pro informace o jazykové verzi i jazyková verze uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="60dbb-917">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="60dbb-918">Hlavička protokolu HTTP pro přijetí – jazyk</span><span class="sxs-lookup"><span data-stu-id="60dbb-918">The Accept-Language HTTP header</span></span>

<span data-ttu-id="60dbb-919">[Záhlaví Accept-Language](https://www.w3.org/International/questions/qa-accept-lang-locales) lze nastavit ve většině prohlížečů a původně bylo určeno pro určení jazyka uživatele.</span><span class="sxs-lookup"><span data-stu-id="60dbb-919">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="60dbb-920">Toto nastavení indikuje, co je v prohlížeči nastavené na Odeslat nebo zděděné z podkladového operačního systému.</span><span class="sxs-lookup"><span data-stu-id="60dbb-920">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="60dbb-921">Hlavička protokolu HTTP Accept-Language z požadavku prohlížeče není infallible způsobem, jak zjistit preferovaný jazyk uživatele (viz [Nastavení jazykových předvoleb v prohlížeči](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span><span class="sxs-lookup"><span data-stu-id="60dbb-921">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="60dbb-922">Produkční aplikace by měla obsahovat způsob, jak může uživatel přizpůsobit svou volbu kultury.</span><span class="sxs-lookup"><span data-stu-id="60dbb-922">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="60dbb-923">Nastavení hlavičky protokolu HTTP Accept-Language v IE</span><span class="sxs-lookup"><span data-stu-id="60dbb-923">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="60dbb-924">Z ikony ozubeného kolečka klepněte na **Možnosti Internetu**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-924">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="60dbb-925">Klepněte na **jazyky**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-925">Tap **Languages**.</span></span>

    ![Možnosti Internetu](localization/_static/lang.png)

3. <span data-ttu-id="60dbb-927">Klepněte na **nastavit jazykové předvolby**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-927">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="60dbb-928">Klepněte na **Přidat jazyk**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-928">Tap **Add a language**.</span></span>

5. <span data-ttu-id="60dbb-929">Přidejte jazyk.</span><span class="sxs-lookup"><span data-stu-id="60dbb-929">Add the language.</span></span>

6. <span data-ttu-id="60dbb-930">Klepněte na jazyk a potom klepněte na **Přesunout nahoru**.</span><span class="sxs-lookup"><span data-stu-id="60dbb-930">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="60dbb-931">Hlavička protokolu HTTP v jazykovém obsahu</span><span class="sxs-lookup"><span data-stu-id="60dbb-931">The Content-Language HTTP header</span></span>

<span data-ttu-id="60dbb-932">Hlavička entity [jazyka obsahu](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) :</span><span class="sxs-lookup"><span data-stu-id="60dbb-932">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="60dbb-933">Slouží k popisu jazyků, které jsou určené pro cílovou skupinu.</span><span class="sxs-lookup"><span data-stu-id="60dbb-933">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="60dbb-934">Umožňuje uživateli rozlišovat podle vlastního preferovaného jazyka uživatelů.</span><span class="sxs-lookup"><span data-stu-id="60dbb-934">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="60dbb-935">Záhlaví entit se používají v požadavcích HTTP i v odpovědích.</span><span class="sxs-lookup"><span data-stu-id="60dbb-935">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="60dbb-936">`Content-Language`Záhlaví lze přidat nastavením vlastnosti `ApplyCurrentCultureToResponseHeaders` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-936">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="60dbb-937">Přidání `Content-Language` hlavičky:</span><span class="sxs-lookup"><span data-stu-id="60dbb-937">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="60dbb-938">Umožňuje RequestLocalizationMiddleware nastavit `Content-Language` hlavičku pomocí `CurrentUICulture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-938">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="60dbb-939">Eliminuje nutnost explicitně nastavit hlavičku odpovědi `Content-Language` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-939">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="60dbb-940">Použití vlastního zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="60dbb-940">Use a custom provider</span></span>

<span data-ttu-id="60dbb-941">Předpokládejme, že chcete, aby vaši zákazníci mohli ukládat svůj jazyk a jazykovou verzi do databází.</span><span class="sxs-lookup"><span data-stu-id="60dbb-941">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="60dbb-942">Můžete napsat poskytovatele a vyhledat tyto hodnoty pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="60dbb-942">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="60dbb-943">Následující kód ukazuje, jak přidat vlastního zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="60dbb-943">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="60dbb-944">Použijte `RequestLocalizationOptions` k přidání nebo odebrání poskytovatelů lokalizace.</span><span class="sxs-lookup"><span data-stu-id="60dbb-944">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="60dbb-945">Programové nastavení kultury</span><span class="sxs-lookup"><span data-stu-id="60dbb-945">Set the culture programmatically</span></span>

<span data-ttu-id="60dbb-946">Tento ukázkový projekt **Localization. StarterWeb** na [GITHUBU](https://github.com/aspnet/entropy) obsahuje uživatelské rozhraní pro nastavení `Culture` .</span><span class="sxs-lookup"><span data-stu-id="60dbb-946">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="60dbb-947">Soubor *views/Shared/_SelectLanguagePartial. cshtml* umožňuje vybrat jazykovou verzi ze seznamu podporovaných jazykových verzí:</span><span class="sxs-lookup"><span data-stu-id="60dbb-947">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="60dbb-948">Soubor *views/Shared/_SelectLanguagePartial. cshtml* se přidá do `footer` oddílu souboru rozložení, takže bude k dispozici pro všechna zobrazení:</span><span class="sxs-lookup"><span data-stu-id="60dbb-948">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="60dbb-949">`SetLanguage`Metoda nastaví soubor cookie jazykové verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-949">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="60dbb-950">Nemůžete připojit *_SelectLanguagePartial. cshtml* k ukázkovému kódu pro tento projekt.</span><span class="sxs-lookup"><span data-stu-id="60dbb-950">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="60dbb-951">Projekt **Localization. StarterWeb** na [GitHubu](https://github.com/aspnet/entropy) obsahuje kód pro tok `RequestLocalizationOptions` do Razor částečného toku prostřednictvím kontejneru pro [vkládání závislostí](dependency-injection.md) .</span><span class="sxs-lookup"><span data-stu-id="60dbb-951">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="60dbb-952">Vazba modelu data směrování a řetězce dotazů</span><span class="sxs-lookup"><span data-stu-id="60dbb-952">Model binding route data and query strings</span></span>

<span data-ttu-id="60dbb-953">Podívejte [se na téma chování globalizace modelu vazby dat trasy a řetězce dotazu](xref:mvc/models/model-binding#glob).</span><span class="sxs-lookup"><span data-stu-id="60dbb-953">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="60dbb-954">Výrazy globalizace a lokalizace</span><span class="sxs-lookup"><span data-stu-id="60dbb-954">Globalization and localization terms</span></span>

<span data-ttu-id="60dbb-955">Proces lokalizace vaší aplikace také vyžaduje základní znalosti relevantních znakových sad, které se běžně používají při vývoji moderního softwaru, a porozumění problémům, které s nimi souvisejí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-955">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="60dbb-956">I když všechny počítače ukládají text jako čísla (kódy), různé systémy ukládají stejný text s různými čísly.</span><span class="sxs-lookup"><span data-stu-id="60dbb-956">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="60dbb-957">Proces lokalizace odkazuje na překlad uživatelského rozhraní aplikace (UI) pro konkrétní jazykovou verzi nebo národní prostředí.</span><span class="sxs-lookup"><span data-stu-id="60dbb-957">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="60dbb-958">[Lokalizace](/dotnet/standard/globalization-localization/localizability-review) je přechodný proces pro ověření, že globální aplikace je připravená na lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="60dbb-958">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="60dbb-959">Formát [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) pro název jazykové verze je `<languagecode2>-<country/regioncode2>` , kde `<languagecode2>` je kód jazyka a `<country/regioncode2>` je kód subkultury.</span><span class="sxs-lookup"><span data-stu-id="60dbb-959">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="60dbb-960">Například `es-CL` pro španělštinu (Chile), `en-US` pro angličtinu (USA) a `en-AU` pro angličtinu (Austrálie).</span><span class="sxs-lookup"><span data-stu-id="60dbb-960">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="60dbb-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) je kombinací kódu kultury ISO 639 2 s malým písmenem, který je přidružený k jazyku, a kódu subkultury na velká písmena ISO 3166 2, který je přidružený k zemi nebo oblasti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="60dbb-962">Viz [název jazykové verze](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span><span class="sxs-lookup"><span data-stu-id="60dbb-962">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="60dbb-963">Mezinárodní využití se často zkracuje na "I18N".</span><span class="sxs-lookup"><span data-stu-id="60dbb-963">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="60dbb-964">Zkratka používá první a poslední písmena a počet písmen mezi nimi, takže 18 představuje počet písmen mezi první I a poslední znak "N".</span><span class="sxs-lookup"><span data-stu-id="60dbb-964">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="60dbb-965">Totéž platí pro globalizaci (G11N) a lokalizaci (L10N).</span><span class="sxs-lookup"><span data-stu-id="60dbb-965">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="60dbb-966">Uvedenými</span><span class="sxs-lookup"><span data-stu-id="60dbb-966">Terms:</span></span>

* <span data-ttu-id="60dbb-967">Globalizace (G11N): proces vytvoření aplikace, která podporuje různé jazyky a oblasti.</span><span class="sxs-lookup"><span data-stu-id="60dbb-967">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="60dbb-968">Lokalizace (L10N): proces přizpůsobení aplikace pro daný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-968">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="60dbb-969">Mezinárodní (I18N): popisuje globalizaci a lokalizaci.</span><span class="sxs-lookup"><span data-stu-id="60dbb-969">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="60dbb-970">Jazyková verze: Jedná se o jazyk a volitelně i oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-970">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="60dbb-971">Neutrální jazyková verze: jazyková verze, která má zadaný jazyk, ale ne oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-971">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="60dbb-972">(například "en", "ES")</span><span class="sxs-lookup"><span data-stu-id="60dbb-972">(for example "en", "es")</span></span>
* <span data-ttu-id="60dbb-973">Specifická jazyková verze: jazyková verze, která má zadaný jazyk a oblast.</span><span class="sxs-lookup"><span data-stu-id="60dbb-973">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="60dbb-974">(například "en-US", "en-GB", "ES-CL")</span><span class="sxs-lookup"><span data-stu-id="60dbb-974">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="60dbb-975">Nadřazená jazyková verze: neutrální jazyková verze, která obsahuje konkrétní jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="60dbb-975">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="60dbb-976">(například "en" je nadřazená jazyková verze "en-US" a "en-GB")</span><span class="sxs-lookup"><span data-stu-id="60dbb-976">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="60dbb-977">Národní prostředí: národní prostředí je stejné jako jazyková verze.</span><span class="sxs-lookup"><span data-stu-id="60dbb-977">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="60dbb-978">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="60dbb-978">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="60dbb-979">[StarterWeb projekt Localization](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) , který se používá v článku.</span><span class="sxs-lookup"><span data-stu-id="60dbb-979">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="60dbb-980">Globalizace a lokalizace aplikací .NET</span><span class="sxs-lookup"><span data-stu-id="60dbb-980">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="60dbb-981">Prostředky v souborech. resx</span><span class="sxs-lookup"><span data-stu-id="60dbb-981">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="60dbb-982">Sada nástrojů pro vícejazyčné aplikace od Microsoftu</span><span class="sxs-lookup"><span data-stu-id="60dbb-982">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="60dbb-983">Lokalizace & – obecné typy</span><span class="sxs-lookup"><span data-stu-id="60dbb-983">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

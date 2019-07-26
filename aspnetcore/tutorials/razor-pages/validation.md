---
title: Přidání ověřování na stránku ASP.NET Core Razor
author: rick-anderson
description: Zjistěte, jak přidat ověřování na stránku Razor v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: d6d45dc7154bf415c3b098299d066b6fb37cf64d
ms.sourcegitcommit: 16502797ea749e2690feaa5e652a65b89c007c89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483275"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="fb975-103">Přidání ověřování na stránku ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="fb975-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="fb975-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fb975-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fb975-105">V této části je logika ověřování přidána do `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="fb975-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="fb975-106">Ověřovací pravidla se vynutily pokaždé, když uživatel vytvoří nebo upraví film.</span><span class="sxs-lookup"><span data-stu-id="fb975-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="fb975-107">Ověřování</span><span class="sxs-lookup"><span data-stu-id="fb975-107">Validation</span></span>

<span data-ttu-id="fb975-108">Key principem vývoje softwaru se nazývá [suchý](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**EPEAT **Y**ourself").</span><span class="sxs-lookup"><span data-stu-id="fb975-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="fb975-109">Razor Pages podporuje vývoj, ve kterém jsou funkce zadány jednou a které se projeví v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fb975-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="fb975-110">Bezsuchá může pomáhat:</span><span class="sxs-lookup"><span data-stu-id="fb975-110">DRY can help:</span></span>

* <span data-ttu-id="fb975-111">Snižte množství kódu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fb975-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="fb975-112">Udělejte kód méně náchylné k chybám a usnadněte si testování a údržbu.</span><span class="sxs-lookup"><span data-stu-id="fb975-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="fb975-113">Podpora ověřování, kterou poskytuje Razor Pages a Entity Framework, je dobrým příkladem SUCHÉho principu.</span><span class="sxs-lookup"><span data-stu-id="fb975-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="fb975-114">Ověřovací pravidla jsou deklarativně určena na jednom místě (ve třídě modelu) a pravidla jsou vynutila všude v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fb975-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="fb975-115">Uživatelské rozhraní chyby ověřování v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="fb975-115">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="fb975-116">Spusťte aplikaci a přejděte na stránky/filmy.</span><span class="sxs-lookup"><span data-stu-id="fb975-116">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="fb975-117">Vyberte odkaz **vytvořit nový** .</span><span class="sxs-lookup"><span data-stu-id="fb975-117">Select the **Create New** link.</span></span> <span data-ttu-id="fb975-118">Vyplňte formulář s neplatnými hodnotami.</span><span class="sxs-lookup"><span data-stu-id="fb975-118">Complete the form with some invalid values.</span></span> <span data-ttu-id="fb975-119">Když při ověřování na straně klienta zjistí chybu, zobrazí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="fb975-119">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formulář zobrazení videa s několika chybami ověřování na straně klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

<span data-ttu-id="fb975-121">Všimněte si, jak formulář automaticky vykresluje chybovou zprávu ověřování v každém poli, které obsahuje neplatnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="fb975-121">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="fb975-122">Chyby se vynutily na straně klienta (pomocí JavaScriptu a jQuery) a na straně serveru (když má uživatel zakázaný JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fb975-122">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="fb975-123">Významnou výhodou je, že na stránkách vytvořit nebo upravit nebyly nutné **žádné** změny kódu.</span><span class="sxs-lookup"><span data-stu-id="fb975-123">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="fb975-124">Jakmile se v modelu aplikují dataanotace, uživatelské rozhraní ověřování je povolené.</span><span class="sxs-lookup"><span data-stu-id="fb975-124">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="fb975-125">Razor Pages vytvořená v tomto kurzu automaticky vybrala ověřovací pravidla (pomocí atributů ověřování ve vlastnostech `Movie` třídy modelu).</span><span class="sxs-lookup"><span data-stu-id="fb975-125">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="fb975-126">Ověření testu pomocí stránky pro úpravy je použito stejné ověřování.</span><span class="sxs-lookup"><span data-stu-id="fb975-126">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="fb975-127">Data formuláře se na server neúčtují, dokud nedojde k žádným chybám při ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="fb975-127">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="fb975-128">Ověřte, že data formuláře nejsou publikovaná jedním nebo více z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fb975-128">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="fb975-129">Umístěte bod přerušení do `OnPostAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="fb975-129">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="fb975-130">Odešlete formulář (vyberte **vytvořit** nebo **Uložit**).</span><span class="sxs-lookup"><span data-stu-id="fb975-130">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="fb975-131">Není k dispozice žádný bod přerušení.</span><span class="sxs-lookup"><span data-stu-id="fb975-131">The break point is never hit.</span></span>
* <span data-ttu-id="fb975-132">Použijte [Nástroj Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="fb975-132">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="fb975-133">Pomocí vývojářských nástrojů pro prohlížeč můžete monitorovat síťový provoz.</span><span class="sxs-lookup"><span data-stu-id="fb975-133">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="fb975-134">Ověřování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="fb975-134">Server-side validation</span></span>

<span data-ttu-id="fb975-135">Pokud je v prohlížeči zakázán jazyk JavaScript, odesláním formuláře s chybami bude odesláno na server.</span><span class="sxs-lookup"><span data-stu-id="fb975-135">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="fb975-136">Volitelné, testovací ověřování na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="fb975-136">Optional, test server-side validation:</span></span>

* <span data-ttu-id="fb975-137">Zakáže JavaScript v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="fb975-137">Disable JavaScript in the browser.</span></span> <span data-ttu-id="fb975-138">JavaScript můžete zakázat pomocí vývojářských nástrojů v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="fb975-138">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="fb975-139">Pokud nemůžete zakázat JavaScript v prohlížeči, zkuste použít jiný prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="fb975-139">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="fb975-140">Nastavte bod přerušení v `OnPostAsync` metodě stránky vytvořit nebo upravit.</span><span class="sxs-lookup"><span data-stu-id="fb975-140">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="fb975-141">Odešle formulář s neplatnými daty.</span><span class="sxs-lookup"><span data-stu-id="fb975-141">Submit a form with invalid data.</span></span>
* <span data-ttu-id="fb975-142">Ověřte, že stav modelu není platný:</span><span class="sxs-lookup"><span data-stu-id="fb975-142">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

<span data-ttu-id="fb975-143">Následující kód ukazuje část vygenerovaného uživatelského rozhraní stránky *vytvořit. cshtml* dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="fb975-143">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="fb975-144">Používá se na stránkách vytvořit a upravit k zobrazení počátečního formuláře a k opětovnému zobrazení formuláře v případě chyby.</span><span class="sxs-lookup"><span data-stu-id="fb975-144">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="fb975-145">[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) používá atributy DataAnnotations a vytváří atributy HTML potřebné k ověření jQuery na straně klienta. [](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6)</span><span class="sxs-lookup"><span data-stu-id="fb975-145">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="fb975-146">[Pomocník pro ověřování značek](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazí chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="fb975-146">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="fb975-147">Další informace najdete v tématu [ověření](xref:mvc/models/validation) .</span><span class="sxs-lookup"><span data-stu-id="fb975-147">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="fb975-148">Na stránkách pro vytváření a úpravy nejsou v nich žádná ověřovací pravidla.</span><span class="sxs-lookup"><span data-stu-id="fb975-148">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="fb975-149">Ověřovací pravidla a řetězce chyb jsou určeny pouze ve `Movie` třídě.</span><span class="sxs-lookup"><span data-stu-id="fb975-149">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="fb975-150">Tato ověřovací pravidla se automaticky aplikují na Razor Pages, která `Movie` model upravují.</span><span class="sxs-lookup"><span data-stu-id="fb975-150">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="fb975-151">Když je potřeba logiku ověřování změnit, provede se jenom v modelu.</span><span class="sxs-lookup"><span data-stu-id="fb975-151">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="fb975-152">Ověřování se konzistentně používá v celé aplikaci (logika ověřování je definovaná na jednom místě).</span><span class="sxs-lookup"><span data-stu-id="fb975-152">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="fb975-153">Ověřování na jednom místě usnadňuje vyčištění kódu a usnadňuje údržbu a aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="fb975-153">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="fb975-154">Použití atributů DataType</span><span class="sxs-lookup"><span data-stu-id="fb975-154">Using DataType Attributes</span></span>

<span data-ttu-id="fb975-155">Prověřte `Movie` třídu.</span><span class="sxs-lookup"><span data-stu-id="fb975-155">Examine the `Movie` class.</span></span> <span data-ttu-id="fb975-156">`System.ComponentModel.DataAnnotations` Obor názvů poskytuje kromě předdefinované sady ověřovacích atributů i atributy formátování.</span><span class="sxs-lookup"><span data-stu-id="fb975-156">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="fb975-157">Atribut je použit `ReleaseDate` pro vlastnosti a `Price`. `DataType`</span><span class="sxs-lookup"><span data-stu-id="fb975-157">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="fb975-158">Atributy poskytují nápovědu pouze pro modul zobrazení k formátování dat (a poskytování atributů `<a>` , jako je například adresa URL a `<a href="mailto:EmailAddress.com">` e-mailu). `DataType`</span><span class="sxs-lookup"><span data-stu-id="fb975-158">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="fb975-159">`RegularExpression` Použijte atribut k ověření formátu dat.</span><span class="sxs-lookup"><span data-stu-id="fb975-159">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="fb975-160">`DataType` Atribut slouží k určení datového typu, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="fb975-160">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="fb975-161">`DataType`atributy nejsou ověřovány.</span><span class="sxs-lookup"><span data-stu-id="fb975-161">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="fb975-162">V ukázkové aplikaci se zobrazí pouze datum, a to bez času.</span><span class="sxs-lookup"><span data-stu-id="fb975-162">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="fb975-163">`DataType` Výčet poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress a další.</span><span class="sxs-lookup"><span data-stu-id="fb975-163">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="fb975-164">`DataType` Atribut může také povolit aplikaci automatické poskytování funkcí specifických pro typ.</span><span class="sxs-lookup"><span data-stu-id="fb975-164">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="fb975-165">Můžete například `mailto:` vytvořit odkaz pro `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="fb975-165">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="fb975-166">Selektor data lze zadat pro `DataType.Date` v prohlížečích, které podporují HTML5.</span><span class="sxs-lookup"><span data-stu-id="fb975-166">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="fb975-167">Atributy emitují atributy HTML 5 `data-` (vyslovované datové přerušované), které používají prohlížeče formátu HTML 5. `DataType`</span><span class="sxs-lookup"><span data-stu-id="fb975-167">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="fb975-168">Atributy neposkytují žádné ověřování.  `DataType`</span><span class="sxs-lookup"><span data-stu-id="fb975-168">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="fb975-169">`DataType.Date`neurčuje formát data, které se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="fb975-169">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="fb975-170">Ve výchozím nastavení se datové pole zobrazuje v závislosti na výchozích formátech založených na serveru `CultureInfo`.</span><span class="sxs-lookup"><span data-stu-id="fb975-170">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="fb975-171">Datová anotace je vyžadována, aby Entity Framework Core mohl správně `Price` mapovat na měnu v databázi. `[Column(TypeName = "decimal(18, 2)")]`</span><span class="sxs-lookup"><span data-stu-id="fb975-171">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="fb975-172">Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="fb975-172">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="fb975-173">`DisplayFormat` Atribut slouží k explicitnímu zadání formátu data:</span><span class="sxs-lookup"><span data-stu-id="fb975-173">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="fb975-174">Toto `ApplyFormatInEditMode` nastavení určuje, že se má formátování použít při zobrazení hodnoty pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="fb975-174">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="fb975-175">Pro některá pole možná nebudete chtít toto chování.</span><span class="sxs-lookup"><span data-stu-id="fb975-175">You might not want that behavior for some fields.</span></span> <span data-ttu-id="fb975-176">Například v hodnotách měn pravděpodobně nebudete chtít symbol měny v uživatelském rozhraní úprav.</span><span class="sxs-lookup"><span data-stu-id="fb975-176">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="fb975-177">Atribut může být použit sám sebe, ale obecně je vhodné `DataType` použít atribut. `DisplayFormat`</span><span class="sxs-lookup"><span data-stu-id="fb975-177">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="fb975-178">`DataType` Atribut předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce a poskytuje následující výhody, které nezískáte pomocí DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="fb975-178">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="fb975-179">Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolu měny odpovídající národním prostředí, e-mailových odkazů atd.)</span><span class="sxs-lookup"><span data-stu-id="fb975-179">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="fb975-180">Ve výchozím nastavení bude prohlížeč data vykreslovat pomocí správného formátu na základě vašeho národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="fb975-180">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="fb975-181">`DataType` Atribut může ASP.NET Core rozhraní povolit výběr pravé šablony pole k vykreslení dat.</span><span class="sxs-lookup"><span data-stu-id="fb975-181">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="fb975-182">V `DisplayFormat` případě, že se používá samostatně, používá šablonu řetězce.</span><span class="sxs-lookup"><span data-stu-id="fb975-182">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="fb975-183">Poznámka: ověřování jQuery nefunguje s `Range` atributem a. `DateTime`</span><span class="sxs-lookup"><span data-stu-id="fb975-183">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="fb975-184">Například následující kód bude vždy zobrazovat chybu ověřování na straně klienta, i když je datum v zadaném rozsahu:</span><span class="sxs-lookup"><span data-stu-id="fb975-184">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="fb975-185">Obvykle není dobrým zvykem při kompilování pevných dat ve vašich modelech, takže použití `Range` atributu a `DateTime` nedoporučuje se.</span><span class="sxs-lookup"><span data-stu-id="fb975-185">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="fb975-186">Následující kód ukazuje kombinování atributů na jednom řádku:</span><span class="sxs-lookup"><span data-stu-id="fb975-186">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="fb975-187">[Začínáme se Razor Pages a EF Core](xref:data/ef-rp/intro) zobrazuje pokročilé EF Core operace s Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="fb975-187">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="fb975-188">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="fb975-188">Apply migrations</span></span>

<span data-ttu-id="fb975-189">Tato dataanotace použitá pro třídu mění schéma.</span><span class="sxs-lookup"><span data-stu-id="fb975-189">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="fb975-190">Například dataanotace použité pro `Title` pole:</span><span class="sxs-lookup"><span data-stu-id="fb975-190">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="fb975-191">Omezí znaky na 60.</span><span class="sxs-lookup"><span data-stu-id="fb975-191">Limits the characters to 60.</span></span>
* <span data-ttu-id="fb975-192">Nepovoluje `null` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="fb975-192">Doesn't allow a `null` value.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fb975-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb975-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fb975-194">`Movie` Tabulka teď má následující schéma:</span><span class="sxs-lookup"><span data-stu-id="fb975-194">The `Movie` table currently has the following schema:</span></span>

``` sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="fb975-195">Předchozí změny schématu nezpůsobí výjimku EF k vyvolání výjimky.</span><span class="sxs-lookup"><span data-stu-id="fb975-195">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="fb975-196">Vytvořte ale migraci tak, aby schéma bylo konzistentní s modelem.</span><span class="sxs-lookup"><span data-stu-id="fb975-196">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="fb975-197">V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="fb975-197">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="fb975-198">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="fb975-198">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="fb975-199">`Update-Database``Up` spustí metody`New_DataAnnotations` třídy.</span><span class="sxs-lookup"><span data-stu-id="fb975-199">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="fb975-200">Projděte `Up` si metodu:</span><span class="sxs-lookup"><span data-stu-id="fb975-200">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="fb975-201">Aktualizovaná `Movie` tabulka má následující schéma:</span><span class="sxs-lookup"><span data-stu-id="fb975-201">The updated `Movie` table has the following schema:</span></span>

``` sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="fb975-202">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="fb975-202">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="fb975-203">Migrace nejsou pro SQLite požadovány.</span><span class="sxs-lookup"><span data-stu-id="fb975-203">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="fb975-204">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="fb975-204">Publish to Azure</span></span>

<span data-ttu-id="fb975-205">Informace o nasazení do Azure najdete v tématu [kurz: Sestavte ASP.NET Core aplikaci v Azure pomocí](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)SQL Database.</span><span class="sxs-lookup"><span data-stu-id="fb975-205">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="fb975-206">Děkujeme za dokončení tohoto úvodu do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="fb975-206">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="fb975-207">Začněte [s Razor Pages a EF Core](xref:data/ef-rp/intro) je vynikajícím postupem v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="fb975-207">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb975-208">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fb975-208">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="fb975-209">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="fb975-209">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="fb975-210">Předchozí Přidání nového pole</span><span class="sxs-lookup"><span data-stu-id="fb975-210">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)

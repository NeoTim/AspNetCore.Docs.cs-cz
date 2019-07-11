---
title: Přidání ověřování do stránky ASP.NET Core Razor
author: rick-anderson
description: Objevte, jak přidat ověření pro stránky Razor v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2018
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 8495849c89ca3d6fd2b2006b61ce2ec75ff504a5
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815667"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="e6fec-103">Přidání ověřování do stránky ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="e6fec-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="e6fec-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e6fec-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e6fec-105">V této části je přidat logiku ověřování k `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="e6fec-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="e6fec-106">Ověřovací pravidla se uplatňují kdykoli uživatel vytvoří nebo upraví videa.</span><span class="sxs-lookup"><span data-stu-id="e6fec-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="e6fec-107">Ověřování</span><span class="sxs-lookup"><span data-stu-id="e6fec-107">Validation</span></span>

<span data-ttu-id="e6fec-108">Klíčovým principem vývoj softwaru je volána [suchého](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**neměnit **R**epeat **Y**ourself").</span><span class="sxs-lookup"><span data-stu-id="e6fec-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="e6fec-109">Stránky Razor podporuje vývoj funkce určena jednou, kde se projeví v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e6fec-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="e6fec-110">ZKUŠEBNÍ může pomoct:</span><span class="sxs-lookup"><span data-stu-id="e6fec-110">DRY can help:</span></span>

* <span data-ttu-id="e6fec-111">Snížíte množství kódu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e6fec-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="e6fec-112">Změňte kód méně chyby náchylné k chybám a usnadňuje testování a udržovat.</span><span class="sxs-lookup"><span data-stu-id="e6fec-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="e6fec-113">Podpora ověřování poskytované stránkami Razor a technologií Entity Framework je typickým příkladem suchého zásady.</span><span class="sxs-lookup"><span data-stu-id="e6fec-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="e6fec-114">Ověřovací pravidla deklarativně zadávají se na jednom místě (ve třídě modelu) a pravidel se vynucují kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e6fec-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="e6fec-115">Chyba ověřování uživatelské rozhraní pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="e6fec-115">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="e6fec-116">Spusťte aplikaci a přejděte na stránky/filmy.</span><span class="sxs-lookup"><span data-stu-id="e6fec-116">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="e6fec-117">Vyberte **vytvořit nový** odkaz.</span><span class="sxs-lookup"><span data-stu-id="e6fec-117">Select the **Create New** link.</span></span> <span data-ttu-id="e6fec-118">Vyplňte formulář s některé neplatné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e6fec-118">Complete the form with some invalid values.</span></span> <span data-ttu-id="e6fec-119">Při ověřování jQuery na straně klienta zjistí chyby, zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="e6fec-119">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Video zobrazit formulář kvůli několika chybám ověření na straně klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

<span data-ttu-id="e6fec-121">Všimněte si, jak formuláře se automaticky vykreslen chybovou zprávu ověření v každé pole, který obsahuje neplatnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e6fec-121">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="e6fec-122">Chyby se vynucují straně klienta (pomocí jazyků JavaScript a jQuery) a na straně serveru (Pokud má uživatel zakázán jazyk JavaScript).</span><span class="sxs-lookup"><span data-stu-id="e6fec-122">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="e6fec-123">Významné výhodou je, že **žádné** změny kódu byly nezbytné ve vytvoření nebo úprava stránky.</span><span class="sxs-lookup"><span data-stu-id="e6fec-123">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="e6fec-124">Jakmile DataAnnotations byly použité pro model, bylo povoleno ověření uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e6fec-124">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="e6fec-125">Stránky Razor, v tomto kurzu automaticky vytvoří nenačítají ověřovacích pravidel (pomocí atributů ověření na vlastnosti `Movie` třída modelu).</span><span class="sxs-lookup"><span data-stu-id="e6fec-125">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="e6fec-126">Test ověření pomocí stránky pro úpravu stejného ověření se aplikuje.</span><span class="sxs-lookup"><span data-stu-id="e6fec-126">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="e6fec-127">Formulář dat není odeslána na server, dokud nejsou žádné chyby ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e6fec-127">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="e6fec-128">Zkontrolujte formulář, který není data zveřejnil(a) jeden nebo více z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="e6fec-128">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="e6fec-129">Vložit přerušení `OnPostAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="e6fec-129">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="e6fec-130">Odeslání formuláře (vyberte **vytvořit** nebo **Uložit**).</span><span class="sxs-lookup"><span data-stu-id="e6fec-130">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="e6fec-131">Nikdy dosáhnete bodu přerušení.</span><span class="sxs-lookup"><span data-stu-id="e6fec-131">The break point is never hit.</span></span>
* <span data-ttu-id="e6fec-132">Použití [nástroj Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="e6fec-132">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="e6fec-133">Monitorování síťového provozu pomocí vývojářských nástrojů prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e6fec-133">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="e6fec-134">Ověřování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="e6fec-134">Server-side validation</span></span>

<span data-ttu-id="e6fec-135">Pokud JavaScript je zakázaný v prohlížeči, odeslání formuláře s chybami bude účtovat na server.</span><span class="sxs-lookup"><span data-stu-id="e6fec-135">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="e6fec-136">Volitelný parametr, test ověření na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="e6fec-136">Optional, test server-side validation:</span></span>

* <span data-ttu-id="e6fec-137">Zakážete jazyka JavaScript v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e6fec-137">Disable JavaScript in the browser.</span></span> <span data-ttu-id="e6fec-138">Můžete provést pomocí nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e6fec-138">You can do this using your browser's developer tools.</span></span> <span data-ttu-id="e6fec-139">Pokud nelze zakázat jazyka JavaScript v prohlížeči, zkuste jiný prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="e6fec-139">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="e6fec-140">Nastavte zarážky `OnPostAsync` metoda vytvoření nebo úprava stránky.</span><span class="sxs-lookup"><span data-stu-id="e6fec-140">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="e6fec-141">Odeslání formuláře se chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="e6fec-141">Submit a form with validation errors.</span></span>
* <span data-ttu-id="e6fec-142">Ověřte, že stav modelu, který je neplatný:</span><span class="sxs-lookup"><span data-stu-id="e6fec-142">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

<span data-ttu-id="e6fec-143">Následující kód ukazuje část *Create.cshtml* stránka, která automaticky dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="e6fec-143">The following code shows a portion of the *Create.cshtml* page that you scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="e6fec-144">Používá se stránkami vytvořit a upravit počáteční formulář pro zobrazení a znovu zobrazit formulář v případě chyby.</span><span class="sxs-lookup"><span data-stu-id="e6fec-144">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="e6fec-145">[Pomocné rutiny značky vstup](xref:mvc/views/working-with-forms) používá [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) atributy a vytváří atributy HTML, které jsou potřeba pro architekturu jQuery ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e6fec-145">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="e6fec-146">[Pomocné rutiny značky ověření](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazí chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="e6fec-146">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="e6fec-147">Zobrazit [ověření](xref:mvc/models/validation) Další informace.</span><span class="sxs-lookup"><span data-stu-id="e6fec-147">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="e6fec-148">Vytvoření a úprava stránky mít žádné ověřovací pravidla v nich.</span><span class="sxs-lookup"><span data-stu-id="e6fec-148">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="e6fec-149">Ověřovací pravidla a chybové řetězce se zadávají pouze v `Movie` třídy.</span><span class="sxs-lookup"><span data-stu-id="e6fec-149">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="e6fec-150">Tato pravidla ověřování jsou automaticky použita pro stránky Razor, upravovat `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="e6fec-150">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="e6fec-151">Když je potřeba změnit logiku ověřování, se provádí pouze v modelu.</span><span class="sxs-lookup"><span data-stu-id="e6fec-151">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="e6fec-152">Ověření je konzistentní v rámci aplikace (logika ověřování je definována na jednom místě).</span><span class="sxs-lookup"><span data-stu-id="e6fec-152">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="e6fec-153">Ověřování na jednom místě udržet čistý kód a usnadňuje spravují a aktualizují.</span><span class="sxs-lookup"><span data-stu-id="e6fec-153">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="e6fec-154">Pomocí atributů datový typ</span><span class="sxs-lookup"><span data-stu-id="e6fec-154">Using DataType Attributes</span></span>

<span data-ttu-id="e6fec-155">Zkontrolujte `Movie` třídy.</span><span class="sxs-lookup"><span data-stu-id="e6fec-155">Examine the `Movie` class.</span></span> <span data-ttu-id="e6fec-156">`System.ComponentModel.DataAnnotations` Obor názvů obsahuje atributy formátování kromě integrovaná sada atributů ověření.</span><span class="sxs-lookup"><span data-stu-id="e6fec-156">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="e6fec-157">`DataType` Atribut aplikován `ReleaseDate` a `Price` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e6fec-157">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="e6fec-158">`DataType` Atributy pouze poskytnout nápovědu pro modul zobrazení pro zobrazení dat (a poskytuje atributy, jako `<a>` pro adresy URL a `<a href="mailto:EmailAddress.com">` e-mailu).</span><span class="sxs-lookup"><span data-stu-id="e6fec-158">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="e6fec-159">Použití `RegularExpression` atribut pro ověření formátu data.</span><span class="sxs-lookup"><span data-stu-id="e6fec-159">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="e6fec-160">`DataType` Atribut se používá k určení datový typ, který je specifičtější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="e6fec-160">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="e6fec-161">`DataType` atributy nejsou atributů ověření.</span><span class="sxs-lookup"><span data-stu-id="e6fec-161">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="e6fec-162">V ukázkové aplikaci se zobrazí pouze datum bez času.</span><span class="sxs-lookup"><span data-stu-id="e6fec-162">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="e6fec-163">`DataType` Výčet poskytuje pro mnoho typů dat, jako je například datum, čas, telefonní číslo, Měna, EmailAddress a další.</span><span class="sxs-lookup"><span data-stu-id="e6fec-163">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="e6fec-164">`DataType` Atribut můžete také povolit aplikace a zajistit tak automaticky specifické pro typ funkce.</span><span class="sxs-lookup"><span data-stu-id="e6fec-164">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="e6fec-165">Například `mailto:` propojení lze vytvořit pro `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="e6fec-165">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="e6fec-166">Výběr data lze zadat pro `DataType.Date` v prohlížečích podporujících HTML5.</span><span class="sxs-lookup"><span data-stu-id="e6fec-166">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="e6fec-167">`DataType` Atributy vysílá HTML 5 `data-` (čteno data dash) atributy, které využívají prohlížeče HTML 5.</span><span class="sxs-lookup"><span data-stu-id="e6fec-167">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="e6fec-168">`DataType` Proveďte atributy **není** žádné ověřování.</span><span class="sxs-lookup"><span data-stu-id="e6fec-168">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="e6fec-169">`DataType.Date` neurčuje formátu, který se zobrazí datum.</span><span class="sxs-lookup"><span data-stu-id="e6fec-169">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="e6fec-170">Ve výchozím nastavení, zobrazí se pole data podle výchozí formát založený na serveru `CultureInfo`.</span><span class="sxs-lookup"><span data-stu-id="e6fec-170">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="e6fec-171">`[Column(TypeName = "decimal(18, 2)")]` Anotace dat se vyžaduje, aby správně můžete mapovat Entity Framework Core `Price` měnu v databázi.</span><span class="sxs-lookup"><span data-stu-id="e6fec-171">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="e6fec-172">Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="e6fec-172">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="e6fec-173">`DisplayFormat` Atribut se používá s ohledem na formát data:</span><span class="sxs-lookup"><span data-stu-id="e6fec-173">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="e6fec-174">`ApplyFormatInEditMode` Nastavení určuje, že pokud je hodnota zobrazena pro úpravy, měl být použit formátování.</span><span class="sxs-lookup"><span data-stu-id="e6fec-174">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="e6fec-175">Toto chování není vhodné pro některá pole.</span><span class="sxs-lookup"><span data-stu-id="e6fec-175">You might not want that behavior for some fields.</span></span> <span data-ttu-id="e6fec-176">Například v hodnot měny, pravděpodobně nechcete symbol měny v režimu úpravy uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e6fec-176">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="e6fec-177">`DisplayFormat` Atribut lze použít samostatně, ale je obecně vhodné použít `DataType` atribut.</span><span class="sxs-lookup"><span data-stu-id="e6fec-177">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="e6fec-178">`DataType` Atribut přenáší sémantiku dat na rozdíl od vykreslování na obrazovce a nabízí následující výhody, které vám s DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="e6fec-178">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="e6fec-179">Prohlížeči můžete povolit funkce HTML5 (pro příklad, který znázorňuje ovládacího prvku kalendář, symbol měny odpovídající národní prostředí, odkazy na e-mailu, atd.)</span><span class="sxs-lookup"><span data-stu-id="e6fec-179">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="e6fec-180">Ve výchozím prohlížeči bude vykreslovat data ve správném formátu podle vašeho národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="e6fec-180">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="e6fec-181">`DataType` Atribut můžete povolit rozhraní ASP.NET Core pro výběr šablony pravé pole vykreslit data.</span><span class="sxs-lookup"><span data-stu-id="e6fec-181">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="e6fec-182">`DisplayFormat` Pokud používá sama používá šablony řetězce.</span><span class="sxs-lookup"><span data-stu-id="e6fec-182">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="e6fec-183">Poznámka: k ověřování jQuery nefunguje při využití `Range` atribut a `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="e6fec-183">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="e6fec-184">Například následující kód se vždy zobrazí chybu ověřování na straně klienta i v případě, že datum je v zadaném rozsahu:</span><span class="sxs-lookup"><span data-stu-id="e6fec-184">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="e6fec-185">Není obvykle vhodné pro kompilaci pevného data ve vašich modelů použít `Range` atribut a `DateTime` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="e6fec-185">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="e6fec-186">Následující kód ukazuje kombinace atributů na jednom řádku:</span><span class="sxs-lookup"><span data-stu-id="e6fec-186">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="e6fec-187">[Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) ukazuje pokročilé operace EF Core se stránkami Razor.</span><span class="sxs-lookup"><span data-stu-id="e6fec-187">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="publish-to-azure"></a><span data-ttu-id="e6fec-188">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="e6fec-188">Publish to Azure</span></span>

<span data-ttu-id="e6fec-189">Informace o nasazení do Azure najdete v tématu [kurzu: Vytvoření aplikace ASP.NET se službou SQL Database v Azure](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase).</span><span class="sxs-lookup"><span data-stu-id="e6fec-189">For information on deploying to Azure, see [Tutorial: Build an ASP.NET app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase).</span></span> <span data-ttu-id="e6fec-190">Tyto pokyny jsou určené pro aplikace ASP.NET, není aplikace v ASP.NET Core, ale postup je stejný.</span><span class="sxs-lookup"><span data-stu-id="e6fec-190">These instructions are for an ASP.NET app, not an ASP.NET Core app, but the steps are the same.</span></span>

<span data-ttu-id="e6fec-191">Děkujeme vám za dokončení tohoto úvodu do stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="e6fec-191">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="e6fec-192">[Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) je vynikající postupujte až v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="e6fec-192">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6fec-193">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e6fec-193">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="e6fec-194">Verzi tohoto kurzu na webu YouTube</span><span class="sxs-lookup"><span data-stu-id="e6fec-194">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="e6fec-195">Předchozí: Přidání nového pole</span><span class="sxs-lookup"><span data-stu-id="e6fec-195">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)

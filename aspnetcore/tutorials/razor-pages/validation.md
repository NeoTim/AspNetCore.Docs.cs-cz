---
title: Přidání ověřování do stránky ASP.NET Core Razor
author: rick-anderson
description: Objevte, jak přidat ověření pro stránky Razor v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f46bddb618d2a030e29b7dfa1671ea53b0d4bcc2
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021349"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="392c1-103">Přidání ověřování do stránky ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="392c1-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="392c1-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="392c1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="392c1-105">V této části je přidat logiku ověřování k `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="392c1-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="392c1-106">Ověřovací pravidla se uplatňují kdykoli uživatel vytvoří nebo upraví videa.</span><span class="sxs-lookup"><span data-stu-id="392c1-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="392c1-107">Ověřování</span><span class="sxs-lookup"><span data-stu-id="392c1-107">Validation</span></span>

<span data-ttu-id="392c1-108">Klíčovým principem vývoj softwaru je volána [suchého](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**neměnit **R**epeat **Y**ourself").</span><span class="sxs-lookup"><span data-stu-id="392c1-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="392c1-109">Stránky Razor podporuje vývoj funkce určena jednou, kde se projeví v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="392c1-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="392c1-110">ZKUŠEBNÍ může pomoct snížit objem kódu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="392c1-110">DRY can help reduce the amount of code in an app.</span></span> <span data-ttu-id="392c1-111">ZKUŠEBNÍ díky kód náchylné k chybám a usnadňuje testování a udržovat méně chyb.</span><span class="sxs-lookup"><span data-stu-id="392c1-111">DRY makes the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="392c1-112">Podpora ověřování poskytované stránkami Razor a technologií Entity Framework je typickým příkladem suchého zásady.</span><span class="sxs-lookup"><span data-stu-id="392c1-112">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="392c1-113">Ověřovací pravidla deklarativně zadávají se na jednom místě (ve třídě modelu) a pravidel se vynucují kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="392c1-113">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

### <a name="adding-validation-rules-to-the-movie-model"></a><span data-ttu-id="392c1-114">Přidání pravidel ověřování do modelu movie</span><span class="sxs-lookup"><span data-stu-id="392c1-114">Adding validation rules to the movie model</span></span>

<span data-ttu-id="392c1-115">Otevřít *Models/Movie.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="392c1-115">Open the *Models/Movie.cs* file.</span></span> <span data-ttu-id="392c1-116">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) poskytuje integrovanou sadu atributů ověření, která se použijí deklarativně třída nebo vlastnost.</span><span class="sxs-lookup"><span data-stu-id="392c1-116">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) provides a built-in set of validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="392c1-117">DataAnnotations taky obsahuje atributy formátování, jako je `DataType` , pomoct při formátování a neposkytují ověření.</span><span class="sxs-lookup"><span data-stu-id="392c1-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide validation.</span></span>

<span data-ttu-id="392c1-118">Aktualizace `Movie` třídy výhod `Required`, `StringLength`, `RegularExpression`, a `Range` atributů ověření.</span><span class="sxs-lookup"><span data-stu-id="392c1-118">Update the `Movie` class to take advantage of the `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRatingDA.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21/Models/MovieDateRatingDA.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="392c1-119">Ověření atributy určují chování, které je vynucuje na vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="392c1-119">Validation attributes specify behavior that's enforced on model properties:</span></span>

* <span data-ttu-id="392c1-120">`Required` a `MinimumLength` atributy znamená, že vlastnost musí mít hodnotu.</span><span class="sxs-lookup"><span data-stu-id="392c1-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="392c1-121">Ale nic zabraňuje uživateli v zadávání prázdné znaky omezení ověření pro typ připouštějící hodnotu Null.</span><span class="sxs-lookup"><span data-stu-id="392c1-121">However, nothing prevents a user from entering whitespace to satisfy the validation constraint for a nullable type.</span></span> <span data-ttu-id="392c1-122">Neumožňující [typů hodnot](/dotnet/csharp/language-reference/keywords/value-types) (například `decimal`, `int`, `float`, a `DateTime`) jsou ze své podstaty povinné a nemusíte `Required` atribut.</span><span class="sxs-lookup"><span data-stu-id="392c1-122">Non-nullable [value types](/dotnet/csharp/language-reference/keywords/value-types) (such as `decimal`, `int`, `float`, and `DateTime`) are inherently required and don't need the `Required` attribute.</span></span>
* <span data-ttu-id="392c1-123">`RegularExpression` Atribut omezuje znaky, které může uživatel zadat.</span><span class="sxs-lookup"><span data-stu-id="392c1-123">The `RegularExpression` attribute limits the characters that the user can enter.</span></span> <span data-ttu-id="392c1-124">V předchozím kódu `Genre` musí začínat jeden nebo více písmeny a postupujte podle s nula nebo více písmeny, jednoduché nebo dvojité uvozovky, prázdné znaky nebo spojovníky.</span><span class="sxs-lookup"><span data-stu-id="392c1-124">In the preceding code, `Genre` must start with one or more capital letters and follow with zero or more letters, single or double quotes, whitespace characters, or dashes.</span></span> <span data-ttu-id="392c1-125">`Rating` musí začínat jeden nebo více písmeny a postupujte podle se nula nebo více písmena, čísla, jednoduché nebo dvojité uvozovky, prázdné znaky nebo spojovníky.</span><span class="sxs-lookup"><span data-stu-id="392c1-125">`Rating` must start with one or more capital letters and follow with zero or more letters, numbers, single or double quotes, whitespace characters, or dashes.</span></span>
* <span data-ttu-id="392c1-126">`Range` Atribut omezuje hodnotu do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="392c1-126">The `Range` attribute constrains a value to a specified range.</span></span>
* <span data-ttu-id="392c1-127">`StringLength` Atribut Nastaví maximální délku řetězce a volitelně minimální délku.</span><span class="sxs-lookup"><span data-stu-id="392c1-127">The `StringLength` attribute sets the maximum length of a string, and optionally the minimum length.</span></span> 

<span data-ttu-id="392c1-128">Ověřovací pravidla automaticky vynucuje sada ASP.NET Core s pomáhá vytvářet aplikace robustnější.</span><span class="sxs-lookup"><span data-stu-id="392c1-128">Having validation rules automatically enforced by ASP.NET Core helps make an app more robust.</span></span> <span data-ttu-id="392c1-129">Automatické ověření u modelů pomáhá chránit aplikace, protože nebudou muset pamatovat jejich použití, když se přidá nový kód.</span><span class="sxs-lookup"><span data-stu-id="392c1-129">Automatic validation on models helps protect the app because you don't have to remember to apply them when new code is added.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="392c1-130">Chyba ověřování uživatelské rozhraní pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="392c1-130">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="392c1-131">Spusťte aplikaci a přejděte na stránky/filmy.</span><span class="sxs-lookup"><span data-stu-id="392c1-131">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="392c1-132">Vyberte **vytvořit nový** odkaz.</span><span class="sxs-lookup"><span data-stu-id="392c1-132">Select the **Create New** link.</span></span> <span data-ttu-id="392c1-133">Vyplňte formulář s některé neplatné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="392c1-133">Complete the form with some invalid values.</span></span> <span data-ttu-id="392c1-134">Při ověřování jQuery na straně klienta zjistí chyby, zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="392c1-134">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Video zobrazit formulář kvůli několika chybám ověření na straně klienta jQuery](validation/_static/val.png)

> [!NOTE]
> <span data-ttu-id="392c1-136">Není možné zadávat desetinné tečky a čárky v `Price` pole.</span><span class="sxs-lookup"><span data-stu-id="392c1-136">You may not be able to enter decimal points or commas in the `Price` field.</span></span> <span data-ttu-id="392c1-137">Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) v jiných jazycích než angličtině, které používají čárkou (",") pro desetinné čárky a USA retweetovat neanglické formáty kalendářního data, je nutné provést kroky aplikaci poslali do světa.</span><span class="sxs-lookup"><span data-stu-id="392c1-137">To support [jQuery validation](https://jqueryvalidation.org/) in non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="392c1-138">Zobrazit [další prostředky](#additional-resources) Další informace.</span><span class="sxs-lookup"><span data-stu-id="392c1-138">See [Additional resources](#additional-resources) for more information.</span></span> <span data-ttu-id="392c1-139">Teď zadejte celá čísla, jako je 10.</span><span class="sxs-lookup"><span data-stu-id="392c1-139">For now, just enter whole numbers like 10.</span></span>

<span data-ttu-id="392c1-140">Všimněte si, jak formuláře se automaticky vykreslen chybovou zprávu ověření v každé pole, který obsahuje neplatnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="392c1-140">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="392c1-141">Chyby se vynucují straně klienta (pomocí jazyků JavaScript a jQuery) a na straně serveru (Pokud má uživatel zakázán jazyk JavaScript).</span><span class="sxs-lookup"><span data-stu-id="392c1-141">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="392c1-142">Významné výhodou je, že **žádné** změny kódu byly nezbytné ve vytvoření nebo úprava stránky.</span><span class="sxs-lookup"><span data-stu-id="392c1-142">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="392c1-143">Jakmile DataAnnotations byly použité pro model, bylo povoleno ověření uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="392c1-143">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="392c1-144">Stránky Razor, v tomto kurzu automaticky vytvoří nenačítají ověřovacích pravidel (pomocí atributů ověření na vlastnosti `Movie` třída modelu).</span><span class="sxs-lookup"><span data-stu-id="392c1-144">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="392c1-145">Test ověření pomocí stránky pro úpravu stejného ověření se aplikuje.</span><span class="sxs-lookup"><span data-stu-id="392c1-145">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="392c1-146">Formulář dat není odeslána na server, dokud nejsou žádné chyby ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="392c1-146">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="392c1-147">Zkontrolujte formulář, který není data zveřejnil(a) jeden nebo více z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="392c1-147">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="392c1-148">Vložit přerušení `OnPostAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="392c1-148">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="392c1-149">Odeslání formuláře (vyberte **vytvořit** nebo **Uložit**).</span><span class="sxs-lookup"><span data-stu-id="392c1-149">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="392c1-150">Nikdy dosáhnete bodu přerušení.</span><span class="sxs-lookup"><span data-stu-id="392c1-150">The break point is never hit.</span></span>
* <span data-ttu-id="392c1-151">Použití [nástroj Fiddler](http://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="392c1-151">Use the [Fiddler tool](http://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="392c1-152">Monitorování síťového provozu pomocí vývojářských nástrojů prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="392c1-152">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="392c1-153">Ověřování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="392c1-153">Server-side validation</span></span>

<span data-ttu-id="392c1-154">Pokud JavaScript je zakázaný v prohlížeči, odeslání formuláře s chybami bude účtovat na server.</span><span class="sxs-lookup"><span data-stu-id="392c1-154">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="392c1-155">Volitelný parametr, test ověření na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="392c1-155">Optional, test server-side validation:</span></span>

* <span data-ttu-id="392c1-156">Zakážete jazyka JavaScript v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="392c1-156">Disable JavaScript in the browser.</span></span> <span data-ttu-id="392c1-157">Můžete provést pomocí nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="392c1-157">You can do this using your browser's developer tools.</span></span> <span data-ttu-id="392c1-158">Pokud nelze zakázat jazyka JavaScript v prohlížeči, zkuste jiný prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="392c1-158">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="392c1-159">Nastavte zarážky `OnPostAsync` metoda vytvoření nebo úprava stránky.</span><span class="sxs-lookup"><span data-stu-id="392c1-159">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="392c1-160">Odeslání formuláře se chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="392c1-160">Submit a form with validation errors.</span></span>
* <span data-ttu-id="392c1-161">Ověřte, že stav modelu, který je neplatný:</span><span class="sxs-lookup"><span data-stu-id="392c1-161">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

<span data-ttu-id="392c1-162">Následující kód ukazuje část *Create.cshtml* stránka, která automaticky dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="392c1-162">The following code shows a portion of the *Create.cshtml* page that you scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="392c1-163">Používá se stránkami vytvořit a upravit počáteční formulář pro zobrazení a znovu zobrazit formulář v případě chyby.</span><span class="sxs-lookup"><span data-stu-id="392c1-163">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="392c1-164">[Pomocné rutiny značky vstup](xref:mvc/views/working-with-forms) používá [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) atributy a vytváří atributy HTML, které jsou potřeba pro architekturu jQuery ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="392c1-164">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="392c1-165">[Pomocné rutiny značky ověření](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazí chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="392c1-165">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="392c1-166">Zobrazit [ověření](xref:mvc/models/validation) Další informace.</span><span class="sxs-lookup"><span data-stu-id="392c1-166">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="392c1-167">Vytvoření a úprava stránky mít žádné ověřovací pravidla v nich.</span><span class="sxs-lookup"><span data-stu-id="392c1-167">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="392c1-168">Ověřovací pravidla a chybové řetězce se zadávají pouze v `Movie` třídy.</span><span class="sxs-lookup"><span data-stu-id="392c1-168">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="392c1-169">Tato pravidla ověřování jsou automaticky použita pro stránky Razor, upravovat `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="392c1-169">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="392c1-170">Když je potřeba změnit logiku ověřování, se provádí pouze v modelu.</span><span class="sxs-lookup"><span data-stu-id="392c1-170">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="392c1-171">Ověření je konzistentní v rámci aplikace (logika ověřování je definována na jednom místě).</span><span class="sxs-lookup"><span data-stu-id="392c1-171">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="392c1-172">Ověřování na jednom místě udržet čistý kód a usnadňuje spravují a aktualizují.</span><span class="sxs-lookup"><span data-stu-id="392c1-172">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="392c1-173">Pomocí atributů datový typ</span><span class="sxs-lookup"><span data-stu-id="392c1-173">Using DataType Attributes</span></span>

<span data-ttu-id="392c1-174">Zkontrolujte `Movie` třídy.</span><span class="sxs-lookup"><span data-stu-id="392c1-174">Examine the `Movie` class.</span></span> <span data-ttu-id="392c1-175">`System.ComponentModel.DataAnnotations` Obor názvů obsahuje atributy formátování kromě integrovaná sada atributů ověření.</span><span class="sxs-lookup"><span data-stu-id="392c1-175">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="392c1-176">`DataType` Atribut aplikován `ReleaseDate` a `Price` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="392c1-176">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="392c1-177">`DataType` Atributy pouze poskytnout nápovědu pro modul zobrazení pro zobrazení dat (a poskytuje atributy, jako `<a>` pro adresy URL a `<a href="mailto:EmailAddress.com">` e-mailu).</span><span class="sxs-lookup"><span data-stu-id="392c1-177">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="392c1-178">Použití `RegularExpression` atribut pro ověření formátu data.</span><span class="sxs-lookup"><span data-stu-id="392c1-178">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="392c1-179">`DataType` Atribut se používá k určení datový typ, který je specifičtější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="392c1-179">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="392c1-180">`DataType` atributy nejsou atributů ověření.</span><span class="sxs-lookup"><span data-stu-id="392c1-180">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="392c1-181">V ukázkové aplikaci se zobrazí pouze datum bez času.</span><span class="sxs-lookup"><span data-stu-id="392c1-181">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="392c1-182">`DataType` Výčet poskytuje pro mnoho typů dat, jako je například datum, čas, telefonní číslo, Měna, EmailAddress a další.</span><span class="sxs-lookup"><span data-stu-id="392c1-182">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="392c1-183">`DataType` Atribut můžete také povolit aplikace a zajistit tak automaticky specifické pro typ funkce.</span><span class="sxs-lookup"><span data-stu-id="392c1-183">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="392c1-184">Například `mailto:` propojení lze vytvořit pro `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="392c1-184">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="392c1-185">Výběr data lze zadat pro `DataType.Date` v prohlížečích podporujících HTML5.</span><span class="sxs-lookup"><span data-stu-id="392c1-185">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="392c1-186">`DataType` Atributy vysílá HTML 5 `data-` (čteno data dash) atributy, které využívají prohlížeče HTML 5.</span><span class="sxs-lookup"><span data-stu-id="392c1-186">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="392c1-187">`DataType` Proveďte atributy **není** žádné ověřování.</span><span class="sxs-lookup"><span data-stu-id="392c1-187">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="392c1-188">`DataType.Date` neurčuje formátu, který se zobrazí datum.</span><span class="sxs-lookup"><span data-stu-id="392c1-188">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="392c1-189">Ve výchozím nastavení, zobrazí se pole data podle výchozí formát založený na serveru `CultureInfo`.</span><span class="sxs-lookup"><span data-stu-id="392c1-189">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="392c1-190">`[Column(TypeName = "decimal(18, 2)")]` Anotace dat se vyžaduje, aby správně můžete mapovat Entity Framework Core `Price` měnu v databázi.</span><span class="sxs-lookup"><span data-stu-id="392c1-190">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="392c1-191">Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="392c1-191">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

::: moniker-end

<span data-ttu-id="392c1-192">`DisplayFormat` Atribut se používá s ohledem na formát data:</span><span class="sxs-lookup"><span data-stu-id="392c1-192">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="392c1-193">`ApplyFormatInEditMode` Nastavení určuje, že pokud je hodnota zobrazena pro úpravy, měl být použit formátování.</span><span class="sxs-lookup"><span data-stu-id="392c1-193">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="392c1-194">Toto chování není vhodné pro některá pole.</span><span class="sxs-lookup"><span data-stu-id="392c1-194">You might not want that behavior for some fields.</span></span> <span data-ttu-id="392c1-195">Například v hodnot měny, pravděpodobně nechcete symbol měny v režimu úpravy uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="392c1-195">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="392c1-196">`DisplayFormat` Atribut lze použít samostatně, ale je obecně vhodné použít `DataType` atribut.</span><span class="sxs-lookup"><span data-stu-id="392c1-196">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="392c1-197">`DataType` Atribut přenáší sémantiku dat na rozdíl od vykreslování na obrazovce a nabízí následující výhody, které vám s DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="392c1-197">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="392c1-198">Prohlížeči můžete povolit funkce HTML5 (pro příklad, který znázorňuje ovládacího prvku kalendář, symbol měny odpovídající národní prostředí, odkazy na e-mailu, atd.)</span><span class="sxs-lookup"><span data-stu-id="392c1-198">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="392c1-199">Ve výchozím prohlížeči bude vykreslovat data ve správném formátu podle vašeho národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="392c1-199">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="392c1-200">`DataType` Atribut můžete povolit rozhraní ASP.NET Core pro výběr šablony pravé pole vykreslit data.</span><span class="sxs-lookup"><span data-stu-id="392c1-200">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="392c1-201">`DisplayFormat` Pokud používá sama používá šablony řetězce.</span><span class="sxs-lookup"><span data-stu-id="392c1-201">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="392c1-202">Poznámka: k ověřování jQuery nefunguje při využití `Range` atribut a `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="392c1-202">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="392c1-203">Například následující kód se vždy zobrazí chybu ověřování na straně klienta i v případě, že datum je v zadaném rozsahu:</span><span class="sxs-lookup"><span data-stu-id="392c1-203">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="392c1-204">Není obvykle vhodné pro kompilaci pevného data ve vašich modelů použít `Range` atribut a `DateTime` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="392c1-204">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="392c1-205">Následující kód ukazuje kombinace atributů na jednom řádku:</span><span class="sxs-lookup"><span data-stu-id="392c1-205">The following code shows combining attributes on one line:</span></span>

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDAmult.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Models/MovieDateRatingDAmult.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="392c1-206">[Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) ukazuje pokročilé operace EF Core se stránkami Razor.</span><span class="sxs-lookup"><span data-stu-id="392c1-206">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="publish-to-azure"></a><span data-ttu-id="392c1-207">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="392c1-207">Publish to Azure</span></span>

<span data-ttu-id="392c1-208">Informace o nasazení do Azure, viz [kurz: vytvoření aplikace ASP.NET se službou SQL Database v Azure](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase).</span><span class="sxs-lookup"><span data-stu-id="392c1-208">For information on deploying to Azure, See [Tutorial: Build an ASP.NET app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase).</span></span> <span data-ttu-id="392c1-209">Tyto pokyny jsou určené pro aplikace ASP.NET, není aplikace v ASP.NET Core, ale postup je stejný.</span><span class="sxs-lookup"><span data-stu-id="392c1-209">These instructions are for an ASP.NET app, not an ASP.NET Core app, but the steps are the same.</span></span>

<span data-ttu-id="392c1-210">Děkujeme vám za dokončení tohoto úvodu do stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="392c1-210">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="392c1-211">Děkujeme za zpětnou vazbu.</span><span class="sxs-lookup"><span data-stu-id="392c1-211">We appreciate feedback.</span></span> <span data-ttu-id="392c1-212">[Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) je vynikající postupujte až v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="392c1-212">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="392c1-213">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="392c1-213">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="392c1-214">Předchozí: Přidání nového pole</span><span class="sxs-lookup"><span data-stu-id="392c1-214">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)

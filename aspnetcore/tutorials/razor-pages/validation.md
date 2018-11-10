---
title: Přidání ověřování do stránky ASP.NET Core Razor
author: rick-anderson
description: Objevte, jak přidat ověření pro stránky Razor v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: tutorials/razor-pages/validation
ms.openlocfilehash: d4cc0ab9de314c0c5a1a9016efd1e566ff1c47d2
ms.sourcegitcommit: edb9d2d78c9a4d68b397e74ae2aff088b325a143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51505775"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="de3cf-103">Přidání ověřování do stránky ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="de3cf-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="de3cf-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="de3cf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="de3cf-105">V této části je přidat logiku ověřování k `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="de3cf-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="de3cf-106">Ověřovací pravidla se uplatňují kdykoli uživatel vytvoří nebo upraví videa.</span><span class="sxs-lookup"><span data-stu-id="de3cf-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="de3cf-107">Ověřování</span><span class="sxs-lookup"><span data-stu-id="de3cf-107">Validation</span></span>

<span data-ttu-id="de3cf-108">Klíčovým principem vývoj softwaru je volána [suchého](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**neměnit **R**epeat **Y**ourself").</span><span class="sxs-lookup"><span data-stu-id="de3cf-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="de3cf-109">Stránky Razor podporuje vývoj funkce určena jednou, kde se projeví v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="de3cf-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="de3cf-110">ZKUŠEBNÍ může pomoct snížit objem kódu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="de3cf-110">DRY can help reduce the amount of code in an app.</span></span> <span data-ttu-id="de3cf-111">ZKUŠEBNÍ díky kód náchylné k chybám a usnadňuje testování a udržovat méně chyb.</span><span class="sxs-lookup"><span data-stu-id="de3cf-111">DRY makes the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="de3cf-112">Podpora ověřování poskytované stránkami Razor a technologií Entity Framework je typickým příkladem suchého zásady.</span><span class="sxs-lookup"><span data-stu-id="de3cf-112">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="de3cf-113">Ověřovací pravidla deklarativně zadávají se na jednom místě (ve třídě modelu) a pravidel se vynucují kdekoli v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="de3cf-113">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

### <a name="adding-validation-rules-to-the-movie-model"></a><span data-ttu-id="de3cf-114">Přidání pravidel ověřování do modelu movie</span><span class="sxs-lookup"><span data-stu-id="de3cf-114">Adding validation rules to the movie model</span></span>

<span data-ttu-id="de3cf-115">Otevřít *Models/Movie.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="de3cf-115">Open the *Models/Movie.cs* file.</span></span> <span data-ttu-id="de3cf-116">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) poskytuje integrovanou sadu atributů ověření, která se použijí deklarativně třída nebo vlastnost.</span><span class="sxs-lookup"><span data-stu-id="de3cf-116">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) provides a built-in set of validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="de3cf-117">DataAnnotations taky obsahuje atributy formátování, jako je `DataType` , pomoct při formátování a neposkytují ověření.</span><span class="sxs-lookup"><span data-stu-id="de3cf-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide validation.</span></span>

<span data-ttu-id="de3cf-118">Aktualizace `Movie` třídy výhod `Required`, `StringLength`, `RegularExpression`, a `Range` atributů ověření.</span><span class="sxs-lookup"><span data-stu-id="de3cf-118">Update the `Movie` class to take advantage of the `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRatingDA.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21/Models/MovieDateRatingDA.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="de3cf-119">Ověření atributy určují chování, které je vynucuje na vlastnosti modelu:</span><span class="sxs-lookup"><span data-stu-id="de3cf-119">Validation attributes specify behavior that's enforced on model properties:</span></span>

* <span data-ttu-id="de3cf-120">`Required` a `MinimumLength` atributy znamená, že vlastnost musí mít hodnotu.</span><span class="sxs-lookup"><span data-stu-id="de3cf-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="de3cf-121">Ale nic zabraňuje uživateli v zadávání prázdné znaky omezení ověření pro typ připouštějící hodnotu Null.</span><span class="sxs-lookup"><span data-stu-id="de3cf-121">However, nothing prevents a user from entering whitespace to satisfy the validation constraint for a nullable type.</span></span> <span data-ttu-id="de3cf-122">Neumožňující [typů hodnot](/dotnet/csharp/language-reference/keywords/value-types) (například `decimal`, `int`, `float`, a `DateTime`) jsou ze své podstaty povinné a nemusíte `Required` atribut.</span><span class="sxs-lookup"><span data-stu-id="de3cf-122">Non-nullable [value types](/dotnet/csharp/language-reference/keywords/value-types) (such as `decimal`, `int`, `float`, and `DateTime`) are inherently required and don't need the `Required` attribute.</span></span>
* <span data-ttu-id="de3cf-123">`RegularExpression` Atribut omezuje znaky, které může uživatel zadat.</span><span class="sxs-lookup"><span data-stu-id="de3cf-123">The `RegularExpression` attribute limits the characters that the user can enter.</span></span> <span data-ttu-id="de3cf-124">V předchozím kódu `Genre` musí začínat jeden nebo více písmeny a postupujte podle s nula nebo více písmeny, jednoduché nebo dvojité uvozovky, prázdné znaky nebo spojovníky.</span><span class="sxs-lookup"><span data-stu-id="de3cf-124">In the preceding code, `Genre` must start with one or more capital letters and follow with zero or more letters, single or double quotes, whitespace characters, or dashes.</span></span> <span data-ttu-id="de3cf-125">`Rating` musí začínat jeden nebo více písmeny a postupujte podle se nula nebo více písmena, čísla, jednoduché nebo dvojité uvozovky, prázdné znaky nebo spojovníky.</span><span class="sxs-lookup"><span data-stu-id="de3cf-125">`Rating` must start with one or more capital letters and follow with zero or more letters, numbers, single or double quotes, whitespace characters, or dashes.</span></span>
* <span data-ttu-id="de3cf-126">`Range` Atribut omezuje hodnotu do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="de3cf-126">The `Range` attribute constrains a value to a specified range.</span></span>
* <span data-ttu-id="de3cf-127">`StringLength` Atribut Nastaví maximální délku řetězce a volitelně minimální délku.</span><span class="sxs-lookup"><span data-stu-id="de3cf-127">The `StringLength` attribute sets the maximum length of a string, and optionally the minimum length.</span></span> 

<span data-ttu-id="de3cf-128">Ověřovací pravidla automaticky vynucuje sada ASP.NET Core s pomáhá vytvářet aplikace robustnější.</span><span class="sxs-lookup"><span data-stu-id="de3cf-128">Having validation rules automatically enforced by ASP.NET Core helps make an app more robust.</span></span> <span data-ttu-id="de3cf-129">Automatické ověření u modelů pomáhá chránit aplikace, protože nebudou muset pamatovat jejich použití, když se přidá nový kód.</span><span class="sxs-lookup"><span data-stu-id="de3cf-129">Automatic validation on models helps protect the app because you don't have to remember to apply them when new code is added.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="de3cf-130">Chyba ověřování uživatelské rozhraní pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="de3cf-130">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="de3cf-131">Spusťte aplikaci a přejděte na stránky/filmy.</span><span class="sxs-lookup"><span data-stu-id="de3cf-131">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="de3cf-132">Vyberte **vytvořit nový** odkaz.</span><span class="sxs-lookup"><span data-stu-id="de3cf-132">Select the **Create New** link.</span></span> <span data-ttu-id="de3cf-133">Vyplňte formulář s některé neplatné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="de3cf-133">Complete the form with some invalid values.</span></span> <span data-ttu-id="de3cf-134">Při ověřování jQuery na straně klienta zjistí chyby, zobrazí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="de3cf-134">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Video zobrazit formulář kvůli několika chybám ověření na straně klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

<span data-ttu-id="de3cf-136">Všimněte si, jak formuláře se automaticky vykreslen chybovou zprávu ověření v každé pole, který obsahuje neplatnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="de3cf-136">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="de3cf-137">Chyby se vynucují straně klienta (pomocí jazyků JavaScript a jQuery) a na straně serveru (Pokud má uživatel zakázán jazyk JavaScript).</span><span class="sxs-lookup"><span data-stu-id="de3cf-137">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="de3cf-138">Významné výhodou je, že **žádné** změny kódu byly nezbytné ve vytvoření nebo úprava stránky.</span><span class="sxs-lookup"><span data-stu-id="de3cf-138">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="de3cf-139">Jakmile DataAnnotations byly použité pro model, bylo povoleno ověření uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="de3cf-139">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="de3cf-140">Stránky Razor, v tomto kurzu automaticky vytvoří nenačítají ověřovacích pravidel (pomocí atributů ověření na vlastnosti `Movie` třída modelu).</span><span class="sxs-lookup"><span data-stu-id="de3cf-140">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="de3cf-141">Test ověření pomocí stránky pro úpravu stejného ověření se aplikuje.</span><span class="sxs-lookup"><span data-stu-id="de3cf-141">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="de3cf-142">Formulář dat není odeslána na server, dokud nejsou žádné chyby ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="de3cf-142">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="de3cf-143">Zkontrolujte formulář, který není data zveřejnil(a) jeden nebo více z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="de3cf-143">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="de3cf-144">Vložit přerušení `OnPostAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="de3cf-144">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="de3cf-145">Odeslání formuláře (vyberte **vytvořit** nebo **Uložit**).</span><span class="sxs-lookup"><span data-stu-id="de3cf-145">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="de3cf-146">Nikdy dosáhnete bodu přerušení.</span><span class="sxs-lookup"><span data-stu-id="de3cf-146">The break point is never hit.</span></span>
* <span data-ttu-id="de3cf-147">Použití [nástroj Fiddler](http://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="de3cf-147">Use the [Fiddler tool](http://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="de3cf-148">Monitorování síťového provozu pomocí vývojářských nástrojů prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="de3cf-148">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="de3cf-149">Ověřování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="de3cf-149">Server-side validation</span></span>

<span data-ttu-id="de3cf-150">Pokud JavaScript je zakázaný v prohlížeči, odeslání formuláře s chybami bude účtovat na server.</span><span class="sxs-lookup"><span data-stu-id="de3cf-150">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="de3cf-151">Volitelný parametr, test ověření na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="de3cf-151">Optional, test server-side validation:</span></span>

* <span data-ttu-id="de3cf-152">Zakážete jazyka JavaScript v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="de3cf-152">Disable JavaScript in the browser.</span></span> <span data-ttu-id="de3cf-153">Můžete provést pomocí nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="de3cf-153">You can do this using your browser's developer tools.</span></span> <span data-ttu-id="de3cf-154">Pokud nelze zakázat jazyka JavaScript v prohlížeči, zkuste jiný prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="de3cf-154">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="de3cf-155">Nastavte zarážky `OnPostAsync` metoda vytvoření nebo úprava stránky.</span><span class="sxs-lookup"><span data-stu-id="de3cf-155">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="de3cf-156">Odeslání formuláře se chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="de3cf-156">Submit a form with validation errors.</span></span>
* <span data-ttu-id="de3cf-157">Ověřte, že stav modelu, který je neplatný:</span><span class="sxs-lookup"><span data-stu-id="de3cf-157">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

<span data-ttu-id="de3cf-158">Následující kód ukazuje část *Create.cshtml* stránka, která automaticky dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="de3cf-158">The following code shows a portion of the *Create.cshtml* page that you scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="de3cf-159">Používá se stránkami vytvořit a upravit počáteční formulář pro zobrazení a znovu zobrazit formulář v případě chyby.</span><span class="sxs-lookup"><span data-stu-id="de3cf-159">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="de3cf-160">[Pomocné rutiny značky vstup](xref:mvc/views/working-with-forms) používá [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) atributy a vytváří atributy HTML, které jsou potřeba pro architekturu jQuery ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="de3cf-160">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="de3cf-161">[Pomocné rutiny značky ověření](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazí chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="de3cf-161">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="de3cf-162">Zobrazit [ověření](xref:mvc/models/validation) Další informace.</span><span class="sxs-lookup"><span data-stu-id="de3cf-162">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="de3cf-163">Vytvoření a úprava stránky mít žádné ověřovací pravidla v nich.</span><span class="sxs-lookup"><span data-stu-id="de3cf-163">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="de3cf-164">Ověřovací pravidla a chybové řetězce se zadávají pouze v `Movie` třídy.</span><span class="sxs-lookup"><span data-stu-id="de3cf-164">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="de3cf-165">Tato pravidla ověřování jsou automaticky použita pro stránky Razor, upravovat `Movie` modelu.</span><span class="sxs-lookup"><span data-stu-id="de3cf-165">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="de3cf-166">Když je potřeba změnit logiku ověřování, se provádí pouze v modelu.</span><span class="sxs-lookup"><span data-stu-id="de3cf-166">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="de3cf-167">Ověření je konzistentní v rámci aplikace (logika ověřování je definována na jednom místě).</span><span class="sxs-lookup"><span data-stu-id="de3cf-167">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="de3cf-168">Ověřování na jednom místě udržet čistý kód a usnadňuje spravují a aktualizují.</span><span class="sxs-lookup"><span data-stu-id="de3cf-168">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="de3cf-169">Pomocí atributů datový typ</span><span class="sxs-lookup"><span data-stu-id="de3cf-169">Using DataType Attributes</span></span>

<span data-ttu-id="de3cf-170">Zkontrolujte `Movie` třídy.</span><span class="sxs-lookup"><span data-stu-id="de3cf-170">Examine the `Movie` class.</span></span> <span data-ttu-id="de3cf-171">`System.ComponentModel.DataAnnotations` Obor názvů obsahuje atributy formátování kromě integrovaná sada atributů ověření.</span><span class="sxs-lookup"><span data-stu-id="de3cf-171">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="de3cf-172">`DataType` Atribut aplikován `ReleaseDate` a `Price` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="de3cf-172">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="de3cf-173">`DataType` Atributy pouze poskytnout nápovědu pro modul zobrazení pro zobrazení dat (a poskytuje atributy, jako `<a>` pro adresy URL a `<a href="mailto:EmailAddress.com">` e-mailu).</span><span class="sxs-lookup"><span data-stu-id="de3cf-173">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="de3cf-174">Použití `RegularExpression` atribut pro ověření formátu data.</span><span class="sxs-lookup"><span data-stu-id="de3cf-174">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="de3cf-175">`DataType` Atribut se používá k určení datový typ, který je specifičtější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="de3cf-175">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="de3cf-176">`DataType` atributy nejsou atributů ověření.</span><span class="sxs-lookup"><span data-stu-id="de3cf-176">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="de3cf-177">V ukázkové aplikaci se zobrazí pouze datum bez času.</span><span class="sxs-lookup"><span data-stu-id="de3cf-177">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="de3cf-178">`DataType` Výčet poskytuje pro mnoho typů dat, jako je například datum, čas, telefonní číslo, Měna, EmailAddress a další.</span><span class="sxs-lookup"><span data-stu-id="de3cf-178">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="de3cf-179">`DataType` Atribut můžete také povolit aplikace a zajistit tak automaticky specifické pro typ funkce.</span><span class="sxs-lookup"><span data-stu-id="de3cf-179">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="de3cf-180">Například `mailto:` propojení lze vytvořit pro `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="de3cf-180">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="de3cf-181">Výběr data lze zadat pro `DataType.Date` v prohlížečích podporujících HTML5.</span><span class="sxs-lookup"><span data-stu-id="de3cf-181">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="de3cf-182">`DataType` Atributy vysílá HTML 5 `data-` (čteno data dash) atributy, které využívají prohlížeče HTML 5.</span><span class="sxs-lookup"><span data-stu-id="de3cf-182">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="de3cf-183">`DataType` Proveďte atributy **není** žádné ověřování.</span><span class="sxs-lookup"><span data-stu-id="de3cf-183">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="de3cf-184">`DataType.Date` neurčuje formátu, který se zobrazí datum.</span><span class="sxs-lookup"><span data-stu-id="de3cf-184">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="de3cf-185">Ve výchozím nastavení, zobrazí se pole data podle výchozí formát založený na serveru `CultureInfo`.</span><span class="sxs-lookup"><span data-stu-id="de3cf-185">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="de3cf-186">`[Column(TypeName = "decimal(18, 2)")]` Anotace dat se vyžaduje, aby správně můžete mapovat Entity Framework Core `Price` měnu v databázi.</span><span class="sxs-lookup"><span data-stu-id="de3cf-186">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="de3cf-187">Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="de3cf-187">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

::: moniker-end

<span data-ttu-id="de3cf-188">`DisplayFormat` Atribut se používá s ohledem na formát data:</span><span class="sxs-lookup"><span data-stu-id="de3cf-188">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="de3cf-189">`ApplyFormatInEditMode` Nastavení určuje, že pokud je hodnota zobrazena pro úpravy, měl být použit formátování.</span><span class="sxs-lookup"><span data-stu-id="de3cf-189">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="de3cf-190">Toto chování není vhodné pro některá pole.</span><span class="sxs-lookup"><span data-stu-id="de3cf-190">You might not want that behavior for some fields.</span></span> <span data-ttu-id="de3cf-191">Například v hodnot měny, pravděpodobně nechcete symbol měny v režimu úpravy uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="de3cf-191">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="de3cf-192">`DisplayFormat` Atribut lze použít samostatně, ale je obecně vhodné použít `DataType` atribut.</span><span class="sxs-lookup"><span data-stu-id="de3cf-192">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="de3cf-193">`DataType` Atribut přenáší sémantiku dat na rozdíl od vykreslování na obrazovce a nabízí následující výhody, které vám s DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="de3cf-193">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="de3cf-194">Prohlížeči můžete povolit funkce HTML5 (pro příklad, který znázorňuje ovládacího prvku kalendář, symbol měny odpovídající národní prostředí, odkazy na e-mailu, atd.)</span><span class="sxs-lookup"><span data-stu-id="de3cf-194">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="de3cf-195">Ve výchozím prohlížeči bude vykreslovat data ve správném formátu podle vašeho národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="de3cf-195">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="de3cf-196">`DataType` Atribut můžete povolit rozhraní ASP.NET Core pro výběr šablony pravé pole vykreslit data.</span><span class="sxs-lookup"><span data-stu-id="de3cf-196">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="de3cf-197">`DisplayFormat` Pokud používá sama používá šablony řetězce.</span><span class="sxs-lookup"><span data-stu-id="de3cf-197">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="de3cf-198">Poznámka: k ověřování jQuery nefunguje při využití `Range` atribut a `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="de3cf-198">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="de3cf-199">Například následující kód se vždy zobrazí chybu ověřování na straně klienta i v případě, že datum je v zadaném rozsahu:</span><span class="sxs-lookup"><span data-stu-id="de3cf-199">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="de3cf-200">Není obvykle vhodné pro kompilaci pevného data ve vašich modelů použít `Range` atribut a `DateTime` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="de3cf-200">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="de3cf-201">Následující kód ukazuje kombinace atributů na jednom řádku:</span><span class="sxs-lookup"><span data-stu-id="de3cf-201">The following code shows combining attributes on one line:</span></span>

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDAmult.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Models/MovieDateRatingDAmult.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="de3cf-202">[Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) ukazuje pokročilé operace EF Core se stránkami Razor.</span><span class="sxs-lookup"><span data-stu-id="de3cf-202">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="publish-to-azure"></a><span data-ttu-id="de3cf-203">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="de3cf-203">Publish to Azure</span></span>

<span data-ttu-id="de3cf-204">Informace o nasazení do Azure, viz [kurz: vytvoření aplikace ASP.NET se službou SQL Database v Azure](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase).</span><span class="sxs-lookup"><span data-stu-id="de3cf-204">For information on deploying to Azure, See [Tutorial: Build an ASP.NET app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnet-sqldatabase).</span></span> <span data-ttu-id="de3cf-205">Tyto pokyny jsou určené pro aplikace ASP.NET, není aplikace v ASP.NET Core, ale postup je stejný.</span><span class="sxs-lookup"><span data-stu-id="de3cf-205">These instructions are for an ASP.NET app, not an ASP.NET Core app, but the steps are the same.</span></span>

<span data-ttu-id="de3cf-206">Děkujeme vám za dokončení tohoto úvodu do stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="de3cf-206">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="de3cf-207">Děkujeme za zpětnou vazbu.</span><span class="sxs-lookup"><span data-stu-id="de3cf-207">We appreciate feedback.</span></span> <span data-ttu-id="de3cf-208">[Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) je vynikající postupujte až v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="de3cf-208">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="de3cf-209">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="de3cf-209">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="de3cf-210">Předchozí: Přidání nového pole</span><span class="sxs-lookup"><span data-stu-id="de3cf-210">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)

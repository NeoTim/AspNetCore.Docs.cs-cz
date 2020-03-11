---
title: Přidání ověřování na stránku ASP.NET Core Razor
author: rick-anderson
description: Zjistěte, jak přidat ověřování na stránku Razor v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f283234ed8a32dc9b7904bc6fee1cc9c04741029
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666017"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="d20bf-103">Přidání ověřování na stránku ASP.NET Core Razor</span><span class="sxs-lookup"><span data-stu-id="d20bf-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="d20bf-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d20bf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d20bf-105">V této části je logika ověřování přidána do modelu `Movie`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="d20bf-106">Ověřovací pravidla se vynutily pokaždé, když uživatel vytvoří nebo upraví film.</span><span class="sxs-lookup"><span data-stu-id="d20bf-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="d20bf-107">Ověření</span><span class="sxs-lookup"><span data-stu-id="d20bf-107">Validation</span></span>

<span data-ttu-id="d20bf-108">Key principem vývoje softwaru se nazývá [suchý](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**EPEAT **Y**ourself").</span><span class="sxs-lookup"><span data-stu-id="d20bf-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="d20bf-109">Razor Pages podporuje vývoj, ve kterém jsou funkce zadány jednou a které se projeví v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d20bf-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="d20bf-110">Bezsuchá může pomáhat:</span><span class="sxs-lookup"><span data-stu-id="d20bf-110">DRY can help:</span></span>

* <span data-ttu-id="d20bf-111">Snižte množství kódu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d20bf-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="d20bf-112">Udělejte kód méně náchylné k chybám a usnadněte si testování a údržbu.</span><span class="sxs-lookup"><span data-stu-id="d20bf-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="d20bf-113">Podpora ověřování, kterou poskytuje Razor Pages a Entity Framework, je dobrým příkladem SUCHÉho principu.</span><span class="sxs-lookup"><span data-stu-id="d20bf-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="d20bf-114">Ověřovací pravidla jsou deklarativně určena na jednom místě (ve třídě modelu) a pravidla jsou vynutila všude v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d20bf-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="d20bf-115">Přidání ověřovacích pravidel do modelu filmů</span><span class="sxs-lookup"><span data-stu-id="d20bf-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="d20bf-116">Obor názvů DataAnnotations poskytuje sadu předdefinovaných ověřovacích atributů, které se aplikují deklarativně na třídu nebo vlastnost.</span><span class="sxs-lookup"><span data-stu-id="d20bf-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="d20bf-117">Tato dataanotace také obsahuje atributy formátování, jako `DataType`, které vám pomůžou s formátováním a neposkytují žádné ověřování.</span><span class="sxs-lookup"><span data-stu-id="d20bf-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="d20bf-118">Aktualizujte třídu `Movie`, abyste mohli využívat předdefinované atributy ověřování `Required`, `StringLength`, `RegularExpression`a `Range`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="d20bf-119">Atributy ověřování určují chování, které chcete vyhovět pro vlastnosti modelu, na které se aplikují:</span><span class="sxs-lookup"><span data-stu-id="d20bf-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="d20bf-120">Atributy `Required` a `MinimumLength` označují, že vlastnost musí mít hodnotu. ale nic nebrání uživateli v zadání prázdného místa pro splnění tohoto ověření.</span><span class="sxs-lookup"><span data-stu-id="d20bf-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="d20bf-121">Atribut `RegularExpression` slouží k omezení znaků, které lze zadat.</span><span class="sxs-lookup"><span data-stu-id="d20bf-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="d20bf-122">V předchozím kódu "Žánr":</span><span class="sxs-lookup"><span data-stu-id="d20bf-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="d20bf-123">Je nutné použít pouze písmena.</span><span class="sxs-lookup"><span data-stu-id="d20bf-123">Must only use letters.</span></span>
  * <span data-ttu-id="d20bf-124">První písmeno musí být velkými písmeny.</span><span class="sxs-lookup"><span data-stu-id="d20bf-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="d20bf-125">Mezery, číslice a speciální znaky nejsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="d20bf-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="d20bf-126">`RegularExpression` "hodnocení":</span><span class="sxs-lookup"><span data-stu-id="d20bf-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="d20bf-127">Vyžaduje, aby byl první znak velkým písmenem.</span><span class="sxs-lookup"><span data-stu-id="d20bf-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="d20bf-128">Umožňuje speciální znaky a čísla v následujících mezerách.</span><span class="sxs-lookup"><span data-stu-id="d20bf-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="d20bf-129">"PG-13" je platné pro hodnocení, ale pro "Žánr" se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="d20bf-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="d20bf-130">Atribut `Range` omezuje hodnotu na v zadaném rozsahu.</span><span class="sxs-lookup"><span data-stu-id="d20bf-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="d20bf-131">Atribut `StringLength` umožňuje nastavit maximální délku řetězcové vlastnosti a volitelně její minimální délku.</span><span class="sxs-lookup"><span data-stu-id="d20bf-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="d20bf-132">Typy hodnot (například `decimal`, `int`, `float`, `DateTime`) jsou podstatou požadovány a nepotřebují atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="d20bf-133">Automatické vynucení ověřovacích pravidel nástrojem ASP.NET Core pomáhá zajistit větší odolnost aplikace.</span><span class="sxs-lookup"><span data-stu-id="d20bf-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="d20bf-134">Také zajišťuje, že se nebudete moci zapomenout a neúmyslně ověřit data v databázi.</span><span class="sxs-lookup"><span data-stu-id="d20bf-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="d20bf-135">Uživatelské rozhraní chyby ověřování v Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d20bf-135">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="d20bf-136">Spusťte aplikaci a přejděte na stránky/filmy.</span><span class="sxs-lookup"><span data-stu-id="d20bf-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="d20bf-137">Vyberte odkaz **vytvořit nový** .</span><span class="sxs-lookup"><span data-stu-id="d20bf-137">Select the **Create New** link.</span></span> <span data-ttu-id="d20bf-138">Vyplňte formulář s neplatnými hodnotami.</span><span class="sxs-lookup"><span data-stu-id="d20bf-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="d20bf-139">Když při ověřování na straně klienta zjistí chybu, zobrazí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="d20bf-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formulář zobrazení videa s několika chybami ověřování na straně klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="d20bf-141">Všimněte si, jak formulář automaticky vykresluje chybovou zprávu ověřování v každém poli, které obsahuje neplatnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d20bf-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="d20bf-142">Chyby se vynutily na straně klienta (pomocí JavaScriptu a jQuery) a na straně serveru (když má uživatel zakázaný JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d20bf-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="d20bf-143">Významnou výhodou je, že na stránkách vytvořit nebo upravit nebyly nutné **žádné** změny kódu.</span><span class="sxs-lookup"><span data-stu-id="d20bf-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="d20bf-144">Jakmile se v modelu aplikují dataanotace, uživatelské rozhraní ověřování je povolené.</span><span class="sxs-lookup"><span data-stu-id="d20bf-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="d20bf-145">Razor Pages vytvořená v tomto kurzu automaticky vybrala ověřovací pravidla (pomocí atributů ověřování ve vlastnostech třídy modelu `Movie`).</span><span class="sxs-lookup"><span data-stu-id="d20bf-145">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="d20bf-146">Ověření testu pomocí stránky pro úpravy je použito stejné ověřování.</span><span class="sxs-lookup"><span data-stu-id="d20bf-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="d20bf-147">Data formuláře se na server neúčtují, dokud nedojde k žádným chybám při ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d20bf-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="d20bf-148">Ověřte, že data formuláře nejsou publikovaná jedním nebo více z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d20bf-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="d20bf-149">Umístěte bod přerušení do metody `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="d20bf-150">Odešlete formulář (vyberte **vytvořit** nebo **Uložit**).</span><span class="sxs-lookup"><span data-stu-id="d20bf-150">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="d20bf-151">Není k dispozice žádný bod přerušení.</span><span class="sxs-lookup"><span data-stu-id="d20bf-151">The break point is never hit.</span></span>
* <span data-ttu-id="d20bf-152">Použijte [Nástroj Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="d20bf-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="d20bf-153">Pomocí vývojářských nástrojů pro prohlížeč můžete monitorovat síťový provoz.</span><span class="sxs-lookup"><span data-stu-id="d20bf-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="d20bf-154">Ověřování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="d20bf-154">Server-side validation</span></span>

<span data-ttu-id="d20bf-155">Pokud je v prohlížeči zakázán jazyk JavaScript, odesláním formuláře s chybami bude odesláno na server.</span><span class="sxs-lookup"><span data-stu-id="d20bf-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="d20bf-156">Volitelné, testovací ověřování na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="d20bf-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="d20bf-157">Zakáže JavaScript v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d20bf-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="d20bf-158">JavaScript můžete zakázat pomocí vývojářských nástrojů v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d20bf-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="d20bf-159">Pokud nemůžete zakázat JavaScript v prohlížeči, zkuste použít jiný prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="d20bf-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="d20bf-160">Nastavte bod přerušení v metodě `OnPostAsync` stránky vytvořit nebo upravit.</span><span class="sxs-lookup"><span data-stu-id="d20bf-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="d20bf-161">Odešle formulář s neplatnými daty.</span><span class="sxs-lookup"><span data-stu-id="d20bf-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="d20bf-162">Ověřte, že stav modelu není platný:</span><span class="sxs-lookup"><span data-stu-id="d20bf-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="d20bf-163">Případně můžete [na serveru vypnout ověřování na straně klienta](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="d20bf-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="d20bf-164">Následující kód ukazuje část vygenerovaného uživatelského rozhraní stránky *vytvořit. cshtml* dříve v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="d20bf-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="d20bf-165">Používá se na stránkách vytvořit a upravit k zobrazení počátečního formuláře a k opětovnému zobrazení formuláře v případě chyby.</span><span class="sxs-lookup"><span data-stu-id="d20bf-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="d20bf-166">[Pomocná rutina vstupní značky](xref:mvc/views/working-with-forms) používá atributy [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné k ověření jQuery na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="d20bf-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="d20bf-167">[Pomocník pro ověřování značek](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazí chyby ověřování.</span><span class="sxs-lookup"><span data-stu-id="d20bf-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="d20bf-168">Další informace najdete v tématu [ověření](xref:mvc/models/validation) .</span><span class="sxs-lookup"><span data-stu-id="d20bf-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="d20bf-169">Na stránkách pro vytváření a úpravy nejsou v nich žádná ověřovací pravidla.</span><span class="sxs-lookup"><span data-stu-id="d20bf-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="d20bf-170">Ověřovací pravidla a řetězce chyb jsou určeny pouze ve třídě `Movie`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="d20bf-171">Tato ověřovací pravidla se automaticky aplikují na Razor Pages, která upravují model `Movie`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-171">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="d20bf-172">Když je potřeba logiku ověřování změnit, provede se jenom v modelu.</span><span class="sxs-lookup"><span data-stu-id="d20bf-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="d20bf-173">Ověřování se konzistentně používá v celé aplikaci (logika ověřování je definovaná na jednom místě).</span><span class="sxs-lookup"><span data-stu-id="d20bf-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="d20bf-174">Ověřování na jednom místě usnadňuje vyčištění kódu a usnadňuje údržbu a aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="d20bf-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="d20bf-175">Použití atributů DataType</span><span class="sxs-lookup"><span data-stu-id="d20bf-175">Using DataType Attributes</span></span>

<span data-ttu-id="d20bf-176">Prověřte třídu `Movie`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-176">Examine the `Movie` class.</span></span> <span data-ttu-id="d20bf-177">Obor názvů `System.ComponentModel.DataAnnotations` poskytuje kromě předdefinované sady ověřovacích atributů také atributy formátování.</span><span class="sxs-lookup"><span data-stu-id="d20bf-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="d20bf-178">Atribut `DataType` se aplikuje na vlastnosti `ReleaseDate` a `Price`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="d20bf-179">Atributy `DataType` poskytují pouze pomocné parametry pro modul zobrazení k formátování dat (a poskytování atributů, jako je například `<a>` pro adresu URL a `<a href="mailto:EmailAddress.com">` pro e-maily).</span><span class="sxs-lookup"><span data-stu-id="d20bf-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="d20bf-180">Použijte atribut `RegularExpression` k ověření formátu dat.</span><span class="sxs-lookup"><span data-stu-id="d20bf-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="d20bf-181">Atribut `DataType` slouží k zadání datového typu, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="d20bf-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="d20bf-182">atributy `DataType` nejsou ověřovacími atributy.</span><span class="sxs-lookup"><span data-stu-id="d20bf-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="d20bf-183">V ukázkové aplikaci se zobrazí pouze datum, a to bez času.</span><span class="sxs-lookup"><span data-stu-id="d20bf-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="d20bf-184">Výčet `DataType` poskytuje mnoho datových typů, jako je datum, čas, PhoneNumber, měna, EmailAddress a další.</span><span class="sxs-lookup"><span data-stu-id="d20bf-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="d20bf-185">Atribut `DataType` může také povolit aplikaci automatické poskytování funkcí specifických pro typ.</span><span class="sxs-lookup"><span data-stu-id="d20bf-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="d20bf-186">Například odkaz `mailto:` lze vytvořit pro `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="d20bf-187">Selektor data lze zadat pro `DataType.Date` v prohlížečích, které podporují HTML5.</span><span class="sxs-lookup"><span data-stu-id="d20bf-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="d20bf-188">Atributy `DataType` emitují atributy HTML 5 `data-` (vyslovované datové pomlčky), které používají prohlížeče formátu HTML 5.</span><span class="sxs-lookup"><span data-stu-id="d20bf-188">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="d20bf-189">Atributy `DataType` **neposkytují žádné** ověřování.</span><span class="sxs-lookup"><span data-stu-id="d20bf-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="d20bf-190">`DataType.Date` neurčuje formát data, které se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="d20bf-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="d20bf-191">Ve výchozím nastavení se datové pole zobrazuje v závislosti na výchozích formátech na základě `CultureInfo`serveru.</span><span class="sxs-lookup"><span data-stu-id="d20bf-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="d20bf-192">Datová anotace `[Column(TypeName = "decimal(18, 2)")]` je vyžadována, aby Entity Framework Core mohl správně mapovat `Price` na měnu v databázi.</span><span class="sxs-lookup"><span data-stu-id="d20bf-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="d20bf-193">Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="d20bf-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="d20bf-194">Atribut `DisplayFormat` slouží k explicitnímu zadání formátu data:</span><span class="sxs-lookup"><span data-stu-id="d20bf-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="d20bf-195">Nastavení `ApplyFormatInEditMode` určuje, zda má být použito formátování při zobrazení hodnoty pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="d20bf-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="d20bf-196">Pro některá pole možná nebudete chtít toto chování.</span><span class="sxs-lookup"><span data-stu-id="d20bf-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="d20bf-197">Například v hodnotách měn pravděpodobně nebudete chtít symbol měny v uživatelském rozhraní úprav.</span><span class="sxs-lookup"><span data-stu-id="d20bf-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="d20bf-198">Atribut `DisplayFormat` může být použit samostatně, ale obecně je vhodné použít atribut `DataType`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="d20bf-199">Atribut `DataType` předává sémantiku dat na rozdíl od způsobu vykreslování na obrazovce a poskytuje následující výhody, které nezískáte pomocí DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="d20bf-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="d20bf-200">Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolu měny odpovídající národním prostředí, e-mailových odkazů atd.)</span><span class="sxs-lookup"><span data-stu-id="d20bf-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="d20bf-201">Ve výchozím nastavení bude prohlížeč data vykreslovat pomocí správného formátu na základě vašeho národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="d20bf-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="d20bf-202">Atribut `DataType` může povolit ASP.NET Coremu rozhraní vybrat šablonu pravého pole pro vykreslení dat.</span><span class="sxs-lookup"><span data-stu-id="d20bf-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="d20bf-203">`DisplayFormat`, pokud se používá samostatně, používá šablonu řetězce.</span><span class="sxs-lookup"><span data-stu-id="d20bf-203">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="d20bf-204">Poznámka: ověřování jQuery nefunguje s atributem `Range` a `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-204">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="d20bf-205">Například následující kód bude vždy zobrazovat chybu ověřování na straně klienta, i když je datum v zadaném rozsahu:</span><span class="sxs-lookup"><span data-stu-id="d20bf-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="d20bf-206">Obvykle není dobrým zvykem při kompilování pevných dat ve vašich modelech, takže použijte atribut `Range` a `DateTime` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="d20bf-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="d20bf-207">Následující kód ukazuje kombinování atributů na jednom řádku:</span><span class="sxs-lookup"><span data-stu-id="d20bf-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="d20bf-208">[Začínáme se Razor Pages a EF Core](xref:data/ef-rp/intro) zobrazuje pokročilé EF Core operace s Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d20bf-208">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="d20bf-209">Použít migrace</span><span class="sxs-lookup"><span data-stu-id="d20bf-209">Apply migrations</span></span>

<span data-ttu-id="d20bf-210">Tato dataanotace použitá pro třídu mění schéma.</span><span class="sxs-lookup"><span data-stu-id="d20bf-210">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="d20bf-211">Například dataanotace použité pro pole `Title`:</span><span class="sxs-lookup"><span data-stu-id="d20bf-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="d20bf-212">Omezí znaky na 60.</span><span class="sxs-lookup"><span data-stu-id="d20bf-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="d20bf-213">Nepovoluje hodnotu `null`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d20bf-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d20bf-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d20bf-215">Tabulka `Movie` aktuálně má následující schéma:</span><span class="sxs-lookup"><span data-stu-id="d20bf-215">The `Movie` table currently has the following schema:</span></span>

```sql
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

<span data-ttu-id="d20bf-216">Předchozí změny schématu nezpůsobí výjimku EF k vyvolání výjimky.</span><span class="sxs-lookup"><span data-stu-id="d20bf-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="d20bf-217">Vytvořte ale migraci tak, aby schéma bylo konzistentní s modelem.</span><span class="sxs-lookup"><span data-stu-id="d20bf-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="d20bf-218">V nabídce **nástroje** vyberte **správce balíčků NuGet > konzolu Správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="d20bf-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="d20bf-219">V konzole PMC zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d20bf-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="d20bf-220">`Update-Database` spouští `Up` metody třídy `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="d20bf-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="d20bf-221">Projděte si metodu `Up`:</span><span class="sxs-lookup"><span data-stu-id="d20bf-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="d20bf-222">Aktualizovaná `Movie` tabulka má následující schéma:</span><span class="sxs-lookup"><span data-stu-id="d20bf-222">The updated `Movie` table has the following schema:</span></span>

```sql
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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d20bf-223">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d20bf-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d20bf-224">Migrace nejsou pro SQLite požadovány.</span><span class="sxs-lookup"><span data-stu-id="d20bf-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="d20bf-225">Publikování aplikací do Azure</span><span class="sxs-lookup"><span data-stu-id="d20bf-225">Publish to Azure</span></span>

<span data-ttu-id="d20bf-226">Informace o nasazení do Azure najdete v tématu [kurz: sestavení aplikace ASP.NET Core v Azure pomocí SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="d20bf-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="d20bf-227">Děkujeme za dokončení tohoto úvodu do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="d20bf-227">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="d20bf-228">Začněte [s Razor Pages a EF Core](xref:data/ef-rp/intro) je vynikajícím postupem v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="d20bf-228">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d20bf-229">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d20bf-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="d20bf-230">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="d20bf-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="d20bf-231">Předchozí: Přidání nového pole</span><span class="sxs-lookup"><span data-stu-id="d20bf-231">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)

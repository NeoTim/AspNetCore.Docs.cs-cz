---
title: Přidání ověření na stránku ASP.NET core razor
author: rick-anderson
description: Zjistěte, jak přidat ověření na stránku Razor v ASP.NET jádra.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f283234ed8a32dc9b7904bc6fee1cc9c04741029
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666017"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="286ad-103">Přidání ověření na stránku ASP.NET core razor</span><span class="sxs-lookup"><span data-stu-id="286ad-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="286ad-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="286ad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="286ad-105">V této části je do `Movie` modelu přidána logika ověření.</span><span class="sxs-lookup"><span data-stu-id="286ad-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="286ad-106">Ověřovací pravidla jsou vynucena pokaždé, když uživatel vytvoří nebo uvede film.</span><span class="sxs-lookup"><span data-stu-id="286ad-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="286ad-107">Ověřování</span><span class="sxs-lookup"><span data-stu-id="286ad-107">Validation</span></span>

<span data-ttu-id="286ad-108">Klíčovým principem vývoje softwaru se nazývá [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) **("D**on't **R**epeat **Y**ourself").</span><span class="sxs-lookup"><span data-stu-id="286ad-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="286ad-109">Razor Pages podporuje vývoj, kde je jednou zadána funkčnost a odráží se v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="286ad-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="286ad-110">DRY může pomoci:</span><span class="sxs-lookup"><span data-stu-id="286ad-110">DRY can help:</span></span>

* <span data-ttu-id="286ad-111">Snižte množství kódu v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="286ad-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="286ad-112">Aby kód méně náchylné k chybám a snadněji testovat a udržovat.</span><span class="sxs-lookup"><span data-stu-id="286ad-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="286ad-113">Podpora validace poskytovaná razor pages a entity framework je dobrým příkladem principu DRY.</span><span class="sxs-lookup"><span data-stu-id="286ad-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="286ad-114">Ověřovací pravidla jsou deklarativně zadána na jednom místě (ve třídě modelu) a pravidla jsou vynucena všude v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="286ad-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="286ad-115">Přidání ověřovacích pravidel do filmového modelu</span><span class="sxs-lookup"><span data-stu-id="286ad-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="286ad-116">Obor názvů DataAnnotations poskytuje sadu předdefinovaných ověřovacích atributů, které jsou deklarativně použity na třídu nebo vlastnost.</span><span class="sxs-lookup"><span data-stu-id="286ad-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="286ad-117">DataAnnotations také obsahuje atributy `DataType` formátování, jako je to, které pomáhají s formátováním a neposkytují žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="286ad-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="286ad-118">Aktualizujte `Movie` třídu, abyste využili `Required`výhod `StringLength` `RegularExpression`předdefinovaných atributů , a `Range` validation.</span><span class="sxs-lookup"><span data-stu-id="286ad-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="286ad-119">Atributy ověření určují chování, které chcete vynutit u vlastností modelu, na které se použijí:</span><span class="sxs-lookup"><span data-stu-id="286ad-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="286ad-120">`Required` Atributy `MinimumLength` a označují, že vlastnost musí mít hodnotu; ale nic nebrání uživateli v zadání prázdného místa k uspokojení tohoto ověření.</span><span class="sxs-lookup"><span data-stu-id="286ad-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="286ad-121">Atribut `RegularExpression` se používá k omezení, jaké znaky lze zadat.</span><span class="sxs-lookup"><span data-stu-id="286ad-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="286ad-122">V předchozím kódu "Žánr":</span><span class="sxs-lookup"><span data-stu-id="286ad-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="286ad-123">Musí používat pouze písmena.</span><span class="sxs-lookup"><span data-stu-id="286ad-123">Must only use letters.</span></span>
  * <span data-ttu-id="286ad-124">První písmeno musí být velkými písmeny.</span><span class="sxs-lookup"><span data-stu-id="286ad-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="286ad-125">Prázdné znaky, čísla a speciální znaky nejsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="286ad-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="286ad-126">"Hodnocení": `RegularExpression`</span><span class="sxs-lookup"><span data-stu-id="286ad-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="286ad-127">Vyžaduje, aby první znak byl velké písmeno.</span><span class="sxs-lookup"><span data-stu-id="286ad-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="286ad-128">Umožňuje speciální znaky a čísla v následujících mezerách.</span><span class="sxs-lookup"><span data-stu-id="286ad-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="286ad-129">"PG-13" je platný pro hodnocení, ale selže pro "Žánr".</span><span class="sxs-lookup"><span data-stu-id="286ad-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="286ad-130">Atribut `Range` omezuje hodnotu v konkrétním rozsahu.</span><span class="sxs-lookup"><span data-stu-id="286ad-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="286ad-131">Atribut `StringLength` umožňuje nastavit maximální délku vlastnosti řetězce a volitelně její minimální délku.</span><span class="sxs-lookup"><span data-stu-id="286ad-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="286ad-132">Typy `decimal`hodnot (například `float` `DateTime`, `int`, ) jsou ze své `[Required]` podstaty povinné a nepotřebují atribut.</span><span class="sxs-lookup"><span data-stu-id="286ad-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="286ad-133">Díky tomu, že se ověřovací pravidla automaticky vynucují pomocí ASP.NET Core, vaše aplikace bude robustnější.</span><span class="sxs-lookup"><span data-stu-id="286ad-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="286ad-134">Také zajišťuje, že nemůžete zapomenout na ověření něco a nechtěně nechat špatná data do databáze.</span><span class="sxs-lookup"><span data-stu-id="286ad-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="286ad-135">U rozhraní chyby ověření na stránkách Břitva</span><span class="sxs-lookup"><span data-stu-id="286ad-135">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="286ad-136">Spusťte aplikaci a přejděte na Stránky/filmy.</span><span class="sxs-lookup"><span data-stu-id="286ad-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="286ad-137">Vyberte odkaz **Vytvořit nový.**</span><span class="sxs-lookup"><span data-stu-id="286ad-137">Select the **Create New** link.</span></span> <span data-ttu-id="286ad-138">Vyplňte formulář s některými neplatnými hodnotami.</span><span class="sxs-lookup"><span data-stu-id="286ad-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="286ad-139">Když ověření na straně klienta jQuery zjistí chybu, zobrazí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="286ad-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formulář zobrazení filmu s více chybami ověření na straně klienta jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="286ad-141">Všimněte si, jak formulář automaticky vykreslil chybovou zprávu ověření v každém poli obsahující neplatnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="286ad-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="286ad-142">Chyby jsou vynuceny jak na straně klienta (pomocí JavaScriptu a jQuery), tak na straně serveru (pokud má uživatel JavaScript zakázán).</span><span class="sxs-lookup"><span data-stu-id="286ad-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="286ad-143">Významnou výhodou je, že na stránkách Vytvořit nebo Upravit nebyly nutné **žádné** změny kódu.</span><span class="sxs-lookup"><span data-stu-id="286ad-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="286ad-144">Jakmile dataAnnotations byly použity pro model, bylo povoleno ověřovací ui.</span><span class="sxs-lookup"><span data-stu-id="286ad-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="286ad-145">Stránky razor vytvořené v tomto kurzu automaticky zvedl ověřovací pravidla (pomocí `Movie` ověřovací atributy na vlastnosti třídy modelu).</span><span class="sxs-lookup"><span data-stu-id="286ad-145">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="286ad-146">Test ověření pomocí upravit stránku, stejné ověření se použije.</span><span class="sxs-lookup"><span data-stu-id="286ad-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="286ad-147">Data formuláře nejsou zaúčtována na server, dokud neexistují žádné chyby ověření na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="286ad-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="286ad-148">Ověřte, zda data formuláře nejsou zaúčtována jedním nebo více z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="286ad-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="286ad-149">Vložte bod přerušení `OnPostAsync` do metody.</span><span class="sxs-lookup"><span data-stu-id="286ad-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="286ad-150">Odešlete formulář (vyberte **Vytvořit** nebo **Uložit**).</span><span class="sxs-lookup"><span data-stu-id="286ad-150">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="286ad-151">Bod zlomu není nikdy zasažen.</span><span class="sxs-lookup"><span data-stu-id="286ad-151">The break point is never hit.</span></span>
* <span data-ttu-id="286ad-152">Použijte [nástroj šumavka](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="286ad-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="286ad-153">Pomocí nástrojů pro vývojáře prohlížeče můžete sledovat síťový provoz.</span><span class="sxs-lookup"><span data-stu-id="286ad-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="286ad-154">Ověření na straně serveru</span><span class="sxs-lookup"><span data-stu-id="286ad-154">Server-side validation</span></span>

<span data-ttu-id="286ad-155">Pokud je JavaScript v prohlížeči zakázán, odeslání formuláře s chybami se odešle na server.</span><span class="sxs-lookup"><span data-stu-id="286ad-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="286ad-156">Volitelné ověření na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="286ad-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="286ad-157">Zakažte JavaScript v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="286ad-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="286ad-158">JavaScript můžete zakázat pomocí vývojářských nástrojů prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="286ad-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="286ad-159">Pokud nemůžete v prohlížeči zakázat JavaScript, zkuste jiný prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="286ad-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="286ad-160">Nastavte bod přerušení `OnPostAsync` v metodě stránky Vytvořit nebo upravit.</span><span class="sxs-lookup"><span data-stu-id="286ad-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="286ad-161">Odešlete formulář s neplatnými údaji.</span><span class="sxs-lookup"><span data-stu-id="286ad-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="286ad-162">Ověřte, zda je stav modelu neplatný:</span><span class="sxs-lookup"><span data-stu-id="286ad-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="286ad-163">Případně můžete [zakázat ověřování na straně klienta na serveru](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="286ad-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="286ad-164">Následující kód ukazuje část *Create.cshtml* stránky šetrné dříve v kurzu.</span><span class="sxs-lookup"><span data-stu-id="286ad-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="286ad-165">Stránky Vytvořit a upravit jej používají k zobrazení počátečního formuláře a k opětovnému zobrazení formuláře v případě chyby.</span><span class="sxs-lookup"><span data-stu-id="286ad-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="286ad-166">Pomocník [vstupníznačky](xref:mvc/views/working-with-forms) používá [atributy DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) a vytváří atributy HTML potřebné pro ověření jQuery na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="286ad-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="286ad-167">Pomocná [pomoc s ověřovacíznačkou](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazuje chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="286ad-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="286ad-168">Další informace naleznete v [tématu Ověření.](xref:mvc/models/validation)</span><span class="sxs-lookup"><span data-stu-id="286ad-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="286ad-169">Na stránkách Vytvořit a upravit nejsou žádná ověřovací pravidla.</span><span class="sxs-lookup"><span data-stu-id="286ad-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="286ad-170">Ověřovací pravidla a chybové řetězce jsou `Movie` určeny pouze ve třídě.</span><span class="sxs-lookup"><span data-stu-id="286ad-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="286ad-171">Tato ověřovací pravidla se automaticky použijí `Movie` na stránky Razor, které modelu upravují.</span><span class="sxs-lookup"><span data-stu-id="286ad-171">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="286ad-172">Když se logika ověření musí změnit, provádí se pouze v modelu.</span><span class="sxs-lookup"><span data-stu-id="286ad-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="286ad-173">Ověření se používá konzistentně v celé aplikaci (ověřovací logika je definována na jednom místě).</span><span class="sxs-lookup"><span data-stu-id="286ad-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="286ad-174">Ověření na jednom místě pomáhá udržovat kód čistý a usnadňuje údržbu a aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="286ad-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="286ad-175">Použití atributů datového typu</span><span class="sxs-lookup"><span data-stu-id="286ad-175">Using DataType Attributes</span></span>

<span data-ttu-id="286ad-176">Prozkoumejte `Movie` třídu.</span><span class="sxs-lookup"><span data-stu-id="286ad-176">Examine the `Movie` class.</span></span> <span data-ttu-id="286ad-177">Obor `System.ComponentModel.DataAnnotations` názvů poskytuje kromě předdefinované sady atributů ověření také atributy formátování.</span><span class="sxs-lookup"><span data-stu-id="286ad-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="286ad-178">Pro vlastnosti `ReleaseDate` a `Price` je použit atribut `DataType`.</span><span class="sxs-lookup"><span data-stu-id="286ad-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="286ad-179">Atributy `DataType` poskytují pouze rady pro modul zobrazení pro formátování dat (a `<a>` dodává atributy, jako jsou adresy URL a `<a href="mailto:EmailAddress.com">` e-mail).</span><span class="sxs-lookup"><span data-stu-id="286ad-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="286ad-180">Pomocí `RegularExpression` atributu ověřte formát dat.</span><span class="sxs-lookup"><span data-stu-id="286ad-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="286ad-181">Atribut `DataType` se používá k určení datového typu, který je konkrétnější než vnitřní typ databáze.</span><span class="sxs-lookup"><span data-stu-id="286ad-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="286ad-182">`DataType`atributy nejsou atributy ověření.</span><span class="sxs-lookup"><span data-stu-id="286ad-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="286ad-183">V ukázkové aplikaci se zobrazí pouze datum bez času.</span><span class="sxs-lookup"><span data-stu-id="286ad-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="286ad-184">`DataType` Výčet poskytuje mnoho datových typů, jako je například datum, čas, telefonní číslo, měna, e-mailová adresa a další.</span><span class="sxs-lookup"><span data-stu-id="286ad-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="286ad-185">Atribut `DataType` může také povolit aplikaci automaticky poskytovat funkce specifické pro daný typ.</span><span class="sxs-lookup"><span data-stu-id="286ad-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="286ad-186">Odkaz lze `mailto:` například vytvořit `DataType.EmailAddress`pro aplikaci .</span><span class="sxs-lookup"><span data-stu-id="286ad-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="286ad-187">Volič data může být k `DataType.Date` dispozici v prohlížečích, které podporují HTML5.</span><span class="sxs-lookup"><span data-stu-id="286ad-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="286ad-188">Atributy `DataType` vyzařují atributy HTML 5 `data-` (vyslovuje se pomlčka dat), které prohlížeče HTML 5 spotřebovávají.</span><span class="sxs-lookup"><span data-stu-id="286ad-188">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="286ad-189">Atributy `DataType` **neposkytují** žádné ověření.</span><span class="sxs-lookup"><span data-stu-id="286ad-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="286ad-190">`DataType.Date`neurčuje formát zobrazeného data.</span><span class="sxs-lookup"><span data-stu-id="286ad-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="286ad-191">Ve výchozím nastavení je datové pole zobrazeno podle výchozích formátů založených na souborech serveru `CultureInfo`.</span><span class="sxs-lookup"><span data-stu-id="286ad-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="286ad-192">Anotace `[Column(TypeName = "decimal(18, 2)")]` dat je vyžadována, aby `Price` bylo jádro entity správně mapováno na měnu v databázi.</span><span class="sxs-lookup"><span data-stu-id="286ad-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="286ad-193">Další informace naleznete [v tématu Datové typy](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="286ad-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="286ad-194">Atribut `DisplayFormat` se používá k explicitnímu zadání formátu data:</span><span class="sxs-lookup"><span data-stu-id="286ad-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="286ad-195">Toto `ApplyFormatInEditMode` nastavení určuje, že formátování má být použito při zobrazení hodnoty pro úpravy.</span><span class="sxs-lookup"><span data-stu-id="286ad-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="286ad-196">Toto chování možná nebudete chtít u některých polí.</span><span class="sxs-lookup"><span data-stu-id="286ad-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="286ad-197">Například v hodnotách měny pravděpodobně nechcete symbol měny v uměle upravit.</span><span class="sxs-lookup"><span data-stu-id="286ad-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="286ad-198">Atribut `DisplayFormat` lze použít samostatně, ale je obecně vhodné použít `DataType` atribut.</span><span class="sxs-lookup"><span data-stu-id="286ad-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="286ad-199">Atribut `DataType` vyjadřuje sémantiku dat na rozdíl od způsobu jejich vykreslení na obrazovce a poskytuje následující výhody, které nezískáte s DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="286ad-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="286ad-200">Prohlížeč může povolit funkce HTML5 (například pro zobrazení ovládacího prvku kalendáře, symbolměny odpovídající národnímu prostředí, odkazy na e-mailatd.)</span><span class="sxs-lookup"><span data-stu-id="286ad-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="286ad-201">Ve výchozím nastavení prohlížeč vykreslí data ve správném formátu na základě národního prostředí.</span><span class="sxs-lookup"><span data-stu-id="286ad-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="286ad-202">Atribut `DataType` může povolit ASP.NET core framework zvolit správnou šablonu pole pro vykreslení dat.</span><span class="sxs-lookup"><span data-stu-id="286ad-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="286ad-203">If `DisplayFormat` používá sám používá šablonu řetězce.</span><span class="sxs-lookup"><span data-stu-id="286ad-203">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="286ad-204">Poznámka: jQuery ověření nefunguje `Range` s `DateTime`atributem a .</span><span class="sxs-lookup"><span data-stu-id="286ad-204">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="286ad-205">Například následující kód vždy zobrazí chybu ověření na straně klienta, i když je datum v zadaném rozsahu:</span><span class="sxs-lookup"><span data-stu-id="286ad-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="286ad-206">Obecně není vhodné kompilovat tvrdá data ve vašich `Range` modelech, takže použití atributu a `DateTime` je odrazováno.</span><span class="sxs-lookup"><span data-stu-id="286ad-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="286ad-207">Následující kód ukazuje kombinování atributů na jednom řádku:</span><span class="sxs-lookup"><span data-stu-id="286ad-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="286ad-208">[Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) ukazuje pokročilé EF Core operace s Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="286ad-208">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="286ad-209">Použití migrace</span><span class="sxs-lookup"><span data-stu-id="286ad-209">Apply migrations</span></span>

<span data-ttu-id="286ad-210">DataAnnotations aplikované na třídu změnit schéma.</span><span class="sxs-lookup"><span data-stu-id="286ad-210">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="286ad-211">Například dataAnnotations aplikované `Title` na pole:</span><span class="sxs-lookup"><span data-stu-id="286ad-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="286ad-212">Omezuje znaky na 60.</span><span class="sxs-lookup"><span data-stu-id="286ad-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="286ad-213">Neumožňuje hodnotu. `null`</span><span class="sxs-lookup"><span data-stu-id="286ad-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="286ad-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="286ad-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="286ad-215">Tabulka `Movie` má aktuálně následující schéma:</span><span class="sxs-lookup"><span data-stu-id="286ad-215">The `Movie` table currently has the following schema:</span></span>

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

<span data-ttu-id="286ad-216">Předchozí změny schématu nezpůsobí EF vyvolat výjimku.</span><span class="sxs-lookup"><span data-stu-id="286ad-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="286ad-217">Vytvořte však migraci, aby schéma bylo konzistentní s modelem.</span><span class="sxs-lookup"><span data-stu-id="286ad-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="286ad-218">V nabídce **Nástroje** vyberte **položku NuGet Package Manager > Konzola správce balíčků**.</span><span class="sxs-lookup"><span data-stu-id="286ad-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="286ad-219">Do pmc zadejte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="286ad-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="286ad-220">`Update-Database`spustí `Up` metody třídy. `New_DataAnnotations`</span><span class="sxs-lookup"><span data-stu-id="286ad-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="286ad-221">Zkontrolujte `Up` metodu:</span><span class="sxs-lookup"><span data-stu-id="286ad-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="286ad-222">Aktualizovaná `Movie` tabulka obsahuje následující schéma:</span><span class="sxs-lookup"><span data-stu-id="286ad-222">The updated `Movie` table has the following schema:</span></span>

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="286ad-223">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="286ad-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="286ad-224">Migrace nejsou vyžadovány pro SQLite.</span><span class="sxs-lookup"><span data-stu-id="286ad-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="286ad-225">Publikování aplikací do Azure</span><span class="sxs-lookup"><span data-stu-id="286ad-225">Publish to Azure</span></span>

<span data-ttu-id="286ad-226">Informace o nasazení do Azure najdete [v tématu Kurz: Vytvoření aplikace ASP.NET Core v Azure s databází SQL .](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)</span><span class="sxs-lookup"><span data-stu-id="286ad-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="286ad-227">Děkujeme za dokončení tohoto úvodu do Razor Stránek.</span><span class="sxs-lookup"><span data-stu-id="286ad-227">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="286ad-228">[Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) je vynikající sledování tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="286ad-228">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="286ad-229">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="286ad-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="286ad-230">Verze tohoto kurzu pro YouTube</span><span class="sxs-lookup"><span data-stu-id="286ad-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="286ad-231">Předchozí: Přidání nového pole</span><span class="sxs-lookup"><span data-stu-id="286ad-231">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)

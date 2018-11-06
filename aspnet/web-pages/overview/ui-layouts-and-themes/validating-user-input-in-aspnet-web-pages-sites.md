---
uid: web-pages/overview/ui-layouts-and-themes/validating-user-input-in-aspnet-web-pages-sites
title: Ověřování uživatelského vstupu v rozhraní ASP.NET Web Pages servery (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek popisuje, jak ověřit informace získáte od uživatelů &mdash; tedy zajistit, aby uživatelé zadali platné informace ve formátu HTML formulářů v jako...
ms.author: riande
ms.date: 02/20/2014
ms.assetid: 4eb060cc-cf14-41ae-bab1-14a2c15332d0
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/validating-user-input-in-aspnet-web-pages-sites
msc.type: authoredcontent
ms.openlocfilehash: 8f049adce33e452896b5e2a444635ff30d18e480
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021518"
---
<a name="validating-user-input-in-aspnet-web-pages-razor-sites"></a><span data-ttu-id="3eb99-103">Ověřování uživatelského vstupu v lokalitách rozhraní ASP.NET Web Pages (Razor)</span><span class="sxs-lookup"><span data-stu-id="3eb99-103">Validating User Input in ASP.NET Web Pages (Razor) Sites</span></span>
====================
<span data-ttu-id="3eb99-104">podle [Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="3eb99-104">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="3eb99-105">Tento článek popisuje, jak ověřit informace získáte od uživatelů &mdash; to znamená, ujistěte se, aby uživatelé zadali platné informace ve formátu HTML formulářů na webu rozhraní ASP.NET Web Pages (Razor).</span><span class="sxs-lookup"><span data-stu-id="3eb99-105">This article discusses how to validate information you get from users &mdash; that is, to make sure that users enter valid information in HTML forms in an ASP.NET Web Pages (Razor) site.</span></span>
> 
> <span data-ttu-id="3eb99-106">Co se dozvíte:</span><span class="sxs-lookup"><span data-stu-id="3eb99-106">What you'll learn:</span></span>
> 
> - <span data-ttu-id="3eb99-107">Jak zkontrolovat, jestli se uživatelovo zadání uživatele odpovídá ověřovací kritéria, které definujete.</span><span class="sxs-lookup"><span data-stu-id="3eb99-107">How to check that a user's input matches validation criteria that you define.</span></span>
> - <span data-ttu-id="3eb99-108">Jak určit, jestli jste předali všechny testy pro ověření.</span><span class="sxs-lookup"><span data-stu-id="3eb99-108">How to determine whether all validation tests have passed.</span></span>
> - <span data-ttu-id="3eb99-109">Jak zobrazit chyby ověření (a způsob jejich formátování).</span><span class="sxs-lookup"><span data-stu-id="3eb99-109">How to display validation errors (and how to format them).</span></span>
> - <span data-ttu-id="3eb99-110">Jak ověřit data, která nepochází přímo od uživatele.</span><span class="sxs-lookup"><span data-stu-id="3eb99-110">How to validate data that doesn't come directly from users.</span></span>
> 
> <span data-ttu-id="3eb99-111">Toto jsou ASP.NET programování koncepty představenými v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="3eb99-111">These are the ASP.NET programming concepts introduced in the article:</span></span>
> 
> - <span data-ttu-id="3eb99-112">`Validation` Pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="3eb99-112">The `Validation` helper.</span></span>
> - <span data-ttu-id="3eb99-113">`Html.ValidationSummary` a `Html.ValidationMessage` metody.</span><span class="sxs-lookup"><span data-stu-id="3eb99-113">The `Html.ValidationSummary` and `Html.ValidationMessage` methods.</span></span>
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="3eb99-114">V tomto kurzu použili verze softwaru</span><span class="sxs-lookup"><span data-stu-id="3eb99-114">Software versions used in the tutorial</span></span>
> 
> 
> - <span data-ttu-id="3eb99-115">Webové stránky ASP.NET (Razor) 3</span><span class="sxs-lookup"><span data-stu-id="3eb99-115">ASP.NET Web Pages (Razor) 3</span></span>
>   
> 
> <span data-ttu-id="3eb99-116">V tomto kurzu se také pracuje s ASP.NET Web Pages 2.</span><span class="sxs-lookup"><span data-stu-id="3eb99-116">This tutorial also works with ASP.NET Web Pages 2.</span></span>


<span data-ttu-id="3eb99-117">Tento článek obsahuje následující části:</span><span class="sxs-lookup"><span data-stu-id="3eb99-117">This article contains the following sections:</span></span>

- [<span data-ttu-id="3eb99-118">Přehled ověřování uživatelského vstupu</span><span class="sxs-lookup"><span data-stu-id="3eb99-118">Overview of User Input Validation</span></span>](#Overview_of_User_Input_Validation)
- [<span data-ttu-id="3eb99-119">Ověřování uživatelského vstupu</span><span class="sxs-lookup"><span data-stu-id="3eb99-119">Validating User Input</span></span>](#Validating_User_Input)
- [<span data-ttu-id="3eb99-120">Přidání ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="3eb99-120">Adding Client-Side Validation</span></span>](#Adding_Client-Side_Validation)
- [<span data-ttu-id="3eb99-121">Formátování chyby ověření</span><span class="sxs-lookup"><span data-stu-id="3eb99-121">Formatting Validation Errors</span></span>](#Formatting_Validation_Errors)
- [<span data-ttu-id="3eb99-122">Ověřování dat, který nepochází přímo od uživatele</span><span class="sxs-lookup"><span data-stu-id="3eb99-122">Validating Data That Doesn't Come Directly from Users</span></span>](#Validating_Data_That_Doesnt_Come_Directly_from_Users)

<a id="Overview_of_User_Input_Validation"></a>
## <a name="overview-of-user-input-validation"></a><span data-ttu-id="3eb99-123">Přehled ověřování uživatelského vstupu</span><span class="sxs-lookup"><span data-stu-id="3eb99-123">Overview of User Input Validation</span></span>

<span data-ttu-id="3eb99-124">Pokud požadujete, aby uživatelé zadat informace na stránce – například do formátu, je důležité, abyste měli jistotu, že jsou hodnoty, které jsou zadány platné.</span><span class="sxs-lookup"><span data-stu-id="3eb99-124">If you ask users to enter information in a page — for example, into a form — it's important to make sure that the values that they enter are valid.</span></span> <span data-ttu-id="3eb99-125">Například nechcete zpracovat formuláře, který chybí důležitých informací.</span><span class="sxs-lookup"><span data-stu-id="3eb99-125">For example, you don't want to process a form that's missing critical information.</span></span>

<span data-ttu-id="3eb99-126">Při zadávání hodnoty do formuláře HTML, jsou hodnoty, které se vstupem řetězce.</span><span class="sxs-lookup"><span data-stu-id="3eb99-126">When users enter values into an HTML form, the values that they enter are strings.</span></span> <span data-ttu-id="3eb99-127">V mnoha případech jsou hodnoty, které budete potřebovat některé jiné datové typy, jako jsou celá čísla nebo kalendářní data.</span><span class="sxs-lookup"><span data-stu-id="3eb99-127">In many cases, the values you need are some other data types, like integers or dates.</span></span> <span data-ttu-id="3eb99-128">Proto máte také aby se zajistilo, že hodnoty, které uživatelé zadají lze správně převést na odpovídající datové typy.</span><span class="sxs-lookup"><span data-stu-id="3eb99-128">Therefore, you also have to make sure that the values that users enter can be correctly converted to the appropriate data types.</span></span>

<span data-ttu-id="3eb99-129">Můžete mít také určitá omezení na hodnotách.</span><span class="sxs-lookup"><span data-stu-id="3eb99-129">You might also have certain restrictions on the values.</span></span> <span data-ttu-id="3eb99-130">I v případě, že uživatelé správně zadat celé číslo, například můžete potřebovat abyste měli jistotu, že hodnota spadá do určitého rozsahu.</span><span class="sxs-lookup"><span data-stu-id="3eb99-130">Even if users correctly enter an integer, for example, you might need to make sure that the value falls within a certain range.</span></span>

![Chyby ověření, které používají třídy CSS](validating-user-input-in-aspnet-web-pages-sites/_static/image1.png)

> [!NOTE] 
> 
> <span data-ttu-id="3eb99-132">**Důležité** ověřování uživatelského vstupu je také důležité pro zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="3eb99-132">**Important** Validating user input is also important for security.</span></span> <span data-ttu-id="3eb99-133">Omezíte hodnoty, které mohou uživatelé zadat ve formulářích, snížíte pravděpodobnost, že někdo můžete zadat hodnotu, která může ohrozit zabezpečení vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="3eb99-133">When you restrict the values that users can enter in forms, you reduce the chance that someone can enter a value that can compromise the security of your site.</span></span>


<a id="Validating_User_Input"></a>
## <a name="validating-user-input"></a><span data-ttu-id="3eb99-134">Ověřování uživatelského vstupu</span><span class="sxs-lookup"><span data-stu-id="3eb99-134">Validating User Input</span></span>

<span data-ttu-id="3eb99-135">V ASP.NET Web Pages 2, můžete použít `Validator` pomocná rutina pro vstup uživatele.</span><span class="sxs-lookup"><span data-stu-id="3eb99-135">In ASP.NET Web Pages 2, you can use the `Validator` helper to test user input.</span></span> <span data-ttu-id="3eb99-136">Základní přístup je provést následující kroky:</span><span class="sxs-lookup"><span data-stu-id="3eb99-136">The basic approach is to do the following:</span></span>

1. <span data-ttu-id="3eb99-137">Určete, který vstupní prvky (pole), kterou chcete ověřit.</span><span class="sxs-lookup"><span data-stu-id="3eb99-137">Determine which input elements (fields) you want to validate.</span></span>

    <span data-ttu-id="3eb99-138">Obvykle ověřte hodnoty v `<input>` prvky ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="3eb99-138">You typically validate values in `<input>` elements in a form.</span></span> <span data-ttu-id="3eb99-139">Je však vhodné ověřte všechny vstupy, dokonce i vstup, který přichází z omezené prvky jako `<select>` seznamu.</span><span class="sxs-lookup"><span data-stu-id="3eb99-139">However, it's a good practice to validate all input, even input that comes from a constrained element like a `<select>` list.</span></span> <span data-ttu-id="3eb99-140">To pomáhá zajistit, že uživatelé není obejití ovládacích prvků na stránce a odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="3eb99-140">This helps to make sure that users don't bypass the controls on a page and submit a form.</span></span>
2. <span data-ttu-id="3eb99-141">V kódu stránky přidat jednotlivé ověřovací kontroly pro každý vstupní element pomocí metod `Validation` pomocné rutiny.</span><span class="sxs-lookup"><span data-stu-id="3eb99-141">In the page code, add individual validation checks for each input element by using methods of the `Validation` helper.</span></span>

    <span data-ttu-id="3eb99-142">Chcete-li zkontrolovat u povinných polí, použijte `Validation.RequireField(field, [error message])` (pro jednotlivá pole) nebo `Validation.RequireFields(field1, field2, ...))` (pro seznam polí).</span><span class="sxs-lookup"><span data-stu-id="3eb99-142">To check for required fields, use `Validation.RequireField(field, [error message])` (for an individual field) or `Validation.RequireFields(field1, field2, ...))` (for a list of fields).</span></span> <span data-ttu-id="3eb99-143">Pro jiné typy ověřování pomocí `Validation.Add(field, ValidationType)`.</span><span class="sxs-lookup"><span data-stu-id="3eb99-143">For other types of validation, use `Validation.Add(field, ValidationType)`.</span></span> <span data-ttu-id="3eb99-144">Pro `ValidationType`, můžete použít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="3eb99-144">For `ValidationType`, you can use these options:</span></span>

    `Validator.DateTime ([error message])`  
   `Validator.Decimal([error message])`  
   `Validator.EqualsTo(otherField [, error message])`  
   `Validator.Float([error message])`  
   `Validator.Integer([error message])`  
   `Validator.Range(min, max [, error message])`  
   `Validator.RegEx(pattern [, error message])`  
   `Validator.Required([error message])`  
   `Validator.StringLength(length)`  
   `Validator.Url([error message])`
3. <span data-ttu-id="3eb99-145">Při odeslání stránky, zkontrolujte, zda má ověření proběhlo úspěšně, tak, že zkontrolujete `Validation.IsValid`:</span><span class="sxs-lookup"><span data-stu-id="3eb99-145">When the page is submitted, check whether validation has passed by checking `Validation.IsValid`:</span></span>

    [!code-csharp[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample1.cs)]

    <span data-ttu-id="3eb99-146">Pokud nejsou žádné chyby ověření, můžete přeskočit, zpracování normální stránky.</span><span class="sxs-lookup"><span data-stu-id="3eb99-146">If there are any validation errors, you skip normal page processing.</span></span> <span data-ttu-id="3eb99-147">Například pokud je účel stránky aktualizace databáze, neuděláte, dokud byly vyřešeny všechny chyby ověření.</span><span class="sxs-lookup"><span data-stu-id="3eb99-147">For example, if the purpose of the page is to update a database, you don't do that until all validation errors have been fixed.</span></span>
4. <span data-ttu-id="3eb99-148">Pokud jsou chyby ověření, zobrazit chybové zprávy v kódu stránky pomocí `Html.ValidationSummary` nebo `Html.ValidationMessage`, nebo obojí.</span><span class="sxs-lookup"><span data-stu-id="3eb99-148">If there are validation errors, display error messages in the page's markup by using `Html.ValidationSummary` or `Html.ValidationMessage`, or both.</span></span>

<span data-ttu-id="3eb99-149">Následující příklad ukazuje stránka, která znázorňuje tyto kroky.</span><span class="sxs-lookup"><span data-stu-id="3eb99-149">The following example shows a page that illustrates these steps.</span></span>

[!code-cshtml[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample2.cshtml)]

<span data-ttu-id="3eb99-150">Pokud chcete zobrazit, jak funguje ověřování, spusťte na této stránce a záměrně dělat chyby.</span><span class="sxs-lookup"><span data-stu-id="3eb99-150">To see how validation works, run this page and deliberately make mistakes.</span></span> <span data-ttu-id="3eb99-151">Například tady je co bude stránka vypadat, pokud zapomenete zadat název kurzu, pokud zadáte, a pokud zadáte neplatné datum:</span><span class="sxs-lookup"><span data-stu-id="3eb99-151">For example, here's what the page looks like if you forget to enter a course name, if you enter an, and if you enter an invalid date:</span></span>

![Chyby ověření na vykreslené stránce](validating-user-input-in-aspnet-web-pages-sites/_static/image2.png)

<a id="Adding_Client-Side_Validation"></a>
## <a name="adding-client-side-validation"></a><span data-ttu-id="3eb99-153">Přidání ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="3eb99-153">Adding Client-Side Validation</span></span>

<span data-ttu-id="3eb99-154">Ve výchozím nastavení, uživatelský vstup je ověřen po odeslání stránky – to znamená, ověření se provede v kódu serveru.</span><span class="sxs-lookup"><span data-stu-id="3eb99-154">By default, user input is validated after users submit the page — that is, the validation is performed in server code.</span></span> <span data-ttu-id="3eb99-155">Nevýhody tohoto přístupu je, že uživatelé neví, že udělali chybu až po jejich odeslání stránky.</span><span class="sxs-lookup"><span data-stu-id="3eb99-155">A disadvantage of this approach is that users don't know that they've made an error until after they submit the page.</span></span> <span data-ttu-id="3eb99-156">Pokud formuláře je dlouhý nebo složitý, může být nepraktické uživatel hlášení chyb, až po odeslání stránky.</span><span class="sxs-lookup"><span data-stu-id="3eb99-156">If a form is long or complex, reporting errors only after the page is submitted can be inconvenient to the user.</span></span>

<span data-ttu-id="3eb99-157">Můžete přidat podporu k provedení ověření v klientského skriptu.</span><span class="sxs-lookup"><span data-stu-id="3eb99-157">You can add support to perform validation in client script.</span></span> <span data-ttu-id="3eb99-158">V takovém případě ověření se provede, jak uživatelé pracují v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="3eb99-158">In that case, the validation is performed as users work in the browser.</span></span> <span data-ttu-id="3eb99-159">Předpokládejme například, že určíte, že hodnota by měla být celé.</span><span class="sxs-lookup"><span data-stu-id="3eb99-159">For example, suppose you specify that a value should be an integer.</span></span> <span data-ttu-id="3eb99-160">Pokud uživatel zadá hodnotu jiných než celých čísel, je Chyba hlášená jako uživatel společnost opustí polem pro zadání.</span><span class="sxs-lookup"><span data-stu-id="3eb99-160">If a user enters a non-integer value, the error is reported as soon as the user leaves the entry field.</span></span> <span data-ttu-id="3eb99-161">Uživatelé získají okamžitou zpětnou vazbu, která je vhodné pro ně.</span><span class="sxs-lookup"><span data-stu-id="3eb99-161">Users get immediate feedback, which is convenient for them.</span></span> <span data-ttu-id="3eb99-162">Ověřování na základě klienta může také omezit počet případů, kdy se uživatel má k odeslání formuláře, chcete-li opravit několik chyb.</span><span class="sxs-lookup"><span data-stu-id="3eb99-162">Client-based validation can also reduce the number of times that the user has to submit the form to correct multiple errors.</span></span>

> [!NOTE]
> <span data-ttu-id="3eb99-163">I když používáte ověřování na straně klienta, provede se ověření vždycky také v serverovém kódu.</span><span class="sxs-lookup"><span data-stu-id="3eb99-163">Even if you use client-side validation, validation is always also performed in server code.</span></span> <span data-ttu-id="3eb99-164">Provedení ověření v serverovém kódu je v rámci bezpečnostních opatření v případě, že uživatelé obejít ověřování na základě klienta.</span><span class="sxs-lookup"><span data-stu-id="3eb99-164">Performing validation in server code is a security measure, in case users bypass client-based validation.</span></span>


1. <span data-ttu-id="3eb99-165">Zaregistrujte následující knihovny jazyka JavaScript na stránce:</span><span class="sxs-lookup"><span data-stu-id="3eb99-165">Register the following JavaScript libraries in the page:</span></span>  

    [!code-html[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample3.html)]

   <span data-ttu-id="3eb99-166">Jsou dva knihoven načíst ze sítě pro doručování obsahu (CDN), takže není nutné nemusí mít v počítači nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="3eb99-166">Two of the libraries are loadable from a content delivery network (CDN), so you don't necessarily have to have them on your computer or server.</span></span> <span data-ttu-id="3eb99-167">Však musí mít místní kopii *jquery.validate.unobtrusive.js*.</span><span class="sxs-lookup"><span data-stu-id="3eb99-167">However, you must have a local copy of *jquery.validate.unobtrusive.js*.</span></span> <span data-ttu-id="3eb99-168">Pokud dosud nepracujete s šablonou služby WebMatrix (jako je **Starter Site** ), který obsahuje knihovnu, vytvoření webových stránek webu, který je založen na **Starter Site**.</span><span class="sxs-lookup"><span data-stu-id="3eb99-168">If you are not already working with a WebMatrix template (like **Starter Site** ) that includes the library, create a Web Pages site that's based on **Starter Site**.</span></span> <span data-ttu-id="3eb99-169">Zkopírujte *js* souboru k aktuální lokalitě.</span><span class="sxs-lookup"><span data-stu-id="3eb99-169">Then copy the *.js* file to your current site.</span></span>
2. <span data-ttu-id="3eb99-170">Ve značkách pro každý prvek, který jste ověření, přidejte volání do `Validation.For(field)`.</span><span class="sxs-lookup"><span data-stu-id="3eb99-170">In markup, for each element that you're validating, add a call to `Validation.For(field)`.</span></span> <span data-ttu-id="3eb99-171">Tato metoda generuje atributy, které používají ověřování na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="3eb99-171">This method emits attributes that are used by client-side validation.</span></span> <span data-ttu-id="3eb99-172">(Místo generování skutečný kód jazyka JavaScript, generuje metodu atributů, jako je `data-val-...`.</span><span class="sxs-lookup"><span data-stu-id="3eb99-172">(Rather than emitting actual JavaScript code, the method emits attributes like `data-val-...`.</span></span> <span data-ttu-id="3eb99-173">Tyto atributy podporují ověření nerušivého klienta, který používá jQuery proveďte práci).</span><span class="sxs-lookup"><span data-stu-id="3eb99-173">These attributes support unobtrusive client validation that uses jQuery to do the work.)</span></span>

<span data-ttu-id="3eb99-174">Na následující stránce ukazuje, jak přidat funkce ověření klienta v příkladu je uvedeno výše.</span><span class="sxs-lookup"><span data-stu-id="3eb99-174">The following page shows how to add client validation features to the example shown earlier.</span></span>

[!code-cshtml[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample4.cshtml?highlight=35-39,51,61,71)]

<span data-ttu-id="3eb99-175">Ne všechny ověřovací kontroly na klientech.</span><span class="sxs-lookup"><span data-stu-id="3eb99-175">Not all validation checks run on the client.</span></span> <span data-ttu-id="3eb99-176">Konkrétně se ověření datového typu (celé číslo, datum atd.) nejsou spuštěné na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="3eb99-176">In particular, data-type validation (integer, date, and so on) don't run on the client.</span></span> <span data-ttu-id="3eb99-177">Následující kontroly pracovat na klienta a serveru:</span><span class="sxs-lookup"><span data-stu-id="3eb99-177">The following checks work on both the client and server:</span></span>

- `Required`
- `Range(minValue, maxValue)`
- `StringLength(maxLength[, minLength])`
- `Regex(pattern)`
- `EqualsTo(otherField)`

<span data-ttu-id="3eb99-178">V tomto příkladu nebudou fungovat test platné datum v kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="3eb99-178">In this example, the test for a valid date won't work in client code.</span></span> <span data-ttu-id="3eb99-179">Však test se provede v kódu serveru.</span><span class="sxs-lookup"><span data-stu-id="3eb99-179">However, the test will be performed in server code.</span></span>

<a id="Formatting_Validation_Errors"></a>
## <a name="formatting-validation-errors"></a><span data-ttu-id="3eb99-180">Formátování chyby ověření</span><span class="sxs-lookup"><span data-stu-id="3eb99-180">Formatting Validation Errors</span></span>

<span data-ttu-id="3eb99-181">Můžete řídit, jak se zobrazí chyby ověření definováním třídy CSS, které mají následující vyhrazené názvy:</span><span class="sxs-lookup"><span data-stu-id="3eb99-181">You can control how validation errors are displayed by defining CSS classes that have the following reserved names:</span></span>

- <span data-ttu-id="3eb99-182">`field-validation-error`.</span><span class="sxs-lookup"><span data-stu-id="3eb99-182">`field-validation-error`.</span></span> <span data-ttu-id="3eb99-183">Definuje výstup `Html.ValidationMessage` metoda při zobrazuje chybu.</span><span class="sxs-lookup"><span data-stu-id="3eb99-183">Defines the output of the `Html.ValidationMessage` method when it's displaying an error.</span></span>
- <span data-ttu-id="3eb99-184">`field-validation-valid`.</span><span class="sxs-lookup"><span data-stu-id="3eb99-184">`field-validation-valid`.</span></span> <span data-ttu-id="3eb99-185">Definuje výstup `Html.ValidationMessage` metodu, pokud se nezobrazí žádná chyba.</span><span class="sxs-lookup"><span data-stu-id="3eb99-185">Defines the output of the `Html.ValidationMessage` method when there is no error.</span></span>
- <span data-ttu-id="3eb99-186">`input-validation-error`.</span><span class="sxs-lookup"><span data-stu-id="3eb99-186">`input-validation-error`.</span></span> <span data-ttu-id="3eb99-187">Definuje způsob `<input>` prvky jsou generovány, když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="3eb99-187">Defines how `<input>` elements are rendered when there's an error.</span></span> <span data-ttu-id="3eb99-188">(Například může tato třída slouží k nastavení barvy pozadí &lt;vstupní&gt; element na jinou barvu, pokud její hodnota je neplatná.) Tato třída šablon stylů CSS se používá pouze během ověření klienta (v ASP.NET Web Pages 2).</span><span class="sxs-lookup"><span data-stu-id="3eb99-188">(For example, you can use this class to set the background color of an &lt;input&gt; element to a different color if its value is invalid.) This CSS class is used only during client validation (in ASP.NET Web Pages 2).</span></span>
- <span data-ttu-id="3eb99-189">`input-validation-valid`.</span><span class="sxs-lookup"><span data-stu-id="3eb99-189">`input-validation-valid`.</span></span> <span data-ttu-id="3eb99-190">Definuje vzhled elementů `<input>` elementy, pokud se nezobrazí žádná chyba.</span><span class="sxs-lookup"><span data-stu-id="3eb99-190">Defines the appearance of `<input>` elements when there is no error.</span></span>
- <span data-ttu-id="3eb99-191">`validation-summary-errors`.</span><span class="sxs-lookup"><span data-stu-id="3eb99-191">`validation-summary-errors`.</span></span> <span data-ttu-id="3eb99-192">Definuje výstup `Html.ValidationSummary` metoda zobrazuje seznam chyb.</span><span class="sxs-lookup"><span data-stu-id="3eb99-192">Defines the output of the `Html.ValidationSummary` method it's displaying a list of errors.</span></span>
- <span data-ttu-id="3eb99-193">`validation-summary-valid`.</span><span class="sxs-lookup"><span data-stu-id="3eb99-193">`validation-summary-valid`.</span></span> <span data-ttu-id="3eb99-194">Definuje výstup `Html.ValidationSummary` metodu, pokud se nezobrazí žádná chyba.</span><span class="sxs-lookup"><span data-stu-id="3eb99-194">Defines the output of the `Html.ValidationSummary` method when there is no error.</span></span>

<span data-ttu-id="3eb99-195">Následující `<style>` bloku zobrazuje pravidla pro chybové podmínky.</span><span class="sxs-lookup"><span data-stu-id="3eb99-195">The following `<style>` block shows rules for error conditions.</span></span>

[!code-css[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample5.css)]

<span data-ttu-id="3eb99-196">Zadáte-li tento blok stylu na příkladu stránky z dříve v tomto článku, zobrazení chyb bude vypadat jako na následujícím obrázku:</span><span class="sxs-lookup"><span data-stu-id="3eb99-196">If you include this style block in the example pages from earlier in the article, the error display will look like the following illustration:</span></span>

![Chyby ověření, které používají třídy CSS](validating-user-input-in-aspnet-web-pages-sites/_static/image3.png)

> [!NOTE]
> <span data-ttu-id="3eb99-198">Pokud nepoužíváte ověřování na straně klienta v ASP.NET Web Pages 2, šablon stylů CSS třídy pro `<input>` elementy (`input-validation-error` a `input-validation-valid` nemají žádný vliv.</span><span class="sxs-lookup"><span data-stu-id="3eb99-198">If you're not using client validation in ASP.NET Web Pages 2, the CSS classes for the `<input>` elements (`input-validation-error` and `input-validation-valid` don't have any effect.</span></span>


### <a name="static-and-dynamic-error-display"></a><span data-ttu-id="3eb99-199">Chyby statické a dynamické zobrazení</span><span class="sxs-lookup"><span data-stu-id="3eb99-199">Static and Dynamic Error Display</span></span>

<span data-ttu-id="3eb99-200">Pravidla šablon stylů CSS pocházet uvedený jako dvojice, například `validation-summary-errors` a `validation-summary-valid`.</span><span class="sxs-lookup"><span data-stu-id="3eb99-200">The CSS rules come in pairs, such as `validation-summary-errors` and `validation-summary-valid`.</span></span> <span data-ttu-id="3eb99-201">Tyto páry umožňují definovat pravidla pro obě podmínky: chybovou podmínku a podmínku "normální" (bez chyb).</span><span class="sxs-lookup"><span data-stu-id="3eb99-201">These pairs let you define rules for both conditions: an error condition and a "normal" (non-error) condition.</span></span> <span data-ttu-id="3eb99-202">Je důležité pochopit, že vždy vykreslení značky pro zobrazení chyb, i když nejsou žádné chyby.</span><span class="sxs-lookup"><span data-stu-id="3eb99-202">It's important to understand that the markup for the error display is always rendered, even if there are no errors.</span></span> <span data-ttu-id="3eb99-203">Například, když má stránka `Html.ValidationSummary` metody v kódu, zdroj stránky bude obsahovat následující kód, i když je zobrazení stránky vyžadováno poprvé:</span><span class="sxs-lookup"><span data-stu-id="3eb99-203">For example, if a page has an `Html.ValidationSummary` method in the markup, the page source will contain the following markup even when the page is requested for the first time:</span></span>

`<div class="validation-summary-valid" data-valmsg-summary="true"><ul></ul></div>`

<span data-ttu-id="3eb99-204">Jinými slovy `Html.ValidationSummary` vždy vykreslí metoda `<div>` elementu a seznam, i když v seznamu chyb je prázdný.</span><span class="sxs-lookup"><span data-stu-id="3eb99-204">In other words, the `Html.ValidationSummary` method always renders a `<div>` element and a list, even if the error list is empty.</span></span> <span data-ttu-id="3eb99-205">Podobně `Html.ValidationMessage` vždy vykreslí metoda `<span>` prvek jako zástupný symbol pro jednotlivá pole chyb, i když se nezobrazí žádná chyba.</span><span class="sxs-lookup"><span data-stu-id="3eb99-205">Similarly, the `Html.ValidationMessage` method always renders a `<span>` element as a placeholder for an individual field error, even if there is no error.</span></span>

<span data-ttu-id="3eb99-206">V některých situacích s chybovou zprávou můžou způsobit stránky a přeformátování a může způsobit elementy na stránce uspořádat jinak.</span><span class="sxs-lookup"><span data-stu-id="3eb99-206">In some situations, displaying an error message can cause the page to reflow and can cause elements on the page to move around.</span></span> <span data-ttu-id="3eb99-207">Pravidla šablon stylů CSS, které končí `-valid` umožňují definovat rozložení, který pomůže tomuto problému zabránit.</span><span class="sxs-lookup"><span data-stu-id="3eb99-207">The CSS rules that end in `-valid` let you define a layout that can help prevent this problem.</span></span> <span data-ttu-id="3eb99-208">Například můžete definovat `field-validation-error` a `field-validation-valid` na obě stejné opravili velikost.</span><span class="sxs-lookup"><span data-stu-id="3eb99-208">For example, you can define `field-validation-error` and `field-validation-valid` to both have the same fixed size.</span></span> <span data-ttu-id="3eb99-209">Tímto způsobem zobrazované oblasti pro pole je statická a tok stránky se nezmění, pokud se zobrazí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="3eb99-209">That way, the display area for the field is static and won't change the page flow if an error message is displayed.</span></span>

<a id="Validating_Data_That_Doesnt_Come_Directly_from_Users"></a>
## <a name="validating-data-that-doesnt-come-directly-from-users"></a><span data-ttu-id="3eb99-210">Ověřování dat, který nepochází přímo od uživatele</span><span class="sxs-lookup"><span data-stu-id="3eb99-210">Validating Data That Doesn't Come Directly from Users</span></span>

<span data-ttu-id="3eb99-211">Někdy je nutné ověřit informace, které nepřejde do stavu přímo z formuláře HTML.</span><span class="sxs-lookup"><span data-stu-id="3eb99-211">Sometimes you have to validate information that doesn't come directly from an HTML form.</span></span> <span data-ttu-id="3eb99-212">Typickým příkladem je stránka, kde je hodnota předaná v řetězci dotazu, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3eb99-212">A typical example is a page where a value is passed in a query string, as in the following example:</span></span>

`http://server/myapp/EditClassInformation?classid=1022`

<span data-ttu-id="3eb99-213">V tomto případě chcete Ujistěte se, že hodnota, která je předána na stránku (tady, 1022 pro hodnotu vlastnosti `classid`) je neplatný.</span><span class="sxs-lookup"><span data-stu-id="3eb99-213">In this case, you want to make sure that the value that's passed to the page (here, 1022 for the value of `classid`) is valid.</span></span> <span data-ttu-id="3eb99-214">Nelze použít přímo `Validation` pomocná provádět ověřování.</span><span class="sxs-lookup"><span data-stu-id="3eb99-214">You can't directly use the `Validation` helper to perform this validation.</span></span> <span data-ttu-id="3eb99-215">Můžete však použít další funkce ověřování systému, jako je schopnost zobrazit chybové zprávy ověření.</span><span class="sxs-lookup"><span data-stu-id="3eb99-215">However, you can use other features of the validation system, like the ability to display validation error messages.</span></span>

> [!NOTE] 
> 
> <span data-ttu-id="3eb99-216">**Důležité** vždy ověřte hodnoty, které jste získali z *jakékoli* zdroje, včetně hodnoty pole formuláře, hodnoty řetězce dotazu a hodnoty souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="3eb99-216">**Important** Always validate values that you get from *any* source, including form-field values, query-string values, and cookie values.</span></span> <span data-ttu-id="3eb99-217">Je snadné lidem, kteří tato místa změňte (třeba ke škodlivým účelům).</span><span class="sxs-lookup"><span data-stu-id="3eb99-217">It's easy for people to change these values (perhaps for malicious purposes).</span></span> <span data-ttu-id="3eb99-218">Proto je nutné zkontrolovat tyto hodnoty z důvodu ochrany vašich aplikací.</span><span class="sxs-lookup"><span data-stu-id="3eb99-218">So you must check these values in order to protect your application.</span></span>


<span data-ttu-id="3eb99-219">Následující příklad ukazuje, jak může ověřit hodnotu, která je předána v řetězci dotazu.</span><span class="sxs-lookup"><span data-stu-id="3eb99-219">The following example shows how you might validate a value that's passed in a query string.</span></span> <span data-ttu-id="3eb99-220">Kód se ověřuje, že hodnota není prázdná a že se jedná o celé číslo.</span><span class="sxs-lookup"><span data-stu-id="3eb99-220">The code tests that the value is not empty and that it's an integer.</span></span>

[!code-csharp[Main](validating-user-input-in-aspnet-web-pages-sites/samples/sample6.cs)]

<span data-ttu-id="3eb99-221">Všimněte si, že test se provádí při požadavku není odeslání formuláře (`if(!IsPost)`).</span><span class="sxs-lookup"><span data-stu-id="3eb99-221">Notice that the test is performed when the request is not a form submission (`if(!IsPost)`).</span></span> <span data-ttu-id="3eb99-222">Tento test by úspěšně prošel zpracováním při prvním vyžádání stránky, ale ne žádosti při odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="3eb99-222">This test would pass the first time that the page is requested, but not when the request is a form submission.</span></span>

<span data-ttu-id="3eb99-223">Chcete-li zobrazit tato chyba, můžete přidat chyby do seznamu chyb ověřování voláním `Validation.AddFormError("message")`.</span><span class="sxs-lookup"><span data-stu-id="3eb99-223">To display this error, you can add the error to the list of validation errors by calling `Validation.AddFormError("message")`.</span></span> <span data-ttu-id="3eb99-224">Pokud stránka obsahuje volání `Html.ValidationSummary` metoda, chyba je zobrazené v protokolu, stejně jako chyba ověření vstupu uživatele.</span><span class="sxs-lookup"><span data-stu-id="3eb99-224">If the page contains a call to the `Html.ValidationSummary` method, the error is displayed there, just like a user-input validation error.</span></span>

<a id="AdditionalResources"></a>
## <a name="additional-resources"></a><span data-ttu-id="3eb99-225">Další prostředky</span><span class="sxs-lookup"><span data-stu-id="3eb99-225">Additional Resources</span></span>

[<span data-ttu-id="3eb99-226">Práce s formuláři HTML ve webech stránky ASP.NET</span><span class="sxs-lookup"><span data-stu-id="3eb99-226">Working with HTML Forms in ASP.NET Web Pages Sites</span></span>](https://go.microsoft.com/fwlink/?LinkID=202892)

---
title: Ověření modelu v ASP.NET Core MVC
author: tdykstra
description: Další informace o ověření modelu v ASP.NET Core MVC a stránky Razor.
ms.author: riande
ms.custom: mvc
ms.date: 04/01/2019
uid: mvc/models/validation
ms.openlocfilehash: b766d47f296745ba4be6ea8cb6335db9c3e2d975
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809312"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="ff6ce-103">Ověření modelu v ASP.NET Core MVC a stránky Razor</span><span class="sxs-lookup"><span data-stu-id="ff6ce-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

<span data-ttu-id="ff6ce-104">Tento článek vysvětluje, jak ověřit vstup uživatele v aplikaci ASP.NET Core MVC nebo stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-104">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="ff6ce-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="ff6ce-106">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="ff6ce-106">Model state</span></span>

<span data-ttu-id="ff6ce-107">Stav modelu reprezentuje chyby, které pocházejí z dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-107">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="ff6ce-108">Chyby, které pocházejí z [vazby modelu](model-binding.md) jsou obecně chyby převodu dat (například "x" je zadáno v poli, která očekává celé číslo).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-108">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="ff6ce-109">Ověření modelu dojde poté, co vazby modelu a sestavy chyb, pokud data neodpovídají obchodní pravidla (například 0 je zadáno v poli, která očekává hodnocení 1 až 5).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-109">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ff6ce-110">Vazby modelu a ověření dojít před spuštěním akce kontroleru nebo metodu obslužné rutiny pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-110">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="ff6ce-111">Je zodpovědností aplikace ke kontrole `ModelState.IsValid` a reagují odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-111">It's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="ff6ce-112">Webové aplikace obvykle opětovné zobrazení na stránce s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-112">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ff6ce-113">Vazby modelu a ověření dojít před spuštěním akce kontroleru nebo metodu obslužné rutiny pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-113">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="ff6ce-114">Pro web apps, zodpovídá aplikace ke kontrole `ModelState.IsValid` a reagují odpovídajícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="ff6ce-115">Webové aplikace obvykle opětovné zobrazení na stránce s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="ff6ce-116">Kontrolerů webového rozhraní API není potřeba zkontrolovat `ModelState.IsValid` případě, že mají `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="ff6ce-117">Automatické HTTP 400 odpověď obsahující podrobnosti o problému v takovém případě je vrácena, když neplatí stavu modelu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-117">In that case, an automatic HTTP 400 response containing issue details is returned when model state is invalid.</span></span> <span data-ttu-id="ff6ce-118">Další informace najdete v tématu [odpovědi HTTP 400 automatické](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

::: moniker-end

## <a name="rerun-validation"></a><span data-ttu-id="ff6ce-119">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="ff6ce-119">Rerun validation</span></span>

<span data-ttu-id="ff6ce-120">Ověření je automatická, ale můžete zopakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="ff6ce-121">Například může vypočítat hodnotu pro vlastnost a chcete znovu spusťte ověření po nastavení vlastnosti vypočtená hodnota.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="ff6ce-122">Chcete-li znovu spustit ověřování, zavolejte `TryValidateModel` způsob, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/sample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="ff6ce-123">Ověřování atributů</span><span class="sxs-lookup"><span data-stu-id="ff6ce-123">Validation attributes</span></span>

<span data-ttu-id="ff6ce-124">Ověřování atributů umožňují zadat ověřovacích pravidel pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="ff6ce-125">Následující příklad z [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample) ukazuje třídu modelu, která je označena s atributy ověření.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-125">The following example from [the sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="ff6ce-126">`[ClassicMovie]` Atribut vlastního ověřovacího atributu a ostatní jsou integrované.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="ff6ce-127">(Není vidět je `[ClassicMovie2]`, který ukazuje alternativní způsob, jak implementovat vlastní atribut.)</span><span class="sxs-lookup"><span data-stu-id="ff6ce-127">(Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.)</span></span>

[!code-csharp[](validation/sample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="ff6ce-128">Vestavěné atributy</span><span class="sxs-lookup"><span data-stu-id="ff6ce-128">Built-in attributes</span></span>

<span data-ttu-id="ff6ce-129">Tady jsou některé atributy ověření integrované:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-129">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="ff6ce-130">`[CreditCard]`: Ověří, zda má vlastnost formátu platební karty.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-130">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="ff6ce-131">`[Compare]`: Ověřuje, že dvě vlastnosti v modelu shoda.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-131">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="ff6ce-132">`[EmailAddress]`: Ověří, zda vlastnost má formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-132">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="ff6ce-133">`[Phone]`: Ověří, zda vlastnost má telefonní číslo formátu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-133">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="ff6ce-134">`[Range]`: Ověřuje, že hodnota vlastnosti spadají do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-134">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="ff6ce-135">`[RegularExpression]`: Ověřuje, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-135">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="ff6ce-136">`[Required]`: Ověřuje, že pole není null.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-136">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="ff6ce-137">Zobrazit [[povinné] atribut](#required-attribute) podrobné informace o chování tohoto atributu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-137">See [[Required] attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="ff6ce-138">`[StringLength]`: Ověřuje, že hodnota vlastnosti řetězce nepřekračuje omezení délky.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-138">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="ff6ce-139">`[Url]`: Ověří, zda vlastnost má formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-139">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="ff6ce-140">`[Remote]`: Ověřuje vstup v klientovi po zavolání metody akce na serveru.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-140">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="ff6ce-141">V tématu [[vzdálené] atribut](#remote-attribute) podrobné informace o chování tohoto atributu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-141">See [[Remote] attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="ff6ce-142">Úplný seznam atributů ověřování najdete v [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-142">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="ff6ce-143">Chybové zprávy</span><span class="sxs-lookup"><span data-stu-id="ff6ce-143">Error messages</span></span>

<span data-ttu-id="ff6ce-144">Ověřování atributů umožňují nastavit chybovou zprávu, který se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-144">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="ff6ce-145">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-145">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="ff6ce-146">Interně jsou volání atributy `String.Format` s zástupný symbol pro název pole a zástupné symboly někdy další.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-146">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="ff6ce-147">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-147">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="ff6ce-148">Při použití `Name` vlastnost, chybová zpráva vytvořili v předchozím kódu by být "Délka názvu musí být mezi 6 a 8.".</span><span class="sxs-lookup"><span data-stu-id="ff6ce-148">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="ff6ce-149">Chcete zjistit, jaké parametry jsou předány `String.Format` konkrétní atribut chybovou zprávu najdete v článku [DataAnnotations zdrojový kód](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-149">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="ff6ce-150">[Povinné] atributu</span><span class="sxs-lookup"><span data-stu-id="ff6ce-150">[Required] attribute</span></span>

<span data-ttu-id="ff6ce-151">Ve výchozím nastavení, ověření systém zpracovává null parametry nebo vlastnosti jako by měly `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-151">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="ff6ce-152">[Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) například `decimal` a `int` jsou null.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-152">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="ff6ce-153">[Povinné] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="ff6ce-153">[Required] validation on the server</span></span>

<span data-ttu-id="ff6ce-154">Na serveru požadovaná hodnota se považuje za chybějící Pokud vlastnost má hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-154">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="ff6ce-155">Null pole je vždy platné, a nikdy se zobrazí chybová zpráva atribut [povinné].</span><span class="sxs-lookup"><span data-stu-id="ff6ce-155">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="ff6ce-156">Ale model vazby pro vlastnost se zakázanou můžou selhat, a vytvářet v chybové zprávě, jako `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-156">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="ff6ce-157">Pokud chcete zadat vlastní chybovou zprávu pro ověřování na straně serveru Null typů, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-157">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="ff6ce-158">Nastavit pole s možnou hodnotou Null (například `decimal?` místo `decimal`).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-158">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="ff6ce-159">[S povolenou hodnotou Null\<T >](/dotnet/csharp/programming-guide/nullable-types/) typy hodnot jsou považovány za standardních typů s povolenou hodnotou Null.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-159">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="ff6ce-160">Nastavit výchozí chybovou zprávu pro vazbu modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-160">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="ff6ce-161">Další informace o chybách vazby modelu, můžete nastavit výchozí zprávy pro, najdete v části <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-161">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="ff6ce-162">[Povinné] ověření na straně klienta</span><span class="sxs-lookup"><span data-stu-id="ff6ce-162">[Required] validation on the client</span></span>

<span data-ttu-id="ff6ce-163">Typy neumožňující a řetězce jsou zpracovány jinak na straně klienta na server v porovnání.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-163">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="ff6ce-164">Na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-164">On the client:</span></span>

* <span data-ttu-id="ff6ce-165">Hodnota se považuje za k dispozici pouze v případě, že jsou zadány pro něj.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-165">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="ff6ce-166">Proto ověřování na straně klienta zpracovává typy neumožňující stejný jako typů s povolenou hodnotou Null.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-166">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="ff6ce-167">Prázdné znaky na pole řetězce se považuje za platný vstup pomocí jQuery ověření [požadované](https://jqueryvalidation.org/required-method/) metody.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-167">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="ff6ce-168">Ověřování na straně serveru bude považovat za pole vyžaduje řetězec neplatný Pokud pouze zadané prázdné znaky.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-168">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="ff6ce-169">Jak bylo uvedeno dříve, NULL typy se považují jako by se měli `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-169">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="ff6ce-170">To znamená, že získáte ověřování na straně klienta i v případě, že jste se nevztahují `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-170">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="ff6ce-171">Ale pokud nechcete použít atribut, můžete získat výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-171">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="ff6ce-172">Pokud chcete zadat vlastní chybové zprávy, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-172">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="ff6ce-173">Atribut [vzdálené]</span><span class="sxs-lookup"><span data-stu-id="ff6ce-173">[Remote] attribute</span></span>

<span data-ttu-id="ff6ce-174">`[Remote]` Atribut implementuje ověření na straně klienta, který vyžaduje volání metody na serveru k určení, zda vstupní pole je platný.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-174">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="ff6ce-175">Aplikace může třeba ověřit, zda uživatelské jméno je již používán.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-175">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="ff6ce-176">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-176">To implement remote validation:</span></span>

1. <span data-ttu-id="ff6ce-177">Vytvořte pro JavaScript volat metodu akce.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-177">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="ff6ce-178">JQuery ověřit [vzdálené](https://jqueryvalidation.org/remote-method/) metoda očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-178">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="ff6ce-179">`"true"` znamená to, že se vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-179">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="ff6ce-180">`"false"`, `undefined`, nebo `null` znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-180">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="ff6ce-181">Zobrazte výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-181">Display the default error message.</span></span>
   * <span data-ttu-id="ff6ce-182">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-182">Any other string means the input is invalid.</span></span> <span data-ttu-id="ff6ce-183">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-183">Display the string as a custom error message.</span></span>

   <span data-ttu-id="ff6ce-184">Tady je příklad metody akce, která vrací vlastní chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-184">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="ff6ce-185">V třídě modelu poznámky vlastnost s `[Remote]` atribut, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-185">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserEmailProperty)]

### <a name="additional-fields"></a><span data-ttu-id="ff6ce-186">Další pole</span><span class="sxs-lookup"><span data-stu-id="ff6ce-186">Additional fields</span></span>

<span data-ttu-id="ff6ce-187">`AdditionalFields` Vlastnost `[Remote]` atribut umožňuje ověření kombinace různých typů polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-187">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="ff6ce-188">Například pokud `User` měl modelů `FirstName` a `LastName` vlastností, můžete chtít ověřit, že stávající uživatelé už nemají tohoto páru názvy.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-188">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="ff6ce-189">Následující příklad ukazuje, jak používat `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-189">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="ff6ce-190">`AdditionalFields` může být explicitně nastaveno na řetězce `"FirstName"` a `"LastName"`, ale pomocí [ `nameof` ](/dotnet/csharp/language-reference/keywords/nameof) operátor zjednodušuje později refaktoring.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-190">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="ff6ce-191">Metoda akce pro toto ověření musí přijmout název první a poslední název argumenty:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-191">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="ff6ce-192">Pokud uživatel zadá název první nebo poslední, JavaScript zavolá vzdálené zobrazíte, pokud je už zabraný tohoto páru názvy.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-192">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="ff6ce-193">K ověření nejmíň dva další pole, zadejte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-193">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="ff6ce-194">Například, chcete-li přidat `MiddleName` nastavena vlastnost modelu, `[Remote]` atributu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-194">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="ff6ce-195">`AdditionalFields`, jako jsou všechny argumenty atributu musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-195">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="ff6ce-196">Proto nepoužívejte [interpolovaný řetězec](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> inicializovat `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-196">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="ff6ce-197">Alternativy k vestavěné atributy</span><span class="sxs-lookup"><span data-stu-id="ff6ce-197">Alternatives to built-in attributes</span></span>

<span data-ttu-id="ff6ce-198">Pokud potřebujete ověření integrované atributy není zadaný, můžete:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-198">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="ff6ce-199">[Vytvořit vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-199">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="ff6ce-200">[Implementace IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-200">[Implement IValidatableObject](#ivalidatableobject).</span></span>


## <a name="custom-attributes"></a><span data-ttu-id="ff6ce-201">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="ff6ce-201">Custom attributes</span></span>

<span data-ttu-id="ff6ce-202">Pro scénáře, které atributy ověření integrované nezpracovávají můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-202">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="ff6ce-203">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>a přepsat <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metody.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-203">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="ff6ce-204">`IsValid` Metoda přijímá objekt s názvem *hodnota*, což je vstup, který má být ověřen.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-204">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="ff6ce-205">Přetížení přijímá také `ValidationContext` objektu, který poskytuje dodatečné informace, jako je například instance modelu, která vytvořil vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-205">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="ff6ce-206">Následující příklad ověří, jestli datum vydání verze pro video v *Classic* žánr není novější než zadaného roku.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-206">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="ff6ce-207">`[ClassicMovie2]` Atribut nejprve zkontroluje žánr a pokračuje pouze pokud má *Classic*.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-207">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="ff6ce-208">Identifikuje jako klasické filmů zkontroluje datum vydání, abyste měli jistotu, že není novější než limit předána do konstruktoru atributu.)</span><span class="sxs-lookup"><span data-stu-id="ff6ce-208">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/sample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="ff6ce-209">`movie` Proměnná v předchozím příkladu představuje `Movie` objekt, který obsahuje data z odeslání formuláře.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-209">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="ff6ce-210">`IsValid` Metoda zkontroluje data a rozšířením podle tematických.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-210">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="ff6ce-211">Po úspěšném ověření `IsValid` vrátí `ValidationResult.Success` kódu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-211">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="ff6ce-212">Když se ověřování nezdaří, `ValidationResult` s chybou je vrácená zpráva.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-212">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="ff6ce-213">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="ff6ce-213">IValidatableObject</span></span>

<span data-ttu-id="ff6ce-214">Předchozí příklad pracuje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-214">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="ff6ce-215">Další možností pro ověřování na úrovni třídy je implementovat `IValidatableObject` v třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-215">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/sample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

::: moniker range=">= aspnetcore-2.1"

## <a name="top-level-node-validation"></a><span data-ttu-id="ff6ce-216">Uzel nejvyšší úrovně ověření</span><span class="sxs-lookup"><span data-stu-id="ff6ce-216">Top-level node validation</span></span>

<span data-ttu-id="ff6ce-217">Uzly nejvyšší úrovně patří:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-217">Top-level nodes include:</span></span>

* <span data-ttu-id="ff6ce-218">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="ff6ce-218">Action parameters</span></span>
* <span data-ttu-id="ff6ce-219">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="ff6ce-219">Controller properties</span></span>
* <span data-ttu-id="ff6ce-220">Parametry rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="ff6ce-220">Page handler parameters</span></span>
* <span data-ttu-id="ff6ce-221">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="ff6ce-221">Page model properties</span></span>

<span data-ttu-id="ff6ce-222">Model vázaný uzly nejvyšší úrovně se ověří kromě ověřování vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-222">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="ff6ce-223">V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření `phone` parametr akce:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-223">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="ff6ce-224">Uzly nejvyšší úrovně můžete použít <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověření.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-224">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="ff6ce-225">V následujícím příkladu z ukázkové aplikace `CheckAge` metody Určuje, že `age` parametr musí být vázán z řetězce dotazu, když se odešle formulář:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-225">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="ff6ce-226">Na stránce Zkontrolovat stáří (*CheckAge.cshtml*), existují dva typy.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-226">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="ff6ce-227">První formulář odešle `Age` hodnotu `99` jako řetězec dotazu: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-227">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="ff6ce-228">Když správně formátovaná `age` parametr z řetězce dotazu se odešle, ověří formuláře.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-228">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="ff6ce-229">Druhý formulář na stránce Zkontrolovat stáří odešle `Age` hodnoty v těle žádosti a ověření selže.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-229">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="ff6ce-230">Vazba se nezdaří, protože `age` parametr musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-230">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="ff6ce-231">Při spuštění s `CompatibilityVersion.Version_2_1` nebo novější, je ve výchozím nastavení povoleno ověření uzel nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-231">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="ff6ce-232">V opačném případě ověření uzel nejvyšší úrovně je zakázané.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-232">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="ff6ce-233">Výchozí možnost můžete přepsat tak, že nastavíte <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> vlastnost v (`Startup.ConfigureServices`), jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-233">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

::: moniker-end

## <a name="maximum-errors"></a><span data-ttu-id="ff6ce-234">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="ff6ce-234">Maximum errors</span></span>

<span data-ttu-id="ff6ce-235">Ověření zastaví, když je dosaženo maximálního počtu chyb (200 ve výchozím nastavení).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-235">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="ff6ce-236">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-236">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

::: moniker range=">= aspnetcore-2.1"

## <a name="maximum-recursion"></a><span data-ttu-id="ff6ce-237">Maximální povolený počet rekurzí</span><span class="sxs-lookup"><span data-stu-id="ff6ce-237">Maximum recursion</span></span>

<span data-ttu-id="ff6ce-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> prochází graf objektu model ověřován.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="ff6ce-239">U modelů, které jsou velmi podrobné nebo neomezeně rekurzivní ověření může způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-239">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="ff6ce-240">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit již v rané fázi ověřování, pokud překročí nakonfigurovanou hloubku rekurze návštěvníka.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-240">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="ff6ce-241">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 200 při spuštění s `CompatibilityVersion.Version_2_2` nebo novější.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-241">The default value of `MvcOptions.MaxValidationDepth` is 200 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="ff6ce-242">U starších verzí hodnota je null, což znamená, že žádná omezení hloubky.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-242">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="ff6ce-243">Automatické zkrácenou</span><span class="sxs-lookup"><span data-stu-id="ff6ce-243">Automatic short-circuit</span></span>

<span data-ttu-id="ff6ce-244">Ověření se automaticky zkratována (vynechaný) Pokud graf modelu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-244">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="ff6ce-245">Objekty, které modul runtime přeskočí ověřování zahrnout kolekce primitivních elementů (například `byte[]`, `string[]`, `Dictionary<string, string>`) a komplexní objekt, grafy, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-245">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

::: moniker-end

## <a name="disable-validation"></a><span data-ttu-id="ff6ce-246">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="ff6ce-246">Disable validation</span></span>

<span data-ttu-id="ff6ce-247">Postup při zakázání ověření:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-247">To disable validation:</span></span>

1. <span data-ttu-id="ff6ce-248">Vytvořte implementaci třídy `IObjectModelValidator` všechna pole, která není označit jako neplatný.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-248">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/sample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="ff6ce-249">Přidejte následující kód, který `Startup.ConfigureServices` nahradit výchozí `IObjectModelValidator` implementace v kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-249">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="ff6ce-250">Stále se může zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-250">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="ff6ce-251">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="ff6ce-251">Client-side validation</span></span>

<span data-ttu-id="ff6ce-252">Ověřování na straně klienta zabrání odeslání dokud formulář není platný.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-252">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="ff6ce-253">Tlačítka pro odeslání spustí jazyka JavaScript, který se odešle formulář nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-253">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="ff6ce-254">Ověřování na straně klienta se vyhnete zbytečným odezvy serveru když jsou vstupní chyby ve formuláři.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-254">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="ff6ce-255">Následující skript se odkazuje v *_Layout.cshtml* a *_ValidationScriptsPartial.cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-255">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="ff6ce-256">[JQuery Nerušivý ověření](https://github.com/aspnet/jquery-validation-unobtrusive) skript je vlastní Microsoft front-endu knihovnu, která staví na oblíbené [jQuery ověřit](https://jqueryvalidation.org/) modulu plug-in.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-256">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="ff6ce-257">Bez jQuery Nerušivý ověřování, bude muset kód stejnou logiku ověřování na dvou místech: jednou v atributech ověřování na straně serveru na vlastnosti projektu a pak znovu ve skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-257">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="ff6ce-258">Místo toho [pomocných rutin značek](xref:mvc/views/tag-helpers/intro) a [pomocných rutin HTML](xref:mvc/views/overview) atributů ověření a zadejte metadata z vlastnosti modelu k vykreslení HTML 5 `data-` atributy prvků formuláře, které vyžadují ověřování.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-258">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="ff6ce-259">analyzuje Nerušivý ověřování jQuery `data-` atributy a předá logiku jQuery ověřit efektivně "kopírování" logiku ověřování na straně serveru do klienta.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-259">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="ff6ce-260">Zobrazení chyb ověřování na straně klienta, použití pomocných rutin značek, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-260">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="ff6ce-261">Předchozí pomocných rutin značek vykreslení následující kód HTML.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-261">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="" />
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="ff6ce-262">Všimněte si, že `data-` atributů v kódu HTML výstup odpovídají atributů ověření pro `ReleaseDate` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-262">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="ff6ce-263">`data-val-required` Atribut obsahuje chybovou zprávu se zobrazí, pokud uživatel nemá vyplnit pole Datum vydání verze.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-263">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="ff6ce-264">jQuery Nerušivý ověření tuto hodnotu předá jQuery ověřit [ `required()` ](https://jqueryvalidation.org/required-method/) metodu, která se zobrazí zpráva v souvisejícím  **\<span >** elementu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-264">jQuery Unobtrusive Validation passes this value to the jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="ff6ce-265">Ověření typu dat vychází .NET typu vlastnosti, pokud není, který je přepsán `[DataType]` atribut.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-265">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="ff6ce-266">Prohlížečů mají své vlastní výchozí chybové zprávy, ale jQuery ověření Nerušivého ověření balíček můžete přepsat tyto zprávy.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-266">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="ff6ce-267">`[DataType]` atributy a podtříd, jako `[EmailAddress]` umožňují nastavit chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-267">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="ff6ce-268">Přidání ověřování pro dynamické formuláře</span><span class="sxs-lookup"><span data-stu-id="ff6ce-268">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="ff6ce-269">jQuery Nerušivý ověření předá logiku ověřování a parametry jQuery ověřit při prvním načtení stránky.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-269">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="ff6ce-270">Ověření proto nebude fungovat automaticky v dynamicky generovaném formuláři.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-270">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="ff6ce-271">Pokud chcete povolit ověřování, řekněte jQuery Nerušivý ověření analyzovat dynamická podoba ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-271">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="ff6ce-272">Například následující kód nastaví ověřování na straně klienta ve formuláři přidány prostřednictvím AJAX.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-272">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```js
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="ff6ce-273">`$.validator.unobtrusive.parse()` Metoda přijímá selektor jQuery pro svůj argument.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-273">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="ff6ce-274">Tato metoda říká jQuery Nerušivý ověření analyzovat `data-` atributy formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-274">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="ff6ce-275">Hodnoty tyto atributy jsou potom předány do modulu plug-in jQuery ověřit.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-275">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="ff6ce-276">Přidání ověřování pro dynamické ovládací prvky</span><span class="sxs-lookup"><span data-stu-id="ff6ce-276">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="ff6ce-277">`$.validator.unobtrusive.parse()` Metoda se dá použít na celý formulář, nikoli na jednotlivé dynamicky generované ovládací prvky, jako například `<input/>` a `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-277">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input/>` and `<select/>`.</span></span> <span data-ttu-id="ff6ce-278">Chcete-li rozboru formuláře, odeberte ověřovací data, která se přidal, když formulář byl analyzován dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-278">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```js
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="ff6ce-279">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="ff6ce-279">Custom client-side validation</span></span>

<span data-ttu-id="ff6ce-280">Vlastní ověřování na straně klienta se provádí pomocí generování `data-` atributy HTML, které pracují s adaptérem ověřit vlastní jQuery.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-280">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="ff6ce-281">Následující ukázkový kód adaptéru bylo napsáno pro `ClassicMovie` a `ClassicMovie2` atributy, které byly zavedeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-281">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/sample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="ff6ce-282">Informace o tom, jak psát adaptéry najdete v tématu [jQuery ověřit dokumentaci](http://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-282">For information about how to write adapters, see the [jQuery Validate documentation](http://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="ff6ce-283">Použít adaptér pro dané pole aktivuje `data-` atributy, které:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-283">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="ff6ce-284">Označit pole jako neprošla ověřením (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-284">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="ff6ce-285">Identifikace ověření pravidlo název a chyba text zprávy (třeba `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-285">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="ff6ce-286">Zadejte další parametry validátor potřebuje (například `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-286">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="ff6ce-287">Následující příklad ukazuje `data-` atributy pro ukázkovou aplikaci `ClassicMovie` atribut:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-287">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="" />
```

<span data-ttu-id="ff6ce-288">Jak bylo uvedeno dříve, [pomocných rutin značek](xref:mvc/views/tag-helpers/intro) a [pomocných rutin HTML](xref:mvc/views/overview) pomocí informací z atributů ověření pro vykreslení `data-` atributy.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-288">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="ff6ce-289">Existují dvě možnosti pro psaní kódu, který má za následek vytvoření vlastní `data-` atributy HTML:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-289">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="ff6ce-290">Vytvořte třídu, která je odvozena z `AttributeAdapterBase<TAttribute>` a třídu, která implementuje `IValidationAttributeAdapterProvider`a registrace v DI atribut a jeho adaptéru.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-290">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="ff6ce-291">Tato metoda odpovídá [jednotnou zodpovědnost hlavní](https://wikipedia.org/wiki/Single_responsibility_principle) v tom, že je kód pro ověření související se server a klienta v samostatné třídy.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-291">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="ff6ce-292">Adaptér má také využívat, protože je registrován v DI, ostatní služby v DI jsou k dispozici v případě potřeby.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-292">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="ff6ce-293">Implementace `IClientModelValidator` ve vašich `ValidationAttribute` třídy.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-293">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="ff6ce-294">Tato metoda může být vhodné, pokud atribut neprovádí žádné ověřování na straně serveru a není nutné žádné služby z DI.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-294">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="ff6ce-295">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="ff6ce-295">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="ff6ce-296">Tato metoda vykreslování `data-` atributy ve formátu HTML je používán `ClassicMovie` atribut v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-296">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="ff6ce-297">Přidání ověřování na straně klienta pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-297">To add client validation by using this method:</span></span>

1. <span data-ttu-id="ff6ce-298">Vytvořte třídu atributu adaptér pro atribut vlastní ověřování.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-298">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="ff6ce-299">Odvodit třídu z [AttributeAdapterBase\<T >](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="ff6ce-299">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="ff6ce-300">Vytvoření `AddValidation` metodu, která přidá `data-` atributů vykresleného výstupu, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-300">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="ff6ce-301">Vytvořit třídu adaptéru poskytovatele, který implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-301">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="ff6ce-302">V `GetAttributeAdapter` metoda předat ve vlastním atributu je adaptér konstruktoru, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-302">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="ff6ce-303">Zaregistrujte adaptér poskytovatele pro DI v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-303">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="ff6ce-304">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="ff6ce-304">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="ff6ce-305">Tato metoda vykreslování `data-` atributy ve formátu HTML je používán `ClassicMovie2` atribut v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-305">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="ff6ce-306">Přidání ověřování na straně klienta pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-306">To add client validation by using this method:</span></span>

* <span data-ttu-id="ff6ce-307">V atributu vlastní ověřovací implementovat `IClientModelValidator` rozhraní a vytvořit `AddValidation` metody.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-307">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="ff6ce-308">V `AddValidation` metodu, přidejte `data-` atributů pro ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-308">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="ff6ce-309">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="ff6ce-309">Disable client-side validation</span></span>

<span data-ttu-id="ff6ce-310">Následující kód zakazuje ověření klienta v zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="ff6ce-310">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="ff6ce-311">Tento postup funguje pouze v zobrazení MVC, není v stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-311">This works only in MVC views, not in Razor Pages.</span></span> <span data-ttu-id="ff6ce-312">Další možností pro zakázání ověřování na straně klienta je odkaz na komentář `_ValidationScriptsPartial` ve vašich *.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="ff6ce-312">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff6ce-313">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ff6ce-313">Additional resources</span></span>

* [<span data-ttu-id="ff6ce-314">Obor názvů System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="ff6ce-314">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="ff6ce-315">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="ff6ce-315">Model Binding</span></span>](model-binding.md)
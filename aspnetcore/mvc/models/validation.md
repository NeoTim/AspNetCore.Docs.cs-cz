---
title: Ověření modelu ve ASP.NET Core MVC
author: tdykstra
description: Přečtěte si o ověřování modelu ve ASP.NET Core MVC a Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
monikerRange: '>= aspnetcore-2.1'
uid: mvc/models/validation
ms.openlocfilehash: 43b69e9b7588ad575f203200c5bc59a4272d0066
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "67814103"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="fe1a9-103">Ověřování modelu ve ASP.NET Core MVC a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="fe1a9-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

<span data-ttu-id="fe1a9-104">Tento článek vysvětluje, jak ověřit vstup uživatele ve ASP.NET Core MVC nebo v aplikaci Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-104">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="fe1a9-105">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="fe1a9-106">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="fe1a9-106">Model state</span></span>

<span data-ttu-id="fe1a9-107">Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-107">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="fe1a9-108">Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat (například "x" je zadáno v poli, které očekává celé číslo).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-108">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="fe1a9-109">K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům (například hodnota 0 je zadána v poli, které očekává hodnocení mezi 1 a 5).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-109">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="fe1a9-110">Před provedením akce kontroleru nebo obslužné rutiny Razor Pages se vyskytuje jak vazba modelů, tak ověřování.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-110">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="fe1a9-111">U webových aplikací je zodpovědností aplikace vhodné je kontrolovat `ModelState.IsValid` a reagovat.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-111">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="fe1a9-112">Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-112">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="fe1a9-113">Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid` , jestli `[ApiController]` mají atribut.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-113">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="fe1a9-114">V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o problému, pokud stav modelu není platný.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-114">In that case, an automatic HTTP 400 response containing issue details is returned when model state is invalid.</span></span> <span data-ttu-id="fe1a9-115">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-115">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="fe1a9-116">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="fe1a9-116">Rerun validation</span></span>

<span data-ttu-id="fe1a9-117">Ověřování je automatické, ale můžete je chtít opakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-117">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="fe1a9-118">Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-118">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="fe1a9-119">Chcete-li znovu spustit ověřování `TryValidateModel` , zavolejte metodu, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-119">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/sample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="fe1a9-120">Atributy ověřování</span><span class="sxs-lookup"><span data-stu-id="fe1a9-120">Validation attributes</span></span>

<span data-ttu-id="fe1a9-121">Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-121">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="fe1a9-122">Následující příklad z [ukázkové aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) zobrazuje třídu modelu s poznámkou ověřování atributů.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-122">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="fe1a9-123">`[ClassicMovie]` Atribut je vlastní ověřovací atribut a jsou integrovány ostatní.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-123">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="fe1a9-124">(Nezobrazuje se `[ClassicMovie2]`, což ukazuje alternativní způsob implementace vlastního atributu.)</span><span class="sxs-lookup"><span data-stu-id="fe1a9-124">(Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.)</span></span>

[!code-csharp[](validation/sample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="fe1a9-125">Předdefinované atributy</span><span class="sxs-lookup"><span data-stu-id="fe1a9-125">Built-in attributes</span></span>

<span data-ttu-id="fe1a9-126">Tady jsou některé z vestavěných ověřovacích atributů:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-126">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="fe1a9-127">`[CreditCard]`: Ověří, jestli má vlastnost formát kreditní karty.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-127">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="fe1a9-128">`[Compare]`: Ověřuje, že se shodují dvě vlastnosti v modelu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-128">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="fe1a9-129">`[EmailAddress]`: Ověří, zda má vlastnost formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-129">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="fe1a9-130">`[Phone]`: Ověří, zda má vlastnost formát telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-130">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="fe1a9-131">`[Range]`: Ověří, zda hodnota vlastnosti spadá do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-131">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="fe1a9-132">`[RegularExpression]`: Ověří, zda hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-132">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="fe1a9-133">`[Required]`: Ověří, zda pole nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-133">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="fe1a9-134">Podrobnosti o chování tohoto atributu naleznete v [atributu [required]](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="fe1a9-134">See [[Required] attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="fe1a9-135">`[StringLength]`: Ověří, že hodnota vlastnosti řetězce nepřekračuje zadané omezení délky.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-135">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="fe1a9-136">`[Url]`: Ověří, zda má vlastnost formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-136">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="fe1a9-137">`[Remote]`: Ověří vstup na straně klienta voláním metody Action na serveru.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-137">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="fe1a9-138">Podrobnosti o chování tohoto atributu naleznete v [atributu [Remote]](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="fe1a9-138">See [[Remote] attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="fe1a9-139">Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel.](xref:System.ComponentModel.DataAnnotations) DataAnnotations.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-139">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="fe1a9-140">Chybové zprávy</span><span class="sxs-lookup"><span data-stu-id="fe1a9-140">Error messages</span></span>

<span data-ttu-id="fe1a9-141">Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-141">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="fe1a9-142">Příklad:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-142">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="fe1a9-143">Interně atributy volají `String.Format` jako zástupný symbol pro název pole a někdy další zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-143">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="fe1a9-144">Příklad:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-144">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="fe1a9-145">Při použití na `Name` vlastnost bude chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."</span><span class="sxs-lookup"><span data-stu-id="fe1a9-145">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="fe1a9-146">Chcete-li zjistit, které parametry jsou `String.Format` předány pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)pro anotace.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-146">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="fe1a9-147">[Required] – atribut</span><span class="sxs-lookup"><span data-stu-id="fe1a9-147">[Required] attribute</span></span>

<span data-ttu-id="fe1a9-148">Ve výchozím nastavení systém ověřování zpracovává parametry, které neumožňují hodnotu null nebo vlastnosti, jako by měly `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-148">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="fe1a9-149">`int` [Typy hodnot,](/dotnet/csharp/language-reference/keywords/value-types) jako jsou a,nejsounull.`decimal`</span><span class="sxs-lookup"><span data-stu-id="fe1a9-149">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="fe1a9-150">[Požadováno] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="fe1a9-150">[Required] validation on the server</span></span>

<span data-ttu-id="fe1a9-151">Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-151">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="fe1a9-152">Pole, které nesmí mít hodnotu null, je vždy platné a chybová zpráva [required] se nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-152">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="fe1a9-153">Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, což vede k chybové zprávě, `The value '' is invalid`jako je například.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-153">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="fe1a9-154">Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-154">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="fe1a9-155">Převést pole na hodnotu null (například `decimal?` `decimal`místo).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-155">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="fe1a9-156">Typy hodnot s možnou hodnotou [null\<>](/dotnet/csharp/programming-guide/nullable-types/) jsou považovány za standardní typy s možnou hodnotou null</span><span class="sxs-lookup"><span data-stu-id="fe1a9-156">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="fe1a9-157">Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-157">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="fe1a9-158">Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>v tématu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-158">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="fe1a9-159">[Požadováno] ověřování na klientovi</span><span class="sxs-lookup"><span data-stu-id="fe1a9-159">[Required] validation on the client</span></span>

<span data-ttu-id="fe1a9-160">Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-160">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="fe1a9-161">Na klientovi:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-161">On the client:</span></span>

* <span data-ttu-id="fe1a9-162">Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-162">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="fe1a9-163">Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-163">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="fe1a9-164">Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-164">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="fe1a9-165">Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-165">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="fe1a9-166">Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za `[Required]` , i když mají atribut.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-166">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="fe1a9-167">To znamená, že získáte ověřování na straně klienta i v `[Required]` případě, že atribut nepoužijete.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-167">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="fe1a9-168">Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-168">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="fe1a9-169">Chcete-li zadat vlastní chybovou zprávu, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-169">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="fe1a9-170">[Remote] – atribut</span><span class="sxs-lookup"><span data-stu-id="fe1a9-170">[Remote] attribute</span></span>

<span data-ttu-id="fe1a9-171">`[Remote]` Atribut implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-171">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="fe1a9-172">Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-172">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="fe1a9-173">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-173">To implement remote validation:</span></span>

1. <span data-ttu-id="fe1a9-174">Vytvořte metodu Action pro volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-174">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="fe1a9-175">Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-175">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="fe1a9-176">`"true"`znamená, že jsou vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-176">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="fe1a9-177">`"false"`, `undefined` nebo`null` znamená, že vstup není platný.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-177">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="fe1a9-178">Zobrazí výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-178">Display the default error message.</span></span>
   * <span data-ttu-id="fe1a9-179">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-179">Any other string means the input is invalid.</span></span> <span data-ttu-id="fe1a9-180">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-180">Display the string as a custom error message.</span></span>

   <span data-ttu-id="fe1a9-181">Tady je příklad metody akce, která vrací vlastní chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-181">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="fe1a9-182">Ve třídě modelu poznámkujte vlastnost s `[Remote]` atributem, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-182">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="fe1a9-183">`[Remote]` Atribut je`Microsoft.AspNetCore.Mvc` v oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-183">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="fe1a9-184">Pokud nepoužíváte `Microsoft.AspNetCore.App` Metapackage nebo `Microsoft.AspNetCore.All` , nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) .</span><span class="sxs-lookup"><span data-stu-id="fe1a9-184">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="fe1a9-185">Další pole</span><span class="sxs-lookup"><span data-stu-id="fe1a9-185">Additional fields</span></span>

<span data-ttu-id="fe1a9-186">`AdditionalFields` Vlastnost`[Remote]` atributu umožňuje ověřit kombinace polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-186">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="fe1a9-187">Pokud má `User` `LastName` model například `FirstName` a vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají odpovídající dvojici názvů.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-187">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="fe1a9-188">Následující příklad ukazuje, jak použít `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-188">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="fe1a9-189">`AdditionalFields`lze nastavit explicitně na řetězce `"FirstName"` a `"LastName"`, ale použití [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operátoru zjednodušuje pozdější refaktoring.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-189">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="fe1a9-190">Metoda Action pro toto ověření musí přijmout argumenty jméno a příjmení:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-190">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="fe1a9-191">Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-191">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="fe1a9-192">Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-192">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="fe1a9-193">Chcete-li například přidat `MiddleName` vlastnost do modelu, `[Remote]` nastavte atribut, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-193">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="fe1a9-194">`AdditionalFields`Podobně jako všechny argumenty atributu musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-194">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="fe1a9-195">Proto nepoužívejte interpolované [řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-195">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="fe1a9-196">Alternativy k předdefinovaným atributům</span><span class="sxs-lookup"><span data-stu-id="fe1a9-196">Alternatives to built-in attributes</span></span>

<span data-ttu-id="fe1a9-197">Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-197">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="fe1a9-198">[Vytvořte vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-198">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="fe1a9-199">[Implementujte IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-199">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="fe1a9-200">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="fe1a9-200">Custom attributes</span></span>

<span data-ttu-id="fe1a9-201">U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-201">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="fe1a9-202">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, a <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> přepište metodu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-202">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="fe1a9-203">Metoda přijímá objekt s názvem hodnota, což je vstup, který má být ověřen.  `IsValid`</span><span class="sxs-lookup"><span data-stu-id="fe1a9-203">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="fe1a9-204">Přetížení také přijímá `ValidationContext` objekt, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-204">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="fe1a9-205">Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-205">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="fe1a9-206">Atribut nejprve zkontroluje Žánr a pokračuje pouze v případě, že je *klasický.* `[ClassicMovie2]`</span><span class="sxs-lookup"><span data-stu-id="fe1a9-206">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="fe1a9-207">U filmů identifikovaných jako klasických kontroluje datum vydání, aby se zajistilo, že není pozdější než limit předaný konstruktoru atributu.)</span><span class="sxs-lookup"><span data-stu-id="fe1a9-207">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/sample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="fe1a9-208">Proměnná v předchozím příkladu `Movie` představuje objekt, který obsahuje data z odesílání formuláře. `movie`</span><span class="sxs-lookup"><span data-stu-id="fe1a9-208">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="fe1a9-209">`IsValid` Metoda zkontroluje datum a Žánr.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-209">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="fe1a9-210">Po úspěšném ověření `IsValid` `ValidationResult.Success` vrátí kód.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-210">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="fe1a9-211">Pokud se ověření nepovede `ValidationResult` , vrátí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-211">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="fe1a9-212">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="fe1a9-212">IValidatableObject</span></span>

<span data-ttu-id="fe1a9-213">Předchozí příklad funguje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-213">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="fe1a9-214">Další možností pro ověřování na úrovni třídy je implementovat `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-214">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/sample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="fe1a9-215">Ověřování uzlu nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="fe1a9-215">Top-level node validation</span></span>

<span data-ttu-id="fe1a9-216">Uzly nejvyšší úrovně zahrnují:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-216">Top-level nodes include:</span></span>

* <span data-ttu-id="fe1a9-217">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="fe1a9-217">Action parameters</span></span>
* <span data-ttu-id="fe1a9-218">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="fe1a9-218">Controller properties</span></span>
* <span data-ttu-id="fe1a9-219">Parametry obslužné rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="fe1a9-219">Page handler parameters</span></span>
* <span data-ttu-id="fe1a9-220">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="fe1a9-220">Page model properties</span></span>

<span data-ttu-id="fe1a9-221">Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-221">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="fe1a9-222">V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> používá k ověření `phone` parametru akce:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-222">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="fe1a9-223">Uzly nejvyšší úrovně mohou být použity <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-223">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="fe1a9-224">V následujícím příkladu z ukázkové aplikace `CheckAge` určuje metoda `age` , že parametr musí být svázán z řetězce dotazu při odeslání formuláře:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-224">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="fe1a9-225">Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-225">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="fe1a9-226">První formulář odešle `Age` `99` hodnotu jako řetězec dotazu: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-226">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="fe1a9-227">Při odeslání správně formátovaného `age` parametru z řetězce dotazu se formulář ověří.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-227">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="fe1a9-228">Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-228">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="fe1a9-229">Vazba se nezdařila, `age` protože parametr musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-229">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="fe1a9-230">Při spuštění s `CompatibilityVersion.Version_2_1` nebo novějším je ověřování uzlů nejvyšší úrovně ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-230">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="fe1a9-231">V opačném případě je ověřování uzlu nejvyšší úrovně zakázané.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-231">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="fe1a9-232">Výchozí možnost může být přepsána nastavením <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> vlastnosti v (`Startup.ConfigureServices`), jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-232">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="fe1a9-233">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="fe1a9-233">Maximum errors</span></span>

<span data-ttu-id="fe1a9-234">Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-234">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="fe1a9-235">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-235">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="fe1a9-236">Maximální rekurze</span><span class="sxs-lookup"><span data-stu-id="fe1a9-236">Maximum recursion</span></span>

<span data-ttu-id="fe1a9-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>projde grafem objektu ověřovaného modelu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="fe1a9-238">U modelů, které jsou velmi hlubokoelné nebo nekonečné rekurzivní, může ověřování způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-238">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="fe1a9-239">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="fe1a9-240">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 200 při spuštění v systému `CompatibilityVersion.Version_2_2` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-240">The default value of `MvcOptions.MaxValidationDepth` is 200 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="fe1a9-241">Pro starší verze je hodnota null, což znamená bez omezení hloubky.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-241">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="fe1a9-242">Automatické krátké okruhy</span><span class="sxs-lookup"><span data-stu-id="fe1a9-242">Automatic short-circuit</span></span>

<span data-ttu-id="fe1a9-243">Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-243">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="fe1a9-244">Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních elementů ( `byte[]`například `string[]`, `Dictionary<string, string>`,) a složitých grafů objektů, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-244">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="fe1a9-245">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="fe1a9-245">Disable validation</span></span>

<span data-ttu-id="fe1a9-246">Zakázání ověřování:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-246">To disable validation:</span></span>

1. <span data-ttu-id="fe1a9-247">Vytvořte implementaci `IObjectModelValidator` , která neoznačí žádná pole jako neplatnou.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-247">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/sample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="fe1a9-248">Přidejte následující kód k `Startup.ConfigureServices` nahrazení výchozí `IObjectModelValidator` implementace v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-248">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="fe1a9-249">Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-249">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="fe1a9-250">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="fe1a9-250">Client-side validation</span></span>

<span data-ttu-id="fe1a9-251">Ověřování na straně klienta brání odeslání, dokud není formulář platný.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-251">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="fe1a9-252">Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-252">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="fe1a9-253">Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-253">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="fe1a9-254">Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-254">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="fe1a9-255">Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="fe1a9-255">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="fe1a9-256">Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-256">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="fe1a9-257">Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu k vykreslování atributů HTML 5 `data-` pro prvky formuláře, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-257">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="fe1a9-258">jQuery nenáročné ověřování analyzuje `data-` atributy a předá logiku do příkazu jQuery Validate a efektivně kopíruje logiku ověřování na straně serveru do klienta.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-258">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="fe1a9-259">Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-259">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="fe1a9-260">Předchozí pomocník značek vykresluje následující kód HTML.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-260">The preceding tag helpers render the following HTML.</span></span>

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
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="fe1a9-261">Všimněte si, `data-` že atributy ve výstupu HTML odpovídají atributům ověřování `ReleaseDate` pro vlastnost.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-261">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="fe1a9-262">`data-val-required` Atribut obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-262">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="fe1a9-263">jQuery unpassing předá tuto hodnotu metodě jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodném  **\<elementu span >** .</span><span class="sxs-lookup"><span data-stu-id="fe1a9-263">jQuery Unobtrusive Validation passes this value to the jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="fe1a9-264">Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána `[DataType]` atributem.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-264">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="fe1a9-265">Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-265">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="fe1a9-266">`[DataType]`atributy a podtřídy, jako je `[EmailAddress]` například umožňuje zadat chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-266">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="fe1a9-267">Přidání ověřování do dynamických formulářů</span><span class="sxs-lookup"><span data-stu-id="fe1a9-267">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="fe1a9-268">jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-268">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="fe1a9-269">Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-269">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="fe1a9-270">Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-270">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="fe1a9-271">Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-271">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="fe1a9-272">`$.validator.unobtrusive.parse()` Metoda přijímá selektor jQuery pro svůj jeden argument.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-272">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="fe1a9-273">Tato metoda oznamuje nenáročné ověřování, aby bylo `data-` možné analyzovat atributy formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-273">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="fe1a9-274">Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-274">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="fe1a9-275">Přidání ověřování do dynamických ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="fe1a9-275">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="fe1a9-276">Metoda funguje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, `<input>` například a `<select/>`. `$.validator.unobtrusive.parse()`</span><span class="sxs-lookup"><span data-stu-id="fe1a9-276">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="fe1a9-277">Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-277">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="fe1a9-278">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="fe1a9-278">Custom client-side validation</span></span>

<span data-ttu-id="fe1a9-279">Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-279">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="fe1a9-280">Následující vzorový kód adaptéru byl napsán pro `ClassicMovie` atributy a `ClassicMovie2` , které byly představeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-280">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/sample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="fe1a9-281">Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-281">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="fe1a9-282">Použití adaptéru pro dané pole se spustí pomocí `data-` atributů, které:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-282">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="fe1a9-283">Označte pole jako podléhající ověřování (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-283">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="fe1a9-284">Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-284">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="fe1a9-285">Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-285">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="fe1a9-286">Následující příklad ukazuje `data-` atributy pro `ClassicMovie` atribut ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-286">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="fe1a9-287">Jak bylo uvedeno dříve, [pomocníkům značek](xref:mvc/views/tag-helpers/intro) a značkám [HTML](xref:mvc/views/overview) se při vykreslování `data-` atributů používají informace z atributů ověřování.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-287">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="fe1a9-288">Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních `data-` atributů HTML:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-288">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="fe1a9-289">Vytvořte třídu, která je odvozena `AttributeAdapterBase<TAttribute>` z třídy a třídu, `IValidationAttributeAdapterProvider`která implementuje, a zaregistrujte svůj atribut a jeho adaptér v di.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-289">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="fe1a9-290">Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-290">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="fe1a9-291">Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-291">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="fe1a9-292">Implementujte `IClientModelValidator` ve `ValidationAttribute` své třídě.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-292">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="fe1a9-293">Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-293">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="fe1a9-294">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="fe1a9-294">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="fe1a9-295">Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie` atributem v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-295">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="fe1a9-296">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-296">To add client validation by using this method:</span></span>

1. <span data-ttu-id="fe1a9-297">Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-297">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="fe1a9-298">Odvodit třídu z [AttributeAdapterBase\<T >](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="fe1a9-298">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="fe1a9-299">Vytvořte metodu, která přidá `data-` atributy do vykresleného výstupu, jak je znázorněno v následujícím příkladu: `AddValidation`</span><span class="sxs-lookup"><span data-stu-id="fe1a9-299">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="fe1a9-300">Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-300">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="fe1a9-301">`GetAttributeAdapter` V metodě předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-301">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="fe1a9-302">Zaregistrujte poskytovatele adaptéru pro DI `Startup.ConfigureServices`v:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-302">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="fe1a9-303">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="fe1a9-303">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="fe1a9-304">Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie2` atributem v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-304">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="fe1a9-305">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-305">To add client validation by using this method:</span></span>

* <span data-ttu-id="fe1a9-306">Ve vlastním ověřovacím atributu implementujte `IClientModelValidator` rozhraní a `AddValidation` vytvořte metodu.</span><span class="sxs-lookup"><span data-stu-id="fe1a9-306">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="fe1a9-307">V metodě přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu: `AddValidation`</span><span class="sxs-lookup"><span data-stu-id="fe1a9-307">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="fe1a9-308">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="fe1a9-308">Disable client-side validation</span></span>

<span data-ttu-id="fe1a9-309">Následující kód zakáže ověřování klienta v zobrazeních MVC:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-309">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="fe1a9-310">A v Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="fe1a9-310">And in Razor Pages:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="fe1a9-311">Další možností pro zakázání ověřování klienta je přidat komentář k odkazu do `_ValidationScriptsPartial` souboru *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="fe1a9-311">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe1a9-312">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fe1a9-312">Additional resources</span></span>

* [<span data-ttu-id="fe1a9-313">Obor názvů System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="fe1a9-313">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="fe1a9-314">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="fe1a9-314">Model Binding</span></span>](model-binding.md)

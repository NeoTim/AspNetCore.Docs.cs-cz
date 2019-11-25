---
title: Ověření modelu ve ASP.NET Core MVC
author: rick-anderson
description: Přečtěte si o ověřování modelu ve ASP.NET Core MVC a Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2019
uid: mvc/models/validation
ms.openlocfilehash: 19f71799e958e2761832c91cec6762a6d391d2b5
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317430"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="b1b71-103">Ověřování modelu ve ASP.NET Core MVC a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b1b71-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1b71-104">Od [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b1b71-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="b1b71-105">Tento článek vysvětluje, jak ověřit vstup uživatele ve ASP.NET Core MVC nebo v aplikaci Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="b1b71-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="b1b71-106">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b1b71-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="b1b71-107">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="b1b71-107">Model state</span></span>

<span data-ttu-id="b1b71-108">Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="b1b71-109">Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat.</span><span class="sxs-lookup"><span data-stu-id="b1b71-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="b1b71-110">Například "x" je zadáno v poli typu Integer.</span><span class="sxs-lookup"><span data-stu-id="b1b71-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="b1b71-111">K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům.</span><span class="sxs-lookup"><span data-stu-id="b1b71-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="b1b71-112">Například hodnota 0 se zadává v poli, které očekává hodnocení mezi 1 a 5.</span><span class="sxs-lookup"><span data-stu-id="b1b71-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="b1b71-113">Před provedením akce kontroleru nebo obslužné rutiny Razor Pages dojde k ověření vazby modelů i k ověřování modelu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="b1b71-114">U webových aplikací je zodpovědností aplikace na kontrolu `ModelState.IsValid` a odpovídajícím způsobem reagovat.</span><span class="sxs-lookup"><span data-stu-id="b1b71-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="b1b71-115">Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="b1b71-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="b1b71-116">Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid`, pokud mají `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="b1b71-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="b1b71-117">V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o chybě, pokud stav modelu není platný.</span><span class="sxs-lookup"><span data-stu-id="b1b71-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="b1b71-118">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="b1b71-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="b1b71-119">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="b1b71-119">Rerun validation</span></span>

<span data-ttu-id="b1b71-120">Ověřování je automatické, ale můžete je chtít opakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="b1b71-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="b1b71-121">Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="b1b71-122">Chcete-li znovu spustit ověřování, zavolejte metodu `TryValidateModel`, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="b1b71-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="b1b71-123">Atributy ověřování</span><span class="sxs-lookup"><span data-stu-id="b1b71-123">Validation attributes</span></span>

<span data-ttu-id="b1b71-124">Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="b1b71-125">Následující příklad z ukázkové aplikace zobrazuje třídu modelu s poznámkou ověřování atributů.</span><span class="sxs-lookup"><span data-stu-id="b1b71-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="b1b71-126">Atribut `[ClassicMovie]` je vlastní ověřovací atribut a jsou integrovány i ostatní.</span><span class="sxs-lookup"><span data-stu-id="b1b71-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="b1b71-127">Nezobrazuje se `[ClassicMovieWithClientValidator]`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="b1b71-128">`[ClassicMovieWithClientValidator]` ukazuje alternativní způsob implementace vlastního atributu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="b1b71-129">Předdefinované atributy</span><span class="sxs-lookup"><span data-stu-id="b1b71-129">Built-in attributes</span></span>

<span data-ttu-id="b1b71-130">Tady jsou některé z vestavěných ověřovacích atributů:</span><span class="sxs-lookup"><span data-stu-id="b1b71-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="b1b71-131">`[CreditCard]`: ověřuje, zda má vlastnost formát kreditní karty.</span><span class="sxs-lookup"><span data-stu-id="b1b71-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="b1b71-132">`[Compare]`: ověří, že se dvě vlastnosti v modelu shodují.</span><span class="sxs-lookup"><span data-stu-id="b1b71-132">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="b1b71-133">`[EmailAddress]`: ověřuje, zda má vlastnost formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-133">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="b1b71-134">`[Phone]`: ověřuje, zda má vlastnost formát telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="b1b71-134">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="b1b71-135">`[Range]`: ověří, že hodnota vlastnosti spadá do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-135">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="b1b71-136">`[RegularExpression]`: ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-136">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="b1b71-137">`[Required]`: ověří, že pole nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="b1b71-137">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="b1b71-138">Podrobnosti o chování tohoto atributu naleznete v [atributu [required]](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="b1b71-138">See [[Required] attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="b1b71-139">`[StringLength]`: ověří, že hodnota řetězcové vlastnosti nepřekračuje zadané omezení délky.</span><span class="sxs-lookup"><span data-stu-id="b1b71-139">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="b1b71-140">`[Url]`: ověřuje, zda má vlastnost formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="b1b71-140">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="b1b71-141">`[Remote]`: ověřuje vstup na straně klienta voláním metody Action na serveru.</span><span class="sxs-lookup"><span data-stu-id="b1b71-141">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="b1b71-142">Podrobnosti o chování tohoto atributu naleznete v [atributu [Remote]](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="b1b71-142">See [[Remote] attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="b1b71-143">Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="b1b71-143">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="b1b71-144">Chybové zprávy</span><span class="sxs-lookup"><span data-stu-id="b1b71-144">Error messages</span></span>

<span data-ttu-id="b1b71-145">Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="b1b71-145">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="b1b71-146">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b1b71-146">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="b1b71-147">Interně atributy volají `String.Format` zástupný symbol pro název pole a někdy další zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="b1b71-147">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="b1b71-148">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b1b71-148">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="b1b71-149">Při použití na vlastnost `Name` by byla chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."</span><span class="sxs-lookup"><span data-stu-id="b1b71-149">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="b1b71-150">Chcete-li zjistit, které parametry jsou předány `String.Format` pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="b1b71-150">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="b1b71-151">[Required] – atribut</span><span class="sxs-lookup"><span data-stu-id="b1b71-151">[Required] attribute</span></span>

<span data-ttu-id="b1b71-152">Ve výchozím nastavení systém ověřování zpracovává parametry bez hodnoty null nebo vlastnosti, jako by měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-152">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="b1b71-153">[Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , například `decimal` a `int`, nejsou null.</span><span class="sxs-lookup"><span data-stu-id="b1b71-153">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="b1b71-154">[Požadováno] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="b1b71-154">[Required] validation on the server</span></span>

<span data-ttu-id="b1b71-155">Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="b1b71-155">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="b1b71-156">Pole, které nesmí mít hodnotu null, je vždy platné a chybová zpráva atributu `[Required]` se nikdy nezobrazuje.</span><span class="sxs-lookup"><span data-stu-id="b1b71-156">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="b1b71-157">Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, takže se zobrazí chybová zpráva, například `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-157">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="b1b71-158">Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="b1b71-158">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="b1b71-159">Převést pole na hodnotu null (například `decimal?` místo `decimal`).</span><span class="sxs-lookup"><span data-stu-id="b1b71-159">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="b1b71-160">Typy hodnot s [povolenou hodnotou null\<t >](/dotnet/csharp/programming-guide/nullable-types/) se považují za standardní typy Nullable.</span><span class="sxs-lookup"><span data-stu-id="b1b71-160">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="b1b71-161">Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-161">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="b1b71-162">Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="b1b71-162">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="b1b71-163">[Požadováno] ověřování na klientovi</span><span class="sxs-lookup"><span data-stu-id="b1b71-163">[Required] validation on the client</span></span>

<span data-ttu-id="b1b71-164">Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi.</span><span class="sxs-lookup"><span data-stu-id="b1b71-164">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="b1b71-165">Na klientovi:</span><span class="sxs-lookup"><span data-stu-id="b1b71-165">On the client:</span></span>

* <span data-ttu-id="b1b71-166">Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup.</span><span class="sxs-lookup"><span data-stu-id="b1b71-166">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="b1b71-167">Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="b1b71-167">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="b1b71-168">Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením.</span><span class="sxs-lookup"><span data-stu-id="b1b71-168">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="b1b71-169">Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.</span><span class="sxs-lookup"><span data-stu-id="b1b71-169">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="b1b71-170">Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za, ale měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-170">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="b1b71-171">To znamená, že získáte ověřování na straně klienta i v případě, že nepoužijete atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-171">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="b1b71-172">Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="b1b71-172">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="b1b71-173">Chcete-li zadat vlastní chybovou zprávu, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="b1b71-173">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="b1b71-174">[Remote] – atribut</span><span class="sxs-lookup"><span data-stu-id="b1b71-174">[Remote] attribute</span></span>

<span data-ttu-id="b1b71-175">Atribut `[Remote]` implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný.</span><span class="sxs-lookup"><span data-stu-id="b1b71-175">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="b1b71-176">Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.</span><span class="sxs-lookup"><span data-stu-id="b1b71-176">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="b1b71-177">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="b1b71-177">To implement remote validation:</span></span>

1. <span data-ttu-id="b1b71-178">Vytvořte metodu Action pro volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-178">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="b1b71-179">Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="b1b71-179">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="b1b71-180">`true` znamená, že jsou vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="b1b71-180">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="b1b71-181">`false`, `undefined`nebo `null` znamená, že vstup není platný.</span><span class="sxs-lookup"><span data-stu-id="b1b71-181">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="b1b71-182">Zobrazí výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-182">Display the default error message.</span></span>
   * <span data-ttu-id="b1b71-183">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="b1b71-183">Any other string means the input is invalid.</span></span> <span data-ttu-id="b1b71-184">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-184">Display the string as a custom error message.</span></span>

   <span data-ttu-id="b1b71-185">Tady je příklad metody akce, která vrací vlastní chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-185">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="b1b71-186">Ve třídě modelu poznámku k vlastnosti s atributem `[Remote]`, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-186">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="b1b71-187">Atribut `[Remote]` je v oboru názvů `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-187">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="b1b71-188">Další pole</span><span class="sxs-lookup"><span data-stu-id="b1b71-188">Additional fields</span></span>

<span data-ttu-id="b1b71-189">Vlastnost `AdditionalFields` atributu `[Remote]` umožňuje ověřit kombinace polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="b1b71-189">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="b1b71-190">Pokud například `User` model obsahoval `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají tento pár názvů.</span><span class="sxs-lookup"><span data-stu-id="b1b71-190">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="b1b71-191">Následující příklad ukazuje, jak použít `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="b1b71-191">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="b1b71-192">`AdditionalFields` lze nastavit explicitně na řetězce `"FirstName"` a `"LastName"`, ale použití operátoru [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) zjednodušuje pozdější refaktoring.</span><span class="sxs-lookup"><span data-stu-id="b1b71-192">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="b1b71-193">Metoda Action pro toto ověření musí přijmout argumenty `firstName` i `lastName`:</span><span class="sxs-lookup"><span data-stu-id="b1b71-193">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="b1b71-194">Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.</span><span class="sxs-lookup"><span data-stu-id="b1b71-194">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="b1b71-195">Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="b1b71-195">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="b1b71-196">Chcete-li například přidat vlastnost `MiddleName` do modelu, nastavte atribut `[Remote]`, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-196">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="b1b71-197">`AdditionalFields`, jako jsou všechny argumenty atributu, musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="b1b71-197">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="b1b71-198">Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-198">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="b1b71-199">Alternativy k předdefinovaným atributům</span><span class="sxs-lookup"><span data-stu-id="b1b71-199">Alternatives to built-in attributes</span></span>

<span data-ttu-id="b1b71-200">Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:</span><span class="sxs-lookup"><span data-stu-id="b1b71-200">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="b1b71-201">[Vytvořte vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="b1b71-201">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="b1b71-202">[Implementujte IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="b1b71-202">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="b1b71-203">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="b1b71-203">Custom attributes</span></span>

<span data-ttu-id="b1b71-204">U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="b1b71-204">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="b1b71-205">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>a přepište metodu <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="b1b71-205">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="b1b71-206">Metoda `IsValid` přijímá objekt s názvem *hodnota*, který je vstupem k ověření.</span><span class="sxs-lookup"><span data-stu-id="b1b71-206">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="b1b71-207">Přetížení také přijímá objekt `ValidationContext`, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-207">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="b1b71-208">Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce.</span><span class="sxs-lookup"><span data-stu-id="b1b71-208">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="b1b71-209">Atribut `[ClassicMovie]`:</span><span class="sxs-lookup"><span data-stu-id="b1b71-209">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="b1b71-210">Se spouští jenom na serveru.</span><span class="sxs-lookup"><span data-stu-id="b1b71-210">Is only run on the server.</span></span>
* <span data-ttu-id="b1b71-211">U klasických filmů ověří datum vydání:</span><span class="sxs-lookup"><span data-stu-id="b1b71-211">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="b1b71-212">Proměnná `movie` v předchozím příkladu představuje objekt `Movie`, který obsahuje data z odesílání formuláře.</span><span class="sxs-lookup"><span data-stu-id="b1b71-212">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="b1b71-213">Pokud se ověření nepovede, vrátí se `ValidationResult` s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="b1b71-213">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="b1b71-214">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="b1b71-214">IValidatableObject</span></span>

<span data-ttu-id="b1b71-215">Předchozí příklad funguje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="b1b71-215">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="b1b71-216">Další možností ověřování na úrovni třídy je implementace `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-216">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="b1b71-217">Ověřování uzlu nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="b1b71-217">Top-level node validation</span></span>

<span data-ttu-id="b1b71-218">Uzly nejvyšší úrovně zahrnují:</span><span class="sxs-lookup"><span data-stu-id="b1b71-218">Top-level nodes include:</span></span>

* <span data-ttu-id="b1b71-219">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="b1b71-219">Action parameters</span></span>
* <span data-ttu-id="b1b71-220">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="b1b71-220">Controller properties</span></span>
* <span data-ttu-id="b1b71-221">Parametry obslužné rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="b1b71-221">Page handler parameters</span></span>
* <span data-ttu-id="b1b71-222">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="b1b71-222">Page model properties</span></span>

<span data-ttu-id="b1b71-223">Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem.</span><span class="sxs-lookup"><span data-stu-id="b1b71-223">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="b1b71-224">V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření parametru `phone` akce:</span><span class="sxs-lookup"><span data-stu-id="b1b71-224">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="b1b71-225">Uzly nejvyšší úrovně mohou použít <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování.</span><span class="sxs-lookup"><span data-stu-id="b1b71-225">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="b1b71-226">V následujícím příkladu z ukázkové aplikace určuje metoda `CheckAge`, že parametr `age` musí být při odeslání formuláře svázán z řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-226">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="b1b71-227">Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře.</span><span class="sxs-lookup"><span data-stu-id="b1b71-227">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="b1b71-228">První formulář odešle hodnotu `Age` `99` jako parametr řetězce dotazu: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-228">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="b1b71-229">Když se odešle správně formátovaný `age` parametr z řetězce dotazu, formulář se ověří.</span><span class="sxs-lookup"><span data-stu-id="b1b71-229">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="b1b71-230">Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede.</span><span class="sxs-lookup"><span data-stu-id="b1b71-230">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="b1b71-231">Vazba se nezdařila, protože parametr `age` musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-231">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="b1b71-232">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="b1b71-232">Maximum errors</span></span>

<span data-ttu-id="b1b71-233">Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200).</span><span class="sxs-lookup"><span data-stu-id="b1b71-233">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="b1b71-234">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b1b71-234">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="b1b71-235">Maximální rekurze</span><span class="sxs-lookup"><span data-stu-id="b1b71-235">Maximum recursion</span></span>

<span data-ttu-id="b1b71-236"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> projde grafem objektu ověřovaného modelu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-236"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="b1b71-237">U modelů, které jsou hluboko nebo jsou nekonečně rekurzivní, může ověřování způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="b1b71-237">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="b1b71-238">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku.</span><span class="sxs-lookup"><span data-stu-id="b1b71-238">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="b1b71-239">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 32.</span><span class="sxs-lookup"><span data-stu-id="b1b71-239">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="b1b71-240">Automatické krátké okruhy</span><span class="sxs-lookup"><span data-stu-id="b1b71-240">Automatic short-circuit</span></span>

<span data-ttu-id="b1b71-241">Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="b1b71-241">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="b1b71-242">Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních objektů (například `byte[]`, `string[]`, `Dictionary<string, string>`) a složitých grafů objektů, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="b1b71-242">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="b1b71-243">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="b1b71-243">Disable validation</span></span>

<span data-ttu-id="b1b71-244">Zakázání ověřování:</span><span class="sxs-lookup"><span data-stu-id="b1b71-244">To disable validation:</span></span>

1. <span data-ttu-id="b1b71-245">Vytvořte implementaci `IObjectModelValidator`, která neoznačí žádná pole jako neplatnou.</span><span class="sxs-lookup"><span data-stu-id="b1b71-245">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="b1b71-246">Přidejte následující kód, který `Startup.ConfigureServices` k nahrazení výchozí implementace `IObjectModelValidator` v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="b1b71-246">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="b1b71-247">Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-247">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="b1b71-248">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b1b71-248">Client-side validation</span></span>

<span data-ttu-id="b1b71-249">Ověřování na straně klienta brání odeslání, dokud není formulář platný.</span><span class="sxs-lookup"><span data-stu-id="b1b71-249">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="b1b71-250">Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="b1b71-250">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="b1b71-251">Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="b1b71-251">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="b1b71-252">Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="b1b71-252">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="b1b71-253">Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="b1b71-253">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="b1b71-254">Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="b1b71-254">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="b1b71-255">Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu pro vykreslení HTML 5 `data-` atributů pro prvky formuláře, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="b1b71-255">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="b1b71-256">jQuery nenáročné ověřování analyzuje atributy `data-` a předá logiku do příkazu jQuery Validate a efektivně kopíruje do klienta logiku ověřování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="b1b71-256">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="b1b71-257">Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="b1b71-257">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="b1b71-258">Předchozí pomocník značek vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="b1b71-258">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="b1b71-259">Všimněte si, že atributy `data-` ve výstupu HTML odpovídají atributům ověřování pro vlastnost `Movie.ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-259">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="b1b71-260">Atribut `data-val-required` obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání.</span><span class="sxs-lookup"><span data-stu-id="b1b71-260">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="b1b71-261">jQuery unpassing předá tuto hodnotu do metody jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodné **\<> elementu span** .</span><span class="sxs-lookup"><span data-stu-id="b1b71-261">jQuery Unobtrusive Validation passes this value to the jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="b1b71-262">Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána atributem `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-262">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="b1b71-263">Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček.</span><span class="sxs-lookup"><span data-stu-id="b1b71-263">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="b1b71-264">`[DataType]` atributů a podtřídách, jako je `[EmailAddress]`, vám umožní zadat chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-264">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="b1b71-265">Přidání ověřování do dynamických formulářů</span><span class="sxs-lookup"><span data-stu-id="b1b71-265">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="b1b71-266">jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují.</span><span class="sxs-lookup"><span data-stu-id="b1b71-266">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="b1b71-267">Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích.</span><span class="sxs-lookup"><span data-stu-id="b1b71-267">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="b1b71-268">Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="b1b71-268">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="b1b71-269">Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.</span><span class="sxs-lookup"><span data-stu-id="b1b71-269">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="b1b71-270">Metoda `$.validator.unobtrusive.parse()` přijímá selektor jQuery pro svůj jeden argument.</span><span class="sxs-lookup"><span data-stu-id="b1b71-270">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="b1b71-271">Tato metoda oznamuje nenáročné ověřování, aby bylo možné analyzovat `data-` atributů formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="b1b71-271">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="b1b71-272">Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="b1b71-272">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="b1b71-273">Přidání ověřování do dynamických ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="b1b71-273">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="b1b71-274">Metoda `$.validator.unobtrusive.parse()` pracuje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, jako je `<input>` a `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-274">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="b1b71-275">Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-275">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="b1b71-276">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b1b71-276">Custom client-side validation</span></span>

<span data-ttu-id="b1b71-277">Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery.</span><span class="sxs-lookup"><span data-stu-id="b1b71-277">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="b1b71-278">Následující vzorový kód adaptéru byl napsán pro `[ClassicMovie]` a `[ClassicMovieWithClientValidator]` atributy, které byly představeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="b1b71-278">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="b1b71-279">Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="b1b71-279">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="b1b71-280">Použití adaptéru pro dané pole je aktivováno `data-` atributy, které:</span><span class="sxs-lookup"><span data-stu-id="b1b71-280">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="b1b71-281">Označte pole jako podléhající ověřování (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="b1b71-281">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="b1b71-282">Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="b1b71-282">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="b1b71-283">Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-param1="value"`).</span><span class="sxs-lookup"><span data-stu-id="b1b71-283">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="b1b71-284">Následující příklad ukazuje atributy `data-` pro atribut `ClassicMovie` ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="b1b71-284">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="b1b71-285">Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) využívají informace z atributů ověřování k vykreslování atributů `data-`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-285">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="b1b71-286">Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních atributů `data-` HTML:</span><span class="sxs-lookup"><span data-stu-id="b1b71-286">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="b1b71-287">Vytvořte třídu, která je odvozena z `AttributeAdapterBase<TAttribute>` a třídu, která implementuje `IValidationAttributeAdapterProvider`a zaregistrujte svůj atribut a jeho adaptér v DI.</span><span class="sxs-lookup"><span data-stu-id="b1b71-287">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="b1b71-288">Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách.</span><span class="sxs-lookup"><span data-stu-id="b1b71-288">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="b1b71-289">Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.</span><span class="sxs-lookup"><span data-stu-id="b1b71-289">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="b1b71-290">Implementujte `IClientModelValidator` ve vaší třídě `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-290">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="b1b71-291">Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.</span><span class="sxs-lookup"><span data-stu-id="b1b71-291">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="b1b71-292">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b1b71-292">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="b1b71-293">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovie` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b1b71-293">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="b1b71-294">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="b1b71-294">To add client validation by using this method:</span></span>

1. <span data-ttu-id="b1b71-295">Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut.</span><span class="sxs-lookup"><span data-stu-id="b1b71-295">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="b1b71-296">Odvodit třídu z [AttributeAdapterBase\<t >](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="b1b71-296">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="b1b71-297">Vytvořte `AddValidation` metodu, která přidá atributy `data-` do vykresleného výstupu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-297">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="b1b71-298">Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="b1b71-298">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="b1b71-299">V metodě `GetAttributeAdapter` předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-299">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="b1b71-300">Zaregistrujte poskytovatele adaptéru pro DI v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b1b71-300">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="b1b71-301">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b1b71-301">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="b1b71-302">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovieWithClientValidator` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b1b71-302">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="b1b71-303">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="b1b71-303">To add client validation by using this method:</span></span>

* <span data-ttu-id="b1b71-304">Ve vlastním ověřovacím atributu implementujte rozhraní `IClientModelValidator` a vytvořte metodu `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-304">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="b1b71-305">V metodě `AddValidation` přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-305">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="b1b71-306">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b1b71-306">Disable client-side validation</span></span>

<span data-ttu-id="b1b71-307">Následující kód zakáže ověřování klienta v Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="b1b71-307">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="b1b71-308">Další možnosti zakázání ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="b1b71-308">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="b1b71-309">Odkomentujte odkaz na `_ValidationScriptsPartial` ve všech souborech *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="b1b71-309">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="b1b71-310">Odeberte obsah souboru *Pages\Shared\_ValidationScriptsPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="b1b71-310">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="b1b71-311">Předchozí přístup nezabrání ověřování na straně klienta ASP.NET Core knihovně tříd identity Razor.</span><span class="sxs-lookup"><span data-stu-id="b1b71-311">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="b1b71-312">Další informace najdete v tématu <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="b1b71-312">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1b71-313">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="b1b71-313">Additional resources</span></span>

* [<span data-ttu-id="b1b71-314">Obor názvů System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="b1b71-314">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="b1b71-315">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="b1b71-315">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b1b71-316">Tento článek vysvětluje, jak ověřit vstup uživatele ve ASP.NET Core MVC nebo v aplikaci Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="b1b71-316">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="b1b71-317">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b1b71-317">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="b1b71-318">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="b1b71-318">Model state</span></span>

<span data-ttu-id="b1b71-319">Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-319">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="b1b71-320">Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat (například "x" je zadáno v poli, které očekává celé číslo).</span><span class="sxs-lookup"><span data-stu-id="b1b71-320">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="b1b71-321">K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům (například hodnota 0 je zadána v poli, které očekává hodnocení mezi 1 a 5).</span><span class="sxs-lookup"><span data-stu-id="b1b71-321">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="b1b71-322">Před provedením akce kontroleru nebo obslužné rutiny Razor Pages se vyskytuje jak vazba modelů, tak ověřování.</span><span class="sxs-lookup"><span data-stu-id="b1b71-322">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="b1b71-323">U webových aplikací je zodpovědností aplikace na kontrolu `ModelState.IsValid` a odpovídajícím způsobem reagovat.</span><span class="sxs-lookup"><span data-stu-id="b1b71-323">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="b1b71-324">Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="b1b71-324">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="b1b71-325">Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid`, pokud mají `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="b1b71-325">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="b1b71-326">V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o chybě, pokud stav modelu není platný.</span><span class="sxs-lookup"><span data-stu-id="b1b71-326">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="b1b71-327">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="b1b71-327">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="b1b71-328">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="b1b71-328">Rerun validation</span></span>

<span data-ttu-id="b1b71-329">Ověřování je automatické, ale můžete je chtít opakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="b1b71-329">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="b1b71-330">Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-330">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="b1b71-331">Chcete-li znovu spustit ověřování, zavolejte metodu `TryValidateModel`, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="b1b71-331">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="b1b71-332">Atributy ověřování</span><span class="sxs-lookup"><span data-stu-id="b1b71-332">Validation attributes</span></span>

<span data-ttu-id="b1b71-333">Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-333">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="b1b71-334">Následující příklad z [ukázkové aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) zobrazuje třídu modelu s poznámkou ověřování atributů.</span><span class="sxs-lookup"><span data-stu-id="b1b71-334">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="b1b71-335">Atribut `[ClassicMovie]` je vlastní ověřovací atribut a jsou integrovány i ostatní.</span><span class="sxs-lookup"><span data-stu-id="b1b71-335">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="b1b71-336">Nezobrazuje se `[ClassicMovie2]`, který ukazuje alternativní způsob implementace vlastního atributu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-336">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="b1b71-337">Předdefinované atributy</span><span class="sxs-lookup"><span data-stu-id="b1b71-337">Built-in attributes</span></span>

<span data-ttu-id="b1b71-338">Mezi předdefinované atributy ověřování patří:</span><span class="sxs-lookup"><span data-stu-id="b1b71-338">Built-in validation attributes include:</span></span>

* <span data-ttu-id="b1b71-339">`[CreditCard]`: ověřuje, zda má vlastnost formát kreditní karty.</span><span class="sxs-lookup"><span data-stu-id="b1b71-339">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="b1b71-340">`[Compare]`: ověří, že se dvě vlastnosti v modelu shodují.</span><span class="sxs-lookup"><span data-stu-id="b1b71-340">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="b1b71-341">Například soubor *Register.cshtml.cs* používá `[Compare]` k ověření, že se dvě zadaná hesla shodují.</span><span class="sxs-lookup"><span data-stu-id="b1b71-341">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="b1b71-342">[Identita uživatelského rozhraní](xref:security/authentication/scaffold-identity) pro zobrazení kódu registrace.</span><span class="sxs-lookup"><span data-stu-id="b1b71-342">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="b1b71-343">`[EmailAddress]`: ověřuje, zda má vlastnost formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-343">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="b1b71-344">`[Phone]`: ověřuje, zda má vlastnost formát telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="b1b71-344">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="b1b71-345">`[Range]`: ověří, že hodnota vlastnosti spadá do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-345">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="b1b71-346">`[RegularExpression]`: ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-346">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="b1b71-347">`[Required]`: ověří, že pole nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="b1b71-347">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="b1b71-348">Podrobnosti o chování tohoto atributu naleznete v [atributu [required]](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="b1b71-348">See [[Required] attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="b1b71-349">`[StringLength]`: ověří, že hodnota řetězcové vlastnosti nepřekračuje zadané omezení délky.</span><span class="sxs-lookup"><span data-stu-id="b1b71-349">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="b1b71-350">`[Url]`: ověřuje, zda má vlastnost formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="b1b71-350">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="b1b71-351">`[Remote]`: ověřuje vstup na straně klienta voláním metody Action na serveru.</span><span class="sxs-lookup"><span data-stu-id="b1b71-351">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="b1b71-352">Podrobnosti o chování tohoto atributu naleznete v [atributu [Remote]](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="b1b71-352">See [[Remote] attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="b1b71-353">Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="b1b71-353">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="b1b71-354">Chybové zprávy</span><span class="sxs-lookup"><span data-stu-id="b1b71-354">Error messages</span></span>

<span data-ttu-id="b1b71-355">Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="b1b71-355">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="b1b71-356">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b1b71-356">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="b1b71-357">Interně atributy volají `String.Format` zástupný symbol pro název pole a někdy další zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="b1b71-357">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="b1b71-358">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b1b71-358">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="b1b71-359">Při použití na vlastnost `Name` by byla chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."</span><span class="sxs-lookup"><span data-stu-id="b1b71-359">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="b1b71-360">Chcete-li zjistit, které parametry jsou předány `String.Format` pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="b1b71-360">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="b1b71-361">[Required] – atribut</span><span class="sxs-lookup"><span data-stu-id="b1b71-361">[Required] attribute</span></span>

<span data-ttu-id="b1b71-362">Ve výchozím nastavení systém ověřování zpracovává parametry bez hodnoty null nebo vlastnosti, jako by měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-362">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="b1b71-363">[Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , například `decimal` a `int`, nejsou null.</span><span class="sxs-lookup"><span data-stu-id="b1b71-363">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="b1b71-364">[Požadováno] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="b1b71-364">[Required] validation on the server</span></span>

<span data-ttu-id="b1b71-365">Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="b1b71-365">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="b1b71-366">Pole, které nesmí mít hodnotu null, je vždy platné a chybová zpráva [required] se nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="b1b71-366">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="b1b71-367">Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, takže se zobrazí chybová zpráva, například `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-367">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="b1b71-368">Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="b1b71-368">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="b1b71-369">Převést pole na hodnotu null (například `decimal?` místo `decimal`).</span><span class="sxs-lookup"><span data-stu-id="b1b71-369">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="b1b71-370">Typy hodnot s [povolenou hodnotou null\<t >](/dotnet/csharp/programming-guide/nullable-types/) se považují za standardní typy Nullable.</span><span class="sxs-lookup"><span data-stu-id="b1b71-370">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="b1b71-371">Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-371">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="b1b71-372">Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="b1b71-372">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="b1b71-373">[Požadováno] ověřování na klientovi</span><span class="sxs-lookup"><span data-stu-id="b1b71-373">[Required] validation on the client</span></span>

<span data-ttu-id="b1b71-374">Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi.</span><span class="sxs-lookup"><span data-stu-id="b1b71-374">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="b1b71-375">Na klientovi:</span><span class="sxs-lookup"><span data-stu-id="b1b71-375">On the client:</span></span>

* <span data-ttu-id="b1b71-376">Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup.</span><span class="sxs-lookup"><span data-stu-id="b1b71-376">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="b1b71-377">Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="b1b71-377">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="b1b71-378">Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením.</span><span class="sxs-lookup"><span data-stu-id="b1b71-378">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="b1b71-379">Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.</span><span class="sxs-lookup"><span data-stu-id="b1b71-379">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="b1b71-380">Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za, ale měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-380">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="b1b71-381">To znamená, že získáte ověřování na straně klienta i v případě, že nepoužijete atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-381">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="b1b71-382">Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="b1b71-382">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="b1b71-383">Chcete-li zadat vlastní chybovou zprávu, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="b1b71-383">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="b1b71-384">[Remote] – atribut</span><span class="sxs-lookup"><span data-stu-id="b1b71-384">[Remote] attribute</span></span>

<span data-ttu-id="b1b71-385">Atribut `[Remote]` implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný.</span><span class="sxs-lookup"><span data-stu-id="b1b71-385">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="b1b71-386">Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.</span><span class="sxs-lookup"><span data-stu-id="b1b71-386">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="b1b71-387">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="b1b71-387">To implement remote validation:</span></span>

1. <span data-ttu-id="b1b71-388">Vytvořte metodu Action pro volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-388">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="b1b71-389">Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="b1b71-389">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="b1b71-390">`"true"` znamená, že jsou vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="b1b71-390">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="b1b71-391">`"false"`, `undefined`nebo `null` znamená, že vstup není platný.</span><span class="sxs-lookup"><span data-stu-id="b1b71-391">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="b1b71-392">Zobrazí výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-392">Display the default error message.</span></span>
   * <span data-ttu-id="b1b71-393">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="b1b71-393">Any other string means the input is invalid.</span></span> <span data-ttu-id="b1b71-394">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-394">Display the string as a custom error message.</span></span>

   <span data-ttu-id="b1b71-395">Tady je příklad metody akce, která vrací vlastní chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-395">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="b1b71-396">Ve třídě modelu poznámku k vlastnosti s atributem `[Remote]`, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-396">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="b1b71-397">Atribut `[Remote]` je v oboru názvů `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-397">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="b1b71-398">Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) , pokud nepoužíváte `Microsoft.AspNetCore.App` nebo `Microsoft.AspNetCore.All` Metapackage.</span><span class="sxs-lookup"><span data-stu-id="b1b71-398">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="b1b71-399">Další pole</span><span class="sxs-lookup"><span data-stu-id="b1b71-399">Additional fields</span></span>

<span data-ttu-id="b1b71-400">Vlastnost `AdditionalFields` atributu `[Remote]` umožňuje ověřit kombinace polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="b1b71-400">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="b1b71-401">Pokud například `User` model obsahoval `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají tento pár názvů.</span><span class="sxs-lookup"><span data-stu-id="b1b71-401">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="b1b71-402">Následující příklad ukazuje, jak použít `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="b1b71-402">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="b1b71-403">`AdditionalFields` lze nastavit explicitně na řetězce `"FirstName"` a `"LastName"`, ale použití operátoru [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) zjednodušuje pozdější refaktoring.</span><span class="sxs-lookup"><span data-stu-id="b1b71-403">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="b1b71-404">Metoda Action pro toto ověření musí přijmout argumenty jméno a příjmení:</span><span class="sxs-lookup"><span data-stu-id="b1b71-404">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="b1b71-405">Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.</span><span class="sxs-lookup"><span data-stu-id="b1b71-405">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="b1b71-406">Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="b1b71-406">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="b1b71-407">Chcete-li například přidat vlastnost `MiddleName` do modelu, nastavte atribut `[Remote]`, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-407">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="b1b71-408">`AdditionalFields`, jako jsou všechny argumenty atributu, musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="b1b71-408">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="b1b71-409">Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-409">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="b1b71-410">Alternativy k předdefinovaným atributům</span><span class="sxs-lookup"><span data-stu-id="b1b71-410">Alternatives to built-in attributes</span></span>

<span data-ttu-id="b1b71-411">Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:</span><span class="sxs-lookup"><span data-stu-id="b1b71-411">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="b1b71-412">[Vytvořte vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="b1b71-412">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="b1b71-413">[Implementujte IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="b1b71-413">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="b1b71-414">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="b1b71-414">Custom attributes</span></span>

<span data-ttu-id="b1b71-415">U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="b1b71-415">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="b1b71-416">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>a přepište metodu <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="b1b71-416">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="b1b71-417">Metoda `IsValid` přijímá objekt s názvem *hodnota*, který je vstupem k ověření.</span><span class="sxs-lookup"><span data-stu-id="b1b71-417">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="b1b71-418">Přetížení také přijímá objekt `ValidationContext`, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-418">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="b1b71-419">Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce.</span><span class="sxs-lookup"><span data-stu-id="b1b71-419">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="b1b71-420">Atribut `[ClassicMovie2]` nejprve zkontroluje Žánr a pokračuje pouze v případě, že je *klasický*.</span><span class="sxs-lookup"><span data-stu-id="b1b71-420">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="b1b71-421">U filmů identifikovaných jako klasických kontroluje datum vydání, aby se zajistilo, že není pozdější než limit předaný konstruktoru atributu.)</span><span class="sxs-lookup"><span data-stu-id="b1b71-421">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="b1b71-422">Proměnná `movie` v předchozím příkladu představuje objekt `Movie`, který obsahuje data z odesílání formuláře.</span><span class="sxs-lookup"><span data-stu-id="b1b71-422">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="b1b71-423">Metoda `IsValid` kontroluje datum a Žánr.</span><span class="sxs-lookup"><span data-stu-id="b1b71-423">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="b1b71-424">Po úspěšném ověření `IsValid` vrátí kód `ValidationResult.Success`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-424">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="b1b71-425">Pokud se ověření nepovede, vrátí se `ValidationResult` s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="b1b71-425">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="b1b71-426">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="b1b71-426">IValidatableObject</span></span>

<span data-ttu-id="b1b71-427">Předchozí příklad funguje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="b1b71-427">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="b1b71-428">Další možností ověřování na úrovni třídy je implementace `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-428">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="b1b71-429">Ověřování uzlu nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="b1b71-429">Top-level node validation</span></span>

<span data-ttu-id="b1b71-430">Uzly nejvyšší úrovně zahrnují:</span><span class="sxs-lookup"><span data-stu-id="b1b71-430">Top-level nodes include:</span></span>

* <span data-ttu-id="b1b71-431">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="b1b71-431">Action parameters</span></span>
* <span data-ttu-id="b1b71-432">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="b1b71-432">Controller properties</span></span>
* <span data-ttu-id="b1b71-433">Parametry obslužné rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="b1b71-433">Page handler parameters</span></span>
* <span data-ttu-id="b1b71-434">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="b1b71-434">Page model properties</span></span>

<span data-ttu-id="b1b71-435">Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem.</span><span class="sxs-lookup"><span data-stu-id="b1b71-435">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="b1b71-436">V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření parametru `phone` akce:</span><span class="sxs-lookup"><span data-stu-id="b1b71-436">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="b1b71-437">Uzly nejvyšší úrovně mohou použít <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování.</span><span class="sxs-lookup"><span data-stu-id="b1b71-437">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="b1b71-438">V následujícím příkladu z ukázkové aplikace určuje metoda `CheckAge`, že parametr `age` musí být při odeslání formuláře svázán z řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-438">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="b1b71-439">Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře.</span><span class="sxs-lookup"><span data-stu-id="b1b71-439">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="b1b71-440">První formulář odešle hodnotu `Age` `99` jako řetězec dotazu: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-440">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="b1b71-441">Když se odešle správně formátovaný `age` parametr z řetězce dotazu, formulář se ověří.</span><span class="sxs-lookup"><span data-stu-id="b1b71-441">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="b1b71-442">Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede.</span><span class="sxs-lookup"><span data-stu-id="b1b71-442">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="b1b71-443">Vazba se nezdařila, protože parametr `age` musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-443">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="b1b71-444">Při spuštění s `CompatibilityVersion.Version_2_1` nebo novějším je ve výchozím nastavení povoleno ověřování uzlů nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="b1b71-444">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="b1b71-445">V opačném případě je ověřování uzlu nejvyšší úrovně zakázané.</span><span class="sxs-lookup"><span data-stu-id="b1b71-445">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="b1b71-446">Výchozí možnost může být přepsána nastavením vlastnosti <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> v (`Startup.ConfigureServices`), jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="b1b71-446">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="b1b71-447">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="b1b71-447">Maximum errors</span></span>

<span data-ttu-id="b1b71-448">Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200).</span><span class="sxs-lookup"><span data-stu-id="b1b71-448">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="b1b71-449">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b1b71-449">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="b1b71-450">Maximální rekurze</span><span class="sxs-lookup"><span data-stu-id="b1b71-450">Maximum recursion</span></span>

<span data-ttu-id="b1b71-451"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> projde grafem objektu ověřovaného modelu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-451"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="b1b71-452">U modelů, které jsou velmi hlubokoelné nebo nekonečné rekurzivní, může ověřování způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="b1b71-452">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="b1b71-453">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku.</span><span class="sxs-lookup"><span data-stu-id="b1b71-453">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="b1b71-454">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 32 při spuštění s `CompatibilityVersion.Version_2_2` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="b1b71-454">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="b1b71-455">Pro starší verze je hodnota null, což znamená bez omezení hloubky.</span><span class="sxs-lookup"><span data-stu-id="b1b71-455">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="b1b71-456">Automatické krátké okruhy</span><span class="sxs-lookup"><span data-stu-id="b1b71-456">Automatic short-circuit</span></span>

<span data-ttu-id="b1b71-457">Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="b1b71-457">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="b1b71-458">Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních objektů (například `byte[]`, `string[]`, `Dictionary<string, string>`) a složitých grafů objektů, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="b1b71-458">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="b1b71-459">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="b1b71-459">Disable validation</span></span>

<span data-ttu-id="b1b71-460">Zakázání ověřování:</span><span class="sxs-lookup"><span data-stu-id="b1b71-460">To disable validation:</span></span>

1. <span data-ttu-id="b1b71-461">Vytvořte implementaci `IObjectModelValidator`, která neoznačí žádná pole jako neplatnou.</span><span class="sxs-lookup"><span data-stu-id="b1b71-461">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="b1b71-462">Přidejte následující kód, který `Startup.ConfigureServices` k nahrazení výchozí implementace `IObjectModelValidator` v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="b1b71-462">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="b1b71-463">Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-463">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="b1b71-464">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b1b71-464">Client-side validation</span></span>

<span data-ttu-id="b1b71-465">Ověřování na straně klienta brání odeslání, dokud není formulář platný.</span><span class="sxs-lookup"><span data-stu-id="b1b71-465">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="b1b71-466">Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="b1b71-466">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="b1b71-467">Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="b1b71-467">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="b1b71-468">Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="b1b71-468">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="b1b71-469">Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="b1b71-469">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="b1b71-470">Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="b1b71-470">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="b1b71-471">Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu pro vykreslení HTML 5 `data-` atributů pro prvky formuláře, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="b1b71-471">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="b1b71-472">jQuery nenáročné ověřování analyzuje atributy `data-` a předá logiku do příkazu jQuery Validate a efektivně kopíruje do klienta logiku ověřování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="b1b71-472">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="b1b71-473">Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="b1b71-473">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="b1b71-474">Předchozí pomocník značek vykresluje následující kód HTML.</span><span class="sxs-lookup"><span data-stu-id="b1b71-474">The preceding tag helpers render the following HTML.</span></span>

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

<span data-ttu-id="b1b71-475">Všimněte si, že atributy `data-` ve výstupu HTML odpovídají atributům ověřování pro vlastnost `ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-475">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="b1b71-476">Atribut `data-val-required` obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání.</span><span class="sxs-lookup"><span data-stu-id="b1b71-476">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="b1b71-477">jQuery unpassing předá tuto hodnotu do metody jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodné **\<> elementu span** .</span><span class="sxs-lookup"><span data-stu-id="b1b71-477">jQuery Unobtrusive Validation passes this value to the jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="b1b71-478">Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána atributem `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-478">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="b1b71-479">Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček.</span><span class="sxs-lookup"><span data-stu-id="b1b71-479">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="b1b71-480">`[DataType]` atributů a podtřídách, jako je `[EmailAddress]`, vám umožní zadat chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="b1b71-480">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="b1b71-481">Přidání ověřování do dynamických formulářů</span><span class="sxs-lookup"><span data-stu-id="b1b71-481">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="b1b71-482">jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují.</span><span class="sxs-lookup"><span data-stu-id="b1b71-482">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="b1b71-483">Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích.</span><span class="sxs-lookup"><span data-stu-id="b1b71-483">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="b1b71-484">Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="b1b71-484">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="b1b71-485">Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.</span><span class="sxs-lookup"><span data-stu-id="b1b71-485">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="b1b71-486">Metoda `$.validator.unobtrusive.parse()` přijímá selektor jQuery pro svůj jeden argument.</span><span class="sxs-lookup"><span data-stu-id="b1b71-486">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="b1b71-487">Tato metoda oznamuje nenáročné ověřování, aby bylo možné analyzovat `data-` atributů formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="b1b71-487">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="b1b71-488">Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="b1b71-488">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="b1b71-489">Přidání ověřování do dynamických ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="b1b71-489">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="b1b71-490">Metoda `$.validator.unobtrusive.parse()` pracuje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, jako je `<input>` a `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-490">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="b1b71-491">Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-491">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="b1b71-492">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b1b71-492">Custom client-side validation</span></span>

<span data-ttu-id="b1b71-493">Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery.</span><span class="sxs-lookup"><span data-stu-id="b1b71-493">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="b1b71-494">Následující vzorový kód adaptéru byl napsán pro `ClassicMovie` a `ClassicMovie2` atributy, které byly představeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="b1b71-494">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="b1b71-495">Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="b1b71-495">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="b1b71-496">Použití adaptéru pro dané pole je aktivováno `data-` atributy, které:</span><span class="sxs-lookup"><span data-stu-id="b1b71-496">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="b1b71-497">Označte pole jako podléhající ověřování (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="b1b71-497">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="b1b71-498">Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="b1b71-498">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="b1b71-499">Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="b1b71-499">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="b1b71-500">Následující příklad ukazuje atributy `data-` pro atribut `ClassicMovie` ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="b1b71-500">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="b1b71-501">Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) využívají informace z atributů ověřování k vykreslování atributů `data-`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-501">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="b1b71-502">Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních atributů `data-` HTML:</span><span class="sxs-lookup"><span data-stu-id="b1b71-502">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="b1b71-503">Vytvořte třídu, která je odvozena z `AttributeAdapterBase<TAttribute>` a třídu, která implementuje `IValidationAttributeAdapterProvider`a zaregistrujte svůj atribut a jeho adaptér v DI.</span><span class="sxs-lookup"><span data-stu-id="b1b71-503">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="b1b71-504">Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách.</span><span class="sxs-lookup"><span data-stu-id="b1b71-504">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="b1b71-505">Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.</span><span class="sxs-lookup"><span data-stu-id="b1b71-505">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="b1b71-506">Implementujte `IClientModelValidator` ve vaší třídě `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-506">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="b1b71-507">Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.</span><span class="sxs-lookup"><span data-stu-id="b1b71-507">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="b1b71-508">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b1b71-508">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="b1b71-509">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovie` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b1b71-509">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="b1b71-510">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="b1b71-510">To add client validation by using this method:</span></span>

1. <span data-ttu-id="b1b71-511">Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut.</span><span class="sxs-lookup"><span data-stu-id="b1b71-511">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="b1b71-512">Odvodit třídu z [AttributeAdapterBase\<t >](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="b1b71-512">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="b1b71-513">Vytvořte `AddValidation` metodu, která přidá atributy `data-` do vykresleného výstupu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-513">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="b1b71-514">Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="b1b71-514">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="b1b71-515">V metodě `GetAttributeAdapter` předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-515">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="b1b71-516">Zaregistrujte poskytovatele adaptéru pro DI v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b1b71-516">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="b1b71-517">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b1b71-517">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="b1b71-518">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovie2` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b1b71-518">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="b1b71-519">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="b1b71-519">To add client validation by using this method:</span></span>

* <span data-ttu-id="b1b71-520">Ve vlastním ověřovacím atributu implementujte rozhraní `IClientModelValidator` a vytvořte metodu `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="b1b71-520">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="b1b71-521">V metodě `AddValidation` přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b1b71-521">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="b1b71-522">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b1b71-522">Disable client-side validation</span></span>

<span data-ttu-id="b1b71-523">Následující kód zakáže ověřování klienta v zobrazeních MVC:</span><span class="sxs-lookup"><span data-stu-id="b1b71-523">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="b1b71-524">A v Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="b1b71-524">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="b1b71-525">Další možností pro vypnutí ověřování klienta je zadání komentáře k odkazu na `_ValidationScriptsPartial` v souboru *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="b1b71-525">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1b71-526">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="b1b71-526">Additional resources</span></span>

* [<span data-ttu-id="b1b71-527">Obor názvů System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="b1b71-527">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="b1b71-528">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="b1b71-528">Model Binding</span></span>](model-binding.md)

::: moniker-end

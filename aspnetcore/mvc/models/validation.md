---
title: Ověření modelu ve ASP.NET Core MVC
author: rick-anderson
description: Přečtěte si o ověřování modelu ve ASP.NET Core MVC a Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2019
uid: mvc/models/validation
ms.openlocfilehash: 8e9e588c8665962b2fe285b0feab977b16938154
ms.sourcegitcommit: 99cdd60a26ff0970880bb43c558d78b1ef17c237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2019
ms.locfileid: "73915530"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="86ad6-103">Ověřování modelu ve ASP.NET Core MVC a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="86ad6-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86ad6-104">Tento článek vysvětluje, jak ověřit vstup uživatele ve ASP.NET Core MVC nebo v aplikaci Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="86ad6-104">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="86ad6-105">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="86ad6-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="86ad6-106">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="86ad6-106">Model state</span></span>

<span data-ttu-id="86ad6-107">Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-107">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="86ad6-108">Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat (například "x" je zadáno v poli, které očekává celé číslo).</span><span class="sxs-lookup"><span data-stu-id="86ad6-108">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="86ad6-109">K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům (například hodnota 0 je zadána v poli, které očekává hodnocení mezi 1 a 5).</span><span class="sxs-lookup"><span data-stu-id="86ad6-109">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="86ad6-110">Před provedením akce kontroleru nebo obslužné rutiny Razor Pages se vyskytuje jak vazba modelů, tak ověřování.</span><span class="sxs-lookup"><span data-stu-id="86ad6-110">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="86ad6-111">U webových aplikací je zodpovědností aplikace na kontrolu `ModelState.IsValid` a odpovídajícím způsobem reagovat.</span><span class="sxs-lookup"><span data-stu-id="86ad6-111">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="86ad6-112">Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="86ad6-112">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="86ad6-113">Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid`, pokud mají `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="86ad6-113">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="86ad6-114">V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o problému, pokud stav modelu není platný.</span><span class="sxs-lookup"><span data-stu-id="86ad6-114">In that case, an automatic HTTP 400 response containing issue details is returned when model state is invalid.</span></span> <span data-ttu-id="86ad6-115">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="86ad6-115">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="86ad6-116">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="86ad6-116">Rerun validation</span></span>

<span data-ttu-id="86ad6-117">Ověřování je automatické, ale můžete je chtít opakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="86ad6-117">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="86ad6-118">Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-118">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="86ad6-119">Chcete-li znovu spustit ověřování, zavolejte metodu `TryValidateModel`, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="86ad6-119">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/sample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="86ad6-120">Atributy ověřování</span><span class="sxs-lookup"><span data-stu-id="86ad6-120">Validation attributes</span></span>

<span data-ttu-id="86ad6-121">Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-121">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="86ad6-122">Následující příklad z [ukázkové aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) zobrazuje třídu modelu s poznámkou ověřování atributů.</span><span class="sxs-lookup"><span data-stu-id="86ad6-122">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="86ad6-123">Atribut `[ClassicMovie]` je vlastní ověřovací atribut a jsou integrovány i ostatní.</span><span class="sxs-lookup"><span data-stu-id="86ad6-123">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="86ad6-124">(Nezobrazuje se `[ClassicMovie2]`, který ukazuje alternativní způsob implementace vlastního atributu.)</span><span class="sxs-lookup"><span data-stu-id="86ad6-124">(Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.)</span></span>

[!code-csharp[](validation/sample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="86ad6-125">Předdefinované atributy</span><span class="sxs-lookup"><span data-stu-id="86ad6-125">Built-in attributes</span></span>

<span data-ttu-id="86ad6-126">Tady jsou některé z vestavěných ověřovacích atributů:</span><span class="sxs-lookup"><span data-stu-id="86ad6-126">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="86ad6-127">`[CreditCard]`: ověřuje, zda má vlastnost formát kreditní karty.</span><span class="sxs-lookup"><span data-stu-id="86ad6-127">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="86ad6-128">`[Compare]`: ověří, že se dvě vlastnosti v modelu shodují.</span><span class="sxs-lookup"><span data-stu-id="86ad6-128">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="86ad6-129">`[EmailAddress]`: ověřuje, zda má vlastnost formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-129">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="86ad6-130">`[Phone]`: ověřuje, zda má vlastnost formát telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="86ad6-130">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="86ad6-131">`[Range]`: ověří, že hodnota vlastnosti spadá do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-131">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="86ad6-132">`[RegularExpression]`: ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-132">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="86ad6-133">`[Required]`: ověří, že pole nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="86ad6-133">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="86ad6-134">Podrobnosti o chování tohoto atributu naleznete v [atributu [required]](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="86ad6-134">See [[Required] attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="86ad6-135">`[StringLength]`: ověří, že hodnota řetězcové vlastnosti nepřekračuje zadané omezení délky.</span><span class="sxs-lookup"><span data-stu-id="86ad6-135">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="86ad6-136">`[Url]`: ověřuje, zda má vlastnost formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="86ad6-136">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="86ad6-137">`[Remote]`: ověřuje vstup na straně klienta voláním metody Action na serveru.</span><span class="sxs-lookup"><span data-stu-id="86ad6-137">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="86ad6-138">Podrobnosti o chování tohoto atributu naleznete v [atributu [Remote]](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="86ad6-138">See [[Remote] attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="86ad6-139">Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="86ad6-139">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="86ad6-140">Chybové zprávy</span><span class="sxs-lookup"><span data-stu-id="86ad6-140">Error messages</span></span>

<span data-ttu-id="86ad6-141">Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="86ad6-141">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="86ad6-142">Příklad:</span><span class="sxs-lookup"><span data-stu-id="86ad6-142">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="86ad6-143">Interně atributy volají `String.Format` zástupný symbol pro název pole a někdy další zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="86ad6-143">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="86ad6-144">Příklad:</span><span class="sxs-lookup"><span data-stu-id="86ad6-144">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="86ad6-145">Při použití na vlastnost `Name` by byla chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."</span><span class="sxs-lookup"><span data-stu-id="86ad6-145">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="86ad6-146">Chcete-li zjistit, které parametry jsou předány `String.Format` pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="86ad6-146">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="86ad6-147">[Required] – atribut</span><span class="sxs-lookup"><span data-stu-id="86ad6-147">[Required] attribute</span></span>

<span data-ttu-id="86ad6-148">Ve výchozím nastavení systém ověřování zpracovává parametry bez hodnoty null nebo vlastnosti, jako by měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-148">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="86ad6-149">[Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , například `decimal` a `int`, nejsou null.</span><span class="sxs-lookup"><span data-stu-id="86ad6-149">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="86ad6-150">[Požadováno] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="86ad6-150">[Required] validation on the server</span></span>

<span data-ttu-id="86ad6-151">Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="86ad6-151">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="86ad6-152">Pole, které nesmí mít hodnotu null, je vždy platné a chybová zpráva [required] se nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="86ad6-152">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="86ad6-153">Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, takže se zobrazí chybová zpráva, například `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-153">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="86ad6-154">Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="86ad6-154">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="86ad6-155">Převést pole na hodnotu null (například `decimal?` místo `decimal`).</span><span class="sxs-lookup"><span data-stu-id="86ad6-155">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="86ad6-156">Typy hodnot s [povolenou hodnotou null\<t >](/dotnet/csharp/programming-guide/nullable-types/) se považují za standardní typy Nullable.</span><span class="sxs-lookup"><span data-stu-id="86ad6-156">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="86ad6-157">Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-157">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="86ad6-158">Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="86ad6-158">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="86ad6-159">[Požadováno] ověřování na klientovi</span><span class="sxs-lookup"><span data-stu-id="86ad6-159">[Required] validation on the client</span></span>

<span data-ttu-id="86ad6-160">Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi.</span><span class="sxs-lookup"><span data-stu-id="86ad6-160">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="86ad6-161">Na klientovi:</span><span class="sxs-lookup"><span data-stu-id="86ad6-161">On the client:</span></span>

* <span data-ttu-id="86ad6-162">Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup.</span><span class="sxs-lookup"><span data-stu-id="86ad6-162">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="86ad6-163">Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="86ad6-163">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="86ad6-164">Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením.</span><span class="sxs-lookup"><span data-stu-id="86ad6-164">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="86ad6-165">Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.</span><span class="sxs-lookup"><span data-stu-id="86ad6-165">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="86ad6-166">Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za, ale měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-166">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="86ad6-167">To znamená, že získáte ověřování na straně klienta i v případě, že nepoužijete atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-167">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="86ad6-168">Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="86ad6-168">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="86ad6-169">Chcete-li zadat vlastní chybovou zprávu, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="86ad6-169">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="86ad6-170">[Remote] – atribut</span><span class="sxs-lookup"><span data-stu-id="86ad6-170">[Remote] attribute</span></span>

<span data-ttu-id="86ad6-171">Atribut `[Remote]` implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný.</span><span class="sxs-lookup"><span data-stu-id="86ad6-171">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="86ad6-172">Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.</span><span class="sxs-lookup"><span data-stu-id="86ad6-172">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="86ad6-173">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="86ad6-173">To implement remote validation:</span></span>

1. <span data-ttu-id="86ad6-174">Vytvořte metodu Action pro volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-174">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="86ad6-175">Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="86ad6-175">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="86ad6-176">`"true"` znamená, že jsou vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="86ad6-176">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="86ad6-177">`"false"`, `undefined`nebo `null` znamená, že vstup není platný.</span><span class="sxs-lookup"><span data-stu-id="86ad6-177">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="86ad6-178">Zobrazí výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-178">Display the default error message.</span></span>
   * <span data-ttu-id="86ad6-179">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="86ad6-179">Any other string means the input is invalid.</span></span> <span data-ttu-id="86ad6-180">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-180">Display the string as a custom error message.</span></span>

   <span data-ttu-id="86ad6-181">Tady je příklad metody akce, která vrací vlastní chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-181">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="86ad6-182">Ve třídě modelu poznámku k vlastnosti s atributem `[Remote]`, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-182">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="86ad6-183">Atribut `[Remote]` je v oboru názvů `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-183">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="86ad6-184">Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) , pokud nepoužíváte `Microsoft.AspNetCore.App` nebo `Microsoft.AspNetCore.All` Metapackage.</span><span class="sxs-lookup"><span data-stu-id="86ad6-184">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="86ad6-185">Další pole</span><span class="sxs-lookup"><span data-stu-id="86ad6-185">Additional fields</span></span>

<span data-ttu-id="86ad6-186">Vlastnost `AdditionalFields` atributu `[Remote]` umožňuje ověřit kombinace polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="86ad6-186">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="86ad6-187">Pokud například `User` model obsahoval `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají tento pár názvů.</span><span class="sxs-lookup"><span data-stu-id="86ad6-187">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="86ad6-188">Následující příklad ukazuje, jak použít `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="86ad6-188">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="86ad6-189">`AdditionalFields` lze nastavit explicitně na řetězce `"FirstName"` a `"LastName"`, ale použití operátoru [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) zjednodušuje pozdější refaktoring.</span><span class="sxs-lookup"><span data-stu-id="86ad6-189">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="86ad6-190">Metoda Action pro toto ověření musí přijmout argumenty jméno a příjmení:</span><span class="sxs-lookup"><span data-stu-id="86ad6-190">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="86ad6-191">Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.</span><span class="sxs-lookup"><span data-stu-id="86ad6-191">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="86ad6-192">Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="86ad6-192">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="86ad6-193">Chcete-li například přidat vlastnost `MiddleName` do modelu, nastavte atribut `[Remote]`, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-193">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="86ad6-194">`AdditionalFields`, jako jsou všechny argumenty atributu, musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="86ad6-194">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="86ad6-195">Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-195">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="86ad6-196">Alternativy k předdefinovaným atributům</span><span class="sxs-lookup"><span data-stu-id="86ad6-196">Alternatives to built-in attributes</span></span>

<span data-ttu-id="86ad6-197">Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:</span><span class="sxs-lookup"><span data-stu-id="86ad6-197">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="86ad6-198">[Vytvořte vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="86ad6-198">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="86ad6-199">[Implementujte IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="86ad6-199">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="86ad6-200">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="86ad6-200">Custom attributes</span></span>

<span data-ttu-id="86ad6-201">U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="86ad6-201">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="86ad6-202">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>a přepište metodu <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="86ad6-202">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="86ad6-203">Metoda `IsValid` přijímá objekt s názvem *hodnota*, který je vstupem k ověření.</span><span class="sxs-lookup"><span data-stu-id="86ad6-203">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="86ad6-204">Přetížení také přijímá objekt `ValidationContext`, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-204">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="86ad6-205">Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce.</span><span class="sxs-lookup"><span data-stu-id="86ad6-205">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="86ad6-206">Atribut `[ClassicMovie2]` nejprve zkontroluje Žánr a pokračuje pouze v případě, že je *klasický*.</span><span class="sxs-lookup"><span data-stu-id="86ad6-206">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="86ad6-207">U filmů identifikovaných jako klasických kontroluje datum vydání, aby se zajistilo, že není pozdější než limit předaný konstruktoru atributu.)</span><span class="sxs-lookup"><span data-stu-id="86ad6-207">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/sample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="86ad6-208">Proměnná `movie` v předchozím příkladu představuje objekt `Movie`, který obsahuje data z odesílání formuláře.</span><span class="sxs-lookup"><span data-stu-id="86ad6-208">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="86ad6-209">Metoda `IsValid` kontroluje datum a Žánr.</span><span class="sxs-lookup"><span data-stu-id="86ad6-209">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="86ad6-210">Po úspěšném ověření `IsValid` vrátí kód `ValidationResult.Success`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-210">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="86ad6-211">Pokud se ověření nepovede, vrátí se `ValidationResult` s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="86ad6-211">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="86ad6-212">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="86ad6-212">IValidatableObject</span></span>

<span data-ttu-id="86ad6-213">Předchozí příklad funguje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="86ad6-213">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="86ad6-214">Další možností ověřování na úrovni třídy je implementace `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-214">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/sample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="86ad6-215">Ověřování uzlu nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="86ad6-215">Top-level node validation</span></span>

<span data-ttu-id="86ad6-216">Uzly nejvyšší úrovně zahrnují:</span><span class="sxs-lookup"><span data-stu-id="86ad6-216">Top-level nodes include:</span></span>

* <span data-ttu-id="86ad6-217">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="86ad6-217">Action parameters</span></span>
* <span data-ttu-id="86ad6-218">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="86ad6-218">Controller properties</span></span>
* <span data-ttu-id="86ad6-219">Parametry obslužné rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="86ad6-219">Page handler parameters</span></span>
* <span data-ttu-id="86ad6-220">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="86ad6-220">Page model properties</span></span>

<span data-ttu-id="86ad6-221">Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem.</span><span class="sxs-lookup"><span data-stu-id="86ad6-221">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="86ad6-222">V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření parametru `phone` akce:</span><span class="sxs-lookup"><span data-stu-id="86ad6-222">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="86ad6-223">Uzly nejvyšší úrovně mohou použít <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování.</span><span class="sxs-lookup"><span data-stu-id="86ad6-223">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="86ad6-224">V následujícím příkladu z ukázkové aplikace určuje metoda `CheckAge`, že parametr `age` musí být při odeslání formuláře svázán z řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-224">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="86ad6-225">Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře.</span><span class="sxs-lookup"><span data-stu-id="86ad6-225">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="86ad6-226">První formulář odešle hodnotu `Age` `99` jako řetězec dotazu: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-226">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="86ad6-227">Když se odešle správně formátovaný `age` parametr z řetězce dotazu, formulář se ověří.</span><span class="sxs-lookup"><span data-stu-id="86ad6-227">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="86ad6-228">Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede.</span><span class="sxs-lookup"><span data-stu-id="86ad6-228">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="86ad6-229">Vazba se nezdařila, protože parametr `age` musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-229">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="86ad6-230">Při spuštění s `CompatibilityVersion.Version_2_1` nebo novějším je ve výchozím nastavení povoleno ověřování uzlů nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="86ad6-230">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="86ad6-231">V opačném případě je ověřování uzlu nejvyšší úrovně zakázané.</span><span class="sxs-lookup"><span data-stu-id="86ad6-231">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="86ad6-232">Výchozí možnost může být přepsána nastavením vlastnosti <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> v (`Startup.ConfigureServices`), jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="86ad6-232">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="86ad6-233">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="86ad6-233">Maximum errors</span></span>

<span data-ttu-id="86ad6-234">Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200).</span><span class="sxs-lookup"><span data-stu-id="86ad6-234">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="86ad6-235">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="86ad6-235">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="86ad6-236">Maximální rekurze</span><span class="sxs-lookup"><span data-stu-id="86ad6-236">Maximum recursion</span></span>

<span data-ttu-id="86ad6-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> projde grafem objektu ověřovaného modelu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="86ad6-238">U modelů, které jsou velmi hlubokoelné nebo nekonečné rekurzivní, může ověřování způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="86ad6-238">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="86ad6-239">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku.</span><span class="sxs-lookup"><span data-stu-id="86ad6-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="86ad6-240">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 200 při spuštění s `CompatibilityVersion.Version_2_2` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="86ad6-240">The default value of `MvcOptions.MaxValidationDepth` is 200 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="86ad6-241">Pro starší verze je hodnota null, což znamená bez omezení hloubky.</span><span class="sxs-lookup"><span data-stu-id="86ad6-241">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="86ad6-242">Automatické krátké okruhy</span><span class="sxs-lookup"><span data-stu-id="86ad6-242">Automatic short-circuit</span></span>

<span data-ttu-id="86ad6-243">Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="86ad6-243">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="86ad6-244">Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních objektů (například `byte[]`, `string[]`, `Dictionary<string, string>`) a složitých grafů objektů, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="86ad6-244">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="86ad6-245">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="86ad6-245">Disable validation</span></span>

<span data-ttu-id="86ad6-246">Zakázání ověřování:</span><span class="sxs-lookup"><span data-stu-id="86ad6-246">To disable validation:</span></span>

1. <span data-ttu-id="86ad6-247">Vytvořte implementaci `IObjectModelValidator`, která neoznačí žádná pole jako neplatnou.</span><span class="sxs-lookup"><span data-stu-id="86ad6-247">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/sample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="86ad6-248">Přidejte následující kód, který `Startup.ConfigureServices` k nahrazení výchozí implementace `IObjectModelValidator` v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="86ad6-248">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="86ad6-249">Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-249">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="86ad6-250">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="86ad6-250">Client-side validation</span></span>

<span data-ttu-id="86ad6-251">Ověřování na straně klienta brání odeslání, dokud není formulář platný.</span><span class="sxs-lookup"><span data-stu-id="86ad6-251">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="86ad6-252">Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="86ad6-252">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="86ad6-253">Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="86ad6-253">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="86ad6-254">Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="86ad6-254">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="86ad6-255">Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="86ad6-255">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="86ad6-256">Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="86ad6-256">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="86ad6-257">Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu pro vykreslení HTML 5 `data-` atributů pro prvky formuláře, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="86ad6-257">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="86ad6-258">jQuery nenáročné ověřování analyzuje atributy `data-` a předá logiku do příkazu jQuery Validate a efektivně kopíruje do klienta logiku ověřování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="86ad6-258">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="86ad6-259">Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="86ad6-259">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="86ad6-260">Předchozí pomocník značek vykresluje následující kód HTML.</span><span class="sxs-lookup"><span data-stu-id="86ad6-260">The preceding tag helpers render the following HTML.</span></span>

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

<span data-ttu-id="86ad6-261">Všimněte si, že atributy `data-` ve výstupu HTML odpovídají atributům ověřování pro vlastnost `ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-261">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="86ad6-262">Atribut `data-val-required` obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání.</span><span class="sxs-lookup"><span data-stu-id="86ad6-262">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="86ad6-263">jQuery unpassing předá tuto hodnotu do metody jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodné **\<> elementu span** .</span><span class="sxs-lookup"><span data-stu-id="86ad6-263">jQuery Unobtrusive Validation passes this value to the jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="86ad6-264">Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána atributem `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-264">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="86ad6-265">Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček.</span><span class="sxs-lookup"><span data-stu-id="86ad6-265">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="86ad6-266">`[DataType]` atributů a podtřídách, jako je `[EmailAddress]`, vám umožní zadat chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-266">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="86ad6-267">Přidání ověřování do dynamických formulářů</span><span class="sxs-lookup"><span data-stu-id="86ad6-267">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="86ad6-268">jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují.</span><span class="sxs-lookup"><span data-stu-id="86ad6-268">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="86ad6-269">Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích.</span><span class="sxs-lookup"><span data-stu-id="86ad6-269">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="86ad6-270">Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="86ad6-270">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="86ad6-271">Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.</span><span class="sxs-lookup"><span data-stu-id="86ad6-271">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="86ad6-272">Metoda `$.validator.unobtrusive.parse()` přijímá selektor jQuery pro svůj jeden argument.</span><span class="sxs-lookup"><span data-stu-id="86ad6-272">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="86ad6-273">Tato metoda oznamuje nenáročné ověřování, aby bylo možné analyzovat `data-` atributů formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="86ad6-273">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="86ad6-274">Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="86ad6-274">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="86ad6-275">Přidání ověřování do dynamických ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="86ad6-275">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="86ad6-276">Metoda `$.validator.unobtrusive.parse()` pracuje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, jako je `<input>` a `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-276">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="86ad6-277">Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-277">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="86ad6-278">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="86ad6-278">Custom client-side validation</span></span>

<span data-ttu-id="86ad6-279">Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery.</span><span class="sxs-lookup"><span data-stu-id="86ad6-279">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="86ad6-280">Následující vzorový kód adaptéru byl napsán pro `ClassicMovie` a `ClassicMovie2` atributy, které byly představeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="86ad6-280">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/sample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="86ad6-281">Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="86ad6-281">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="86ad6-282">Použití adaptéru pro dané pole je aktivováno `data-` atributy, které:</span><span class="sxs-lookup"><span data-stu-id="86ad6-282">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="86ad6-283">Označte pole jako podléhající ověřování (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="86ad6-283">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="86ad6-284">Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="86ad6-284">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="86ad6-285">Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="86ad6-285">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="86ad6-286">Následující příklad ukazuje atributy `data-` pro atribut `ClassicMovie` ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="86ad6-286">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="86ad6-287">Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) využívají informace z atributů ověřování k vykreslování atributů `data-`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-287">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="86ad6-288">Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních atributů `data-` HTML:</span><span class="sxs-lookup"><span data-stu-id="86ad6-288">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="86ad6-289">Vytvořte třídu, která je odvozena z `AttributeAdapterBase<TAttribute>` a třídu, která implementuje `IValidationAttributeAdapterProvider`a zaregistrujte svůj atribut a jeho adaptér v DI.</span><span class="sxs-lookup"><span data-stu-id="86ad6-289">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="86ad6-290">Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách.</span><span class="sxs-lookup"><span data-stu-id="86ad6-290">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="86ad6-291">Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.</span><span class="sxs-lookup"><span data-stu-id="86ad6-291">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="86ad6-292">Implementujte `IClientModelValidator` ve vaší třídě `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-292">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="86ad6-293">Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.</span><span class="sxs-lookup"><span data-stu-id="86ad6-293">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="86ad6-294">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="86ad6-294">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="86ad6-295">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovie` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86ad6-295">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="86ad6-296">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="86ad6-296">To add client validation by using this method:</span></span>

1. <span data-ttu-id="86ad6-297">Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut.</span><span class="sxs-lookup"><span data-stu-id="86ad6-297">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="86ad6-298">Odvodit třídu z [AttributeAdapterBase\<t >](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="86ad6-298">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="86ad6-299">Vytvořte `AddValidation` metodu, která přidá atributy `data-` do vykresleného výstupu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-299">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="86ad6-300">Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="86ad6-300">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="86ad6-301">V metodě `GetAttributeAdapter` předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-301">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="86ad6-302">Zaregistrujte poskytovatele adaptéru pro DI v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="86ad6-302">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="86ad6-303">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="86ad6-303">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="86ad6-304">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovie2` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86ad6-304">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="86ad6-305">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="86ad6-305">To add client validation by using this method:</span></span>

* <span data-ttu-id="86ad6-306">Ve vlastním ověřovacím atributu implementujte rozhraní `IClientModelValidator` a vytvořte metodu `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-306">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="86ad6-307">V metodě `AddValidation` přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-307">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="86ad6-308">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="86ad6-308">Disable client-side validation</span></span>

<span data-ttu-id="86ad6-309">Následující kód zakáže ověřování klienta v zobrazeních MVC:</span><span class="sxs-lookup"><span data-stu-id="86ad6-309">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="86ad6-310">A v Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="86ad6-310">And in Razor Pages:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="86ad6-311">Další možností pro vypnutí ověřování klienta je zadání komentáře k odkazu na `_ValidationScriptsPartial` v souboru *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="86ad6-311">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="86ad6-312">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="86ad6-312">Additional resources</span></span>

* [<span data-ttu-id="86ad6-313">Obor názvů System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="86ad6-313">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="86ad6-314">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="86ad6-314">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="86ad6-315">Tento článek vysvětluje, jak ověřit vstup uživatele ve ASP.NET Core MVC nebo v aplikaci Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="86ad6-315">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="86ad6-316">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="86ad6-316">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="86ad6-317">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="86ad6-317">Model state</span></span>

<span data-ttu-id="86ad6-318">Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-318">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="86ad6-319">Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat (například "x" je zadáno v poli, které očekává celé číslo).</span><span class="sxs-lookup"><span data-stu-id="86ad6-319">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="86ad6-320">K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům (například hodnota 0 je zadána v poli, které očekává hodnocení mezi 1 a 5).</span><span class="sxs-lookup"><span data-stu-id="86ad6-320">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="86ad6-321">Před provedením akce kontroleru nebo obslužné rutiny Razor Pages se vyskytuje jak vazba modelů, tak ověřování.</span><span class="sxs-lookup"><span data-stu-id="86ad6-321">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="86ad6-322">U webových aplikací je zodpovědností aplikace na kontrolu `ModelState.IsValid` a odpovídajícím způsobem reagovat.</span><span class="sxs-lookup"><span data-stu-id="86ad6-322">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="86ad6-323">Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="86ad6-323">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="86ad6-324">Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid`, pokud mají `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="86ad6-324">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="86ad6-325">V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o problému, pokud stav modelu není platný.</span><span class="sxs-lookup"><span data-stu-id="86ad6-325">In that case, an automatic HTTP 400 response containing issue details is returned when model state is invalid.</span></span> <span data-ttu-id="86ad6-326">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="86ad6-326">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="86ad6-327">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="86ad6-327">Rerun validation</span></span>

<span data-ttu-id="86ad6-328">Ověřování je automatické, ale můžete je chtít opakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="86ad6-328">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="86ad6-329">Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-329">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="86ad6-330">Chcete-li znovu spustit ověřování, zavolejte metodu `TryValidateModel`, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="86ad6-330">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/sample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="86ad6-331">Atributy ověřování</span><span class="sxs-lookup"><span data-stu-id="86ad6-331">Validation attributes</span></span>

<span data-ttu-id="86ad6-332">Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-332">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="86ad6-333">Následující příklad z [ukázkové aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) zobrazuje třídu modelu s poznámkou ověřování atributů.</span><span class="sxs-lookup"><span data-stu-id="86ad6-333">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="86ad6-334">Atribut `[ClassicMovie]` je vlastní ověřovací atribut a jsou integrovány i ostatní.</span><span class="sxs-lookup"><span data-stu-id="86ad6-334">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="86ad6-335">(Nezobrazuje se `[ClassicMovie2]`, který ukazuje alternativní způsob implementace vlastního atributu.)</span><span class="sxs-lookup"><span data-stu-id="86ad6-335">(Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.)</span></span>

[!code-csharp[](validation/sample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="86ad6-336">Předdefinované atributy</span><span class="sxs-lookup"><span data-stu-id="86ad6-336">Built-in attributes</span></span>

<span data-ttu-id="86ad6-337">Tady jsou některé z vestavěných ověřovacích atributů:</span><span class="sxs-lookup"><span data-stu-id="86ad6-337">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="86ad6-338">`[CreditCard]`: ověřuje, zda má vlastnost formát kreditní karty.</span><span class="sxs-lookup"><span data-stu-id="86ad6-338">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="86ad6-339">`[Compare]`: ověří, že se dvě vlastnosti v modelu shodují.</span><span class="sxs-lookup"><span data-stu-id="86ad6-339">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="86ad6-340">`[EmailAddress]`: ověřuje, zda má vlastnost formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-340">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="86ad6-341">`[Phone]`: ověřuje, zda má vlastnost formát telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="86ad6-341">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="86ad6-342">`[Range]`: ověří, že hodnota vlastnosti spadá do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-342">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="86ad6-343">`[RegularExpression]`: ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-343">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="86ad6-344">`[Required]`: ověří, že pole nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="86ad6-344">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="86ad6-345">Podrobnosti o chování tohoto atributu naleznete v [atributu [required]](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="86ad6-345">See [[Required] attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="86ad6-346">`[StringLength]`: ověří, že hodnota řetězcové vlastnosti nepřekračuje zadané omezení délky.</span><span class="sxs-lookup"><span data-stu-id="86ad6-346">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="86ad6-347">`[Url]`: ověřuje, zda má vlastnost formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="86ad6-347">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="86ad6-348">`[Remote]`: ověřuje vstup na straně klienta voláním metody Action na serveru.</span><span class="sxs-lookup"><span data-stu-id="86ad6-348">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="86ad6-349">Podrobnosti o chování tohoto atributu naleznete v [atributu [Remote]](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="86ad6-349">See [[Remote] attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="86ad6-350">Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="86ad6-350">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="86ad6-351">Chybové zprávy</span><span class="sxs-lookup"><span data-stu-id="86ad6-351">Error messages</span></span>

<span data-ttu-id="86ad6-352">Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="86ad6-352">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="86ad6-353">Příklad:</span><span class="sxs-lookup"><span data-stu-id="86ad6-353">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="86ad6-354">Interně atributy volají `String.Format` zástupný symbol pro název pole a někdy další zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="86ad6-354">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="86ad6-355">Příklad:</span><span class="sxs-lookup"><span data-stu-id="86ad6-355">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="86ad6-356">Při použití na vlastnost `Name` by byla chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."</span><span class="sxs-lookup"><span data-stu-id="86ad6-356">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="86ad6-357">Chcete-li zjistit, které parametry jsou předány `String.Format` pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="86ad6-357">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="86ad6-358">[Required] – atribut</span><span class="sxs-lookup"><span data-stu-id="86ad6-358">[Required] attribute</span></span>

<span data-ttu-id="86ad6-359">Ve výchozím nastavení systém ověřování zpracovává parametry bez hodnoty null nebo vlastnosti, jako by měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-359">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="86ad6-360">[Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , například `decimal` a `int`, nejsou null.</span><span class="sxs-lookup"><span data-stu-id="86ad6-360">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="86ad6-361">[Požadováno] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="86ad6-361">[Required] validation on the server</span></span>

<span data-ttu-id="86ad6-362">Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="86ad6-362">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="86ad6-363">Pole, které nesmí mít hodnotu null, je vždy platné a chybová zpráva [required] se nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="86ad6-363">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="86ad6-364">Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, takže se zobrazí chybová zpráva, například `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-364">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="86ad6-365">Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="86ad6-365">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="86ad6-366">Převést pole na hodnotu null (například `decimal?` místo `decimal`).</span><span class="sxs-lookup"><span data-stu-id="86ad6-366">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="86ad6-367">Typy hodnot s [povolenou hodnotou null\<t >](/dotnet/csharp/programming-guide/nullable-types/) se považují za standardní typy Nullable.</span><span class="sxs-lookup"><span data-stu-id="86ad6-367">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="86ad6-368">Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-368">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="86ad6-369">Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="86ad6-369">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="86ad6-370">[Požadováno] ověřování na klientovi</span><span class="sxs-lookup"><span data-stu-id="86ad6-370">[Required] validation on the client</span></span>

<span data-ttu-id="86ad6-371">Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi.</span><span class="sxs-lookup"><span data-stu-id="86ad6-371">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="86ad6-372">Na klientovi:</span><span class="sxs-lookup"><span data-stu-id="86ad6-372">On the client:</span></span>

* <span data-ttu-id="86ad6-373">Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup.</span><span class="sxs-lookup"><span data-stu-id="86ad6-373">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="86ad6-374">Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="86ad6-374">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="86ad6-375">Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením.</span><span class="sxs-lookup"><span data-stu-id="86ad6-375">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="86ad6-376">Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.</span><span class="sxs-lookup"><span data-stu-id="86ad6-376">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="86ad6-377">Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za, ale měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-377">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="86ad6-378">To znamená, že získáte ověřování na straně klienta i v případě, že nepoužijete atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-378">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="86ad6-379">Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="86ad6-379">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="86ad6-380">Chcete-li zadat vlastní chybovou zprávu, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="86ad6-380">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="86ad6-381">[Remote] – atribut</span><span class="sxs-lookup"><span data-stu-id="86ad6-381">[Remote] attribute</span></span>

<span data-ttu-id="86ad6-382">Atribut `[Remote]` implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný.</span><span class="sxs-lookup"><span data-stu-id="86ad6-382">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="86ad6-383">Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.</span><span class="sxs-lookup"><span data-stu-id="86ad6-383">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="86ad6-384">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="86ad6-384">To implement remote validation:</span></span>

1. <span data-ttu-id="86ad6-385">Vytvořte metodu Action pro volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-385">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="86ad6-386">Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="86ad6-386">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="86ad6-387">`"true"` znamená, že jsou vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="86ad6-387">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="86ad6-388">`"false"`, `undefined`nebo `null` znamená, že vstup není platný.</span><span class="sxs-lookup"><span data-stu-id="86ad6-388">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="86ad6-389">Zobrazí výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-389">Display the default error message.</span></span>
   * <span data-ttu-id="86ad6-390">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="86ad6-390">Any other string means the input is invalid.</span></span> <span data-ttu-id="86ad6-391">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-391">Display the string as a custom error message.</span></span>

   <span data-ttu-id="86ad6-392">Tady je příklad metody akce, která vrací vlastní chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-392">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="86ad6-393">Ve třídě modelu poznámku k vlastnosti s atributem `[Remote]`, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-393">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="86ad6-394">Atribut `[Remote]` je v oboru názvů `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-394">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="86ad6-395">Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) , pokud nepoužíváte `Microsoft.AspNetCore.App` nebo `Microsoft.AspNetCore.All` Metapackage.</span><span class="sxs-lookup"><span data-stu-id="86ad6-395">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="86ad6-396">Další pole</span><span class="sxs-lookup"><span data-stu-id="86ad6-396">Additional fields</span></span>

<span data-ttu-id="86ad6-397">Vlastnost `AdditionalFields` atributu `[Remote]` umožňuje ověřit kombinace polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="86ad6-397">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="86ad6-398">Pokud například `User` model obsahoval `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají tento pár názvů.</span><span class="sxs-lookup"><span data-stu-id="86ad6-398">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="86ad6-399">Následující příklad ukazuje, jak použít `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="86ad6-399">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="86ad6-400">`AdditionalFields` lze nastavit explicitně na řetězce `"FirstName"` a `"LastName"`, ale použití operátoru [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) zjednodušuje pozdější refaktoring.</span><span class="sxs-lookup"><span data-stu-id="86ad6-400">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="86ad6-401">Metoda Action pro toto ověření musí přijmout argumenty jméno a příjmení:</span><span class="sxs-lookup"><span data-stu-id="86ad6-401">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="86ad6-402">Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.</span><span class="sxs-lookup"><span data-stu-id="86ad6-402">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="86ad6-403">Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="86ad6-403">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="86ad6-404">Chcete-li například přidat vlastnost `MiddleName` do modelu, nastavte atribut `[Remote]`, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-404">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="86ad6-405">`AdditionalFields`, jako jsou všechny argumenty atributu, musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="86ad6-405">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="86ad6-406">Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-406">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="86ad6-407">Alternativy k předdefinovaným atributům</span><span class="sxs-lookup"><span data-stu-id="86ad6-407">Alternatives to built-in attributes</span></span>

<span data-ttu-id="86ad6-408">Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:</span><span class="sxs-lookup"><span data-stu-id="86ad6-408">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="86ad6-409">[Vytvořte vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="86ad6-409">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="86ad6-410">[Implementujte IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="86ad6-410">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="86ad6-411">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="86ad6-411">Custom attributes</span></span>

<span data-ttu-id="86ad6-412">U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="86ad6-412">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="86ad6-413">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>a přepište metodu <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="86ad6-413">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="86ad6-414">Metoda `IsValid` přijímá objekt s názvem *hodnota*, který je vstupem k ověření.</span><span class="sxs-lookup"><span data-stu-id="86ad6-414">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="86ad6-415">Přetížení také přijímá objekt `ValidationContext`, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-415">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="86ad6-416">Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce.</span><span class="sxs-lookup"><span data-stu-id="86ad6-416">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="86ad6-417">Atribut `[ClassicMovie2]` nejprve zkontroluje Žánr a pokračuje pouze v případě, že je *klasický*.</span><span class="sxs-lookup"><span data-stu-id="86ad6-417">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="86ad6-418">U filmů identifikovaných jako klasických kontroluje datum vydání, aby se zajistilo, že není pozdější než limit předaný konstruktoru atributu.)</span><span class="sxs-lookup"><span data-stu-id="86ad6-418">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/sample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="86ad6-419">Proměnná `movie` v předchozím příkladu představuje objekt `Movie`, který obsahuje data z odesílání formuláře.</span><span class="sxs-lookup"><span data-stu-id="86ad6-419">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="86ad6-420">Metoda `IsValid` kontroluje datum a Žánr.</span><span class="sxs-lookup"><span data-stu-id="86ad6-420">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="86ad6-421">Po úspěšném ověření `IsValid` vrátí kód `ValidationResult.Success`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-421">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="86ad6-422">Pokud se ověření nepovede, vrátí se `ValidationResult` s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="86ad6-422">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="86ad6-423">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="86ad6-423">IValidatableObject</span></span>

<span data-ttu-id="86ad6-424">Předchozí příklad funguje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="86ad6-424">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="86ad6-425">Další možností ověřování na úrovni třídy je implementace `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-425">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/sample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="86ad6-426">Ověřování uzlu nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="86ad6-426">Top-level node validation</span></span>

<span data-ttu-id="86ad6-427">Uzly nejvyšší úrovně zahrnují:</span><span class="sxs-lookup"><span data-stu-id="86ad6-427">Top-level nodes include:</span></span>

* <span data-ttu-id="86ad6-428">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="86ad6-428">Action parameters</span></span>
* <span data-ttu-id="86ad6-429">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="86ad6-429">Controller properties</span></span>
* <span data-ttu-id="86ad6-430">Parametry obslužné rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="86ad6-430">Page handler parameters</span></span>
* <span data-ttu-id="86ad6-431">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="86ad6-431">Page model properties</span></span>

<span data-ttu-id="86ad6-432">Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem.</span><span class="sxs-lookup"><span data-stu-id="86ad6-432">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="86ad6-433">V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření parametru `phone` akce:</span><span class="sxs-lookup"><span data-stu-id="86ad6-433">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="86ad6-434">Uzly nejvyšší úrovně mohou použít <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování.</span><span class="sxs-lookup"><span data-stu-id="86ad6-434">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="86ad6-435">V následujícím příkladu z ukázkové aplikace určuje metoda `CheckAge`, že parametr `age` musí být při odeslání formuláře svázán z řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-435">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="86ad6-436">Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře.</span><span class="sxs-lookup"><span data-stu-id="86ad6-436">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="86ad6-437">První formulář odešle hodnotu `Age` `99` jako řetězec dotazu: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-437">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="86ad6-438">Když se odešle správně formátovaný `age` parametr z řetězce dotazu, formulář se ověří.</span><span class="sxs-lookup"><span data-stu-id="86ad6-438">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="86ad6-439">Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede.</span><span class="sxs-lookup"><span data-stu-id="86ad6-439">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="86ad6-440">Vazba se nezdařila, protože parametr `age` musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-440">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="86ad6-441">Při spuštění s `CompatibilityVersion.Version_2_1` nebo novějším je ve výchozím nastavení povoleno ověřování uzlů nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="86ad6-441">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="86ad6-442">V opačném případě je ověřování uzlu nejvyšší úrovně zakázané.</span><span class="sxs-lookup"><span data-stu-id="86ad6-442">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="86ad6-443">Výchozí možnost může být přepsána nastavením vlastnosti <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> v (`Startup.ConfigureServices`), jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="86ad6-443">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="86ad6-444">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="86ad6-444">Maximum errors</span></span>

<span data-ttu-id="86ad6-445">Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200).</span><span class="sxs-lookup"><span data-stu-id="86ad6-445">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="86ad6-446">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="86ad6-446">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="86ad6-447">Maximální rekurze</span><span class="sxs-lookup"><span data-stu-id="86ad6-447">Maximum recursion</span></span>

<span data-ttu-id="86ad6-448"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> projde grafem objektu ověřovaného modelu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-448"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="86ad6-449">U modelů, které jsou velmi hlubokoelné nebo nekonečné rekurzivní, může ověřování způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="86ad6-449">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="86ad6-450">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku.</span><span class="sxs-lookup"><span data-stu-id="86ad6-450">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="86ad6-451">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 200 při spuštění s `CompatibilityVersion.Version_2_2` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="86ad6-451">The default value of `MvcOptions.MaxValidationDepth` is 200 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="86ad6-452">Pro starší verze je hodnota null, což znamená bez omezení hloubky.</span><span class="sxs-lookup"><span data-stu-id="86ad6-452">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="86ad6-453">Automatické krátké okruhy</span><span class="sxs-lookup"><span data-stu-id="86ad6-453">Automatic short-circuit</span></span>

<span data-ttu-id="86ad6-454">Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="86ad6-454">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="86ad6-455">Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních objektů (například `byte[]`, `string[]`, `Dictionary<string, string>`) a složitých grafů objektů, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="86ad6-455">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="86ad6-456">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="86ad6-456">Disable validation</span></span>

<span data-ttu-id="86ad6-457">Zakázání ověřování:</span><span class="sxs-lookup"><span data-stu-id="86ad6-457">To disable validation:</span></span>

1. <span data-ttu-id="86ad6-458">Vytvořte implementaci `IObjectModelValidator`, která neoznačí žádná pole jako neplatnou.</span><span class="sxs-lookup"><span data-stu-id="86ad6-458">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/sample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="86ad6-459">Přidejte následující kód, který `Startup.ConfigureServices` k nahrazení výchozí implementace `IObjectModelValidator` v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="86ad6-459">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="86ad6-460">Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-460">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="86ad6-461">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="86ad6-461">Client-side validation</span></span>

<span data-ttu-id="86ad6-462">Ověřování na straně klienta brání odeslání, dokud není formulář platný.</span><span class="sxs-lookup"><span data-stu-id="86ad6-462">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="86ad6-463">Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="86ad6-463">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="86ad6-464">Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="86ad6-464">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="86ad6-465">Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="86ad6-465">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="86ad6-466">Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="86ad6-466">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="86ad6-467">Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="86ad6-467">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="86ad6-468">Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu pro vykreslení HTML 5 `data-` atributů pro prvky formuláře, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="86ad6-468">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="86ad6-469">jQuery nenáročné ověřování analyzuje atributy `data-` a předá logiku do příkazu jQuery Validate a efektivně kopíruje do klienta logiku ověřování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="86ad6-469">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="86ad6-470">Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="86ad6-470">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="86ad6-471">Předchozí pomocník značek vykresluje následující kód HTML.</span><span class="sxs-lookup"><span data-stu-id="86ad6-471">The preceding tag helpers render the following HTML.</span></span>

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

<span data-ttu-id="86ad6-472">Všimněte si, že atributy `data-` ve výstupu HTML odpovídají atributům ověřování pro vlastnost `ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-472">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="86ad6-473">Atribut `data-val-required` obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání.</span><span class="sxs-lookup"><span data-stu-id="86ad6-473">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="86ad6-474">jQuery unpassing předá tuto hodnotu do metody jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodné **\<> elementu span** .</span><span class="sxs-lookup"><span data-stu-id="86ad6-474">jQuery Unobtrusive Validation passes this value to the jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="86ad6-475">Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána atributem `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-475">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="86ad6-476">Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček.</span><span class="sxs-lookup"><span data-stu-id="86ad6-476">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="86ad6-477">`[DataType]` atributů a podtřídách, jako je `[EmailAddress]`, vám umožní zadat chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="86ad6-477">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="86ad6-478">Přidání ověřování do dynamických formulářů</span><span class="sxs-lookup"><span data-stu-id="86ad6-478">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="86ad6-479">jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují.</span><span class="sxs-lookup"><span data-stu-id="86ad6-479">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="86ad6-480">Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích.</span><span class="sxs-lookup"><span data-stu-id="86ad6-480">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="86ad6-481">Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="86ad6-481">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="86ad6-482">Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.</span><span class="sxs-lookup"><span data-stu-id="86ad6-482">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="86ad6-483">Metoda `$.validator.unobtrusive.parse()` přijímá selektor jQuery pro svůj jeden argument.</span><span class="sxs-lookup"><span data-stu-id="86ad6-483">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="86ad6-484">Tato metoda oznamuje nenáročné ověřování, aby bylo možné analyzovat `data-` atributů formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="86ad6-484">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="86ad6-485">Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="86ad6-485">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="86ad6-486">Přidání ověřování do dynamických ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="86ad6-486">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="86ad6-487">Metoda `$.validator.unobtrusive.parse()` pracuje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, jako je `<input>` a `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-487">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="86ad6-488">Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-488">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="86ad6-489">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="86ad6-489">Custom client-side validation</span></span>

<span data-ttu-id="86ad6-490">Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery.</span><span class="sxs-lookup"><span data-stu-id="86ad6-490">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="86ad6-491">Následující vzorový kód adaptéru byl napsán pro `ClassicMovie` a `ClassicMovie2` atributy, které byly představeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="86ad6-491">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/sample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="86ad6-492">Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="86ad6-492">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="86ad6-493">Použití adaptéru pro dané pole je aktivováno `data-` atributy, které:</span><span class="sxs-lookup"><span data-stu-id="86ad6-493">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="86ad6-494">Označte pole jako podléhající ověřování (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="86ad6-494">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="86ad6-495">Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="86ad6-495">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="86ad6-496">Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="86ad6-496">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="86ad6-497">Následující příklad ukazuje atributy `data-` pro atribut `ClassicMovie` ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="86ad6-497">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="86ad6-498">Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) využívají informace z atributů ověřování k vykreslování atributů `data-`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-498">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="86ad6-499">Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních atributů `data-` HTML:</span><span class="sxs-lookup"><span data-stu-id="86ad6-499">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="86ad6-500">Vytvořte třídu, která je odvozena z `AttributeAdapterBase<TAttribute>` a třídu, která implementuje `IValidationAttributeAdapterProvider`a zaregistrujte svůj atribut a jeho adaptér v DI.</span><span class="sxs-lookup"><span data-stu-id="86ad6-500">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="86ad6-501">Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách.</span><span class="sxs-lookup"><span data-stu-id="86ad6-501">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="86ad6-502">Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.</span><span class="sxs-lookup"><span data-stu-id="86ad6-502">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="86ad6-503">Implementujte `IClientModelValidator` ve vaší třídě `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-503">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="86ad6-504">Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.</span><span class="sxs-lookup"><span data-stu-id="86ad6-504">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="86ad6-505">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="86ad6-505">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="86ad6-506">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovie` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86ad6-506">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="86ad6-507">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="86ad6-507">To add client validation by using this method:</span></span>

1. <span data-ttu-id="86ad6-508">Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut.</span><span class="sxs-lookup"><span data-stu-id="86ad6-508">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="86ad6-509">Odvodit třídu z [AttributeAdapterBase\<t >](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="86ad6-509">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="86ad6-510">Vytvořte `AddValidation` metodu, která přidá atributy `data-` do vykresleného výstupu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-510">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="86ad6-511">Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="86ad6-511">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="86ad6-512">V metodě `GetAttributeAdapter` předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-512">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="86ad6-513">Zaregistrujte poskytovatele adaptéru pro DI v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="86ad6-513">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="86ad6-514">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="86ad6-514">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="86ad6-515">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovie2` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="86ad6-515">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="86ad6-516">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="86ad6-516">To add client validation by using this method:</span></span>

* <span data-ttu-id="86ad6-517">Ve vlastním ověřovacím atributu implementujte rozhraní `IClientModelValidator` a vytvořte metodu `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="86ad6-517">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="86ad6-518">V metodě `AddValidation` přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="86ad6-518">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="86ad6-519">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="86ad6-519">Disable client-side validation</span></span>

<span data-ttu-id="86ad6-520">Následující kód zakáže ověřování klienta v zobrazeních MVC:</span><span class="sxs-lookup"><span data-stu-id="86ad6-520">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="86ad6-521">A v Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="86ad6-521">And in Razor Pages:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="86ad6-522">Další možností pro vypnutí ověřování klienta je zadání komentáře k odkazu na `_ValidationScriptsPartial` v souboru *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="86ad6-522">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="86ad6-523">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="86ad6-523">Additional resources</span></span>

* [<span data-ttu-id="86ad6-524">Obor názvů System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="86ad6-524">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="86ad6-525">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="86ad6-525">Model Binding</span></span>](model-binding.md)

::: moniker-end

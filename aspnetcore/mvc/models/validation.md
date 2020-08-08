---
title: Ověření modelu ve ASP.NET Core MVC
author: rick-anderson
description: Přečtěte si o ověřování modelu v ASP.NET Core MVC a Razor stránkách.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/validation
ms.openlocfilehash: c0edf56c966cb90c1c308f300a8944d392fdc0e7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020974"
---
# <a name="model-validation-in-aspnet-core-mvc-and-no-locrazor-pages"></a><span data-ttu-id="9f41e-103">Ověření modelu ve ASP.NET Core MVC a Razor stránkách</span><span class="sxs-lookup"><span data-stu-id="9f41e-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9f41e-104">Od [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="9f41e-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="9f41e-105">Tento článek vysvětluje, jak ověřit vstup uživatele v aplikaci ASP.NET Core MVC nebo Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="9f41e-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="9f41e-106">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9f41e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="9f41e-107">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="9f41e-107">Model state</span></span>

<span data-ttu-id="9f41e-108">Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="9f41e-109">Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat.</span><span class="sxs-lookup"><span data-stu-id="9f41e-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="9f41e-110">Například "x" je zadáno v poli typu Integer.</span><span class="sxs-lookup"><span data-stu-id="9f41e-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="9f41e-111">K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům.</span><span class="sxs-lookup"><span data-stu-id="9f41e-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="9f41e-112">Například hodnota 0 se zadává v poli, které očekává hodnocení mezi 1 a 5.</span><span class="sxs-lookup"><span data-stu-id="9f41e-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="9f41e-113">Vazba modelů i ověřování modelu proběhne před provedením akce kontroleru nebo Razor metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="9f41e-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="9f41e-114">U webových aplikací je zodpovědností aplikace vhodné je kontrolovat `ModelState.IsValid` a reagovat.</span><span class="sxs-lookup"><span data-stu-id="9f41e-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="9f41e-115">Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="9f41e-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="9f41e-116">Řadiče webového rozhraní API nemusí kontrolovat, `ModelState.IsValid` jestli mají `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="9f41e-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="9f41e-117">V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o chybě, pokud stav modelu není platný.</span><span class="sxs-lookup"><span data-stu-id="9f41e-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="9f41e-118">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="9f41e-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="9f41e-119">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="9f41e-119">Rerun validation</span></span>

<span data-ttu-id="9f41e-120">Ověřování je automatické, ale můžete je chtít opakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="9f41e-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="9f41e-121">Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="9f41e-122">Chcete-li znovu spustit ověřování, zavolejte `TryValidateModel` metodu, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="9f41e-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="9f41e-123">Atributy ověřování</span><span class="sxs-lookup"><span data-stu-id="9f41e-123">Validation attributes</span></span>

<span data-ttu-id="9f41e-124">Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="9f41e-125">Následující příklad z ukázkové aplikace zobrazuje třídu modelu s poznámkou ověřování atributů.</span><span class="sxs-lookup"><span data-stu-id="9f41e-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="9f41e-126">`[ClassicMovie]`Atribut je vlastní ověřovací atribut a jsou integrovány ostatní.</span><span class="sxs-lookup"><span data-stu-id="9f41e-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="9f41e-127">Není zobrazeno `[ClassicMovieWithClientValidator]` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="9f41e-128">`[ClassicMovieWithClientValidator]`ukazuje alternativní způsob implementace vlastního atributu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="9f41e-129">Předdefinované atributy</span><span class="sxs-lookup"><span data-stu-id="9f41e-129">Built-in attributes</span></span>

<span data-ttu-id="9f41e-130">Tady jsou některé z vestavěných ověřovacích atributů:</span><span class="sxs-lookup"><span data-stu-id="9f41e-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="9f41e-131">`[CreditCard]`: Ověří, zda má vlastnost formát kreditní karty.</span><span class="sxs-lookup"><span data-stu-id="9f41e-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span> <span data-ttu-id="9f41e-132">Vyžaduje [Další metody ověření jQuery](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span><span class="sxs-lookup"><span data-stu-id="9f41e-132">Requires [jQuery Validation Additional Methods](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span></span>
* <span data-ttu-id="9f41e-133">`[Compare]`: Ověří, že se dvě vlastnosti v modelu shodují.</span><span class="sxs-lookup"><span data-stu-id="9f41e-133">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="9f41e-134">`[EmailAddress]`: Ověří, zda má vlastnost formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-134">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="9f41e-135">`[Phone]`: Ověří, zda má vlastnost formát telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="9f41e-135">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="9f41e-136">`[Range]`: Ověří, že hodnota vlastnosti spadá do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-136">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="9f41e-137">`[RegularExpression]`: Ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-137">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="9f41e-138">`[Required]`: Ověří, že pole nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="9f41e-138">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="9f41e-139">Podrobnosti o chování tohoto atributu naleznete v [ `[Required]` atributu Attribute](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="9f41e-139">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="9f41e-140">`[StringLength]`: Ověří, že hodnota vlastnosti řetězce nepřekračuje zadané omezení délky.</span><span class="sxs-lookup"><span data-stu-id="9f41e-140">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="9f41e-141">`[Url]`: Ověří, zda má vlastnost formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="9f41e-141">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="9f41e-142">`[Remote]`: Ověří vstup na straně klienta voláním metody Action na serveru.</span><span class="sxs-lookup"><span data-stu-id="9f41e-142">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="9f41e-143">Podrobnosti o chování tohoto atributu naleznete v [ `[Remote]` atributu Attribute](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="9f41e-143">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="9f41e-144">Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="9f41e-144">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="9f41e-145">Chybové zprávy</span><span class="sxs-lookup"><span data-stu-id="9f41e-145">Error messages</span></span>

<span data-ttu-id="9f41e-146">Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="9f41e-146">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="9f41e-147">Například:</span><span class="sxs-lookup"><span data-stu-id="9f41e-147">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="9f41e-148">Interně atributy volají jako `String.Format` zástupný symbol pro název pole a někdy další zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="9f41e-148">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="9f41e-149">Například:</span><span class="sxs-lookup"><span data-stu-id="9f41e-149">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="9f41e-150">Při použití na `Name` vlastnost bude chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."</span><span class="sxs-lookup"><span data-stu-id="9f41e-150">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="9f41e-151">Chcete-li zjistit, které parametry jsou předány `String.Format` pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="9f41e-151">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="9f41e-152">[Required] – atribut</span><span class="sxs-lookup"><span data-stu-id="9f41e-152">[Required] attribute</span></span>

<span data-ttu-id="9f41e-153">Systém ověřování v .NET Core 3,0 a novějším zpracovává parametry bez hodnoty null nebo vázané vlastnosti, jako kdyby měl `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="9f41e-153">The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="9f41e-154">[Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , jako `decimal` jsou a, `int` nejsou null.</span><span class="sxs-lookup"><span data-stu-id="9f41e-154">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span> <span data-ttu-id="9f41e-155">Toto chování je možné zakázat konfigurací <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9f41e-155">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="9f41e-156">[Požadováno] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="9f41e-156">[Required] validation on the server</span></span>

<span data-ttu-id="9f41e-157">Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="9f41e-157">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="9f41e-158">Pole, které nesmí mít hodnotu null, je vždy platné a `[Required]` chybová zpráva atributu se nikdy nezobrazuje.</span><span class="sxs-lookup"><span data-stu-id="9f41e-158">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="9f41e-159">Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, což vede k chybové zprávě, jako je například `The value '' is invalid` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-159">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="9f41e-160">Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="9f41e-160">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="9f41e-161">Převést pole na hodnotu null (například `decimal?` místo `decimal` ).</span><span class="sxs-lookup"><span data-stu-id="9f41e-161">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="9f41e-162">[Nullable \<T> ](/dotnet/csharp/programming-guide/nullable-types/) typy hodnot jsou zpracovány jako standardní typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="9f41e-162">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="9f41e-163">Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-163">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="9f41e-164">Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods> .</span><span class="sxs-lookup"><span data-stu-id="9f41e-164">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="9f41e-165">[Požadováno] ověřování na klientovi</span><span class="sxs-lookup"><span data-stu-id="9f41e-165">[Required] validation on the client</span></span>

<span data-ttu-id="9f41e-166">Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi.</span><span class="sxs-lookup"><span data-stu-id="9f41e-166">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="9f41e-167">Na klientovi:</span><span class="sxs-lookup"><span data-stu-id="9f41e-167">On the client:</span></span>

* <span data-ttu-id="9f41e-168">Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup.</span><span class="sxs-lookup"><span data-stu-id="9f41e-168">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="9f41e-169">Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="9f41e-169">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="9f41e-170">Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením.</span><span class="sxs-lookup"><span data-stu-id="9f41e-170">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="9f41e-171">Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.</span><span class="sxs-lookup"><span data-stu-id="9f41e-171">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="9f41e-172">Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za, i když mají `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="9f41e-172">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="9f41e-173">To znamená, že získáte ověřování na straně klienta i v případě, že atribut nepoužijete `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-173">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="9f41e-174">Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="9f41e-174">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="9f41e-175">Chcete-li zadat vlastní chybovou zprávu, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="9f41e-175">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="9f41e-176">[Remote] – atribut</span><span class="sxs-lookup"><span data-stu-id="9f41e-176">[Remote] attribute</span></span>

<span data-ttu-id="9f41e-177">`[Remote]`Atribut implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný.</span><span class="sxs-lookup"><span data-stu-id="9f41e-177">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="9f41e-178">Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.</span><span class="sxs-lookup"><span data-stu-id="9f41e-178">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="9f41e-179">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="9f41e-179">To implement remote validation:</span></span>

1. <span data-ttu-id="9f41e-180">Vytvořte metodu Action pro volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-180">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="9f41e-181">[Vzdálená](https://jqueryvalidation.org/remote-method/) metoda ověření jQuery očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="9f41e-181">The jQuery Validation [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="9f41e-182">`true`znamená, že jsou vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="9f41e-182">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="9f41e-183">`false`, `undefined` nebo `null` znamená, že vstup není platný.</span><span class="sxs-lookup"><span data-stu-id="9f41e-183">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="9f41e-184">Zobrazí výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-184">Display the default error message.</span></span>
   * <span data-ttu-id="9f41e-185">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="9f41e-185">Any other string means the input is invalid.</span></span> <span data-ttu-id="9f41e-186">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-186">Display the string as a custom error message.</span></span>

   <span data-ttu-id="9f41e-187">Tady je příklad metody akce, která vrací vlastní chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-187">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="9f41e-188">Ve třídě modelu poznámkujte vlastnost s `[Remote]` atributem, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-188">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="9f41e-189">`[Remote]`Atribut je v `Microsoft.AspNetCore.Mvc` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="9f41e-189">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="9f41e-190">Další pole</span><span class="sxs-lookup"><span data-stu-id="9f41e-190">Additional fields</span></span>

<span data-ttu-id="9f41e-191">`AdditionalFields`Vlastnost `[Remote]` atributu umožňuje ověřit kombinace polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="9f41e-191">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="9f41e-192">Pokud má `User` model například `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají odpovídající dvojici názvů.</span><span class="sxs-lookup"><span data-stu-id="9f41e-192">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="9f41e-193">Následující příklad ukazuje, jak použít `AdditionalFields` :</span><span class="sxs-lookup"><span data-stu-id="9f41e-193">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="9f41e-194">`AdditionalFields`lze nastavit explicitně na řetězce "FirstName" a "LastName", ale pomocí operátoru [nameof](/dotnet/csharp/language-reference/keywords/nameof) se zjednoduší pozdější refaktoring.</span><span class="sxs-lookup"><span data-stu-id="9f41e-194">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="9f41e-195">Metoda Action pro toto ověření musí přijmout oba `firstName` argumenty a `lastName` :</span><span class="sxs-lookup"><span data-stu-id="9f41e-195">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="9f41e-196">Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.</span><span class="sxs-lookup"><span data-stu-id="9f41e-196">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="9f41e-197">Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="9f41e-197">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="9f41e-198">Chcete-li například přidat `MiddleName` vlastnost do modelu, nastavte `[Remote]` atribut, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-198">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="9f41e-199">`AdditionalFields`Podobně jako všechny argumenty atributu musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="9f41e-199">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="9f41e-200">Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-200">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="9f41e-201">Alternativy k předdefinovaným atributům</span><span class="sxs-lookup"><span data-stu-id="9f41e-201">Alternatives to built-in attributes</span></span>

<span data-ttu-id="9f41e-202">Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:</span><span class="sxs-lookup"><span data-stu-id="9f41e-202">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="9f41e-203">[Vytvořte vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="9f41e-203">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="9f41e-204">[Implementujte IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="9f41e-204">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="9f41e-205">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="9f41e-205">Custom attributes</span></span>

<span data-ttu-id="9f41e-206">U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="9f41e-206">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="9f41e-207">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> , a přepište <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metodu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-207">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="9f41e-208">`IsValid`Metoda přijímá objekt s názvem *hodnota*, což je vstup, který má být ověřen.</span><span class="sxs-lookup"><span data-stu-id="9f41e-208">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="9f41e-209">Přetížení také přijímá `ValidationContext` objekt, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-209">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="9f41e-210">Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce.</span><span class="sxs-lookup"><span data-stu-id="9f41e-210">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="9f41e-211">`[ClassicMovie]`Atribut:</span><span class="sxs-lookup"><span data-stu-id="9f41e-211">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="9f41e-212">Se spouští jenom na serveru.</span><span class="sxs-lookup"><span data-stu-id="9f41e-212">Is only run on the server.</span></span>
* <span data-ttu-id="9f41e-213">U klasických filmů ověří datum vydání:</span><span class="sxs-lookup"><span data-stu-id="9f41e-213">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="9f41e-214">`movie`Proměnná v předchozím příkladu představuje `Movie` objekt, který obsahuje data z odesílání formuláře.</span><span class="sxs-lookup"><span data-stu-id="9f41e-214">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="9f41e-215">Pokud se ověření nepovede, `ValidationResult` vrátí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="9f41e-215">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="9f41e-216">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="9f41e-216">IValidatableObject</span></span>

<span data-ttu-id="9f41e-217">Předchozí příklad funguje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="9f41e-217">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="9f41e-218">Další možností pro ověřování na úrovni třídy je implementovat `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-218">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="9f41e-219">Ověřování uzlu nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="9f41e-219">Top-level node validation</span></span>

<span data-ttu-id="9f41e-220">Uzly nejvyšší úrovně zahrnují:</span><span class="sxs-lookup"><span data-stu-id="9f41e-220">Top-level nodes include:</span></span>

* <span data-ttu-id="9f41e-221">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="9f41e-221">Action parameters</span></span>
* <span data-ttu-id="9f41e-222">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="9f41e-222">Controller properties</span></span>
* <span data-ttu-id="9f41e-223">Parametry obslužné rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="9f41e-223">Page handler parameters</span></span>
* <span data-ttu-id="9f41e-224">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="9f41e-224">Page model properties</span></span>

<span data-ttu-id="9f41e-225">Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem.</span><span class="sxs-lookup"><span data-stu-id="9f41e-225">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="9f41e-226">V následujícím příkladu z ukázkové aplikace `VerifyPhone` Metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření `phone` parametru akce:</span><span class="sxs-lookup"><span data-stu-id="9f41e-226">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="9f41e-227">Uzly nejvyšší úrovně mohou být použity <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování.</span><span class="sxs-lookup"><span data-stu-id="9f41e-227">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="9f41e-228">V následujícím příkladu z ukázkové aplikace `CheckAge` určuje metoda, že `age` parametr musí být svázán z řetězce dotazu při odeslání formuláře:</span><span class="sxs-lookup"><span data-stu-id="9f41e-228">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="9f41e-229">Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře.</span><span class="sxs-lookup"><span data-stu-id="9f41e-229">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="9f41e-230">První formulář odešle `Age` hodnotu `99` jako parametr řetězce dotazu: `https://localhost:5001/Users/CheckAge?Age=99` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-230">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="9f41e-231">Při odeslání správně formátovaného `age` parametru z řetězce dotazu se formulář ověří.</span><span class="sxs-lookup"><span data-stu-id="9f41e-231">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="9f41e-232">Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede.</span><span class="sxs-lookup"><span data-stu-id="9f41e-232">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="9f41e-233">Vazba se nezdařila, protože `age` parametr musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-233">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="9f41e-234">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="9f41e-234">Maximum errors</span></span>

<span data-ttu-id="9f41e-235">Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200).</span><span class="sxs-lookup"><span data-stu-id="9f41e-235">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="9f41e-236">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9f41e-236">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="9f41e-237">Maximální rekurze</span><span class="sxs-lookup"><span data-stu-id="9f41e-237">Maximum recursion</span></span>

<span data-ttu-id="9f41e-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>projde grafem objektu ověřovaného modelu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="9f41e-239">U modelů, které jsou hluboko nebo jsou nekonečně rekurzivní, může ověřování způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="9f41e-239">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="9f41e-240">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku.</span><span class="sxs-lookup"><span data-stu-id="9f41e-240">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="9f41e-241">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 32.</span><span class="sxs-lookup"><span data-stu-id="9f41e-241">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="9f41e-242">Automatické krátké okruhy</span><span class="sxs-lookup"><span data-stu-id="9f41e-242">Automatic short-circuit</span></span>

<span data-ttu-id="9f41e-243">Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="9f41e-243">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="9f41e-244">Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních elementů (například `byte[]` , `string[]` , `Dictionary<string, string>` ) a složitých grafů objektů, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="9f41e-244">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="9f41e-245">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="9f41e-245">Disable validation</span></span>

<span data-ttu-id="9f41e-246">Zakázání ověřování:</span><span class="sxs-lookup"><span data-stu-id="9f41e-246">To disable validation:</span></span>

1. <span data-ttu-id="9f41e-247">Vytvořte implementaci `IObjectModelValidator` , která neoznačí žádná pole jako neplatnou.</span><span class="sxs-lookup"><span data-stu-id="9f41e-247">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="9f41e-248">Přidejte následující kód k `Startup.ConfigureServices` nahrazení výchozí `IObjectModelValidator` implementace v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="9f41e-248">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="9f41e-249">Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-249">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="9f41e-250">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="9f41e-250">Client-side validation</span></span>

<span data-ttu-id="9f41e-251">Ověřování na straně klienta brání odeslání, dokud není formulář platný.</span><span class="sxs-lookup"><span data-stu-id="9f41e-251">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="9f41e-252">Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="9f41e-252">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="9f41e-253">Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="9f41e-253">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="9f41e-254">Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="9f41e-254">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="9f41e-255">Skript [jQuery pro ověření](https://github.com/aspnet/jquery-validation-unobtrusive) je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in pro [ověření jQuery](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="9f41e-255">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validation](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="9f41e-256">Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9f41e-256">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="9f41e-257">Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu k vykreslování atributů HTML 5 `data-` pro prvky formuláře, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="9f41e-257">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="9f41e-258">jQuery nenáročné ověřování analyzuje `data-` atributy a předá logiku k ověřování jQuery a efektivně "kopíruje" logiku ověřování na straně serveru klientovi.</span><span class="sxs-lookup"><span data-stu-id="9f41e-258">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validation, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="9f41e-259">Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="9f41e-259">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="9f41e-260">Předchozí pomocník značek vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="9f41e-260">The preceding tag helpers render the following HTML:</span></span>

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

<span data-ttu-id="9f41e-261">Všimněte si, že `data-` atributy ve výstupu HTML odpovídají atributům ověřování pro `Movie.ReleaseDate` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="9f41e-261">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="9f41e-262">`data-val-required`Atribut obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání.</span><span class="sxs-lookup"><span data-stu-id="9f41e-262">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="9f41e-263">jQuery unpassing předá tuto hodnotu metodě pro ověření jQuery [Required ()](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodném **\<span>** prvku.</span><span class="sxs-lookup"><span data-stu-id="9f41e-263">jQuery Unobtrusive Validation passes this value to the jQuery Validation [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="9f41e-264">Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána `[DataType]` atributem.</span><span class="sxs-lookup"><span data-stu-id="9f41e-264">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="9f41e-265">Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček.</span><span class="sxs-lookup"><span data-stu-id="9f41e-265">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="9f41e-266">`[DataType]`atributy a podtřídy, jako je například `[EmailAddress]` umožňuje zadat chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-266">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="9f41e-267">Nenáročná ověření</span><span class="sxs-lookup"><span data-stu-id="9f41e-267">Unobtrusive validation</span></span>

<span data-ttu-id="9f41e-268">Informace o nenáročnosti ověřování najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span><span class="sxs-lookup"><span data-stu-id="9f41e-268">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="9f41e-269">Přidání ověřování do dynamických formulářů</span><span class="sxs-lookup"><span data-stu-id="9f41e-269">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="9f41e-270">jQuery – neúspěšné ověření projde logiku ověřování a parametry k ověření jQuery při prvním načtení stránky.</span><span class="sxs-lookup"><span data-stu-id="9f41e-270">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validation when the page first loads.</span></span> <span data-ttu-id="9f41e-271">Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích.</span><span class="sxs-lookup"><span data-stu-id="9f41e-271">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="9f41e-272">Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="9f41e-272">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="9f41e-273">Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.</span><span class="sxs-lookup"><span data-stu-id="9f41e-273">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
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

<span data-ttu-id="9f41e-274">`$.validator.unobtrusive.parse()`Metoda přijímá selektor jQuery pro svůj jeden argument.</span><span class="sxs-lookup"><span data-stu-id="9f41e-274">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="9f41e-275">Tato metoda oznamuje nenáročné ověřování, aby bylo možné analyzovat `data-` atributy formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="9f41e-275">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="9f41e-276">Hodnoty těchto atributů jsou poté předány modulu plug-in pro ověření jQuery.</span><span class="sxs-lookup"><span data-stu-id="9f41e-276">The values of those attributes are then passed to the jQuery Validation plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="9f41e-277">Přidání ověřování do dynamických ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="9f41e-277">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="9f41e-278">`$.validator.unobtrusive.parse()`Metoda funguje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, například `<input>` a `<select/>` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-278">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="9f41e-279">Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-279">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="9f41e-280">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="9f41e-280">Custom client-side validation</span></span>

<span data-ttu-id="9f41e-281">Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery.</span><span class="sxs-lookup"><span data-stu-id="9f41e-281">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validation adapter.</span></span> <span data-ttu-id="9f41e-282">Následující vzorový kód adaptéru byl napsán pro `[ClassicMovie]` `[ClassicMovieWithClientValidator]` atributy a, které byly představeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="9f41e-282">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="9f41e-283">Informace o tom, jak psát adaptéry, najdete v [dokumentaci k ověření jQuery](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="9f41e-283">For information about how to write adapters, see the [jQuery Validation documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="9f41e-284">Použití adaptéru pro dané pole se spustí pomocí `data-` atributů, které:</span><span class="sxs-lookup"><span data-stu-id="9f41e-284">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="9f41e-285">Označte pole jako podléhající ověřování ( `data-val="true"` ).</span><span class="sxs-lookup"><span data-stu-id="9f41e-285">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="9f41e-286">Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."` ).</span><span class="sxs-lookup"><span data-stu-id="9f41e-286">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="9f41e-287">Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-param1="value"` ).</span><span class="sxs-lookup"><span data-stu-id="9f41e-287">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="9f41e-288">Následující příklad ukazuje `data-` atributy pro atribut ukázkové aplikace `ClassicMovie` :</span><span class="sxs-lookup"><span data-stu-id="9f41e-288">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="9f41e-289">Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) se při vykreslování atributů používají informace z atributů ověřování `data-` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-289">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="9f41e-290">Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních `data-` atributů HTML:</span><span class="sxs-lookup"><span data-stu-id="9f41e-290">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="9f41e-291">Vytvořte třídu, která je odvozena z `AttributeAdapterBase<TAttribute>` třídy a třídu, která implementuje `IValidationAttributeAdapterProvider` , a zaregistrujte svůj atribut a jeho adaptér v di.</span><span class="sxs-lookup"><span data-stu-id="9f41e-291">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="9f41e-292">Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách.</span><span class="sxs-lookup"><span data-stu-id="9f41e-292">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="9f41e-293">Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.</span><span class="sxs-lookup"><span data-stu-id="9f41e-293">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="9f41e-294">Implementujte `IClientModelValidator` ve své `ValidationAttribute` třídě.</span><span class="sxs-lookup"><span data-stu-id="9f41e-294">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="9f41e-295">Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.</span><span class="sxs-lookup"><span data-stu-id="9f41e-295">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="9f41e-296">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="9f41e-296">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="9f41e-297">Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie` atributem v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9f41e-297">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="9f41e-298">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="9f41e-298">To add client validation by using this method:</span></span>

1. <span data-ttu-id="9f41e-299">Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut.</span><span class="sxs-lookup"><span data-stu-id="9f41e-299">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="9f41e-300">Odvodit třídu z [AttributeAdapterBase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="9f41e-300">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="9f41e-301">Vytvořte `AddValidation` metodu, která přidá `data-` atributy do vykresleného výstupu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-301">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="9f41e-302">Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> .</span><span class="sxs-lookup"><span data-stu-id="9f41e-302">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="9f41e-303">V `GetAttributeAdapter` metodě předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-303">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="9f41e-304">Zaregistrujte poskytovatele adaptéru pro DI v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9f41e-304">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="9f41e-305">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="9f41e-305">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="9f41e-306">Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovieWithClientValidator` atributem v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9f41e-306">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="9f41e-307">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="9f41e-307">To add client validation by using this method:</span></span>

* <span data-ttu-id="9f41e-308">Ve vlastním ověřovacím atributu implementujte `IClientModelValidator` rozhraní a vytvořte `AddValidation` metodu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-308">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="9f41e-309">V `AddValidation` metodě přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-309">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="9f41e-310">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="9f41e-310">Disable client-side validation</span></span>

<span data-ttu-id="9f41e-311">Následující kód zakáže ověřování klienta na Razor stránkách:</span><span class="sxs-lookup"><span data-stu-id="9f41e-311">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="9f41e-312">Další možnosti zakázání ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="9f41e-312">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="9f41e-313">Odkomentujte odkaz na `_ValidationScriptsPartial` ve všech souborech *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9f41e-313">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="9f41e-314">Odeberte obsah souboru *Pages\Shared \_ ValidationScriptsPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9f41e-314">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="9f41e-315">Předchozí přístup nebrání ověřování na straně klienta ASP.NET Core Identity Razor knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="9f41e-315">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="9f41e-316">Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="9f41e-316">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f41e-317">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9f41e-317">Additional resources</span></span>

* [<span data-ttu-id="9f41e-318">Obor názvů System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="9f41e-318">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="9f41e-319">Vazba modelu</span><span class="sxs-lookup"><span data-stu-id="9f41e-319">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9f41e-320">Tento článek vysvětluje, jak ověřit vstup uživatele v aplikaci ASP.NET Core MVC nebo Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="9f41e-320">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="9f41e-321">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9f41e-321">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="9f41e-322">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="9f41e-322">Model state</span></span>

<span data-ttu-id="9f41e-323">Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-323">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="9f41e-324">Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat (například "x" je zadáno v poli, které očekává celé číslo).</span><span class="sxs-lookup"><span data-stu-id="9f41e-324">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="9f41e-325">K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům (například hodnota 0 je zadána v poli, které očekává hodnocení mezi 1 a 5).</span><span class="sxs-lookup"><span data-stu-id="9f41e-325">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="9f41e-326">Vazba modelů i ověřování probíhá před provedením akce kontroleru nebo Razor metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="9f41e-326">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="9f41e-327">U webových aplikací je zodpovědností aplikace vhodné je kontrolovat `ModelState.IsValid` a reagovat.</span><span class="sxs-lookup"><span data-stu-id="9f41e-327">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="9f41e-328">Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="9f41e-328">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="9f41e-329">Řadiče webového rozhraní API nemusí kontrolovat, `ModelState.IsValid` jestli mají `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="9f41e-329">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="9f41e-330">V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o chybě, pokud stav modelu není platný.</span><span class="sxs-lookup"><span data-stu-id="9f41e-330">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="9f41e-331">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="9f41e-331">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="9f41e-332">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="9f41e-332">Rerun validation</span></span>

<span data-ttu-id="9f41e-333">Ověřování je automatické, ale můžete je chtít opakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="9f41e-333">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="9f41e-334">Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-334">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="9f41e-335">Chcete-li znovu spustit ověřování, zavolejte `TryValidateModel` metodu, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="9f41e-335">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="9f41e-336">Atributy ověřování</span><span class="sxs-lookup"><span data-stu-id="9f41e-336">Validation attributes</span></span>

<span data-ttu-id="9f41e-337">Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-337">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="9f41e-338">Následující příklad z [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) zobrazuje třídu modelu s poznámkou ověřování atributů.</span><span class="sxs-lookup"><span data-stu-id="9f41e-338">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="9f41e-339">`[ClassicMovie]`Atribut je vlastní ověřovací atribut a jsou integrovány ostatní.</span><span class="sxs-lookup"><span data-stu-id="9f41e-339">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="9f41e-340">Není zobrazeno `[ClassicMovie2]` , který ukazuje alternativní způsob implementace vlastního atributu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-340">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="9f41e-341">Předdefinované atributy</span><span class="sxs-lookup"><span data-stu-id="9f41e-341">Built-in attributes</span></span>

<span data-ttu-id="9f41e-342">Mezi předdefinované atributy ověřování patří:</span><span class="sxs-lookup"><span data-stu-id="9f41e-342">Built-in validation attributes include:</span></span>

* <span data-ttu-id="9f41e-343">`[CreditCard]`: Ověří, zda má vlastnost formát kreditní karty.</span><span class="sxs-lookup"><span data-stu-id="9f41e-343">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="9f41e-344">`[Compare]`: Ověří, že se dvě vlastnosti v modelu shodují.</span><span class="sxs-lookup"><span data-stu-id="9f41e-344">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="9f41e-345">Například soubor *Register.cshtml.cs* používá `[Compare]` k ověření, že se dvě zadaná hesla shodují.</span><span class="sxs-lookup"><span data-stu-id="9f41e-345">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="9f41e-346">[Generování Identity uživatelského rozhraní](xref:security/authentication/scaffold-identity) Chcete-li zobrazit kód registrace.</span><span class="sxs-lookup"><span data-stu-id="9f41e-346">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="9f41e-347">`[EmailAddress]`: Ověří, zda má vlastnost formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-347">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="9f41e-348">`[Phone]`: Ověří, zda má vlastnost formát telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="9f41e-348">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="9f41e-349">`[Range]`: Ověří, že hodnota vlastnosti spadá do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-349">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="9f41e-350">`[RegularExpression]`: Ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-350">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="9f41e-351">`[Required]`: Ověří, že pole nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="9f41e-351">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="9f41e-352">Podrobnosti o chování tohoto atributu naleznete v [ `[Required]` atributu Attribute](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="9f41e-352">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="9f41e-353">`[StringLength]`: Ověří, že hodnota vlastnosti řetězce nepřekračuje zadané omezení délky.</span><span class="sxs-lookup"><span data-stu-id="9f41e-353">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="9f41e-354">`[Url]`: Ověří, zda má vlastnost formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="9f41e-354">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="9f41e-355">`[Remote]`: Ověří vstup na straně klienta voláním metody Action na serveru.</span><span class="sxs-lookup"><span data-stu-id="9f41e-355">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="9f41e-356">Podrobnosti o chování tohoto atributu naleznete v [ `[Remote]` atributu Attribute](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="9f41e-356">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="9f41e-357">Při použití `[RegularExpression]` atributu s ověřováním na straně klienta je regulární výraz spuštěn v jazyce JavaScript na klientovi.</span><span class="sxs-lookup"><span data-stu-id="9f41e-357">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="9f41e-358">To znamená, že se použije chování pro porovnání [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) .</span><span class="sxs-lookup"><span data-stu-id="9f41e-358">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="9f41e-359">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/corefx/issues/42487).</span><span class="sxs-lookup"><span data-stu-id="9f41e-359">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="9f41e-360">Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="9f41e-360">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="9f41e-361">Chybové zprávy</span><span class="sxs-lookup"><span data-stu-id="9f41e-361">Error messages</span></span>

<span data-ttu-id="9f41e-362">Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="9f41e-362">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="9f41e-363">Například:</span><span class="sxs-lookup"><span data-stu-id="9f41e-363">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="9f41e-364">Interně atributy volají jako `String.Format` zástupný symbol pro název pole a někdy další zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="9f41e-364">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="9f41e-365">Například:</span><span class="sxs-lookup"><span data-stu-id="9f41e-365">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="9f41e-366">Při použití na `Name` vlastnost bude chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."</span><span class="sxs-lookup"><span data-stu-id="9f41e-366">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="9f41e-367">Chcete-li zjistit, které parametry jsou předány `String.Format` pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="9f41e-367">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="9f41e-368">[Required] – atribut</span><span class="sxs-lookup"><span data-stu-id="9f41e-368">[Required] attribute</span></span>

<span data-ttu-id="9f41e-369">Ve výchozím nastavení systém ověřování zpracovává parametry, které neumožňují hodnotu null nebo vlastnosti, jako by měly `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="9f41e-369">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="9f41e-370">[Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , jako `decimal` jsou a, `int` nejsou null.</span><span class="sxs-lookup"><span data-stu-id="9f41e-370">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="9f41e-371">[Požadováno] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="9f41e-371">[Required] validation on the server</span></span>

<span data-ttu-id="9f41e-372">Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="9f41e-372">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="9f41e-373">Pole, které nesmí mít hodnotu null, je vždy platné a chybová zpráva [required] se nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="9f41e-373">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="9f41e-374">Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, což vede k chybové zprávě, jako je například `The value '' is invalid` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-374">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="9f41e-375">Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="9f41e-375">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="9f41e-376">Převést pole na hodnotu null (například `decimal?` místo `decimal` ).</span><span class="sxs-lookup"><span data-stu-id="9f41e-376">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="9f41e-377">[Nullable \<T> ](/dotnet/csharp/programming-guide/nullable-types/) typy hodnot jsou zpracovány jako standardní typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="9f41e-377">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="9f41e-378">Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-378">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="9f41e-379">Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods> .</span><span class="sxs-lookup"><span data-stu-id="9f41e-379">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="9f41e-380">[Požadováno] ověřování na klientovi</span><span class="sxs-lookup"><span data-stu-id="9f41e-380">[Required] validation on the client</span></span>

<span data-ttu-id="9f41e-381">Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi.</span><span class="sxs-lookup"><span data-stu-id="9f41e-381">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="9f41e-382">Na klientovi:</span><span class="sxs-lookup"><span data-stu-id="9f41e-382">On the client:</span></span>

* <span data-ttu-id="9f41e-383">Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup.</span><span class="sxs-lookup"><span data-stu-id="9f41e-383">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="9f41e-384">Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="9f41e-384">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="9f41e-385">Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením.</span><span class="sxs-lookup"><span data-stu-id="9f41e-385">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="9f41e-386">Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.</span><span class="sxs-lookup"><span data-stu-id="9f41e-386">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="9f41e-387">Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za, i když mají `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="9f41e-387">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="9f41e-388">To znamená, že získáte ověřování na straně klienta i v případě, že atribut nepoužijete `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-388">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="9f41e-389">Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="9f41e-389">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="9f41e-390">Chcete-li zadat vlastní chybovou zprávu, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="9f41e-390">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="9f41e-391">[Remote] – atribut</span><span class="sxs-lookup"><span data-stu-id="9f41e-391">[Remote] attribute</span></span>

<span data-ttu-id="9f41e-392">`[Remote]`Atribut implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný.</span><span class="sxs-lookup"><span data-stu-id="9f41e-392">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="9f41e-393">Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.</span><span class="sxs-lookup"><span data-stu-id="9f41e-393">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="9f41e-394">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="9f41e-394">To implement remote validation:</span></span>

1. <span data-ttu-id="9f41e-395">Vytvořte metodu Action pro volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-395">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="9f41e-396">Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="9f41e-396">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="9f41e-397">`"true"`znamená, že jsou vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="9f41e-397">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="9f41e-398">`"false"`, `undefined` nebo `null` znamená, že vstup není platný.</span><span class="sxs-lookup"><span data-stu-id="9f41e-398">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="9f41e-399">Zobrazí výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-399">Display the default error message.</span></span>
   * <span data-ttu-id="9f41e-400">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="9f41e-400">Any other string means the input is invalid.</span></span> <span data-ttu-id="9f41e-401">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-401">Display the string as a custom error message.</span></span>

   <span data-ttu-id="9f41e-402">Tady je příklad metody akce, která vrací vlastní chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-402">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="9f41e-403">Ve třídě modelu poznámkujte vlastnost s `[Remote]` atributem, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-403">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="9f41e-404">`[Remote]`Atribut je v `Microsoft.AspNetCore.Mvc` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="9f41e-404">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="9f41e-405">Pokud nepoužíváte Metapackage nebo, nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) `Microsoft.AspNetCore.App` `Microsoft.AspNetCore.All` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-405">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="9f41e-406">Další pole</span><span class="sxs-lookup"><span data-stu-id="9f41e-406">Additional fields</span></span>

<span data-ttu-id="9f41e-407">`AdditionalFields`Vlastnost `[Remote]` atributu umožňuje ověřit kombinace polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="9f41e-407">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="9f41e-408">Pokud má `User` model například `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají odpovídající dvojici názvů.</span><span class="sxs-lookup"><span data-stu-id="9f41e-408">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="9f41e-409">Následující příklad ukazuje, jak použít `AdditionalFields` :</span><span class="sxs-lookup"><span data-stu-id="9f41e-409">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="9f41e-410">`AdditionalFields`lze nastavit explicitně na řetězce `"FirstName"` a `"LastName"` , ale použití operátoru [nameof](/dotnet/csharp/language-reference/keywords/nameof) zjednodušuje pozdější refaktoring.</span><span class="sxs-lookup"><span data-stu-id="9f41e-410">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="9f41e-411">Metoda Action pro toto ověření musí přijmout argumenty jméno a příjmení:</span><span class="sxs-lookup"><span data-stu-id="9f41e-411">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="9f41e-412">Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.</span><span class="sxs-lookup"><span data-stu-id="9f41e-412">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="9f41e-413">Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="9f41e-413">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="9f41e-414">Chcete-li například přidat `MiddleName` vlastnost do modelu, nastavte `[Remote]` atribut, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-414">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="9f41e-415">`AdditionalFields`Podobně jako všechny argumenty atributu musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="9f41e-415">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="9f41e-416">Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-416">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="9f41e-417">Alternativy k předdefinovaným atributům</span><span class="sxs-lookup"><span data-stu-id="9f41e-417">Alternatives to built-in attributes</span></span>

<span data-ttu-id="9f41e-418">Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:</span><span class="sxs-lookup"><span data-stu-id="9f41e-418">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="9f41e-419">[Vytvořte vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="9f41e-419">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="9f41e-420">[Implementujte IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="9f41e-420">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="9f41e-421">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="9f41e-421">Custom attributes</span></span>

<span data-ttu-id="9f41e-422">U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="9f41e-422">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="9f41e-423">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> , a přepište <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metodu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-423">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="9f41e-424">`IsValid`Metoda přijímá objekt s názvem *hodnota*, což je vstup, který má být ověřen.</span><span class="sxs-lookup"><span data-stu-id="9f41e-424">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="9f41e-425">Přetížení také přijímá `ValidationContext` objekt, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-425">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="9f41e-426">Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce.</span><span class="sxs-lookup"><span data-stu-id="9f41e-426">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="9f41e-427">`[ClassicMovie2]`Atribut nejprve zkontroluje Žánr a pokračuje pouze v případě, že je *klasický*.</span><span class="sxs-lookup"><span data-stu-id="9f41e-427">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="9f41e-428">U filmů identifikovaných jako klasických kontroluje datum vydání, aby se zajistilo, že není pozdější než limit předaný konstruktoru atributu.)</span><span class="sxs-lookup"><span data-stu-id="9f41e-428">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="9f41e-429">`movie`Proměnná v předchozím příkladu představuje `Movie` objekt, který obsahuje data z odesílání formuláře.</span><span class="sxs-lookup"><span data-stu-id="9f41e-429">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="9f41e-430">`IsValid`Metoda zkontroluje datum a Žánr.</span><span class="sxs-lookup"><span data-stu-id="9f41e-430">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="9f41e-431">Po úspěšném ověření `IsValid` vrátí `ValidationResult.Success` kód.</span><span class="sxs-lookup"><span data-stu-id="9f41e-431">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="9f41e-432">Pokud se ověření nepovede, `ValidationResult` vrátí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="9f41e-432">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="9f41e-433">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="9f41e-433">IValidatableObject</span></span>

<span data-ttu-id="9f41e-434">Předchozí příklad funguje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="9f41e-434">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="9f41e-435">Další možností pro ověřování na úrovni třídy je implementovat `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-435">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="9f41e-436">Ověřování uzlu nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="9f41e-436">Top-level node validation</span></span>

<span data-ttu-id="9f41e-437">Uzly nejvyšší úrovně zahrnují:</span><span class="sxs-lookup"><span data-stu-id="9f41e-437">Top-level nodes include:</span></span>

* <span data-ttu-id="9f41e-438">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="9f41e-438">Action parameters</span></span>
* <span data-ttu-id="9f41e-439">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="9f41e-439">Controller properties</span></span>
* <span data-ttu-id="9f41e-440">Parametry obslužné rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="9f41e-440">Page handler parameters</span></span>
* <span data-ttu-id="9f41e-441">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="9f41e-441">Page model properties</span></span>

<span data-ttu-id="9f41e-442">Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem.</span><span class="sxs-lookup"><span data-stu-id="9f41e-442">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="9f41e-443">V následujícím příkladu z ukázkové aplikace `VerifyPhone` Metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření `phone` parametru akce:</span><span class="sxs-lookup"><span data-stu-id="9f41e-443">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="9f41e-444">Uzly nejvyšší úrovně mohou být použity <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování.</span><span class="sxs-lookup"><span data-stu-id="9f41e-444">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="9f41e-445">V následujícím příkladu z ukázkové aplikace `CheckAge` určuje metoda, že `age` parametr musí být svázán z řetězce dotazu při odeslání formuláře:</span><span class="sxs-lookup"><span data-stu-id="9f41e-445">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="9f41e-446">Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře.</span><span class="sxs-lookup"><span data-stu-id="9f41e-446">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="9f41e-447">První formulář odešle `Age` hodnotu `99` jako řetězec dotazu: `https://localhost:5001/Users/CheckAge?Age=99` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-447">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="9f41e-448">Při odeslání správně formátovaného `age` parametru z řetězce dotazu se formulář ověří.</span><span class="sxs-lookup"><span data-stu-id="9f41e-448">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="9f41e-449">Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede.</span><span class="sxs-lookup"><span data-stu-id="9f41e-449">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="9f41e-450">Vazba se nezdařila, protože `age` parametr musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-450">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="9f41e-451">Při spuštění s `CompatibilityVersion.Version_2_1` nebo novějším je ověřování uzlů nejvyšší úrovně ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="9f41e-451">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="9f41e-452">V opačném případě je ověřování uzlu nejvyšší úrovně zakázané.</span><span class="sxs-lookup"><span data-stu-id="9f41e-452">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="9f41e-453">Výchozí možnost může být přepsána nastavením <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> vlastnosti v ( `Startup.ConfigureServices` ), jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="9f41e-453">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="9f41e-454">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="9f41e-454">Maximum errors</span></span>

<span data-ttu-id="9f41e-455">Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200).</span><span class="sxs-lookup"><span data-stu-id="9f41e-455">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="9f41e-456">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9f41e-456">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="9f41e-457">Maximální rekurze</span><span class="sxs-lookup"><span data-stu-id="9f41e-457">Maximum recursion</span></span>

<span data-ttu-id="9f41e-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>projde grafem objektu ověřovaného modelu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="9f41e-459">U modelů, které jsou velmi hlubokoelné nebo nekonečné rekurzivní, může ověřování způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="9f41e-459">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="9f41e-460">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku.</span><span class="sxs-lookup"><span data-stu-id="9f41e-460">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="9f41e-461">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 32 při spuštění v systému `CompatibilityVersion.Version_2_2` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="9f41e-461">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="9f41e-462">Pro starší verze je hodnota null, což znamená bez omezení hloubky.</span><span class="sxs-lookup"><span data-stu-id="9f41e-462">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="9f41e-463">Automatické krátké okruhy</span><span class="sxs-lookup"><span data-stu-id="9f41e-463">Automatic short-circuit</span></span>

<span data-ttu-id="9f41e-464">Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="9f41e-464">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="9f41e-465">Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních elementů (například `byte[]` , `string[]` , `Dictionary<string, string>` ) a složitých grafů objektů, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="9f41e-465">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="9f41e-466">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="9f41e-466">Disable validation</span></span>

<span data-ttu-id="9f41e-467">Zakázání ověřování:</span><span class="sxs-lookup"><span data-stu-id="9f41e-467">To disable validation:</span></span>

1. <span data-ttu-id="9f41e-468">Vytvořte implementaci `IObjectModelValidator` , která neoznačí žádná pole jako neplatnou.</span><span class="sxs-lookup"><span data-stu-id="9f41e-468">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="9f41e-469">Přidejte následující kód k `Startup.ConfigureServices` nahrazení výchozí `IObjectModelValidator` implementace v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="9f41e-469">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="9f41e-470">Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-470">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="9f41e-471">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="9f41e-471">Client-side validation</span></span>

<span data-ttu-id="9f41e-472">Ověřování na straně klienta brání odeslání, dokud není formulář platný.</span><span class="sxs-lookup"><span data-stu-id="9f41e-472">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="9f41e-473">Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="9f41e-473">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="9f41e-474">Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="9f41e-474">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="9f41e-475">Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="9f41e-475">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="9f41e-476">Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="9f41e-476">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="9f41e-477">Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9f41e-477">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="9f41e-478">Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu k vykreslování atributů HTML 5 `data-` pro prvky formuláře, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="9f41e-478">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="9f41e-479">jQuery nenáročné ověřování analyzuje `data-` atributy a předá logiku do příkazu jQuery Validate a efektivně kopíruje logiku ověřování na straně serveru do klienta.</span><span class="sxs-lookup"><span data-stu-id="9f41e-479">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="9f41e-480">Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="9f41e-480">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="9f41e-481">Předchozí pomocník značek vykresluje následující kód HTML.</span><span class="sxs-lookup"><span data-stu-id="9f41e-481">The preceding tag helpers render the following HTML.</span></span>

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

<span data-ttu-id="9f41e-482">Všimněte si, že `data-` atributy ve výstupu HTML odpovídají atributům ověřování pro `ReleaseDate` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="9f41e-482">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="9f41e-483">`data-val-required`Atribut obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání.</span><span class="sxs-lookup"><span data-stu-id="9f41e-483">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="9f41e-484">jQuery unpassing předá tuto hodnotu metodě jQuery [Required ()](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodném **\<span>** prvku.</span><span class="sxs-lookup"><span data-stu-id="9f41e-484">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="9f41e-485">Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána `[DataType]` atributem.</span><span class="sxs-lookup"><span data-stu-id="9f41e-485">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="9f41e-486">Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček.</span><span class="sxs-lookup"><span data-stu-id="9f41e-486">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="9f41e-487">`[DataType]`atributy a podtřídy, jako je například `[EmailAddress]` umožňuje zadat chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-487">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="9f41e-488">Přidání ověřování do dynamických formulářů</span><span class="sxs-lookup"><span data-stu-id="9f41e-488">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="9f41e-489">jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují.</span><span class="sxs-lookup"><span data-stu-id="9f41e-489">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="9f41e-490">Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích.</span><span class="sxs-lookup"><span data-stu-id="9f41e-490">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="9f41e-491">Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="9f41e-491">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="9f41e-492">Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.</span><span class="sxs-lookup"><span data-stu-id="9f41e-492">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
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

<span data-ttu-id="9f41e-493">`$.validator.unobtrusive.parse()`Metoda přijímá selektor jQuery pro svůj jeden argument.</span><span class="sxs-lookup"><span data-stu-id="9f41e-493">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="9f41e-494">Tato metoda oznamuje nenáročné ověřování, aby bylo možné analyzovat `data-` atributy formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="9f41e-494">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="9f41e-495">Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="9f41e-495">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="9f41e-496">Přidání ověřování do dynamických ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="9f41e-496">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="9f41e-497">`$.validator.unobtrusive.parse()`Metoda funguje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, například `<input>` a `<select/>` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-497">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="9f41e-498">Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-498">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
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

## <a name="custom-client-side-validation"></a><span data-ttu-id="9f41e-499">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="9f41e-499">Custom client-side validation</span></span>

<span data-ttu-id="9f41e-500">Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery.</span><span class="sxs-lookup"><span data-stu-id="9f41e-500">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="9f41e-501">Následující vzorový kód adaptéru byl napsán pro `ClassicMovie` `ClassicMovie2` atributy a, které byly představeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="9f41e-501">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="9f41e-502">Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="9f41e-502">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="9f41e-503">Použití adaptéru pro dané pole se spustí pomocí `data-` atributů, které:</span><span class="sxs-lookup"><span data-stu-id="9f41e-503">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="9f41e-504">Označte pole jako podléhající ověřování ( `data-val="true"` ).</span><span class="sxs-lookup"><span data-stu-id="9f41e-504">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="9f41e-505">Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."` ).</span><span class="sxs-lookup"><span data-stu-id="9f41e-505">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="9f41e-506">Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-parm1="value"` ).</span><span class="sxs-lookup"><span data-stu-id="9f41e-506">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="9f41e-507">Následující příklad ukazuje `data-` atributy pro atribut ukázkové aplikace `ClassicMovie` :</span><span class="sxs-lookup"><span data-stu-id="9f41e-507">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="9f41e-508">Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) se při vykreslování atributů používají informace z atributů ověřování `data-` .</span><span class="sxs-lookup"><span data-stu-id="9f41e-508">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="9f41e-509">Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních `data-` atributů HTML:</span><span class="sxs-lookup"><span data-stu-id="9f41e-509">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="9f41e-510">Vytvořte třídu, která je odvozena z `AttributeAdapterBase<TAttribute>` třídy a třídu, která implementuje `IValidationAttributeAdapterProvider` , a zaregistrujte svůj atribut a jeho adaptér v di.</span><span class="sxs-lookup"><span data-stu-id="9f41e-510">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="9f41e-511">Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách.</span><span class="sxs-lookup"><span data-stu-id="9f41e-511">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="9f41e-512">Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.</span><span class="sxs-lookup"><span data-stu-id="9f41e-512">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="9f41e-513">Implementujte `IClientModelValidator` ve své `ValidationAttribute` třídě.</span><span class="sxs-lookup"><span data-stu-id="9f41e-513">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="9f41e-514">Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.</span><span class="sxs-lookup"><span data-stu-id="9f41e-514">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="9f41e-515">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="9f41e-515">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="9f41e-516">Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie` atributem v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9f41e-516">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="9f41e-517">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="9f41e-517">To add client validation by using this method:</span></span>

1. <span data-ttu-id="9f41e-518">Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut.</span><span class="sxs-lookup"><span data-stu-id="9f41e-518">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="9f41e-519">Odvodit třídu z [AttributeAdapterBase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="9f41e-519">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="9f41e-520">Vytvořte `AddValidation` metodu, která přidá `data-` atributy do vykresleného výstupu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-520">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="9f41e-521">Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> .</span><span class="sxs-lookup"><span data-stu-id="9f41e-521">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="9f41e-522">V `GetAttributeAdapter` metodě předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-522">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="9f41e-523">Zaregistrujte poskytovatele adaptéru pro DI v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9f41e-523">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="9f41e-524">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="9f41e-524">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="9f41e-525">Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie2` atributem v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9f41e-525">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="9f41e-526">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="9f41e-526">To add client validation by using this method:</span></span>

* <span data-ttu-id="9f41e-527">Ve vlastním ověřovacím atributu implementujte `IClientModelValidator` rozhraní a vytvořte `AddValidation` metodu.</span><span class="sxs-lookup"><span data-stu-id="9f41e-527">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="9f41e-528">V `AddValidation` metodě přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9f41e-528">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="9f41e-529">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="9f41e-529">Disable client-side validation</span></span>

<span data-ttu-id="9f41e-530">Následující kód zakáže ověřování klienta v zobrazeních MVC:</span><span class="sxs-lookup"><span data-stu-id="9f41e-530">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="9f41e-531">A na Razor stránkách:</span><span class="sxs-lookup"><span data-stu-id="9f41e-531">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="9f41e-532">Další možností pro zakázání ověřování klienta je přidat komentář k odkazu do `_ValidationScriptsPartial` souboru *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="9f41e-532">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f41e-533">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9f41e-533">Additional resources</span></span>

* [<span data-ttu-id="9f41e-534">Obor názvů System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="9f41e-534">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="9f41e-535">Vazba modelu</span><span class="sxs-lookup"><span data-stu-id="9f41e-535">Model Binding</span></span>](model-binding.md)

::: moniker-end

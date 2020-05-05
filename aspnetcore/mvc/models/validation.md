---
title: Ověření modelu ve ASP.NET Core MVC
author: rick-anderson
description: Přečtěte si o ověřování modelu v ASP.NET Core Razor MVC a stránkách.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/validation
ms.openlocfilehash: a0f7c070514de26ae007526a5587c13d26d1eb1b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777173"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="f4189-103">Ověření modelu ve ASP.NET Core MVC a Razor stránkách</span><span class="sxs-lookup"><span data-stu-id="f4189-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f4189-104">Od [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="f4189-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="f4189-105">Tento článek vysvětluje, jak ověřit vstup uživatele v aplikaci ASP.NET Core MVC nebo Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="f4189-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="f4189-106">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f4189-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="f4189-107">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="f4189-107">Model state</span></span>

<span data-ttu-id="f4189-108">Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="f4189-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="f4189-109">Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat.</span><span class="sxs-lookup"><span data-stu-id="f4189-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="f4189-110">Například "x" je zadáno v poli typu Integer.</span><span class="sxs-lookup"><span data-stu-id="f4189-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="f4189-111">K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům.</span><span class="sxs-lookup"><span data-stu-id="f4189-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="f4189-112">Například hodnota 0 se zadává v poli, které očekává hodnocení mezi 1 a 5.</span><span class="sxs-lookup"><span data-stu-id="f4189-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="f4189-113">Vazba modelů i ověřování modelu proběhne před provedením akce kontroleru nebo metody obslužné rutiny Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="f4189-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="f4189-114">U webových aplikací je zodpovědností aplikace vhodné je kontrolovat `ModelState.IsValid` a reagovat.</span><span class="sxs-lookup"><span data-stu-id="f4189-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="f4189-115">Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="f4189-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="f4189-116">Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid` , jestli mají `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="f4189-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="f4189-117">V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o chybě, pokud stav modelu není platný.</span><span class="sxs-lookup"><span data-stu-id="f4189-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="f4189-118">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="f4189-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="f4189-119">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="f4189-119">Rerun validation</span></span>

<span data-ttu-id="f4189-120">Ověřování je automatické, ale můžete je chtít opakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="f4189-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="f4189-121">Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f4189-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="f4189-122">Chcete-li znovu spustit ověřování `TryValidateModel` , zavolejte metodu, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="f4189-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="f4189-123">Atributy ověřování</span><span class="sxs-lookup"><span data-stu-id="f4189-123">Validation attributes</span></span>

<span data-ttu-id="f4189-124">Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="f4189-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="f4189-125">Následující příklad z ukázkové aplikace zobrazuje třídu modelu s poznámkou ověřování atributů.</span><span class="sxs-lookup"><span data-stu-id="f4189-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="f4189-126">`[ClassicMovie]` Atribut je vlastní ověřovací atribut a jsou integrovány ostatní.</span><span class="sxs-lookup"><span data-stu-id="f4189-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="f4189-127">Není zobrazeno `[ClassicMovieWithClientValidator]`.</span><span class="sxs-lookup"><span data-stu-id="f4189-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="f4189-128">`[ClassicMovieWithClientValidator]`ukazuje alternativní způsob implementace vlastního atributu.</span><span class="sxs-lookup"><span data-stu-id="f4189-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="f4189-129">Předdefinované atributy</span><span class="sxs-lookup"><span data-stu-id="f4189-129">Built-in attributes</span></span>

<span data-ttu-id="f4189-130">Tady jsou některé z vestavěných ověřovacích atributů:</span><span class="sxs-lookup"><span data-stu-id="f4189-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="f4189-131">`[CreditCard]`: Ověří, zda má vlastnost formát kreditní karty.</span><span class="sxs-lookup"><span data-stu-id="f4189-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="f4189-132">`[Compare]`: Ověří, že se dvě vlastnosti v modelu shodují.</span><span class="sxs-lookup"><span data-stu-id="f4189-132">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="f4189-133">`[EmailAddress]`: Ověří, zda má vlastnost formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="f4189-133">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="f4189-134">`[Phone]`: Ověří, zda má vlastnost formát telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="f4189-134">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="f4189-135">`[Range]`: Ověří, že hodnota vlastnosti spadá do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="f4189-135">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="f4189-136">`[RegularExpression]`: Ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="f4189-136">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="f4189-137">`[Required]`: Ověří, že pole nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f4189-137">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="f4189-138">Podrobnosti o chování tohoto atributu naleznete v [ `[Required]` atributu Attribute](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="f4189-138">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="f4189-139">`[StringLength]`: Ověří, že hodnota vlastnosti řetězce nepřekračuje zadané omezení délky.</span><span class="sxs-lookup"><span data-stu-id="f4189-139">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="f4189-140">`[Url]`: Ověří, zda má vlastnost formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="f4189-140">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="f4189-141">`[Remote]`: Ověří vstup na straně klienta voláním metody Action na serveru.</span><span class="sxs-lookup"><span data-stu-id="f4189-141">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="f4189-142">Podrobnosti o chování tohoto atributu naleznete v [ `[Remote]` atributu Attribute](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="f4189-142">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="f4189-143">Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="f4189-143">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="f4189-144">Chybové zprávy</span><span class="sxs-lookup"><span data-stu-id="f4189-144">Error messages</span></span>

<span data-ttu-id="f4189-145">Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="f4189-145">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="f4189-146">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f4189-146">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="f4189-147">Interně atributy volají `String.Format` jako zástupný symbol pro název pole a někdy další zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="f4189-147">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="f4189-148">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f4189-148">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="f4189-149">Při použití na `Name` vlastnost bude chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."</span><span class="sxs-lookup"><span data-stu-id="f4189-149">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="f4189-150">Chcete-li zjistit, které parametry jsou `String.Format` předány pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="f4189-150">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="f4189-151">[Required] – atribut</span><span class="sxs-lookup"><span data-stu-id="f4189-151">[Required] attribute</span></span>

<span data-ttu-id="f4189-152">Systém ověřování v .NET Core 3,0 a novějším zpracovává parametry bez hodnoty null nebo vázané vlastnosti, jako kdyby měl `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="f4189-152">The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="f4189-153">[Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , jako `decimal` jsou `int` a, nejsou null.</span><span class="sxs-lookup"><span data-stu-id="f4189-153">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span> <span data-ttu-id="f4189-154">Toto chování je možné zakázat konfigurací <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> v `Startup.ConfigureServices`nástroji:</span><span class="sxs-lookup"><span data-stu-id="f4189-154">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="f4189-155">[Požadováno] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="f4189-155">[Required] validation on the server</span></span>

<span data-ttu-id="f4189-156">Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f4189-156">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="f4189-157">Pole, které nesmí mít hodnotu null, je vždy platné `[Required]` a chybová zpráva atributu se nikdy nezobrazuje.</span><span class="sxs-lookup"><span data-stu-id="f4189-157">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="f4189-158">Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, což vede k chybové zprávě, `The value '' is invalid`jako je například.</span><span class="sxs-lookup"><span data-stu-id="f4189-158">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="f4189-159">Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="f4189-159">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="f4189-160">Převést pole na hodnotu null (například `decimal?` místo `decimal`).</span><span class="sxs-lookup"><span data-stu-id="f4189-160">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="f4189-161">Typy hodnot s [možnou hodnotou null\<>](/dotnet/csharp/programming-guide/nullable-types/) jsou považovány za standardní typy s možnou hodnotou null</span><span class="sxs-lookup"><span data-stu-id="f4189-161">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="f4189-162">Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-162">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="f4189-163">Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>v tématu.</span><span class="sxs-lookup"><span data-stu-id="f4189-163">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="f4189-164">[Požadováno] ověřování na klientovi</span><span class="sxs-lookup"><span data-stu-id="f4189-164">[Required] validation on the client</span></span>

<span data-ttu-id="f4189-165">Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi.</span><span class="sxs-lookup"><span data-stu-id="f4189-165">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="f4189-166">Na klientovi:</span><span class="sxs-lookup"><span data-stu-id="f4189-166">On the client:</span></span>

* <span data-ttu-id="f4189-167">Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup.</span><span class="sxs-lookup"><span data-stu-id="f4189-167">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="f4189-168">Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="f4189-168">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="f4189-169">Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením.</span><span class="sxs-lookup"><span data-stu-id="f4189-169">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="f4189-170">Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.</span><span class="sxs-lookup"><span data-stu-id="f4189-170">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="f4189-171">Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za `[Required]` , i když mají atribut.</span><span class="sxs-lookup"><span data-stu-id="f4189-171">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="f4189-172">To znamená, že získáte ověřování na straně klienta i v `[Required]` případě, že atribut nepoužijete.</span><span class="sxs-lookup"><span data-stu-id="f4189-172">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="f4189-173">Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="f4189-173">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="f4189-174">Chcete-li zadat vlastní chybovou zprávu, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="f4189-174">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="f4189-175">[Remote] – atribut</span><span class="sxs-lookup"><span data-stu-id="f4189-175">[Remote] attribute</span></span>

<span data-ttu-id="f4189-176">`[Remote]` Atribut implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný.</span><span class="sxs-lookup"><span data-stu-id="f4189-176">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="f4189-177">Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.</span><span class="sxs-lookup"><span data-stu-id="f4189-177">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="f4189-178">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="f4189-178">To implement remote validation:</span></span>

1. <span data-ttu-id="f4189-179">Vytvořte metodu Action pro volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="f4189-179">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="f4189-180">Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="f4189-180">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="f4189-181">`true`znamená, že jsou vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="f4189-181">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="f4189-182">`false`, `undefined`nebo `null` znamená, že vstup není platný.</span><span class="sxs-lookup"><span data-stu-id="f4189-182">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="f4189-183">Zobrazí výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="f4189-183">Display the default error message.</span></span>
   * <span data-ttu-id="f4189-184">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="f4189-184">Any other string means the input is invalid.</span></span> <span data-ttu-id="f4189-185">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="f4189-185">Display the string as a custom error message.</span></span>

   <span data-ttu-id="f4189-186">Tady je příklad metody akce, která vrací vlastní chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="f4189-186">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="f4189-187">Ve třídě modelu poznámkujte vlastnost s `[Remote]` atributem, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-187">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="f4189-188">`[Remote]` Atribut je v `Microsoft.AspNetCore.Mvc` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f4189-188">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="f4189-189">Další pole</span><span class="sxs-lookup"><span data-stu-id="f4189-189">Additional fields</span></span>

<span data-ttu-id="f4189-190">`AdditionalFields` Vlastnost `[Remote]` atributu umožňuje ověřit kombinace polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="f4189-190">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="f4189-191">Pokud má `User` model například `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají odpovídající dvojici názvů.</span><span class="sxs-lookup"><span data-stu-id="f4189-191">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="f4189-192">Následující příklad ukazuje, jak použít `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="f4189-192">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="f4189-193">`AdditionalFields`lze nastavit explicitně na řetězce "FirstName" a "LastName", ale pomocí operátoru [nameof](/dotnet/csharp/language-reference/keywords/nameof) se zjednoduší pozdější refaktoring.</span><span class="sxs-lookup"><span data-stu-id="f4189-193">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="f4189-194">Metoda Action pro toto ověření musí přijmout oba `firstName` argumenty a `lastName` :</span><span class="sxs-lookup"><span data-stu-id="f4189-194">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="f4189-195">Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.</span><span class="sxs-lookup"><span data-stu-id="f4189-195">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="f4189-196">Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="f4189-196">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="f4189-197">Chcete-li například přidat `MiddleName` vlastnost do modelu, nastavte `[Remote]` atribut, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-197">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="f4189-198">`AdditionalFields`Podobně jako všechny argumenty atributu musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="f4189-198">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="f4189-199">Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="f4189-199">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="f4189-200">Alternativy k předdefinovaným atributům</span><span class="sxs-lookup"><span data-stu-id="f4189-200">Alternatives to built-in attributes</span></span>

<span data-ttu-id="f4189-201">Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:</span><span class="sxs-lookup"><span data-stu-id="f4189-201">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="f4189-202">[Vytvořte vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="f4189-202">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="f4189-203">[Implementujte IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="f4189-203">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="f4189-204">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="f4189-204">Custom attributes</span></span>

<span data-ttu-id="f4189-205">U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="f4189-205">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="f4189-206">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, a přepište <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metodu.</span><span class="sxs-lookup"><span data-stu-id="f4189-206">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="f4189-207">`IsValid` Metoda přijímá objekt s názvem *hodnota*, což je vstup, který má být ověřen.</span><span class="sxs-lookup"><span data-stu-id="f4189-207">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="f4189-208">Přetížení také přijímá `ValidationContext` objekt, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="f4189-208">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="f4189-209">Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce.</span><span class="sxs-lookup"><span data-stu-id="f4189-209">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="f4189-210">`[ClassicMovie]` Atribut:</span><span class="sxs-lookup"><span data-stu-id="f4189-210">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="f4189-211">Se spouští jenom na serveru.</span><span class="sxs-lookup"><span data-stu-id="f4189-211">Is only run on the server.</span></span>
* <span data-ttu-id="f4189-212">U klasických filmů ověří datum vydání:</span><span class="sxs-lookup"><span data-stu-id="f4189-212">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="f4189-213">`movie` Proměnná v předchozím příkladu představuje `Movie` objekt, který obsahuje data z odesílání formuláře.</span><span class="sxs-lookup"><span data-stu-id="f4189-213">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="f4189-214">Pokud se ověření nepovede `ValidationResult` , vrátí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="f4189-214">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="f4189-215">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="f4189-215">IValidatableObject</span></span>

<span data-ttu-id="f4189-216">Předchozí příklad funguje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="f4189-216">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="f4189-217">Další možností pro ověřování na úrovni třídy je implementovat `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-217">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="f4189-218">Ověřování uzlu nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="f4189-218">Top-level node validation</span></span>

<span data-ttu-id="f4189-219">Uzly nejvyšší úrovně zahrnují:</span><span class="sxs-lookup"><span data-stu-id="f4189-219">Top-level nodes include:</span></span>

* <span data-ttu-id="f4189-220">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="f4189-220">Action parameters</span></span>
* <span data-ttu-id="f4189-221">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="f4189-221">Controller properties</span></span>
* <span data-ttu-id="f4189-222">Parametry obslužné rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="f4189-222">Page handler parameters</span></span>
* <span data-ttu-id="f4189-223">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="f4189-223">Page model properties</span></span>

<span data-ttu-id="f4189-224">Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem.</span><span class="sxs-lookup"><span data-stu-id="f4189-224">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="f4189-225">V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření parametru `phone` akce:</span><span class="sxs-lookup"><span data-stu-id="f4189-225">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="f4189-226">Uzly nejvyšší úrovně mohou být použity <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování.</span><span class="sxs-lookup"><span data-stu-id="f4189-226">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="f4189-227">V následujícím příkladu z ukázkové aplikace určuje `CheckAge` metoda, že `age` parametr musí být svázán z řetězce dotazu při odeslání formuláře:</span><span class="sxs-lookup"><span data-stu-id="f4189-227">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="f4189-228">Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře.</span><span class="sxs-lookup"><span data-stu-id="f4189-228">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="f4189-229">První formulář odešle `Age` hodnotu `99` jako parametr řetězce dotazu:. `https://localhost:5001/Users/CheckAge?Age=99`</span><span class="sxs-lookup"><span data-stu-id="f4189-229">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="f4189-230">Při odeslání správně formátovaného `age` parametru z řetězce dotazu se formulář ověří.</span><span class="sxs-lookup"><span data-stu-id="f4189-230">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="f4189-231">Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede.</span><span class="sxs-lookup"><span data-stu-id="f4189-231">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="f4189-232">Vazba se nezdařila, `age` protože parametr musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f4189-232">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="f4189-233">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="f4189-233">Maximum errors</span></span>

<span data-ttu-id="f4189-234">Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200).</span><span class="sxs-lookup"><span data-stu-id="f4189-234">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="f4189-235">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f4189-235">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="f4189-236">Maximální rekurze</span><span class="sxs-lookup"><span data-stu-id="f4189-236">Maximum recursion</span></span>

<span data-ttu-id="f4189-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>projde grafem objektu ověřovaného modelu.</span><span class="sxs-lookup"><span data-stu-id="f4189-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="f4189-238">U modelů, které jsou hluboko nebo jsou nekonečně rekurzivní, může ověřování způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="f4189-238">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="f4189-239">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku.</span><span class="sxs-lookup"><span data-stu-id="f4189-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="f4189-240">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 32.</span><span class="sxs-lookup"><span data-stu-id="f4189-240">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="f4189-241">Automatické krátké okruhy</span><span class="sxs-lookup"><span data-stu-id="f4189-241">Automatic short-circuit</span></span>

<span data-ttu-id="f4189-242">Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="f4189-242">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="f4189-243">Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních elementů ( `byte[]`například `string[]`, `Dictionary<string, string>`,) a složitých grafů objektů, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="f4189-243">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="f4189-244">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="f4189-244">Disable validation</span></span>

<span data-ttu-id="f4189-245">Zakázání ověřování:</span><span class="sxs-lookup"><span data-stu-id="f4189-245">To disable validation:</span></span>

1. <span data-ttu-id="f4189-246">Vytvořte implementaci `IObjectModelValidator` , která neoznačí žádná pole jako neplatnou.</span><span class="sxs-lookup"><span data-stu-id="f4189-246">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="f4189-247">Přidejte následující kód k `Startup.ConfigureServices` nahrazení výchozí `IObjectModelValidator` implementace v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="f4189-247">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="f4189-248">Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="f4189-248">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="f4189-249">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="f4189-249">Client-side validation</span></span>

<span data-ttu-id="f4189-250">Ověřování na straně klienta brání odeslání, dokud není formulář platný.</span><span class="sxs-lookup"><span data-stu-id="f4189-250">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="f4189-251">Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="f4189-251">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="f4189-252">Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="f4189-252">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="f4189-253">Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="f4189-253">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="f4189-254">Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="f4189-254">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="f4189-255">Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f4189-255">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="f4189-256">Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu k vykreslování atributů HTML 5 `data-` pro prvky formuláře, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="f4189-256">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="f4189-257">jQuery nenáročné ověřování analyzuje `data-` atributy a předá logiku do příkazu jQuery Validate a efektivně kopíruje logiku ověřování na straně serveru do klienta.</span><span class="sxs-lookup"><span data-stu-id="f4189-257">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="f4189-258">Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="f4189-258">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="f4189-259">Předchozí pomocník značek vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="f4189-259">The preceding tag helpers render the following HTML:</span></span>

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

<span data-ttu-id="f4189-260">Všimněte si, `data-` že atributy ve výstupu HTML odpovídají atributům ověřování pro `Movie.ReleaseDate` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f4189-260">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="f4189-261">`data-val-required` Atribut obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání.</span><span class="sxs-lookup"><span data-stu-id="f4189-261">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="f4189-262">jQuery unpassing předá tuto hodnotu metodě jQuery [Required ()](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodném \*\* \<elementu span>\*\* .</span><span class="sxs-lookup"><span data-stu-id="f4189-262">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="f4189-263">Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána `[DataType]` atributem.</span><span class="sxs-lookup"><span data-stu-id="f4189-263">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="f4189-264">Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček.</span><span class="sxs-lookup"><span data-stu-id="f4189-264">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="f4189-265">`[DataType]`atributy a podtřídy, jako je `[EmailAddress]` například umožňuje zadat chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="f4189-265">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="f4189-266">Nenáročná ověření</span><span class="sxs-lookup"><span data-stu-id="f4189-266">Unobtrusive validation</span></span>

<span data-ttu-id="f4189-267">Informace o nenáročnosti ověřování najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span><span class="sxs-lookup"><span data-stu-id="f4189-267">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="f4189-268">Přidání ověřování do dynamických formulářů</span><span class="sxs-lookup"><span data-stu-id="f4189-268">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="f4189-269">jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují.</span><span class="sxs-lookup"><span data-stu-id="f4189-269">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="f4189-270">Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích.</span><span class="sxs-lookup"><span data-stu-id="f4189-270">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="f4189-271">Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="f4189-271">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="f4189-272">Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.</span><span class="sxs-lookup"><span data-stu-id="f4189-272">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="f4189-273">`$.validator.unobtrusive.parse()` Metoda přijímá selektor jQuery pro svůj jeden argument.</span><span class="sxs-lookup"><span data-stu-id="f4189-273">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="f4189-274">Tato metoda oznamuje nenáročné ověřování, aby bylo `data-` možné analyzovat atributy formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="f4189-274">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="f4189-275">Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="f4189-275">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="f4189-276">Přidání ověřování do dynamických ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="f4189-276">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="f4189-277">`$.validator.unobtrusive.parse()` Metoda funguje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, například `<input>` a `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="f4189-277">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="f4189-278">Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-278">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="f4189-279">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="f4189-279">Custom client-side validation</span></span>

<span data-ttu-id="f4189-280">Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery.</span><span class="sxs-lookup"><span data-stu-id="f4189-280">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="f4189-281">Následující vzorový kód adaptéru byl napsán pro atributy `[ClassicMovie]` a `[ClassicMovieWithClientValidator]` , které byly představeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="f4189-281">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="f4189-282">Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="f4189-282">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="f4189-283">Použití adaptéru pro dané pole se spustí pomocí `data-` atributů, které:</span><span class="sxs-lookup"><span data-stu-id="f4189-283">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="f4189-284">Označte pole jako podléhající ověřování (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="f4189-284">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="f4189-285">Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="f4189-285">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="f4189-286">Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-param1="value"`).</span><span class="sxs-lookup"><span data-stu-id="f4189-286">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="f4189-287">Následující příklad ukazuje `data-` atributy pro `ClassicMovie` atribut ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="f4189-287">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="f4189-288">Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) se při vykreslování `data-` atributů používají informace z atributů ověřování.</span><span class="sxs-lookup"><span data-stu-id="f4189-288">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="f4189-289">Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních `data-` atributů HTML:</span><span class="sxs-lookup"><span data-stu-id="f4189-289">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="f4189-290">Vytvořte třídu, která je odvozena `AttributeAdapterBase<TAttribute>` z třídy a třídu, `IValidationAttributeAdapterProvider`která implementuje, a zaregistrujte svůj atribut a jeho adaptér v di.</span><span class="sxs-lookup"><span data-stu-id="f4189-290">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="f4189-291">Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách.</span><span class="sxs-lookup"><span data-stu-id="f4189-291">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="f4189-292">Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.</span><span class="sxs-lookup"><span data-stu-id="f4189-292">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="f4189-293">Implementujte `IClientModelValidator` ve `ValidationAttribute` své třídě.</span><span class="sxs-lookup"><span data-stu-id="f4189-293">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="f4189-294">Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.</span><span class="sxs-lookup"><span data-stu-id="f4189-294">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="f4189-295">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="f4189-295">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="f4189-296">Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie` atributem v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f4189-296">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="f4189-297">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="f4189-297">To add client validation by using this method:</span></span>

1. <span data-ttu-id="f4189-298">Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut.</span><span class="sxs-lookup"><span data-stu-id="f4189-298">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="f4189-299">Odvodit třídu z [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="f4189-299">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="f4189-300">Vytvořte `AddValidation` metodu, která přidá `data-` atributy do vykresleného výstupu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-300">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="f4189-301">Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="f4189-301">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="f4189-302">V `GetAttributeAdapter` metodě předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-302">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="f4189-303">Zaregistrujte poskytovatele adaptéru pro DI `Startup.ConfigureServices`v:</span><span class="sxs-lookup"><span data-stu-id="f4189-303">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="f4189-304">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="f4189-304">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="f4189-305">Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovieWithClientValidator` atributem v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f4189-305">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="f4189-306">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="f4189-306">To add client validation by using this method:</span></span>

* <span data-ttu-id="f4189-307">Ve vlastním ověřovacím atributu implementujte `IClientModelValidator` rozhraní a vytvořte `AddValidation` metodu.</span><span class="sxs-lookup"><span data-stu-id="f4189-307">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="f4189-308">V `AddValidation` metodě přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-308">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="f4189-309">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="f4189-309">Disable client-side validation</span></span>

<span data-ttu-id="f4189-310">Následující kód zakáže ověřování klienta na Razor stránkách:</span><span class="sxs-lookup"><span data-stu-id="f4189-310">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="f4189-311">Další možnosti zakázání ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="f4189-311">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="f4189-312">Odkomentujte odkaz na `_ValidationScriptsPartial` ve všech souborech *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f4189-312">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="f4189-313">Odeberte obsah souboru *Pages\Shared\_ValidationScriptsPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f4189-313">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="f4189-314">Předchozí přístup nebrání ověřování na straně klienta ASP.NET Core Identity Razor knihovny tříd.</span><span class="sxs-lookup"><span data-stu-id="f4189-314">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="f4189-315">Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="f4189-315">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f4189-316">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f4189-316">Additional resources</span></span>

* [<span data-ttu-id="f4189-317">Obor názvů System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="f4189-317">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="f4189-318">Vazba modelu</span><span class="sxs-lookup"><span data-stu-id="f4189-318">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f4189-319">Tento článek vysvětluje, jak ověřit vstup uživatele v aplikaci ASP.NET Core MVC nebo Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="f4189-319">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="f4189-320">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f4189-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="f4189-321">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="f4189-321">Model state</span></span>

<span data-ttu-id="f4189-322">Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="f4189-322">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="f4189-323">Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat (například "x" je zadáno v poli, které očekává celé číslo).</span><span class="sxs-lookup"><span data-stu-id="f4189-323">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="f4189-324">K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům (například hodnota 0 je zadána v poli, které očekává hodnocení mezi 1 a 5).</span><span class="sxs-lookup"><span data-stu-id="f4189-324">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="f4189-325">Vazba modelů i ověřování probíhá před provedením akce kontroleru nebo metody obslužné rutiny Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="f4189-325">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="f4189-326">U webových aplikací je zodpovědností aplikace vhodné je kontrolovat `ModelState.IsValid` a reagovat.</span><span class="sxs-lookup"><span data-stu-id="f4189-326">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="f4189-327">Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="f4189-327">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="f4189-328">Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid` , jestli mají `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="f4189-328">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="f4189-329">V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o chybě, pokud stav modelu není platný.</span><span class="sxs-lookup"><span data-stu-id="f4189-329">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="f4189-330">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="f4189-330">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="f4189-331">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="f4189-331">Rerun validation</span></span>

<span data-ttu-id="f4189-332">Ověřování je automatické, ale můžete je chtít opakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="f4189-332">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="f4189-333">Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f4189-333">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="f4189-334">Chcete-li znovu spustit ověřování `TryValidateModel` , zavolejte metodu, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="f4189-334">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="f4189-335">Atributy ověřování</span><span class="sxs-lookup"><span data-stu-id="f4189-335">Validation attributes</span></span>

<span data-ttu-id="f4189-336">Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="f4189-336">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="f4189-337">Následující příklad z [ukázkové aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) zobrazuje třídu modelu s poznámkou ověřování atributů.</span><span class="sxs-lookup"><span data-stu-id="f4189-337">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="f4189-338">`[ClassicMovie]` Atribut je vlastní ověřovací atribut a jsou integrovány ostatní.</span><span class="sxs-lookup"><span data-stu-id="f4189-338">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="f4189-339">Není zobrazeno `[ClassicMovie2]`, který ukazuje alternativní způsob implementace vlastního atributu.</span><span class="sxs-lookup"><span data-stu-id="f4189-339">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="f4189-340">Předdefinované atributy</span><span class="sxs-lookup"><span data-stu-id="f4189-340">Built-in attributes</span></span>

<span data-ttu-id="f4189-341">Mezi předdefinované atributy ověřování patří:</span><span class="sxs-lookup"><span data-stu-id="f4189-341">Built-in validation attributes include:</span></span>

* <span data-ttu-id="f4189-342">`[CreditCard]`: Ověří, zda má vlastnost formát kreditní karty.</span><span class="sxs-lookup"><span data-stu-id="f4189-342">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="f4189-343">`[Compare]`: Ověří, že se dvě vlastnosti v modelu shodují.</span><span class="sxs-lookup"><span data-stu-id="f4189-343">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="f4189-344">Například soubor *Register.cshtml.cs* používá `[Compare]` k ověření, že se dvě zadaná hesla shodují.</span><span class="sxs-lookup"><span data-stu-id="f4189-344">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="f4189-345">[Uživatelské Identity rozhraní](xref:security/authentication/scaffold-identity) pro zobrazení kódu registrace.</span><span class="sxs-lookup"><span data-stu-id="f4189-345">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="f4189-346">`[EmailAddress]`: Ověří, zda má vlastnost formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="f4189-346">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="f4189-347">`[Phone]`: Ověří, zda má vlastnost formát telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="f4189-347">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="f4189-348">`[Range]`: Ověří, že hodnota vlastnosti spadá do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="f4189-348">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="f4189-349">`[RegularExpression]`: Ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="f4189-349">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="f4189-350">`[Required]`: Ověří, že pole nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f4189-350">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="f4189-351">Podrobnosti o chování tohoto atributu naleznete v [ `[Required]` atributu Attribute](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="f4189-351">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="f4189-352">`[StringLength]`: Ověří, že hodnota vlastnosti řetězce nepřekračuje zadané omezení délky.</span><span class="sxs-lookup"><span data-stu-id="f4189-352">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="f4189-353">`[Url]`: Ověří, zda má vlastnost formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="f4189-353">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="f4189-354">`[Remote]`: Ověří vstup na straně klienta voláním metody Action na serveru.</span><span class="sxs-lookup"><span data-stu-id="f4189-354">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="f4189-355">Podrobnosti o chování tohoto atributu naleznete v [ `[Remote]` atributu Attribute](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="f4189-355">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="f4189-356">Při použití `[RegularExpression]` atributu s ověřováním na straně klienta je regulární výraz spuštěn v jazyce JavaScript na klientovi.</span><span class="sxs-lookup"><span data-stu-id="f4189-356">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="f4189-357">To znamená, že se použije chování pro porovnání [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) .</span><span class="sxs-lookup"><span data-stu-id="f4189-357">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="f4189-358">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/corefx/issues/42487).</span><span class="sxs-lookup"><span data-stu-id="f4189-358">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="f4189-359">Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="f4189-359">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="f4189-360">Chybové zprávy</span><span class="sxs-lookup"><span data-stu-id="f4189-360">Error messages</span></span>

<span data-ttu-id="f4189-361">Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="f4189-361">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="f4189-362">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f4189-362">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="f4189-363">Interně atributy volají `String.Format` jako zástupný symbol pro název pole a někdy další zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="f4189-363">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="f4189-364">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f4189-364">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="f4189-365">Při použití na `Name` vlastnost bude chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."</span><span class="sxs-lookup"><span data-stu-id="f4189-365">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="f4189-366">Chcete-li zjistit, které parametry jsou `String.Format` předány pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="f4189-366">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="f4189-367">[Required] – atribut</span><span class="sxs-lookup"><span data-stu-id="f4189-367">[Required] attribute</span></span>

<span data-ttu-id="f4189-368">Ve výchozím nastavení systém ověřování zpracovává parametry, které neumožňují hodnotu null nebo vlastnosti, jako by měly `[Required]` atribut.</span><span class="sxs-lookup"><span data-stu-id="f4189-368">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="f4189-369">[Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , jako `decimal` jsou `int` a, nejsou null.</span><span class="sxs-lookup"><span data-stu-id="f4189-369">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="f4189-370">[Požadováno] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="f4189-370">[Required] validation on the server</span></span>

<span data-ttu-id="f4189-371">Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="f4189-371">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="f4189-372">Pole, které nesmí mít hodnotu null, je vždy platné a chybová zpráva [required] se nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="f4189-372">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="f4189-373">Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, což vede k chybové zprávě, `The value '' is invalid`jako je například.</span><span class="sxs-lookup"><span data-stu-id="f4189-373">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="f4189-374">Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="f4189-374">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="f4189-375">Převést pole na hodnotu null (například `decimal?` místo `decimal`).</span><span class="sxs-lookup"><span data-stu-id="f4189-375">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="f4189-376">Typy hodnot s [možnou hodnotou null\<>](/dotnet/csharp/programming-guide/nullable-types/) jsou považovány za standardní typy s možnou hodnotou null</span><span class="sxs-lookup"><span data-stu-id="f4189-376">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="f4189-377">Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-377">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="f4189-378">Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>v tématu.</span><span class="sxs-lookup"><span data-stu-id="f4189-378">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="f4189-379">[Požadováno] ověřování na klientovi</span><span class="sxs-lookup"><span data-stu-id="f4189-379">[Required] validation on the client</span></span>

<span data-ttu-id="f4189-380">Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi.</span><span class="sxs-lookup"><span data-stu-id="f4189-380">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="f4189-381">Na klientovi:</span><span class="sxs-lookup"><span data-stu-id="f4189-381">On the client:</span></span>

* <span data-ttu-id="f4189-382">Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup.</span><span class="sxs-lookup"><span data-stu-id="f4189-382">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="f4189-383">Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="f4189-383">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="f4189-384">Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením.</span><span class="sxs-lookup"><span data-stu-id="f4189-384">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="f4189-385">Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.</span><span class="sxs-lookup"><span data-stu-id="f4189-385">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="f4189-386">Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za `[Required]` , i když mají atribut.</span><span class="sxs-lookup"><span data-stu-id="f4189-386">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="f4189-387">To znamená, že získáte ověřování na straně klienta i v `[Required]` případě, že atribut nepoužijete.</span><span class="sxs-lookup"><span data-stu-id="f4189-387">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="f4189-388">Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="f4189-388">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="f4189-389">Chcete-li zadat vlastní chybovou zprávu, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="f4189-389">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="f4189-390">[Remote] – atribut</span><span class="sxs-lookup"><span data-stu-id="f4189-390">[Remote] attribute</span></span>

<span data-ttu-id="f4189-391">`[Remote]` Atribut implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný.</span><span class="sxs-lookup"><span data-stu-id="f4189-391">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="f4189-392">Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.</span><span class="sxs-lookup"><span data-stu-id="f4189-392">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="f4189-393">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="f4189-393">To implement remote validation:</span></span>

1. <span data-ttu-id="f4189-394">Vytvořte metodu Action pro volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="f4189-394">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="f4189-395">Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="f4189-395">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="f4189-396">`"true"`znamená, že jsou vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="f4189-396">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="f4189-397">`"false"`, `undefined`nebo `null` znamená, že vstup není platný.</span><span class="sxs-lookup"><span data-stu-id="f4189-397">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="f4189-398">Zobrazí výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="f4189-398">Display the default error message.</span></span>
   * <span data-ttu-id="f4189-399">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="f4189-399">Any other string means the input is invalid.</span></span> <span data-ttu-id="f4189-400">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="f4189-400">Display the string as a custom error message.</span></span>

   <span data-ttu-id="f4189-401">Tady je příklad metody akce, která vrací vlastní chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="f4189-401">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="f4189-402">Ve třídě modelu poznámkujte vlastnost s `[Remote]` atributem, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-402">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="f4189-403">`[Remote]` Atribut je v `Microsoft.AspNetCore.Mvc` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f4189-403">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="f4189-404">Pokud nepoužíváte Metapackage `Microsoft.AspNetCore.App` nebo `Microsoft.AspNetCore.All` , nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) .</span><span class="sxs-lookup"><span data-stu-id="f4189-404">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="f4189-405">Další pole</span><span class="sxs-lookup"><span data-stu-id="f4189-405">Additional fields</span></span>

<span data-ttu-id="f4189-406">`AdditionalFields` Vlastnost `[Remote]` atributu umožňuje ověřit kombinace polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="f4189-406">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="f4189-407">Pokud má `User` model například `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají odpovídající dvojici názvů.</span><span class="sxs-lookup"><span data-stu-id="f4189-407">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="f4189-408">Následující příklad ukazuje, jak použít `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="f4189-408">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="f4189-409">`AdditionalFields`lze nastavit explicitně na řetězce `"FirstName"` a `"LastName"`, ale použití operátoru [nameof](/dotnet/csharp/language-reference/keywords/nameof) zjednodušuje pozdější refaktoring.</span><span class="sxs-lookup"><span data-stu-id="f4189-409">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="f4189-410">Metoda Action pro toto ověření musí přijmout argumenty jméno a příjmení:</span><span class="sxs-lookup"><span data-stu-id="f4189-410">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="f4189-411">Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.</span><span class="sxs-lookup"><span data-stu-id="f4189-411">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="f4189-412">Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="f4189-412">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="f4189-413">Chcete-li například přidat `MiddleName` vlastnost do modelu, nastavte `[Remote]` atribut, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-413">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="f4189-414">`AdditionalFields`Podobně jako všechny argumenty atributu musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="f4189-414">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="f4189-415">Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="f4189-415">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="f4189-416">Alternativy k předdefinovaným atributům</span><span class="sxs-lookup"><span data-stu-id="f4189-416">Alternatives to built-in attributes</span></span>

<span data-ttu-id="f4189-417">Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:</span><span class="sxs-lookup"><span data-stu-id="f4189-417">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="f4189-418">[Vytvořte vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="f4189-418">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="f4189-419">[Implementujte IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="f4189-419">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="f4189-420">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="f4189-420">Custom attributes</span></span>

<span data-ttu-id="f4189-421">U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="f4189-421">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="f4189-422">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, a přepište <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> metodu.</span><span class="sxs-lookup"><span data-stu-id="f4189-422">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="f4189-423">`IsValid` Metoda přijímá objekt s názvem *hodnota*, což je vstup, který má být ověřen.</span><span class="sxs-lookup"><span data-stu-id="f4189-423">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="f4189-424">Přetížení také přijímá `ValidationContext` objekt, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="f4189-424">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="f4189-425">Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce.</span><span class="sxs-lookup"><span data-stu-id="f4189-425">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="f4189-426">`[ClassicMovie2]` Atribut nejprve zkontroluje Žánr a pokračuje pouze v případě, že je *klasický*.</span><span class="sxs-lookup"><span data-stu-id="f4189-426">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="f4189-427">U filmů identifikovaných jako klasických kontroluje datum vydání, aby se zajistilo, že není pozdější než limit předaný konstruktoru atributu.)</span><span class="sxs-lookup"><span data-stu-id="f4189-427">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="f4189-428">`movie` Proměnná v předchozím příkladu představuje `Movie` objekt, který obsahuje data z odesílání formuláře.</span><span class="sxs-lookup"><span data-stu-id="f4189-428">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="f4189-429">`IsValid` Metoda zkontroluje datum a Žánr.</span><span class="sxs-lookup"><span data-stu-id="f4189-429">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="f4189-430">Po úspěšném ověření `IsValid` vrátí `ValidationResult.Success` kód.</span><span class="sxs-lookup"><span data-stu-id="f4189-430">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="f4189-431">Pokud se ověření nepovede `ValidationResult` , vrátí se chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="f4189-431">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="f4189-432">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="f4189-432">IValidatableObject</span></span>

<span data-ttu-id="f4189-433">Předchozí příklad funguje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="f4189-433">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="f4189-434">Další možností pro ověřování na úrovni třídy je implementovat `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-434">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="f4189-435">Ověřování uzlu nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="f4189-435">Top-level node validation</span></span>

<span data-ttu-id="f4189-436">Uzly nejvyšší úrovně zahrnují:</span><span class="sxs-lookup"><span data-stu-id="f4189-436">Top-level nodes include:</span></span>

* <span data-ttu-id="f4189-437">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="f4189-437">Action parameters</span></span>
* <span data-ttu-id="f4189-438">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="f4189-438">Controller properties</span></span>
* <span data-ttu-id="f4189-439">Parametry obslužné rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="f4189-439">Page handler parameters</span></span>
* <span data-ttu-id="f4189-440">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="f4189-440">Page model properties</span></span>

<span data-ttu-id="f4189-441">Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem.</span><span class="sxs-lookup"><span data-stu-id="f4189-441">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="f4189-442">V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření parametru `phone` akce:</span><span class="sxs-lookup"><span data-stu-id="f4189-442">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="f4189-443">Uzly nejvyšší úrovně mohou být použity <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování.</span><span class="sxs-lookup"><span data-stu-id="f4189-443">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="f4189-444">V následujícím příkladu z ukázkové aplikace určuje `CheckAge` metoda, že `age` parametr musí být svázán z řetězce dotazu při odeslání formuláře:</span><span class="sxs-lookup"><span data-stu-id="f4189-444">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="f4189-445">Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře.</span><span class="sxs-lookup"><span data-stu-id="f4189-445">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="f4189-446">První formulář odešle `Age` hodnotu `99` jako řetězec dotazu:. `https://localhost:5001/Users/CheckAge?Age=99`</span><span class="sxs-lookup"><span data-stu-id="f4189-446">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="f4189-447">Při odeslání správně formátovaného `age` parametru z řetězce dotazu se formulář ověří.</span><span class="sxs-lookup"><span data-stu-id="f4189-447">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="f4189-448">Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede.</span><span class="sxs-lookup"><span data-stu-id="f4189-448">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="f4189-449">Vazba se nezdařila, `age` protože parametr musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f4189-449">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="f4189-450">Při spuštění s `CompatibilityVersion.Version_2_1` nebo novějším je ověřování uzlů nejvyšší úrovně ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="f4189-450">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="f4189-451">V opačném případě je ověřování uzlu nejvyšší úrovně zakázané.</span><span class="sxs-lookup"><span data-stu-id="f4189-451">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="f4189-452">Výchozí možnost může být přepsána nastavením <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> vlastnosti v (`Startup.ConfigureServices`), jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="f4189-452">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="f4189-453">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="f4189-453">Maximum errors</span></span>

<span data-ttu-id="f4189-454">Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200).</span><span class="sxs-lookup"><span data-stu-id="f4189-454">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="f4189-455">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f4189-455">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="f4189-456">Maximální rekurze</span><span class="sxs-lookup"><span data-stu-id="f4189-456">Maximum recursion</span></span>

<span data-ttu-id="f4189-457"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>projde grafem objektu ověřovaného modelu.</span><span class="sxs-lookup"><span data-stu-id="f4189-457"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="f4189-458">U modelů, které jsou velmi hlubokoelné nebo nekonečné rekurzivní, může ověřování způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="f4189-458">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="f4189-459">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku.</span><span class="sxs-lookup"><span data-stu-id="f4189-459">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="f4189-460">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 32 při spuštění v systému `CompatibilityVersion.Version_2_2` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="f4189-460">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="f4189-461">Pro starší verze je hodnota null, což znamená bez omezení hloubky.</span><span class="sxs-lookup"><span data-stu-id="f4189-461">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="f4189-462">Automatické krátké okruhy</span><span class="sxs-lookup"><span data-stu-id="f4189-462">Automatic short-circuit</span></span>

<span data-ttu-id="f4189-463">Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="f4189-463">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="f4189-464">Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních elementů ( `byte[]`například `string[]`, `Dictionary<string, string>`,) a složitých grafů objektů, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="f4189-464">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="f4189-465">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="f4189-465">Disable validation</span></span>

<span data-ttu-id="f4189-466">Zakázání ověřování:</span><span class="sxs-lookup"><span data-stu-id="f4189-466">To disable validation:</span></span>

1. <span data-ttu-id="f4189-467">Vytvořte implementaci `IObjectModelValidator` , která neoznačí žádná pole jako neplatnou.</span><span class="sxs-lookup"><span data-stu-id="f4189-467">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="f4189-468">Přidejte následující kód k `Startup.ConfigureServices` nahrazení výchozí `IObjectModelValidator` implementace v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="f4189-468">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="f4189-469">Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="f4189-469">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="f4189-470">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="f4189-470">Client-side validation</span></span>

<span data-ttu-id="f4189-471">Ověřování na straně klienta brání odeslání, dokud není formulář platný.</span><span class="sxs-lookup"><span data-stu-id="f4189-471">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="f4189-472">Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="f4189-472">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="f4189-473">Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="f4189-473">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="f4189-474">Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="f4189-474">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="f4189-475">Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="f4189-475">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="f4189-476">Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f4189-476">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="f4189-477">Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu k vykreslování atributů HTML 5 `data-` pro prvky formuláře, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="f4189-477">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="f4189-478">jQuery nenáročné ověřování analyzuje `data-` atributy a předá logiku do příkazu jQuery Validate a efektivně kopíruje logiku ověřování na straně serveru do klienta.</span><span class="sxs-lookup"><span data-stu-id="f4189-478">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="f4189-479">Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="f4189-479">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="f4189-480">Předchozí pomocník značek vykresluje následující kód HTML.</span><span class="sxs-lookup"><span data-stu-id="f4189-480">The preceding tag helpers render the following HTML.</span></span>

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

<span data-ttu-id="f4189-481">Všimněte si, `data-` že atributy ve výstupu HTML odpovídají atributům ověřování pro `ReleaseDate` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f4189-481">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="f4189-482">`data-val-required` Atribut obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání.</span><span class="sxs-lookup"><span data-stu-id="f4189-482">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="f4189-483">jQuery unpassing předá tuto hodnotu metodě jQuery [Required ()](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodném \*\* \<elementu span>\*\* .</span><span class="sxs-lookup"><span data-stu-id="f4189-483">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="f4189-484">Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána `[DataType]` atributem.</span><span class="sxs-lookup"><span data-stu-id="f4189-484">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="f4189-485">Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček.</span><span class="sxs-lookup"><span data-stu-id="f4189-485">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="f4189-486">`[DataType]`atributy a podtřídy, jako je `[EmailAddress]` například umožňuje zadat chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="f4189-486">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="f4189-487">Přidání ověřování do dynamických formulářů</span><span class="sxs-lookup"><span data-stu-id="f4189-487">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="f4189-488">jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují.</span><span class="sxs-lookup"><span data-stu-id="f4189-488">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="f4189-489">Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích.</span><span class="sxs-lookup"><span data-stu-id="f4189-489">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="f4189-490">Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="f4189-490">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="f4189-491">Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.</span><span class="sxs-lookup"><span data-stu-id="f4189-491">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="f4189-492">`$.validator.unobtrusive.parse()` Metoda přijímá selektor jQuery pro svůj jeden argument.</span><span class="sxs-lookup"><span data-stu-id="f4189-492">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="f4189-493">Tato metoda oznamuje nenáročné ověřování, aby bylo `data-` možné analyzovat atributy formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="f4189-493">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="f4189-494">Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="f4189-494">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="f4189-495">Přidání ověřování do dynamických ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="f4189-495">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="f4189-496">`$.validator.unobtrusive.parse()` Metoda funguje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, například `<input>` a `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="f4189-496">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="f4189-497">Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-497">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="f4189-498">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="f4189-498">Custom client-side validation</span></span>

<span data-ttu-id="f4189-499">Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery.</span><span class="sxs-lookup"><span data-stu-id="f4189-499">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="f4189-500">Následující vzorový kód adaptéru byl napsán pro atributy `ClassicMovie` a `ClassicMovie2` , které byly představeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="f4189-500">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="f4189-501">Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="f4189-501">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="f4189-502">Použití adaptéru pro dané pole se spustí pomocí `data-` atributů, které:</span><span class="sxs-lookup"><span data-stu-id="f4189-502">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="f4189-503">Označte pole jako podléhající ověřování (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="f4189-503">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="f4189-504">Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="f4189-504">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="f4189-505">Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="f4189-505">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="f4189-506">Následující příklad ukazuje `data-` atributy pro `ClassicMovie` atribut ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="f4189-506">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="f4189-507">Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) se při vykreslování `data-` atributů používají informace z atributů ověřování.</span><span class="sxs-lookup"><span data-stu-id="f4189-507">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="f4189-508">Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních `data-` atributů HTML:</span><span class="sxs-lookup"><span data-stu-id="f4189-508">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="f4189-509">Vytvořte třídu, která je odvozena `AttributeAdapterBase<TAttribute>` z třídy a třídu, `IValidationAttributeAdapterProvider`která implementuje, a zaregistrujte svůj atribut a jeho adaptér v di.</span><span class="sxs-lookup"><span data-stu-id="f4189-509">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="f4189-510">Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách.</span><span class="sxs-lookup"><span data-stu-id="f4189-510">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="f4189-511">Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.</span><span class="sxs-lookup"><span data-stu-id="f4189-511">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="f4189-512">Implementujte `IClientModelValidator` ve `ValidationAttribute` své třídě.</span><span class="sxs-lookup"><span data-stu-id="f4189-512">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="f4189-513">Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.</span><span class="sxs-lookup"><span data-stu-id="f4189-513">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="f4189-514">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="f4189-514">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="f4189-515">Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie` atributem v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f4189-515">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="f4189-516">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="f4189-516">To add client validation by using this method:</span></span>

1. <span data-ttu-id="f4189-517">Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut.</span><span class="sxs-lookup"><span data-stu-id="f4189-517">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="f4189-518">Odvodit třídu z [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="f4189-518">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="f4189-519">Vytvořte `AddValidation` metodu, která přidá `data-` atributy do vykresleného výstupu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-519">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="f4189-520">Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="f4189-520">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="f4189-521">V `GetAttributeAdapter` metodě předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-521">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="f4189-522">Zaregistrujte poskytovatele adaptéru pro DI `Startup.ConfigureServices`v:</span><span class="sxs-lookup"><span data-stu-id="f4189-522">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="f4189-523">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="f4189-523">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="f4189-524">Tato metoda vykreslování `data-` atributů ve formátu HTML je používána `ClassicMovie2` atributem v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f4189-524">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="f4189-525">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="f4189-525">To add client validation by using this method:</span></span>

* <span data-ttu-id="f4189-526">Ve vlastním ověřovacím atributu implementujte `IClientModelValidator` rozhraní a vytvořte `AddValidation` metodu.</span><span class="sxs-lookup"><span data-stu-id="f4189-526">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="f4189-527">V `AddValidation` metodě přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="f4189-527">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="f4189-528">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="f4189-528">Disable client-side validation</span></span>

<span data-ttu-id="f4189-529">Následující kód zakáže ověřování klienta v zobrazeních MVC:</span><span class="sxs-lookup"><span data-stu-id="f4189-529">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="f4189-530">A na Razor stránkách:</span><span class="sxs-lookup"><span data-stu-id="f4189-530">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="f4189-531">Další možností pro zakázání ověřování klienta je přidat komentář k odkazu do `_ValidationScriptsPartial` souboru *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f4189-531">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f4189-532">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f4189-532">Additional resources</span></span>

* [<span data-ttu-id="f4189-533">Obor názvů System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="f4189-533">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="f4189-534">Vazba modelu</span><span class="sxs-lookup"><span data-stu-id="f4189-534">Model Binding</span></span>](model-binding.md)

::: moniker-end

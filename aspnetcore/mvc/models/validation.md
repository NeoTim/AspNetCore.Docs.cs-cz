---
title: Ověření modelu ve ASP.NET Core MVC
author: rick-anderson
description: Přečtěte si o ověřování modelu ve ASP.NET Core MVC a Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: mvc/models/validation
ms.openlocfilehash: 042a9933e561de4957f6332bdff3c4f09d2e119b
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355268"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="90c2b-103">Ověřování modelu ve ASP.NET Core MVC a Razor Pages</span><span class="sxs-lookup"><span data-stu-id="90c2b-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="90c2b-104">Od [Kirka Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="90c2b-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="90c2b-105">Tento článek vysvětluje, jak ověřit vstup uživatele ve ASP.NET Core MVC nebo v aplikaci Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="90c2b-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="90c2b-106">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="90c2b-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="90c2b-107">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="90c2b-107">Model state</span></span>

<span data-ttu-id="90c2b-108">Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="90c2b-109">Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat.</span><span class="sxs-lookup"><span data-stu-id="90c2b-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="90c2b-110">Například "x" je zadáno v poli typu Integer.</span><span class="sxs-lookup"><span data-stu-id="90c2b-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="90c2b-111">K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům.</span><span class="sxs-lookup"><span data-stu-id="90c2b-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="90c2b-112">Například hodnota 0 se zadává v poli, které očekává hodnocení mezi 1 a 5.</span><span class="sxs-lookup"><span data-stu-id="90c2b-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="90c2b-113">Před provedením akce kontroleru nebo obslužné rutiny Razor Pages dojde k ověření vazby modelů i k ověřování modelu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="90c2b-114">U webových aplikací je zodpovědností aplikace na kontrolu `ModelState.IsValid` a odpovídajícím způsobem reagovat.</span><span class="sxs-lookup"><span data-stu-id="90c2b-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="90c2b-115">Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="90c2b-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="90c2b-116">Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid`, pokud mají `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="90c2b-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="90c2b-117">V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o chybě, pokud stav modelu není platný.</span><span class="sxs-lookup"><span data-stu-id="90c2b-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="90c2b-118">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="90c2b-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="90c2b-119">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="90c2b-119">Rerun validation</span></span>

<span data-ttu-id="90c2b-120">Ověřování je automatické, ale můžete je chtít opakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="90c2b-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="90c2b-121">Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="90c2b-122">Chcete-li znovu spustit ověřování, zavolejte metodu `TryValidateModel`, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="90c2b-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="90c2b-123">Atributy ověřování</span><span class="sxs-lookup"><span data-stu-id="90c2b-123">Validation attributes</span></span>

<span data-ttu-id="90c2b-124">Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="90c2b-125">Následující příklad z ukázkové aplikace zobrazuje třídu modelu s poznámkou ověřování atributů.</span><span class="sxs-lookup"><span data-stu-id="90c2b-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="90c2b-126">Atribut `[ClassicMovie]` je vlastní ověřovací atribut a jsou integrovány i ostatní.</span><span class="sxs-lookup"><span data-stu-id="90c2b-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="90c2b-127">Nezobrazuje se `[ClassicMovieWithClientValidator]`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="90c2b-128">`[ClassicMovieWithClientValidator]` ukazuje alternativní způsob implementace vlastního atributu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="90c2b-129">Předdefinované atributy</span><span class="sxs-lookup"><span data-stu-id="90c2b-129">Built-in attributes</span></span>

<span data-ttu-id="90c2b-130">Tady jsou některé z vestavěných ověřovacích atributů:</span><span class="sxs-lookup"><span data-stu-id="90c2b-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="90c2b-131">`[CreditCard]`: ověřuje, zda má vlastnost formát kreditní karty.</span><span class="sxs-lookup"><span data-stu-id="90c2b-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="90c2b-132">`[Compare]`: ověří, že se dvě vlastnosti v modelu shodují.</span><span class="sxs-lookup"><span data-stu-id="90c2b-132">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="90c2b-133">`[EmailAddress]`: ověřuje, zda má vlastnost formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-133">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="90c2b-134">`[Phone]`: ověřuje, zda má vlastnost formát telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="90c2b-134">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="90c2b-135">`[Range]`: ověří, že hodnota vlastnosti spadá do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-135">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="90c2b-136">`[RegularExpression]`: ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-136">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="90c2b-137">`[Required]`: ověří, že pole nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="90c2b-137">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="90c2b-138">Podrobnosti o chování tohoto atributu naleznete v tématu [`[Required]` atributu](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="90c2b-138">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="90c2b-139">`[StringLength]`: ověří, že hodnota řetězcové vlastnosti nepřekračuje zadané omezení délky.</span><span class="sxs-lookup"><span data-stu-id="90c2b-139">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="90c2b-140">`[Url]`: ověřuje, zda má vlastnost formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="90c2b-140">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="90c2b-141">`[Remote]`: ověřuje vstup na straně klienta voláním metody Action na serveru.</span><span class="sxs-lookup"><span data-stu-id="90c2b-141">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="90c2b-142">Podrobnosti o chování tohoto atributu naleznete v tématu [`[Remote]` atributu](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="90c2b-142">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="90c2b-143">Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="90c2b-143">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="90c2b-144">Chybovými zprávami</span><span class="sxs-lookup"><span data-stu-id="90c2b-144">Error messages</span></span>

<span data-ttu-id="90c2b-145">Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="90c2b-145">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="90c2b-146">Příklad:</span><span class="sxs-lookup"><span data-stu-id="90c2b-146">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="90c2b-147">Interně atributy volají `String.Format` zástupný symbol pro název pole a někdy další zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="90c2b-147">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="90c2b-148">Příklad:</span><span class="sxs-lookup"><span data-stu-id="90c2b-148">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="90c2b-149">Při použití na vlastnost `Name` by byla chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."</span><span class="sxs-lookup"><span data-stu-id="90c2b-149">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="90c2b-150">Chcete-li zjistit, které parametry jsou předány `String.Format` pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="90c2b-150">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="90c2b-151">[Required] – atribut</span><span class="sxs-lookup"><span data-stu-id="90c2b-151">[Required] attribute</span></span>

<span data-ttu-id="90c2b-152">Ve výchozím nastavení systém ověřování zpracovává parametry bez hodnoty null nebo vlastnosti, jako by měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-152">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="90c2b-153">[Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , například `decimal` a `int`, nejsou null.</span><span class="sxs-lookup"><span data-stu-id="90c2b-153">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="90c2b-154">[Požadováno] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="90c2b-154">[Required] validation on the server</span></span>

<span data-ttu-id="90c2b-155">Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="90c2b-155">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="90c2b-156">Pole, které nesmí mít hodnotu null, je vždy platné a chybová zpráva atributu `[Required]` se nikdy nezobrazuje.</span><span class="sxs-lookup"><span data-stu-id="90c2b-156">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="90c2b-157">Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, takže se zobrazí chybová zpráva, například `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-157">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="90c2b-158">Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="90c2b-158">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="90c2b-159">Převést pole na hodnotu null (například `decimal?` místo `decimal`).</span><span class="sxs-lookup"><span data-stu-id="90c2b-159">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="90c2b-160">Typy hodnot s [povolenou hodnotou null\<t >](/dotnet/csharp/programming-guide/nullable-types/) se považují za standardní typy Nullable.</span><span class="sxs-lookup"><span data-stu-id="90c2b-160">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="90c2b-161">Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-161">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="90c2b-162">Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="90c2b-162">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="90c2b-163">[Požadováno] ověřování na klientovi</span><span class="sxs-lookup"><span data-stu-id="90c2b-163">[Required] validation on the client</span></span>

<span data-ttu-id="90c2b-164">Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi.</span><span class="sxs-lookup"><span data-stu-id="90c2b-164">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="90c2b-165">Na klientovi:</span><span class="sxs-lookup"><span data-stu-id="90c2b-165">On the client:</span></span>

* <span data-ttu-id="90c2b-166">Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup.</span><span class="sxs-lookup"><span data-stu-id="90c2b-166">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="90c2b-167">Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="90c2b-167">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="90c2b-168">Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením.</span><span class="sxs-lookup"><span data-stu-id="90c2b-168">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="90c2b-169">Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.</span><span class="sxs-lookup"><span data-stu-id="90c2b-169">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="90c2b-170">Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za, ale měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-170">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="90c2b-171">To znamená, že získáte ověřování na straně klienta i v případě, že nepoužijete atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-171">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="90c2b-172">Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="90c2b-172">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="90c2b-173">Chcete-li zadat vlastní chybovou zprávu, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="90c2b-173">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="90c2b-174">[Remote] – atribut</span><span class="sxs-lookup"><span data-stu-id="90c2b-174">[Remote] attribute</span></span>

<span data-ttu-id="90c2b-175">Atribut `[Remote]` implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný.</span><span class="sxs-lookup"><span data-stu-id="90c2b-175">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="90c2b-176">Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.</span><span class="sxs-lookup"><span data-stu-id="90c2b-176">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="90c2b-177">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="90c2b-177">To implement remote validation:</span></span>

1. <span data-ttu-id="90c2b-178">Vytvořte metodu Action pro volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-178">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="90c2b-179">Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="90c2b-179">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="90c2b-180">`true` znamená, že jsou vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="90c2b-180">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="90c2b-181">`false`, `undefined`nebo `null` znamená, že vstup není platný.</span><span class="sxs-lookup"><span data-stu-id="90c2b-181">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="90c2b-182">Zobrazí výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-182">Display the default error message.</span></span>
   * <span data-ttu-id="90c2b-183">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="90c2b-183">Any other string means the input is invalid.</span></span> <span data-ttu-id="90c2b-184">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-184">Display the string as a custom error message.</span></span>

   <span data-ttu-id="90c2b-185">Tady je příklad metody akce, která vrací vlastní chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-185">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="90c2b-186">Ve třídě modelu poznámku k vlastnosti s atributem `[Remote]`, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-186">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="90c2b-187">Atribut `[Remote]` je v oboru názvů `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-187">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="90c2b-188">Další pole</span><span class="sxs-lookup"><span data-stu-id="90c2b-188">Additional fields</span></span>

<span data-ttu-id="90c2b-189">Vlastnost `AdditionalFields` atributu `[Remote]` umožňuje ověřit kombinace polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="90c2b-189">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="90c2b-190">Pokud například `User` model obsahoval `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají tento pár názvů.</span><span class="sxs-lookup"><span data-stu-id="90c2b-190">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="90c2b-191">Následující příklad ukazuje, jak použít `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="90c2b-191">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="90c2b-192">`AdditionalFields` možné nastavit explicitně na řetězce "FirstName" a "LastName", ale pomocí operátoru [nameof](/dotnet/csharp/language-reference/keywords/nameof) se zjednoduší pozdější refaktoring.</span><span class="sxs-lookup"><span data-stu-id="90c2b-192">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="90c2b-193">Metoda Action pro toto ověření musí přijmout argumenty `firstName` i `lastName`:</span><span class="sxs-lookup"><span data-stu-id="90c2b-193">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="90c2b-194">Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.</span><span class="sxs-lookup"><span data-stu-id="90c2b-194">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="90c2b-195">Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="90c2b-195">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="90c2b-196">Chcete-li například přidat vlastnost `MiddleName` do modelu, nastavte atribut `[Remote]`, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-196">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="90c2b-197">`AdditionalFields`, jako jsou všechny argumenty atributu, musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="90c2b-197">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="90c2b-198">Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-198">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="90c2b-199">Alternativy k předdefinovaným atributům</span><span class="sxs-lookup"><span data-stu-id="90c2b-199">Alternatives to built-in attributes</span></span>

<span data-ttu-id="90c2b-200">Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:</span><span class="sxs-lookup"><span data-stu-id="90c2b-200">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="90c2b-201">[Vytvořte vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="90c2b-201">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="90c2b-202">[Implementujte IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="90c2b-202">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="90c2b-203">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="90c2b-203">Custom attributes</span></span>

<span data-ttu-id="90c2b-204">U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="90c2b-204">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="90c2b-205">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>a přepište metodu <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="90c2b-205">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="90c2b-206">Metoda `IsValid` přijímá objekt s názvem *hodnota*, který je vstupem k ověření.</span><span class="sxs-lookup"><span data-stu-id="90c2b-206">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="90c2b-207">Přetížení také přijímá objekt `ValidationContext`, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-207">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="90c2b-208">Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce.</span><span class="sxs-lookup"><span data-stu-id="90c2b-208">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="90c2b-209">Atribut `[ClassicMovie]`:</span><span class="sxs-lookup"><span data-stu-id="90c2b-209">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="90c2b-210">Se spouští jenom na serveru.</span><span class="sxs-lookup"><span data-stu-id="90c2b-210">Is only run on the server.</span></span>
* <span data-ttu-id="90c2b-211">U klasických filmů ověří datum vydání:</span><span class="sxs-lookup"><span data-stu-id="90c2b-211">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="90c2b-212">Proměnná `movie` v předchozím příkladu představuje objekt `Movie`, který obsahuje data z odesílání formuláře.</span><span class="sxs-lookup"><span data-stu-id="90c2b-212">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="90c2b-213">Pokud se ověření nepovede, vrátí se `ValidationResult` s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="90c2b-213">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="90c2b-214">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="90c2b-214">IValidatableObject</span></span>

<span data-ttu-id="90c2b-215">Předchozí příklad funguje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="90c2b-215">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="90c2b-216">Další možností ověřování na úrovni třídy je implementace `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-216">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="90c2b-217">Ověřování uzlu nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="90c2b-217">Top-level node validation</span></span>

<span data-ttu-id="90c2b-218">Uzly nejvyšší úrovně zahrnují:</span><span class="sxs-lookup"><span data-stu-id="90c2b-218">Top-level nodes include:</span></span>

* <span data-ttu-id="90c2b-219">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="90c2b-219">Action parameters</span></span>
* <span data-ttu-id="90c2b-220">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="90c2b-220">Controller properties</span></span>
* <span data-ttu-id="90c2b-221">Parametry obslužné rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="90c2b-221">Page handler parameters</span></span>
* <span data-ttu-id="90c2b-222">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="90c2b-222">Page model properties</span></span>

<span data-ttu-id="90c2b-223">Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem.</span><span class="sxs-lookup"><span data-stu-id="90c2b-223">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="90c2b-224">V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření parametru `phone` akce:</span><span class="sxs-lookup"><span data-stu-id="90c2b-224">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="90c2b-225">Uzly nejvyšší úrovně mohou použít <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování.</span><span class="sxs-lookup"><span data-stu-id="90c2b-225">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="90c2b-226">V následujícím příkladu z ukázkové aplikace určuje metoda `CheckAge`, že parametr `age` musí být při odeslání formuláře svázán z řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-226">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="90c2b-227">Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře.</span><span class="sxs-lookup"><span data-stu-id="90c2b-227">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="90c2b-228">První formulář odešle hodnotu `Age` `99` jako parametr řetězce dotazu: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-228">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="90c2b-229">Když se odešle správně formátovaný `age` parametr z řetězce dotazu, formulář se ověří.</span><span class="sxs-lookup"><span data-stu-id="90c2b-229">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="90c2b-230">Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede.</span><span class="sxs-lookup"><span data-stu-id="90c2b-230">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="90c2b-231">Vazba se nezdařila, protože parametr `age` musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-231">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="90c2b-232">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="90c2b-232">Maximum errors</span></span>

<span data-ttu-id="90c2b-233">Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200).</span><span class="sxs-lookup"><span data-stu-id="90c2b-233">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="90c2b-234">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="90c2b-234">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="90c2b-235">Maximální rekurze</span><span class="sxs-lookup"><span data-stu-id="90c2b-235">Maximum recursion</span></span>

<span data-ttu-id="90c2b-236"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> projde grafem objektu ověřovaného modelu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-236"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="90c2b-237">U modelů, které jsou hluboko nebo jsou nekonečně rekurzivní, může ověřování způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="90c2b-237">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="90c2b-238">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku.</span><span class="sxs-lookup"><span data-stu-id="90c2b-238">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="90c2b-239">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 32.</span><span class="sxs-lookup"><span data-stu-id="90c2b-239">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="90c2b-240">Automatické krátké okruhy</span><span class="sxs-lookup"><span data-stu-id="90c2b-240">Automatic short-circuit</span></span>

<span data-ttu-id="90c2b-241">Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="90c2b-241">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="90c2b-242">Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních objektů (například `byte[]`, `string[]`, `Dictionary<string, string>`) a složitých grafů objektů, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="90c2b-242">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="90c2b-243">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="90c2b-243">Disable validation</span></span>

<span data-ttu-id="90c2b-244">Zakázání ověřování:</span><span class="sxs-lookup"><span data-stu-id="90c2b-244">To disable validation:</span></span>

1. <span data-ttu-id="90c2b-245">Vytvořte implementaci `IObjectModelValidator`, která neoznačí žádná pole jako neplatnou.</span><span class="sxs-lookup"><span data-stu-id="90c2b-245">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="90c2b-246">Přidejte následující kód, který `Startup.ConfigureServices` k nahrazení výchozí implementace `IObjectModelValidator` v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="90c2b-246">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="90c2b-247">Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-247">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="90c2b-248">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="90c2b-248">Client-side validation</span></span>

<span data-ttu-id="90c2b-249">Ověřování na straně klienta brání odeslání, dokud není formulář platný.</span><span class="sxs-lookup"><span data-stu-id="90c2b-249">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="90c2b-250">Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="90c2b-250">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="90c2b-251">Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="90c2b-251">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="90c2b-252">Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="90c2b-252">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="90c2b-253">Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="90c2b-253">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="90c2b-254">Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="90c2b-254">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="90c2b-255">Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu pro vykreslení HTML 5 `data-` atributů pro prvky formuláře, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="90c2b-255">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="90c2b-256">jQuery nenáročné ověřování analyzuje atributy `data-` a předá logiku do příkazu jQuery Validate a efektivně kopíruje do klienta logiku ověřování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="90c2b-256">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="90c2b-257">Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="90c2b-257">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="90c2b-258">Předchozí pomocník značek vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="90c2b-258">The preceding tag helpers render the following HTML:</span></span>

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

<span data-ttu-id="90c2b-259">Všimněte si, že atributy `data-` ve výstupu HTML odpovídají atributům ověřování pro vlastnost `Movie.ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-259">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="90c2b-260">Atribut `data-val-required` obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání.</span><span class="sxs-lookup"><span data-stu-id="90c2b-260">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="90c2b-261">jQuery unpassing předá tuto hodnotu metodě jQuery [Required ()](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodném **\<rozpětí >** elementu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-261">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="90c2b-262">Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána atributem `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-262">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="90c2b-263">Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček.</span><span class="sxs-lookup"><span data-stu-id="90c2b-263">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="90c2b-264">`[DataType]` atributů a podtřídách, jako je `[EmailAddress]`, vám umožní zadat chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-264">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="90c2b-265">Nenáročná ověření</span><span class="sxs-lookup"><span data-stu-id="90c2b-265">Unobtrusive validation</span></span>

<span data-ttu-id="90c2b-266">Informace o nenáročnosti ověřování najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore.Docs/issues/1111).</span><span class="sxs-lookup"><span data-stu-id="90c2b-266">For information on unobtrusive validation, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="90c2b-267">Přidání ověřování do dynamických formulářů</span><span class="sxs-lookup"><span data-stu-id="90c2b-267">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="90c2b-268">jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují.</span><span class="sxs-lookup"><span data-stu-id="90c2b-268">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="90c2b-269">Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích.</span><span class="sxs-lookup"><span data-stu-id="90c2b-269">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="90c2b-270">Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="90c2b-270">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="90c2b-271">Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.</span><span class="sxs-lookup"><span data-stu-id="90c2b-271">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="90c2b-272">Metoda `$.validator.unobtrusive.parse()` přijímá selektor jQuery pro svůj jeden argument.</span><span class="sxs-lookup"><span data-stu-id="90c2b-272">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="90c2b-273">Tato metoda oznamuje nenáročné ověřování, aby bylo možné analyzovat `data-` atributů formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="90c2b-273">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="90c2b-274">Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="90c2b-274">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="90c2b-275">Přidání ověřování do dynamických ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="90c2b-275">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="90c2b-276">Metoda `$.validator.unobtrusive.parse()` pracuje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, jako je `<input>` a `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-276">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="90c2b-277">Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-277">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="90c2b-278">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="90c2b-278">Custom client-side validation</span></span>

<span data-ttu-id="90c2b-279">Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery.</span><span class="sxs-lookup"><span data-stu-id="90c2b-279">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="90c2b-280">Následující vzorový kód adaptéru byl napsán pro `[ClassicMovie]` a `[ClassicMovieWithClientValidator]` atributy, které byly představeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="90c2b-280">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="90c2b-281">Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="90c2b-281">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="90c2b-282">Použití adaptéru pro dané pole je aktivováno `data-` atributy, které:</span><span class="sxs-lookup"><span data-stu-id="90c2b-282">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="90c2b-283">Označte pole jako podléhající ověřování (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="90c2b-283">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="90c2b-284">Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="90c2b-284">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="90c2b-285">Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-param1="value"`).</span><span class="sxs-lookup"><span data-stu-id="90c2b-285">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="90c2b-286">Následující příklad ukazuje atributy `data-` pro atribut `ClassicMovie` ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="90c2b-286">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="90c2b-287">Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) využívají informace z atributů ověřování k vykreslování atributů `data-`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-287">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="90c2b-288">Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních atributů `data-` HTML:</span><span class="sxs-lookup"><span data-stu-id="90c2b-288">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="90c2b-289">Vytvořte třídu, která je odvozena z `AttributeAdapterBase<TAttribute>` a třídu, která implementuje `IValidationAttributeAdapterProvider`a zaregistrujte svůj atribut a jeho adaptér v DI.</span><span class="sxs-lookup"><span data-stu-id="90c2b-289">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="90c2b-290">Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách.</span><span class="sxs-lookup"><span data-stu-id="90c2b-290">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="90c2b-291">Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.</span><span class="sxs-lookup"><span data-stu-id="90c2b-291">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="90c2b-292">Implementujte `IClientModelValidator` ve vaší třídě `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-292">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="90c2b-293">Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.</span><span class="sxs-lookup"><span data-stu-id="90c2b-293">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="90c2b-294">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="90c2b-294">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="90c2b-295">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovie` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="90c2b-295">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="90c2b-296">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="90c2b-296">To add client validation by using this method:</span></span>

1. <span data-ttu-id="90c2b-297">Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut.</span><span class="sxs-lookup"><span data-stu-id="90c2b-297">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="90c2b-298">Odvodit třídu z [AttributeAdapterBase\<t >](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="90c2b-298">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="90c2b-299">Vytvořte `AddValidation` metodu, která přidá atributy `data-` do vykresleného výstupu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-299">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="90c2b-300">Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="90c2b-300">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="90c2b-301">V metodě `GetAttributeAdapter` předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-301">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="90c2b-302">Zaregistrujte poskytovatele adaptéru pro DI v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="90c2b-302">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="90c2b-303">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="90c2b-303">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="90c2b-304">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovieWithClientValidator` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="90c2b-304">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="90c2b-305">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="90c2b-305">To add client validation by using this method:</span></span>

* <span data-ttu-id="90c2b-306">Ve vlastním ověřovacím atributu implementujte rozhraní `IClientModelValidator` a vytvořte metodu `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-306">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="90c2b-307">V metodě `AddValidation` přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-307">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="90c2b-308">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="90c2b-308">Disable client-side validation</span></span>

<span data-ttu-id="90c2b-309">Následující kód zakáže ověřování klienta v Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="90c2b-309">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="90c2b-310">Další možnosti zakázání ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="90c2b-310">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="90c2b-311">Odkomentujte odkaz na `_ValidationScriptsPartial` ve všech souborech *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="90c2b-311">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="90c2b-312">Odeberte obsah souboru *Pages\Shared\_ValidationScriptsPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="90c2b-312">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="90c2b-313">Předchozí přístup nezabrání ověřování na straně klienta ASP.NET Core knihovně tříd identity Razor.</span><span class="sxs-lookup"><span data-stu-id="90c2b-313">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="90c2b-314">Další informace najdete v tématu <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="90c2b-314">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90c2b-315">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="90c2b-315">Additional resources</span></span>

* [<span data-ttu-id="90c2b-316">Obor názvů System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="90c2b-316">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="90c2b-317">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="90c2b-317">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="90c2b-318">Tento článek vysvětluje, jak ověřit vstup uživatele ve ASP.NET Core MVC nebo v aplikaci Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="90c2b-318">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="90c2b-319">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="90c2b-319">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="90c2b-320">Stav modelu</span><span class="sxs-lookup"><span data-stu-id="90c2b-320">Model state</span></span>

<span data-ttu-id="90c2b-321">Stav modelu představuje chyby, které pocházejí ze dvou subsystémů: vazby modelu a ověření modelu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-321">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="90c2b-322">Chyby, které pocházejí z [vazby mezi modely](model-binding.md) , jsou obvykle chyby převodu dat (například "x" je zadáno v poli, které očekává celé číslo).</span><span class="sxs-lookup"><span data-stu-id="90c2b-322">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="90c2b-323">K ověření modelu dochází po vazbě modelu a hlášení chyb, kde data neodpovídají obchodním pravidlům (například hodnota 0 je zadána v poli, které očekává hodnocení mezi 1 a 5).</span><span class="sxs-lookup"><span data-stu-id="90c2b-323">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="90c2b-324">Před provedením akce kontroleru nebo obslužné rutiny Razor Pages se vyskytuje jak vazba modelů, tak ověřování.</span><span class="sxs-lookup"><span data-stu-id="90c2b-324">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="90c2b-325">U webových aplikací je zodpovědností aplikace na kontrolu `ModelState.IsValid` a odpovídajícím způsobem reagovat.</span><span class="sxs-lookup"><span data-stu-id="90c2b-325">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="90c2b-326">Webové aplikace obvykle znovu zobrazí stránku s chybovou zprávou:</span><span class="sxs-lookup"><span data-stu-id="90c2b-326">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="90c2b-327">Řadiče webového rozhraní API nemusí kontrolovat `ModelState.IsValid`, pokud mají `[ApiController]` atribut.</span><span class="sxs-lookup"><span data-stu-id="90c2b-327">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="90c2b-328">V takovém případě je vrácena Automatická odpověď HTTP 400 obsahující podrobnosti o chybě, pokud stav modelu není platný.</span><span class="sxs-lookup"><span data-stu-id="90c2b-328">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="90c2b-329">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="90c2b-329">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="90c2b-330">Znovu spustit ověření</span><span class="sxs-lookup"><span data-stu-id="90c2b-330">Rerun validation</span></span>

<span data-ttu-id="90c2b-331">Ověřování je automatické, ale můžete je chtít opakovat ručně.</span><span class="sxs-lookup"><span data-stu-id="90c2b-331">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="90c2b-332">Můžete například vypočítat hodnotu pro vlastnost a chtít znovu spustit ověřování po nastavení vlastnosti na vypočítanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-332">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="90c2b-333">Chcete-li znovu spustit ověřování, zavolejte metodu `TryValidateModel`, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="90c2b-333">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="90c2b-334">Atributy ověřování</span><span class="sxs-lookup"><span data-stu-id="90c2b-334">Validation attributes</span></span>

<span data-ttu-id="90c2b-335">Atributy ověřování umožňují zadat pravidla ověřování pro vlastnosti modelu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-335">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="90c2b-336">Následující příklad z [ukázkové aplikace](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) zobrazuje třídu modelu s poznámkou ověřování atributů.</span><span class="sxs-lookup"><span data-stu-id="90c2b-336">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="90c2b-337">Atribut `[ClassicMovie]` je vlastní ověřovací atribut a jsou integrovány i ostatní.</span><span class="sxs-lookup"><span data-stu-id="90c2b-337">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="90c2b-338">Nezobrazuje se `[ClassicMovie2]`, který ukazuje alternativní způsob implementace vlastního atributu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-338">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="90c2b-339">Předdefinované atributy</span><span class="sxs-lookup"><span data-stu-id="90c2b-339">Built-in attributes</span></span>

<span data-ttu-id="90c2b-340">Mezi předdefinované atributy ověřování patří:</span><span class="sxs-lookup"><span data-stu-id="90c2b-340">Built-in validation attributes include:</span></span>

* <span data-ttu-id="90c2b-341">`[CreditCard]`: ověřuje, zda má vlastnost formát kreditní karty.</span><span class="sxs-lookup"><span data-stu-id="90c2b-341">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="90c2b-342">`[Compare]`: ověří, že se dvě vlastnosti v modelu shodují.</span><span class="sxs-lookup"><span data-stu-id="90c2b-342">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="90c2b-343">Například soubor *Register.cshtml.cs* používá `[Compare]` k ověření, že se dvě zadaná hesla shodují.</span><span class="sxs-lookup"><span data-stu-id="90c2b-343">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="90c2b-344">[Identita uživatelského rozhraní](xref:security/authentication/scaffold-identity) pro zobrazení kódu registrace.</span><span class="sxs-lookup"><span data-stu-id="90c2b-344">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="90c2b-345">`[EmailAddress]`: ověřuje, zda má vlastnost formát e-mailu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-345">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="90c2b-346">`[Phone]`: ověřuje, zda má vlastnost formát telefonního čísla.</span><span class="sxs-lookup"><span data-stu-id="90c2b-346">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="90c2b-347">`[Range]`: ověří, že hodnota vlastnosti spadá do zadaného rozsahu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-347">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="90c2b-348">`[RegularExpression]`: ověří, že hodnota vlastnosti odpovídá zadanému regulárnímu výrazu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-348">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="90c2b-349">`[Required]`: ověří, že pole nemá hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="90c2b-349">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="90c2b-350">Podrobnosti o chování tohoto atributu naleznete v tématu [`[Required]` atributu](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="90c2b-350">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="90c2b-351">`[StringLength]`: ověří, že hodnota řetězcové vlastnosti nepřekračuje zadané omezení délky.</span><span class="sxs-lookup"><span data-stu-id="90c2b-351">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="90c2b-352">`[Url]`: ověřuje, zda má vlastnost formát adresy URL.</span><span class="sxs-lookup"><span data-stu-id="90c2b-352">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="90c2b-353">`[Remote]`: ověřuje vstup na straně klienta voláním metody Action na serveru.</span><span class="sxs-lookup"><span data-stu-id="90c2b-353">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="90c2b-354">Podrobnosti o chování tohoto atributu naleznete v tématu [`[Remote]` atributu](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="90c2b-354">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="90c2b-355">Při použití atributu `[RegularExpression]` s ověřováním na straně klienta je regulární výraz spuštěn v jazyce JavaScript na klientovi.</span><span class="sxs-lookup"><span data-stu-id="90c2b-355">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="90c2b-356">To znamená, že se použije chování pro porovnání [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) .</span><span class="sxs-lookup"><span data-stu-id="90c2b-356">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="90c2b-357">Další informace najdete v tématu [tento problém Githubu](https://github.com/dotnet/corefx/issues/42487).</span><span class="sxs-lookup"><span data-stu-id="90c2b-357">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="90c2b-358">Úplný seznam ověřovacích atributů najdete v oboru názvů [System. ComponentModel. DataAnnotations](xref:System.ComponentModel.DataAnnotations) .</span><span class="sxs-lookup"><span data-stu-id="90c2b-358">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="90c2b-359">Chybovými zprávami</span><span class="sxs-lookup"><span data-stu-id="90c2b-359">Error messages</span></span>

<span data-ttu-id="90c2b-360">Atributy ověřování umožňují zadat chybovou zprávu, která se má zobrazit pro neplatný vstup.</span><span class="sxs-lookup"><span data-stu-id="90c2b-360">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="90c2b-361">Příklad:</span><span class="sxs-lookup"><span data-stu-id="90c2b-361">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="90c2b-362">Interně atributy volají `String.Format` zástupný symbol pro název pole a někdy další zástupné symboly.</span><span class="sxs-lookup"><span data-stu-id="90c2b-362">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="90c2b-363">Příklad:</span><span class="sxs-lookup"><span data-stu-id="90c2b-363">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="90c2b-364">Při použití na vlastnost `Name` by byla chybová zpráva vytvořená v předchozím kódu "Délka názvu musí být v rozmezí 6 až 8."</span><span class="sxs-lookup"><span data-stu-id="90c2b-364">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="90c2b-365">Chcete-li zjistit, které parametry jsou předány `String.Format` pro konkrétní chybovou zprávu atributu, přečtěte si [zdrojový kód pro anotace](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="90c2b-365">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="90c2b-366">[Required] – atribut</span><span class="sxs-lookup"><span data-stu-id="90c2b-366">[Required] attribute</span></span>

<span data-ttu-id="90c2b-367">Ve výchozím nastavení systém ověřování zpracovává parametry bez hodnoty null nebo vlastnosti, jako by měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-367">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="90c2b-368">[Typy hodnot](/dotnet/csharp/language-reference/keywords/value-types) , například `decimal` a `int`, nejsou null.</span><span class="sxs-lookup"><span data-stu-id="90c2b-368">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="90c2b-369">[Požadováno] ověření na serveru</span><span class="sxs-lookup"><span data-stu-id="90c2b-369">[Required] validation on the server</span></span>

<span data-ttu-id="90c2b-370">Na serveru je požadovaná hodnota považována za chybějící, pokud má vlastnost hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="90c2b-370">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="90c2b-371">Pole, které nesmí mít hodnotu null, je vždy platné a chybová zpráva [required] se nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="90c2b-371">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="90c2b-372">Vazba modelu pro vlastnost, která nemůže mít hodnotu null, může selhat, takže se zobrazí chybová zpráva, například `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-372">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="90c2b-373">Chcete-li zadat vlastní chybovou zprávu pro ověřování na straně serveru pro typy, které neumožňují hodnotu null, máte následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="90c2b-373">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="90c2b-374">Převést pole na hodnotu null (například `decimal?` místo `decimal`).</span><span class="sxs-lookup"><span data-stu-id="90c2b-374">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="90c2b-375">Typy hodnot s [povolenou hodnotou null\<t >](/dotnet/csharp/programming-guide/nullable-types/) se považují za standardní typy Nullable.</span><span class="sxs-lookup"><span data-stu-id="90c2b-375">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="90c2b-376">Zadejte výchozí chybovou zprávu, kterou má použít vazba modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-376">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="90c2b-377">Další informace o chybách vazeb modelů, které lze nastavit jako výchozí zprávy pro, naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="90c2b-377">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="90c2b-378">[Požadováno] ověřování na klientovi</span><span class="sxs-lookup"><span data-stu-id="90c2b-378">[Required] validation on the client</span></span>

<span data-ttu-id="90c2b-379">Typy a řetězce, které neumožňují hodnotu null, jsou v porovnání s tímto serverem zpracovávány jinak v klientovi.</span><span class="sxs-lookup"><span data-stu-id="90c2b-379">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="90c2b-380">Na klientovi:</span><span class="sxs-lookup"><span data-stu-id="90c2b-380">On the client:</span></span>

* <span data-ttu-id="90c2b-381">Hodnota se považuje za přítomnou pouze v případě, že je pro ni zadán vstup.</span><span class="sxs-lookup"><span data-stu-id="90c2b-381">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="90c2b-382">Proto ověřování na straně klienta zpracovává typy, které neumožňují hodnotu null, stejné jako typy s možnou hodnotou null.</span><span class="sxs-lookup"><span data-stu-id="90c2b-382">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="90c2b-383">Prázdné znaky v poli řetězce se považují za platný vstup metodou jQuery [vyžadované](https://jqueryvalidation.org/required-method/) ověřením.</span><span class="sxs-lookup"><span data-stu-id="90c2b-383">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="90c2b-384">Ověřování na straně serveru považuje požadované pole řetězce za neplatné, pokud je zadána pouze mezera.</span><span class="sxs-lookup"><span data-stu-id="90c2b-384">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="90c2b-385">Jak bylo uvedeno dříve, typy neumožňující hodnotu null jsou považovány za, ale měly atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-385">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="90c2b-386">To znamená, že získáte ověřování na straně klienta i v případě, že nepoužijete atribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-386">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="90c2b-387">Pokud však atribut nepoužíváte, zobrazí se výchozí chybová zpráva.</span><span class="sxs-lookup"><span data-stu-id="90c2b-387">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="90c2b-388">Chcete-li zadat vlastní chybovou zprávu, použijte atribut.</span><span class="sxs-lookup"><span data-stu-id="90c2b-388">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="90c2b-389">[Remote] – atribut</span><span class="sxs-lookup"><span data-stu-id="90c2b-389">[Remote] attribute</span></span>

<span data-ttu-id="90c2b-390">Atribut `[Remote]` implementuje ověřování na straně klienta, které vyžaduje volání metody na serveru, aby bylo možné určit, zda je vstup pole platný.</span><span class="sxs-lookup"><span data-stu-id="90c2b-390">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="90c2b-391">Aplikace může například potřebovat ověřit, zda se uživatelské jméno již používá.</span><span class="sxs-lookup"><span data-stu-id="90c2b-391">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="90c2b-392">Implementace vzdáleného ověřování:</span><span class="sxs-lookup"><span data-stu-id="90c2b-392">To implement remote validation:</span></span>

1. <span data-ttu-id="90c2b-393">Vytvořte metodu Action pro volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-393">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="90c2b-394">Metoda jQuery Validate [Remote](https://jqueryvalidation.org/remote-method/) očekává odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="90c2b-394">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="90c2b-395">`"true"` znamená, že jsou vstupní data platná.</span><span class="sxs-lookup"><span data-stu-id="90c2b-395">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="90c2b-396">`"false"`, `undefined`nebo `null` znamená, že vstup není platný.</span><span class="sxs-lookup"><span data-stu-id="90c2b-396">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="90c2b-397">Zobrazí výchozí chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-397">Display the default error message.</span></span>
   * <span data-ttu-id="90c2b-398">Jakýkoli jiný řetězec znamená, že vstup je neplatný.</span><span class="sxs-lookup"><span data-stu-id="90c2b-398">Any other string means the input is invalid.</span></span> <span data-ttu-id="90c2b-399">Zobrazí řetězec jako vlastní chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-399">Display the string as a custom error message.</span></span>

   <span data-ttu-id="90c2b-400">Tady je příklad metody akce, která vrací vlastní chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-400">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="90c2b-401">Ve třídě modelu poznámku k vlastnosti s atributem `[Remote]`, který odkazuje na metodu akce ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-401">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="90c2b-402">Atribut `[Remote]` je v oboru názvů `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-402">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="90c2b-403">Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) , pokud nepoužíváte `Microsoft.AspNetCore.App` nebo `Microsoft.AspNetCore.All` Metapackage.</span><span class="sxs-lookup"><span data-stu-id="90c2b-403">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="90c2b-404">Další pole</span><span class="sxs-lookup"><span data-stu-id="90c2b-404">Additional fields</span></span>

<span data-ttu-id="90c2b-405">Vlastnost `AdditionalFields` atributu `[Remote]` umožňuje ověřit kombinace polí s daty na serveru.</span><span class="sxs-lookup"><span data-stu-id="90c2b-405">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="90c2b-406">Pokud například `User` model obsahoval `FirstName` a `LastName` vlastnosti, můžete chtít ověřit, že žádní stávající uživatelé již nemají tento pár názvů.</span><span class="sxs-lookup"><span data-stu-id="90c2b-406">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="90c2b-407">Následující příklad ukazuje, jak použít `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="90c2b-407">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="90c2b-408">`AdditionalFields` lze nastavit explicitně na řetězce `"FirstName"` a `"LastName"`, ale pomocí operátoru [nameof](/dotnet/csharp/language-reference/keywords/nameof) se zjednoduší pozdější refaktoring.</span><span class="sxs-lookup"><span data-stu-id="90c2b-408">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="90c2b-409">Metoda Action pro toto ověření musí přijmout argumenty jméno a příjmení:</span><span class="sxs-lookup"><span data-stu-id="90c2b-409">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="90c2b-410">Když uživatel zadá jméno nebo příjmení, JavaScript vytvoří vzdálené volání, aby viděli, jestli se tento pár názvů povedl.</span><span class="sxs-lookup"><span data-stu-id="90c2b-410">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="90c2b-411">Chcete-li ověřit dvě nebo více dalších polí, poskytněte je jako seznam oddělený čárkami.</span><span class="sxs-lookup"><span data-stu-id="90c2b-411">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="90c2b-412">Chcete-li například přidat vlastnost `MiddleName` do modelu, nastavte atribut `[Remote]`, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-412">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="90c2b-413">`AdditionalFields`, jako jsou všechny argumenty atributu, musí být konstantní výraz.</span><span class="sxs-lookup"><span data-stu-id="90c2b-413">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="90c2b-414">Proto nepoužívejte [interpolované řetězce](/dotnet/csharp/language-reference/keywords/interpolated-strings) nebo volání <xref:System.String.Join*> k inicializaci `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-414">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="90c2b-415">Alternativy k předdefinovaným atributům</span><span class="sxs-lookup"><span data-stu-id="90c2b-415">Alternatives to built-in attributes</span></span>

<span data-ttu-id="90c2b-416">Pokud potřebujete ověření, které neposkytuje předdefinované atributy, můžete:</span><span class="sxs-lookup"><span data-stu-id="90c2b-416">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="90c2b-417">[Vytvořte vlastní atributy](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="90c2b-417">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="90c2b-418">[Implementujte IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="90c2b-418">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="90c2b-419">Vlastní atributy</span><span class="sxs-lookup"><span data-stu-id="90c2b-419">Custom attributes</span></span>

<span data-ttu-id="90c2b-420">U scénářů, které vestavěné atributy ověřování nezpracovávají, můžete vytvořit vlastní ověřovací atributy.</span><span class="sxs-lookup"><span data-stu-id="90c2b-420">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="90c2b-421">Vytvořte třídu, která dědí z <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>a přepište metodu <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="90c2b-421">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="90c2b-422">Metoda `IsValid` přijímá objekt s názvem *hodnota*, který je vstupem k ověření.</span><span class="sxs-lookup"><span data-stu-id="90c2b-422">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="90c2b-423">Přetížení také přijímá objekt `ValidationContext`, který poskytuje další informace, jako je například instance modelu vytvořená vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-423">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="90c2b-424">Následující příklad ověří, že datum vydání filmu v *klasickém* žánru nenásleduje po zadaném roce.</span><span class="sxs-lookup"><span data-stu-id="90c2b-424">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="90c2b-425">Atribut `[ClassicMovie2]` nejprve zkontroluje Žánr a pokračuje pouze v případě, že je *klasický*.</span><span class="sxs-lookup"><span data-stu-id="90c2b-425">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="90c2b-426">U filmů identifikovaných jako klasických kontroluje datum vydání, aby se zajistilo, že není pozdější než limit předaný konstruktoru atributu.)</span><span class="sxs-lookup"><span data-stu-id="90c2b-426">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="90c2b-427">Proměnná `movie` v předchozím příkladu představuje objekt `Movie`, který obsahuje data z odesílání formuláře.</span><span class="sxs-lookup"><span data-stu-id="90c2b-427">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="90c2b-428">Metoda `IsValid` kontroluje datum a Žánr.</span><span class="sxs-lookup"><span data-stu-id="90c2b-428">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="90c2b-429">Po úspěšném ověření `IsValid` vrátí kód `ValidationResult.Success`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-429">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="90c2b-430">Pokud se ověření nepovede, vrátí se `ValidationResult` s chybovou zprávou.</span><span class="sxs-lookup"><span data-stu-id="90c2b-430">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="90c2b-431">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="90c2b-431">IValidatableObject</span></span>

<span data-ttu-id="90c2b-432">Předchozí příklad funguje pouze s `Movie` typy.</span><span class="sxs-lookup"><span data-stu-id="90c2b-432">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="90c2b-433">Další možností ověřování na úrovni třídy je implementace `IValidatableObject` ve třídě modelu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-433">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="90c2b-434">Ověřování uzlu nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="90c2b-434">Top-level node validation</span></span>

<span data-ttu-id="90c2b-435">Uzly nejvyšší úrovně zahrnují:</span><span class="sxs-lookup"><span data-stu-id="90c2b-435">Top-level nodes include:</span></span>

* <span data-ttu-id="90c2b-436">Parametry akce</span><span class="sxs-lookup"><span data-stu-id="90c2b-436">Action parameters</span></span>
* <span data-ttu-id="90c2b-437">Vlastnosti kontroleru</span><span class="sxs-lookup"><span data-stu-id="90c2b-437">Controller properties</span></span>
* <span data-ttu-id="90c2b-438">Parametry obslužné rutiny stránky</span><span class="sxs-lookup"><span data-stu-id="90c2b-438">Page handler parameters</span></span>
* <span data-ttu-id="90c2b-439">Vlastnosti modelu stránky</span><span class="sxs-lookup"><span data-stu-id="90c2b-439">Page model properties</span></span>

<span data-ttu-id="90c2b-440">Kromě ověřování vlastností modelu jsou ověřovány i uzly nejvyšší úrovně svázané s modelem.</span><span class="sxs-lookup"><span data-stu-id="90c2b-440">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="90c2b-441">V následujícím příkladu z ukázkové aplikace `VerifyPhone` metoda používá <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> k ověření parametru `phone` akce:</span><span class="sxs-lookup"><span data-stu-id="90c2b-441">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="90c2b-442">Uzly nejvyšší úrovně mohou použít <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> s atributy ověřování.</span><span class="sxs-lookup"><span data-stu-id="90c2b-442">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="90c2b-443">V následujícím příkladu z ukázkové aplikace určuje metoda `CheckAge`, že parametr `age` musí být při odeslání formuláře svázán z řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-443">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="90c2b-444">Na stránce pro kontrolu stáří (*check. cshtml*) Existují dva formuláře.</span><span class="sxs-lookup"><span data-stu-id="90c2b-444">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="90c2b-445">První formulář odešle hodnotu `Age` `99` jako řetězec dotazu: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-445">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="90c2b-446">Když se odešle správně formátovaný `age` parametr z řetězce dotazu, formulář se ověří.</span><span class="sxs-lookup"><span data-stu-id="90c2b-446">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="90c2b-447">Druhý formulář na stránce Kontrola stáří odesílá `Age` hodnotu v těle žádosti a ověření se nepovede.</span><span class="sxs-lookup"><span data-stu-id="90c2b-447">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="90c2b-448">Vazba se nezdařila, protože parametr `age` musí pocházet z řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-448">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="90c2b-449">Při spuštění s `CompatibilityVersion.Version_2_1` nebo novějším je ve výchozím nastavení povoleno ověřování uzlů nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="90c2b-449">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="90c2b-450">V opačném případě je ověřování uzlu nejvyšší úrovně zakázané.</span><span class="sxs-lookup"><span data-stu-id="90c2b-450">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="90c2b-451">Výchozí možnost může být přepsána nastavením vlastnosti <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> v (`Startup.ConfigureServices`), jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="90c2b-451">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="90c2b-452">Maximální počet chyb</span><span class="sxs-lookup"><span data-stu-id="90c2b-452">Maximum errors</span></span>

<span data-ttu-id="90c2b-453">Ověřování se zastaví, když se dosáhne maximálního počtu chyb (ve výchozím nastavení je 200).</span><span class="sxs-lookup"><span data-stu-id="90c2b-453">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="90c2b-454">Toto číslo můžete nakonfigurovat pomocí následujícího kódu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="90c2b-454">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="90c2b-455">Maximální rekurze</span><span class="sxs-lookup"><span data-stu-id="90c2b-455">Maximum recursion</span></span>

<span data-ttu-id="90c2b-456"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> projde grafem objektu ověřovaného modelu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-456"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="90c2b-457">U modelů, které jsou velmi hlubokoelné nebo nekonečné rekurzivní, může ověřování způsobit přetečení zásobníku.</span><span class="sxs-lookup"><span data-stu-id="90c2b-457">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="90c2b-458">[MvcOptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) poskytuje způsob, jak zastavit ověřování v brzkém případě, kdy rekurze návštěvníka překročí nakonfigurovanou hloubku.</span><span class="sxs-lookup"><span data-stu-id="90c2b-458">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="90c2b-459">Výchozí hodnota `MvcOptions.MaxValidationDepth` je 32 při spuštění s `CompatibilityVersion.Version_2_2` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="90c2b-459">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="90c2b-460">Pro starší verze je hodnota null, což znamená bez omezení hloubky.</span><span class="sxs-lookup"><span data-stu-id="90c2b-460">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="90c2b-461">Automatické krátké okruhy</span><span class="sxs-lookup"><span data-stu-id="90c2b-461">Automatic short-circuit</span></span>

<span data-ttu-id="90c2b-462">Ověřování je automaticky zkrácené (vynecháno), pokud model grafu nevyžaduje ověření.</span><span class="sxs-lookup"><span data-stu-id="90c2b-462">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="90c2b-463">Objekty, které modul runtime přeskočí ověřování pro zahrnutí kolekcí primitivních objektů (například `byte[]`, `string[]`, `Dictionary<string, string>`) a složitých grafů objektů, které nemají žádné validátory.</span><span class="sxs-lookup"><span data-stu-id="90c2b-463">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="90c2b-464">Zakázat ověřování</span><span class="sxs-lookup"><span data-stu-id="90c2b-464">Disable validation</span></span>

<span data-ttu-id="90c2b-465">Zakázání ověřování:</span><span class="sxs-lookup"><span data-stu-id="90c2b-465">To disable validation:</span></span>

1. <span data-ttu-id="90c2b-466">Vytvořte implementaci `IObjectModelValidator`, která neoznačí žádná pole jako neplatnou.</span><span class="sxs-lookup"><span data-stu-id="90c2b-466">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="90c2b-467">Přidejte následující kód, který `Startup.ConfigureServices` k nahrazení výchozí implementace `IObjectModelValidator` v kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="90c2b-467">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="90c2b-468">Pořád se můžou zobrazit chyby stavu modelu, které pocházejí z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-468">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="90c2b-469">Ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="90c2b-469">Client-side validation</span></span>

<span data-ttu-id="90c2b-470">Ověřování na straně klienta brání odeslání, dokud není formulář platný.</span><span class="sxs-lookup"><span data-stu-id="90c2b-470">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="90c2b-471">Tlačítko Odeslat spustí JavaScript, který buď odešle formulář, nebo zobrazí chybové zprávy.</span><span class="sxs-lookup"><span data-stu-id="90c2b-471">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="90c2b-472">Ověřování na straně klienta zabrání zbytečnému přenosu na server, pokud dojde k chybám vstupu na formuláři.</span><span class="sxs-lookup"><span data-stu-id="90c2b-472">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="90c2b-473">Následující odkazy skriptu v *_Layout. cshtml* a *_ValidationScriptsPartial. cshtml* podporují ověřování na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="90c2b-473">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="90c2b-474">Skript [jQuery](https://github.com/aspnet/jquery-validation-unobtrusive) nenáročného ověřování je vlastní knihovna front-end Microsoftu, která se vytváří na oblíbený modul plug-in [jQuery pro ověření](https://jqueryvalidation.org/) .</span><span class="sxs-lookup"><span data-stu-id="90c2b-474">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="90c2b-475">Bez nenáročného ověřování by bylo nutné kód stejné ověřovací logiky nakódovat na dvou místech: jednou v atributech ověřování na straně serveru u vlastností modelu a pak znovu v skriptech na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="90c2b-475">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="90c2b-476">Místo toho [můžou pomocníky značek](xref:mvc/views/tag-helpers/intro) a [nápovědu HTML](xref:mvc/views/overview) používat atributy ověřování a metadata typu z vlastností modelu pro vykreslení HTML 5 `data-` atributů pro prvky formuláře, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="90c2b-476">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="90c2b-477">jQuery nenáročné ověřování analyzuje atributy `data-` a předá logiku do příkazu jQuery Validate a efektivně kopíruje do klienta logiku ověřování na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="90c2b-477">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="90c2b-478">Chyby ověřování můžete zobrazit na klientovi pomocí značek pomocníka, jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="90c2b-478">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="90c2b-479">Předchozí pomocník značek vykresluje následující kód HTML.</span><span class="sxs-lookup"><span data-stu-id="90c2b-479">The preceding tag helpers render the following HTML.</span></span>

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

<span data-ttu-id="90c2b-480">Všimněte si, že atributy `data-` ve výstupu HTML odpovídají atributům ověřování pro vlastnost `ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-480">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="90c2b-481">Atribut `data-val-required` obsahuje chybovou zprávu, která se zobrazí, pokud uživatel neplní pole Datum vydání.</span><span class="sxs-lookup"><span data-stu-id="90c2b-481">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="90c2b-482">jQuery unpassing předá tuto hodnotu metodě jQuery [Required ()](https://jqueryvalidation.org/required-method/) , která pak zobrazí tuto zprávu v doprovodném **\<rozpětí >** elementu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-482">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="90c2b-483">Ověřování datového typu je založené na typu .NET vlastnosti, pokud není přepsána atributem `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-483">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="90c2b-484">Prohlížeče mají vlastní výchozí chybové zprávy, ale tyto zprávy můžou potlačit ověření jQuery nenáročná ověřovací balíček.</span><span class="sxs-lookup"><span data-stu-id="90c2b-484">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="90c2b-485">`[DataType]` atributů a podtřídách, jako je `[EmailAddress]`, vám umožní zadat chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="90c2b-485">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="90c2b-486">Přidání ověřování do dynamických formulářů</span><span class="sxs-lookup"><span data-stu-id="90c2b-486">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="90c2b-487">jQuery – neúspěšné ověření projde logiku ověření a parametry, které se při prvním načtení stránky ověřují.</span><span class="sxs-lookup"><span data-stu-id="90c2b-487">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="90c2b-488">Proto ověřování nefunguje automaticky na dynamicky generovaných formulářích.</span><span class="sxs-lookup"><span data-stu-id="90c2b-488">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="90c2b-489">Chcete-li povolit ověřování, řekněte jQuery nenápadu ověřování, aby se dynamický formulář analyzoval ihned po jeho vytvoření.</span><span class="sxs-lookup"><span data-stu-id="90c2b-489">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="90c2b-490">Například následující kód nastaví ověřování na straně klienta na formuláři přidaném prostřednictvím jazyka AJAX.</span><span class="sxs-lookup"><span data-stu-id="90c2b-490">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="90c2b-491">Metoda `$.validator.unobtrusive.parse()` přijímá selektor jQuery pro svůj jeden argument.</span><span class="sxs-lookup"><span data-stu-id="90c2b-491">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="90c2b-492">Tato metoda oznamuje nenáročné ověřování, aby bylo možné analyzovat `data-` atributů formulářů v rámci tohoto selektoru.</span><span class="sxs-lookup"><span data-stu-id="90c2b-492">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="90c2b-493">Hodnoty těchto atributů jsou poté předány modulu plug-in jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="90c2b-493">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="90c2b-494">Přidání ověřování do dynamických ovládacích prvků</span><span class="sxs-lookup"><span data-stu-id="90c2b-494">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="90c2b-495">Metoda `$.validator.unobtrusive.parse()` pracuje na celém formuláři, nikoli na jednotlivých dynamicky generovaných ovládacích prvcích, jako je `<input>` a `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-495">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="90c2b-496">Chcete-li znovu analyzovat formulář, odeberte data ověřování, která byla přidána při analýze formuláře dříve, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-496">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="90c2b-497">Vlastní ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="90c2b-497">Custom client-side validation</span></span>

<span data-ttu-id="90c2b-498">Vlastní ověřování na straně klienta se provádí generováním `data-` atributů HTML, které fungují s vlastním ověřovacím adaptérem jQuery.</span><span class="sxs-lookup"><span data-stu-id="90c2b-498">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="90c2b-499">Následující vzorový kód adaptéru byl napsán pro `ClassicMovie` a `ClassicMovie2` atributy, které byly představeny dříve v tomto článku:</span><span class="sxs-lookup"><span data-stu-id="90c2b-499">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="90c2b-500">Informace o tom, jak psát adaptéry, najdete v [dokumentaci ke službě jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="90c2b-500">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="90c2b-501">Použití adaptéru pro dané pole je aktivováno `data-` atributy, které:</span><span class="sxs-lookup"><span data-stu-id="90c2b-501">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="90c2b-502">Označte pole jako podléhající ověřování (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="90c2b-502">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="90c2b-503">Identifikujte název ověřovacího pravidla a text chybové zprávy (například `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="90c2b-503">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="90c2b-504">Zadejte další parametry, které vyžaduje validátor (například `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="90c2b-504">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="90c2b-505">Následující příklad ukazuje atributy `data-` pro atribut `ClassicMovie` ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="90c2b-505">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="90c2b-506">Jak bylo uvedeno dříve, [pomocníkům značek a značkám](xref:mvc/views/tag-helpers/intro) [HTML](xref:mvc/views/overview) využívají informace z atributů ověřování k vykreslování atributů `data-`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-506">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="90c2b-507">Existují dvě možnosti pro psaní kódu, který je výsledkem vytváření vlastních atributů `data-` HTML:</span><span class="sxs-lookup"><span data-stu-id="90c2b-507">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="90c2b-508">Vytvořte třídu, která je odvozena z `AttributeAdapterBase<TAttribute>` a třídu, která implementuje `IValidationAttributeAdapterProvider`a zaregistrujte svůj atribut a jeho adaptér v DI.</span><span class="sxs-lookup"><span data-stu-id="90c2b-508">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="90c2b-509">Tato metoda následuje za [instančním objektem zodpovědnosti](https://wikipedia.org/wiki/Single_responsibility_principle) v tomto ověřovacím kódu souvisejícím se serverem a klientem je v samostatných třídách.</span><span class="sxs-lookup"><span data-stu-id="90c2b-509">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="90c2b-510">Adaptér má také výhodu, že protože je zaregistrován v DI, jsou v případě potřeby k dispozici jiné služby v DI.</span><span class="sxs-lookup"><span data-stu-id="90c2b-510">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="90c2b-511">Implementujte `IClientModelValidator` ve vaší třídě `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-511">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="90c2b-512">Tato metoda může být vhodná, pokud atribut neprovádí žádné ověřování na straně serveru a nepotřebuje žádné služby od DI.</span><span class="sxs-lookup"><span data-stu-id="90c2b-512">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="90c2b-513">AttributeAdapter pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="90c2b-513">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="90c2b-514">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovie` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="90c2b-514">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="90c2b-515">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="90c2b-515">To add client validation by using this method:</span></span>

1. <span data-ttu-id="90c2b-516">Vytvořte třídu adaptéru atributů pro vlastní ověřovací atribut.</span><span class="sxs-lookup"><span data-stu-id="90c2b-516">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="90c2b-517">Odvodit třídu z [AttributeAdapterBase\<t >](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="90c2b-517">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="90c2b-518">Vytvořte `AddValidation` metodu, která přidá atributy `data-` do vykresleného výstupu, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-518">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="90c2b-519">Vytvořte třídu poskytovatele adaptéru, která implementuje <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="90c2b-519">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="90c2b-520">V metodě `GetAttributeAdapter` předejte vlastní atribut konstruktoru adaptéru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-520">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="90c2b-521">Zaregistrujte poskytovatele adaptéru pro DI v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="90c2b-521">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="90c2b-522">IClientModelValidator pro ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="90c2b-522">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="90c2b-523">Tato metoda vykreslování atributů `data-` ve formátu HTML je používána atributem `ClassicMovie2` v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="90c2b-523">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="90c2b-524">Přidání ověřování klientů pomocí této metody:</span><span class="sxs-lookup"><span data-stu-id="90c2b-524">To add client validation by using this method:</span></span>

* <span data-ttu-id="90c2b-525">Ve vlastním ověřovacím atributu implementujte rozhraní `IClientModelValidator` a vytvořte metodu `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="90c2b-525">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="90c2b-526">V metodě `AddValidation` přidejte `data-` atributy pro ověření, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="90c2b-526">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="90c2b-527">Zakázat ověřování na straně klienta</span><span class="sxs-lookup"><span data-stu-id="90c2b-527">Disable client-side validation</span></span>

<span data-ttu-id="90c2b-528">Následující kód zakáže ověřování klienta v zobrazeních MVC:</span><span class="sxs-lookup"><span data-stu-id="90c2b-528">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="90c2b-529">A v Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="90c2b-529">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="90c2b-530">Další možností pro vypnutí ověřování klienta je zadání komentáře k odkazu na `_ValidationScriptsPartial` v souboru *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="90c2b-530">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90c2b-531">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="90c2b-531">Additional resources</span></span>

* [<span data-ttu-id="90c2b-532">Obor názvů System. ComponentModel. DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="90c2b-532">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="90c2b-533">Vazby modelu</span><span class="sxs-lookup"><span data-stu-id="90c2b-533">Model Binding</span></span>](model-binding.md)

::: moniker-end

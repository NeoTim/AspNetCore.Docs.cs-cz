---
title: Vlastní formátovací moduly v ASP.NET Core Web API
author: rick-anderson
description: Naučte se vytvářet a používat vlastní formátovací moduly pro webová rozhraní API v ASP.NET Core.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: ecf233273a28df9b2d35edf3264b8c73b16759e5
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021871"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="98a2e-103">Vlastní formátovací moduly v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="98a2e-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="98a2e-104">[Kirka Larkin](https://twitter.com/serpent5) a [Dykstra](https://github.com/tdykstra).</span><span class="sxs-lookup"><span data-stu-id="98a2e-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Tom Dykstra](https://github.com/tdykstra).</span></span>

<span data-ttu-id="98a2e-105">ASP.NET Core MVC podporuje výměnu dat ve webových rozhraních API pomocí formátování vstupu a výstupu.</span><span class="sxs-lookup"><span data-stu-id="98a2e-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="98a2e-106">Vstupní formátovací moduly jsou používány [vazbami modelů](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="98a2e-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="98a2e-107">Výstupní formátovací moduly se používají k [formátování odpovědí](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="98a2e-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="98a2e-108">Rozhraní poskytuje předdefinované vstupní a výstupní formátovací moduly pro JSON a XML.</span><span class="sxs-lookup"><span data-stu-id="98a2e-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="98a2e-109">Poskytuje vestavěný výstupní formátovací modul pro prostý text, ale neposkytuje vstupní formátovací modul pro prostý text.</span><span class="sxs-lookup"><span data-stu-id="98a2e-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="98a2e-110">Tento článek ukazuje, jak přidat podporu pro další formáty vytvořením vlastních formátovacích modulů.</span><span class="sxs-lookup"><span data-stu-id="98a2e-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="98a2e-111">Příklad vlastního formátovacího modulu zadávání prostého textu najdete v tématu [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="98a2e-111">For an example of a custom plain text input formatter, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="98a2e-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="98a2e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="98a2e-113">Kdy použít vlastní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="98a2e-113">When to use custom formatters</span></span>

<span data-ttu-id="98a2e-114">Pomocí vlastního formátovacího modulu můžete přidat podporu pro typ obsahu, který není zpracován vestavěnými formátovacími moduly.</span><span class="sxs-lookup"><span data-stu-id="98a2e-114">Use a custom formatter to add support for a content type that isn't handled by the built-in formatters.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="98a2e-115">Přehled způsobu použití vlastního formátovacího modulu</span><span class="sxs-lookup"><span data-stu-id="98a2e-115">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="98a2e-116">Vytvoření vlastního formátovacího modulu:</span><span class="sxs-lookup"><span data-stu-id="98a2e-116">To create a custom formatter:</span></span>

* <span data-ttu-id="98a2e-117">Pro serializaci dat odesílaných klientovi vytvořte výstupní třídu formátování.</span><span class="sxs-lookup"><span data-stu-id="98a2e-117">For serializing data sent to the client, create an output formatter class.</span></span>
* <span data-ttu-id="98a2e-118">Pro deserializaci dat přijatých z klienta vytvořte vstupní třídu formátování Input.</span><span class="sxs-lookup"><span data-stu-id="98a2e-118">For deserializing data received from the client, create an input formatter class.</span></span>
* <span data-ttu-id="98a2e-119">Přidejte instance formátovacích tříd do `InputFormatters` kolekcí a `OutputFormatters` v rámci <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="98a2e-119">Add instances of formatter classes to the `InputFormatters` and `OutputFormatters` collections in <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="98a2e-120">Jak vytvořit vlastní třídu formátování</span><span class="sxs-lookup"><span data-stu-id="98a2e-120">How to create a custom formatter class</span></span>

<span data-ttu-id="98a2e-121">Vytvoření formátovacího modulu:</span><span class="sxs-lookup"><span data-stu-id="98a2e-121">To create a formatter:</span></span>

* <span data-ttu-id="98a2e-122">Odvodit třídu od příslušné základní třídy.</span><span class="sxs-lookup"><span data-stu-id="98a2e-122">Derive the class from the appropriate base class.</span></span> <span data-ttu-id="98a2e-123">Ukázková aplikace je odvozena z <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> a <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="98a2e-123">The sample app derives from <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> and <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter>.</span></span>
* <span data-ttu-id="98a2e-124">Zadejte platné typy médií a kódování v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="98a2e-124">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="98a2e-125">Přepište <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> metody a.</span><span class="sxs-lookup"><span data-stu-id="98a2e-125">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> and <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> methods.</span></span>
* <span data-ttu-id="98a2e-126">Přepište <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> `WriteResponseBodyAsync` metody a.</span><span class="sxs-lookup"><span data-stu-id="98a2e-126">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> and `WriteResponseBodyAsync` methods.</span></span>

<span data-ttu-id="98a2e-127">Následující kód ukazuje `VcardOutputFormatter` třídu z [ukázky](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span><span class="sxs-lookup"><span data-stu-id="98a2e-127">The following code shows the `VcardOutputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="98a2e-128">Odvození od příslušné základní třídy</span><span class="sxs-lookup"><span data-stu-id="98a2e-128">Derive from the appropriate base class</span></span>

<span data-ttu-id="98a2e-129">Pro typy textových médií (například vCard) je odvozen ze <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> základní třídy nebo.</span><span class="sxs-lookup"><span data-stu-id="98a2e-129">For text media types (for example, vCard), derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> base class.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

<span data-ttu-id="98a2e-130">Pro binární typy je odvozen ze <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> základní třídy nebo.</span><span class="sxs-lookup"><span data-stu-id="98a2e-130">For binary types, derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="98a2e-131">Zadejte platné typy médií a kódování.</span><span class="sxs-lookup"><span data-stu-id="98a2e-131">Specify valid media types and encodings</span></span>

<span data-ttu-id="98a2e-132">V konstruktoru zadejte platné typy médií a kódování přidáním do `SupportedMediaTypes` `SupportedEncodings` kolekce a.</span><span class="sxs-lookup"><span data-stu-id="98a2e-132">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

<span data-ttu-id="98a2e-133">Třída formátovacího modulu **nemůže použít injektáže** konstruktoru pro jeho závislosti.</span><span class="sxs-lookup"><span data-stu-id="98a2e-133">A formatter class can **not** use constructor injection for its dependencies.</span></span> <span data-ttu-id="98a2e-134">Například `ILogger<VcardOutputFormatter>` nelze přidat jako parametr do konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="98a2e-134">For example, `ILogger<VcardOutputFormatter>` cannot be added as a parameter to the constructor.</span></span> <span data-ttu-id="98a2e-135">Chcete-li získat přístup ke službám, použijte kontextový objekt, který se předává do metod.</span><span class="sxs-lookup"><span data-stu-id="98a2e-135">To access services, use the context object that gets passed in to the methods.</span></span> <span data-ttu-id="98a2e-136">Příklad kódu v tomto článku a [Ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ukazuje, jak to provést.</span><span class="sxs-lookup"><span data-stu-id="98a2e-136">A code example in this article and the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) show how to do this.</span></span>

### <a name="override-canreadtype-and-canwritetype"></a><span data-ttu-id="98a2e-137">Přepsat CanReadType a CanWriteType</span><span class="sxs-lookup"><span data-stu-id="98a2e-137">Override CanReadType and CanWriteType</span></span>

<span data-ttu-id="98a2e-138">Určete typ pro deserializaci nebo serializaci pomocí přepsání `CanReadType` `CanWriteType` metod nebo.</span><span class="sxs-lookup"><span data-stu-id="98a2e-138">Specify the type to deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="98a2e-139">Například vytváření textu v souborovém formátu vCard z `Contact` typu a naopak.</span><span class="sxs-lookup"><span data-stu-id="98a2e-139">For example, creating vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="98a2e-140">Metoda CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="98a2e-140">The CanWriteResult method</span></span>

<span data-ttu-id="98a2e-141">V některých případech `CanWriteResult` je nutné přepsat místo `CanWriteType` .</span><span class="sxs-lookup"><span data-stu-id="98a2e-141">In some scenarios, `CanWriteResult` must be overridden rather than `CanWriteType`.</span></span> <span data-ttu-id="98a2e-142">Použijte `CanWriteResult` , pokud jsou splněné následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="98a2e-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="98a2e-143">Metoda Action vrátí třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="98a2e-143">The action method returns a model class.</span></span>
* <span data-ttu-id="98a2e-144">K dispozici jsou odvozené třídy, které mohou být vráceny za běhu.</span><span class="sxs-lookup"><span data-stu-id="98a2e-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="98a2e-145">Odvozená třída vrácená akcí musí být známá za běhu.</span><span class="sxs-lookup"><span data-stu-id="98a2e-145">The derived class returned by the action must be known at runtime.</span></span>

<span data-ttu-id="98a2e-146">Předpokládejme například metodu Action:</span><span class="sxs-lookup"><span data-stu-id="98a2e-146">For example, suppose the action method:</span></span>

* <span data-ttu-id="98a2e-147">Signatura vrátí `Person` typ.</span><span class="sxs-lookup"><span data-stu-id="98a2e-147">Signature returns a `Person` type.</span></span>
* <span data-ttu-id="98a2e-148">Může vracet `Student` typ nebo `Instructor` , který je odvozen z `Person` .</span><span class="sxs-lookup"><span data-stu-id="98a2e-148">Can return a `Student` or `Instructor` type that derives from `Person`.</span></span> 

<span data-ttu-id="98a2e-149">Aby formátovací modul zpracovával pouze `Student` objekty, ověřte typ <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> v objektu Context, který je k metodě k dispozici `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="98a2e-149">For the formatter to handle only `Student` objects, check the type of <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="98a2e-150">Když metoda akce vrátí `IActionResult` :</span><span class="sxs-lookup"><span data-stu-id="98a2e-150">When the action method returns `IActionResult`:</span></span>

* <span data-ttu-id="98a2e-151">Není nutné používat `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="98a2e-151">It's not necessary to use `CanWriteResult`.</span></span>
* <span data-ttu-id="98a2e-152">`CanWriteType`Metoda přijímá typ modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="98a2e-152">The `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a><span data-ttu-id="98a2e-153">Přepsat ReadRequestBodyAsync a WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="98a2e-153">Override ReadRequestBodyAsync and WriteResponseBodyAsync</span></span>

<span data-ttu-id="98a2e-154">Deserializace nebo serializace je prováděna v `ReadRequestBodyAsync` nebo `WriteResponseBodyAsync` .</span><span class="sxs-lookup"><span data-stu-id="98a2e-154">Deserialization or serialization is performed in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="98a2e-155">Následující příklad ukazuje, jak získat služby z kontejneru injektáže na základě závislostí.</span><span class="sxs-lookup"><span data-stu-id="98a2e-155">The following example shows how to get services from the dependency injection container.</span></span> <span data-ttu-id="98a2e-156">Z parametrů konstruktoru nelze získat služby.</span><span class="sxs-lookup"><span data-stu-id="98a2e-156">Services can't be obtained from constructor parameters.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="98a2e-157">Jak konfigurovat MVC pro použití vlastního formátovacího modulu</span><span class="sxs-lookup"><span data-stu-id="98a2e-157">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="98a2e-158">Chcete-li použít vlastní formátovací modul, přidejte instanci formátovací třídy do `InputFormatters` `OutputFormatters` kolekce nebo.</span><span class="sxs-lookup"><span data-stu-id="98a2e-158">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

<span data-ttu-id="98a2e-159">Formátovací moduly jsou vyhodnocovány v pořadí, ve kterém je vložíte.</span><span class="sxs-lookup"><span data-stu-id="98a2e-159">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="98a2e-160">První z nich má přednost.</span><span class="sxs-lookup"><span data-stu-id="98a2e-160">The first one takes precedence.</span></span>

## <a name="the-complete-vcardinputformatter-class"></a><span data-ttu-id="98a2e-161">Úplná `VcardInputFormatter` Třída</span><span class="sxs-lookup"><span data-stu-id="98a2e-161">The complete `VcardInputFormatter` class</span></span>

<span data-ttu-id="98a2e-162">Následující kód ukazuje `VcardInputFormatter` třídu z [ukázky](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span><span class="sxs-lookup"><span data-stu-id="98a2e-162">The following code shows the `VcardInputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a><span data-ttu-id="98a2e-163">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="98a2e-163">Test the app</span></span>

<span data-ttu-id="98a2e-164">[Spusťte ukázkovou aplikaci pro tento článek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), která implementuje základní formátovací a výstupní formátovací moduly vCard.</span><span class="sxs-lookup"><span data-stu-id="98a2e-164">[Run the sample app for this article](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), which implements basic vCard input and output formatters.</span></span> <span data-ttu-id="98a2e-165">Aplikace čte a zapisuje soubory vCard podobně jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="98a2e-165">The app reads and writes vCards similar to the following:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

<span data-ttu-id="98a2e-166">Chcete-li zobrazit výstup v programu vCard, spusťte aplikaci a odešlete požadavek GET s hlavičkou Accept `text/vcard` do `https://localhost:5001/api/contacts` .</span><span class="sxs-lookup"><span data-stu-id="98a2e-166">To see vCard output, run the app and send a Get request with Accept header `text/vcard` to `https://localhost:5001/api/contacts`.</span></span>

<span data-ttu-id="98a2e-167">Chcete-li přidat soubor vCard do kolekce kontaktů v paměti:</span><span class="sxs-lookup"><span data-stu-id="98a2e-167">To add a vCard to the in-memory collection of contacts:</span></span>

* <span data-ttu-id="98a2e-168">Odešlete `Post` požadavek na `/api/contacts` nástroj, jako je například post.</span><span class="sxs-lookup"><span data-stu-id="98a2e-168">Send a `Post` request to `/api/contacts` with a tool like Postman.</span></span>
* <span data-ttu-id="98a2e-169">Nastavte `Content-Type` hlavičku na `text/vcard` .</span><span class="sxs-lookup"><span data-stu-id="98a2e-169">Set the `Content-Type` header to `text/vcard`.</span></span>
* <span data-ttu-id="98a2e-170">Nastavte `vCard` text v těle formátovaného jako v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="98a2e-170">Set `vCard` text in the body, formatted like the preceding example.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98a2e-171">Další materiály</span><span class="sxs-lookup"><span data-stu-id="98a2e-171">Additional resources</span></span>

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>

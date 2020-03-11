---
title: Vlastní formátovací moduly v ASP.NET Core Web API
author: rick-anderson
description: Naučte se vytvářet a používat vlastní formátovací moduly pro webová rozhraní API v ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: dd25cda460ba758cd07de094eaadd1f2d8c28657
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667669"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="5adde-103">Vlastní formátovací moduly v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="5adde-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="5adde-104">tím, že [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="5adde-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="5adde-105">ASP.NET Core MVC podporuje výměnu dat ve webových rozhraních API pomocí formátování vstupu a výstupu.</span><span class="sxs-lookup"><span data-stu-id="5adde-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="5adde-106">Vstupní formátovací moduly jsou používány [vazbami modelů](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="5adde-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="5adde-107">Výstupní formátovací moduly se používají k [formátování odpovědí](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="5adde-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="5adde-108">Rozhraní poskytuje předdefinované vstupní a výstupní formátovací moduly pro JSON a XML.</span><span class="sxs-lookup"><span data-stu-id="5adde-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="5adde-109">Poskytuje vestavěný výstupní formátovací modul pro prostý text, ale neposkytuje vstupní formátovací modul pro prostý text.</span><span class="sxs-lookup"><span data-stu-id="5adde-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="5adde-110">Tento článek ukazuje, jak přidat podporu pro další formáty vytvořením vlastních formátovacích modulů.</span><span class="sxs-lookup"><span data-stu-id="5adde-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="5adde-111">Příklad vlastního vstupního formátovacího modulu pro prostý text najdete v tématu [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="5adde-111">For an example of a custom input formatter for plain text, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="5adde-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5adde-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="5adde-113">Kdy použít vlastní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="5adde-113">When to use custom formatters</span></span>

<span data-ttu-id="5adde-114">Vlastní formátovací modul použijte v případě, že chcete, aby proces [vyjednávání obsahu](xref:web-api/advanced/formatting#content-negotiation) podporoval typ obsahu, který není podporován vestavěnými formátovacími moduly.</span><span class="sxs-lookup"><span data-stu-id="5adde-114">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters.</span></span>

<span data-ttu-id="5adde-115">Pokud například někteří klienti webového rozhraní API mohou zpracovat formát [Protobuf](https://github.com/google/protobuf) , můžete chtít použít Protobuf u těchto klientů, protože je efektivnější.</span><span class="sxs-lookup"><span data-stu-id="5adde-115">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="5adde-116">Nebo můžete chtít, aby vaše webové rozhraní API odesílalo jména kontaktů a adresy ve formátu [vCard](https://wikipedia.org/wiki/VCard) , což je běžně používaný formát pro výměnu dat kontaktů.</span><span class="sxs-lookup"><span data-stu-id="5adde-116">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="5adde-117">Ukázková aplikace, která je součástí tohoto článku, implementuje jednoduchý formátovací modul vCard.</span><span class="sxs-lookup"><span data-stu-id="5adde-117">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="5adde-118">Přehled způsobu použití vlastního formátovacího modulu</span><span class="sxs-lookup"><span data-stu-id="5adde-118">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="5adde-119">Tady je postup vytvoření a použití vlastního formátovacího modulu:</span><span class="sxs-lookup"><span data-stu-id="5adde-119">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="5adde-120">Vytvořte výstupní třídu formátování výstupu, pokud chcete serializovat data pro odeslání klientovi.</span><span class="sxs-lookup"><span data-stu-id="5adde-120">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="5adde-121">Vytvořte vstupní třídu formátování, pokud chcete deserializovat data přijatá z klienta.</span><span class="sxs-lookup"><span data-stu-id="5adde-121">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="5adde-122">Přidejte instance formátovacích modulů do kolekce `InputFormatters` a `OutputFormatters` v [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="5adde-122">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="5adde-123">Následující části obsahují pokyny a příklady kódu pro každý z těchto kroků.</span><span class="sxs-lookup"><span data-stu-id="5adde-123">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="5adde-124">Jak vytvořit vlastní třídu formátování</span><span class="sxs-lookup"><span data-stu-id="5adde-124">How to create a custom formatter class</span></span>

<span data-ttu-id="5adde-125">Vytvoření formátovacího modulu:</span><span class="sxs-lookup"><span data-stu-id="5adde-125">To create a formatter:</span></span>

* <span data-ttu-id="5adde-126">Odvodit třídu od příslušné základní třídy.</span><span class="sxs-lookup"><span data-stu-id="5adde-126">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="5adde-127">Zadejte platné typy médií a kódování v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="5adde-127">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="5adde-128">Přepsat `CanReadType`/`CanWriteType` metody</span><span class="sxs-lookup"><span data-stu-id="5adde-128">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="5adde-129">Přepsat `ReadRequestBodyAsync`/`WriteResponseBodyAsync` metody</span><span class="sxs-lookup"><span data-stu-id="5adde-129">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="5adde-130">Odvození od příslušné základní třídy</span><span class="sxs-lookup"><span data-stu-id="5adde-130">Derive from the appropriate base class</span></span>

<span data-ttu-id="5adde-131">Pro typy textových médií (například vCard) je odvozen ze základní třídy [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) nebo [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) .</span><span class="sxs-lookup"><span data-stu-id="5adde-131">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="5adde-132">Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="5adde-132">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="5adde-133">Pro binární typy je odvozeno od základní třídy [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) nebo [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) .</span><span class="sxs-lookup"><span data-stu-id="5adde-133">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="5adde-134">Zadejte platné typy médií a kódování.</span><span class="sxs-lookup"><span data-stu-id="5adde-134">Specify valid media types and encodings</span></span>

<span data-ttu-id="5adde-135">V konstruktoru zadejte platné typy médií a kódování přidáním do kolekce `SupportedMediaTypes` a `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="5adde-135">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="5adde-136">Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="5adde-136">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="5adde-137">Vložení závislosti konstruktoru nelze provést ve třídě formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="5adde-137">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="5adde-138">Například nemůžete získat protokolovací nástroj přidáním parametru protokolovacího nástroje do konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="5adde-138">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="5adde-139">Pro přístup ke službám je nutné použít objekt kontextu, který se předává do vašich metod.</span><span class="sxs-lookup"><span data-stu-id="5adde-139">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="5adde-140">[Následující](#read-write) příklad kódu ukazuje, jak to provést.</span><span class="sxs-lookup"><span data-stu-id="5adde-140">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="5adde-141">Přepsat CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="5adde-141">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="5adde-142">Zadejte typ, který lze deserializovat nebo serializovat, přepsáním `CanReadType` nebo `CanWriteType`ch metod.</span><span class="sxs-lookup"><span data-stu-id="5adde-142">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="5adde-143">Můžete například vytvořit text v vCard pouze z `Contact`ho typu a naopak.</span><span class="sxs-lookup"><span data-stu-id="5adde-143">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="5adde-144">Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="5adde-144">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="5adde-145">Metoda CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="5adde-145">The CanWriteResult method</span></span>

<span data-ttu-id="5adde-146">V některých případech je nutné přepsat `CanWriteResult` místo `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="5adde-146">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="5adde-147">Pokud jsou splněné následující podmínky, použijte `CanWriteResult`:</span><span class="sxs-lookup"><span data-stu-id="5adde-147">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="5adde-148">Vaše metoda akce vrátí třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="5adde-148">Your action method returns a model class.</span></span>
* <span data-ttu-id="5adde-149">K dispozici jsou odvozené třídy, které mohou být vráceny za běhu.</span><span class="sxs-lookup"><span data-stu-id="5adde-149">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="5adde-150">Je potřeba znát za běhu, který tato akce vrátila odvozenou třídu.</span><span class="sxs-lookup"><span data-stu-id="5adde-150">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="5adde-151">Předpokládejme například, že váš podpis metody akce vrací typ `Person`, ale může vracet `Student` nebo `Instructor` typ, který je odvozen od `Person`.</span><span class="sxs-lookup"><span data-stu-id="5adde-151">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="5adde-152">Chcete-li, aby formátovací modul zpracovával pouze `Student` objekty, ověřte typ [objektu](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) v objektu Context, který je k dispozici metodě `CanWriteResult`.</span><span class="sxs-lookup"><span data-stu-id="5adde-152">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="5adde-153">Všimněte si, že není nutné použít `CanWriteResult`, když metoda akce vrátí `IActionResult`; v takovém případě metoda `CanWriteType` přijímá typ modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="5adde-153">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="5adde-154">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="5adde-154">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="5adde-155">Provedete skutečnou práci při deserializaci nebo serializaci v `ReadRequestBodyAsync` nebo `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="5adde-155">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="5adde-156">Zvýrazněné řádky v následujícím příkladu ukazují, jak získat služby z kontejneru vkládání závislostí (nemůžete je získat z parametrů konstruktoru).</span><span class="sxs-lookup"><span data-stu-id="5adde-156">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="5adde-157">Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="5adde-157">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="5adde-158">Jak konfigurovat MVC pro použití vlastního formátovacího modulu</span><span class="sxs-lookup"><span data-stu-id="5adde-158">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="5adde-159">Chcete-li použít vlastní formátovací modul, přidejte instanci formátovací třídy do kolekce `InputFormatters` nebo `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="5adde-159">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="5adde-160">Formátovací moduly jsou vyhodnocovány v pořadí, ve kterém je vložíte.</span><span class="sxs-lookup"><span data-stu-id="5adde-160">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="5adde-161">První z nich má přednost.</span><span class="sxs-lookup"><span data-stu-id="5adde-161">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5adde-162">Další kroky</span><span class="sxs-lookup"><span data-stu-id="5adde-162">Next steps</span></span>

* <span data-ttu-id="5adde-163">[Ukázková aplikace pro tento dokument](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), která implementuje jednoduché formátovací a výstupní formátovací moduly vCard</span><span class="sxs-lookup"><span data-stu-id="5adde-163">[Sample app for this doc](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="5adde-164">Aplikace čte a zapisuje soubory vCard, které vypadají jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="5adde-164">The apps reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="5adde-165">Chcete-li zobrazit výstup v programu vCard, spusťte aplikaci a odešlete požadavek GET s hlavičkou Accept "text/vCard" do `http://localhost:63313/api/contacts/` (při spuštění ze sady Visual Studio) nebo `http://localhost:5000/api/contacts/` (při spuštění z příkazového řádku).</span><span class="sxs-lookup"><span data-stu-id="5adde-165">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="5adde-166">Chcete-li přidat soubor vCard do kolekce kontaktů v paměti, odešlete požadavek post na stejnou adresu URL s hlavičkou Content-Type "text/vCard" a textem vCard v těle, který je formátován jako příklad výše.</span><span class="sxs-lookup"><span data-stu-id="5adde-166">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>

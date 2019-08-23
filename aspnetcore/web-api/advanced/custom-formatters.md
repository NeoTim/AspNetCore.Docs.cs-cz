---
title: Vlastní formátovací moduly v ASP.NET Core Web API
author: rick-anderson
description: Naučte se vytvářet a používat vlastní formátovací moduly pro webová rozhraní API v ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 6fb7e192bf3e943eb9018b08fb87a833d3643208
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975672"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="22f07-103">Vlastní formátovací moduly v ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="22f07-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="22f07-104">Tím, že [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="22f07-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="22f07-105">ASP.NET Core MVC obsahuje integrovanou podporu výměny dat ve webových rozhraních API pomocí JSON nebo XML.</span><span class="sxs-lookup"><span data-stu-id="22f07-105">ASP.NET Core MVC has built-in support for data exchange in web APIs by using JSON or XML.</span></span> <span data-ttu-id="22f07-106">Tento článek ukazuje, jak přidat podporu pro další formáty vytvořením vlastních formátovacích modulů.</span><span class="sxs-lookup"><span data-stu-id="22f07-106">This article shows how to add support for additional formats by creating custom formatters.</span></span>

<span data-ttu-id="22f07-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="22f07-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="22f07-108">Kdy použít vlastní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="22f07-108">When to use custom formatters</span></span>

<span data-ttu-id="22f07-109">Vlastní formátovací modul použijte v případě, že chcete, aby proces [vyjednávání obsahu](xref:web-api/advanced/formatting#content-negotiation) podporoval typ obsahu, který není podporován vestavěnými formátovacími moduly (JSON a XML).</span><span class="sxs-lookup"><span data-stu-id="22f07-109">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters (JSON and XML).</span></span>

<span data-ttu-id="22f07-110">Pokud například někteří klienti webového rozhraní API mohou zpracovat formát [Protobuf](https://github.com/google/protobuf) , můžete chtít použít Protobuf u těchto klientů, protože je efektivnější.</span><span class="sxs-lookup"><span data-stu-id="22f07-110">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="22f07-111">Nebo můžete chtít, aby vaše webové rozhraní API odesílalo jména kontaktů a adresy ve formátu [vCard](https://wikipedia.org/wiki/VCard) , což je běžně používaný formát pro výměnu dat kontaktů.</span><span class="sxs-lookup"><span data-stu-id="22f07-111">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="22f07-112">Ukázková aplikace, která je součástí tohoto článku, implementuje jednoduchý formátovací modul vCard.</span><span class="sxs-lookup"><span data-stu-id="22f07-112">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="22f07-113">Přehled způsobu použití vlastního formátovacího modulu</span><span class="sxs-lookup"><span data-stu-id="22f07-113">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="22f07-114">Tady je postup vytvoření a použití vlastního formátovacího modulu:</span><span class="sxs-lookup"><span data-stu-id="22f07-114">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="22f07-115">Vytvořte výstupní třídu formátování výstupu, pokud chcete serializovat data pro odeslání klientovi.</span><span class="sxs-lookup"><span data-stu-id="22f07-115">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="22f07-116">Vytvořte vstupní třídu formátování, pokud chcete deserializovat data přijatá z klienta.</span><span class="sxs-lookup"><span data-stu-id="22f07-116">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="22f07-117">Přidejte instance formátovacích modulů do `InputFormatters` kolekcí a `OutputFormatters` v [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="22f07-117">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="22f07-118">Následující části obsahují pokyny a příklady kódu pro každý z těchto kroků.</span><span class="sxs-lookup"><span data-stu-id="22f07-118">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="22f07-119">Jak vytvořit vlastní třídu formátování</span><span class="sxs-lookup"><span data-stu-id="22f07-119">How to create a custom formatter class</span></span>

<span data-ttu-id="22f07-120">Vytvoření formátovacího modulu:</span><span class="sxs-lookup"><span data-stu-id="22f07-120">To create a formatter:</span></span>

* <span data-ttu-id="22f07-121">Odvodit třídu od příslušné základní třídy.</span><span class="sxs-lookup"><span data-stu-id="22f07-121">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="22f07-122">Zadejte platné typy médií a kódování v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="22f07-122">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="22f07-123">Metody `CanReadType` přepsání / `CanWriteType`</span><span class="sxs-lookup"><span data-stu-id="22f07-123">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="22f07-124">Metody `ReadRequestBodyAsync` přepsání / `WriteResponseBodyAsync`</span><span class="sxs-lookup"><span data-stu-id="22f07-124">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="22f07-125">Odvození od příslušné základní třídy</span><span class="sxs-lookup"><span data-stu-id="22f07-125">Derive from the appropriate base class</span></span>

<span data-ttu-id="22f07-126">Pro typy textových médií (například vCard) je odvozen ze základní třídy [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) nebo [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) .</span><span class="sxs-lookup"><span data-stu-id="22f07-126">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="22f07-127">Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="22f07-127">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="22f07-128">Pro binární typy je odvozeno od základní třídy [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) nebo [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) .</span><span class="sxs-lookup"><span data-stu-id="22f07-128">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="22f07-129">Zadejte platné typy médií a kódování.</span><span class="sxs-lookup"><span data-stu-id="22f07-129">Specify valid media types and encodings</span></span>

<span data-ttu-id="22f07-130">V konstruktoru zadejte platné typy médií a kódování přidáním do `SupportedMediaTypes` kolekce a. `SupportedEncodings`</span><span class="sxs-lookup"><span data-stu-id="22f07-130">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="22f07-131">Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="22f07-131">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="22f07-132">Vložení závislosti konstruktoru nelze provést ve třídě formátovacího modulu.</span><span class="sxs-lookup"><span data-stu-id="22f07-132">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="22f07-133">Například nemůžete získat protokolovací nástroj přidáním parametru protokolovacího nástroje do konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="22f07-133">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="22f07-134">Pro přístup ke službám je nutné použít objekt kontextu, který se předává do vašich metod.</span><span class="sxs-lookup"><span data-stu-id="22f07-134">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="22f07-135">[Následující](#read-write) příklad kódu ukazuje, jak to provést.</span><span class="sxs-lookup"><span data-stu-id="22f07-135">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="22f07-136">Přepsat CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="22f07-136">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="22f07-137">Zadejte typ, který lze deserializovat nebo serializovat, přepsáním `CanReadType` metod nebo. `CanWriteType`</span><span class="sxs-lookup"><span data-stu-id="22f07-137">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="22f07-138">Například může být možné pouze vytvořit text v vCard z `Contact` typu a naopak.</span><span class="sxs-lookup"><span data-stu-id="22f07-138">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="22f07-139">Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="22f07-139">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="22f07-140">Metoda CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="22f07-140">The CanWriteResult method</span></span>

<span data-ttu-id="22f07-141">V některých případech je nutné přepsat `CanWriteResult` `CanWriteType`místo.</span><span class="sxs-lookup"><span data-stu-id="22f07-141">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="22f07-142">Použijte `CanWriteResult` , pokud jsou splněné následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="22f07-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="22f07-143">Vaše metoda akce vrátí třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="22f07-143">Your action method returns a model class.</span></span>
* <span data-ttu-id="22f07-144">K dispozici jsou odvozené třídy, které mohou být vráceny za běhu.</span><span class="sxs-lookup"><span data-stu-id="22f07-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="22f07-145">Je potřeba znát za běhu, který tato akce vrátila odvozenou třídu.</span><span class="sxs-lookup"><span data-stu-id="22f07-145">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="22f07-146">Předpokládejme například, že váš podpis metody akce `Person` vrací typ, ale může `Student` vracet typ nebo `Instructor` , který je odvozen z `Person`.</span><span class="sxs-lookup"><span data-stu-id="22f07-146">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="22f07-147">Chcete-li, aby formátovací modul zpracovával `Student` pouze objekty, ověřte typ [objektu](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) v objektu Context, který je `CanWriteResult` k metodě k dispozici.</span><span class="sxs-lookup"><span data-stu-id="22f07-147">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="22f07-148">Všimněte si, že není nutné použít `CanWriteResult` při návratu `IActionResult`do metody Action; `CanWriteType` v takovém případě metoda přijímá typ modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="22f07-148">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="22f07-149">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="22f07-149">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="22f07-150">Provedete skutečnou práci deserializace nebo serializace v `ReadRequestBodyAsync` nebo. `WriteResponseBodyAsync`</span><span class="sxs-lookup"><span data-stu-id="22f07-150">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="22f07-151">Zvýrazněné řádky v následujícím příkladu ukazují, jak získat služby z kontejneru vkládání závislostí (nemůžete je získat z parametrů konstruktoru).</span><span class="sxs-lookup"><span data-stu-id="22f07-151">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="22f07-152">Příklad vstupního formátovacího modulu najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="22f07-152">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="22f07-153">Jak konfigurovat MVC pro použití vlastního formátovacího modulu</span><span class="sxs-lookup"><span data-stu-id="22f07-153">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="22f07-154">Chcete-li použít vlastní formátovací modul, přidejte instanci formátovací třídy do `InputFormatters` kolekce nebo. `OutputFormatters`</span><span class="sxs-lookup"><span data-stu-id="22f07-154">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="22f07-155">Formátovací moduly jsou vyhodnocovány v pořadí, ve kterém je vložíte.</span><span class="sxs-lookup"><span data-stu-id="22f07-155">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="22f07-156">První z nich má přednost.</span><span class="sxs-lookup"><span data-stu-id="22f07-156">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="22f07-157">Další kroky</span><span class="sxs-lookup"><span data-stu-id="22f07-157">Next steps</span></span>

* [<span data-ttu-id="22f07-158">Vzorový kód pro formátování prostého textu na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="22f07-158">Plain text formatter sample code on GitHub.</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Mvc.Formatters)
* <span data-ttu-id="22f07-159">[Ukázková aplikace pro tento dokument](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), která implementuje jednoduché formátovací a výstupní formátovací moduly vCard</span><span class="sxs-lookup"><span data-stu-id="22f07-159">[Sample app for this doc](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="22f07-160">Aplikace čte a zapisuje soubory vCard, které vypadají jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="22f07-160">The apps reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="22f07-161">Chcete-li zobrazit výstup v programu vCard, spusťte aplikaci a odešlete požadavek GET s hlavičkou Accept "text `http://localhost:63313/api/contacts/` /vCard" do (při spuštění ze `http://localhost:5000/api/contacts/` sady Visual Studio) nebo (při spuštění z příkazového řádku).</span><span class="sxs-lookup"><span data-stu-id="22f07-161">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="22f07-162">Chcete-li přidat soubor vCard do kolekce kontaktů v paměti, odešlete požadavek post na stejnou adresu URL s hlavičkou Content-Type "text/vCard" a textem vCard v těle, který je formátován jako příklad výše.</span><span class="sxs-lookup"><span data-stu-id="22f07-162">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>

---
title: Vlastní formátování v rozhraní Web API ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit a použít vlastní formátovací moduly pro webová rozhraní API v ASP.NET Core.
ms.author: tdykstra
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: ee6f166ced41c41506f2a17a7d362399c165b718
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020647"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="34bd7-103">Vlastní formátování v rozhraní Web API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34bd7-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="34bd7-104">podle [Petr Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="34bd7-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="34bd7-105">ASP.NET Core MVC obsahuje integrovanou podporu pro výměnu dat ve webovém rozhraní API s použitím formátu JSON, XML nebo prostý text.</span><span class="sxs-lookup"><span data-stu-id="34bd7-105">ASP.NET Core MVC has built-in support for data exchange in web APIs by using JSON, XML, or plain text formats.</span></span> <span data-ttu-id="34bd7-106">Tento článek ukazuje, jak přidat podporu pro další formáty tak, že vytvoříte vlastní formátovací moduly.</span><span class="sxs-lookup"><span data-stu-id="34bd7-106">This article shows how to add support for additional formats by creating custom formatters.</span></span>

<span data-ttu-id="34bd7-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34bd7-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="34bd7-108">Kdy použít vlastní formátovací moduly</span><span class="sxs-lookup"><span data-stu-id="34bd7-108">When to use custom formatters</span></span>

<span data-ttu-id="34bd7-109">Pokud chcete použít vlastní formátovací modul [vyjednávání obsahu](xref:web-api/advanced/formatting#content-negotiation) proces pro podporu typu obsahu, který není podporován předdefinované formátování (JSON, XML a prostý text).</span><span class="sxs-lookup"><span data-stu-id="34bd7-109">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters (JSON, XML, and plain text).</span></span>

<span data-ttu-id="34bd7-110">Například, pokud některé z klientů pro vaše webové rozhraní API může zpracovat [Protobuf](https://github.com/google/protobuf) formátu, můžete chtít použít Protobuf s těmito klienty, protože je mnohem efektivnější.</span><span class="sxs-lookup"><span data-stu-id="34bd7-110">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="34bd7-111">Nebo můžete chtít vaše webové rozhraní API k odeslání jména kontaktů a adresy v [vCard](https://wikipedia.org/wiki/VCard) formát, běžně používaný formát pro výměnu kontaktní údaje.</span><span class="sxs-lookup"><span data-stu-id="34bd7-111">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="34bd7-112">Ukázkovou aplikaci k dispozici v tomto článku implementuje jednoduchou vCard formátování.</span><span class="sxs-lookup"><span data-stu-id="34bd7-112">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="34bd7-113">Přehled o tom, jak používat vlastní formátování</span><span class="sxs-lookup"><span data-stu-id="34bd7-113">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="34bd7-114">Tady jsou kroky k vytvoření a použití vlastní formátovací modul:</span><span class="sxs-lookup"><span data-stu-id="34bd7-114">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="34bd7-115">Vytvořte třídu formátování výstupu, pokud chcete serializovat data k odeslání do klienta.</span><span class="sxs-lookup"><span data-stu-id="34bd7-115">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="34bd7-116">Vytvořte třídu vstupní formátovací modul, pokud chcete zrušit serializaci dat přijatých z klienta.</span><span class="sxs-lookup"><span data-stu-id="34bd7-116">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="34bd7-117">Doplnit dodatečné instance vaší formátovací moduly, `InputFormatters` a `OutputFormatters` kolekce v [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="34bd7-117">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="34bd7-118">Následující části obsahují pokyny a příklady kódu pro každý z těchto kroků.</span><span class="sxs-lookup"><span data-stu-id="34bd7-118">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="34bd7-119">Jak vytvořit třídu vlastního formátování</span><span class="sxs-lookup"><span data-stu-id="34bd7-119">How to create a custom formatter class</span></span>

<span data-ttu-id="34bd7-120">Chcete-li vytvořit formátování:</span><span class="sxs-lookup"><span data-stu-id="34bd7-120">To create a formatter:</span></span>

* <span data-ttu-id="34bd7-121">Třídy odvozen od příslušné základní třídy.</span><span class="sxs-lookup"><span data-stu-id="34bd7-121">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="34bd7-122">Zadejte platné médium typy a kódování v konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="34bd7-122">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="34bd7-123">Přepsat `CanReadType` / `CanWriteType` metody</span><span class="sxs-lookup"><span data-stu-id="34bd7-123">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="34bd7-124">Přepsat `ReadRequestBodyAsync` / `WriteResponseBodyAsync` metody</span><span class="sxs-lookup"><span data-stu-id="34bd7-124">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="34bd7-125">Odvozen od příslušné základní třídy</span><span class="sxs-lookup"><span data-stu-id="34bd7-125">Derive from the appropriate base class</span></span>

<span data-ttu-id="34bd7-126">Typ média textu (například soubor vCard), jsou odvozeny z [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) nebo [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) základní třídy.</span><span class="sxs-lookup"><span data-stu-id="34bd7-126">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="34bd7-127">Příklad vstupní formátovací modul, najdete v článku [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="34bd7-127">For an input formatter example, see the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="34bd7-128">Pro binární typy jsou odvozeny z [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) nebo [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) základní třídy.</span><span class="sxs-lookup"><span data-stu-id="34bd7-128">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="34bd7-129">Zadejte platné médium typy a kódování</span><span class="sxs-lookup"><span data-stu-id="34bd7-129">Specify valid media types and encodings</span></span>

<span data-ttu-id="34bd7-130">V konstruktoru, určete přidáním do platné médium typy a kódování `SupportedMediaTypes` a `SupportedEncodings` kolekce.</span><span class="sxs-lookup"><span data-stu-id="34bd7-130">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="34bd7-131">Příklad vstupní formátovací modul, najdete v článku [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="34bd7-131">For an input formatter example, see the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="34bd7-132">Injektáž závislostí konstruktoru ve třídě formátovacího modulu nelze provést.</span><span class="sxs-lookup"><span data-stu-id="34bd7-132">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="34bd7-133">Například nelze získat protokolovač tak, že přidáte parametr protokolovač konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="34bd7-133">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="34bd7-134">Pro přístup ke službám, budete muset použít objekt kontextu, který získá předán do metody.</span><span class="sxs-lookup"><span data-stu-id="34bd7-134">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="34bd7-135">Příklad kódu [níže](#read-write) ukazuje, jak to provést.</span><span class="sxs-lookup"><span data-stu-id="34bd7-135">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="34bd7-136">Přepsat CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="34bd7-136">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="34bd7-137">Určení typu lze deserializovat do nebo z serializovat tak, že přepíšete `CanReadType` nebo `CanWriteType` metody.</span><span class="sxs-lookup"><span data-stu-id="34bd7-137">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="34bd7-138">Například může být pouze budete moci vytvořit vCard text z `Contact` typu a naopak.</span><span class="sxs-lookup"><span data-stu-id="34bd7-138">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="34bd7-139">Příklad vstupní formátovací modul, najdete v článku [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="34bd7-139">For an input formatter example, see the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="34bd7-140">CanWriteResult – metoda</span><span class="sxs-lookup"><span data-stu-id="34bd7-140">The CanWriteResult method</span></span>

<span data-ttu-id="34bd7-141">V některých scénářích je nutné přepsat `CanWriteResult` místo `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="34bd7-141">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="34bd7-142">Použití `CanWriteResult` Pokud jsou splněny následující podmínky:</span><span class="sxs-lookup"><span data-stu-id="34bd7-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="34bd7-143">Vaše metoda akce vrací třídu modelu.</span><span class="sxs-lookup"><span data-stu-id="34bd7-143">Your action method returns a model class.</span></span>
* <span data-ttu-id="34bd7-144">Existují odvozených tříd, které může být vrácena za běhu.</span><span class="sxs-lookup"><span data-stu-id="34bd7-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="34bd7-145">Musíte znát za běhu, který odvozené třídy vrátil akce.</span><span class="sxs-lookup"><span data-stu-id="34bd7-145">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="34bd7-146">Předpokládejme například, vrátí podpis metody akce `Person` typu, ale může vrátit `Student` nebo `Instructor` typ, který je odvozen od `Person`.</span><span class="sxs-lookup"><span data-stu-id="34bd7-146">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="34bd7-147">Pokud chcete, aby vaše formátovací modul pro zpracování pouze `Student` objekty, zkontrolujte typ [objekt](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) v objektu kontextu k dispozici na `CanWriteResult` metody.</span><span class="sxs-lookup"><span data-stu-id="34bd7-147">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="34bd7-148">Všimněte si, že není nutné používat `CanWriteResult` při vrácení metody akce `IActionResult`; v takovém případě `CanWriteType` metoda přijímá typ modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="34bd7-148">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>
### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="34bd7-149">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="34bd7-149">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="34bd7-150">Vykonávají samotnou práci rušení serializace nebo serializace v `ReadRequestBodyAsync` nebo `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="34bd7-150">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="34bd7-151">Zvýrazněné řádky v následujícím příkladu ukazují, jak získat služby z kontejneru pro vkládání závislosti (je již nelze získat z parametrů konstruktoru).</span><span class="sxs-lookup"><span data-stu-id="34bd7-151">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="34bd7-152">Příklad vstupní formátovací modul, najdete v článku [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="34bd7-152">For an input formatter example, see the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="34bd7-153">Jak nakonfigurovat MVC pomocí vlastního formátovacího modulu</span><span class="sxs-lookup"><span data-stu-id="34bd7-153">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="34bd7-154">Pokud chcete použít vlastní formátovací modul, přidat instanci formátovacího modulu třídy, která se `InputFormatters` nebo `OutputFormatters` kolekce.</span><span class="sxs-lookup"><span data-stu-id="34bd7-154">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="34bd7-155">Formátovací moduly jsou vyhodnocovány v pořadí, v jakém že je vkládat v případě potřeby.</span><span class="sxs-lookup"><span data-stu-id="34bd7-155">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="34bd7-156">První z nich má přednost.</span><span class="sxs-lookup"><span data-stu-id="34bd7-156">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="34bd7-157">Další kroky</span><span class="sxs-lookup"><span data-stu-id="34bd7-157">Next steps</span></span>

<span data-ttu-id="34bd7-158">Najdete v článku [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), který implementuje jednoduchou vCard vstup a formátování výstupu.</span><span class="sxs-lookup"><span data-stu-id="34bd7-158">See the [sample application](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="34bd7-159">Aplikace čte a zapisuje vCard, která vypadají, jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="34bd7-159">The application reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="34bd7-160">Chcete-li zobrazit vCard výstup, spusťte aplikaci a odeslat požadavek Get s přijmout záhlaví "text/vcard" k `http://localhost:63313/api/contacts/` (při spuštění ze sady Visual Studio) nebo `http://localhost:5000/api/contacts/` (při spuštění z příkazového řádku).</span><span class="sxs-lookup"><span data-stu-id="34bd7-160">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="34bd7-161">Přidat do kolekce v paměti kontaktů vizitku, odešlete požadavek Post na stejnou adresu URL, s hlavičkou Content-Type "text/vcard" a textem vCard v textu ve formátu jako v příkladu výše.</span><span class="sxs-lookup"><span data-stu-id="34bd7-161">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>

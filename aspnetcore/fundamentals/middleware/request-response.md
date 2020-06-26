---
title: Operace žádosti a odpovědi v ASP.NET Core
author: jkotalik
description: Přečtěte si, jak číst text žádosti a napsat text odpovědi v ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b6fc7a115cb0f4696d10bf036eadb59028dfb605
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404129"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="9c338-103">Operace žádosti a odpovědi v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c338-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="9c338-104">Od [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="9c338-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="9c338-105">Tento článek vysvětluje, jak číst z textu žádosti a zapisovat do těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="9c338-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="9c338-106">Při zápisu middlewaru může být vyžadován kód pro tyto operace.</span><span class="sxs-lookup"><span data-stu-id="9c338-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="9c338-107">Mimo psací middleware není vlastní kód všeobecně nutný, protože operace jsou zpracovávány pomocí MVC a Razor stránek.</span><span class="sxs-lookup"><span data-stu-id="9c338-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="9c338-108">Pro tělo žádosti a odpovědi jsou k dispozici dvě abstrakce: <xref:System.IO.Stream> a <xref:System.IO.Pipelines.Pipe> .</span><span class="sxs-lookup"><span data-stu-id="9c338-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="9c338-109">Pro čtení požadavku <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> je <xref:System.IO.Stream> a `HttpRequest.BodyReader` <xref:System.IO.Pipelines.PipeReader> .</span><span class="sxs-lookup"><span data-stu-id="9c338-109">For request reading, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="9c338-110">Pro zápis odpovědí <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> je <xref:System.IO.Stream> a `HttpResponse.BodyWriter` <xref:System.IO.Pipelines.PipeWriter> .</span><span class="sxs-lookup"><span data-stu-id="9c338-110">For response writing, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="9c338-111">[Kanály](/dotnet/standard/io/pipelines) se doporučují přes streamy.</span><span class="sxs-lookup"><span data-stu-id="9c338-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="9c338-112">Datové proudy můžou být pro některé jednoduché operace jednodušší, ale kanály mají výkonnou výhodu a je možné je ve většině scénářů snadněji používat.</span><span class="sxs-lookup"><span data-stu-id="9c338-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="9c338-113">ASP.NET Core začne používat kanály místo interního streamování.</span><span class="sxs-lookup"><span data-stu-id="9c338-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="9c338-114">Mezi příklady patří:</span><span class="sxs-lookup"><span data-stu-id="9c338-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="9c338-115">Datové proudy nejsou odebírány z rozhraní.</span><span class="sxs-lookup"><span data-stu-id="9c338-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="9c338-116">Streamy se v rámci .NET i nadále používají a mnoho typů datových proudů nemá ekvivalenty kanálů, jako jsou `FileStreams` a `ResponseCompression` .</span><span class="sxs-lookup"><span data-stu-id="9c338-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="9c338-117">Příklady streamu</span><span class="sxs-lookup"><span data-stu-id="9c338-117">Stream examples</span></span>

<span data-ttu-id="9c338-118">Předpokládejme, že cílem je vytvořit middleware, který přečte celý text žádosti jako seznam řetězců a rozdělí na nové řádky.</span><span class="sxs-lookup"><span data-stu-id="9c338-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="9c338-119">Implementace jednoduchého datového proudu může vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="9c338-119">A simple stream implementation might look like the following example:</span></span>

> [!WARNING]
> <span data-ttu-id="9c338-120">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="9c338-120">The following code:</span></span>
> * <span data-ttu-id="9c338-121">Slouží k předvedení potíží s nepoužitím kanálu ke čtení textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="9c338-121">Is used to demonstrate the problems with not using a pipe to read the request body.</span></span>
> * <span data-ttu-id="9c338-122">Není určeno pro použití v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="9c338-122">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="9c338-123">Tento kód funguje, ale došlo k určitým problémům:</span><span class="sxs-lookup"><span data-stu-id="9c338-123">This code works, but there are some issues:</span></span>

* <span data-ttu-id="9c338-124">Před připojením k `StringBuilder` , je v příkladu vytvořen jiný řetězec ( `encodedString` ), který je vyvolán okamžitě.</span><span class="sxs-lookup"><span data-stu-id="9c338-124">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="9c338-125">K tomuto procesu dochází pro všechny bajty v datovém proudu, takže výsledkem je dodatečné přidělení paměti velikosti celého textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="9c338-125">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="9c338-126">Tento příklad přečte celý řetězec před rozdělením na nové řádky.</span><span class="sxs-lookup"><span data-stu-id="9c338-126">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="9c338-127">Je efektivnější kontrolovat nové řádky v bajtovém poli.</span><span class="sxs-lookup"><span data-stu-id="9c338-127">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="9c338-128">Tady je příklad, který opravuje některé předchozí problémy:</span><span class="sxs-lookup"><span data-stu-id="9c338-128">Here's an example that fixes some of the preceding issues:</span></span>

> [!WARNING]
> <span data-ttu-id="9c338-129">Následující kód:</span><span class="sxs-lookup"><span data-stu-id="9c338-129">The following code:</span></span>
> * <span data-ttu-id="9c338-130">Slouží k předvedení řešení některých problémů v předchozím kódu, zatímco neřeší všechny problémy.</span><span class="sxs-lookup"><span data-stu-id="9c338-130">Is used to demonstrate the solutions to some problems in the preceding code while not solving all the problems.</span></span>
> * <span data-ttu-id="9c338-131">Není určeno pro použití v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="9c338-131">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="9c338-132">Tento předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="9c338-132">This preceding example:</span></span>

* <span data-ttu-id="9c338-133">Neukládá do vyrovnávací paměti celý text žádosti, `StringBuilder` Pokud neexistují žádné znaky nového řádku.</span><span class="sxs-lookup"><span data-stu-id="9c338-133">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="9c338-134">Nevolá `Split` řetězec.</span><span class="sxs-lookup"><span data-stu-id="9c338-134">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="9c338-135">Stále ale dochází k několika problémům:</span><span class="sxs-lookup"><span data-stu-id="9c338-135">However, there are still are a few issues:</span></span>

* <span data-ttu-id="9c338-136">Pokud jsou znaky nového řádku zhuštěné, je většina textu žádosti v řetězci ukládána do vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="9c338-136">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="9c338-137">Kód pokračuje v vytváření řetězců ( `remainingString` ) a přidává je do vyrovnávací paměti řetězců, což vede k dodatečnému přidělení.</span><span class="sxs-lookup"><span data-stu-id="9c338-137">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="9c338-138">Tyto problémy jsou fixable, ale kód se postupně častěji komplikovane s malým vylepšením.</span><span class="sxs-lookup"><span data-stu-id="9c338-138">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="9c338-139">Kanály poskytují způsob, jak tyto problémy vyřešit s minimální složitou složitostí kódu.</span><span class="sxs-lookup"><span data-stu-id="9c338-139">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="9c338-140">Pipelines</span><span class="sxs-lookup"><span data-stu-id="9c338-140">Pipelines</span></span>

<span data-ttu-id="9c338-141">Následující příklad ukazuje, jak lze stejný scénář zpracovat pomocí [PipeReader](/dotnet/standard/io/pipelines#pipe):</span><span class="sxs-lookup"><span data-stu-id="9c338-141">The following example shows how the same scenario can be handled using a [PipeReader](/dotnet/standard/io/pipelines#pipe):</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="9c338-142">Tento příklad opravuje mnoho problémů, které měly implementace datových proudů:</span><span class="sxs-lookup"><span data-stu-id="9c338-142">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="9c338-143">Není nutné ukládat vyrovnávací paměť řetězců `PipeReader` , protože zpracovává bajty, které nebyly použity.</span><span class="sxs-lookup"><span data-stu-id="9c338-143">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="9c338-144">Zakódované řetězce jsou přímo přidány do seznamu vrácených řetězců.</span><span class="sxs-lookup"><span data-stu-id="9c338-144">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="9c338-145">Jiné než `ToArray` volání a paměť, která je používána řetězcem, je vytvoření řetězce bez přidělení.</span><span class="sxs-lookup"><span data-stu-id="9c338-145">Other than the `ToArray` call, and the memory used by the string, string creation is allocation free.</span></span>

## <a name="adapters"></a><span data-ttu-id="9c338-146">Adaptéry</span><span class="sxs-lookup"><span data-stu-id="9c338-146">Adapters</span></span>

<span data-ttu-id="9c338-147">`Body`Vlastnosti, `BodyReader` a `BodyWriter` jsou k dispozici pro `HttpRequest` a `HttpResponse` .</span><span class="sxs-lookup"><span data-stu-id="9c338-147">The `Body`, `BodyReader`, and `BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="9c338-148">Když nastavíte `Body` jiný datový proud, nová sada adaptérů automaticky přizpůsobí každý typ druhé.</span><span class="sxs-lookup"><span data-stu-id="9c338-148">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="9c338-149">Pokud nastavíte `HttpRequest.Body` nový datový proud, `HttpRequest.BodyReader` je automaticky nastaven na nový `PipeReader` , který zabalí `HttpRequest.Body` .</span><span class="sxs-lookup"><span data-stu-id="9c338-149">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="9c338-150">StartAsync</span><span class="sxs-lookup"><span data-stu-id="9c338-150">StartAsync</span></span>

<span data-ttu-id="9c338-151">`HttpResponse.StartAsync`slouží k označení, že záhlaví jsou neupravitelná a ke spouštění `OnStarting` zpětných volání.</span><span class="sxs-lookup"><span data-stu-id="9c338-151">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="9c338-152">Při použití Kestrel jako serveru zavolá `StartAsync` před použitím `PipeReader` záruky, že paměť vrácená funkcí `GetMemory` patří do interní paměti Kestrel, <xref:System.IO.Pipelines.Pipe> nikoli z externí vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="9c338-152">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9c338-153">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9c338-153">Additional resources</span></span>

* [<span data-ttu-id="9c338-154">System. IO. Pipelines v .NET</span><span class="sxs-lookup"><span data-stu-id="9c338-154">System.IO.Pipelines in .NET</span></span>](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->

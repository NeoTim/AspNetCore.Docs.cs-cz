---
title: Operace žádosti a odpovědi v ASP.NET Core
author: jkotalik
description: Přečtěte si, jak číst text žádosti a napsat text odpovědi v ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: e992401da2d194b178afbe51a293d103def0f940
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238150"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="af054-103">Operace žádosti a odpovědi v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af054-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="af054-104">Od [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="af054-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="af054-105">Tento článek vysvětluje, jak číst z textu žádosti a zapisovat do těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="af054-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="af054-106">Při zápisu middlewaru může být vyžadován kód pro tyto operace.</span><span class="sxs-lookup"><span data-stu-id="af054-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="af054-107">Mimo psací middleware není vlastní kód všeobecně nutný, protože operace jsou zpracovávány pomocí MVC a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="af054-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="af054-108">Pro tělo žádosti a odpovědi jsou k dispozici dvě abstrakce <xref:System.IO.Stream> : <xref:System.IO.Pipelines.Pipe>a.</span><span class="sxs-lookup"><span data-stu-id="af054-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="af054-109">Pro čtení požadavku [HttpRequest. body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) je <xref:System.IO.Stream>a `HttpRequest.BodyReader` <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="af054-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="af054-110">Pro zápis odpovědí je [HttpResponse. body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) a `HttpResponse.BodyWriter` <xref:System.IO.Pipelines.PipeWriter>.</span><span class="sxs-lookup"><span data-stu-id="af054-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="af054-111">Kanály se doporučují přes streamy.</span><span class="sxs-lookup"><span data-stu-id="af054-111">Pipelines are recommended over streams.</span></span> <span data-ttu-id="af054-112">Datové proudy můžou být pro některé jednoduché operace jednodušší, ale kanály mají výkonnou výhodu a je možné je ve většině scénářů snadněji používat.</span><span class="sxs-lookup"><span data-stu-id="af054-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="af054-113">ASP.NET Core začne používat kanály místo interního streamování.</span><span class="sxs-lookup"><span data-stu-id="af054-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="af054-114">Příklady:</span><span class="sxs-lookup"><span data-stu-id="af054-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="af054-115">Datové proudy nejsou odebírány z rozhraní.</span><span class="sxs-lookup"><span data-stu-id="af054-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="af054-116">Streamy se v rámci .NET i nadále používají a mnoho typů datových proudů nemá ekvivalenty kanálů, `FileStreams` jako `ResponseCompression`jsou a.</span><span class="sxs-lookup"><span data-stu-id="af054-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="af054-117">Příklady streamu</span><span class="sxs-lookup"><span data-stu-id="af054-117">Stream examples</span></span>

<span data-ttu-id="af054-118">Předpokládejme, že cílem je vytvořit middleware, který přečte celý text žádosti jako seznam řetězců a rozdělí na nové řádky.</span><span class="sxs-lookup"><span data-stu-id="af054-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="af054-119">Implementace jednoduchého datového proudu může vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="af054-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="af054-120">Tento kód funguje, ale došlo k určitým problémům:</span><span class="sxs-lookup"><span data-stu-id="af054-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="af054-121">Před připojením k `StringBuilder`, je v příkladu vytvořen jiný řetězec (`encodedString`), který je vyvolán okamžitě.</span><span class="sxs-lookup"><span data-stu-id="af054-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="af054-122">K tomuto procesu dochází pro všechny bajty v datovém proudu, takže výsledkem je dodatečné přidělení paměti velikosti celého textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="af054-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="af054-123">Tento příklad přečte celý řetězec před rozdělením na nové řádky.</span><span class="sxs-lookup"><span data-stu-id="af054-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="af054-124">Je efektivnější kontrolovat nové řádky v bajtovém poli.</span><span class="sxs-lookup"><span data-stu-id="af054-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="af054-125">Tady je příklad, který opravuje některé předchozí problémy:</span><span class="sxs-lookup"><span data-stu-id="af054-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="af054-126">Tento předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="af054-126">This preceding example:</span></span>

* <span data-ttu-id="af054-127">Neukládá do vyrovnávací paměti celý text `StringBuilder` žádosti, pokud neexistují žádné znaky nového řádku.</span><span class="sxs-lookup"><span data-stu-id="af054-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="af054-128">Nevolá `Split` řetězec.</span><span class="sxs-lookup"><span data-stu-id="af054-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="af054-129">Stále ale dochází k několika problémům:</span><span class="sxs-lookup"><span data-stu-id="af054-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="af054-130">Pokud jsou znaky nového řádku zhuštěné, je většina textu žádosti v řetězci ukládána do vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="af054-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="af054-131">Kód pokračuje v vytváření řetězců (`remainingString`) a přidává je do vyrovnávací paměti řetězců, což vede k dodatečnému přidělení.</span><span class="sxs-lookup"><span data-stu-id="af054-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="af054-132">Tyto problémy jsou fixable, ale kód se postupně častěji komplikovane s malým vylepšením.</span><span class="sxs-lookup"><span data-stu-id="af054-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="af054-133">Kanály poskytují způsob, jak tyto problémy vyřešit s minimální složitou složitostí kódu.</span><span class="sxs-lookup"><span data-stu-id="af054-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="af054-134">Kanály</span><span class="sxs-lookup"><span data-stu-id="af054-134">Pipelines</span></span>

<span data-ttu-id="af054-135">Následující příklad ukazuje, jak lze stejný scénář zpracovat pomocí `PipeReader`:</span><span class="sxs-lookup"><span data-stu-id="af054-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="af054-136">Tento příklad opravuje mnoho problémů, které měly implementace datových proudů:</span><span class="sxs-lookup"><span data-stu-id="af054-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="af054-137">Není nutné ukládat vyrovnávací paměť řetězců, protože `PipeReader` zpracovává bajty, které nebyly použity.</span><span class="sxs-lookup"><span data-stu-id="af054-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="af054-138">Zakódované řetězce jsou přímo přidány do seznamu vrácených řetězců.</span><span class="sxs-lookup"><span data-stu-id="af054-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="af054-139">Vytváření řetězců je bez přidělení, kromě paměti využívané řetězcem (s výjimkou `ToArray()` volání).</span><span class="sxs-lookup"><span data-stu-id="af054-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="af054-140">Adaptéry</span><span class="sxs-lookup"><span data-stu-id="af054-140">Adapters</span></span>

<span data-ttu-id="af054-141">Pro `Body` ajsou`HttpResponse`k dispozici obě vlastnosti i. `BodyReader/BodyWriter` `HttpRequest`</span><span class="sxs-lookup"><span data-stu-id="af054-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="af054-142">Když nastavíte `Body` jiný datový proud, nová sada adaptérů automaticky přizpůsobí každý typ druhé.</span><span class="sxs-lookup"><span data-stu-id="af054-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="af054-143">Pokud nastavíte `HttpRequest.Body` nový datový proud, `HttpRequest.BodyReader` je automaticky nastaven na nový `PipeReader` , který zabalí `HttpRequest.Body`.</span><span class="sxs-lookup"><span data-stu-id="af054-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="af054-144">StartAsync</span><span class="sxs-lookup"><span data-stu-id="af054-144">StartAsync</span></span>

<span data-ttu-id="af054-145">`HttpResponse.StartAsync`slouží k označení, že záhlaví jsou neupravitelná a ke spouštění `OnStarting` zpětných volání.</span><span class="sxs-lookup"><span data-stu-id="af054-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="af054-146">Při použití Kestrel `StartAsync` jako serveru zavolá před `PipeReader` použitím záruky, že paměť vrácená funkcí `GetMemory` patří do interní <xref:System.IO.Pipelines.Pipe> paměti Kestrel, nikoli z externí vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="af054-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af054-147">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="af054-147">Additional resources</span></span>

* [<span data-ttu-id="af054-148">Představujeme System. IO. Pipelines</span><span class="sxs-lookup"><span data-stu-id="af054-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>

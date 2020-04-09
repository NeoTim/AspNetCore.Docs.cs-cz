---
title: Operace požadavků a odpovědí v ASP.NET Core
author: jkotalik
description: Naučte se číst tělo požadavku a napsat tělo odpovědi v ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b473fa02e1d23f02bc5d2e15fa54ab7b1dbbb17c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667214"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="12f3e-103">Operace požadavků a odpovědí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="12f3e-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="12f3e-104">Podle [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="12f3e-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="12f3e-105">Tento článek vysvětluje, jak číst z těla požadavku a zapisovat do těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="12f3e-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="12f3e-106">Kód pro tyto operace může být vyžadovánpři psaní middleware.</span><span class="sxs-lookup"><span data-stu-id="12f3e-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="12f3e-107">Mimo psaní middleware, vlastní kód není obecně vyžadováno, protože operace jsou zpracovány MVC a Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="12f3e-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="12f3e-108">Existují dvě abstrakce pro tělo žádosti <xref:System.IO.Stream> <xref:System.IO.Pipelines.Pipe>a odpovědi: a .</span><span class="sxs-lookup"><span data-stu-id="12f3e-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="12f3e-109">Pro čtení požadavku [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) <xref:System.IO.Stream>je `HttpRequest.BodyReader` a <xref:System.IO.Pipelines.PipeReader>, a je .</span><span class="sxs-lookup"><span data-stu-id="12f3e-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="12f3e-110">Pro psaní odpovědi [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) <xref:System.IO.Stream>je `HttpResponse.BodyWriter` a <xref:System.IO.Pipelines.PipeWriter>je .</span><span class="sxs-lookup"><span data-stu-id="12f3e-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="12f3e-111">[Kanály](/dotnet/standard/io/pipelines) se doporučují přes datové proudy.</span><span class="sxs-lookup"><span data-stu-id="12f3e-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="12f3e-112">Datové proudy lze snadněji použít pro některé jednoduché operace, ale kanály mají výhodu výkonu a jsou snadněji použitelné ve většině scénářů.</span><span class="sxs-lookup"><span data-stu-id="12f3e-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="12f3e-113">ASP.NET Core začíná používat kanály namísto datových proudů interně.</span><span class="sxs-lookup"><span data-stu-id="12f3e-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="12f3e-114">Příklady obsahují:</span><span class="sxs-lookup"><span data-stu-id="12f3e-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="12f3e-115">Datové proudy nejsou odebírány z rámce.</span><span class="sxs-lookup"><span data-stu-id="12f3e-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="12f3e-116">Datové proudy se nadále používají v celé síti .NET a mnoho `FileStreams` `ResponseCompression`typů datových proudů nemá ekvivalenty kanálu, například a .</span><span class="sxs-lookup"><span data-stu-id="12f3e-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="12f3e-117">Příklady streamování</span><span class="sxs-lookup"><span data-stu-id="12f3e-117">Stream examples</span></span>

<span data-ttu-id="12f3e-118">Předpokládejme, že cílem je vytvořit middleware, který přečte celé tělo požadavku jako seznam řetězců, rozdělení na nové řádky.</span><span class="sxs-lookup"><span data-stu-id="12f3e-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="12f3e-119">Jednoduchá implementace datového proudu může vypadat jako následující příklad:</span><span class="sxs-lookup"><span data-stu-id="12f3e-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="12f3e-120">Tento kód funguje, ale existují některé problémy:</span><span class="sxs-lookup"><span data-stu-id="12f3e-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="12f3e-121">Před připojením `StringBuilder`k , příklad vytvoří`encodedString`jiný řetězec ( ), který je zahozen okamžitě.</span><span class="sxs-lookup"><span data-stu-id="12f3e-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="12f3e-122">Tento proces probíhá pro všechny bajty v datovém proudu, takže výsledkem je přidělení další paměti velikost celého těla požadavku.</span><span class="sxs-lookup"><span data-stu-id="12f3e-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="12f3e-123">Příklad přečte celý řetězec před rozdělením na nové řádky.</span><span class="sxs-lookup"><span data-stu-id="12f3e-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="12f3e-124">Je efektivnější zkontrolovat nové řádky v poli bajtů.</span><span class="sxs-lookup"><span data-stu-id="12f3e-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="12f3e-125">Tady je příklad, který opravuje některé z předchozích problémů:</span><span class="sxs-lookup"><span data-stu-id="12f3e-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="12f3e-126">Tento předchozí příklad:</span><span class="sxs-lookup"><span data-stu-id="12f3e-126">This preceding example:</span></span>

* <span data-ttu-id="12f3e-127">Není vyrovnávací paměti celé tělo `StringBuilder` požadavku v, pokud neexistují žádné znaky nového řádku.</span><span class="sxs-lookup"><span data-stu-id="12f3e-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="12f3e-128">Nevolá `Split` na provázku.</span><span class="sxs-lookup"><span data-stu-id="12f3e-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="12f3e-129">Stále však existuje několik problémů:</span><span class="sxs-lookup"><span data-stu-id="12f3e-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="12f3e-130">Pokud jsou znaky nového řádku řídké, velká část těla požadavku je v řetězci uložena do vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="12f3e-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="12f3e-131">Kód pokračuje ve vytváření`remainingString`řetězců ( ) a přidá je do vyrovnávací paměti řetězce, což má za následek další přidělení.</span><span class="sxs-lookup"><span data-stu-id="12f3e-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="12f3e-132">Tyto problémy jsou opravitelné, ale kód se stává stále složitější s malým zlepšením.</span><span class="sxs-lookup"><span data-stu-id="12f3e-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="12f3e-133">Kanály poskytují způsob, jak vyřešit tyto problémy s minimální složitostí kódu.</span><span class="sxs-lookup"><span data-stu-id="12f3e-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="12f3e-134">Kanály</span><span class="sxs-lookup"><span data-stu-id="12f3e-134">Pipelines</span></span>

<span data-ttu-id="12f3e-135">Následující příklad ukazuje, jak lze stejný `PipeReader`scénář zpracovat pomocí :</span><span class="sxs-lookup"><span data-stu-id="12f3e-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="12f3e-136">Tento příklad opravuje mnoho problémů, které implementace datových proudů měl:</span><span class="sxs-lookup"><span data-stu-id="12f3e-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="12f3e-137">Není potřeba vyrovnávací paměti řetězce, `PipeReader` protože popisovače bajtů, které nebyly použity.</span><span class="sxs-lookup"><span data-stu-id="12f3e-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="12f3e-138">Kódované řetězce jsou přímo přidány do seznamu vrácených řetězců.</span><span class="sxs-lookup"><span data-stu-id="12f3e-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="12f3e-139">Vytvoření řetězce je bez přidělení kromě paměti používané `ToArray()` řetězcem (s výjimkou volání).</span><span class="sxs-lookup"><span data-stu-id="12f3e-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="12f3e-140">Adaptéry</span><span class="sxs-lookup"><span data-stu-id="12f3e-140">Adapters</span></span>

<span data-ttu-id="12f3e-141">K `Body` `BodyReader/BodyWriter` dispozici jsou `HttpRequest` i `HttpResponse`vlastnosti pro a .</span><span class="sxs-lookup"><span data-stu-id="12f3e-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="12f3e-142">Pokud nastavíte `Body` jiný datový proud, nová sada adaptérů automaticky přizpůsobí každý typ druhému.</span><span class="sxs-lookup"><span data-stu-id="12f3e-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="12f3e-143">Pokud `HttpRequest.Body` nastavíte nový `HttpRequest.BodyReader` datový proud, je `PipeReader` automaticky `HttpRequest.Body`nastavena na nový, který obtéká .</span><span class="sxs-lookup"><span data-stu-id="12f3e-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="12f3e-144">Spuštění synchronizace</span><span class="sxs-lookup"><span data-stu-id="12f3e-144">StartAsync</span></span>

<span data-ttu-id="12f3e-145">`HttpResponse.StartAsync`se používá k označení, že záhlaví jsou `OnStarting` nemodifikovatelné a ke spuštění zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="12f3e-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="12f3e-146">Při použití Kestrel jako `StartAsync` server, `PipeReader` volání před použitím `GetMemory` záruky, že paměť vrácena patří kestrel vnitřní <xref:System.IO.Pipelines.Pipe> spíše než externí vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="12f3e-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12f3e-147">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="12f3e-147">Additional resources</span></span>

* [<span data-ttu-id="12f3e-148">Představujeme System.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="12f3e-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>

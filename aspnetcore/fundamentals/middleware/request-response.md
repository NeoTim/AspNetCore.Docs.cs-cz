---
title: Operace požadavku a odpovědi v ASP.NET Core
author: jkotalik
description: Zjistěte, jak datovou část požadavku čtení a zápis datové části odpovědi v ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jkotalik
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b6e3cd4b79e0c062b271c65cd5ecbdb4ef80c3a1
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208054"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="d2d8d-103">Operace požadavku a odpovědi v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2d8d-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="d2d8d-104">Podle [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="d2d8d-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="d2d8d-105">Tento článek vysvětluje, jak číst z textu požadavku a zapisovat do datové části odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="d2d8d-106">Můžete napsat kód pro tyto operace při psaní middlewaru.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-106">You might need to write code for these operations when you're writing middleware.</span></span> <span data-ttu-id="d2d8d-107">V opačném případě obvykle není nutné tento kód napsat, protože operace jsou zpracovávány MVC a stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-107">Otherwise, you typically don't have to write this code because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="d2d8d-108">V ASP.NET Core 3.0 jsou dvě abstrakce pro těla požadavku a odpovědi: <xref:System.IO.Stream> a <xref:System.IO.Pipelines.Pipe>.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-108">In ASP.NET Core 3.0, there are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="d2d8d-109">Pro žádosti o čtení [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) je <xref:System.IO.Stream>, a `HttpRequest.BodyPipe` je <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyPipe` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="d2d8d-110">Pro psaní odpovědi [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) je `HttpResponse.BodyPipe` je <xref:System.IO.Pipelines.PipeWriter>.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a `HttpResponse.BodyPipe` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="d2d8d-111">Doporučujeme, abyste kanálů prostřednictvím datových proudů.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-111">We recommend pipelines over streams.</span></span> <span data-ttu-id="d2d8d-112">Datové proudy může být jednodušší použít pro některé jednoduché operace, ale kanály mají využívat výkon a usnadňuje používání ve většině scénářů.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="d2d8d-113">3.0 ASP.NET Core spouští kanály nahrazujícím datové proudy interně.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-113">In 3.0, ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="d2d8d-114">Příklady:</span><span class="sxs-lookup"><span data-stu-id="d2d8d-114">Examples include:</span></span>

- `FormReader`
- `TextReader`
- `TexWriter`
- `HttpResponse.WriteAsync`

<span data-ttu-id="d2d8d-115">Datové proudy nejsou opouštěl.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-115">Streams aren't going away.</span></span> <span data-ttu-id="d2d8d-116">Nadále používat v rámci .NET a mnoho typů datového proudu nemají ekvivalenty kanálu, jako je třeba `FileStreams` a `ResponseCompression`.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-116">They continue to be used throughout .NET, and many stream types don't have pipe equivalents, like `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="d2d8d-117">Příklady Stream</span><span class="sxs-lookup"><span data-stu-id="d2d8d-117">Stream examples</span></span>

<span data-ttu-id="d2d8d-118">Předpokládejme, že chcete vytvořit middleware, který čte celého těla požadavku jako seznam řetězců, rozdělení na nových řádcích.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-118">Suppose you want to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="d2d8d-119">Implementace jednoduchého datový proud může vypadat jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d2d8d-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="d2d8d-120">Tento kód funguje, ale dojde k problémům:</span><span class="sxs-lookup"><span data-stu-id="d2d8d-120">This code works, but there are some issues:</span></span>

- <span data-ttu-id="d2d8d-121">Před připojením k `StringBuilder`, v příkladu se vytvoří další řetězec (`encodedString`), která je okamžitě zahozeny.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="d2d8d-122">Tento proces se provádí pro všechny bajty v datovém proudu, takže výsledkem je paměť navíc přidělení velikosti celého těla požadavku.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
- <span data-ttu-id="d2d8d-123">Tento příklad načte celý řetězec před rozdělením na nových řádcích.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="d2d8d-124">Bylo by mnohem efektivnější ke kontrole pro nové řádky v bajtové pole.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-124">It would be more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="d2d8d-125">Tady je příklad, který řeší některé z těchto problémů:</span><span class="sxs-lookup"><span data-stu-id="d2d8d-125">Here's an example that fixes some of these issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="d2d8d-126">V tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="d2d8d-126">This example:</span></span>

- <span data-ttu-id="d2d8d-127">Není ve vyrovnávací paměti textu celého žádosti v `StringBuilder` Pokud nejsou k dispozici žádné znaky nového řádku.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
- <span data-ttu-id="d2d8d-128">Nevolá `Split` na řetězec.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="d2d8d-129">Existují však stále se několik problémů:</span><span class="sxs-lookup"><span data-stu-id="d2d8d-129">However, there are still are a few issues:</span></span>

- <span data-ttu-id="d2d8d-130">Pokud jsou znaky nového řádku zhuštěný, velkou část textu požadavku do vyrovnávací paměti v řetězci.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-130">If newline characters are sparse, much of the request body is buffered in the string .</span></span>
- <span data-ttu-id="d2d8d-131">Přesto vytvoří řetězce (`remainingString`) a přidá je do vyrovnávací paměti řetězce, což vede k další přidělení.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-131">It still creates strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="d2d8d-132">Je možné opravit tyto problémy, ale kód je stává čím dál tím víc složité díky vylepšení malý.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-132">These issues are fixable, but the code is becoming more and more complicated with little improvement.</span></span> <span data-ttu-id="d2d8d-133">Kanály poskytují způsob, jak vyřešit tyto problémy s minimem kódování složitost.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="d2d8d-134">Kanály</span><span class="sxs-lookup"><span data-stu-id="d2d8d-134">Pipelines</span></span>

<span data-ttu-id="d2d8d-135">Následující příklad ukazuje, jak můžete stejný scénář se určují pomocí šablon `PipeReader`:</span><span class="sxs-lookup"><span data-stu-id="d2d8d-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="d2d8d-136">V tomto příkladu řeší řadu problémů, které měly datové proudy implementace:</span><span class="sxs-lookup"><span data-stu-id="d2d8d-136">This example fixes many issues that the streams implementations had:</span></span>

- <span data-ttu-id="d2d8d-137">Není nutné pro vyrovnávací paměti řetězce vzhledem k tomu, `PipeReader` zpracovává bajtů, které nebyly použity.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-137">There is no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
- <span data-ttu-id="d2d8d-138">Kódovaný řetězec přímo se přidají do seznamu vráceného řetězce.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-138">Encoded strings are directly added to the list of returned strings.</span></span>
- <span data-ttu-id="d2d8d-139">Vytváření řetězců je bez přidělení kromě paměť používanou řetězec (s výjimkou `ToArray()` volání).</span><span class="sxs-lookup"><span data-stu-id="d2d8d-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="d2d8d-140">Adaptéry</span><span class="sxs-lookup"><span data-stu-id="d2d8d-140">Adapters</span></span>

<span data-ttu-id="d2d8d-141">Teď, když oba `Body` a `BodyPipe` vlastnosti jsou k dispozici pro `HttpRequest` a `HttpResponse`, co se stane, když nastavíte `Body` na jiný datový proud?</span><span class="sxs-lookup"><span data-stu-id="d2d8d-141">Now that both `Body` and `BodyPipe` properties are available for `HttpRequest` and `HttpResponse`, what happens when you set `Body` to a different stream?</span></span> <span data-ttu-id="d2d8d-142">Nová sada adaptérů 3.0, automaticky přizpůsobí každého typu na druhý.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-142">In 3.0, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="d2d8d-143">Například pokud nastavíte `HttpRequest.Body` do nového datového proudu `HttpRequest.BodyPipe` se automaticky nastaví na novou `PipeReader` tím končí naše `HttpRequest.Body`.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-143">For example, if you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyPipe` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span> <span data-ttu-id="d2d8d-144">Stejné chování platí pro nastavení `BodyPipe` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-144">The same behavior applies to setting the `BodyPipe` property.</span></span> <span data-ttu-id="d2d8d-145">Pokud `HttpResponse.BodyPipe` je nastavena na novou `PipeWriter`, `HttpResponse.Body` se automaticky nastaví na nový datový proud, který obtéká `HttpResponse.BodyPipe`.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-145">If `HttpResponse.BodyPipe` is set to a new `PipeWriter`, the `HttpResponse.Body` is automatically set to a new stream that wraps `HttpResponse.BodyPipe`.</span></span>

## <a name="startasync"></a><span data-ttu-id="d2d8d-146">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d2d8d-146">StartAsync</span></span>

<span data-ttu-id="d2d8d-147">`HttpResponse.StartAsync` Novinky v 3.0.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-147">`HttpResponse.StartAsync` is new in 3.0.</span></span> <span data-ttu-id="d2d8d-148">Používá se k označení, že jsou hlavičky neupravitelných a ke spuštění `OnStarting` zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-148">It is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="d2d8d-149">V 3.0 preview3, je nutné volat `StartAsync` před použitím `HttpRequest.BodyPipe`, a v budoucích verzích bude doporučení.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-149">In 3.0-preview3, you have to call `StartAsync` before using `HttpRequest.BodyPipe`, and in future releases, it will be a recommendation.</span></span> <span data-ttu-id="d2d8d-150">Používáte-li Kestrel jako serveru, volání StartAsync před použitím `PipeReader` zaručuje, že paměť vrácený `GetMemory` bude patřit Kestrel vaší interní <xref:System.IO.Pipelines.Pipe> místo externí vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="d2d8d-150">When using Kestrel as a server, calling StartAsync before using the `PipeReader` guarantees that memory returned by `GetMemory` will belong to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d2d8d-151">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d2d8d-151">Additional resources</span></span>

- [<span data-ttu-id="d2d8d-152">Představujeme System.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="d2d8d-152">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
- <xref:fundamentals/middleware/write>
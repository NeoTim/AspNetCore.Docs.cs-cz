---
title: Použití streamování v ASP.NET Core SignalR
author: bradygaster
description: Naučte se, jak streamovat data mezi klientem a serverem.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/streaming
ms.openlocfilehash: 5a172818f8910a637b731dc1b1315965f448b2ba
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393571"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="d7933-103">Použití streamování v ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="d7933-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="d7933-104">Od [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="d7933-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d7933-105">ASP.NET Core SignalR podporuje streamování z klienta na server a ze serveru na klienta.</span><span class="sxs-lookup"><span data-stu-id="d7933-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="d7933-106">To je užitečné v situacích, kdy fragmenty dat přicházejí v průběhu času.</span><span class="sxs-lookup"><span data-stu-id="d7933-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="d7933-107">Při streamování se každý fragment pošle klientovi nebo serveru hned, jakmile bude k dispozici, a ne tím, že čeká na zpřístupnění všech dat.</span><span class="sxs-lookup"><span data-stu-id="d7933-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d7933-108">ASP.NET Core SignalR podporuje streamování vrácených hodnot metod serveru.</span><span class="sxs-lookup"><span data-stu-id="d7933-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="d7933-109">To je užitečné v situacích, kdy fragmenty dat přicházejí v průběhu času.</span><span class="sxs-lookup"><span data-stu-id="d7933-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="d7933-110">Když je vrácená hodnota streamovaná klientovi, každý fragment se pošle klientovi, jakmile bude k dispozici, místo čekání na zpřístupnění všech dat.</span><span class="sxs-lookup"><span data-stu-id="d7933-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="d7933-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d7933-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="d7933-112">Nastavení centra pro streamování</span><span class="sxs-lookup"><span data-stu-id="d7933-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d7933-113">Metoda centra se automaticky stal metodou streamování, když vrací <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , `Task<IAsyncEnumerable<T>>` nebo `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="d7933-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d7933-114">Metoda centra se automaticky stávají metodou streamování, když vrátí <xref:System.Threading.Channels.ChannelReader%601> nebo `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="d7933-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="d7933-115">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="d7933-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d7933-116">Metody rozbočovače streamování `IAsyncEnumerable<T>` se můžou vracet kromě `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="d7933-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="d7933-117">Nejjednodušší způsob, jak se vrátit, `IAsyncEnumerable<T>` je vytvoření metody asynchronního iterátoru metodou, jak ukazuje následující příklad.</span><span class="sxs-lookup"><span data-stu-id="d7933-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="d7933-118">Metody asynchronního iterátoru centra můžou přijmout `CancellationToken` parametr, který se aktivuje, když se klient odhlásí z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="d7933-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="d7933-119">Metody asynchronního iterátoru zabraňují problémům běžným kanálům, jako je například, že nevrátí `ChannelReader` včas dostatek nebo ukončí metodu bez dokončení <xref:System.Threading.Channels.ChannelWriter`1> .</span><span class="sxs-lookup"><span data-stu-id="d7933-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="d7933-120">Následující příklad znázorňuje základy streamování dat klientovi pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="d7933-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="d7933-121">Pokaždé, když je objekt zapisován do <xref:System.Threading.Channels.ChannelWriter%601> , je objekt okamžitě odeslán klientovi.</span><span class="sxs-lookup"><span data-stu-id="d7933-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="d7933-122">Na konci `ChannelWriter` je dokončeno, aby klient oznámil, že je datový proud uzavřen.</span><span class="sxs-lookup"><span data-stu-id="d7933-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="d7933-123">Zapište do `ChannelWriter<T>` vlákna na pozadí a vraťte co nejrychleji `ChannelReader` .</span><span class="sxs-lookup"><span data-stu-id="d7933-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="d7933-124">Další volání centra jsou blokovaná `ChannelReader` , dokud se nevrátí.</span><span class="sxs-lookup"><span data-stu-id="d7933-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="d7933-125">Zabalit logiku do [ `try ... catch` příkazu](/dotnet/csharp/language-reference/keywords/try-catch)</span><span class="sxs-lookup"><span data-stu-id="d7933-125">Wrap logic in a [`try ... catch` statement](/dotnet/csharp/language-reference/keywords/try-catch).</span></span> <span data-ttu-id="d7933-126">Dokončete `Channel` blok v [ `finally` bloku](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span><span class="sxs-lookup"><span data-stu-id="d7933-126">Complete the `Channel` in a [`finally` block](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span></span> <span data-ttu-id="d7933-127">Pokud chcete zaznamenat chybu, Zachyťte ji uvnitř `catch` bloku a zapište ji do `finally` bloku.</span><span class="sxs-lookup"><span data-stu-id="d7933-127">If you want to flow an error, capture it inside the `catch` block and write it in the `finally` block.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d7933-128">Metody služby streamování na straně serveru můžou přijmout `CancellationToken` parametr, který se aktivuje, když se klient odhlásí z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="d7933-128">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="d7933-129">Pomocí tohoto tokenu zastavte operaci serveru a uvolněte všechny prostředky, pokud se klient odpojí před koncem datového proudu.</span><span class="sxs-lookup"><span data-stu-id="d7933-129">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="d7933-130">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="d7933-130">Client-to-server streaming</span></span>

<span data-ttu-id="d7933-131">Metoda centra se automaticky stalá metodou streamování typu klient-server, když přijme jeden nebo více objektů typu <xref:System.Threading.Channels.ChannelReader%601> nebo <xref:System.Collections.Generic.IAsyncEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="d7933-131">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="d7933-132">Následující příklad ukazuje základy čtení dat streamování odeslaných z klienta.</span><span class="sxs-lookup"><span data-stu-id="d7933-132">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="d7933-133">Pokaždé, když klient zapíše do <xref:System.Threading.Channels.ChannelWriter%601> , data jsou zapsána do na `ChannelReader` serveru, ze kterého metoda rozbočovače čte.</span><span class="sxs-lookup"><span data-stu-id="d7933-133">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="d7933-134"><xref:System.Collections.Generic.IAsyncEnumerable%601>Následuje verze metody.</span><span class="sxs-lookup"><span data-stu-id="d7933-134">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="d7933-135">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="d7933-135">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="d7933-136">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="d7933-136">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d7933-137">`StreamAsync`Metody a `StreamAsChannelAsync` `HubConnection` jsou používány k vyvolání metod streamování mezi servery.</span><span class="sxs-lookup"><span data-stu-id="d7933-137">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="d7933-138">Předejte název a argumenty metody centra definované v metodě hub do `StreamAsync` nebo `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="d7933-138">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="d7933-139">Obecný parametr v `StreamAsync<T>` a `StreamAsChannelAsync<T>` Určuje typ objektů vrácených metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="d7933-139">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="d7933-140">Objekt typu `IAsyncEnumerable<T>` nebo `ChannelReader<T>` je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.</span><span class="sxs-lookup"><span data-stu-id="d7933-140">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="d7933-141">`StreamAsync`Příklad, který vrátí `IAsyncEnumerable<int>` :</span><span class="sxs-lookup"><span data-stu-id="d7933-141">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="d7933-142">Odpovídající `StreamAsChannelAsync` příklad, který vrátí `ChannelReader<int>` :</span><span class="sxs-lookup"><span data-stu-id="d7933-142">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d7933-143">`StreamAsChannelAsync`Metoda v `HubConnection` je použita k vyvolání metody streamování ze serveru na klienta.</span><span class="sxs-lookup"><span data-stu-id="d7933-143">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="d7933-144">Předat název a argumenty metody centra definované v metodě hub pro `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="d7933-144">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="d7933-145">Obecný parametr v `StreamAsChannelAsync<T>` Určuje typ objektů vrácených metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="d7933-145">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="d7933-146">`ChannelReader<T>`Je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.</span><span class="sxs-lookup"><span data-stu-id="d7933-146">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="d7933-147">`StreamAsChannelAsync`Metoda v `HubConnection` je použita k vyvolání metody streamování ze serveru na klienta.</span><span class="sxs-lookup"><span data-stu-id="d7933-147">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="d7933-148">Předat název a argumenty metody centra definované v metodě hub pro `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="d7933-148">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="d7933-149">Obecný parametr v `StreamAsChannelAsync<T>` Určuje typ objektů vrácených metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="d7933-149">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="d7933-150">`ChannelReader<T>`Je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.</span><span class="sxs-lookup"><span data-stu-id="d7933-150">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="d7933-151">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="d7933-151">Client-to-server streaming</span></span>

<span data-ttu-id="d7933-152">Existují dva způsoby, jak vyvolat metodu rozbočovače streamování typu klient-server z klienta .NET.</span><span class="sxs-lookup"><span data-stu-id="d7933-152">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="d7933-153">Můžete buď předat `IAsyncEnumerable<T>` `ChannelReader` parametr nebo jako argument do `SendAsync` , `InvokeAsync` nebo `StreamAsChannelAsync` , v závislosti na volané metodě centra.</span><span class="sxs-lookup"><span data-stu-id="d7933-153">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="d7933-154">Pokaždé, když jsou data zapsána do `IAsyncEnumerable` `ChannelWriter` objektu nebo, metoda centra na serveru obdrží novou položku s daty z klienta.</span><span class="sxs-lookup"><span data-stu-id="d7933-154">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="d7933-155">Při použití `IAsyncEnumerable` objektu se datový proud ukončí po ukončení metody, která vrací položky streamu.</span><span class="sxs-lookup"><span data-stu-id="d7933-155">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="d7933-156">Pokud používáte `ChannelWriter` , můžete kanál dokončete pomocí těchto kroků `channel.Writer.Complete()` :</span><span class="sxs-lookup"><span data-stu-id="d7933-156">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="d7933-157">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="d7933-157">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="d7933-158">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="d7933-158">Server-to-client streaming</span></span>

<span data-ttu-id="d7933-159">Klienti JavaScriptu volají metody streamování na straně serveru na rozbočovačích s `connection.stream` .</span><span class="sxs-lookup"><span data-stu-id="d7933-159">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="d7933-160">`stream`Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="d7933-160">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="d7933-161">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="d7933-161">The name of the hub method.</span></span> <span data-ttu-id="d7933-162">V následujícím příkladu je název metody rozbočovače `Counter` .</span><span class="sxs-lookup"><span data-stu-id="d7933-162">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="d7933-163">Argumenty definované v metodě centra</span><span class="sxs-lookup"><span data-stu-id="d7933-163">Arguments defined in the hub method.</span></span> <span data-ttu-id="d7933-164">V následujícím příkladu jsou argumenty počtem položek datového proudu, které mají být přijímány, a zpoždění mezi položkami datového proudu.</span><span class="sxs-lookup"><span data-stu-id="d7933-164">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="d7933-165">`connection.stream` Vrátí `IStreamResult` , který obsahuje `subscribe` metodu.</span><span class="sxs-lookup"><span data-stu-id="d7933-165">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="d7933-166">Předejte `IStreamSubscriber` `subscribe` a nastavte `next` `error` `complete` zpětná volání, a pro příjem oznámení od `stream` vyvolání.</span><span class="sxs-lookup"><span data-stu-id="d7933-166">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="d7933-167">Chcete-li ukončit datový proud z klienta, zavolejte `dispose` metodu na `ISubscription` , kterou vrátí `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="d7933-167">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="d7933-168">Volání této metody způsobí zrušení `CancellationToken` parametru metody hub, pokud jste ji zadali.</span><span class="sxs-lookup"><span data-stu-id="d7933-168">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="d7933-169">Chcete-li ukončit datový proud z klienta, zavolejte `dispose` metodu na `ISubscription` , kterou vrátí `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="d7933-169">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="d7933-170">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="d7933-170">Client-to-server streaming</span></span>

<span data-ttu-id="d7933-171">Klienti JavaScriptu volají metody streamování typu klient-server na rozbočovači předáním `Subject` jako argumentu `send` , nebo v `invoke` `stream` závislosti na volané metodě centra.</span><span class="sxs-lookup"><span data-stu-id="d7933-171">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="d7933-172">`Subject`Je třída, která vypadá jako `Subject` .</span><span class="sxs-lookup"><span data-stu-id="d7933-172">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="d7933-173">Například v RxJS můžete použít třídu [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) z této knihovny.</span><span class="sxs-lookup"><span data-stu-id="d7933-173">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="d7933-174">Volání `subject.next(item)` s položkou zapisuje položku do datového proudu a metoda hub přijme položku na serveru.</span><span class="sxs-lookup"><span data-stu-id="d7933-174">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="d7933-175">Chcete-li ukončit datový proud, zavolejte `subject.complete()` .</span><span class="sxs-lookup"><span data-stu-id="d7933-175">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="d7933-176">Klient Java</span><span class="sxs-lookup"><span data-stu-id="d7933-176">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="d7933-177">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="d7933-177">Server-to-client streaming</span></span>

<span data-ttu-id="d7933-178">SignalRKlient Java používá `stream` metodu k vyvolání metod streamování.</span><span class="sxs-lookup"><span data-stu-id="d7933-178">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="d7933-179">`stream` přijímá tři nebo více argumentů:</span><span class="sxs-lookup"><span data-stu-id="d7933-179">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="d7933-180">Očekávaný typ položek datového proudu.</span><span class="sxs-lookup"><span data-stu-id="d7933-180">The expected type of the stream items.</span></span>
* <span data-ttu-id="d7933-181">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="d7933-181">The name of the hub method.</span></span>
* <span data-ttu-id="d7933-182">Argumenty definované v metodě centra</span><span class="sxs-lookup"><span data-stu-id="d7933-182">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="d7933-183">`stream`Metoda on `HubConnection` vrátí pozorovatelný typ položky streamu.</span><span class="sxs-lookup"><span data-stu-id="d7933-183">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="d7933-184">Metoda Pozorovatelho typu `subscribe` je místo, kde `onNext` `onError` `onCompleted` jsou definovány obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="d7933-184">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d7933-185">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d7933-185">Additional resources</span></span>

* [<span data-ttu-id="d7933-186">Centra</span><span class="sxs-lookup"><span data-stu-id="d7933-186">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="d7933-187">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="d7933-187">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="d7933-188">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="d7933-188">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="d7933-189">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="d7933-189">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

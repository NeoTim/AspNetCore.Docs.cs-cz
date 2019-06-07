---
title: Použití datových proudů v knihovně SignalR technologie ASP.NET Core
author: bradygaster
description: Naučíte se Streamovat data mezi klientem a serverem.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/05/2019
uid: signalr/streaming
ms.openlocfilehash: a75156f398e113393ddb891d16eec3f09de80c09
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750188"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="6107d-103">Použití datových proudů v knihovně SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6107d-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="6107d-104">Podle [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="6107d-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6107d-105">Funkce SignalR technologie ASP.NET Core podporuje datové proudy z klienta na server a ze serveru do klienta.</span><span class="sxs-lookup"><span data-stu-id="6107d-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="6107d-106">To je užitečné pro scénáře, kde fragmenty data dorazí v čase.</span><span class="sxs-lookup"><span data-stu-id="6107d-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="6107d-107">Při vysílání datových proudů, každý fragment posílá klienta nebo serveru hned, jakmile bude k dispozici, nikoli čeká se na všech dat k dispozici.</span><span class="sxs-lookup"><span data-stu-id="6107d-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6107d-108">Funkce SignalR technologie ASP.NET Core podporuje streamování návratové hodnoty metod serveru.</span><span class="sxs-lookup"><span data-stu-id="6107d-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="6107d-109">To je užitečné pro scénáře, kde fragmenty data dorazí v čase.</span><span class="sxs-lookup"><span data-stu-id="6107d-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="6107d-110">Pokud vrácená hodnota se streamuje klientovi, každý fragment je odeslat klientovi, jakmile bude k dispozici, nikoli čeká všechna data k dispozici.</span><span class="sxs-lookup"><span data-stu-id="6107d-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="6107d-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6107d-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="6107d-112">Nastavení centra pro streamování</span><span class="sxs-lookup"><span data-stu-id="6107d-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6107d-113">Metody rozbočovače automaticky změní streamování metody rozbočovače, pokud vrací <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, nebo `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="6107d-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6107d-114">Metodu rozbočovače na automaticky stane streamování metody rozbočovače, je po návratu <xref:System.Threading.Channels.ChannelReader%601> nebo `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="6107d-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="6107d-115">Server klient streamování</span><span class="sxs-lookup"><span data-stu-id="6107d-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6107d-116">Streamování metod rozbočovače na může vrátit `IAsyncEnumerable<T>` kromě `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="6107d-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="6107d-117">Nejjednodušší způsob, jak vrátit `IAsyncEnumerable<T>` je tím, že metodu rozbočovače na asynchronní metodu iterátoru, jak ukazuje následující příklad.</span><span class="sxs-lookup"><span data-stu-id="6107d-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="6107d-118">Můžete přijmout metod rozbočovače na asynchronní iterátor `CancellationToken` parametr, který se aktivuje, když klient zruší z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="6107d-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="6107d-119">Asynchronní metody iterátoru Předcházejte problémům běžných kanálů, jako je například nevrací `ChannelReader` dostatečně včas nebo standardním ukončením metody bez dokončení <xref:System.Threading.Channels.ChannelWriter`1>.</span><span class="sxs-lookup"><span data-stu-id="6107d-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="6107d-120">Následující příklad ukazuje základy streamovaných dat na klientovi pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="6107d-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="6107d-121">Vždy, když je objekt zapsána do <xref:System.Threading.Channels.ChannelWriter%601>, objekt je okamžitě se odešlou do klienta.</span><span class="sxs-lookup"><span data-stu-id="6107d-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="6107d-122">Na konci `ChannelWriter` je dokončit, aby se dali pokyn klientovi, datový proud je uzavřen.</span><span class="sxs-lookup"><span data-stu-id="6107d-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="6107d-123">Zápis do `ChannelWriter<T>` na vlákně na pozadí a vraťte se `ChannelReader` co nejdříve.</span><span class="sxs-lookup"><span data-stu-id="6107d-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="6107d-124">Další volání rozbočovače je zablokovaná do `ChannelReader` je vrácena.</span><span class="sxs-lookup"><span data-stu-id="6107d-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="6107d-125">Zabalení logiku `try ... catch`.</span><span class="sxs-lookup"><span data-stu-id="6107d-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="6107d-126">Dokončení `Channel` v `catch` i mimo ni `catch` zajistit centra volání metody dokončila správně.</span><span class="sxs-lookup"><span data-stu-id="6107d-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="6107d-127">Streamování metod rozbočovače na serveru do klienta může přijmout `CancellationToken` parametr, který se aktivuje, když klient zruší z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="6107d-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="6107d-128">Pomocí tohoto tokenu operace serveru zastavit a uvolnit všechny prostředky, pokud se klient odpojí do konce datového proudu.</span><span class="sxs-lookup"><span data-stu-id="6107d-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="6107d-129">Klient server streamování</span><span class="sxs-lookup"><span data-stu-id="6107d-129">Client-to-server streaming</span></span>

<span data-ttu-id="6107d-130">Metody rozbočovače automaticky změní streamování metody rozbočovače klienta se serverem, pokud přijímá jeden nebo více objektů typu <xref:System.Threading.Channels.ChannelReader%601> nebo <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span><span class="sxs-lookup"><span data-stu-id="6107d-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="6107d-131">Následující příklad ukazuje základní informace o čtení datových proudů dat odeslaných z klienta.</span><span class="sxs-lookup"><span data-stu-id="6107d-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="6107d-132">Vždy, když klient zapíše do <xref:System.Threading.Channels.ChannelWriter%601>, data jsou zapsána do `ChannelReader` na serveru, ze kterého čte metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6107d-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="6107d-133"><xref:System.Collections.Generic.IAsyncEnumerable%601> Odpovídá verzi metody.</span><span class="sxs-lookup"><span data-stu-id="6107d-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<Stream> stream) 
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="6107d-134">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="6107d-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="6107d-135">Server klient streamování</span><span class="sxs-lookup"><span data-stu-id="6107d-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6107d-136">`StreamAsync` a `StreamAsChannelAsync` metody `HubConnection` se používají k vyvolání metody streaming klient a server.</span><span class="sxs-lookup"><span data-stu-id="6107d-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="6107d-137">Předat název metody rozbočovače a argumenty, které jsou definovány v metody rozbočovače na `StreamAsync` nebo `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="6107d-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="6107d-138">Obecný parametr na `StreamAsync<T>` a `StreamAsChannelAsync<T>` Určuje typ objektu vrácený metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="6107d-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="6107d-139">Objekt typu `IAsyncEnumerable<T>` nebo `ChannelReader<T>` vrácená z volání datového proudu a představuje datový proud na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="6107d-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="6107d-140">A `StreamAsync` příklad, který vrátí `IAsyncEnumerable<int>`:</span><span class="sxs-lookup"><span data-stu-id="6107d-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

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

<span data-ttu-id="6107d-141">Odpovídající `StreamAsChannelAsync` příklad, který vrátí `ChannelReader<int>`:</span><span class="sxs-lookup"><span data-stu-id="6107d-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

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

<span data-ttu-id="6107d-142">`StreamAsChannelAsync` Metodu na `HubConnection` se používá k volání metody streaming klient a server.</span><span class="sxs-lookup"><span data-stu-id="6107d-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="6107d-143">Předat název metody rozbočovače a argumenty, které jsou definovány v metody rozbočovače na `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="6107d-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="6107d-144">Obecný parametr na `StreamAsChannelAsync<T>` Určuje typ objektu vrácený metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="6107d-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="6107d-145">A `ChannelReader<T>` vrácená z volání datového proudu a představuje datový proud na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="6107d-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

<span data-ttu-id="6107d-146">`StreamAsChannelAsync` Metodu na `HubConnection` se používá k volání metody streaming klient a server.</span><span class="sxs-lookup"><span data-stu-id="6107d-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="6107d-147">Předat název metody rozbočovače a argumenty, které jsou definovány v metody rozbočovače na `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="6107d-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="6107d-148">Obecný parametr na `StreamAsChannelAsync<T>` Určuje typ objektu vrácený metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="6107d-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="6107d-149">A `ChannelReader<T>` vrácená z volání datového proudu a představuje datový proud na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="6107d-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="6107d-150">Klient server streamování</span><span class="sxs-lookup"><span data-stu-id="6107d-150">Client-to-server streaming</span></span>

<span data-ttu-id="6107d-151">Existují dva způsoby, jak vyvolat metodu streamování rozbočovače klienta k serveru z klienta .NET.</span><span class="sxs-lookup"><span data-stu-id="6107d-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="6107d-152">Můžete buď předejte `IAsyncEnumerable<T>` nebo `ChannelReader` jako argument `SendAsync`, `InvokeAsync`, nebo `StreamAsChannelAsync`, v závislosti na vyvolání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6107d-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="6107d-153">Vždy, když data se zapisují do `IAsyncEnumerable` nebo `ChannelWriter` objekt metody rozbočovače na serveru obdrží novou položku s daty z klienta.</span><span class="sxs-lookup"><span data-stu-id="6107d-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="6107d-154">Pokud se používá `IAsyncEnumerable` objekt datového proudu končí po metodě vracení datového proudu položky ukončí.</span><span class="sxs-lookup"><span data-stu-id="6107d-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

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

<span data-ttu-id="6107d-155">Nebo pokud používáte `ChannelWriter`, dokončete kanál s `channel.Writer.Complete()`:</span><span class="sxs-lookup"><span data-stu-id="6107d-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="6107d-156">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="6107d-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="6107d-157">Server klient streamování</span><span class="sxs-lookup"><span data-stu-id="6107d-157">Server-to-client streaming</span></span>

<span data-ttu-id="6107d-158">Klientů JavaScript volat metody klient a server streamování na hubs s využitím `connection.stream`.</span><span class="sxs-lookup"><span data-stu-id="6107d-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="6107d-159">`stream` Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="6107d-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="6107d-160">Název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6107d-160">The name of the hub method.</span></span> <span data-ttu-id="6107d-161">V následujícím příkladu je název metody rozbočovače `Counter`.</span><span class="sxs-lookup"><span data-stu-id="6107d-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="6107d-162">Argumenty podle metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6107d-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="6107d-163">V následujícím příkladu jsou argumenty počet pro počet položek datového proudu pro příjem a zpoždění mezi položkami datového proudu.</span><span class="sxs-lookup"><span data-stu-id="6107d-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="6107d-164">`connection.stream` Vrátí `IStreamResult`, který obsahuje `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="6107d-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="6107d-165">Předejte `IStreamSubscriber` k `subscribe` a nastavit `next`, `error`, a `complete` zpětná volání k přijímání oznámení z `stream` vyvolání.</span><span class="sxs-lookup"><span data-stu-id="6107d-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="6107d-166">Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="6107d-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="6107d-167">Voláním této metody způsobí zrušení `CancellationToken` parametru metody rozbočovače, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="6107d-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="6107d-168">Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="6107d-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="6107d-169">Klient server streamování</span><span class="sxs-lookup"><span data-stu-id="6107d-169">Client-to-server streaming</span></span>

<span data-ttu-id="6107d-170">Klientům JavaScript volat metody streamování klient server v centrech předáním `Subject` jako argument `send`, `invoke`, nebo `stream`, v závislosti na vyvolání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6107d-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="6107d-171">`Subject` Je třída, která vypadá jako `Subject`.</span><span class="sxs-lookup"><span data-stu-id="6107d-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="6107d-172">Například v RxJS, můžete použít [subjektu](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) třídy z této knihovny.</span><span class="sxs-lookup"><span data-stu-id="6107d-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="6107d-173">Volání `subject.next(item)` s položku zapíše položku do datového proudu a metody rozbočovače přijímá položka na serveru.</span><span class="sxs-lookup"><span data-stu-id="6107d-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="6107d-174">Do konce datového proudu, volání `subject.complete()`.</span><span class="sxs-lookup"><span data-stu-id="6107d-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="6107d-175">Klient Java</span><span class="sxs-lookup"><span data-stu-id="6107d-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="6107d-176">Server klient streamování</span><span class="sxs-lookup"><span data-stu-id="6107d-176">Server-to-client streaming</span></span>

<span data-ttu-id="6107d-177">Používá klientskou sadou SignalR Java `stream` metoda k vyvolání metody streaming.</span><span class="sxs-lookup"><span data-stu-id="6107d-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="6107d-178">`stream` přijímá tři nebo více argumentů:</span><span class="sxs-lookup"><span data-stu-id="6107d-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="6107d-179">Očekávaný typ položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="6107d-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="6107d-180">Název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6107d-180">The name of the hub method.</span></span>
* <span data-ttu-id="6107d-181">Argumenty podle metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6107d-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="6107d-182">`stream` Metodu na `HubConnection` vrátí existuje zjištěný typ položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="6107d-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="6107d-183">Pozorovatelné typ `subscribe` metoda je tam, kde `onNext`, `onError` a `onCompleted` obslužné rutiny jsou definovány.</span><span class="sxs-lookup"><span data-stu-id="6107d-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6107d-184">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6107d-184">Additional resources</span></span>

* [<span data-ttu-id="6107d-185">Centra</span><span class="sxs-lookup"><span data-stu-id="6107d-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="6107d-186">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="6107d-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="6107d-187">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="6107d-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="6107d-188">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="6107d-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

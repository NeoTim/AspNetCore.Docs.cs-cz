---
title: Použití streamování v ASP.NET CoreSignalR
author: bradygaster
description: Naučte se, jak streamovat data mezi klientem a serverem.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/streaming
ms.openlocfilehash: 4d6461bc85573776ccdbe81bf3c74145a9cf7ed6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773887"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="1e59e-103">Použití streamování v ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="1e59e-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="1e59e-104">Od [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="1e59e-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e59e-105">ASP.NET Core SignalR podporuje streamování z klienta na server a ze serveru na klienta.</span><span class="sxs-lookup"><span data-stu-id="1e59e-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="1e59e-106">To je užitečné v situacích, kdy fragmenty dat přicházejí v průběhu času.</span><span class="sxs-lookup"><span data-stu-id="1e59e-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="1e59e-107">Při streamování se každý fragment pošle klientovi nebo serveru hned, jakmile bude k dispozici, a ne tím, že čeká na zpřístupnění všech dat.</span><span class="sxs-lookup"><span data-stu-id="1e59e-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e59e-108">ASP.NET Core SignalR podporuje streamování vrácených hodnot metod serveru.</span><span class="sxs-lookup"><span data-stu-id="1e59e-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="1e59e-109">To je užitečné v situacích, kdy fragmenty dat přicházejí v průběhu času.</span><span class="sxs-lookup"><span data-stu-id="1e59e-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="1e59e-110">Když je vrácená hodnota streamovaná klientovi, každý fragment se pošle klientovi, jakmile bude k dispozici, místo čekání na zpřístupnění všech dat.</span><span class="sxs-lookup"><span data-stu-id="1e59e-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="1e59e-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1e59e-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="1e59e-112">Nastavení centra pro streamování</span><span class="sxs-lookup"><span data-stu-id="1e59e-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e59e-113">Metoda centra se automaticky stal metodou streamování, když <xref:System.Collections.Generic.IAsyncEnumerable`1>vrací, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`nebo. `Task<ChannelReader<T>>`</span><span class="sxs-lookup"><span data-stu-id="1e59e-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e59e-114">Metoda centra se automaticky stávají metodou streamování, když vrátí <xref:System.Threading.Channels.ChannelReader%601> nebo. `Task<ChannelReader<T>>`</span><span class="sxs-lookup"><span data-stu-id="1e59e-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="1e59e-115">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="1e59e-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e59e-116">Metody rozbočovače streamování `IAsyncEnumerable<T>` se `ChannelReader<T>`můžou vracet kromě.</span><span class="sxs-lookup"><span data-stu-id="1e59e-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="1e59e-117">Nejjednodušší způsob, jak se `IAsyncEnumerable<T>` vrátit, je vytvoření metody asynchronního iterátoru metodou, jak ukazuje následující příklad.</span><span class="sxs-lookup"><span data-stu-id="1e59e-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="1e59e-118">Metody asynchronního iterátoru centra můžou `CancellationToken` přijmout parametr, který se aktivuje, když se klient odhlásí z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="1e59e-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="1e59e-119">Metody asynchronního iterátoru zabraňují problémům běžným kanálům, jako je `ChannelReader` například, že nevrátí včas dostatek nebo ukončí metodu <xref:System.Threading.Channels.ChannelWriter`1>bez dokončení.</span><span class="sxs-lookup"><span data-stu-id="1e59e-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="1e59e-120">Následující příklad znázorňuje základy streamování dat klientovi pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="1e59e-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="1e59e-121">Pokaždé <xref:System.Threading.Channels.ChannelWriter%601>, když je objekt zapisován do, je objekt okamžitě odeslán klientovi.</span><span class="sxs-lookup"><span data-stu-id="1e59e-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="1e59e-122">Na konci `ChannelWriter` je dokončeno, aby klient oznámil, že je datový proud uzavřen.</span><span class="sxs-lookup"><span data-stu-id="1e59e-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="1e59e-123">Zapište do `ChannelWriter<T>` vlákna na pozadí a vraťte `ChannelReader` co nejrychleji.</span><span class="sxs-lookup"><span data-stu-id="1e59e-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="1e59e-124">Další volání centra jsou blokovaná, dokud `ChannelReader` se nevrátí.</span><span class="sxs-lookup"><span data-stu-id="1e59e-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="1e59e-125">Zabalte logiku do `try ... catch`.</span><span class="sxs-lookup"><span data-stu-id="1e59e-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="1e59e-126">Dokončete `Channel` v `catch` a mimo `catch` a ujistěte se, že je volání metody rozbočovače správně dokončeno.</span><span class="sxs-lookup"><span data-stu-id="1e59e-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="1e59e-127">Metody služby streamování na straně serveru můžou přijmout `CancellationToken` parametr, který se aktivuje, když se klient odhlásí z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="1e59e-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="1e59e-128">Pomocí tohoto tokenu zastavte operaci serveru a uvolněte všechny prostředky, pokud se klient odpojí před koncem datového proudu.</span><span class="sxs-lookup"><span data-stu-id="1e59e-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="1e59e-129">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="1e59e-129">Client-to-server streaming</span></span>

<span data-ttu-id="1e59e-130">Metoda centra se automaticky stalá metodou streamování typu klient-server, když přijme jeden nebo více objektů typu <xref:System.Threading.Channels.ChannelReader%601> nebo. <xref:System.Collections.Generic.IAsyncEnumerable%601></span><span class="sxs-lookup"><span data-stu-id="1e59e-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="1e59e-131">Následující příklad ukazuje základy čtení dat streamování odeslaných z klienta.</span><span class="sxs-lookup"><span data-stu-id="1e59e-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="1e59e-132">Pokaždé <xref:System.Threading.Channels.ChannelWriter%601>, když klient zapíše do, data jsou zapsána do `ChannelReader` na serveru, ze kterého metoda rozbočovače čte.</span><span class="sxs-lookup"><span data-stu-id="1e59e-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="1e59e-133">Následuje <xref:System.Collections.Generic.IAsyncEnumerable%601> verze metody.</span><span class="sxs-lookup"><span data-stu-id="1e59e-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

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

## <a name="net-client"></a><span data-ttu-id="1e59e-134">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="1e59e-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="1e59e-135">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="1e59e-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e59e-136">Metody `StreamAsync` a `StreamAsChannelAsync` `HubConnection` jsou používány k vyvolání metod streamování mezi servery.</span><span class="sxs-lookup"><span data-stu-id="1e59e-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="1e59e-137">Předejte název a argumenty metody centra definované v metodě hub do `StreamAsync` nebo. `StreamAsChannelAsync`</span><span class="sxs-lookup"><span data-stu-id="1e59e-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="1e59e-138">Obecný parametr v `StreamAsync<T>` a `StreamAsChannelAsync<T>` určuje typ objektů vrácených metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="1e59e-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="1e59e-139">Objekt typu `IAsyncEnumerable<T>` nebo `ChannelReader<T>` je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.</span><span class="sxs-lookup"><span data-stu-id="1e59e-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="1e59e-140">`StreamAsync` Příklad, který vrátí `IAsyncEnumerable<int>`:</span><span class="sxs-lookup"><span data-stu-id="1e59e-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

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

<span data-ttu-id="1e59e-141">Odpovídající `StreamAsChannelAsync` příklad, který vrátí `ChannelReader<int>`:</span><span class="sxs-lookup"><span data-stu-id="1e59e-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

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

<span data-ttu-id="1e59e-142">`StreamAsChannelAsync` Metoda v `HubConnection` je použita k vyvolání metody streamování ze serveru na klienta.</span><span class="sxs-lookup"><span data-stu-id="1e59e-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="1e59e-143">Předat název a argumenty metody centra definované v metodě hub pro `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="1e59e-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="1e59e-144">Obecný parametr v `StreamAsChannelAsync<T>` určuje typ objektů vrácených metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="1e59e-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="1e59e-145">`ChannelReader<T>` Je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.</span><span class="sxs-lookup"><span data-stu-id="1e59e-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

<span data-ttu-id="1e59e-146">`StreamAsChannelAsync` Metoda v `HubConnection` je použita k vyvolání metody streamování ze serveru na klienta.</span><span class="sxs-lookup"><span data-stu-id="1e59e-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="1e59e-147">Předat název a argumenty metody centra definované v metodě hub pro `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="1e59e-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="1e59e-148">Obecný parametr v `StreamAsChannelAsync<T>` určuje typ objektů vrácených metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="1e59e-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="1e59e-149">`ChannelReader<T>` Je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.</span><span class="sxs-lookup"><span data-stu-id="1e59e-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="1e59e-150">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="1e59e-150">Client-to-server streaming</span></span>

<span data-ttu-id="1e59e-151">Existují dva způsoby, jak vyvolat metodu rozbočovače streamování typu klient-server z klienta .NET.</span><span class="sxs-lookup"><span data-stu-id="1e59e-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="1e59e-152">Můžete `IAsyncEnumerable<T>` buď předat parametr nebo `ChannelReader` jako argument do `SendAsync`, `InvokeAsync`nebo `StreamAsChannelAsync`, v závislosti na volané metodě centra.</span><span class="sxs-lookup"><span data-stu-id="1e59e-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="1e59e-153">Pokaždé, když jsou data `IAsyncEnumerable` zapsána do objektu nebo `ChannelWriter` , metoda centra na serveru obdrží novou položku s daty z klienta.</span><span class="sxs-lookup"><span data-stu-id="1e59e-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="1e59e-154">Při použití `IAsyncEnumerable` objektu se datový proud ukončí po ukončení metody, která vrací položky streamu.</span><span class="sxs-lookup"><span data-stu-id="1e59e-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

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

<span data-ttu-id="1e59e-155">Pokud používáte `ChannelWriter`, můžete kanál dokončete pomocí `channel.Writer.Complete()`těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="1e59e-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="1e59e-156">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="1e59e-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="1e59e-157">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="1e59e-157">Server-to-client streaming</span></span>

<span data-ttu-id="1e59e-158">Klienti JavaScriptu volají metody streamování na straně serveru na rozbočovačích s `connection.stream`.</span><span class="sxs-lookup"><span data-stu-id="1e59e-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="1e59e-159">`stream` Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="1e59e-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="1e59e-160">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="1e59e-160">The name of the hub method.</span></span> <span data-ttu-id="1e59e-161">V následujícím příkladu je `Counter`název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="1e59e-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="1e59e-162">Argumenty definované v metodě centra</span><span class="sxs-lookup"><span data-stu-id="1e59e-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="1e59e-163">V následujícím příkladu jsou argumenty počtem položek datového proudu, které mají být přijímány, a zpoždění mezi položkami datového proudu.</span><span class="sxs-lookup"><span data-stu-id="1e59e-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="1e59e-164">`connection.stream`Vrátí `IStreamResult`, který obsahuje `subscribe` metodu.</span><span class="sxs-lookup"><span data-stu-id="1e59e-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="1e59e-165">`IStreamSubscriber` Předejte `subscribe` a nastavte zpětná `next`volání `error`, a `complete` pro příjem oznámení od `stream` vyvolání.</span><span class="sxs-lookup"><span data-stu-id="1e59e-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="1e59e-166">Chcete-li ukončit datový proud z klienta, zavolejte `dispose` metodu na `ISubscription` , kterou vrátí `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="1e59e-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="1e59e-167">Volání této metody způsobí zrušení `CancellationToken` parametru metody hub, pokud jste ji zadali.</span><span class="sxs-lookup"><span data-stu-id="1e59e-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="1e59e-168">Chcete-li ukončit datový proud z klienta, zavolejte `dispose` metodu na `ISubscription` , kterou vrátí `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="1e59e-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="1e59e-169">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="1e59e-169">Client-to-server streaming</span></span>

<span data-ttu-id="1e59e-170">Klienti JavaScriptu `Subject` volají metody streamování typu klient-server na rozbočovači předáním jako argumentu `send`, `invoke`nebo `stream`v závislosti na volané metodě centra.</span><span class="sxs-lookup"><span data-stu-id="1e59e-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="1e59e-171">`Subject` Je třída, která vypadá jako `Subject`.</span><span class="sxs-lookup"><span data-stu-id="1e59e-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="1e59e-172">Například v RxJS můžete použít třídu [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) z této knihovny.</span><span class="sxs-lookup"><span data-stu-id="1e59e-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="1e59e-173">Volání `subject.next(item)` s položkou zapisuje položku do datového proudu a metoda hub přijme položku na serveru.</span><span class="sxs-lookup"><span data-stu-id="1e59e-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="1e59e-174">Chcete-li ukončit datový proud `subject.complete()`, zavolejte.</span><span class="sxs-lookup"><span data-stu-id="1e59e-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="1e59e-175">Klient Java</span><span class="sxs-lookup"><span data-stu-id="1e59e-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="1e59e-176">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="1e59e-176">Server-to-client streaming</span></span>

<span data-ttu-id="1e59e-177">Klient SignalR Java používá `stream` metodu k vyvolání metod streamování.</span><span class="sxs-lookup"><span data-stu-id="1e59e-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="1e59e-178">`stream`přijímá tři nebo více argumentů:</span><span class="sxs-lookup"><span data-stu-id="1e59e-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="1e59e-179">Očekávaný typ položek datového proudu.</span><span class="sxs-lookup"><span data-stu-id="1e59e-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="1e59e-180">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="1e59e-180">The name of the hub method.</span></span>
* <span data-ttu-id="1e59e-181">Argumenty definované v metodě centra</span><span class="sxs-lookup"><span data-stu-id="1e59e-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="1e59e-182">`stream` Metoda on `HubConnection` vrátí pozorovatelný typ položky streamu.</span><span class="sxs-lookup"><span data-stu-id="1e59e-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="1e59e-183">`subscribe` Metoda pozorovatelho typu je místo, `onNext` `onError` kde jsou definovány `onCompleted` obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="1e59e-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1e59e-184">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1e59e-184">Additional resources</span></span>

* [<span data-ttu-id="1e59e-185">Centra</span><span class="sxs-lookup"><span data-stu-id="1e59e-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="1e59e-186">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="1e59e-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="1e59e-187">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="1e59e-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="1e59e-188">Publikování aplikací do Azure</span><span class="sxs-lookup"><span data-stu-id="1e59e-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

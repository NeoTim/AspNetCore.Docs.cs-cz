---
title: Použití streamování v ASP.NET CoreSignalR
author: bradygaster
description: Naučte se, jak streamovat data mezi klientem a serverem.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
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
ms.openlocfilehash: e0eabe711fd69e42bd9bfa5e03a92e1df780e4db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022508"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="ec8a6-103">Použití streamování v ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="ec8a6-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="ec8a6-104">Od [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="ec8a6-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec8a6-105">ASP.NET Core SignalR podporuje streamování z klienta na server a ze serveru na klienta.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="ec8a6-106">To je užitečné v situacích, kdy fragmenty dat přicházejí v průběhu času.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="ec8a6-107">Při streamování se každý fragment pošle klientovi nebo serveru hned, jakmile bude k dispozici, a ne tím, že čeká na zpřístupnění všech dat.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec8a6-108">ASP.NET Core SignalR podporuje streamování vrácených hodnot metod serveru.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="ec8a6-109">To je užitečné v situacích, kdy fragmenty dat přicházejí v průběhu času.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="ec8a6-110">Když je vrácená hodnota streamovaná klientovi, každý fragment se pošle klientovi, jakmile bude k dispozici, místo čekání na zpřístupnění všech dat.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="ec8a6-111">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ec8a6-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="ec8a6-112">Nastavení centra pro streamování</span><span class="sxs-lookup"><span data-stu-id="ec8a6-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec8a6-113">Metoda centra se automaticky stal metodou streamování, když vrací <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , `Task<IAsyncEnumerable<T>>` nebo `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec8a6-114">Metoda centra se automaticky stávají metodou streamování, když vrátí <xref:System.Threading.Channels.ChannelReader%601> nebo `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="ec8a6-115">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="ec8a6-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec8a6-116">Metody rozbočovače streamování `IAsyncEnumerable<T>` se můžou vracet kromě `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="ec8a6-117">Nejjednodušší způsob, jak se vrátit, `IAsyncEnumerable<T>` je vytvoření metody asynchronního iterátoru metodou, jak ukazuje následující příklad.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="ec8a6-118">Metody asynchronního iterátoru centra můžou přijmout `CancellationToken` parametr, který se aktivuje, když se klient odhlásí z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="ec8a6-119">Metody asynchronního iterátoru zabraňují problémům běžným kanálům, jako je například, že nevrátí `ChannelReader` včas dostatek nebo ukončí metodu bez dokončení <xref:System.Threading.Channels.ChannelWriter`1> .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="ec8a6-120">Následující příklad znázorňuje základy streamování dat klientovi pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="ec8a6-121">Pokaždé, když je objekt zapisován do <xref:System.Threading.Channels.ChannelWriter%601> , je objekt okamžitě odeslán klientovi.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="ec8a6-122">Na konci `ChannelWriter` je dokončeno, aby klient oznámil, že je datový proud uzavřen.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="ec8a6-123">Zapište do `ChannelWriter<T>` vlákna na pozadí a vraťte co nejrychleji `ChannelReader` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="ec8a6-124">Další volání centra jsou blokovaná `ChannelReader` , dokud se nevrátí.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="ec8a6-125">Zabalte logiku do `try ... catch` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="ec8a6-126">Dokončete `Channel` v a mimo a ujistěte se, že `catch` `catch` je volání metody rozbočovače správně dokončeno.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="ec8a6-127">Metody služby streamování na straně serveru můžou přijmout `CancellationToken` parametr, který se aktivuje, když se klient odhlásí z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="ec8a6-128">Pomocí tohoto tokenu zastavte operaci serveru a uvolněte všechny prostředky, pokud se klient odpojí před koncem datového proudu.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="ec8a6-129">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="ec8a6-129">Client-to-server streaming</span></span>

<span data-ttu-id="ec8a6-130">Metoda centra se automaticky stalá metodou streamování typu klient-server, když přijme jeden nebo více objektů typu <xref:System.Threading.Channels.ChannelReader%601> nebo <xref:System.Collections.Generic.IAsyncEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="ec8a6-131">Následující příklad ukazuje základy čtení dat streamování odeslaných z klienta.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="ec8a6-132">Pokaždé, když klient zapíše do <xref:System.Threading.Channels.ChannelWriter%601> , data jsou zapsána do na `ChannelReader` serveru, ze kterého metoda rozbočovače čte.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="ec8a6-133"><xref:System.Collections.Generic.IAsyncEnumerable%601>Následuje verze metody.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

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

## <a name="net-client"></a><span data-ttu-id="ec8a6-134">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="ec8a6-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="ec8a6-135">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="ec8a6-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec8a6-136">`StreamAsync`Metody a `StreamAsChannelAsync` `HubConnection` jsou používány k vyvolání metod streamování mezi servery.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="ec8a6-137">Předejte název a argumenty metody centra definované v metodě hub do `StreamAsync` nebo `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="ec8a6-138">Obecný parametr v `StreamAsync<T>` a `StreamAsChannelAsync<T>` Určuje typ objektů vrácených metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="ec8a6-139">Objekt typu `IAsyncEnumerable<T>` nebo `ChannelReader<T>` je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="ec8a6-140">`StreamAsync`Příklad, který vrátí `IAsyncEnumerable<int>` :</span><span class="sxs-lookup"><span data-stu-id="ec8a6-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

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

<span data-ttu-id="ec8a6-141">Odpovídající `StreamAsChannelAsync` příklad, který vrátí `ChannelReader<int>` :</span><span class="sxs-lookup"><span data-stu-id="ec8a6-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

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

<span data-ttu-id="ec8a6-142">`StreamAsChannelAsync`Metoda v `HubConnection` je použita k vyvolání metody streamování ze serveru na klienta.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="ec8a6-143">Předat název a argumenty metody centra definované v metodě hub pro `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="ec8a6-144">Obecný parametr v `StreamAsChannelAsync<T>` Určuje typ objektů vrácených metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="ec8a6-145">`ChannelReader<T>`Je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

<span data-ttu-id="ec8a6-146">`StreamAsChannelAsync`Metoda v `HubConnection` je použita k vyvolání metody streamování ze serveru na klienta.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="ec8a6-147">Předat název a argumenty metody centra definované v metodě hub pro `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="ec8a6-148">Obecný parametr v `StreamAsChannelAsync<T>` Určuje typ objektů vrácených metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="ec8a6-149">`ChannelReader<T>`Je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="ec8a6-150">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="ec8a6-150">Client-to-server streaming</span></span>

<span data-ttu-id="ec8a6-151">Existují dva způsoby, jak vyvolat metodu rozbočovače streamování typu klient-server z klienta .NET.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="ec8a6-152">Můžete buď předat `IAsyncEnumerable<T>` `ChannelReader` parametr nebo jako argument do `SendAsync` , `InvokeAsync` nebo `StreamAsChannelAsync` , v závislosti na volané metodě centra.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="ec8a6-153">Pokaždé, když jsou data zapsána do `IAsyncEnumerable` `ChannelWriter` objektu nebo, metoda centra na serveru obdrží novou položku s daty z klienta.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="ec8a6-154">Při použití `IAsyncEnumerable` objektu se datový proud ukončí po ukončení metody, která vrací položky streamu.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

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

<span data-ttu-id="ec8a6-155">Pokud používáte `ChannelWriter` , můžete kanál dokončete pomocí těchto kroků `channel.Writer.Complete()` :</span><span class="sxs-lookup"><span data-stu-id="ec8a6-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="ec8a6-156">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="ec8a6-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="ec8a6-157">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="ec8a6-157">Server-to-client streaming</span></span>

<span data-ttu-id="ec8a6-158">Klienti JavaScriptu volají metody streamování na straně serveru na rozbočovačích s `connection.stream` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="ec8a6-159">`stream`Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="ec8a6-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="ec8a6-160">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="ec8a6-160">The name of the hub method.</span></span> <span data-ttu-id="ec8a6-161">V následujícím příkladu je název metody rozbočovače `Counter` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="ec8a6-162">Argumenty definované v metodě centra</span><span class="sxs-lookup"><span data-stu-id="ec8a6-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="ec8a6-163">V následujícím příkladu jsou argumenty počtem položek datového proudu, které mají být přijímány, a zpoždění mezi položkami datového proudu.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="ec8a6-164">`connection.stream`Vrátí `IStreamResult` , který obsahuje `subscribe` metodu.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="ec8a6-165">Předejte `IStreamSubscriber` `subscribe` a nastavte `next` `error` `complete` zpětná volání, a pro příjem oznámení od `stream` vyvolání.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="ec8a6-166">Chcete-li ukončit datový proud z klienta, zavolejte `dispose` metodu na `ISubscription` , kterou vrátí `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="ec8a6-167">Volání této metody způsobí zrušení `CancellationToken` parametru metody hub, pokud jste ji zadali.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="ec8a6-168">Chcete-li ukončit datový proud z klienta, zavolejte `dispose` metodu na `ISubscription` , kterou vrátí `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="ec8a6-169">Streamování klienta na server</span><span class="sxs-lookup"><span data-stu-id="ec8a6-169">Client-to-server streaming</span></span>

<span data-ttu-id="ec8a6-170">Klienti JavaScriptu volají metody streamování typu klient-server na rozbočovači předáním `Subject` jako argumentu `send` , nebo v `invoke` `stream` závislosti na volané metodě centra.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="ec8a6-171">`Subject`Je třída, která vypadá jako `Subject` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="ec8a6-172">Například v RxJS můžete použít třídu [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) z této knihovny.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="ec8a6-173">Volání `subject.next(item)` s položkou zapisuje položku do datového proudu a metoda hub přijme položku na serveru.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="ec8a6-174">Chcete-li ukončit datový proud, zavolejte `subject.complete()` .</span><span class="sxs-lookup"><span data-stu-id="ec8a6-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="ec8a6-175">Klient Java</span><span class="sxs-lookup"><span data-stu-id="ec8a6-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="ec8a6-176">Streamování ze serveru na klienta</span><span class="sxs-lookup"><span data-stu-id="ec8a6-176">Server-to-client streaming</span></span>

<span data-ttu-id="ec8a6-177">SignalRKlient Java používá `stream` metodu k vyvolání metod streamování.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="ec8a6-178">`stream`přijímá tři nebo více argumentů:</span><span class="sxs-lookup"><span data-stu-id="ec8a6-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="ec8a6-179">Očekávaný typ položek datového proudu.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="ec8a6-180">Název metody centra</span><span class="sxs-lookup"><span data-stu-id="ec8a6-180">The name of the hub method.</span></span>
* <span data-ttu-id="ec8a6-181">Argumenty definované v metodě centra</span><span class="sxs-lookup"><span data-stu-id="ec8a6-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="ec8a6-182">`stream`Metoda on `HubConnection` vrátí pozorovatelný typ položky streamu.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="ec8a6-183">Metoda Pozorovatelho typu `subscribe` je místo, kde `onNext` `onError` `onCompleted` jsou definovány obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="ec8a6-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ec8a6-184">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ec8a6-184">Additional resources</span></span>

* [<span data-ttu-id="ec8a6-185">Centra</span><span class="sxs-lookup"><span data-stu-id="ec8a6-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ec8a6-186">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="ec8a6-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ec8a6-187">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="ec8a6-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="ec8a6-188">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="ec8a6-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

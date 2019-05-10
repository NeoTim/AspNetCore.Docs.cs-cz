---
title: Použití datových proudů v knihovně SignalR technologie ASP.NET Core
author: bradygaster
description: Naučíte se Streamovat data mezi klientem a serverem.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2019
uid: signalr/streaming
ms.openlocfilehash: 8f39fdfa45766b5bbec572970f009abefefdc419
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902949"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="14924-103">Použití datových proudů v knihovně SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14924-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="14924-104">Podle [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="14924-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="14924-105">Funkce SignalR technologie ASP.NET Core podporuje datové proudy z klienta na server a ze serveru do klienta.</span><span class="sxs-lookup"><span data-stu-id="14924-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="14924-106">To je užitečné pro scénáře, kde fragmenty data dorazí v čase.</span><span class="sxs-lookup"><span data-stu-id="14924-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="14924-107">Při vysílání datových proudů, každý fragment posílá klienta nebo serveru hned, jakmile bude k dispozici, nikoli čeká se na všech dat k dispozici.</span><span class="sxs-lookup"><span data-stu-id="14924-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14924-108">Funkce SignalR technologie ASP.NET Core podporuje streamování návratové hodnoty metod serveru.</span><span class="sxs-lookup"><span data-stu-id="14924-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="14924-109">To je užitečné pro scénáře, kde fragmenty data dorazí v čase.</span><span class="sxs-lookup"><span data-stu-id="14924-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="14924-110">Pokud vrácená hodnota se streamuje klientovi, každý fragment je odeslat klientovi, jakmile bude k dispozici, nikoli čeká všechna data k dispozici.</span><span class="sxs-lookup"><span data-stu-id="14924-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="14924-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14924-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="14924-112">Nastavení centra pro streamování</span><span class="sxs-lookup"><span data-stu-id="14924-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="14924-113">Metodu rozbočovače na automaticky stane streamování metody rozbočovače, je po návratu <xref:System.Threading.Channels.ChannelReader%601>, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, nebo `Task<IAsyncEnumerable<T>>`.</span><span class="sxs-lookup"><span data-stu-id="14924-113">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601>, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, or `Task<IAsyncEnumerable<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14924-114">Metodu rozbočovače na automaticky stane streamování metody rozbočovače, je po návratu <xref:System.Threading.Channels.ChannelReader%601> nebo `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="14924-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="14924-115">Server klient streamování</span><span class="sxs-lookup"><span data-stu-id="14924-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="14924-116">Streamování metod rozbočovače na může vrátit `IAsyncEnumerable<T>` kromě `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="14924-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="14924-117">Nejjednodušší způsob, jak vrátit `IAsyncEnumerable<T>` je tím, že metodu rozbočovače na asynchronní metodu iterátoru, jak ukazuje následující příklad.</span><span class="sxs-lookup"><span data-stu-id="14924-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="14924-118">Můžete přijmout metod rozbočovače na asynchronní iterátor `CancellationToken` parametr, který se aktivuje, když klient zruší z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="14924-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="14924-119">Asynchronní metody iterátoru Předcházejte problémům běžných kanálů, jako je například nevrací `ChannelReader` dostatečně včas nebo standardním ukončením metody bez dokončení <xref:System.Threading.Channels.ChannelWriter`1>.</span><span class="sxs-lookup"><span data-stu-id="14924-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="14924-120">Následující příklad ukazuje základy streamovaných dat na klientovi pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="14924-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="14924-121">Vždy, když je objekt zapsána do <xref:System.Threading.Channels.ChannelWriter%601>, objekt je okamžitě se odešlou do klienta.</span><span class="sxs-lookup"><span data-stu-id="14924-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="14924-122">Na konci `ChannelWriter` je dokončit, aby se dali pokyn klientovi, datový proud je uzavřen.</span><span class="sxs-lookup"><span data-stu-id="14924-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="14924-123">Zápis do `ChannelWriter<T>` na vlákně na pozadí a vraťte se `ChannelReader` co nejdříve.</span><span class="sxs-lookup"><span data-stu-id="14924-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="14924-124">Další volání rozbočovače je zablokovaná do `ChannelReader` je vrácena.</span><span class="sxs-lookup"><span data-stu-id="14924-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="14924-125">Zabalení logiku `try ... catch`.</span><span class="sxs-lookup"><span data-stu-id="14924-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="14924-126">Dokončení `Channel` v `catch` i mimo ni `catch` zajistit centra volání metody dokončila správně.</span><span class="sxs-lookup"><span data-stu-id="14924-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="14924-127">Streamování metod rozbočovače na serveru do klienta může přijmout `CancellationToken` parametr, který se aktivuje, když klient zruší z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="14924-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="14924-128">Pomocí tohoto tokenu operace serveru zastavit a uvolnit všechny prostředky, pokud se klient odpojí do konce datového proudu.</span><span class="sxs-lookup"><span data-stu-id="14924-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="14924-129">Klient server streamování</span><span class="sxs-lookup"><span data-stu-id="14924-129">Client-to-server streaming</span></span>

<span data-ttu-id="14924-130">Metody rozbočovače automaticky změní streamování metody rozbočovače klienta se serverem, pokud přijímá jeden nebo více <xref:System.Threading.Channels.ChannelReader`1>s.</span><span class="sxs-lookup"><span data-stu-id="14924-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more <xref:System.Threading.Channels.ChannelReader`1>s.</span></span> <span data-ttu-id="14924-131">Následující příklad ukazuje základní informace o čtení datových proudů dat odeslaných z klienta.</span><span class="sxs-lookup"><span data-stu-id="14924-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="14924-132">Vždy, když klient zapíše do <xref:System.Threading.Channels.ChannelWriter`1>, data jsou zapsána do `ChannelReader` na serveru, který čte z metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="14924-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter`1>, the data is written into the `ChannelReader` on the server that the hub method is reading from.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="14924-133">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="14924-133">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="14924-134">Server klient streamování</span><span class="sxs-lookup"><span data-stu-id="14924-134">Server-to-client streaming</span></span>

<span data-ttu-id="14924-135">`StreamAsChannelAsync` Metodu na `HubConnection` se používá k volání metody streaming klient a server.</span><span class="sxs-lookup"><span data-stu-id="14924-135">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="14924-136">Předat název metody rozbočovače a argumenty, které jsou definovány v metody rozbočovače na `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="14924-136">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="14924-137">Obecný parametr na `StreamAsChannelAsync<T>` Určuje typ objektu vrácený metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="14924-137">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="14924-138">A `ChannelReader<T>` vrácená z volání datového proudu a představuje datový proud na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="14924-138">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

::: moniker range=">= aspnetcore-2.2"

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="14924-139">Klient server streamování</span><span class="sxs-lookup"><span data-stu-id="14924-139">Client-to-server streaming</span></span>

<span data-ttu-id="14924-140">Vyvolání metody streaming centra klient server z klienta .NET, vytvořte `Channel` a předat `ChannelReader` jako argument `SendAsync`, `InvokeAsync`, nebo `StreamAsChannelAsync`, v závislosti na vyvolání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="14924-140">To invoke a client-to-server streaming hub method from the .NET client, create a `Channel` and pass the `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="14924-141">Vždy, když data se zapisují do `ChannelWriter`, metody rozbočovače na serveru obdrží novou položku s daty z klienta.</span><span class="sxs-lookup"><span data-stu-id="14924-141">Whenever data is written to the `ChannelWriter`, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="14924-142">Do konce datového proudu, dokončete kanál s `channel.Writer.Complete()`.</span><span class="sxs-lookup"><span data-stu-id="14924-142">To end the stream, complete the channel with `channel.Writer.Complete()`.</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="14924-143">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="14924-143">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="14924-144">Server klient streamování</span><span class="sxs-lookup"><span data-stu-id="14924-144">Server-to-client streaming</span></span>

<span data-ttu-id="14924-145">Klientů JavaScript volat metody klient a server streamování na hubs s využitím `connection.stream`.</span><span class="sxs-lookup"><span data-stu-id="14924-145">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="14924-146">`stream` Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="14924-146">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="14924-147">Název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="14924-147">The name of the hub method.</span></span> <span data-ttu-id="14924-148">V následujícím příkladu je název metody rozbočovače `Counter`.</span><span class="sxs-lookup"><span data-stu-id="14924-148">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="14924-149">Argumenty podle metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="14924-149">Arguments defined in the hub method.</span></span> <span data-ttu-id="14924-150">V následujícím příkladu jsou argumenty počet pro počet položek datového proudu pro příjem a zpoždění mezi položkami datového proudu.</span><span class="sxs-lookup"><span data-stu-id="14924-150">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="14924-151">`connection.stream` Vrátí `IStreamResult`, který obsahuje `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="14924-151">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="14924-152">Předejte `IStreamSubscriber` k `subscribe` a nastavit `next`, `error`, a `complete` zpětná volání k přijímání oznámení z `stream` vyvolání.</span><span class="sxs-lookup"><span data-stu-id="14924-152">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="14924-153">Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="14924-153">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="14924-154">Voláním této metody způsobí zrušení `CancellationToken` parametru metody rozbočovače, pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="14924-154">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="14924-155">Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="14924-155">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="14924-156">Klient server streamování</span><span class="sxs-lookup"><span data-stu-id="14924-156">Client-to-server streaming</span></span>

<span data-ttu-id="14924-157">Klientům JavaScript volat metody streamování klient server v centrech předáním `Subject` jako argument `send`, `invoke`, nebo `stream`, v závislosti na vyvolání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="14924-157">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="14924-158">`Subject` Je třída, která vypadá jako `Subject`.</span><span class="sxs-lookup"><span data-stu-id="14924-158">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="14924-159">Například v RxJS, můžete použít [subjektu](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) třídy z této knihovny.</span><span class="sxs-lookup"><span data-stu-id="14924-159">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="14924-160">Volání `subject.next(item)` s položku zapíše položku do datového proudu a metody rozbočovače přijímá položka na serveru.</span><span class="sxs-lookup"><span data-stu-id="14924-160">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="14924-161">Do konce datového proudu, volání `subject.complete()`.</span><span class="sxs-lookup"><span data-stu-id="14924-161">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="14924-162">Klient Java</span><span class="sxs-lookup"><span data-stu-id="14924-162">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="14924-163">Server klient streamování</span><span class="sxs-lookup"><span data-stu-id="14924-163">Server-to-client streaming</span></span>

<span data-ttu-id="14924-164">Používá klientskou sadou SignalR Java `stream` metoda k vyvolání metody streaming.</span><span class="sxs-lookup"><span data-stu-id="14924-164">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="14924-165">`stream` přijímá tři nebo více argumentů:</span><span class="sxs-lookup"><span data-stu-id="14924-165">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="14924-166">Očekávaný typ položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="14924-166">The expected type of the stream items.</span></span>
* <span data-ttu-id="14924-167">Název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="14924-167">The name of the hub method.</span></span>
* <span data-ttu-id="14924-168">Argumenty podle metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="14924-168">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="14924-169">`stream` Metodu na `HubConnection` vrátí existuje zjištěný typ položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="14924-169">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="14924-170">Pozorovatelné typ `subscribe` metoda je tam, kde `onNext`, `onError` a `onCompleted` obslužné rutiny jsou definovány.</span><span class="sxs-lookup"><span data-stu-id="14924-170">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="14924-171">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="14924-171">Additional resources</span></span>

* [<span data-ttu-id="14924-172">Centra</span><span class="sxs-lookup"><span data-stu-id="14924-172">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="14924-173">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="14924-173">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="14924-174">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="14924-174">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="14924-175">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="14924-175">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

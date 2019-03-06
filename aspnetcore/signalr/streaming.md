---
title: Použití datových proudů v knihovně SignalR technologie ASP.NET Core
author: bradygaster
description: Zjistěte, jak vrátit datové proudy z hodnot z metod rozbočovače na serveru a datové proudy používání klientů .NET a JavaScript.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/streaming
ms.openlocfilehash: fb7183f7189d62c181f69ffdb170e3da25612919
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345584"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="f043a-103">Použití datových proudů v knihovně SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f043a-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="f043a-104">Podle [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="f043a-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="f043a-105">Funkce SignalR technologie ASP.NET Core podporuje streamování návratové hodnoty metod serveru.</span><span class="sxs-lookup"><span data-stu-id="f043a-105">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="f043a-106">To je užitečné pro scénáře, kde budou přicházet fragmenty dat průběhu času.</span><span class="sxs-lookup"><span data-stu-id="f043a-106">This is useful for scenarios where fragments of data will come in over time.</span></span> <span data-ttu-id="f043a-107">Pokud vrácená hodnota se streamuje klientovi, každý fragment je odeslat klientovi, jakmile bude k dispozici, nikoli čeká všechna data k dispozici.</span><span class="sxs-lookup"><span data-stu-id="f043a-107">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

<span data-ttu-id="f043a-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f043a-108">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-the-hub"></a><span data-ttu-id="f043a-109">Nastavení centra</span><span class="sxs-lookup"><span data-stu-id="f043a-109">Set up the hub</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f043a-110">Metodu rozbočovače na automaticky stane streamování metody rozbočovače, je po návratu `ChannelReader<T>`, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, nebo `Task<IAsyncEnumerable<T>>`.</span><span class="sxs-lookup"><span data-stu-id="f043a-110">A hub method automatically becomes a streaming hub method when it returns a `ChannelReader<T>`, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, or `Task<IAsyncEnumerable<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f043a-111">Metodu rozbočovače na automaticky stane streamování metody rozbočovače, je po návratu `ChannelReader<T>` nebo `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="f043a-111">A hub method automatically becomes a streaming hub method when it returns a `ChannelReader<T>` or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f043a-112">V ASP.NET Core 3.0 nebo novější, může vrátit streamování metod rozbočovače na `IAsyncEnumerable<T>` kromě `ChannelReader<T>`.</span><span class="sxs-lookup"><span data-stu-id="f043a-112">In ASP.NET Core 3.0 or later, streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="f043a-113">Nejjednodušší způsob, jak vrátit `IAsyncEnumerable<T>` je tím, že metodu rozbočovače na asynchronní metodu iterátoru, jak ukazuje následující příklad.</span><span class="sxs-lookup"><span data-stu-id="f043a-113">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="f043a-114">Můžete přijmout metod rozbočovače na asynchronní iterátor `CancellationToken` parametr, který se aktivuje, když klient zruší z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="f043a-114">Hub async iterator methods can accept a `CancellationToken` parameter that will be triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="f043a-115">Asynchronní metody iterátoru snadno Předcházejte problémům běžných kanálů například nevrací `ChannelReader` dostatečně včas nebo standardním ukončením metody bez dokončení `ChannelWriter`.</span><span class="sxs-lookup"><span data-stu-id="f043a-115">Async iterator methods easily avoid problems common with Channels such as not returning the `ChannelReader` early enough or exiting the method without completing the `ChannelWriter`.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/sample/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="f043a-116">Následující příklad ukazuje základy streamovaných dat na klientovi pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="f043a-116">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="f043a-117">Vždy, když je objekt zapsána do `ChannelWriter` tento objekt se okamžitě se odešlou do klienta.</span><span class="sxs-lookup"><span data-stu-id="f043a-117">Whenever an object is written to the `ChannelWriter` that object is immediately sent to the client.</span></span> <span data-ttu-id="f043a-118">Na konci `ChannelWriter` je dokončit, aby se dali pokyn klientovi, datový proud je uzavřen.</span><span class="sxs-lookup"><span data-stu-id="f043a-118">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> * <span data-ttu-id="f043a-119">Zápis do `ChannelWriter` na vlákně na pozadí a vraťte se `ChannelReader` co nejdříve.</span><span class="sxs-lookup"><span data-stu-id="f043a-119">Write to the `ChannelWriter` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="f043a-120">Další volání rozbočovače se zablokuje, dokud `ChannelReader` je vrácena.</span><span class="sxs-lookup"><span data-stu-id="f043a-120">Other hub invocations will be blocked until a `ChannelReader` is returned.</span></span>
> * <span data-ttu-id="f043a-121">Zabalení svoji logiku v `try ... catch` a proveďte `Channel` v catch a mimo catch, aby se zajistilo centra volání metody se dokončila správně.</span><span class="sxs-lookup"><span data-stu-id="f043a-121">Wrap your logic in a `try ... catch` and complete the `Channel` in the catch and outside the catch to make sure the hub method invocation is completed properly.</span></span>

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.aspnetcore21.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.cs?name=snippet1)]

<span data-ttu-id="f043a-122">V ASP.NET Core 2.2 nebo vyšší, můžete přijmout streamování metod rozbočovače `CancellationToken` parametr, který se aktivuje, když klient zruší z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="f043a-122">In ASP.NET Core 2.2 or later, streaming hub methods can accept a `CancellationToken` parameter that will be triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="f043a-123">Pomocí tohoto tokenu operace serveru zastavit a uvolnit všechny prostředky, pokud se klient odpojí do konce datového proudu.</span><span class="sxs-lookup"><span data-stu-id="f043a-123">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="f043a-124">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="f043a-124">.NET client</span></span>

<span data-ttu-id="f043a-125">`StreamAsChannelAsync` Metodu na `HubConnection` se používá k volání metody streaming.</span><span class="sxs-lookup"><span data-stu-id="f043a-125">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a streaming method.</span></span> <span data-ttu-id="f043a-126">Předat název metody rozbočovače a argumenty, které jsou definovány v metody rozbočovače na `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="f043a-126">Pass the hub method name, and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="f043a-127">Obecný parametr na `StreamAsChannelAsync<T>` Určuje typ objektu vrácený metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="f043a-127">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="f043a-128">A `ChannelReader<T>` vrácená z volání služby stream a představuje datový proud na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="f043a-128">A `ChannelReader<T>` is returned from the stream invocation, and represents the stream on the client.</span></span> <span data-ttu-id="f043a-129">Čtení dat, běžně používá k vytvoření smyčky přes `WaitToReadAsync` a volat `TryRead` kdy data jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="f043a-129">To read data, a common pattern is to loop over `WaitToReadAsync` and call `TryRead` when data is available.</span></span> <span data-ttu-id="f043a-130">Smyčky se ukončí, pokud datový proud bylo ukončeno serverem nebo předat token zrušení `StreamAsChannelAsync` se zruší.</span><span class="sxs-lookup"><span data-stu-id="f043a-130">The loop will end when the stream has been closed by the server, or the cancellation token passed to `StreamAsChannelAsync` is canceled.</span></span>

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

## <a name="javascript-client"></a><span data-ttu-id="f043a-131">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="f043a-131">JavaScript client</span></span>

<span data-ttu-id="f043a-132">Klientů JavaScript pomocí volání metody streaming v centrech `connection.stream`.</span><span class="sxs-lookup"><span data-stu-id="f043a-132">JavaScript clients call streaming methods on hubs by using `connection.stream`.</span></span> <span data-ttu-id="f043a-133">`stream` Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="f043a-133">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="f043a-134">Název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f043a-134">The name of the hub method.</span></span> <span data-ttu-id="f043a-135">V následujícím příkladu je název metody rozbočovače `Counter`.</span><span class="sxs-lookup"><span data-stu-id="f043a-135">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="f043a-136">Argumenty podle metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f043a-136">Arguments defined in the hub method.</span></span> <span data-ttu-id="f043a-137">V následujícím příkladu jsou argumenty: počet pro počet položek datového proudu pro příjem a zpoždění mezi položkami datového proudu.</span><span class="sxs-lookup"><span data-stu-id="f043a-137">In the following example, the arguments are: a count for the number of stream items to receive, and the delay between stream items.</span></span>

<span data-ttu-id="f043a-138">`connection.stream` Vrátí `IStreamResult` obsahující `subscribe` metody.</span><span class="sxs-lookup"><span data-stu-id="f043a-138">`connection.stream` returns an `IStreamResult` which contains a `subscribe` method.</span></span> <span data-ttu-id="f043a-139">Předejte `IStreamSubscriber` k `subscribe` a nastavit `next`, `error`, a `complete` zpětná volání, které chcete dostávat oznámení `stream` vyvolání.</span><span class="sxs-lookup"><span data-stu-id="f043a-139">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to get notifications from the `stream` invocation.</span></span>

[!code-javascript[Streaming javascript](streaming/sample/wwwroot/js/stream.js?range=19-36)]

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="f043a-140">Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="f043a-140">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f043a-141">Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="f043a-141">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span> <span data-ttu-id="f043a-142">Volání této metody způsobí, `CancellationToken` parametru metody rozbočovače (Pokud jste zadali jednu) budou zrušeny.</span><span class="sxs-lookup"><span data-stu-id="f043a-142">Calling this method will cause the `CancellationToken` parameter of the Hub method (if you provided one) to be canceled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
## <a name="java-client"></a><span data-ttu-id="f043a-143">Klient Java</span><span class="sxs-lookup"><span data-stu-id="f043a-143">Java client</span></span>
<span data-ttu-id="f043a-144">Používá klientskou sadou SignalR Java `stream` metoda k vyvolání metody streaming.</span><span class="sxs-lookup"><span data-stu-id="f043a-144">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="f043a-145">Přijímá tři nebo více argumentů:</span><span class="sxs-lookup"><span data-stu-id="f043a-145">It accepts three or more arguments:</span></span>

* <span data-ttu-id="f043a-146">Očekávaný typ datového proudu položky</span><span class="sxs-lookup"><span data-stu-id="f043a-146">The expected type of the stream items</span></span> 
* <span data-ttu-id="f043a-147">Název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f043a-147">The name of the hub method.</span></span>
* <span data-ttu-id="f043a-148">Argumenty podle metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f043a-148">Arguments defined in the hub method.</span></span> 

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```
<span data-ttu-id="f043a-149">`stream` Metodu na `HubConnection` vrátí existuje zjištěný typ položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="f043a-149">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="f043a-150">Pozorovatelné typ `subscribe` metoda je tady můžete definovat vaše `onNext`, `onError` a `onCompleted` obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="f043a-150">The Observable type's `subscribe` method is where you define your `onNext`,  `onError` and  `onCompleted` handlers.</span></span>

::: moniker-end

## <a name="related-resources"></a><span data-ttu-id="f043a-151">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="f043a-151">Related resources</span></span>

* [<span data-ttu-id="f043a-152">Centra</span><span class="sxs-lookup"><span data-stu-id="f043a-152">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f043a-153">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="f043a-153">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="f043a-154">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="f043a-154">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="f043a-155">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="f043a-155">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

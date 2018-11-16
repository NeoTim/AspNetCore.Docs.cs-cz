---
title: Použití datových proudů v knihovně SignalR technologie ASP.NET Core
author: tdykstra
description: ''
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/streaming
ms.openlocfilehash: 6d5f707bd2a37e1999c6e87e3cfc369aa0301207
ms.sourcegitcommit: 09bcda59a58019fdf47b2db5259fe87acf19dd38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51708436"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a><span data-ttu-id="a369d-102">Použití datových proudů v knihovně SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a369d-102">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="a369d-103">Podle [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="a369d-103">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="a369d-104">Funkce SignalR technologie ASP.NET Core podporuje streamování návratové hodnoty metod serveru.</span><span class="sxs-lookup"><span data-stu-id="a369d-104">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="a369d-105">To je užitečné pro scénáře, kde budou přicházet fragmenty dat průběhu času.</span><span class="sxs-lookup"><span data-stu-id="a369d-105">This is useful for scenarios where fragments of data will come in over time.</span></span> <span data-ttu-id="a369d-106">Pokud vrácená hodnota se streamuje klientovi, každý fragment je odeslat klientovi, jakmile bude k dispozici, nikoli čeká všechna data k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a369d-106">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

<span data-ttu-id="a369d-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a369d-107">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-the-hub"></a><span data-ttu-id="a369d-108">Nastavení centra</span><span class="sxs-lookup"><span data-stu-id="a369d-108">Set up the hub</span></span>

<span data-ttu-id="a369d-109">Metodu rozbočovače na automaticky stane streamování metody rozbočovače, je po návratu `ChannelReader<T>` nebo `Task<ChannelReader<T>>`.</span><span class="sxs-lookup"><span data-stu-id="a369d-109">A hub method automatically becomes a streaming hub method when it returns a `ChannelReader<T>` or a `Task<ChannelReader<T>>`.</span></span> <span data-ttu-id="a369d-110">Níže je příklad, který ukazuje základy streamovaných dat na klientovi.</span><span class="sxs-lookup"><span data-stu-id="a369d-110">Below is a sample that shows the basics of streaming data to the client.</span></span> <span data-ttu-id="a369d-111">Vždy, když je objekt zapsána do `ChannelReader` tento objekt se okamžitě se odešlou do klienta.</span><span class="sxs-lookup"><span data-stu-id="a369d-111">Whenever an object is written to the `ChannelReader` that object is immediately sent to the client.</span></span> <span data-ttu-id="a369d-112">Na konci `ChannelReader` je dokončit, aby se dali pokyn klientovi, datový proud je uzavřen.</span><span class="sxs-lookup"><span data-stu-id="a369d-112">At the end, the `ChannelReader` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="a369d-113">Zápis do `ChannelReader` na vlákně na pozadí a vraťte se `ChannelReader` co nejdříve.</span><span class="sxs-lookup"><span data-stu-id="a369d-113">Write to the `ChannelReader` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="a369d-114">Další volání rozbočovače se zablokuje, dokud `ChannelReader` je vrácena.</span><span class="sxs-lookup"><span data-stu-id="a369d-114">Other hub invocations will be blocked until a `ChannelReader` is returned.</span></span>

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.aspnetcore21.cs?range=12-36)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.cs?range=11-35)]

> [!NOTE]
> <span data-ttu-id="a369d-115">V ASP.NET Core 2.2 nebo vyšší, můžete přijmout streamování metod rozbočovače `CancellationToken` parametr, který se aktivuje, když klient zruší z datového proudu.</span><span class="sxs-lookup"><span data-stu-id="a369d-115">In ASP.NET Core 2.2 or later, streaming Hub methods can accept a `CancellationToken` parameter that will be triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="a369d-116">Pomocí tohoto tokenu operace serveru zastavit a uvolnit všechny prostředky, pokud se klient odpojí do konce datového proudu.</span><span class="sxs-lookup"><span data-stu-id="a369d-116">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="a369d-117">.NET client</span><span class="sxs-lookup"><span data-stu-id="a369d-117">.NET client</span></span>

<span data-ttu-id="a369d-118">`StreamAsChannelAsync` Metodu na `HubConnection` se používá k volání metody streaming.</span><span class="sxs-lookup"><span data-stu-id="a369d-118">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a streaming method.</span></span> <span data-ttu-id="a369d-119">Předat název metody rozbočovače a argumenty, které jsou definovány v metody rozbočovače na `StreamAsChannelAsync`.</span><span class="sxs-lookup"><span data-stu-id="a369d-119">Pass the hub method name, and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="a369d-120">Obecný parametr na `StreamAsChannelAsync<T>` Určuje typ objektu vrácený metodou streamování.</span><span class="sxs-lookup"><span data-stu-id="a369d-120">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="a369d-121">A `ChannelReader<T>` vrácená z volání služby stream a představuje datový proud na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a369d-121">A `ChannelReader<T>` is returned from the stream invocation, and represents the stream on the client.</span></span> <span data-ttu-id="a369d-122">Čtení dat, běžně používá k vytvoření smyčky přes `WaitToReadAsync` a volat `TryRead` kdy data jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a369d-122">To read data, a common pattern is to loop over `WaitToReadAsync` and call `TryRead` when data is available.</span></span> <span data-ttu-id="a369d-123">Smyčky se ukončí, pokud datový proud bylo ukončeno serverem nebo předat token zrušení `StreamAsChannelAsync` se zruší.</span><span class="sxs-lookup"><span data-stu-id="a369d-123">The loop will end when the stream has been closed by the server, or the cancellation token passed to `StreamAsChannelAsync` is canceled.</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to 
// the server, which will trigger the corresponding token in the Hub method.
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

## <a name="javascript-client"></a><span data-ttu-id="a369d-124">Javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="a369d-124">JavaScript client</span></span>

<span data-ttu-id="a369d-125">Klientů JavaScript pomocí volání metody streaming v centrech `connection.stream`.</span><span class="sxs-lookup"><span data-stu-id="a369d-125">JavaScript clients call streaming methods on hubs by using `connection.stream`.</span></span> <span data-ttu-id="a369d-126">`stream` Metoda přijímá dva argumenty:</span><span class="sxs-lookup"><span data-stu-id="a369d-126">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="a369d-127">Název metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a369d-127">The name of the hub method.</span></span> <span data-ttu-id="a369d-128">V následujícím příkladu je název metody rozbočovače `Counter`.</span><span class="sxs-lookup"><span data-stu-id="a369d-128">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="a369d-129">Argumenty podle metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a369d-129">Arguments defined in the hub method.</span></span> <span data-ttu-id="a369d-130">V následujícím příkladu jsou argumenty: počet pro počet položek datového proudu pro příjem a zpoždění mezi položkami datového proudu.</span><span class="sxs-lookup"><span data-stu-id="a369d-130">In the following example, the arguments are: a count for the number of stream items to receive, and the delay between stream items.</span></span>

<span data-ttu-id="a369d-131">`connection.stream` Vrátí `IStreamResult` obsahující `subscribe` metody.</span><span class="sxs-lookup"><span data-stu-id="a369d-131">`connection.stream` returns an `IStreamResult` which contains a `subscribe` method.</span></span> <span data-ttu-id="a369d-132">Předejte `IStreamSubscriber` k `subscribe` a nastavit `next`, `error`, a `complete` zpětná volání, které chcete dostávat oznámení `stream` vyvolání.</span><span class="sxs-lookup"><span data-stu-id="a369d-132">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to get notifications from the `stream` invocation.</span></span>

[!code-javascript[Streaming javascript](streaming/sample/wwwroot/js/stream.js?range=19-36)]

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="a369d-133">Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="a369d-133">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a369d-134">Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda.</span><span class="sxs-lookup"><span data-stu-id="a369d-134">To end the stream from the client, call the `dispose` method on the `ISubscription` that is returned from the `subscribe` method.</span></span> <span data-ttu-id="a369d-135">Volání této metody způsobí, `CancellationToken` parametru metody rozbočovače (Pokud jste zadali jednu) budou zrušeny.</span><span class="sxs-lookup"><span data-stu-id="a369d-135">Calling this method will cause the `CancellationToken` parameter of the Hub method (if you provided one) to be canceled.</span></span>

::: moniker-end

## <a name="related-resources"></a><span data-ttu-id="a369d-136">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="a369d-136">Related resources</span></span>

* [<span data-ttu-id="a369d-137">Centra</span><span class="sxs-lookup"><span data-stu-id="a369d-137">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a369d-138">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="a369d-138">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="a369d-139">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="a369d-139">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="a369d-140">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="a369d-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)

---
title: Použití streamování v ASP.NET Core SignalR
author: bradygaster
description: Naučte se, jak streamovat data mezi klientem a serverem.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/streaming
ms.openlocfilehash: 21dd8180fe168f81ed68b01f02b81a6264d6e5a6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667725"
---
# <a name="use-streaming-in-aspnet-core-opno-locsignalr"></a>Použití streamování v ASP.NET Core SignalR

Od [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR podporuje streamování z klienta na server a ze serveru na klienta. To je užitečné v situacích, kdy fragmenty dat přicházejí v průběhu času. Při streamování se každý fragment pošle klientovi nebo serveru hned, jakmile bude k dispozici, a ne tím, že čeká na zpřístupnění všech dat.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR podporuje streamování vrácených hodnot metod serveru. To je užitečné v situacích, kdy fragmenty dat přicházejí v průběhu času. Když je vrácená hodnota streamovaná klientovi, každý fragment se pošle klientovi, jakmile bude k dispozici, místo čekání na zpřístupnění všech dat.

::: moniker-end

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Nastavení centra pro streamování

::: moniker range=">= aspnetcore-3.0"

Metoda rozbočovače se automaticky aktivuje metodou streamování, když vrátí <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`nebo `Task<ChannelReader<T>>`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Metoda rozbočovače se automaticky aktivuje metodou streamování, když vrátí <xref:System.Threading.Channels.ChannelReader%601> nebo `Task<ChannelReader<T>>`.

::: moniker-end

### <a name="server-to-client-streaming"></a>Streamování ze serveru na klienta

::: moniker range=">= aspnetcore-3.0"

Metody rozbočovače streamování mohou vracet `IAsyncEnumerable<T>` kromě `ChannelReader<T>`. Nejjednodušší způsob, jak vrátit `IAsyncEnumerable<T>`, je provedení asynchronní metody iterátoru metodou, jak ukazuje následující příklad. Metody asynchronního iterátoru centra můžou přijmout parametr `CancellationToken`, který se aktivuje, když se klient odhlásí z datového proudu. Metody asynchronního iterátoru zabraňují problémům běžným kanálům, jako je například nevrácení `ChannelReader` včas nebo ukončení metody bez dokončení <xref:System.Threading.Channels.ChannelWriter`1>.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

Následující příklad znázorňuje základy streamování dat klientovi pomocí kanálů. Pokaždé, když se do <xref:System.Threading.Channels.ChannelWriter%601>zapíše objekt, objekt se okamžitě pošle klientovi. Na konci se `ChannelWriter` dokončí, aby klient oznámil, že je datový proud zavřený.

> [!NOTE]
> Zapište do `ChannelWriter<T>` ve vlákně na pozadí a vraťte `ChannelReader` co nejrychleji. Další volání centra jsou zablokovaná, dokud se nevrátí `ChannelReader`.
>
> Zabalit logiku do `try ... catch`. Dokončete `Channel` v `catch` a mimo `catch`, abyste se ujistili, že je volání metody rozbočovače správně dokončeno.

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

Metody služby streamování na straně serveru můžou přijmout parametr `CancellationToken`, který se aktivuje, když se klient odhlásí z datového proudu. Pomocí tohoto tokenu zastavte operaci serveru a uvolněte všechny prostředky, pokud se klient odpojí před koncem datového proudu.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streamování klienta na server

Metoda centra se automaticky stalá metodou streamování typu klient-server, když přijme jeden nebo více objektů typu <xref:System.Threading.Channels.ChannelReader%601> nebo <xref:System.Collections.Generic.IAsyncEnumerable%601>. Následující příklad ukazuje základy čtení dat streamování odeslaných z klienta. Pokaždé, když klient zapíše do <xref:System.Threading.Channels.ChannelWriter%601>, data se zapisují do `ChannelReader` na serveru, ze kterého metoda rozbočovače čte.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

Následuje <xref:System.Collections.Generic.IAsyncEnumerable%601> verze metody.

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

## <a name="net-client"></a>Klient .NET

### <a name="server-to-client-streaming"></a>Streamování ze serveru na klienta


::: moniker range=">= aspnetcore-3.0"

Metody `StreamAsync` a `StreamAsChannelAsync` na `HubConnection` slouží k vyvolání metod streamování mezi servery. Předejte název a argumenty metody centra definované v metodě hub pro `StreamAsync` nebo `StreamAsChannelAsync`. Obecný parametr v `StreamAsync<T>` a `StreamAsChannelAsync<T>` určuje typ objektů vrácených metodou streamování. Objekt typu `IAsyncEnumerable<T>` nebo `ChannelReader<T>` je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.

`StreamAsync` příklad, který vrací `IAsyncEnumerable<int>`:

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

Odpovídající `StreamAsChannelAsync` příklad, který vrací `ChannelReader<int>`:

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

Metoda `StreamAsChannelAsync` v `HubConnection` se používá k vyvolání metody streamování typu server-klient. Předejte název a argumenty metody centra definované v metodě hub, aby bylo možné `StreamAsChannelAsync`. Obecný parametr v `StreamAsChannelAsync<T>` určuje typ objektů vrácených metodou streamování. Z volání streamu se vrátí `ChannelReader<T>` a představuje datový proud na klientovi.

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

Metoda `StreamAsChannelAsync` v `HubConnection` se používá k vyvolání metody streamování typu server-klient. Předejte název a argumenty metody centra definované v metodě hub, aby bylo možné `StreamAsChannelAsync`. Obecný parametr v `StreamAsChannelAsync<T>` určuje typ objektů vrácených metodou streamování. Z volání streamu se vrátí `ChannelReader<T>` a představuje datový proud na klientovi.

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

### <a name="client-to-server-streaming"></a>Streamování klienta na server

Existují dva způsoby, jak vyvolat metodu rozbočovače streamování typu klient-server z klienta .NET. V závislosti na volané metodě centra můžete buď předat `IAsyncEnumerable<T>` nebo `ChannelReader` jako argument `SendAsync`, `InvokeAsync`nebo `StreamAsChannelAsync`.

Pokaždé, když se data zapisují do objektu `IAsyncEnumerable` nebo `ChannelWriter`, metoda centra na serveru přijme novou položku s daty z klienta.

Při použití objektu `IAsyncEnumerable` končí datový proud po ukončení metody, která vrací položky streamu.

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

Nebo pokud používáte `ChannelWriter`, dokončete kanál s `channel.Writer.Complete()`:

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>Klient JavaScriptu

### <a name="server-to-client-streaming"></a>Streamování ze serveru na klienta

Klienti JavaScriptu volají metody streamování na straně serveru na rozbočovačích s `connection.stream`. Metoda `stream` přijímá dva argumenty:

* Název metody rozbočovače. V následujícím příkladu je název metody centra `Counter`.
* Argumenty definované v metodě centra V následujícím příkladu jsou argumenty počtem položek datového proudu, které mají být přijímány, a zpoždění mezi položkami datového proudu.

`connection.stream` vrátí `IStreamResult`, který obsahuje metodu `subscribe`. Předejte `IStreamSubscriber` `subscribe` a nastavte `next`, `error`a `complete` zpětná volání pro příjem oznámení z `stream`ho vyvolání.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Chcete-li ukončit datový proud z klienta, zavolejte metodu `dispose` v `ISubscription`, který je vrácen metodou `subscribe`. Volání této metody způsobí zrušení parametru `CancellationToken` metody hub, pokud jste ji zadali.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Chcete-li ukončit datový proud z klienta, zavolejte metodu `dispose` v `ISubscription`, který je vrácen metodou `subscribe`.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streamování klienta na server

Klienti JavaScriptu volají metody streamování typu klient-server na rozbočovači předáním `Subject` jako argument `send`, `invoke`nebo `stream`v závislosti na volané metodě centra. `Subject` je třída, která vypadá jako `Subject`. Například v RxJS můžete použít třídu [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) z této knihovny.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

Volání `subject.next(item)` s položkou, která zapisuje položku do datového proudu a metoda rozbočovače přijme položku na serveru.

Chcete-li ukončit datový proud, zavolejte `subject.complete()`.

## <a name="java-client"></a>Klient Java

### <a name="server-to-client-streaming"></a>Streamování ze serveru na klienta

SignalR klient Java používá metodu `stream` k vyvolání metod streamování. `stream` přijímá tři nebo více argumentů:

* Očekávaný typ položek datového proudu.
* Název metody rozbočovače.
* Argumenty definované v metodě centra

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

Metoda `stream` v `HubConnection` vrací pozorovatelný typ položky datového proudu. `subscribe` metoda, která je pozorovatelný typ, je, kde jsou definovány obslužné rutiny `onNext`, `onError` a `onCompleted`.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Javascriptový klient](xref:signalr/javascript-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)

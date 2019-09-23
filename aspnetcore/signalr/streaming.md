---
title: Použití streamování v ASP.NET Coreovém signálu
author: bradygaster
description: Naučte se, jak streamovat data mezi klientem a serverem.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/05/2019
uid: signalr/streaming
ms.openlocfilehash: d520c8eec3e777acb9604bdcb5969268deabf8da
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186927"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>Použití streamování v ASP.NET Coreovém signálu

Od [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Signal podporuje streamování z klienta na server a ze serveru na klienta. To je užitečné v situacích, kdy fragmenty dat přicházejí v průběhu času. Při streamování se každý fragment pošle klientovi nebo serveru hned, jakmile bude k dispozici, a ne tím, že čeká na zpřístupnění všech dat.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core Signal podporuje streamování vrácených hodnot metod serveru. To je užitečné v situacích, kdy fragmenty dat přicházejí v průběhu času. Když je vrácená hodnota streamovaná klientovi, každý fragment se pošle klientovi, jakmile bude k dispozici, místo čekání na zpřístupnění všech dat.

::: moniker-end

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Nastavení centra pro streamování

::: moniker range=">= aspnetcore-3.0"

Metoda centra se automaticky stal metodou streamování, když <xref:System.Collections.Generic.IAsyncEnumerable`1>vrací, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`nebo `Task<ChannelReader<T>>`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Metoda centra se automaticky stávají metodou streamování, když vrátí <xref:System.Threading.Channels.ChannelReader%601> `Task<ChannelReader<T>>`nebo.

::: moniker-end

### <a name="server-to-client-streaming"></a>Streamování ze serveru na klienta

::: moniker range=">= aspnetcore-3.0"

Metody rozbočovače streamování `IAsyncEnumerable<T>` se `ChannelReader<T>`můžou vracet kromě. Nejjednodušší způsob, jak se `IAsyncEnumerable<T>` vrátit, je vytvoření metody asynchronního iterátoru metodou, jak ukazuje následující příklad. Metody asynchronního iterátoru centra můžou `CancellationToken` přijmout parametr, který se aktivuje, když se klient odhlásí z datového proudu. Metody asynchronního iterátoru zabraňují problémům běžným kanálům, jako je `ChannelReader` například, že nevrátí včas dostatek nebo ukončí metodu <xref:System.Threading.Channels.ChannelWriter`1>bez dokončení.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

Následující příklad znázorňuje základy streamování dat klientovi pomocí kanálů. Pokaždé <xref:System.Threading.Channels.ChannelWriter%601>, když je objekt zapisován do, je objekt okamžitě odeslán klientovi. Na konci `ChannelWriter` je dokončeno, aby klient oznámil, že je datový proud uzavřen.

> [!NOTE]
> Zapište do `ChannelWriter<T>` vlákna na pozadí a `ChannelReader` vraťte co nejrychleji. Další volání centra jsou blokovaná, dokud `ChannelReader` se nevrátí.
>
> Zabalte logiku do `try ... catch`. Dokončete `Channel` `catch` v a mimo a ujistěte se, že je volání metody rozbočovače správně dokončeno. `catch`

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

Metody služby streamování na straně serveru můžou přijmout `CancellationToken` parametr, který se aktivuje, když se klient odhlásí z datového proudu. Pomocí tohoto tokenu zastavte operaci serveru a uvolněte všechny prostředky, pokud se klient odpojí před koncem datového proudu.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streamování klienta na server

Metoda centra se automaticky stalá metodou streamování typu klient-server, když přijme jeden nebo více objektů typu <xref:System.Threading.Channels.ChannelReader%601> nebo. <xref:System.Collections.Generic.IAsyncEnumerable%601> Následující příklad ukazuje základy čtení dat streamování odeslaných z klienta. Pokaždé <xref:System.Threading.Channels.ChannelWriter%601>, když klient zapíše do, data jsou zapsána `ChannelReader` do na serveru, ze kterého metoda rozbočovače čte.

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

Metody `StreamAsync` a`StreamAsChannelAsync` jsou používány k vyvolání metod streamování mezi servery. `HubConnection` Předejte název a argumenty metody centra definované v metodě hub do `StreamAsync` nebo. `StreamAsChannelAsync` Obecný parametr v `StreamAsync<T>` a `StreamAsChannelAsync<T>` určuje typ objektů vrácených metodou streamování. Objekt typu `IAsyncEnumerable<T>` nebo `ChannelReader<T>` je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.

Příklad, který vrátí `IAsyncEnumerable<int>`: `StreamAsync`

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

Odpovídající `StreamAsChannelAsync` příklad, který vrátí `ChannelReader<int>`:

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

`StreamAsChannelAsync` Metoda v`HubConnection` je použita k vyvolání metody streamování ze serveru na klienta. Předat název a argumenty metody centra definované v metodě hub pro `StreamAsChannelAsync`. Obecný parametr v `StreamAsChannelAsync<T>` určuje typ objektů vrácených metodou streamování. `ChannelReader<T>` Je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.

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

`StreamAsChannelAsync` Metoda v`HubConnection` je použita k vyvolání metody streamování ze serveru na klienta. Předat název a argumenty metody centra definované v metodě hub pro `StreamAsChannelAsync`. Obecný parametr v `StreamAsChannelAsync<T>` určuje typ objektů vrácených metodou streamování. `ChannelReader<T>` Je vrácen z vyvolání datového proudu a představuje datový proud na klientovi.

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

Existují dva způsoby, jak vyvolat metodu rozbočovače streamování typu klient-server z klienta .NET. Můžete `IAsyncEnumerable<T>` buď předat parametr `SendAsync` `ChannelReader` nebo jako argument do, `InvokeAsync`nebo `StreamAsChannelAsync`, v závislosti na volané metodě centra.

Pokaždé, když jsou data `IAsyncEnumerable` zapsána do objektu nebo `ChannelWriter` , metoda centra na serveru obdrží novou položku s daty z klienta.

Při použití `IAsyncEnumerable` objektu se datový proud ukončí po ukončení metody, která vrací položky streamu.

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

Pokud používáte `ChannelWriter`, můžete kanál dokončete pomocí `channel.Writer.Complete()`těchto kroků:

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

Klienti JavaScriptu volají metody streamování na straně serveru na rozbočovačích s `connection.stream`. `stream` Metoda přijímá dva argumenty:

* Název metody rozbočovače. V následujícím příkladu je `Counter`název metody rozbočovače.
* Argumenty definované v metodě centra V následujícím příkladu jsou argumenty počtem položek datového proudu, které mají být přijímány, a zpoždění mezi položkami datového proudu.

`connection.stream`Vrátí, který obsahuje `subscribe`metodu. `IStreamResult` `error` `complete` `stream` Předejte a nastavte`next`zpětná volání, a pro příjem oznámení od vyvolání. `subscribe` `IStreamSubscriber`

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Chcete-li ukončit datový proud z klienta, zavolejte `dispose` metodu `ISubscription` na `subscribe` , kterou vrátí metoda. Volání této metody způsobí zrušení `CancellationToken` parametru metody hub, pokud jste ji zadali.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Chcete-li ukončit datový proud z klienta, zavolejte `dispose` metodu `ISubscription` na `subscribe` , kterou vrátí metoda.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streamování klienta na server

Klienti JavaScriptu volají metody streamování typu klient-server na rozbočovači `Subject` předáním jako `send`argumentu, `invoke`nebo `stream`v závislosti na volané metodě centra. Je třída, která vypadá `Subject`jako. `Subject` Například v RxJS můžete použít třídu [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) z této knihovny.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

Volání `subject.next(item)` s položkou zapisuje položku do datového proudu a metoda hub přijme položku na serveru.

Chcete-li ukončit datový proud `subject.complete()`, zavolejte.

## <a name="java-client"></a>Klient Java

### <a name="server-to-client-streaming"></a>Streamování ze serveru na klienta

Klient Java Signal používá `stream` metodu k vyvolání metod streamování. `stream`přijímá tři nebo více argumentů:

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

`stream` Metoda on`HubConnection` vrátí pozorovatelný typ položky streamu. `subscribe` Metoda pozorovatelho typu je místo, `onError` `onCompleted` kde `onNext`jsou definovány obslužné rutiny.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)

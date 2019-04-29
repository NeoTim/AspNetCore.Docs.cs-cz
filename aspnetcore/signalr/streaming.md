---
title: Použití datových proudů v knihovně SignalR technologie ASP.NET Core
author: bradygaster
description: Naučíte se Streamovat data mezi klientem a serverem.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2019
uid: signalr/streaming
ms.openlocfilehash: d185056d3bdda089eaa46ae9b8e13ab7a4354f93
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165073"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>Použití datových proudů v knihovně SignalR technologie ASP.NET Core

Podle [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

Funkce SignalR technologie ASP.NET Core podporuje datové proudy z klienta na server a ze serveru do klienta. To je užitečné pro scénáře, kde fragmenty data dorazí v čase. Při vysílání datových proudů, každý fragment posílá klienta nebo serveru hned, jakmile bude k dispozici, nikoli čeká se na všech dat k dispozici.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Funkce SignalR technologie ASP.NET Core podporuje streamování návratové hodnoty metod serveru. To je užitečné pro scénáře, kde fragmenty data dorazí v čase. Pokud vrácená hodnota se streamuje klientovi, každý fragment je odeslat klientovi, jakmile bude k dispozici, nikoli čeká všechna data k dispozici.

::: moniker-end

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Nastavení centra pro streamování

::: moniker range=">= aspnetcore-3.0"

Metodu rozbočovače na automaticky stane streamování metody rozbočovače, je po návratu <xref:System.Threading.Channels.ChannelReader%601>, `IAsyncEnumerable<T>`, `Task<ChannelReader<T>>`, nebo `Task<IAsyncEnumerable<T>>`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Metodu rozbočovače na automaticky stane streamování metody rozbočovače, je po návratu <xref:System.Threading.Channels.ChannelReader%601> nebo `Task<ChannelReader<T>>`.

::: moniker-end

### <a name="server-to-client-streaming"></a>Server klient streamování

::: moniker range=">= aspnetcore-3.0"

Streamování metod rozbočovače na může vrátit `IAsyncEnumerable<T>` kromě `ChannelReader<T>`. Nejjednodušší způsob, jak vrátit `IAsyncEnumerable<T>` je tím, že metodu rozbočovače na asynchronní metodu iterátoru, jak ukazuje následující příklad. Můžete přijmout metod rozbočovače na asynchronní iterátor `CancellationToken` parametr, který se aktivuje, když klient zruší z datového proudu. Asynchronní metody iterátoru Předcházejte problémům běžných kanálů, jako je například nevrací `ChannelReader` dostatečně včas nebo standardním ukončením metody bez dokončení <xref:System.Threading.Channels.ChannelWriter`1>.

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

Následující příklad ukazuje základy streamovaných dat na klientovi pomocí kanálů. Vždy, když je objekt zapsána do <xref:System.Threading.Channels.ChannelWriter%601>, objekt je okamžitě se odešlou do klienta. Na konci `ChannelWriter` je dokončit, aby se dali pokyn klientovi, datový proud je uzavřen.

> [!NOTE]
> Zápis do `ChannelWriter<T>` na vlákně na pozadí a vraťte se `ChannelReader` co nejdříve. Další volání rozbočovače je zablokovaná do `ChannelReader` je vrácena.
>
> Zabalení logiku `try ... catch`. Dokončení `Channel` v `catch` i mimo ni `catch` zajistit centra volání metody dokončila správně.

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

Streamování metod rozbočovače na serveru do klienta může přijmout `CancellationToken` parametr, který se aktivuje, když klient zruší z datového proudu. Pomocí tohoto tokenu operace serveru zastavit a uvolnit všechny prostředky, pokud se klient odpojí do konce datového proudu.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Klient server streamování

Metody rozbočovače automaticky změní streamování metody rozbočovače klienta se serverem, pokud přijímá jeden nebo více <xref:System.Threading.Channels.ChannelReader`1>s. Následující příklad ukazuje základní informace o čtení datových proudů dat odeslaných z klienta. Vždy, když klient zapíše do <xref:System.Threading.Channels.ChannelWriter`1>, data jsou zapsána do `ChannelReader` na serveru, který čte z metody rozbočovače.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

::: moniker-end

## <a name="net-client"></a>Klient .NET

### <a name="server-to-client-streaming"></a>Server klient streamování

`StreamAsChannelAsync` Metodu na `HubConnection` se používá k volání metody streaming klient a server. Předat název metody rozbočovače a argumenty, které jsou definovány v metody rozbočovače na `StreamAsChannelAsync`. Obecný parametr na `StreamAsChannelAsync<T>` Určuje typ objektu vrácený metodou streamování. A `ChannelReader<T>` vrácená z volání datového proudu a představuje datový proud na straně klienta.

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

### <a name="client-to-server-streaming"></a>Klient server streamování

Vyvolání metody streaming centra klient server z klienta .NET, vytvořte `Channel` a předat `ChannelReader` jako argument `SendAsync`, `InvokeAsync`, nebo `StreamAsChannelAsync`, v závislosti na vyvolání metody rozbočovače.

Vždy, když data se zapisují do `ChannelWriter`, metody rozbočovače na serveru obdrží novou položku s daty z klienta.

Do konce datového proudu, dokončete kanál s `channel.Writer.Complete()`.

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>Klient JavaScriptu

### <a name="server-to-client-streaming"></a>Server klient streamování

Klientů JavaScript volat metody klient a server streamování na hubs s využitím `connection.stream`. `stream` Metoda přijímá dva argumenty:

* Název metody rozbočovače. V následujícím příkladu je název metody rozbočovače `Counter`.
* Argumenty podle metody rozbočovače. V následujícím příkladu jsou argumenty počet pro počet položek datového proudu pro příjem a zpoždění mezi položkami datového proudu.

`connection.stream` Vrátí `IStreamResult`, který obsahuje `subscribe` metoda. Předejte `IStreamSubscriber` k `subscribe` a nastavit `next`, `error`, a `complete` zpětná volání k přijímání oznámení z `stream` vyvolání.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda. Voláním této metody způsobí zrušení `CancellationToken` parametru metody rozbočovače, pokud je k dispozici.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Klient server streamování

Klientům JavaScript volat metody streamování klient server v centrech předáním `Subject` jako argument `send`, `invoke`, nebo `stream`, v závislosti na vyvolání metody rozbočovače. `Subject` Je třída, která vypadá jako `Subject`. Například v RxJS, můžete použít [subjektu](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) třídy z této knihovny.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

Volání `subject.next(item)` s položku zapíše položku do datového proudu a metody rozbočovače přijímá položka na serveru.

Do konce datového proudu, volání `subject.complete()`.

## <a name="java-client"></a>Klient Java

### <a name="server-to-client-streaming"></a>Server klient streamování

Používá klientskou sadou SignalR Java `stream` metoda k vyvolání metody streaming. `stream` přijímá tři nebo více argumentů:

* Očekávaný typ položky datového proudu.
* Název metody rozbočovače.
* Argumenty podle metody rozbočovače.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

`stream` Metodu na `HubConnection` vrátí existuje zjištěný typ položky datového proudu. Pozorovatelné typ `subscribe` metoda je tam, kde `onNext`, `onError` a `onCompleted` obslužné rutiny jsou definovány.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)

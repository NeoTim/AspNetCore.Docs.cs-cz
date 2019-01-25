---
title: Použití datových proudů v knihovně SignalR technologie ASP.NET Core
author: bradygaster
description: ''
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/14/2018
uid: signalr/streaming
ms.openlocfilehash: ade2d6fb6e799d53ff3aaa69c641d0088acdee95
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837400"
---
# <a name="use-streaming-in-aspnet-core-signalr"></a>Použití datových proudů v knihovně SignalR technologie ASP.NET Core

Podle [Brennan Conroy](https://github.com/BrennanConroy)

Funkce SignalR technologie ASP.NET Core podporuje streamování návratové hodnoty metod serveru. To je užitečné pro scénáře, kde budou přicházet fragmenty dat průběhu času. Pokud vrácená hodnota se streamuje klientovi, každý fragment je odeslat klientovi, jakmile bude k dispozici, nikoli čeká všechna data k dispozici.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/live/aspnetcore/signalr/streaming/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="set-up-the-hub"></a>Nastavení centra

Metodu rozbočovače na automaticky stane streamování metody rozbočovače, je po návratu `ChannelReader<T>` nebo `Task<ChannelReader<T>>`. Níže je příklad, který ukazuje základy streamovaných dat na klientovi. Vždy, když je objekt zapsána do `ChannelReader` tento objekt se okamžitě se odešlou do klienta. Na konci `ChannelReader` je dokončit, aby se dali pokyn klientovi, datový proud je uzavřen.

> [!NOTE]
> * Zápis do `ChannelReader` na vlákně na pozadí a vraťte se `ChannelReader` co nejdříve. Další volání rozbočovače se zablokuje, dokud `ChannelReader` je vrácena.
> * Zabalení svoji logiku v `try ... catch` a proveďte `Channel` v catch a mimo catch, aby se zajistilo centra volání metody se dokončila správně.

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.aspnetcore21.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/sample/Hubs/StreamHub.cs?name=snippet1)]

V ASP.NET Core 2.2 nebo vyšší, můžete přijmout streamování metod rozbočovače `CancellationToken` parametr, který se aktivuje, když klient zruší z datového proudu. Pomocí tohoto tokenu operace serveru zastavit a uvolnit všechny prostředky, pokud se klient odpojí do konce datového proudu.

::: moniker-end

## <a name="net-client"></a>.NET client

`StreamAsChannelAsync` Metodu na `HubConnection` se používá k volání metody streaming. Předat název metody rozbočovače a argumenty, které jsou definovány v metody rozbočovače na `StreamAsChannelAsync`. Obecný parametr na `StreamAsChannelAsync<T>` Určuje typ objektu vrácený metodou streamování. A `ChannelReader<T>` vrácená z volání služby stream a představuje datový proud na straně klienta. Čtení dat, běžně používá k vytvoření smyčky přes `WaitToReadAsync` a volat `TryRead` kdy data jsou k dispozici. Smyčky se ukončí, pokud datový proud bylo ukončeno serverem nebo předat token zrušení `StreamAsChannelAsync` se zruší.

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

## <a name="javascript-client"></a>Javascriptový klient

Klientů JavaScript pomocí volání metody streaming v centrech `connection.stream`. `stream` Metoda přijímá dva argumenty:

* Název metody rozbočovače. V následujícím příkladu je název metody rozbočovače `Counter`.
* Argumenty podle metody rozbočovače. V následujícím příkladu jsou argumenty: počet pro počet položek datového proudu pro příjem a zpoždění mezi položkami datového proudu.

`connection.stream` Vrátí `IStreamResult` obsahující `subscribe` metody. Předejte `IStreamSubscriber` k `subscribe` a nastavit `next`, `error`, a `complete` zpětná volání, které chcete dostávat oznámení `stream` vyvolání.

[!code-javascript[Streaming javascript](streaming/sample/wwwroot/js/stream.js?range=19-36)]

::: moniker range="= aspnetcore-2.1"

Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Chcete-li ukončit stream z klienta, zavolejte `dispose` metodu na `ISubscription` , který je vrácen z `subscribe` metoda. Volání této metody způsobí, `CancellationToken` parametru metody rozbočovače (Pokud jste zadali jednu) budou zrušeny.

::: moniker-end

## <a name="related-resources"></a>Související prostředky

* [Centra](xref:signalr/hubs)
* [Klient .NET](xref:signalr/dotnet-client)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)

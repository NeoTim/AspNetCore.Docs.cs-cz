---
title: Klient .NET funkce SignalR technologie ASP.NET Core
author: bradygaster
description: Informace o .NET klienta SignalR technologie ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: 97c553874cb1e4b678fa0e5cd65074f135193861
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153127"
---
# <a name="aspnet-core-signalr-net-client"></a>Klient .NET funkce SignalR technologie ASP.NET Core

Klientská knihovna .NET funkce SignalR technologie ASP.NET Core umožňuje komunikovat s rozbočovače SignalR z aplikací .NET.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([stažení](xref:index#how-to-download-a-sample))

Vzorový kód v tomto článku je aplikace WPF, která používá klienta .NET funkce SignalR technologie ASP.NET Core.

## <a name="install-the-signalr-net-client-package"></a>Instalace balíčku pro klienta SignalR .NET

`Microsoft.AspNetCore.SignalR.Client` Balíčku je potřeba pro klienty .NET pro připojení k rozbočovačům SignalR. Pokud chcete nainstalovat klientské knihovny, spusťte následující příkaz **Konzola správce balíčků** okno:

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a>Připojení k rozbočovači

K navázání připojení, vytvoření `HubConnectionBuilder` a volat `Build`. Adresa URL rozbočovače, protokol, typ přenosu, úroveň protokolu, záhlaví a další možnosti je možné nakonfigurovat při vytváření připojení. Nakonfigurujte veškeré požadované možnosti vložíte-li některý z `HubConnectionBuilder` metod do `Build`. Zahájit připojení s `StartAsync`.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Zpracování došlo ke ztrátě připojení

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automaticky znovu připojila

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> Umožňují automaticky znovu připojila, pomocí `WithAutomaticReconnect` metodu <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>. Připojení nebude automaticky ve výchozím nastavení.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

Bez parametrů `WithAutomaticReconnect()` nakonfiguruje klienta na 0, 2, 10 a 30 sekund, resp. před pokusem o každý pokus o volání metody reconnect, zastavování po čtyři neúspěšných pokusech o čekání.

Před zahájením jakékoli pokusy o volání metody reconnect `HubConnection` se přechod na `HubConnectionState.Reconnecting` stav a vyvolat `Reconnecting` událostí.  To představuje příležitost a upozornit uživatele, že připojení bylo ztraceno zakážete prvky uživatelského rozhraní. Neinteraktivní aplikace spustit služby Řízení front nebo vyřazením zprávy.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Pokud klient úspěšně obnoví v rámci své první čtyři pokusy o `HubConnection` přejde zpět `Connected` stav a vyvolat `Reconnected` událostí. To představuje příležitost k informování uživatelů připojení doručena a vyřazovat je z ní všechny zprávy ve frontě.

Vzhledem k tomu, že připojení ověří zcela nový server, nový `ConnectionId` vám poskytneme `Reconnected` obslužných rutin událostí.

> [!WARNING]
> `Reconnected` Obslužná rutina události `connectionId` parametr bude mít hodnotu null Pokud `HubConnection` nastavený tak, aby [přeskočit vyjednávání](xref:signalr/configuration#configure-client-options).

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` nenakonfiguruje `HubConnection` pokus o selhání prvního spuštění, budou muset ručně zpracovat selhání spuštění:

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

Pokud klient nebude znovu připojit úspěšně v rámci své první čtyři pokusy o `HubConnection` se přechod `Disconnected` stav a vyvolat <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> událostí. To poskytuje možnost pokusit se o připojení ručně restartovat nebo informování uživatelů o tom, že připojení bylo trvale ztratí.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Pokud chcete nakonfigurovat vlastní počet pokusů o nové připojení před odpojením nebo změnit časování volání metody reconnect `WithAutomaticReconnect` přijímá pole čísel představující zpoždění v milisekundách pro čekání před zahájením každý pokus o volání metody reconnect.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

Předchozí příklad nakonfiguruje `HubConnection` spustit okamžitě, jakmile dojde ke ztrátě připojení k pokusu o připojování. To platí také pro výchozí konfiguraci.

Pokud první pokus o volání metody reconnect se nezdaří, druhý pokus o volání metody reconnect také začne okamžitě nečekáte, než 2 sekundy, jako by tomu bylo ve výchozí konfiguraci.

Pokud se druhý pokus o volání metody reconnect se nezdaří, třetí pokus o volání metody reconnect se spustí za 10 sekund, což je znovu jako výchozí konfiguraci.

Vlastní chování pak diverges znovu výchozí chování zastavením po selhání pokusu třetí volání metody reconnect. Ve výchozím nastavení existuje by být jeden více znovu připojit pokus o jiné 30 sekund.

Pokud chcete, aby ještě větší kontrolu nad načasování a počet automaticky pokusů `WithAutomaticReconnect` přijímá implementaci objektu `IRetryPolicy` rozhraní, které obsahuje jedinou metodu s názvem `NextRetryDelay`.

`NextRetryDelay` přijímá jeden argument s typem `RetryContext`. `RetryContext` Má tři vlastnosti: `PreviousRetryCount`, `ElapsedTime` a `RetryReason` které jsou `long`, `TimeSpan` a `Exception` v uvedeném pořadí. Před první pokus o volání metody reconnect obě `PreviousRetryCount` a `ElapsedTime` bude nula a `RetryReason` bude výjimka, která způsobila ke ztrátě připojení. Po každý pokus o opakování neúspěšných `PreviousRetryCount` se zvýší o jedna, `ElapsedTime` bude aktualizován tak, aby odrážely množství času stráveného zatím, opětovné připojení a `RetryReason` bude výjimka, která poslední pokus o volání metody reconnect se nezdaří.

`NextRetryDelay` musí vrátit buď časový interval představující dobu čekání před dalším pokusem volání metody reconnect nebo `null` Pokud `HubConnection` by se měla zastavit, opětovné připojení.

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.Next() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

Alternativně můžete napsat kód, který se znovu připojit klientu ručně, jak je ukázáno v [ručně připojit](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ručně připojit

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Než 3.0 klient .NET pro funkci SignalR nebude automaticky znovu připojit. Musíte napsat kód, který se znovu připojit klientu ručně.

::: moniker-end

Použití <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> události a reagovat na došlo ke ztrátě připojení. Můžete například chtít automatizovat opětovné připojení.

`Closed` Událost vyžaduje delegáta, který vrátí `Task`, což umožňuje asynchronní kód ke spuštění bez použití `async void`. Tím se uspokojí delegáta v `Closed` obslužná rutina události, která spustí synchronně, vracet `Task.CompletedTask`:

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Hlavním důvodem pro asynchronní podporu je tak můžete restartovat připojení. Spouští se připojení je asynchronní akce.

V `Closed` obslužná rutina, která restartuje připojení, vezměte v úvahu časový limit na některé náhodné zpoždění, aby se zabránilo přetížení serveru, jak je znázorněno v následujícím příkladu:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Volání metod rozbočovače na z klienta

`InvokeAsync` volání metody rozbočovače. Předat název metody rozbočovače a argumentů podle metody rozbočovače na `InvokeAsync`. SignalR je asynchronní, proto `async` a `await` při volání.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

`InvokeAsync` Metoda vrátí hodnotu `Task` která se dokončí po návratu metody serveru. Návratovou hodnotu, pokud existuje, se poskytuje jako výsledek `Task`. Jakékoli výjimky vyvolané metodou na serveru vytvoří chybovém `Task`. Použití `await` syntaxe čekat metody serveru k dokončení a `try...catch` syntaxe zpracování chyb.

`SendAsync` Metoda vrátí hodnotu `Task` která se dokončí, když zpráva byla odeslána na server. Není zadána žádná návratová hodnota od to `Task` nečeká metoda server dokončí. Vytvoření jakýchkoli výjimek na straně klienta při odesílání zprávy chybovém `Task`. Použití `await` a `try...catch` odeslání syntaxe pro zpracování chyb.

> [!NOTE]
> Pokud používáte služby Azure SignalR v *bez serveru režimu*, nelze volat metody rozbočovače klienta. Další informace najdete v tématu [dokumentace ke službě SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Volání metody klienta od rozbočovače

Definovat metody rozbočovače volá pomocí `connection.On` po sestavení, ale před spuštěním připojení.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Předchozí kód v `connection.On` spustí, když kód na straně serveru pomocí volání `SendAsync` metoda.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Protokolování a zpracování chyb

Zpracování chyb pomocí příkazu try-catch. Zkontrolujte `Exception` objektu určit správnou akce má být provedena, když dojde k chybě.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Další zdroje

* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
* [Dokumentace Azure bez serveru služby SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config)

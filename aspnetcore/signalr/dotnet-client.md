---
title: SignalRKlient rozhraní ASP.NET Core .NET
author: bradygaster
description: Informace o klientovi ASP.NET Core SignalR .NET
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
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
uid: signalr/dotnet-client
ms.openlocfilehash: a03598f887d628c8a2b6720d99826d4aef4e52fa
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019999"
---
# <a name="aspnet-core-no-locsignalr-net-client"></a>SignalRKlient rozhraní ASP.NET Core .NET

SignalRKlientská knihovna ASP.NET Core .NET umožňuje komunikaci s SignalR rozbočovači z aplikací .NET.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázka kódu v tomto článku je aplikace WPF, která používá SignalR klienta rozhraní ASP.NET Core .NET.

## <a name="install-the-no-locsignalr-net-client-package"></a>Instalace SignalR balíčku klienta .NET

[Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Pro klienty rozhraní .NET se vyžaduje balíček klienta, aby se mohl připojit k SignalR rozbočovačům.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Chcete-li nainstalovat knihovnu klienta, spusťte následující příkaz v okně **konzoly Správce balíčků** :

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Knihovnu klienta nainstalujete spuštěním následujícího příkazu v příkazovém prostředí:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Připojení k centru

K navázání připojení vytvořte `HubConnectionBuilder` volání a `Build` . Adresa URL centra, protokol, typ přenosu, úroveň protokolu, hlavičky a další možnosti se dají nakonfigurovat při sestavování připojení. Proveďte konfiguraci libovolných požadovaných možností tak, že do nástroje vložíte jakoukoli z těchto `HubConnectionBuilder` metod `Build` . Spusťte připojení pomocí `StartAsync` .

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Zpracovat ztracené připojení

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automaticky znovu připojit

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>Lze nakonfigurovat pro automatické opětovné připojení pomocí `WithAutomaticReconnect` metody na <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . Ve výchozím nastavení se automaticky znovu nepřipojí.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

Bez parametrů `WithAutomaticReconnect()` nakonfiguruje klienta, aby čekal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.

Před zahájením jakýchkoli pokusů o opětovné připojení `HubConnection` přejde na `HubConnectionState.Reconnecting` stav a aktivuje `Reconnecting` událost.  Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní. Neinteraktivní aplikace můžou spouštět zařazování zpráv do fronty nebo jejich vyřazení.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, převede se `HubConnection` zpátky do `Connected` stavu a aktivuje se `Reconnected` událost. To poskytuje možnost informovat uživatele o opětovném vytvoření připojení a vyřazení všech zpráv ve frontě.

Vzhledem k tomu, že připojení je zcela nového na serveru, `ConnectionId` bude k dispozici pro `Reconnected` obslužné rutiny událostí nový.

> [!WARNING]
> `Reconnected`Parametr obslužné rutiny události `connectionId` bude mít hodnotu null, pokud `HubConnection` byl nakonfigurován tak, aby [přeskočil vyjednávání](xref:signalr/configuration#configure-client-options).

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()`neprovede konfiguraci `HubConnection` pro opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:

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

Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde do `Disconnected` stavu a aktivuje <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> událost. To vám umožní se pokusit o ruční restartování připojení nebo informování uživatelů o trvalé ztrátě připojení.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování pro opětovné připojení, `WithAutomaticReconnect` přijme pole čísel představující zpoždění v milisekundách, které se má počkat, než se spustí pokus o opětovné připojení.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

V předchozím příkladu se nakonfiguruje `HubConnection` tak, aby se při pokusu o opětovné připojení ihned po ztrátě připojení spouštěla znovu. To platí také pro výchozí konfiguraci.

Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.

Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.

Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o třetí připojení. Ve výchozí konfiguraci se za dalších 30 sekund pokusí jeden pokus o nové připojení.

Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení, je třeba `WithAutomaticReconnect` přijmout objekt implementující `IRetryPolicy` rozhraní, které má jedinou metodu s názvem `NextRetryDelay` .

`NextRetryDelay`přijímá jeden argument s typem `RetryContext` . `RetryContext`Má tři vlastnosti: `PreviousRetryCount` , `ElapsedTime` a `RetryReason` , které jsou a v `long` `TimeSpan` `Exception` uvedeném pořadí. Před prvním pokusem o opětovné připojení budou obě `PreviousRetryCount` i `ElapsedTime` nulové a bude `RetryReason` to výjimka, která způsobila ztrátu připojení. Po každém neúspěšném pokusu o opakování se bude `PreviousRetryCount` aktualizovat o jednu, `ElapsedTime` aby odrážela dobu strávenou opětovným připojením, a `RetryReason` bude výjimkou, která způsobila selhání posledního pokusu o opětovné připojení.

`NextRetryDelay`musí vracet buď hodnotu TimeSpan představující čas čekání před dalším pokusem o opětovné připojení, nebo `null` by se `HubConnection` mělo zastavit opětovné připojení.

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
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
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
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ručně znovu připojit

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Před 3,0 se klient .NET pro SignalR neautomaticky znovu nepřipojí. Musíte napsat kód, který bude znovu připojit klienta ručně.

::: moniker-end

K <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> reakci na ztracené připojení použijte událost. Můžete například chtít automatizovat opětovné připojení.

`Closed`Událost vyžaduje delegáta, který vrací `Task` , což umožňuje spuštění asynchronního kódu bez použití `async void` . Pro uspokojení signatury delegáta v `Closed` obslužné rutině události, která běží synchronně, vraťte `Task.CompletedTask` :

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Hlavním důvodem pro asynchronní podporu je, že můžete připojení restartovat. Spuštění připojení je asynchronní akce.

V `Closed` obslužné rutině, která restartuje připojení, zvažte možnost počkat na určité náhodné zpoždění, aby se zabránilo přetížení serveru, jak je znázorněno v následujícím příkladu:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Volání metod centra z klienta

`InvokeAsync`volá metody v centru. Předat název metody centra a všechny argumenty definované v metodě hub pro `InvokeAsync` . SignalRje asynchronní, takže použijte `async` a `await` při provádění volání.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

`InvokeAsync`Metoda vrátí hodnotu, `Task` která je dokončena, když metoda serveru vrátí hodnotu. Návratová hodnota, pokud existuje, je k dispozici jako výsledek `Task` . Jakékoli výjimky vyvolané metodou na serveru vyvolávají chybu `Task` . Použijte `await` syntaxi pro čekání na dokončení metody serveru a `try...catch` syntaxi pro zpracování chyb.

`SendAsync`Metoda vrátí, `Task` která se dokončí při odeslání zprávy na server. Žádná návratová hodnota není k dispozici `Task` , protože nečeká na dokončení metody serveru. Jakékoli výjimky vyvolané u klienta při odeslání zprávy způsobují chybu `Task` . Použijte `await` `try...catch` syntaxi a ke zpracování chyb odesílání.

> [!NOTE]
> Pokud používáte SignalR službu Azure v režimu bez *serveru*, nemůžete volat metody centra z klienta. Další informace najdete v dokumentaci ke [ SignalR službě](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Volání metod klienta z centra

Definujte metody, které volání centra použije `connection.On` po sestavení, ale před zahájením připojení.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Předchozí kód v aplikaci se `connection.On` spustí, když kód na straně serveru volá `SendAsync` metodu.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Zpracování chyb a protokolování

Zpracování chyb pomocí příkazu try-catch. Zkontrolujte `Exception` objekt a určete správnou akci, která se má provést po výskytu chyby.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Další zdroje

* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
* [SignalRDokumentace k serveru se službou Azure](/azure/azure-signalr/signalr-concept-serverless-development-config)

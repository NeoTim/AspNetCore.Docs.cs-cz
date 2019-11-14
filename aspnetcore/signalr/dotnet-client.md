---
title: Klient rozhraní ASP.NET Core SignalR .NET
author: bradygaster
description: Informace o ASP.NET Core SignalR klientovi .NET
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: 28e8fcf808406cd0251ba94e2ef97ab04841fcd0
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963970"
---
# <a name="aspnet-core-opno-locsignalr-net-client"></a>Klient rozhraní ASP.NET Core SignalR .NET

Klientská knihovna ASP.NET Core SignalR .NET umožňuje komunikaci s SignalRmi rozbočovači z aplikací .NET.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázka kódu v tomto článku je aplikace WPF, která používá klienta ASP.NET Core SignalR .NET.

## <a name="install-the-opno-locsignalr-net-client-package"></a>Instalace balíčku klienta SignalR .NET

[Microsoft. AspNetCore.SignalR. ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Pro klienty rozhraní .NET se vyžaduje balíček klienta, aby se mohl připojit k rozbočovačům SignalR.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Chcete-li nainstalovat knihovnu klienta, spusťte následující příkaz v okně **konzoly Správce balíčků** :

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Knihovnu klienta nainstalujete spuštěním následujícího příkazu v příkazovém prostředí:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Připojení k centru

K navázání připojení vytvořte `HubConnectionBuilder` a zavolejte `Build`. Adresa URL centra, protokol, typ přenosu, úroveň protokolu, hlavičky a další možnosti se dají nakonfigurovat při sestavování připojení. Proveďte konfiguraci požadovaných možností tak, že do `Build`vložíte libovolnou z `HubConnectionBuilder`ch metod. Spusťte připojení pomocí `StartAsync`.

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Zpracovat ztracené připojení

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automaticky znovu připojit

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> lze nakonfigurovat tak, aby se automaticky znovu připojoval pomocí metody `WithAutomaticReconnect` na <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>. Ve výchozím nastavení se automaticky znovu nepřipojí.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

Bez parametrů `WithAutomaticReconnect()` nakonfiguruje klienta tak, aby počkal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.

Než začnete s jinými pokusy o připojení, `HubConnection` přejde do stavu `HubConnectionState.Reconnecting` a aktivuje událost `Reconnecting`.  Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní. Neinteraktivní aplikace můžou spouštět zařazování zpráv do fronty nebo jejich vyřazení.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Pokud se klient úspěšně připojí během prvních čtyř pokusů, `HubConnection` přejde zpět do stavu `Connected` a aktivuje událost `Reconnected`. To poskytuje možnost informovat uživatele o opětovném vytvoření připojení a vyřazení všech zpráv ve frontě.

Vzhledem k tomu, že připojení na serveru zcela začíná, bude k dispozici nový `ConnectionId` obslužným rutinám událostí `Reconnected`.

> [!WARNING]
> Parametr `connectionId` obslužné rutiny události `Reconnected` bude mít hodnotu null, pokud byl `HubConnection` nakonfigurován na [přeskočení vyjednávání](xref:signalr/configuration#configure-client-options).

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` nenastaví `HubConnection` na opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:

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

Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde do stavu `Disconnected` a aktivuje událost <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>. To vám umožní se pokusit o ruční restartování připojení nebo informování uživatelů o trvalé ztrátě připojení.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování opakovaného připojení, `WithAutomaticReconnect` akceptuje pole čísel představujících zpoždění v milisekundách, které budou čekat před zahájením každého pokusu o opětovné připojení.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

V předchozím příkladu se nakonfiguruje `HubConnection`, aby se začaly znovu připojovat hned po ztrátě připojení. To platí také pro výchozí konfiguraci.

Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.

Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.

Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o třetí připojení. Ve výchozí konfiguraci se za dalších 30 sekund pokusí jeden pokus o nové připojení.

Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení, `WithAutomaticReconnect` akceptuje objekt implementující rozhraní `IRetryPolicy`, které má jedinou metodu s názvem `NextRetryDelay`.

`NextRetryDelay` přijímá jeden argument typu `RetryContext`. `RetryContext` má tři vlastnosti: `PreviousRetryCount`, `ElapsedTime` a `RetryReason`, které jsou `long`, `TimeSpan` a `Exception`. Před prvním pokusem o opětovné připojení budou `PreviousRetryCount` i `ElapsedTime` nula a `RetryReason` bude výjimka, která způsobila ztrátu připojení. Po každém neúspěšném pokusu o opakování se `PreviousRetryCount` zvýší o jednu, `ElapsedTime` se aktualizuje tak, aby odrážela dobu strávenou opětovným připojením, a `RetryReason` bude výjimka, která způsobila selhání posledního pokusu o opětovné připojení.

`NextRetryDelay` musí vracet hodnotu TimeSpan představující čas čekání před dalším pokusem o opětovné připojení nebo `null`, pokud by se `HubConnection` mělo zastavit opětovné připojení.

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

Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).

::: moniker-end

### <a name="manually-reconnect"></a>Ručně znovu připojit

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Před 3,0 se klient .NET pro SignalR automaticky znovu nepřipojí. Musíte napsat kód, který bude znovu připojit klienta ručně.

::: moniker-end

K reakci na ztracené připojení použijte událost <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>. Můžete například chtít automatizovat opětovné připojení.

Událost `Closed` vyžaduje delegáta, který vrací `Task`, což umožňuje spuštění asynchronního kódu bez použití `async void`. Pro uspokojení signatury delegáta v `Closed` obslužné rutiny události, která běží synchronně, vraťte `Task.CompletedTask`:

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Hlavním důvodem pro asynchronní podporu je, že můžete připojení restartovat. Spuštění připojení je asynchronní akce.

V obslužné rutině `Closed`, která restartuje připojení, zvažte možnost počkat na určité náhodné zpoždění, aby se zabránilo přetížení serveru, jak je znázorněno v následujícím příkladu:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Volání metod centra z klienta

`InvokeAsync` volá metody v centru. Předejte název metody centra a všechny argumenty definované v metodě hub pro `InvokeAsync`. SignalR je asynchronní, takže při volání použijte `async` a `await`.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

Metoda `InvokeAsync` vrátí `Task`, která se dokončí při návratu metody serveru. Návratová hodnota, pokud existuje, je k dispozici jako výsledek `Task`. Jakékoli výjimky vyvolané metodou na serveru vyvolávají chybu `Task`. Pomocí syntaxe `await` můžete počkat na dokončení metody serveru a `try...catch` syntaxi pro zpracování chyb.

Metoda `SendAsync` vrátí `Task`, který se dokončí při odeslání zprávy na server. Není zadána žádná návratová hodnota, protože tato `Task` nečekají na dokončení metody serveru. Jakékoli výjimky vyvolané u klienta při posílání zprávy vytvoří `Task`s chybou. K zpracování chyb odeslání použijte syntaxi `await` a `try...catch`.

> [!NOTE]
> Pokud používáte službu Azure SignalR v režimu bez *serveru*, nemůžete volat metody centra z klienta. Další informace najdete v dokumentaci ke [služběSignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Volání metod klienta z centra

Definujte metody, které centrum volá pomocí `connection.On` po sestavení, ale před spuštěním připojení.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Předchozí kód v `connection.On` se spustí, když kód na straně serveru volá metodu `SendAsync`.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Zpracování chyb a protokolování

Zpracování chyb pomocí příkazu try-catch. Zkontrolujte objekt `Exception` a určete správnou akci, která se má provést po výskytu chyby.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Další zdroje

* [Centra](xref:signalr/hubs)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
* [Dokumentace k serveru služby Azure SignalR bez serveru](/azure/azure-signalr/signalr-concept-serverless-development-config)

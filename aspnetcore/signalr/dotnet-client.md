---
title: Klient .NET pro signalizaci ASP.NET Core
author: bradygaster
description: Informace o klientovi .NET signalizace ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/13/2019
uid: signalr/dotnet-client
ms.openlocfilehash: d2755f652e734bad6447ddeb9a82345dcde25b28
ms.sourcegitcommit: 805f625d16d74e77f02f5f37326e5aceafcb78e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985484"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="3b229-103">Klient .NET pro signalizaci ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b229-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="3b229-104">Klientská knihovna .NET pro signalizaci ASP.NET Core umožňuje komunikaci s centry signalizace z aplikací .NET.</span><span class="sxs-lookup"><span data-stu-id="3b229-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="3b229-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3b229-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3b229-106">Ukázka kódu v tomto článku je aplikace WPF, která používá klienta .NET ASP.NET Coreového signalizace.</span><span class="sxs-lookup"><span data-stu-id="3b229-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="3b229-107">Instalace balíčku klienta rozhraní .NET pro signál</span><span class="sxs-lookup"><span data-stu-id="3b229-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="3b229-108">Pro klienty rozhraní .NET se vyžaduje balíček [Microsoft. AspNetCore. Signal. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) , aby se mohl připojit k rozbočovačům pro signály.</span><span class="sxs-lookup"><span data-stu-id="3b229-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3b229-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b229-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3b229-110">Chcete-li nainstalovat knihovnu klienta, spusťte následující příkaz v okně **konzoly Správce balíčků** :</span><span class="sxs-lookup"><span data-stu-id="3b229-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3b229-111">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3b229-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3b229-112">Knihovnu klienta nainstalujete spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="3b229-112">To install the client library, run the following command in a command shell:</span></span>

```console
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="3b229-113">Připojení k rozbočovači</span><span class="sxs-lookup"><span data-stu-id="3b229-113">Connect to a hub</span></span>

<span data-ttu-id="3b229-114">K navázání připojení vytvořte `HubConnectionBuilder` volání `Build`a.</span><span class="sxs-lookup"><span data-stu-id="3b229-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="3b229-115">Adresa URL centra, protokol, typ přenosu, úroveň protokolu, hlavičky a další možnosti se dají nakonfigurovat při sestavování připojení.</span><span class="sxs-lookup"><span data-stu-id="3b229-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="3b229-116">Proveďte konfiguraci libovolných požadovaných možností tak, že `HubConnectionBuilder` do `Build`nástroje vložíte jakoukoli z těchto metod.</span><span class="sxs-lookup"><span data-stu-id="3b229-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="3b229-117">Spusťte připojení pomocí `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="3b229-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="3b229-118">Zpracovat ztracené připojení</span><span class="sxs-lookup"><span data-stu-id="3b229-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="3b229-119">Automaticky znovu připojit</span><span class="sxs-lookup"><span data-stu-id="3b229-119">Automatically reconnect</span></span>

<span data-ttu-id="3b229-120">Lze nakonfigurovat pro automatické opětovné připojení `WithAutomaticReconnect` pomocí metody na <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>. <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection></span><span class="sxs-lookup"><span data-stu-id="3b229-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="3b229-121">Ve výchozím nastavení se automaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="3b229-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="3b229-122">Bez parametrů nakonfiguruje `WithAutomaticReconnect()` klienta, aby čekal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.</span><span class="sxs-lookup"><span data-stu-id="3b229-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="3b229-123">Před zahájením jakýchkoli pokusů o `HubConnection` opětovné připojení přejde `HubConnectionState.Reconnecting` na stav a aktivuje `Reconnecting` událost.</span><span class="sxs-lookup"><span data-stu-id="3b229-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="3b229-124">Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3b229-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="3b229-125">Neinteraktivní aplikace můžou spouštět zařazování zpráv do fronty nebo jejich vyřazení.</span><span class="sxs-lookup"><span data-stu-id="3b229-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="3b229-126">Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, `HubConnection` převede se zpátky `Connected` do stavu a aktivuje se `Reconnected` událost.</span><span class="sxs-lookup"><span data-stu-id="3b229-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="3b229-127">To poskytuje možnost informovat uživatele o opětovném vytvoření připojení a vyřazení všech zpráv ve frontě.</span><span class="sxs-lookup"><span data-stu-id="3b229-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="3b229-128">Vzhledem k tomu, že připojení je zcela nového na serveru, `ConnectionId` bude `Reconnected` k dispozici pro obslužné rutiny událostí nový.</span><span class="sxs-lookup"><span data-stu-id="3b229-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="3b229-129">Parametr obslužné rutiny `HubConnection`událostibude mít hodnotu null, pokud byl nakonfigurován tak, aby [přeskočil vyjednávání.](xref:signalr/configuration#configure-client-options) `Reconnected` `connectionId`</span><span class="sxs-lookup"><span data-stu-id="3b229-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="3b229-130">`WithAutomaticReconnect()`neprovede konfiguraci `HubConnection` pro opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:</span><span class="sxs-lookup"><span data-stu-id="3b229-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="3b229-131">Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde `Disconnected` do stavu a aktivuje <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> událost.</span><span class="sxs-lookup"><span data-stu-id="3b229-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="3b229-132">To vám umožní se pokusit o ruční restartování připojení nebo informování uživatelů o trvalé ztrátě připojení.</span><span class="sxs-lookup"><span data-stu-id="3b229-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="3b229-133">Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování pro opětovné připojení, přijme `WithAutomaticReconnect` pole čísel představující zpoždění v milisekundách, které se má počkat, než se spustí pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="3b229-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="3b229-134">V předchozím příkladu se nakonfiguruje tak, `HubConnection` aby se při pokusu o opětovné připojení ihned po ztrátě připojení spouštěla znovu.</span><span class="sxs-lookup"><span data-stu-id="3b229-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="3b229-135">To platí také pro výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="3b229-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="3b229-136">Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="3b229-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="3b229-137">Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="3b229-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="3b229-138">Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o třetí připojení.</span><span class="sxs-lookup"><span data-stu-id="3b229-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="3b229-139">Ve výchozí konfiguraci se za dalších 30 sekund pokusí jeden pokus o nové připojení.</span><span class="sxs-lookup"><span data-stu-id="3b229-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="3b229-140">Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení `WithAutomaticReconnect` , je třeba přijmout objekt `IRetryPolicy` implementující rozhraní, které má jedinou metodu `NextRetryDelay`s názvem.</span><span class="sxs-lookup"><span data-stu-id="3b229-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="3b229-141">`NextRetryDelay`přijímá jeden argument s typem `RetryContext`.</span><span class="sxs-lookup"><span data-stu-id="3b229-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="3b229-142">`ElapsedTime` Mátři`Exception` vlastnosti: `PreviousRetryCount` ,`TimeSpan` a `RetryReason` , kteréjsouavuvedenémpořadí.`long` `RetryContext`</span><span class="sxs-lookup"><span data-stu-id="3b229-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason` which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="3b229-143">Před prvním pokusem o opětovné připojení budou `PreviousRetryCount` obě `ElapsedTime` i nulové a `RetryReason` bude to výjimka, která způsobila ztrátu připojení.</span><span class="sxs-lookup"><span data-stu-id="3b229-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="3b229-144">Po každém neúspěšném pokusu o `PreviousRetryCount` opakování se bude aktualizovat o jednu, `ElapsedTime` aby odrážela dobu strávenou opětovným připojením, a `RetryReason` bude výjimkou, která způsobila selhání posledního pokusu o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="3b229-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="3b229-145">`NextRetryDelay`musí vracet buď hodnotu TimeSpan představující čas čekání před dalším pokusem o opětovné připojení, nebo `null` by se `HubConnection` mělo zastavit opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="3b229-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="3b229-146">Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="3b229-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="3b229-147">Ručně znovu připojit</span><span class="sxs-lookup"><span data-stu-id="3b229-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="3b229-148">Před 3,0 se klient .NET pro signál nepřipojí automaticky znovu.</span><span class="sxs-lookup"><span data-stu-id="3b229-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="3b229-149">Musíte napsat kód, který se znovu připojit klientu ručně.</span><span class="sxs-lookup"><span data-stu-id="3b229-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="3b229-150">K reakci na ztracené připojení použijte událost.<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed></span><span class="sxs-lookup"><span data-stu-id="3b229-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="3b229-151">Můžete například chtít automatizovat opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="3b229-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="3b229-152">Událost vyžaduje delegáta, který `Task`vrací, což umožňuje spuštění asynchronního kódu bez použití `async void`. `Closed`</span><span class="sxs-lookup"><span data-stu-id="3b229-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="3b229-153">Pro uspokojení signatury delegáta v `Closed` obslužné rutině události, která běží synchronně `Task.CompletedTask`, vraťte:</span><span class="sxs-lookup"><span data-stu-id="3b229-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="3b229-154">Hlavním důvodem pro asynchronní podporu je, že můžete připojení restartovat.</span><span class="sxs-lookup"><span data-stu-id="3b229-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="3b229-155">Spuštění připojení je asynchronní akce.</span><span class="sxs-lookup"><span data-stu-id="3b229-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="3b229-156">`Closed` V obslužné rutině, která restartuje připojení, zvažte možnost počkat na určité náhodné zpoždění, aby se zabránilo přetížení serveru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="3b229-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="3b229-157">Volání metod rozbočovače na z klienta</span><span class="sxs-lookup"><span data-stu-id="3b229-157">Call hub methods from client</span></span>

<span data-ttu-id="3b229-158">`InvokeAsync`volá metody v centru.</span><span class="sxs-lookup"><span data-stu-id="3b229-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="3b229-159">Předat název metody centra a všechny argumenty definované v metodě hub pro `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="3b229-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="3b229-160">Signál je asynchronní, takže použijte `async` a `await` při volání.</span><span class="sxs-lookup"><span data-stu-id="3b229-160">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="3b229-161">Metoda vrátí hodnotu, `Task` která je dokončena, když metoda serveru vrátí hodnotu. `InvokeAsync`</span><span class="sxs-lookup"><span data-stu-id="3b229-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="3b229-162">Návratová hodnota, pokud existuje, je k dispozici jako výsledek `Task`.</span><span class="sxs-lookup"><span data-stu-id="3b229-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="3b229-163">Jakékoli výjimky vyvolané metodou na serveru vyvolávají chybu `Task`.</span><span class="sxs-lookup"><span data-stu-id="3b229-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="3b229-164">Použijte `await` syntaxi pro čekání na dokončení metody serveru a `try...catch` syntaxi pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="3b229-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="3b229-165">Metoda vrátí, `Task` která se dokončí při odeslání zprávy na server. `SendAsync`</span><span class="sxs-lookup"><span data-stu-id="3b229-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="3b229-166">Žádná návratová hodnota není k dispozici, protože `Task` nečeká na dokončení metody serveru.</span><span class="sxs-lookup"><span data-stu-id="3b229-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="3b229-167">Jakékoli výjimky vyvolané u klienta při odeslání zprávy způsobují chybu `Task`.</span><span class="sxs-lookup"><span data-stu-id="3b229-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="3b229-168">Použijte `await` syntaxi `try...catch` a ke zpracování chyb odesílání.</span><span class="sxs-lookup"><span data-stu-id="3b229-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="3b229-169">Pokud používáte službu Azure Signaler v režimu bez *serveru*, nemůžete volat metody centra z klienta.</span><span class="sxs-lookup"><span data-stu-id="3b229-169">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="3b229-170">Další informace najdete v dokumentaci ke [službě signaler](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="3b229-170">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="3b229-171">Volání metody klienta od rozbočovače</span><span class="sxs-lookup"><span data-stu-id="3b229-171">Call client methods from hub</span></span>

<span data-ttu-id="3b229-172">Definujte metody, které volání centra `connection.On` použije po sestavení, ale před zahájením připojení.</span><span class="sxs-lookup"><span data-stu-id="3b229-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="3b229-173">Předchozí kód v aplikaci `connection.On` se spustí, když kód na straně serveru volá `SendAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="3b229-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="3b229-174">Protokolování a zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="3b229-174">Error handling and logging</span></span>

<span data-ttu-id="3b229-175">Zpracování chyb pomocí příkazu try-catch.</span><span class="sxs-lookup"><span data-stu-id="3b229-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="3b229-176">`Exception` Zkontrolujte objekt a určete správnou akci, která se má provést po výskytu chyby.</span><span class="sxs-lookup"><span data-stu-id="3b229-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="3b229-177">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3b229-177">Additional resources</span></span>

* [<span data-ttu-id="3b229-178">Centra</span><span class="sxs-lookup"><span data-stu-id="3b229-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="3b229-179">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="3b229-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="3b229-180">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="3b229-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="3b229-181">Dokumentace ke službě Azure Signaler bez serveru</span><span class="sxs-lookup"><span data-stu-id="3b229-181">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

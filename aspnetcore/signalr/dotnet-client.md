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
# <a name="aspnet-core-no-locsignalr-net-client"></a><span data-ttu-id="74912-103">SignalRKlient rozhraní ASP.NET Core .NET</span><span class="sxs-lookup"><span data-stu-id="74912-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="74912-104">SignalRKlientská knihovna ASP.NET Core .NET umožňuje komunikaci s SignalR rozbočovači z aplikací .NET.</span><span class="sxs-lookup"><span data-stu-id="74912-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="74912-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="74912-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="74912-106">Ukázka kódu v tomto článku je aplikace WPF, která používá SignalR klienta rozhraní ASP.NET Core .NET.</span><span class="sxs-lookup"><span data-stu-id="74912-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-no-locsignalr-net-client-package"></a><span data-ttu-id="74912-107">Instalace SignalR balíčku klienta .NET</span><span class="sxs-lookup"><span data-stu-id="74912-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="74912-108">[Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Pro klienty rozhraní .NET se vyžaduje balíček klienta, aby se mohl připojit k SignalR rozbočovačům.</span><span class="sxs-lookup"><span data-stu-id="74912-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="74912-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74912-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="74912-110">Chcete-li nainstalovat knihovnu klienta, spusťte následující příkaz v okně **konzoly Správce balíčků** :</span><span class="sxs-lookup"><span data-stu-id="74912-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[<span data-ttu-id="74912-111">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="74912-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="74912-112">Knihovnu klienta nainstalujete spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="74912-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="74912-113">Připojení k centru</span><span class="sxs-lookup"><span data-stu-id="74912-113">Connect to a hub</span></span>

<span data-ttu-id="74912-114">K navázání připojení vytvořte `HubConnectionBuilder` volání a `Build` .</span><span class="sxs-lookup"><span data-stu-id="74912-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="74912-115">Adresa URL centra, protokol, typ přenosu, úroveň protokolu, hlavičky a další možnosti se dají nakonfigurovat při sestavování připojení.</span><span class="sxs-lookup"><span data-stu-id="74912-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="74912-116">Proveďte konfiguraci libovolných požadovaných možností tak, že do nástroje vložíte jakoukoli z těchto `HubConnectionBuilder` metod `Build` .</span><span class="sxs-lookup"><span data-stu-id="74912-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="74912-117">Spusťte připojení pomocí `StartAsync` .</span><span class="sxs-lookup"><span data-stu-id="74912-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="74912-118">Zpracovat ztracené připojení</span><span class="sxs-lookup"><span data-stu-id="74912-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="74912-119">Automaticky znovu připojit</span><span class="sxs-lookup"><span data-stu-id="74912-119">Automatically reconnect</span></span>

<span data-ttu-id="74912-120"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>Lze nakonfigurovat pro automatické opětovné připojení pomocí `WithAutomaticReconnect` metody na <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="74912-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="74912-121">Ve výchozím nastavení se automaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="74912-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="74912-122">Bez parametrů `WithAutomaticReconnect()` nakonfiguruje klienta, aby čekal na 0, 2, 10 a 30 sekund, než se pokusí o každý pokus o opětovné připojení, který se zastaví po čtyřech pokusech o selhání.</span><span class="sxs-lookup"><span data-stu-id="74912-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="74912-123">Před zahájením jakýchkoli pokusů o opětovné připojení `HubConnection` přejde na `HubConnectionState.Reconnecting` stav a aktivuje `Reconnecting` událost.</span><span class="sxs-lookup"><span data-stu-id="74912-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="74912-124">Díky tomu můžete uživatelům upozornit, že připojení bylo ztraceno, a zakázat prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="74912-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="74912-125">Neinteraktivní aplikace můžou spouštět zařazování zpráv do fronty nebo jejich vyřazení.</span><span class="sxs-lookup"><span data-stu-id="74912-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="74912-126">Pokud se klient úspěšně znovu připojí během prvních čtyř pokusů, převede se `HubConnection` zpátky do `Connected` stavu a aktivuje se `Reconnected` událost.</span><span class="sxs-lookup"><span data-stu-id="74912-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="74912-127">To poskytuje možnost informovat uživatele o opětovném vytvoření připojení a vyřazení všech zpráv ve frontě.</span><span class="sxs-lookup"><span data-stu-id="74912-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="74912-128">Vzhledem k tomu, že připojení je zcela nového na serveru, `ConnectionId` bude k dispozici pro `Reconnected` obslužné rutiny událostí nový.</span><span class="sxs-lookup"><span data-stu-id="74912-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="74912-129">`Reconnected`Parametr obslužné rutiny události `connectionId` bude mít hodnotu null, pokud `HubConnection` byl nakonfigurován tak, aby [přeskočil vyjednávání](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="74912-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="74912-130">`WithAutomaticReconnect()`neprovede konfiguraci `HubConnection` pro opakování počátečního spuštění, takže chyby spuštění je nutné zpracovat ručně:</span><span class="sxs-lookup"><span data-stu-id="74912-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="74912-131">Pokud se klient úspěšně znovu nepřipojí během prvních čtyř pokusů, `HubConnection` přejde do `Disconnected` stavu a aktivuje <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> událost.</span><span class="sxs-lookup"><span data-stu-id="74912-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="74912-132">To vám umožní se pokusit o ruční restartování připojení nebo informování uživatelů o trvalé ztrátě připojení.</span><span class="sxs-lookup"><span data-stu-id="74912-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="74912-133">Aby bylo možné nakonfigurovat vlastní počet pokusů o opětovné připojení před odpojením nebo změnou časování pro opětovné připojení, `WithAutomaticReconnect` přijme pole čísel představující zpoždění v milisekundách, které se má počkat, než se spustí pokus o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="74912-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="74912-134">V předchozím příkladu se nakonfiguruje `HubConnection` tak, aby se při pokusu o opětovné připojení ihned po ztrátě připojení spouštěla znovu.</span><span class="sxs-lookup"><span data-stu-id="74912-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="74912-135">To platí také pro výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="74912-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="74912-136">Pokud se při prvním pokusu o opětovné připojení nepovede, druhý pokus o opětovné připojení se spustí hned a místo toho bude čekat 2 sekundy, jako by to bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="74912-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="74912-137">Pokud se druhý pokus o opětovné připojení nepovede, spustí se třetí pokus o opětovné připojení za 10 sekund, který se znovu podobá výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="74912-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="74912-138">Vlastní chování se potom odliší od výchozího chování tím, že se zastaví po neúspěšném pokusu o třetí připojení.</span><span class="sxs-lookup"><span data-stu-id="74912-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="74912-139">Ve výchozí konfiguraci se za dalších 30 sekund pokusí jeden pokus o nové připojení.</span><span class="sxs-lookup"><span data-stu-id="74912-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="74912-140">Pokud chcete ještě větší kontrolu nad časováním a počtem automatických pokusů o opětovné připojení, je třeba `WithAutomaticReconnect` přijmout objekt implementující `IRetryPolicy` rozhraní, které má jedinou metodu s názvem `NextRetryDelay` .</span><span class="sxs-lookup"><span data-stu-id="74912-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="74912-141">`NextRetryDelay`přijímá jeden argument s typem `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="74912-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="74912-142">`RetryContext`Má tři vlastnosti: `PreviousRetryCount` , `ElapsedTime` a `RetryReason` , které jsou a v `long` `TimeSpan` `Exception` uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="74912-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason`, which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="74912-143">Před prvním pokusem o opětovné připojení budou obě `PreviousRetryCount` i `ElapsedTime` nulové a bude `RetryReason` to výjimka, která způsobila ztrátu připojení.</span><span class="sxs-lookup"><span data-stu-id="74912-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="74912-144">Po každém neúspěšném pokusu o opakování se bude `PreviousRetryCount` aktualizovat o jednu, `ElapsedTime` aby odrážela dobu strávenou opětovným připojením, a `RetryReason` bude výjimkou, která způsobila selhání posledního pokusu o opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="74912-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="74912-145">`NextRetryDelay`musí vracet buď hodnotu TimeSpan představující čas čekání před dalším pokusem o opětovné připojení, nebo `null` by se `HubConnection` mělo zastavit opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="74912-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="74912-146">Případně můžete napsat kód, který znovu připojí klienta ručně, jak je znázorněno v [ručním opětovném připojení](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="74912-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="74912-147">Ručně znovu připojit</span><span class="sxs-lookup"><span data-stu-id="74912-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="74912-148">Před 3,0 se klient .NET pro SignalR neautomaticky znovu nepřipojí.</span><span class="sxs-lookup"><span data-stu-id="74912-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="74912-149">Musíte napsat kód, který bude znovu připojit klienta ručně.</span><span class="sxs-lookup"><span data-stu-id="74912-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="74912-150">K <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> reakci na ztracené připojení použijte událost.</span><span class="sxs-lookup"><span data-stu-id="74912-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="74912-151">Můžete například chtít automatizovat opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="74912-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="74912-152">`Closed`Událost vyžaduje delegáta, který vrací `Task` , což umožňuje spuštění asynchronního kódu bez použití `async void` .</span><span class="sxs-lookup"><span data-stu-id="74912-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="74912-153">Pro uspokojení signatury delegáta v `Closed` obslužné rutině události, která běží synchronně, vraťte `Task.CompletedTask` :</span><span class="sxs-lookup"><span data-stu-id="74912-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="74912-154">Hlavním důvodem pro asynchronní podporu je, že můžete připojení restartovat.</span><span class="sxs-lookup"><span data-stu-id="74912-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="74912-155">Spuštění připojení je asynchronní akce.</span><span class="sxs-lookup"><span data-stu-id="74912-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="74912-156">V `Closed` obslužné rutině, která restartuje připojení, zvažte možnost počkat na určité náhodné zpoždění, aby se zabránilo přetížení serveru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="74912-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="74912-157">Volání metod centra z klienta</span><span class="sxs-lookup"><span data-stu-id="74912-157">Call hub methods from client</span></span>

<span data-ttu-id="74912-158">`InvokeAsync`volá metody v centru.</span><span class="sxs-lookup"><span data-stu-id="74912-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="74912-159">Předat název metody centra a všechny argumenty definované v metodě hub pro `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="74912-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="74912-160">SignalRje asynchronní, takže použijte `async` a `await` při provádění volání.</span><span class="sxs-lookup"><span data-stu-id="74912-160">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="74912-161">`InvokeAsync`Metoda vrátí hodnotu, `Task` která je dokončena, když metoda serveru vrátí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="74912-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="74912-162">Návratová hodnota, pokud existuje, je k dispozici jako výsledek `Task` .</span><span class="sxs-lookup"><span data-stu-id="74912-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="74912-163">Jakékoli výjimky vyvolané metodou na serveru vyvolávají chybu `Task` .</span><span class="sxs-lookup"><span data-stu-id="74912-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="74912-164">Použijte `await` syntaxi pro čekání na dokončení metody serveru a `try...catch` syntaxi pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="74912-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="74912-165">`SendAsync`Metoda vrátí, `Task` která se dokončí při odeslání zprávy na server.</span><span class="sxs-lookup"><span data-stu-id="74912-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="74912-166">Žádná návratová hodnota není k dispozici `Task` , protože nečeká na dokončení metody serveru.</span><span class="sxs-lookup"><span data-stu-id="74912-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="74912-167">Jakékoli výjimky vyvolané u klienta při odeslání zprávy způsobují chybu `Task` .</span><span class="sxs-lookup"><span data-stu-id="74912-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="74912-168">Použijte `await` `try...catch` syntaxi a ke zpracování chyb odesílání.</span><span class="sxs-lookup"><span data-stu-id="74912-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="74912-169">Pokud používáte SignalR službu Azure v režimu bez *serveru*, nemůžete volat metody centra z klienta.</span><span class="sxs-lookup"><span data-stu-id="74912-169">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="74912-170">Další informace najdete v dokumentaci ke [ SignalR službě](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="74912-170">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="74912-171">Volání metod klienta z centra</span><span class="sxs-lookup"><span data-stu-id="74912-171">Call client methods from hub</span></span>

<span data-ttu-id="74912-172">Definujte metody, které volání centra použije `connection.On` po sestavení, ale před zahájením připojení.</span><span class="sxs-lookup"><span data-stu-id="74912-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="74912-173">Předchozí kód v aplikaci se `connection.On` spustí, když kód na straně serveru volá `SendAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="74912-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="74912-174">Zpracování chyb a protokolování</span><span class="sxs-lookup"><span data-stu-id="74912-174">Error handling and logging</span></span>

<span data-ttu-id="74912-175">Zpracování chyb pomocí příkazu try-catch.</span><span class="sxs-lookup"><span data-stu-id="74912-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="74912-176">Zkontrolujte `Exception` objekt a určete správnou akci, která se má provést po výskytu chyby.</span><span class="sxs-lookup"><span data-stu-id="74912-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="74912-177">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="74912-177">Additional resources</span></span>

* [<span data-ttu-id="74912-178">Centra</span><span class="sxs-lookup"><span data-stu-id="74912-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="74912-179">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="74912-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="74912-180">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="74912-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="74912-181">SignalRDokumentace k serveru se službou Azure</span><span class="sxs-lookup"><span data-stu-id="74912-181">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

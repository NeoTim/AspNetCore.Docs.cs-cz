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
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="aa704-103">Klient .NET funkce SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa704-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="aa704-104">Klientská knihovna .NET funkce SignalR technologie ASP.NET Core umožňuje komunikovat s rozbočovače SignalR z aplikací .NET.</span><span class="sxs-lookup"><span data-stu-id="aa704-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="aa704-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa704-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="aa704-106">Vzorový kód v tomto článku je aplikace WPF, která používá klienta .NET funkce SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="aa704-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="aa704-107">Instalace balíčku pro klienta SignalR .NET</span><span class="sxs-lookup"><span data-stu-id="aa704-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="aa704-108">`Microsoft.AspNetCore.SignalR.Client` Balíčku je potřeba pro klienty .NET pro připojení k rozbočovačům SignalR.</span><span class="sxs-lookup"><span data-stu-id="aa704-108">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="aa704-109">Pokud chcete nainstalovat klientské knihovny, spusťte následující příkaz **Konzola správce balíčků** okno:</span><span class="sxs-lookup"><span data-stu-id="aa704-109">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="aa704-110">Připojení k rozbočovači</span><span class="sxs-lookup"><span data-stu-id="aa704-110">Connect to a hub</span></span>

<span data-ttu-id="aa704-111">K navázání připojení, vytvoření `HubConnectionBuilder` a volat `Build`.</span><span class="sxs-lookup"><span data-stu-id="aa704-111">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="aa704-112">Adresa URL rozbočovače, protokol, typ přenosu, úroveň protokolu, záhlaví a další možnosti je možné nakonfigurovat při vytváření připojení.</span><span class="sxs-lookup"><span data-stu-id="aa704-112">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="aa704-113">Nakonfigurujte veškeré požadované možnosti vložíte-li některý z `HubConnectionBuilder` metod do `Build`.</span><span class="sxs-lookup"><span data-stu-id="aa704-113">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="aa704-114">Zahájit připojení s `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="aa704-114">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="aa704-115">Zpracování došlo ke ztrátě připojení</span><span class="sxs-lookup"><span data-stu-id="aa704-115">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="aa704-116">Automaticky znovu připojila</span><span class="sxs-lookup"><span data-stu-id="aa704-116">Automatically reconnect</span></span>

<span data-ttu-id="aa704-117"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> Umožňují automaticky znovu připojila, pomocí `WithAutomaticReconnect` metodu <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span><span class="sxs-lookup"><span data-stu-id="aa704-117">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="aa704-118">Připojení nebude automaticky ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="aa704-118">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="aa704-119">Bez parametrů `WithAutomaticReconnect()` nakonfiguruje klienta na 0, 2, 10 a 30 sekund, resp. před pokusem o každý pokus o volání metody reconnect, zastavování po čtyři neúspěšných pokusech o čekání.</span><span class="sxs-lookup"><span data-stu-id="aa704-119">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="aa704-120">Před zahájením jakékoli pokusy o volání metody reconnect `HubConnection` se přechod na `HubConnectionState.Reconnecting` stav a vyvolat `Reconnecting` událostí.</span><span class="sxs-lookup"><span data-stu-id="aa704-120">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="aa704-121">To představuje příležitost a upozornit uživatele, že připojení bylo ztraceno zakážete prvky uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="aa704-121">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="aa704-122">Neinteraktivní aplikace spustit služby Řízení front nebo vyřazením zprávy.</span><span class="sxs-lookup"><span data-stu-id="aa704-122">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="aa704-123">Pokud klient úspěšně obnoví v rámci své první čtyři pokusy o `HubConnection` přejde zpět `Connected` stav a vyvolat `Reconnected` událostí.</span><span class="sxs-lookup"><span data-stu-id="aa704-123">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="aa704-124">To představuje příležitost k informování uživatelů připojení doručena a vyřazovat je z ní všechny zprávy ve frontě.</span><span class="sxs-lookup"><span data-stu-id="aa704-124">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="aa704-125">Vzhledem k tomu, že připojení ověří zcela nový server, nový `ConnectionId` vám poskytneme `Reconnected` obslužných rutin událostí.</span><span class="sxs-lookup"><span data-stu-id="aa704-125">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="aa704-126">`Reconnected` Obslužná rutina události `connectionId` parametr bude mít hodnotu null Pokud `HubConnection` nastavený tak, aby [přeskočit vyjednávání](xref:signalr/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="aa704-126">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="aa704-127">`WithAutomaticReconnect()` nenakonfiguruje `HubConnection` pokus o selhání prvního spuštění, budou muset ručně zpracovat selhání spuštění:</span><span class="sxs-lookup"><span data-stu-id="aa704-127">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="aa704-128">Pokud klient nebude znovu připojit úspěšně v rámci své první čtyři pokusy o `HubConnection` se přechod `Disconnected` stav a vyvolat <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> událostí.</span><span class="sxs-lookup"><span data-stu-id="aa704-128">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="aa704-129">To poskytuje možnost pokusit se o připojení ručně restartovat nebo informování uživatelů o tom, že připojení bylo trvale ztratí.</span><span class="sxs-lookup"><span data-stu-id="aa704-129">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="aa704-130">Pokud chcete nakonfigurovat vlastní počet pokusů o nové připojení před odpojením nebo změnit časování volání metody reconnect `WithAutomaticReconnect` přijímá pole čísel představující zpoždění v milisekundách pro čekání před zahájením každý pokus o volání metody reconnect.</span><span class="sxs-lookup"><span data-stu-id="aa704-130">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="aa704-131">Předchozí příklad nakonfiguruje `HubConnection` spustit okamžitě, jakmile dojde ke ztrátě připojení k pokusu o připojování.</span><span class="sxs-lookup"><span data-stu-id="aa704-131">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="aa704-132">To platí také pro výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="aa704-132">This is also true for the default configuration.</span></span>

<span data-ttu-id="aa704-133">Pokud první pokus o volání metody reconnect se nezdaří, druhý pokus o volání metody reconnect také začne okamžitě nečekáte, než 2 sekundy, jako by tomu bylo ve výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="aa704-133">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="aa704-134">Pokud se druhý pokus o volání metody reconnect se nezdaří, třetí pokus o volání metody reconnect se spustí za 10 sekund, což je znovu jako výchozí konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="aa704-134">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="aa704-135">Vlastní chování pak diverges znovu výchozí chování zastavením po selhání pokusu třetí volání metody reconnect.</span><span class="sxs-lookup"><span data-stu-id="aa704-135">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="aa704-136">Ve výchozím nastavení existuje by být jeden více znovu připojit pokus o jiné 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="aa704-136">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="aa704-137">Pokud chcete, aby ještě větší kontrolu nad načasování a počet automaticky pokusů `WithAutomaticReconnect` přijímá implementaci objektu `IRetryPolicy` rozhraní, které obsahuje jedinou metodu s názvem `NextRetryDelay`.</span><span class="sxs-lookup"><span data-stu-id="aa704-137">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="aa704-138">`NextRetryDelay` přijímá jeden argument s typem `RetryContext`.</span><span class="sxs-lookup"><span data-stu-id="aa704-138">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="aa704-139">`RetryContext` Má tři vlastnosti: `PreviousRetryCount`, `ElapsedTime` a `RetryReason` které jsou `long`, `TimeSpan` a `Exception` v uvedeném pořadí.</span><span class="sxs-lookup"><span data-stu-id="aa704-139">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason` which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="aa704-140">Před první pokus o volání metody reconnect obě `PreviousRetryCount` a `ElapsedTime` bude nula a `RetryReason` bude výjimka, která způsobila ke ztrátě připojení.</span><span class="sxs-lookup"><span data-stu-id="aa704-140">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="aa704-141">Po každý pokus o opakování neúspěšných `PreviousRetryCount` se zvýší o jedna, `ElapsedTime` bude aktualizován tak, aby odrážely množství času stráveného zatím, opětovné připojení a `RetryReason` bude výjimka, která poslední pokus o volání metody reconnect se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="aa704-141">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="aa704-142">`NextRetryDelay` musí vrátit buď časový interval představující dobu čekání před dalším pokusem volání metody reconnect nebo `null` Pokud `HubConnection` by se měla zastavit, opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="aa704-142">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="aa704-143">Alternativně můžete napsat kód, který se znovu připojit klientu ručně, jak je ukázáno v [ručně připojit](#manually-reconnect).</span><span class="sxs-lookup"><span data-stu-id="aa704-143">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="aa704-144">Ručně připojit</span><span class="sxs-lookup"><span data-stu-id="aa704-144">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="aa704-145">Než 3.0 klient .NET pro funkci SignalR nebude automaticky znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="aa704-145">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="aa704-146">Musíte napsat kód, který se znovu připojit klientu ručně.</span><span class="sxs-lookup"><span data-stu-id="aa704-146">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="aa704-147">Použití <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> události a reagovat na došlo ke ztrátě připojení.</span><span class="sxs-lookup"><span data-stu-id="aa704-147">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="aa704-148">Můžete například chtít automatizovat opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="aa704-148">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="aa704-149">`Closed` Událost vyžaduje delegáta, který vrátí `Task`, což umožňuje asynchronní kód ke spuštění bez použití `async void`.</span><span class="sxs-lookup"><span data-stu-id="aa704-149">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="aa704-150">Tím se uspokojí delegáta v `Closed` obslužná rutina události, která spustí synchronně, vracet `Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="aa704-150">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="aa704-151">Hlavním důvodem pro asynchronní podporu je tak můžete restartovat připojení.</span><span class="sxs-lookup"><span data-stu-id="aa704-151">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="aa704-152">Spouští se připojení je asynchronní akce.</span><span class="sxs-lookup"><span data-stu-id="aa704-152">Starting a connection is an async action.</span></span>

<span data-ttu-id="aa704-153">V `Closed` obslužná rutina, která restartuje připojení, vezměte v úvahu časový limit na některé náhodné zpoždění, aby se zabránilo přetížení serveru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="aa704-153">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="aa704-154">Volání metod rozbočovače na z klienta</span><span class="sxs-lookup"><span data-stu-id="aa704-154">Call hub methods from client</span></span>

<span data-ttu-id="aa704-155">`InvokeAsync` volání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="aa704-155">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="aa704-156">Předat název metody rozbočovače a argumentů podle metody rozbočovače na `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="aa704-156">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="aa704-157">SignalR je asynchronní, proto `async` a `await` při volání.</span><span class="sxs-lookup"><span data-stu-id="aa704-157">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="aa704-158">`InvokeAsync` Metoda vrátí hodnotu `Task` která se dokončí po návratu metody serveru.</span><span class="sxs-lookup"><span data-stu-id="aa704-158">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="aa704-159">Návratovou hodnotu, pokud existuje, se poskytuje jako výsledek `Task`.</span><span class="sxs-lookup"><span data-stu-id="aa704-159">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="aa704-160">Jakékoli výjimky vyvolané metodou na serveru vytvoří chybovém `Task`.</span><span class="sxs-lookup"><span data-stu-id="aa704-160">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="aa704-161">Použití `await` syntaxe čekat metody serveru k dokončení a `try...catch` syntaxe zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="aa704-161">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="aa704-162">`SendAsync` Metoda vrátí hodnotu `Task` která se dokončí, když zpráva byla odeslána na server.</span><span class="sxs-lookup"><span data-stu-id="aa704-162">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="aa704-163">Není zadána žádná návratová hodnota od to `Task` nečeká metoda server dokončí.</span><span class="sxs-lookup"><span data-stu-id="aa704-163">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="aa704-164">Vytvoření jakýchkoli výjimek na straně klienta při odesílání zprávy chybovém `Task`.</span><span class="sxs-lookup"><span data-stu-id="aa704-164">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="aa704-165">Použití `await` a `try...catch` odeslání syntaxe pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="aa704-165">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="aa704-166">Pokud používáte služby Azure SignalR v *bez serveru režimu*, nelze volat metody rozbočovače klienta.</span><span class="sxs-lookup"><span data-stu-id="aa704-166">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="aa704-167">Další informace najdete v tématu [dokumentace ke službě SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="aa704-167">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="aa704-168">Volání metody klienta od rozbočovače</span><span class="sxs-lookup"><span data-stu-id="aa704-168">Call client methods from hub</span></span>

<span data-ttu-id="aa704-169">Definovat metody rozbočovače volá pomocí `connection.On` po sestavení, ale před spuštěním připojení.</span><span class="sxs-lookup"><span data-stu-id="aa704-169">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="aa704-170">Předchozí kód v `connection.On` spustí, když kód na straně serveru pomocí volání `SendAsync` metoda.</span><span class="sxs-lookup"><span data-stu-id="aa704-170">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="aa704-171">Protokolování a zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="aa704-171">Error handling and logging</span></span>

<span data-ttu-id="aa704-172">Zpracování chyb pomocí příkazu try-catch.</span><span class="sxs-lookup"><span data-stu-id="aa704-172">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="aa704-173">Zkontrolujte `Exception` objektu určit správnou akce má být provedena, když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="aa704-173">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="aa704-174">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="aa704-174">Additional resources</span></span>

* [<span data-ttu-id="aa704-175">Centra</span><span class="sxs-lookup"><span data-stu-id="aa704-175">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="aa704-176">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="aa704-176">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="aa704-177">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="aa704-177">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="aa704-178">Dokumentace Azure bez serveru služby SignalR</span><span class="sxs-lookup"><span data-stu-id="aa704-178">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

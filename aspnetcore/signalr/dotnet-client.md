---
title: Klient .NET funkce SignalR technologie ASP.NET Core
author: bradygaster
description: Informace o .NET klienta SignalR technologie ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: b59af0f9c84a008f778709970dba2273abdfcd4f
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087720"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="03d73-103">Klient .NET funkce SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03d73-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="03d73-104">Klientská knihovna .NET funkce SignalR technologie ASP.NET Core umožňuje komunikovat s rozbočovače SignalR z aplikací .NET.</span><span class="sxs-lookup"><span data-stu-id="03d73-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="03d73-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="03d73-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="03d73-106">Vzorový kód v tomto článku je aplikace WPF, která používá klienta .NET funkce SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="03d73-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="03d73-107">Instalace balíčku pro klienta SignalR .NET</span><span class="sxs-lookup"><span data-stu-id="03d73-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="03d73-108">`Microsoft.AspNetCore.SignalR.Client` Balíčku je potřeba pro klienty .NET pro připojení k rozbočovačům SignalR.</span><span class="sxs-lookup"><span data-stu-id="03d73-108">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="03d73-109">Pokud chcete nainstalovat klientské knihovny, spusťte následující příkaz **Konzola správce balíčků** okno:</span><span class="sxs-lookup"><span data-stu-id="03d73-109">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="03d73-110">Připojení k rozbočovači</span><span class="sxs-lookup"><span data-stu-id="03d73-110">Connect to a hub</span></span>

<span data-ttu-id="03d73-111">K navázání připojení, vytvoření `HubConnectionBuilder` a volat `Build`.</span><span class="sxs-lookup"><span data-stu-id="03d73-111">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="03d73-112">Adresa URL rozbočovače, protokol, typ přenosu, úroveň protokolu, záhlaví a další možnosti je možné nakonfigurovat při vytváření připojení.</span><span class="sxs-lookup"><span data-stu-id="03d73-112">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="03d73-113">Nakonfigurujte veškeré požadované možnosti vložíte-li některý z `HubConnectionBuilder` metod do `Build`.</span><span class="sxs-lookup"><span data-stu-id="03d73-113">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="03d73-114">Zahájit připojení s `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="03d73-114">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="03d73-115">Zpracování došlo ke ztrátě připojení</span><span class="sxs-lookup"><span data-stu-id="03d73-115">Handle lost connection</span></span>

<span data-ttu-id="03d73-116">Použití <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> události a reagovat na došlo ke ztrátě připojení.</span><span class="sxs-lookup"><span data-stu-id="03d73-116">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="03d73-117">Můžete například chtít automatizovat opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="03d73-117">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="03d73-118">`Closed` Událost vyžaduje delegáta, který vrátí `Task`, což umožňuje asynchronní kód ke spuštění bez použití `async void`.</span><span class="sxs-lookup"><span data-stu-id="03d73-118">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="03d73-119">Tím se uspokojí delegáta v `Closed` obslužná rutina události, která spustí synchronně, vracet `Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="03d73-119">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="03d73-120">Hlavním důvodem pro asynchronní podporu je tak můžete restartovat připojení.</span><span class="sxs-lookup"><span data-stu-id="03d73-120">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="03d73-121">Spouští se připojení je asynchronní akce.</span><span class="sxs-lookup"><span data-stu-id="03d73-121">Starting a connection is an async action.</span></span>

<span data-ttu-id="03d73-122">V `Closed` obslužná rutina, která restartuje připojení, vezměte v úvahu časový limit na některé náhodné zpoždění, aby se zabránilo přetížení serveru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="03d73-122">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="03d73-123">Volání metod rozbočovače na z klienta</span><span class="sxs-lookup"><span data-stu-id="03d73-123">Call hub methods from client</span></span>

<span data-ttu-id="03d73-124">`InvokeAsync` volání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="03d73-124">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="03d73-125">Předat název metody rozbočovače a argumentů podle metody rozbočovače na `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="03d73-125">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="03d73-126">SignalR je asynchronní, proto `async` a `await` při volání.</span><span class="sxs-lookup"><span data-stu-id="03d73-126">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="03d73-127">`InvokeAsync` Metoda vrátí hodnotu `Task` která se dokončí po návratu metody serveru.</span><span class="sxs-lookup"><span data-stu-id="03d73-127">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="03d73-128">Návratovou hodnotu, pokud existuje, se poskytuje jako výsledek `Task`.</span><span class="sxs-lookup"><span data-stu-id="03d73-128">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="03d73-129">Jakékoli výjimky vyvolané metodou na serveru vytvoří chybovém `Task`.</span><span class="sxs-lookup"><span data-stu-id="03d73-129">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="03d73-130">Použití `await` syntaxe čekat metody serveru k dokončení a `try...catch` syntaxe zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="03d73-130">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="03d73-131">`SendAsync` Metoda vrátí hodnotu `Task` která se dokončí, když zpráva byla odeslána na server.</span><span class="sxs-lookup"><span data-stu-id="03d73-131">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="03d73-132">Není zadána žádná návratová hodnota od to `Task` nečeká metoda server dokončí.</span><span class="sxs-lookup"><span data-stu-id="03d73-132">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="03d73-133">Vytvoření jakýchkoli výjimek na straně klienta při odesílání zprávy chybovém `Task`.</span><span class="sxs-lookup"><span data-stu-id="03d73-133">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="03d73-134">Použití `await` a `try...catch` odeslání syntaxe pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="03d73-134">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="03d73-135">Pokud používáte služby Azure SignalR v *bez serveru režimu*, nelze volat metody rozbočovače klienta.</span><span class="sxs-lookup"><span data-stu-id="03d73-135">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="03d73-136">Další informace najdete v tématu [dokumentace ke službě SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="03d73-136">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="03d73-137">Volání metody klienta od rozbočovače</span><span class="sxs-lookup"><span data-stu-id="03d73-137">Call client methods from hub</span></span>

<span data-ttu-id="03d73-138">Definovat metody rozbočovače volá pomocí `connection.On` po sestavení, ale před spuštěním připojení.</span><span class="sxs-lookup"><span data-stu-id="03d73-138">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="03d73-139">Předchozí kód v `connection.On` spustí, když kód na straně serveru pomocí volání `SendAsync` metoda.</span><span class="sxs-lookup"><span data-stu-id="03d73-139">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="03d73-140">Protokolování a zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="03d73-140">Error handling and logging</span></span>

<span data-ttu-id="03d73-141">Zpracování chyb pomocí příkazu try-catch.</span><span class="sxs-lookup"><span data-stu-id="03d73-141">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="03d73-142">Zkontrolujte `Exception` objektu určit správnou akce má být provedena, když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="03d73-142">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="03d73-143">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="03d73-143">Additional resources</span></span>

* [<span data-ttu-id="03d73-144">Centra</span><span class="sxs-lookup"><span data-stu-id="03d73-144">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="03d73-145">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="03d73-145">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="03d73-146">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="03d73-146">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="03d73-147">Dokumentace Azure bez serveru služby SignalR</span><span class="sxs-lookup"><span data-stu-id="03d73-147">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

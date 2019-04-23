---
title: Klient .NET funkce SignalR technologie ASP.NET Core
author: bradygaster
description: Informace o .NET klienta SignalR technologie ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: 640d75157e42ffa6d78235c5be03e4846e8dcde9
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982943"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="a972a-103">Klient .NET funkce SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a972a-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="a972a-104">Klientská knihovna .NET funkce SignalR technologie ASP.NET Core umožňuje komunikovat s rozbočovače SignalR z aplikací .NET.</span><span class="sxs-lookup"><span data-stu-id="a972a-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

> [!NOTE]
> <span data-ttu-id="a972a-105">Xamarin nabízí zvláštní požadavky na verzi sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a972a-105">Xamarin has special requirements for Visual Studio version.</span></span> <span data-ttu-id="a972a-106">Další informace najdete v tématu [klienta SignalR 2.1.1 v Xamarinu](https://github.com/aspnet/Announcements/issues/305).</span><span class="sxs-lookup"><span data-stu-id="a972a-106">For more information, see [SignalR Client 2.1.1 in Xamarin](https://github.com/aspnet/Announcements/issues/305).</span></span>

<span data-ttu-id="a972a-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a972a-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a972a-108">Vzorový kód v tomto článku je aplikace WPF, která používá klienta .NET funkce SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a972a-108">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="a972a-109">Instalace balíčku pro klienta SignalR .NET</span><span class="sxs-lookup"><span data-stu-id="a972a-109">Install the SignalR .NET client package</span></span>

<span data-ttu-id="a972a-110">`Microsoft.AspNetCore.SignalR.Client` Balíčku je potřeba pro klienty .NET pro připojení k rozbočovačům SignalR.</span><span class="sxs-lookup"><span data-stu-id="a972a-110">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="a972a-111">Pokud chcete nainstalovat klientské knihovny, spusťte následující příkaz **Konzola správce balíčků** okno:</span><span class="sxs-lookup"><span data-stu-id="a972a-111">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="a972a-112">Připojení k rozbočovači</span><span class="sxs-lookup"><span data-stu-id="a972a-112">Connect to a hub</span></span>

<span data-ttu-id="a972a-113">K navázání připojení, vytvoření `HubConnectionBuilder` a volat `Build`.</span><span class="sxs-lookup"><span data-stu-id="a972a-113">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="a972a-114">Adresa URL rozbočovače, protokol, typ přenosu, úroveň protokolu, záhlaví a další možnosti je možné nakonfigurovat při vytváření připojení.</span><span class="sxs-lookup"><span data-stu-id="a972a-114">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="a972a-115">Nakonfigurujte veškeré požadované možnosti vložíte-li některý z `HubConnectionBuilder` metod do `Build`.</span><span class="sxs-lookup"><span data-stu-id="a972a-115">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="a972a-116">Zahájit připojení s `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="a972a-116">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="a972a-117">Zpracování došlo ke ztrátě připojení</span><span class="sxs-lookup"><span data-stu-id="a972a-117">Handle lost connection</span></span>

<span data-ttu-id="a972a-118">Použití <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> události a reagovat na došlo ke ztrátě připojení.</span><span class="sxs-lookup"><span data-stu-id="a972a-118">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="a972a-119">Můžete například chtít automatizovat opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="a972a-119">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="a972a-120">`Closed` Událost vyžaduje delegáta, který vrátí `Task`, což umožňuje asynchronní kód ke spuštění bez použití `async void`.</span><span class="sxs-lookup"><span data-stu-id="a972a-120">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="a972a-121">Tím se uspokojí delegáta v `Closed` obslužná rutina události, která spustí synchronně, vracet `Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="a972a-121">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="a972a-122">Hlavním důvodem pro asynchronní podporu je tak můžete restartovat připojení.</span><span class="sxs-lookup"><span data-stu-id="a972a-122">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="a972a-123">Spouští se připojení je asynchronní akce.</span><span class="sxs-lookup"><span data-stu-id="a972a-123">Starting a connection is an async action.</span></span>

<span data-ttu-id="a972a-124">V `Closed` obslužná rutina, která restartuje připojení, vezměte v úvahu časový limit na některé náhodné zpoždění, aby se zabránilo přetížení serveru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="a972a-124">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="a972a-125">Volání metod rozbočovače na z klienta</span><span class="sxs-lookup"><span data-stu-id="a972a-125">Call hub methods from client</span></span>

<span data-ttu-id="a972a-126">`InvokeAsync` volání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="a972a-126">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="a972a-127">Předat název metody rozbočovače a argumentů podle metody rozbočovače na `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="a972a-127">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="a972a-128">SignalR je asynchronní, proto `async` a `await` při volání.</span><span class="sxs-lookup"><span data-stu-id="a972a-128">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="a972a-129">`InvokeAsync` Metoda vrátí hodnotu `Task` která se dokončí po návratu metody serveru.</span><span class="sxs-lookup"><span data-stu-id="a972a-129">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="a972a-130">Návratovou hodnotu, pokud existuje, se poskytuje jako výsledek `Task`.</span><span class="sxs-lookup"><span data-stu-id="a972a-130">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="a972a-131">Jakékoli výjimky vyvolané metodou na serveru vytvoří chybovém `Task`.</span><span class="sxs-lookup"><span data-stu-id="a972a-131">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="a972a-132">Použití `await` syntaxe čekat metody serveru k dokončení a `try...catch` syntaxe zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="a972a-132">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="a972a-133">`SendAsync` Metoda vrátí hodnotu `Task` která se dokončí, když zpráva byla odeslána na server.</span><span class="sxs-lookup"><span data-stu-id="a972a-133">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="a972a-134">Není zadána žádná návratová hodnota od to `Task` nečeká metoda server dokončí.</span><span class="sxs-lookup"><span data-stu-id="a972a-134">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="a972a-135">Vytvoření jakýchkoli výjimek na straně klienta při odesílání zprávy chybovém `Task`.</span><span class="sxs-lookup"><span data-stu-id="a972a-135">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="a972a-136">Použití `await` a `try...catch` odeslání syntaxe pro zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="a972a-136">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="a972a-137">Pokud používáte služby Azure SignalR v *bez serveru režimu*, nelze volat metody rozbočovače klienta.</span><span class="sxs-lookup"><span data-stu-id="a972a-137">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="a972a-138">Další informace najdete v tématu [dokumentace ke službě SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="a972a-138">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="a972a-139">Volání metody klienta od rozbočovače</span><span class="sxs-lookup"><span data-stu-id="a972a-139">Call client methods from hub</span></span>

<span data-ttu-id="a972a-140">Definovat metody rozbočovače volá pomocí `connection.On` po sestavení, ale před spuštěním připojení.</span><span class="sxs-lookup"><span data-stu-id="a972a-140">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="a972a-141">Předchozí kód v `connection.On` spustí, když kód na straně serveru pomocí volání `SendAsync` metoda.</span><span class="sxs-lookup"><span data-stu-id="a972a-141">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="a972a-142">Protokolování a zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="a972a-142">Error handling and logging</span></span>

<span data-ttu-id="a972a-143">Zpracování chyb pomocí příkazu try-catch.</span><span class="sxs-lookup"><span data-stu-id="a972a-143">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="a972a-144">Zkontrolujte `Exception` objektu určit správnou akce má být provedena, když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="a972a-144">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="a972a-145">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a972a-145">Additional resources</span></span>

* [<span data-ttu-id="a972a-146">Centra</span><span class="sxs-lookup"><span data-stu-id="a972a-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a972a-147">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="a972a-147">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="a972a-148">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="a972a-148">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="a972a-149">Dokumentace Azure bez serveru služby SignalR</span><span class="sxs-lookup"><span data-stu-id="a972a-149">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

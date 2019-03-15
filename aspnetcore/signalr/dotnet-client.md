---
title: Klient .NET funkce SignalR technologie ASP.NET Core
author: bradygaster
description: Informace o .NET klienta SignalR technologie ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 03/14/2019
uid: signalr/dotnet-client
ms.openlocfilehash: a03abef53aa44f0a1016b8f72d8e3a7af2f9bed1
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978301"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="bc8d6-103">Klient .NET funkce SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc8d6-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="bc8d6-104">Klientská knihovna .NET funkce SignalR technologie ASP.NET Core umožňuje komunikovat s rozbočovače SignalR z aplikací .NET.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

> [!NOTE]
> <span data-ttu-id="bc8d6-105">Xamarin nabízí zvláštní požadavky na verzi sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-105">Xamarin has special requirements for Visual Studio version.</span></span> <span data-ttu-id="bc8d6-106">Další informace najdete v tématu [klienta SignalR 2.1.1 v Xamarinu](https://github.com/aspnet/Announcements/issues/305).</span><span class="sxs-lookup"><span data-stu-id="bc8d6-106">For more information, see [SignalR Client 2.1.1 in Xamarin](https://github.com/aspnet/Announcements/issues/305).</span></span>

<span data-ttu-id="bc8d6-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bc8d6-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bc8d6-108">Vzorový kód v tomto článku je aplikace WPF, která používá klienta .NET funkce SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-108">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="bc8d6-109">Instalace balíčku pro klienta SignalR .NET</span><span class="sxs-lookup"><span data-stu-id="bc8d6-109">Install the SignalR .NET client package</span></span>

<span data-ttu-id="bc8d6-110">`Microsoft.AspNetCore.SignalR.Client` Balíčku je potřeba pro klienty .NET pro připojení k rozbočovačům SignalR.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-110">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="bc8d6-111">Pokud chcete nainstalovat klientské knihovny, spusťte následující příkaz **Konzola správce balíčků** okno:</span><span class="sxs-lookup"><span data-stu-id="bc8d6-111">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="bc8d6-112">Připojení k rozbočovači</span><span class="sxs-lookup"><span data-stu-id="bc8d6-112">Connect to a hub</span></span>

<span data-ttu-id="bc8d6-113">K navázání připojení, vytvoření `HubConnectionBuilder` a volat `Build`.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-113">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="bc8d6-114">Adresa URL rozbočovače, protokol, typ přenosu, úroveň protokolu, záhlaví a další možnosti je možné nakonfigurovat při vytváření připojení.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-114">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="bc8d6-115">Nakonfigurujte veškeré požadované možnosti vložíte-li některý z `HubConnectionBuilder` metod do `Build`.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-115">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="bc8d6-116">Zahájit připojení s `StartAsync`.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-116">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="bc8d6-117">Zpracování došlo ke ztrátě připojení</span><span class="sxs-lookup"><span data-stu-id="bc8d6-117">Handle lost connection</span></span>

<span data-ttu-id="bc8d6-118">Použití <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> události a reagovat na došlo ke ztrátě připojení.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-118">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="bc8d6-119">Můžete například chtít automatizovat opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-119">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="bc8d6-120">`Closed` Událost vyžaduje delegáta, který vrátí `Task`, což umožňuje asynchronní kód ke spuštění bez použití `async void`.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-120">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="bc8d6-121">Tím se uspokojí delegáta v `Closed` obslužná rutina události, která spustí synchronně, vracet `Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="bc8d6-121">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="bc8d6-122">Hlavním důvodem pro asynchronní podporu je tak můžete restartovat připojení.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-122">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="bc8d6-123">Spouští se připojení je asynchronní akce.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-123">Starting a connection is an async action.</span></span>

<span data-ttu-id="bc8d6-124">V `Closed` obslužná rutina, která restartuje připojení, vezměte v úvahu časový limit na některé náhodné zpoždění, aby se zabránilo přetížení serveru, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bc8d6-124">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="bc8d6-125">Volání metod rozbočovače na z klienta</span><span class="sxs-lookup"><span data-stu-id="bc8d6-125">Call hub methods from client</span></span>

<span data-ttu-id="bc8d6-126">`InvokeAsync` volání metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-126">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="bc8d6-127">Předat název metody rozbočovače a argumentů podle metody rozbočovače na `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-127">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="bc8d6-128">SignalR je asynchronní, proto `async` a `await` při volání.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-128">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

> [!NOTE]
> <span data-ttu-id="bc8d6-129">Pokud používáte služby Azure SignalR v *bez serveru režimu*, nelze volat metody rozbočovače klienta.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-129">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="bc8d6-130">Další informace najdete v tématu [dokumentace ke službě SignalR](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="bc8d6-130">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="bc8d6-131">Volání metody klienta od rozbočovače</span><span class="sxs-lookup"><span data-stu-id="bc8d6-131">Call client methods from hub</span></span>

<span data-ttu-id="bc8d6-132">Definovat metody rozbočovače volá pomocí `connection.On` po sestavení, ale před spuštěním připojení.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-132">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="bc8d6-133">Předchozí kód v `connection.On` spustí, když kód na straně serveru pomocí volání `SendAsync` metoda.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-133">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="bc8d6-134">Protokolování a zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="bc8d6-134">Error handling and logging</span></span>

<span data-ttu-id="bc8d6-135">Zpracování chyb pomocí příkazu try-catch.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-135">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="bc8d6-136">Zkontrolujte `Exception` objektu určit správnou akce má být provedena, když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="bc8d6-136">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="bc8d6-137">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bc8d6-137">Additional resources</span></span>

* [<span data-ttu-id="bc8d6-138">Centra</span><span class="sxs-lookup"><span data-stu-id="bc8d6-138">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="bc8d6-139">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="bc8d6-139">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="bc8d6-140">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="bc8d6-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="bc8d6-141">Dokumentace Azure bez serveru služby SignalR</span><span class="sxs-lookup"><span data-stu-id="bc8d6-141">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

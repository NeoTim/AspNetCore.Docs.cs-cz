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

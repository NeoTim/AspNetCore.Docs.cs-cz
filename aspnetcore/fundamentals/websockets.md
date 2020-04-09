---
title: Podpora websockets v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak začít s WebSockets v ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: a8040003374906fd93e12c9fde44c4a5ccc2cf37
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655783"
---
# <a name="websockets-support-in-aspnet-core"></a>Podpora websockets v ASP.NET Core

Tom [Dykstra](https://github.com/tdykstra) a [Andrew Stanton-Sestra](https://github.com/anurse)

Tento článek vysvětluje, jak začít s WebSockets v ASP.NET Core. [WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) je protokol, který umožňuje obousměrné trvalé komunikační kanály přes připojení TCP. Používá se v aplikacích, které využívají rychlou komunikaci v reálném čase, jako je chat, řídicí panel a herní aplikace.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([jak stáhnout](xref:index#how-to-download-a-sample)). [Jak spustit](#sample-app).

## <a name="signalr"></a>SignalR

[ASP.NET Core SignalR](xref:signalr/introduction) je knihovna, která zjednodušuje přidávání webových funkcí v reálném čase do aplikací. Používá WebSockets, kdykoli je to možné.

Pro většinu aplikací doporučujeme SignalR přes raw WebSockets. SignalR poskytuje přenos záložní pro prostředí, kde WebSockets není k dispozici. Poskytuje také jednoduchý model aplikace vzdáleného volání procedur. A ve většině scénářů SignalR nemá žádné významné nevýhody výkonu ve srovnání s použitím raw WebSockets.

## <a name="prerequisites"></a>Požadavky

* ASP.NET jádro 1.1 nebo novější
* Jakýkoli operační operační spoje, který podporuje ASP.NET Core:
  
  * Windows 7 / Windows Server 2008 nebo novější
  * Linux
  * macOS
  
* Pokud aplikace běží ve Windows se službou IIS:

  * Windows 8 / Windows Server 2012 nebo novější
  * IIS 8 / IIS 8 Express
  * WebSockets musí být povolena (viz část [podpory služby IIS/IIS Express.).](#iisiis-express-support)
  
* Pokud aplikace běží na [HTTP.sys](xref:fundamentals/servers/httpsys):

  * Windows 8 / Windows Server 2012 nebo novější

* Podporované prohlížeče naleznete https://caniuse.com/#feat=websocketsv tématu .

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a>Balíček NuGet

Nainstalujte balíček [Microsoft.AspNetCore.WebSockets.](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/)

::: moniker-end

## <a name="configure-the-middleware"></a>Konfigurace middlewaru


Přidejte middleware WebSockets `Configure` do `Startup` metody třídy:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

Lze nakonfigurovat následující nastavení:

* `KeepAliveInterval`- Jak často posílat "ping" rámce klientovi, aby proxy udržet připojení otevřené. Výchozí hodnota je dvě minuty.
* `ReceiveBufferSize`- Velikost vyrovnávací paměti používané pro příjem dat. Pokročilí uživatelé může být nutné změnit pro optimalizaci výkonu na základě velikosti dat. Výchozí hodnota je 4 KB.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Lze nakonfigurovat následující nastavení:

* `KeepAliveInterval`- Jak často posílat "ping" rámce klientovi, aby proxy udržet připojení otevřené. Výchozí hodnota je dvě minuty.
* <xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>- Velikost vyrovnávací paměti používané pro příjem dat. Pokročilí uživatelé může být nutné změnit pro optimalizaci výkonu na základě velikosti dat. Výchozí hodnota je 4 KB.
* `AllowedOrigins`- Seznam povolených hodnot hlaviček Origin pro požadavky WebSocket. Ve výchozím nastavení jsou povoleny všechny počátky. Podrobnosti naleznete níže v části "Omezení původu websocketu".

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>Přijmout požadavky websocketu

Někde později v životním cyklu `Configure` požadavku (později v metodě nebo v metodě akce, například) zkontrolujte, zda se jedná o požadavek WebSocket a přijměte požadavek WebSocket.

Následující příklad je z `Configure` později v metodě:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Požadavek WebSocket může přijít na libovolnou adresu URL, ale `/ws`tento ukázkový kód přijímá pouze požadavky pro .

Při použití WebSocket, **je nutné** zachovat middleware kanálu spuštěna po dobu trvání připojení. Pokud se pokusíte odeslat nebo přijmout zprávu WebSocket po ukončení kanálu middlewaru, může se zobrazit výjimka, jako je následující:

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

Pokud používáte službu na pozadí k zápisu dat do WebSocket, ujistěte se, že zachovat middleware kanálu spuštěna. Proveďte to <xref:System.Threading.Tasks.TaskCompletionSource%601>pomocí . Předejte `TaskCompletionSource` službu na pozadí <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> a mít volání po dokončení s WebSocket. Pak `await` <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> vlastnost během požadavku, jak je znázorněno v následujícím příkladu:

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
Uzavřená výjimka WebSocket může také dojít, pokud se vrátíte příliš brzy z metody akce. Pokud přijmete soket v metodě akce, počkejte na kód, který používá soket k dokončení před návratem z metody akce.

Nikdy `Task.Wait()`nepoužívejte , `Task.Result`, nebo podobné blokování volání čekat na soket k dokončení, protože to může způsobit vážné problémy s podprocesem. Vždy `await`používejte .

## <a name="send-and-receive-messages"></a>Odesílání a příjem zpráv

Metoda `AcceptWebSocketAsync` inovuje připojení TCP na připojení WebSocket a poskytuje objekt [WebSocket.](/dotnet/core/api/system.net.websockets.websocket) Pomocí `WebSocket` objektu odesílejte a přijímejte zprávy.

Kód zobrazený dříve, který přijímá požadavek WebSocket předá `WebSocket` objekt metodě. `Echo` Kód obdrží zprávu a okamžitě odešle zpět stejnou zprávu. Zprávy jsou odesílány a přijímány ve smyčce, dokud klient neukončí připojení:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

Při přijetí připojení WebSocket před zahájením smyčky middleware kanálu končí. Po zavření zásuvky se potrubí uvolní. To znamená, že požadavek přestane posunout vpřed v kanálu při přijetí WebSocket. Po dokončení smyčky a soketu je uzavřen, požadavek pokračuje zálohovat potrubí.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>Zpracování odpojení klienta

Server není automaticky informován, když se klient odpojí z důvodu ztráty připojení. Server obdrží zprávu o odpojení pouze v případě, že ji klient odešle, což nelze provést v případě ztráty připojení k Internetu. Pokud chcete provést nějakou akci, když k tomu dojde, nastavte časový čas po nic je přijatod klienta v určitém časovém okně.

Pokud klient není vždy odesílání zpráv a nechcete časový limit jen proto, že připojení přejde nečinnosti, mají klient použít časovač odeslat ping zprávy každých X sekund. Pokud zpráva nedorazila do 2\*x sekund po předchozím, ukončete připojení a nahlaste, že se klient odpojil. Počkejte na dvojnásobek očekávaného časového intervalu, aby se doba navíc prozpoždění sítě, která by mohla obsahovat zprávu ping.

## <a name="websocket-origin-restriction"></a>Omezení původu websocketu

Ochrany poskytované CORS se nevztahují na WebSockets. Prohlížeče **nemají**:

* Proveďte předletové požadavky CORS.
* Respektujte omezení `Access-Control` zadaná v záhlaví při vytváření požadavků WebSocket.

Prohlížeče však odeslat `Origin` záhlaví při vydávání požadavků WebSocket. Aplikace by měly být nakonfigurovány tak, aby ověřovaly tyto hlavičky, aby bylo zajištěno, že jsou povoleny pouze websockety pocházející z očekávaného původu.

Pokud hostujete serverhttps://server.comna " " ahttps://client.comhostujete svého klienta na " ", přidejte "https://client.com" do `AllowedOrigins` seznamu pro WebSockets k ověření.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> Záhlaví `Origin` je řízeno klientem a `Referer` stejně jako záhlaví může být zfalšováno. **Nepoužívejte** tyto hlavičky jako mechanismus ověřování.

::: moniker-end

## <a name="iisiis-express-support"></a>Podpora služby IIS/IIS Express

Windows Server 2012 nebo novější a Windows 8 nebo novější se službou IIS/IIS Express 8 nebo novějším podporují protokol WebSocket.

> [!NOTE]
> WebSockets jsou vždy povoleny při použití služby IIS Express.

### <a name="enabling-websockets-on-iis"></a>Povolení webových soketů ve službě IIS

Povolení podpory protokolu WebSocket v systému Windows Server 2012 nebo novějším:

> [!NOTE]
> Tyto kroky nejsou vyžadovány při použití služby IIS Express

1. Použijte Průvodce **přidáním rolí a funkcí** z nabídky **Spravovat** nebo odkaz ve **Správci serveru**.
1. Vyberte **možnost Instalace založená na rolích nebo na základě funkcí**. Vyberte **další**.
1. Vyberte příslušný server (místní server je vybrán ve výchozím nastavení). Vyberte **další**.
1. Rozbalte **webový server (IIS)** ve stromu **rolí,** rozbalte **webový server**a potom **rozbalte položku Vývoj aplikací**.
1. Vyberte **websocketový protokol**. Vyberte **další**.
1. Pokud další funkce nejsou potřeba, vyberte **Další**.
1. Vyberte **Install** (Nainstalovat).
1. Po dokončení instalace vyberte **Zavřít** a ukončete průvodce.

Povolení podpory protokolu WebSocket v systému Windows 8 nebo novějším:

> [!NOTE]
> Tyto kroky nejsou vyžadovány při použití služby IIS Express

1. Přejděte na **Ovládací panely** > **Programy** > **a funkce** > **Zapněte nebo vypněte funkce systému Windows** (levá strana obrazovky).
1. Otevřete následující uzly: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.
1. Vyberte funkci **WebSocket Protocol.** Vyberte **OK**.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Zakázat websocket při použití socket.io na soubor Node.js

Pokud používáte podporu WebSocket v [souboru socket.io](https://socket.io/) na [souboru Node.js](https://nodejs.org/), zakažte výchozí modul IIS WebSocket pomocí prvku v `webSocket` *souboru web.config* nebo *applicationHost.config*. Pokud tento krok není proveden, modul IIS WebSocket se pokusí zpracovat komunikaci WebSocket spíše než Node.js a aplikace.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>Ukázková aplikace

[Ukázková aplikace,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) která doprovází tento článek je echo aplikace. Má webovou stránku, která umožňuje připojení WebSocket a server znovu odešle všechny zprávy, které obdrží zpět klientovi. Spusťte aplikaci z příkazového řádku (není nastavena na spuštění z visual http://localhost:5000studia pomocí iis express) a přejděte na . Na webové stránce se zobrazuje stav připojení v levém horním rohu:

![Počáteční stav webové stránky](websockets/_static/start.png)

Vyberte **Připojit,** chcete-li odeslat požadavek WebSocket na zobrazenou adresu URL. Zadejte testovací zprávu a vyberte **Odeslat**. Až bude hotovo, vyberte **Zavřít soket**. Sekce **Protokol komunikace** hlásí každou akci otevření, odeslání a zavření.

![Počáteční stav webové stránky](websockets/_static/end.png)


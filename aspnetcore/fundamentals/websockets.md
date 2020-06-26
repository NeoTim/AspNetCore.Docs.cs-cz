---
title: Podpora WebSockets v ASP.NET Core
author: rick-anderson
description: Naučte se, jak začít s objekty WebSockets v ASP.NET Core.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: e2aff39db621ea6e71dce1f1560b1aa70fa865f0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404090"
---
# <a name="websockets-support-in-aspnet-core"></a>Podpora WebSockets v ASP.NET Core

Tím, že [Dykstra](https://github.com/tdykstra) a [Andrew Stanton – sestry](https://github.com/anurse)

Tento článek vysvětluje, jak začít s objekty WebSockets v ASP.NET Core. [WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) je protokol, který umožňuje obousměrné trvalé komunikační kanály přes připojení TCP. Používá se v aplikacích, které využívají rychlou komunikaci v reálném čase, jako jsou konverzace, řídicí panely a herní aplikace.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)). [Jak spustit](#sample-app).

## SignalR

[ASP.NET Core SignalR ](xref:signalr/introduction) je knihovna, která zjednodušuje přidávání webových funkcí v reálném čase do aplikací. Pokud je to možné, používá objekty WebSocket.

Pro většinu aplikací doporučujeme používat SignalR nezpracované objekty WebSockets. SignalRposkytuje záložní přenos pro prostředí, kde nejsou k dispozici objekty WebSockets. Poskytuje také jednoduchý model aplikace pro vzdálené volání procedur. Ve většině scénářů SignalR nemá v porovnání s nezpracovanými objekty WebSocket žádný významný nevýhodný výkon.

## <a name="prerequisites"></a>Požadavky

* ASP.NET Core 1,1 nebo novější
* Libovolný operační systém, který podporuje ASP.NET Core:
  
  * Windows 7/Windows Server 2008 nebo novější
  * Linux
  * macOS
  
* Pokud aplikace běží ve Windows se službou IIS:

  * Windows 8/Windows Server 2012 nebo novější
  * IIS 8/IIS 8 Express
  * Je nutné povolit objekty WebSockets (viz část [Podpora služby IIS/IIS Express](#iisiis-express-support) .).
  
* Pokud aplikace běží na [HTTP.sys](xref:fundamentals/servers/httpsys):

  * Windows 8/Windows Server 2012 nebo novější

* Podporované prohlížeče najdete v tématu https://caniuse.com/#feat=websockets .

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a>Balíček NuGet

Nainstalujte balíček [Microsoft. AspNetCore. WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) .

::: moniker-end

## <a name="configure-the-middleware"></a>Konfigurace middlewaru


Do metody třídy přidejte middleware WebSockets `Configure` `Startup` :

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

Je možné nakonfigurovat následující nastavení:

* `KeepAliveInterval`– Jak často se mají do klienta odesílat snímky příkazu "testovat z příkazového testu", aby proxy servery udržovaly otevřené připojení. Výchozí hodnota je dvě minuty.
* `ReceiveBufferSize`– Velikost vyrovnávací paměti použité k příjmu dat. Pokročilí uživatelé tuto změnu můžou potřebovat pro vyladění výkonu na základě velikosti dat. Výchozí hodnota je 4 KB.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Je možné nakonfigurovat následující nastavení:

* `KeepAliveInterval`– Jak často se mají do klienta odesílat snímky příkazu "testovat z příkazového testu", aby proxy servery udržovaly otevřené připojení. Výchozí hodnota je dvě minuty.
* <xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize>– Velikost vyrovnávací paměti použité k příjmu dat. Pokročilí uživatelé tuto změnu můžou potřebovat pro vyladění výkonu na základě velikosti dat. Výchozí hodnota je 4 KB.
* `AllowedOrigins`– Seznam povolených hodnot záhlaví zdroje pro požadavky protokolu WebSocket. Ve výchozím nastavení jsou povoleny všechny zdroje. Podrobnosti najdete níže v části omezení původu pro WebSocket.

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a>Přijmout požadavky protokolu WebSocket

Někam později v životním cyklu požadavku (například později v `Configure` metodě nebo v metodě akce) ověřte, zda se jedná o požadavek protokolu WebSocket a přijmout požadavek protokolu WebSocket.

Následující příklad je z níže v `Configure` metodě:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Požadavek protokolu WebSocket by mohl být na jakékoli adrese URL, ale tento ukázkový kód přijímá pouze požadavky na `/ws` .

Při použití objektu WebSocket je **nutné** , aby byl kanál middleware spuštěn po dobu trvání připojení. Pokud se pokusíte odeslat nebo přijmout zprávu protokolu WebSocket po ukončení kanálu middleware, může se zobrazit výjimka podobná následující:

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

Pokud k zápisu dat do objektu WebSocket používáte službu na pozadí, ujistěte se, že je kanál middleware spuštěný. Použijte k tomu <xref:System.Threading.Tasks.TaskCompletionSource%601> . Předejte `TaskCompletionSource` službě na pozadí a zavolejte ji, <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> až skončíte s WebSocket. Pak `await` <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> vlastnost během žádosti, jak je znázorněno v následujícím příkladu:

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
Uzavřená výjimka WebSocket se může vyskytnout také v případě, že se vrátí příliš brzy od metody Action. Pokud v metodě akce přijmete soket, počkejte, než se před vrácením z metody Action dokončí kód, který používá soket.

Nikdy nepoužívejte `Task.Wait()` , `Task.Result` nebo podobná blokující volání pro čekání na dokončení soketu, což může způsobit vážné problémy s vlákny. Vždy použít `await` .

## <a name="send-and-receive-messages"></a>Odesílání a příjem zpráv

`AcceptWebSocketAsync`Metoda upgraduje připojení TCP na připojení protokolem WebSocket a poskytuje objekt [WebSocket](/dotnet/core/api/system.net.websockets.websocket) . Pomocí `WebSocket` objektu můžete odesílat a přijímat zprávy.

Kód uvedený výše, který přijímá požadavek protokolu WebSocket, předá `WebSocket` objekt `Echo` metodě. Kód obdrží zprávu a ihned pošle zpět stejnou zprávu. Zprávy jsou odesílány a přijímány ve smyčce, dokud klient neukončí připojení:

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

Když přijmete připojení protokolu WebSocket před zahájením smyčky, kanál middleware skončí. Po zavření soketu se kanál odvíjí. To znamená, že požadavek se při přijetí protokolu WebSocket zastaví v kanálu. Když je smyčka dokončená a soket je uzavřený, požadavek pokračuje v zálohování kanálu.

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a>Popisovač odpojení klienta

Pokud se klient odpojí z důvodu ztráty připojení, server se automaticky neinformuje. Server obdrží zprávu odpojení pouze v případě, že ji klient pošle, a pokud dojde ke ztrátě připojení k Internetu. Pokud chcete provést nějakou akci, když k tomu dojde, nastavte časový limit po nedoručení od klienta do určitého časového období.

Pokud klient vždy neposílá zprávy a nechcete, aby vypršel časový limit, protože připojení se nečinný, musí klient použít časovač k odeslání zprávy s příkazem if každých X sekund. Pokud se na serveru nepřišla zpráva do 2 \* X sekund po předchozím konci, ukončí se připojení a oznámí, že se klient odpojil. Počkejte na dvojnásobek očekávaného časového intervalu pro prodlevu při zpoždění sítě, která by mohla obsahovat zprávu s upozorněním na test.

## <a name="websocket-origin-restriction"></a>Omezení původu protokolu WebSocket

Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets. Prohlížeče **neobsahují:**

* Provádění požadavků CORS v řádu CORS
* Respektujte omezení zadaná v `Access-Control` hlavičkách při vytváření požadavků protokolu WebSocket.

Prohlížeče ale `Origin` při vydávání požadavků protokolu WebSocket odesílají hlavičku. Aplikace by měly být nakonfigurovány pro ověření těchto hlaviček, aby bylo zajištěno, že budou povoleny pouze objekty WebSockets přicházející z očekávaných zdrojů.

Pokud váš Server hostuje " https://server.com " a hostující klienta v " https://client.com ", přidejte " https://client.com " do `AllowedOrigins` seznamu pro objekty WebSockets, které chcete ověřit.

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> `Origin`Záhlaví řídí klient a, podobně jako `Referer` záhlaví, může být falešným. Nepoužívejte **tyto** hlavičky jako ověřovací mechanismus.

::: moniker-end

## <a name="iisiis-express-support"></a>Podpora služby IIS/IIS Express

Windows Server 2012 nebo novější a Windows 8 nebo novější se službou IIS/IIS Express 8 nebo novějším má podporu protokolu WebSocket.

> [!NOTE]
> Při použití IIS Express jsou objekty WebSocket vždy povolené.

### <a name="enabling-websockets-on-iis"></a>Povolování WebSockets ve službě IIS

Povolení podpory protokolu WebSocket v systému Windows Server 2012 nebo novějším:

> [!NOTE]
> Při použití IIS Express se tyto kroky nevyžadují.

1. Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**.
1. Vyberte **instalaci na základě rolí nebo na základě funkcí**. Vyberte **Další**.
1. Vyberte příslušný server (ve výchozím nastavení je místní server vybraný). Vyberte **Další**.
1. Rozbalte **webový server (IIS)** ve stromu **role** , rozbalte možnost **webový server**a poté rozbalte položku **vývoj aplikací**.
1. Vyberte **protokol WebSocket**. Vyberte **Další**.
1. Pokud nepotřebujete další funkce, vyberte **Další**.
1. Vyberte **Nainstalovat**.
1. Až se instalace dokončí, kliknutím na **Zavřít** ukončete průvodce.

Povolení podpory protokolu WebSocket v systému Windows 8 nebo novějším:

> [!NOTE]
> Při použití IIS Express se tyto kroky nevyžadují.

1. Přejděte na **Ovládací panely**  >  **programy**programy  >  **a funkce**  >  **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).
1. Otevřete následující uzly: **Internetová informační služba**  >  funkce pro vývoj aplikací v**rámci webové služby**  >  **Application Development Features**.
1. Vyberte funkci **protokolu WebSocket** . Vyberte **OK**.

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a>Zakázat WebSocket při použití socket.io v Node.js

Pokud používáte podporu protokolu WebSocket v [Socket.IO](https://socket.io/) na [Node.js](https://nodejs.org/), zakažte výchozí modul protokolu WebSocket služby IIS pomocí `webSocket` elementu v *web.config* nebo *applicationHost.config*. Pokud tento krok neprovedete, modul WebSocket služby IIS se pokusí zpracovat komunikaci protokolu WebSocket místo Node.js a aplikace.

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a>Ukázková aplikace

[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) , která doprovází tento článek, je aplikace s odezvou. Má webovou stránku, která zpřístupňuje připojení pomocí protokolu WebSocket, a server znovu odešle všechny zprávy, které obdrží zpátky klientovi. Spusťte aplikaci z příkazového řádku (není nastavená tak, aby se spouštěla ze sady Visual Studio s IIS Express), a přejděte na http://localhost:5000 . Na webové stránce se zobrazuje stav připojení v levém horním rohu:

![Počáteční stav webové stránky](websockets/_static/start.png)

Vyberte **připojit** a odešlete požadavek protokolu WebSocket na ZOBRAZENOU adresu URL. Zadejte zkušební zprávu a vyberte **Odeslat**. Po dokončení vyberte **zavřít soket**. Část **protokol komunikace** oznamuje každou akci otevřít, Odeslat a zavřít, když k ní dojde.

![Počáteční stav webové stránky](websockets/_static/end.png)


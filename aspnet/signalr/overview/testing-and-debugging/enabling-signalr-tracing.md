---
uid: signalr/overview/testing-and-debugging/enabling-signalr-tracing
title: Povolení trasování knihovnou SignalR | Dokumentace Microsoftu
author: bradygaster
description: Tento dokument popisuje, jak povolit a nakonfigurovat trasování pro funkci SignalR servery a klienty. Trasování umožňuje zobrazit diagnostické informace o události...
ms.author: bradyg
ms.date: 08/08/2014
ms.assetid: 30060acb-be3e-4347-996f-3870f0c37829
msc.legacyurl: /signalr/overview/testing-and-debugging/enabling-signalr-tracing
msc.type: authoredcontent
ms.openlocfilehash: c6515e3653798ef50e2d2dcb7354ffed407a190e
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836464"
---
<a name="enabling-signalr-tracing"></a>Povolení trasování knihovnou SignalR
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento dokument popisuje, jak povolit a nakonfigurovat trasování pro funkci SignalR servery a klienty. Trasování můžete zobrazit diagnostické informace o událostech v aplikaci funkce SignalR.
>
> Toto téma bylo napsáno původně podle Patrick Fletcher.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET Framework 4.5
> - Funkce SignalR verze 2
>
>
>
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).


Když je povoleno trasování, vytvoří aplikaci s knihovnou SignalR položky protokolu událostí. Protokolování událostí z klienta i serveru. Trasování protokolů připojení k serveru, zprostředkovatele o škálování a událostí Service bus zprávu. Trasování událostí připojení klientských protokolů. V systému SignalR 2.1 nebo novější protokoly trasování na straně klienta celý obsah zprávy volání rozbočovače.

## <a name="contents"></a>Obsah

- [Povolení trasování na serveru](#server)

    - [Protokolování událostí na serveru k textovým souborům](#server_text)
    - [Protokolování serveru události do protokolu událostí](#server_eventlog)
- [Povolení trasování klienta .NET (aplikace pro Windows Desktop)](#net_client)

    - [Protokolování událostí klienta pro stolní počítače do konzoly](#desktop_console)
    - [Protokolování událostí klienta pro stolní počítače do textového souboru](#desktop_text)
- [Povolení trasování v klientech Windows Phone 8](#phone)

    - [Protokolování událostí klienta Windows Phone v uživatelském rozhraní](#phone_ui)
    - [Protokolování událostí pro Windows Phone klienta ke konzole ladění](#phone_debug)
- [Povolení trasování klienta jazyka JavaScript](#javascript)

<a id="server"></a>
## <a name="enabling-tracing-on-the-server"></a>Povolení trasování na serveru

Povolení trasování serveru v konfiguračním souboru aplikace (App.config nebo Web.config v závislosti na typu projektu.) Určíte, které kategorie událostí, které chcete se přihlásit. V konfiguračním souboru, můžete také určit, zda chcete protokolovat události do textového souboru, do protokolu událostí Windows nebo vlastního protokolu pomocí implementace [TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx).

Kategorie události serveru zahrnují následující druhy zprávy:

| Zdroj | Zprávy |
| --- | --- |
| SignalR.SqlMessageBus | Instalační program zprostředkovatele sběrnice zpráv SQL horizontálním navýšením kapacity, operace databáze, chyby a události vypršení časového limitu |
| SignalR.ServiceBusMessageBus | Vytvoření tématu služby Service bus horizontálním navýšením kapacity zprostředkovatele a předplatné, chyb a zasílání zpráv událostí |
| SignalR.RedisMessageBus | Události zprostředkovatele připojení, odpojení a chyba horizontálním navýšením kapacity redis |
| SignalR.ScaleoutMessageBus | Události zasílání zpráv o škálování |
| SignalR.Transports.WebSocketTransport | Události připojení, odpojení, zasílání zpráv a chyba přenosu pomocí protokolu WebSocket |
| SignalR.Transports.ServerSentEventsTransport | ServerSentEvents přenosu připojení, odpojení, zasílání zpráv, události a chyby |
| SignalR.Transports.ForeverFrameTransport | ForeverFrame přenosové připojení, odpojení, zasílání zpráv a chybové události |
| SignalR.Transports.LongPollingTransport | LongPolling přenosové připojení, odpojení, zasílání zpráv a chybové události |
| SignalR.Transports.TransportHeartBeat | Přenést události připojení, odpojení a keepalive |
| SignalR.ReflectedHubDescriptorProvider | Události v centru zjišťování |

<a id="server_text"></a>
### <a name="logging-server-events-to-text-files"></a>Protokolování událostí na serveru k textovým souborům

Následující kód ukazuje, jak povolit trasování pro každou kategorii událostí. Tento příklad konfiguruje aplikaci, do protokolu událostí do textových souborů.

**XML serverový kód pro povolení trasování**

[!code-html[Main](enabling-signalr-tracing/samples/sample1.html)]

Ve výše uvedeném kódu `SignalRSwitch` určuje položku [TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx) používá pro událostí odeslaných do zadaného protokolu. V takovém případě je nastavena na `Verbose` což znamená, že všechny ladění a trasování zpráv jsou protokolovány.

Následující výstup zobrazuje položky `transports.log.txt` soubor pro aplikaci pomocí konfiguračního souboru výše. Zobrazuje nové připojení, odebrané připojení a přenos události prezenčního signálu.

[!code-console[Main](enabling-signalr-tracing/samples/sample2.cmd)]

<a id="server_eventlog"></a>
### <a name="logging-server-events-to-the-event-log"></a>Protokolování serveru události do protokolu událostí

Chcete-li protokolovat události do protokolu událostí, nikoli textový soubor, změňte hodnoty pro položky v `sharedListeners` uzlu. Následující kód ukazuje, jak protokolovat události serveru do protokolu událostí:

**Serverový kód XML pro protokolování události do protokolu událostí**

[!code-xml[Main](enabling-signalr-tracing/samples/sample3.xml)]

Události jsou protokolovány v protokolu aplikací a jsou k dispozici prostřednictvím prohlížeče událostí, jak je znázorněno níže:

![Prohlížeč událostí zobrazuje protokoly SignalR](enabling-signalr-tracing/_static/image1.png)

> [!NOTE]
> Při použití protokolu událostí, nastavte **TraceLevel** k **chyba** zachovat počet zpráv, které lze spravovat.

<a id="net_client"></a>
## <a name="enabling-tracing-in-the-net-client-windows-desktop-apps"></a>Povolení trasování klienta .NET (aplikace pro Windows Desktop)

Klient .NET můžete protokolovat události do konzoly, textový soubor, nebo do vlastního protokolu pomocí implementace [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).

Povolit protokolování klienta rozhraní .NET, nastavíte připojení k `TraceLevel` vlastnost [TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx) hodnotu a `TraceWriter` platnou vlastnost [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) instance.

<a id="desktop_console"></a>
### <a name="logging-desktop-client-events-to-the-console"></a>Protokolování událostí klienta pro stolní počítače do konzoly

Následující kód jazyka C# ukazuje, jak protokolovat události v rozhraní .NET klienta do konzoly:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample4.cs?highlight=2-3)]

<a id="desktop_text"></a>
### <a name="logging-desktop-client-events-to-a-text-file"></a>Protokolování událostí klienta pro stolní počítače do textového souboru

Následující kód jazyka C# ukazuje, jak protokolovat události v rozhraní .NET klienta do textového souboru:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample5.cs?highlight=4-5)]

Následující výstup zobrazuje položky `ClientLog.txt` soubor pro aplikaci pomocí konfiguračního souboru výše. Zobrazuje připojení k serveru klienta a Centrum volání metody klienta volat `addMessage`:

[!code-console[Main](enabling-signalr-tracing/samples/sample6.cmd)]

<a id="phone"></a>
## <a name="enabling-tracing-in-windows-phone-8-clients"></a>Povolení trasování v klientech Windows Phone 8

Aplikace SignalR pro aplikace Windows Phone pomocí stejného klienta .NET jako aplikace klasické pracovní plochy, ale [Console.Out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx) a zápis do souboru s [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) nejsou k dispozici. Místo toho je potřeba vytvořit vlastní implementaci [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) pro trasování.

<a id="phone_ui"></a>
### <a name="logging-windows-phone-client-events-to-the-ui"></a>Protokolování událostí klienta Windows Phone v uživatelském rozhraní

[SignalR codebase](https://github.com/SignalR/SignalR/archive/master.zip) zahrnuje ukázky Windows Phone, která zapíše výstup trasování do [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) pomocí vlastní [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) volaná implementaci `TextBlockWriter`. Tato třída najdete v **samples/Microsoft.AspNet.SignalR.Client.WP8.Samples** projektu. Při vytváření instance `TextBlockWriter`, předejte aktuální [třída SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx)a [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) kde se vytvoří [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) pro trasovacího výstup:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample7.cs)]

Výstup trasování se poté zapíšou do nového [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) vytvořené v [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) jste předali v:

![](enabling-signalr-tracing/_static/image2.png)

<a id="phone_debug"></a>
### <a name="logging-windows-phone-client-events-to-the-debug-console"></a>Protokolování událostí pro Windows Phone klienta ke konzole ladění

Aby odesílal výstup do konzoly ladění místo uživatelského rozhraní, vytvořte implementaci třídy [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) , která zapisuje do okna ladění a přiřadíte ho k připojení k [TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx) vlastnost:

[!code-csharp[Main](enabling-signalr-tracing/samples/sample8.cs)]

Informace o trasování, se zapíšou do okna ladění v sadě Visual Studio:

![](enabling-signalr-tracing/_static/image3.png)

<a id="javascript"></a>
## <a name="enabling-tracing-in-the-javascript-client"></a>Povolení trasování klienta jazyka JavaScript

Chcete-li povolit protokolování na straně klienta na připojení, nastavte `logging` vlastnost u objektu připojení před voláním `start` metoda k navázání připojení.

**Kód JavaScript klienta pro povolení trasování do konzoly prohlížeče (pomocí vygenerovaný proxy server)**

[!code-javascript[Main](enabling-signalr-tracing/samples/sample9.js?highlight=1)]

**Kód JavaScript klienta pro povolení trasování do konzoly prohlížeče (bez vygenerovaný proxy server)**

[!code-javascript[Main](enabling-signalr-tracing/samples/sample10.js?highlight=2)]

Když je povoleno trasování, javascriptový klient protokoluje události do konzoly prohlížeče. Pro přístup do konzoly prohlížeče, naleznete v tématu [monitorování přenosů](../getting-started/introduction-to-signalr.md#MonitoringTransports).

Následující snímek obrazovky ukazuje SignalR JavaScript klienta s povoleným trasováním. Připojení a vyvolání události v centru zobrazí v konzole prohlížeče:

![Události trasování SignalR v konzole prohlížeče](enabling-signalr-tracing/_static/image4.png)

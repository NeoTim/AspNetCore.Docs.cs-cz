---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-net-client
title: Funkce SignalR technologie ASP.NET pokyny k rozhraní API Center – klient .NET (C#) | Dokumentace Microsoftu
author: pfletcher
description: Tento dokument obsahuje úvod k používání rozhraní API rozbočovače pro funkci SignalR verze 2 v rozhraní .NET klientů, jako jsou Windows Store (WinRT), WPF, Silverlight a nevýhody...
ms.author: riande
ms.date: 01/15/2019
ms.assetid: 6d02d9f7-94e5-4140-9f51-5a6040f274f6
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-net-client
msc.type: authoredcontent
ms.openlocfilehash: 9981b8b91be3395b1a3aa7e0cabb1b7f455d47be
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396178"
---
<a name="aspnet-signalr-hubs-api-guide---net-client-c"></a>Funkce SignalR technologie ASP.NET pokyny k rozhraní API Center – klient .NET (C#)
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento dokument obsahuje úvod k používání rozhraní API rozbočovače pro funkci SignalR verze 2 v rozhraní .NET klientů, jako jsou Windows Store (WinRT), WPF, Silverlight a konzolové aplikace.
>
> Rozhraní API pro rozbočovače SignalR umožňuje vytvářet vzdálených volání procedur (RPC) ze serveru pro připojené klienty a z klientů k serveru. V serverovém kódu můžete definovat metody, které mohou být volány klientů a volat metody, které běží na straně klienta. V klientském kódu můžete definovat metody, které lze volat ze serveru a volání metody, které běží na serveru. Funkce SignalR postará za vás zajistí funkčnost systému klient server.
>
> Funkce SignalR také nabízí nižší úrovně rozhraní API volá trvalé připojení. Úvod do SignalR, rozbočovačů a trvalá připojení, nebo kurz, který ukazuje, jak sestavit kompletní aplikace SignalR, přečtěte si téma [SignalR – Začínáme](../getting-started/index.md).
>
> ## <a name="software-versions-used-in-this-topic"></a>Verze softwaru použitým v tomto tématu
>
>
> - [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
> - .NET 4.5
> - Funkce SignalR verze 2
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>Předchozích verzích tohoto tématu
>
> Informace o předchozích verzích systému SignalR naleznete v tématu [starší verze funkce SignalR](../older-versions/index.md).
>
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).

## <a name="overview"></a>Přehled

Tento dokument obsahuje následující části:

- [Instalace klienta](#clientsetup)
- [Postup vytvoření připojení](#establishconnection)

    - [Připojení mezi doménami z klienty prostředí Silverlight](#slcrossdomain)
- [Postup konfigurace připojení](#configureconnection)

    - [Jak nastavit maximální počet souběžných připojení v klientech WPF](#maxconnections)
    - [Určení parametrů řetězce dotazu](#querystring)
    - [Jak určit metodu přenosu](#transport)
    - [Jak zadat hlavičky protokolu HTTP](#httpheaders)
    - [Určení klientských certifikátů](#clientcertificate)
- [Jak vytvořit proxy server rozbočovače](#proxy)
- [Definování metody na straně klienta, která může volat na serveru](#callclient)

    - [Metody bez parametrů](#clientmethodswithoutparms)
    - [Metody s parametry, určující typy parametrů](#clientmethodswithparmtypes)
    - [Metody s parametry, dynamických objektů pro parametry](#clientmethodswithdynamparms)
    - [Jak odebrat obslužnou rutinu](#removehandler)
- [Volání metody serveru z klienta](#callserver)
- [Zpracování událostí doby platnosti](#connectionlifetime)
- [Zpracování chyb](#handleerrors)
- [Jak povolit protokolování na straně klienta](#logging)
- [WPF, Silverlight a Konzolová aplikace ukázky kódu pro metody klienta, které můžete volat na serveru](#wpfsl)

Ukázka .NET klientské projekty naleznete v následujících zdrojích:

- [gustavo armenta / SignalR-Samples](https://github.com/gustavo-armenta/SignalR-Samples) na webu GitHub.com (příklady WinRT, Silverlight, konzoly aplikace).
- [DamianEdwards / SignalR MoveShapeDemo / MoveShape.Desktop](https://github.com/DamianEdwards/SignalR-MoveShapeDemo/tree/master/MoveShape/MoveShape.Desktop) na webu GitHub.com (např. WPF).
- [Funkce SignalR / Microsoft.AspNet.SignalR.Client.Samples](https://github.com/SignalR/SignalR/tree/master/samples/Microsoft.AspNet.SignalR.Client.Samples) na webu GitHub.com (např. aplikace konzoly).

Dokumentace o tom, jak program na serveru nebo klientů JavaScript naleznete v následujících zdrojích informací:

- [Pokyny k rozhraní API Center SignalR – Server](hubs-api-guide-server.md)
- [Pokyny k rozhraní API Center SignalR – javascriptový klient](hubs-api-guide-javascript-client.md)

Odkazy na témata, Reference k rozhraní API se API verze rozhraní .NET 4.5. Pokud používáte .NET 4, přečtěte si téma [verze .NET 4 témat API](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).

<a id="clientsetup"></a>

## <a name="client-setup"></a>Instalace klienta

Nainstalujte [Microsoft.AspNet.SignalR.Client](http://nuget.org/packages/Microsoft.AspNet.SignalR.Client) balíčku NuGet (ne [Microsoft.AspNet.SignalR](http://nuget.org/packages/microsoft.aspnet.signalr) balíček). Tento balíček podporuje WinRT, Silverlight, WPF, konzolovou aplikaci a klienti Windows Phone, .NET 4 a .NET 4.5.

Pokud verze funkce signalr, která máte na straně klienta se liší od verze, které máte na serveru, SignalR často je možné přizpůsobit pro rozdíl. Například server se systémem SignalR verze 2 bude podporovat klienty, kteří mají nainstalované 1.1.x, stejně jako klienti, kteří mají verzi 2 nainstalované. Pokud je příliš velký rozdíl mezi verzí na serveru a verze na klientovi, nebo pokud klient je novější než server, vyvolá funkce SignalR `InvalidOperationException` výjimky, když se klient pokusí navázat připojení. Chybová zpráva "`You are using a version of the client that isn't compatible with the server. Client version X.X, server version X.X`".

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a>Postup vytvoření připojení

Předtím, než můžete navázat spojení, je nutné vytvořit `HubConnection` objektů a vytvořit proxy. K navázání připojení, zavolejte `Start` metodu `HubConnection` objektu.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample1.cs?highlight=1,4)]

> [!NOTE]
> Pro klienty jazyka JavaScript, je nutné provést registraci aspoň jednu obslužnou rutinu události před voláním `Start` metoda k navázání připojení. To není nutné pro klienty .NET. Pro klienty jazyka JavaScript, vygenerovaném kódu proxy automaticky vytvoří proxy pro všechna centra, které existují na serveru a registraci obslužné rutiny je způsob, jak naznačit které rozbočovače klienta chce využít. Ale pro klienta .NET vytvořit proxy servery Hub ručně, tak SignalR předpokládá, že budete používat libovolné centrum, kterou vytvoříte pro proxy server.


Vzorový kód používá výchozí "/ signalr" adresa URL k připojení do služby SignalR. Informace o tom, jak určit různé základní adresu URL najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - /signalr URL](hubs-api-guide-server.md#signalrurl).

`Start` Metoda provedena asynchronně. Ujistěte se, že následující řádky kódu není můžete spustit až po vytvoření připojení, použijte `await` v technologii ASP.NET 4.5 asynchronní metodu nebo `.Wait()` v synchronní metodě. Nepoužívejte `.Wait()` v klientovi WinRT.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample2.cs?highlight=1)]

[!code-css[Main](hubs-api-guide-net-client/samples/sample3.css?highlight=1)]

<a id="slcrossdomain"></a>

### <a name="cross-domain-connections-from-silverlight-clients"></a>Připojení mezi doménami z klienty prostředí Silverlight

Informace o tom, jak povolit připojení mezi doménami z klienty prostředí Silverlight naleznete v tématu [zpřístupnění služby k dispozici napříč hranicemi domén](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).

<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a>Postup konfigurace připojení

Než vytvoříte připojení, můžete určit kterékoli z následujících možností:

- Limit souběžných připojení.
- Parametry řetězce dotazu.
- Metoda přenosu.
- Hlavičky protokolu HTTP.
- Klientské certifikáty.

<a id="maxconnections"></a>

### <a name="how-to-set-the-maximum-number-of-concurrent-connections-in-wpf-clients"></a>Jak nastavit maximální počet souběžných připojení v klientech WPF

V WPF klienty bude pravděpodobně zvýšit maximální počet souběžných připojení z jeho výchozí hodnotu 2. Doporučená hodnota je 10.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample4.cs?highlight=4)]

Další informace najdete v tématu [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx).

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a>Určení parametrů řetězce dotazu

Pokud chcete odesílat data do serveru, když se klient připojí, můžete přidat parametry řetězce dotazu do objekt připojení. Následující příklad ukazuje, jak nastavit parametr řetězce dotazu v klientském kódu.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample5.cs)]

Následující příklad znázorňuje způsob čtení parametru řetězce dotazu v serverovém kódu.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample6.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a>Jak určit metodu přenosu

Jako součást procesu připojování klienta SignalR obvykle vyjedná se serverem a určit nejlepší přenos, který je podporovaný server i klient. Pokud již víte, jaké přenosu, kterou chcete použít, můžete obejít tento proces vyjednávání. K určení metodu přenosu, předejte objekt transport metodě Start. Následující příklad ukazuje, jak zadat metodu přenosu v klientském kódu.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample7.cs?highlight=4)]

[Microsoft.AspNet.SignalR.Client.Transports](https://msdn.microsoft.com/library/jj918090(v=vs.111).aspx) obor názvů obsahuje následující třídy, které můžete použít k určení přenos.

- [LongPollingTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.longpollingtransport(v=vs.111).aspx)
- [ServerSentEventsTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.serversenteventstransport(v=vs.111).aspx)
- [WebSocketTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.websockettransport(v=vs.111).aspx) (dostupné pouze v případě serverů i klientů pomocí rozhraní .NET 4.5.)
- [AutoTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.autotransport(v=vs.111).aspx) (automaticky vybere nejlepší přenos, který podporuje klient i server. Toto je výchozí přenos. Tuto hodnotu na předání `Start` metoda má stejný účinek jako ne vyhovující co.)

Přenos ForeverFrame není zahrnuta v tomto seznamu, protože se používá pouze pomocí prohlížeče.

Informace o tom, jak zkontrolovat metodu přenosu v serverovém kódu najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - jak získat informace o klientovi z kontextové vlastnosti](hubs-api-guide-server.md#contextproperty). Další informace o přenosy a náhrad najdete v tématu [Úvod ke knihovně SignalR – přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports).

<a id="httpheaders"></a>

### <a name="how-to-specify-http-headers"></a>Jak zadat hlavičky protokolu HTTP

K nastavení hlavičky protokolu HTTP, použijte `Headers` vlastnost na objekt připojení. Následující příklad ukazuje, jak přidat hlavičku protokolu HTTP.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample8.cs?highlight=2)]

<a id="clientcertificate"></a>

### <a name="how-to-specify-client-certificates"></a>Určení klientských certifikátů

Chcete-li přidat klientské certifikáty, použijte `AddClientCertificate` metodu na objekt připojení.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample9.cs?highlight=2)]

<a id="proxy"></a>

## <a name="how-to-create-the-hub-proxy"></a>Jak vytvořit proxy server rozbočovače

Aby bylo možné definovat metody na straně klienta, která centrum může volat ze serveru a volání metod rozbočovače na serveru, vytvořit proxy server rozbočovače voláním `CreateHubProxy` na objekt připojení. Řetězec předáním `CreateHubProxy` je název třídy rozbočovače nebo název určený `HubName` atribut, pokud bylo použito na serveru. Shoda názvů nerozlišuje.

**Třída rozbočovače na serveru**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample10.cs?highlight=1)]

**Vytvořit proxy klienta pro rozbočovač třídu**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample11.cs?highlight=2)]

Pokud uspořádání vaší třídy centra s `HubName` atribut, použijte tento název.

**Třída rozbočovače na serveru**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample12.cs)]

**Vytvořit proxy klienta pro rozbočovač třídu**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample13.cs?highlight=2)]

Při volání `HubConnection.CreateHubProxy` víckrát se stejným `hubName`, získáte stejné mezipaměti `IHubProxy` objektu.

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a>Definování metody na straně klienta, která může volat na serveru

Chcete-li definovat metodu, která může volat na serveru, používat proxy server, na `On` metody pro registraci obslužné rutiny události.

Shoda názvu metody je velká a malá písmena. Například `Clients.All.UpdateStockPrice` na serveru spustí `updateStockPrice`, `updatestockprice`, nebo `UpdateStockPrice` na straně klienta.

Různé klientské platformy mají různé požadavky na jak psát kód, metoda pro aktualizaci uživatelského rozhraní. Příklady uvedené jsou pro klienty WinRT (Windows Store .NET). WPF, Silverlight a příklady aplikací konzoly jsou k dispozici v [samostatné části dále v tomto tématu](#wpfsl).

<a id="clientmethodswithoutparms"></a>

### <a name="methods-without-parameters"></a>Metody bez parametrů

Pokud metoda, že zpracování nemá žádné parametry, použijte přetížení obecné `On` metody:

**Volání metody bez parametrů kódu serveru**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample14.cs?highlight=5)]

**WinRT klientský kód pro metodu volat ze serveru bez parametrů ([WPF a Silverlight příklady dále v tomto tématu](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample15.cs)]

<a id="clientmethodswithparmtypes"></a>

### <a name="methods-with-parameters-specifying-the-parameter-types"></a>Metody s parametry, určující typy parametrů

Pokud jste zpracování metody obsahuje parametry, zadejte typy parametrů jako obecné typy `On` metody. Existují přetížení obecné `On` metoda vám umožní určit až na 8 parametry (4 ve Windows Phone 7). V následujícím příkladu je jeden parametr odeslán `UpdateStockPrice` metody.

**Volání metody s parametrem kódu serveru**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample16.cs?highlight=3)]

**Třída akcie použitý pro parametr**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample17.cs)]

**WinRT klientský kód pro metodu volat ze serveru s parametrem ([WPF a Silverlight příklady dále v tomto tématu](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample18.cs?highlight=1,5)]

<a id="clientmethodswithdynamparms"></a>

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a>Metody s parametry, dynamických objektů pro parametry

Jako alternativu k zadání parametrů jako obecné typy `On` metodu, můžete zadat parametry jako dynamické objekty:

**Volání metody s parametrem kódu serveru**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample19.cs?highlight=3)]

**Třída akcie použitý pro parametr**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample20.cs)]

**WinRT klientský kód pro metodu volat ze serveru s parametrem, pomocí dynamického objektu pro parametr ([WPF a Silverlight příklady dále v tomto tématu](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample21.cs?highlight=1,5)]

<a id="removehandler"></a>

### <a name="how-to-remove-a-handler"></a>Jak odebrat obslužnou rutinu

Chcete-li odebrat obslužnou rutinu, zavolejte jeho `Dispose` metoda.

**Klientský kód pro metodu volat ze serveru**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample22.cs?highlight=1)]

**Klientský kód odebrat obslužnou rutinu**

[!code-css[Main](hubs-api-guide-net-client/samples/sample23.css?highlight=1)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a>Volání metody serveru z klienta

Chcete-li volat metodu na serveru, použijte `Invoke` metodu na proxy server rozbočovače.

Pokud metoda server nemá žádnou návratovou hodnotu, použijte přetížení obecné `Invoke` metody.

**Serverový kód pro metodu, která nemá žádnou návratovou hodnotu**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample24.cs?highlight=3)]

**Klientský kód volá metodu, která nemá žádnou návratovou hodnotu**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample25.cs?highlight=1)]

Pokud metoda serveru má návratovou hodnotu, určit návratový typ jako generický typ `Invoke` metody.

**Serverový kód pro metodu, nemá návratovou hodnotu, která přebírá parametr komplexního typu**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample26.cs?highlight=1)]

**Třída akcie použitý pro parametr a vrátí hodnotu**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample27.cs)]

**Klientský kód volá metodu, nemá návratovou hodnotu, která přebírá parametr komplexního typu, v asynchronní metodě technologie ASP.NET 4.5**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample28.cs?highlight=1-2)]

**Klientský kód volá metodu, nemá návratovou hodnotu, která přebírá parametr komplexního typu, v synchronní metodě**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample29.cs?highlight=1-2)]

`Invoke` Metoda asynchronně provede a vrátí `Task` objektu. Pokud nezadáte `await` nebo `.Wait()`, dalším řádku kódu se provedou předtím, než metody, která je zapotřebí vyvolat neskončí.

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a>Zpracování událostí doby platnosti

Funkce SignalR poskytuje následující připojení události doby života, které dokáže zpracovat:

- `Received`: Vyvoláno, když je veškerá data přijatá v připojení. Poskytuje přijatá data.
- `ConnectionSlow`: Vyvoláno, když klient zjistí pomalý nebo často vyřazení připojení.
- `Reconnecting`: Vyvolá se při přenosu začne znovu obnovovat.
- `Reconnected`: Vyvolá se při přenosu má připojen.
- `StateChanged`: Vyvolá se při změně stavu připojení. Poskytuje stav starý a nový stav. Informace o připojení najdete v části hodnoty stavu [ConnectionState výčet](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connectionstate(v=vs.111).aspx).
- `Closed`: Vyvolá se při připojení se odpojil.

Například pokud chcete zobrazit varovné zprávy o chybách, které nejsou závažné, ale způsobit problémy s přerušovaným připojením, například jako pomalost nebo častému odstranění připojení, zpracování `ConnectionSlow` událostí.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample30.cs)]

Další informace najdete v tématu [principy a zpracování událostí doby platnosti v knihovně SignalR](handling-connection-lifetime-events.md).

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a>Zpracování chyb

Pokud není explicitně povolit podrobné chybové zprávy na serveru, obsahuje objekt výjimky, která vrací SignalR po chybě minimální informace o této chybě. Například, pokud je volání `newContosoChatMessage` selže, chybová zpráva v objektu chyba obsahuje "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" podrobné chybové zprávy pro klienty v produkčním prostředí se nedoporučuje z bezpečnostních důvodů, ale pokud chcete povolit podrobné chybové zprávy pro odesílání na serveru pro účely odstraňování potíží, použijte následující kód.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample31.cs?highlight=2)]

<a id="handleerrors"></a>

Zpracování chyb, které vyvolává SignalR, můžete přidat obslužnou rutinu pro `Error` události u objektu připojení.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample32.cs)]

Zpracování chyb z volání metod, zabalte kód v bloku try-catch.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample33.cs)]

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a>Jak povolit protokolování na straně klienta

Chcete-li povolit protokolování na straně klienta, nastavte `TraceLevel` a `TraceWriter` vlastnosti objektu připojení.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample34.cs?highlight=2-3)]

<a id="wpfsl"></a>

## <a name="wpf-silverlight-and-console-application-code-samples-for-client-methods-that-the-server-can-call"></a>WPF, Silverlight a Konzolová aplikace ukázky kódu pro metody klienta, které můžete volat na serveru

Ukázky kódu je uvedeno výše pro definování metody klienta, které můžete volat serveru platí pro klienty WinRT. Následující ukázky ukazují ekvivalentní kód pro WPF, Silverlight a klienty aplikace konzoly.

### <a name="methods-without-parameters"></a>Metody bez parametrů

**WPF klientský kód pro metodu s názvem ze serveru bez parametrů**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample35.cs?highlight=1)]

**Kód klienta programu Silverlight pro metodu s názvem ze serveru bez parametrů**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample36.cs?highlight=1)]

**Konzole application klientský kód pro metodu s názvem ze serveru bez parametrů**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample37.cs?highlight=1)]

### <a name="methods-with-parameters-specifying-the-parameter-types"></a>Metody s parametry, určující typy parametrů

**WPF klientský kód pro metodu volat ze serveru s parametrem**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample38.cs?highlight=1,4)]

**Kód klienta programu Silverlight pro metodu volat ze serveru s parametrem**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample39.cs?highlight=1,5)]

**Kód klienta konzolové aplikace pro metodu volat ze serveru s parametrem**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample40.cs?highlight=1-2)]

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a>Metody s parametry, dynamických objektů pro parametry

**WPF klientský kód pro metodu volat ze serveru s parametrem, pomocí dynamického objektu pro parametr**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample41.cs?highlight=1,4)]

**Kód klienta programu Silverlight pro metodu volat ze serveru s parametrem, pomocí dynamického objektu pro parametr**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample42.cs?highlight=1,5)]

**Kód klienta konzolové aplikace pro metodu volat ze serveru s parametrem, pomocí dynamického objektu pro parametr**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample43.cs?highlight=1-2)]

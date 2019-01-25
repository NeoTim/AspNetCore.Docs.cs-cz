---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
title: Funkce SignalR technologie ASP.NET pokyny k rozhraní API Center – javascriptový klient | Dokumentace Microsoftu
author: bradygaster
description: Tento dokument obsahuje úvod k používání rozhraní API rozbočovače pro funkci SignalR verze 2 v klientů JavaScript, například s prohlížeči a Windows Store (WinJS) applicat...
ms.author: bradyg
ms.date: 01/15/2019
ms.assetid: a9fd4dc0-1b96-4443-82ca-932a5b4a8ea4
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
msc.type: authoredcontent
ms.openlocfilehash: 7689fa5b6a3e81c9f767831423eb3efad72e1ab3
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836594"
---
<a name="aspnet-signalr-hubs-api-guide---javascript-client"></a>Funkce SignalR technologie ASP.NET pokyny k rozhraní API Center – javascriptový klient
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento dokument obsahuje úvod k používání rozhraní API rozbočovače pro funkci SignalR verze 2 v klientů JavaScript, například s prohlížeči a aplikace Windows Store (WinJS).
>
> Rozhraní API pro rozbočovače SignalR umožňuje vytvářet vzdálených volání procedur (RPC) ze serveru pro připojené klienty a z klientů k serveru. V serverovém kódu můžete definovat metody, které mohou být volány klientů a volat metody, které běží na straně klienta. V klientském kódu můžete definovat metody, které lze volat ze serveru a volání metody, které běží na serveru. Funkce SignalR postará za vás zajistí funkčnost systému klient server.
>
> Funkce SignalR také nabízí nižší úrovně rozhraní API volá trvalé připojení. Úvod do SignalR, rozbočovačů a trvalá připojení, najdete v článku [Úvod ke knihovně SignalR](../getting-started/introduction-to-signalr.md).
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

- [Vygenerovaný proxy server a co to dělá za vás](#genproxy)

    - [Kdy použít vygenerovaný proxy server](#cantusegenproxy)
- [Instalace klienta](#clientsetup)

    - [Způsob vytvoření odkazu dynamicky generované proxy](#dynamicproxy)
    - [Vytváření fyzického souboru pro funkci SignalR generované proxy](#manualproxy)
- [Postup vytvoření připojení](#establishconnection)

    - [$. connection.hub je stejný objekt, vytvoří tento $.hubConnection()](#connequivalence)
    - [Asynchronní provádění tohoto start – metoda](#asyncstart)
- [Jak vytvořit připojení mezi doménami](#crossdomain)
- [Postup konfigurace připojení](#configureconnection)

    - [Určení parametrů řetězce dotazu](#querystring)
    - [Jak určit metodu přenosu](#transport)
- [Získání proxy serveru pro rozbočovač třídy](#getproxy)
- [Definování metody na straně klienta, která může volat na serveru](#callclient)
- [Volání metody serveru z klienta](#callserver)
- [Zpracování událostí doby platnosti](#connectionlifetime)
- [Zpracování chyb](#handleerrors)
- [Jak povolit protokolování na straně klienta](#logging)

Dokumentace o tom, jak program na serveru nebo klientů .NET, naleznete v následujících zdrojích informací:

- [Pokyny k rozhraní API Center SignalR – Server](hubs-api-guide-server.md)
- [Pokyny k rozhraní API Center SignalR – klient .NET](hubs-api-guide-net-client.md)

Součást serveru SignalR 2 je dostupná pouze na rozhraní .NET 4.5 (i když je klient .NET pro funkci SignalR 2 v rozhraní .NET 4.0).

<a id="genproxy"></a>

## <a name="the-generated-proxy-and-what-it-does-for-you"></a>Vygenerovaný proxy server a co to dělá za vás

Můžete naprogramovat javascriptový klient komunikovat se službou SignalR s nebo bez proxy serveru, který generuje SignalR za vás. Proxy vykonává pro vás je zjednodušení syntaxe kódu slouží k připojení metod zápisu, která volá na server, a volání metod na serveru.

Při psaní kódu pro volání metody serveru vygenerovaný proxy server vám umožní použít syntaxi, která vypadá jako by byly provádění lokální funkce: můžete napsat `serverMethod(arg1, arg2)` místo `invoke('serverMethod', arg1, arg2)`. Syntaxe vygenerovaný proxy server také umožňuje okamžité a srozumitelné chybě na straně klienta, pokud napíšete metodu název serveru. A pokud ručně vytvořit soubor, který definuje náhledy, můžete také získat podporu technologie IntelliSense pro psaní kódu, který volá metody serveru.

Předpokládejme například, že máte následující třídy rozbočovače na serveru:

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample1.cs?highlight=1,3,5)]

Následující příklady kódu ukazují, co vypadá kódu JavaScript jako pro volání `NewContosoChatMessage` metodu na serveru a přijímají volání `addContosoChatMessageToPage` metoda ze serveru.

**Vygenerovaný proxy**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample2.js?highlight=1-2,8)]

**Bez vygenerovaný proxy server**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample3.js?highlight=2-3,9)]

<a id="cantusegenproxy"></a>

### <a name="when-to-use-the-generated-proxy"></a>Kdy použít vygenerovaný proxy server

Pokud chcete registrovat více obslužných rutin události pro metody, která volá na server, nemůžete použít vygenerovaný proxy server. V opačném případě můžete použít vygenerovaný proxy server nebo není založen na předvolbu kódování. Pokud se rozhodnete nepoužít, není nutné odkazovat na adresu URL "Center/signalr" v `script` elementu v kódu klienta.

<a id="clientsetup"></a>

## <a name="client-setup"></a>Instalace klienta

Klient JavaScriptu vyžaduje odkazy na knihovny jQuery a soubor JavaScript základní funkce SignalR. 1.6.4 nebo hlavní novější verze, jako je například 1.7.2, 1.8.2 nebo 1.9.1 musí být verze jQuery. Pokud se rozhodnete použít vygenerovaný proxy server, potřebujete také odkaz k proxy serveru SignalR vygeneruje soubor jazyka JavaScript. Následující příklad ukazuje, jak může odkazy vypadat na stránku HTML, který používá vygenerovaný proxy server.

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample4.html)]

Tyto odkazy musí být zahrnuty v tomto pořadí: jQuery nejprve naposledy SignalR jádra po tomto a proxy servery SignalR.

<a id="dynamicproxy"></a>

### <a name="how-to-reference-the-dynamically-generated-proxy"></a>Způsob vytvoření odkazu dynamicky generované proxy

V předchozím příkladu je odkaz na proxy serveru SignalR vygeneruje dynamicky generovaném kódu jazyka JavaScript, není k fyzickému souboru. Funkce SignalR vytvoří kód jazyka JavaScript pro proxy server v reálném čase a slouží ke klientovi jako odpověď na adresu "/ signalr/centra". Pokud jste zadali jiné základní adresu URL pro připojení SignalR na serveru ve vaší `MapSignalR` metoda, adresu URL k souboru dynamicky generované proxy je vaše vlastní adresu URL s "/ hubs" připojí k němu.

> [!NOTE]
> Pro klienty systému Windows 8 (Windows Store) jazyka JavaScript použijte soubor fyzické proxy místo dynamicky vygenerovaný. Další informace najdete v tématu [vytváření fyzického souboru pro funkci SignalR generované proxy](#manualproxy) dále v tomto tématu.


V ASP.NET MVC 4 a 5 zobrazení Razor pomocí tilda k odkazování na kořenový adresář aplikace v odkazu na soubor vašeho proxy serveru:

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample5.html)]

Další informace o použití aplikace SignalR v MVC 5 v tématu [Začínáme s knihovnou SignalR a MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).

V zobrazení syntaxe Razor rozhraní ASP.NET MVC 3, použijte `Url.Content` pro odkaz na soubor vašeho proxy serveru:

[!code-cshtml[Main](hubs-api-guide-javascript-client/samples/sample6.cshtml)]

V aplikaci webových formulářů ASP.NET pomocí `ResolveClientUrl` pro vaše proxy odkaz na soubor nebo ho zaregistrovat pomocí prvku ScriptManager pomocí cesty relativní kořenové aplikace (začíná tildou):

[!code-aspx[Main](hubs-api-guide-javascript-client/samples/sample7.aspx)]

Obecně platí použijte stejnou metodu pro zadávání adresy URL "/ signalr/centra", který používáte pro soubory CSS a JavaScript. Pokud zadáte adresu URL bez použití vlnovkou, v některých scénářích aplikace bude fungovat správně při testování v sadě Visual Studio pomocí služby IIS Express, ale selže s chyby 404 při nasazování do úplnou službu IIS. Další informace najdete v tématu **řešení odkazy na prostředky na kořenové úrovni** v [webové servery v sadě Visual Studio pro webové projekty ASP.NET](https://msdn.microsoft.com/library/58wxa9w5.aspx) na webu MSDN.

Při spuštění webový projekt v sadě Visual Studio 2017 v režimu ladění, a pokud používáte Internet Explorer jako prohlížeč, zobrazí se soubor proxy v **Průzkumníka řešení** pod **skripty**.

Pokud chcete zobrazit obsah souboru, klikněte dvakrát na **rozbočovače**. Pokud nepoužíváte Visual Studio 2012 nebo 2013 a aplikace Internet Explorer, nebo pokud nejste v režimu ladění, můžete také získat obsah souboru tak, že přejdete na adresu "/ signalR/centra". Například, pokud vaše lokalita běží v `http://localhost:56699`, přejděte na stránku `http://localhost:56699/SignalR/hubs` v prohlížeči.

<a id="manualproxy"></a>

### <a name="how-to-create-a-physical-file-for-the-signalr-generated-proxy"></a>Vytváření fyzického souboru pro funkci SignalR generované proxy

Jako alternativu k dynamicky generované proxy můžete vytvořit fyzický soubor, který má kód proxy serveru a jako reference. Můžete to udělat pro kontrolu nad ukládání do mezipaměti nebo sdružování chování nebo aby technologie IntelliSense při psaní kódu volání metody serveru.

Vytvoření souboru proxy serveru, proveďte následující kroky:

1. Nainstalujte [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) balíček NuGet.
2. Otevřete příkazový řádek a přejděte *nástroje* složku obsahující soubor SignalR.exe. Nástroje pro složku je v následujícím umístění:

    `[your solution folder]\packages\Microsoft.AspNet.SignalR.Utils.2.1.0\tools`
3. Zadejte následující příkaz:

    `signalr ghp /path:[path to the .dll that contains your Hub class]`

    Cesta k vaší *.dll* je obvykle *bin* ve složce projektu.

    Tento příkaz vytvoří soubor s názvem *server.js* ve stejné složce jako *signalr.exe*.
4. Vložit *server.js* souborů v příslušné složce ve vašem projektu, přejmenujte ho podle potřeby pro vaši aplikaci a přidejte odkaz na jeho místo odkazu "Center/signalr".

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a>Postup vytvoření připojení

Aby mohla navázat připojení, budete muset vytvořit objekt připojení, proxy server a zaregistrujte obslužné rutiny událostí pro metody, které lze volat ze serveru. Po nastavení serveru proxy a událost obslužné rutiny připojení voláním `start` metody.

Pokud používáte vygenerovaný proxy server, není nutné vytvořit objekt připojení ve svém vlastním kódu, protože vygenerovaném kódu proxy to udělá za vás.

<a id="nogenconnection"></a>

**Naváže připojení (vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample8.js?highlight=5)]

**Navázání připojení (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample9.js?highlight=1,6)]

Vzorový kód používá výchozí "/ signalr" adresa URL k připojení do služby SignalR. Informace o tom, jak určit různé základní adresu URL najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - /signalr URL](hubs-api-guide-server.md#signalrurl).

Ve výchozím umístění centra je aktuální server; Pokud se připojujete k jinému serveru, zadejte adresu URL před voláním `start` způsob, jak je znázorněno v následujícím příkladu:

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample10.js)]

> [!NOTE]
> Obvykle registraci obslužné rutiny událostí před voláním `start` metoda k navázání připojení. Pokud chcete zaregistrovat několik obslužných rutin událostí po navázání připojení, můžete to udělat, ale je nutné zaregistrovat alespoň jeden z vašich handler(s) událostí před voláním `start` metody. Jedním z důvodů je, že v aplikaci může být mnoho rozbočovače, ale není vhodné pro aktivaci `OnConnected` událost pro každý rozbočovač, chcete-li pouze pomocí jedné z nich. Po vytvoření připojení je existenci metody na proxy server rozbočovače pro co říká SignalR k aktivaci `OnConnected` událostí. Pokud nezaregistrujete všechny obslužné rutiny událostí před voláním `start` metody, bude možné volat metody v rozbočovači, ale centra `OnConnected` nebude volána metoda a žádné metody klienta, který bude vyvolán ze serveru.


<a id="connequivalence"></a>

### <a name="connectionhub-is-the-same-object-that-hubconnection-creates"></a>$. connection.hub je stejný objekt, vytvoří tento $.hubConnection()

Jak je vidět z příkladů, když použijete vygenerovaný proxy server, `$.connection.hub` odkazuje na objekt připojení. Toto je stejný objekt, který můžete získat voláním `$.hubConnection()` když nepoužíváte vygenerovaný proxy server. Vygenerovaný proxy kód vytvoří spojení za vás spuštěním následujícího příkazu:

![Vytvoření připojení v souboru vygenerovaný proxy server](hubs-api-guide-javascript-client/_static/image3.png)

Pokud používáte vygenerovaný proxy server, můžete provést cokoli, co se `$.connection.hub` , když nepoužíváte vygenerovaný proxy server, co lze použít s objektem připojení.

<a id="asyncstart"></a>

### <a name="asynchronous-execution-of-the-start-method"></a>Asynchronní provádění tohoto start – metoda

`start` Metoda provedena asynchronně. Vrátí [jQuery odloženo objekt](http://api.jquery.com/category/deferred-object/), což znamená, že můžete přidat funkce zpětného volání voláním metod `pipe`, `done`, a `fail`. Pokud máte kód, který chcete spustit po připojení se naváže, jako je například volání metody serveru, vložte tento kód ve funkci zpětného volání nebo jeho volání z funkce zpětného volání. `.done` Metoda zpětného volání se spustí po vytvoření připojení a po žádný kód, který máte vaše `OnConnected` metoda obslužné rutiny události na server dokončí provádění.

Když vložíte příkaz "Teď připojení" z předchozího příkladu jako další řádek kódu po `start` volání metody (ne v `.done` zpětného volání), `console.log` řádek se spustí předtím, než se naváže připojení, jak je znázorněno v následujícím Příklad:

![Ukázka nesprávného způsobu psaní kódu, která se spustí po vytvoření připojení](hubs-api-guide-javascript-client/_static/image5.png)

<a id="crossdomain"></a>

## <a name="how-to-establish-a-cross-domain-connection"></a>Jak vytvořit připojení mezi doménami

Obvykle Pokud prohlížeč načte stránku z `http://contoso.com`, připojení SignalR je ve stejné doméně, v `http://contoso.com/signalr`. Pokud stránku z `http://contoso.com` vytvoří připojení k `http://fabrikam.com/signalr`, to znamená připojení mezi doménami. Z bezpečnostních důvodů jsou ve výchozím nastavení zakázané připojení mezi doménami.

V knihovně SignalR 1.x různé požadavky na doménu se řídí jediného příznaku EnableCrossDomain. Tento příznak řídí požadavky na JSONP a CORS. Pro větší flexibilitu a podporu všech CORS je odebraná ze součásti serveru pro funkci SignalR (JavaScript stále používají klienti CORS obvykle Pokud se zjistí, jestli prohlížeč podporuje), a nové middlewaru OWIN, který byl zpřístupněn pro podporu těchto scénářů.

Pokud na straně klienta (pro podporu požadavků mezi doménami ve starších prohlížečích) je vyžadováno JSONP, ji budou muset být explicitně povoluje nastavením `EnableJSONP` na `HubConfiguration` objektu `true`, jak je znázorněno níže. JSONP je standardně zakázaná, protože je to méně bezpečné než CORS.

**Přidání Microsoft.Owin.Cors do projektu:** Pokud chcete nainstalovat tuto knihovnu, spusťte následující příkaz v konzole Správce balíčků:

`Install-Package Microsoft.Owin.Cors`

Tento příkaz přidá 2.1.0 verzi balíčku do projektu.

### <a name="calling-usecors"></a>Volání UseCors

 Následující fragment kódu ukazuje, jak implementovat mezi doménami připojení v SignalR 2.

**Implementace žádosti napříč doménami v SignalR 2**

Následující kód ukazuje, jak povolit CORS a JSONP v projektu funkcí SignalR 2. Tento vzorový kód používá `Map` a `RunSignalR` místo `MapSignalR`, takže middlewarem CORS běží pouze pro funkci SignalR žádosti, které vyžadují podporu CORS (a nikoli pro veškerý provoz na cestě zadané v `MapSignalR`.) Mapu můžete použít také pro veškerý middleware, který je potřeba spustit pro konkrétní předponu adresy URL, a nikoli pro celou aplikaci.

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample11.cs)]

> [!NOTE]
>
> - Nenastavujte `jQuery.support.cors` na hodnotu true v kódu.
>
>     ![JQuery.support.cors nemají nastavený na hodnotu true](hubs-api-guide-javascript-client/_static/image7.png)
>
>     Funkce SignalR zpracovává použití CORS. Nastavení `jQuery.support.cors` na hodnotu true zakáže JSONP, protože kvůli němu SignalR předpokládat, že prohlížeč podporuje CORS.
> - Když se připojujete k adrese URL místního hostitele, Internet Explorer 10 nebude považují za připojení mezi doménami, tak, že aplikace bude fungovat místně pomocí aplikace Internet Explorer 10 i v případě, že jste ještě nepovolili mezi doménami připojení na serveru.
> - Informace o použití připojení mezi doménami se aplikace Internet Explorer 9, najdete v tématu [toto vlákno na StackOverflow](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).
> - Informace o použití připojení mezi doménami s prohlížečem Chrome naleznete v tématu [toto vlákno na StackOverflow](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).
> - Vzorový kód používá výchozí "/ signalr" adresa URL k připojení do služby SignalR. Informace o tom, jak určit různé základní adresu URL najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - /signalr URL](hubs-api-guide-server.md#signalrurl).


<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a>Postup konfigurace připojení

Než vytvoříte připojení, můžete určit parametry řetězce dotazu nebo zadat metodu přenosu.

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a>Určení parametrů řetězce dotazu

Pokud chcete odesílat data do serveru, když se klient připojí, můžete přidat parametry řetězce dotazu do objekt připojení. Následující příklady ukazují, jak nastavit parametr řetězce dotazu v klientském kódu.

**Nastavte hodnotu řetězce dotazu před voláním metody spuštění (s vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample12.js?highlight=1)]

**Nastavte hodnotu řetězce dotazu před voláním metody start (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample13.js?highlight=2)]

Následující příklad znázorňuje způsob čtení parametru řetězce dotazu v serverovém kódu.

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample14.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a>Jak určit metodu přenosu

Jako součást procesu připojování klienta SignalR obvykle vyjedná se serverem a určit nejlepší přenos, který je podporovaný server i klient. Pokud již víte, jaké přenosu, kterou chcete použít, můžete tento proces vyjednávání obejít tak, že určíte metodu přenosu při volání `start` metody.

**Klientský kód, který určuje metodu přenosu (s vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample15.js?highlight=1)]

**Klientský kód, který určuje metodu přenosu (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample16.js?highlight=2)]

Jako alternativu můžete zadat několik metod přenosu v pořadí, ve kterém chcete SignalR chcete vyzkoušet:

**Klientský kód, který určuje schéma záložního vlastní přenosu (s vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample17.js?highlight=1)]

**Klientský kód, který určuje záložní schématu vlastní přenosu (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample18.js?highlight=2)]

Pro zadání metodu přenosu můžete použít následující hodnoty:

- "webSockets"
- "foreverFrame"
- "serverSentEvents"
- "longPolling"

Následující příklady ukazují, jak zjistit, jakou metodu přenosu používá připojení.

**Klientský kód, který se zobrazí přenos metodu používanou pro připojení (s vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample19.js?highlight=2)]

**Klientský kód, který se zobrazí přenos metodu používanou pro připojení (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample20.js?highlight=3)]

Informace o tom, jak zkontrolovat metodu přenosu v serverovém kódu najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR - Server - jak získat informace o klientovi z kontextové vlastnosti](hubs-api-guide-server.md#contextproperty). Další informace o přenosy a náhrad najdete v tématu [Úvod ke knihovně SignalR – přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports).

<a id="getproxy"></a>

## <a name="how-to-get-a-proxy-for-a-hub-class"></a>Získání proxy serveru pro rozbočovač třídy

Každý objekt připojení, který vytvoříte zapouzdřuje informace o připojení ke službě SignalR, která obsahuje jednu nebo více tříd rozbočovače. Ke komunikaci s třídou rozbočovače, použijte objekt proxy které sami vytvoříte (Pokud nepoužíváte vygenerovaný proxy server) nebo který je generován za vás.

Název proxy serveru na straně klienta je verze ve formátu camelCase názvu třídy rozbočovače. Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript.

**Třída rozbočovače na serveru**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample21.cs?highlight=1)]

**Získání odkazu na proxy serveru generovaného klienta pro rozbočovač**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample22.js?highlight=1)]

**Vytvořit proxy klienta pro rozbočovač třídu (bez vygenerovaný proxy server)**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample23.cs?highlight=1)]

Pokud uspořádání vaší třídy centra s `HubName` atribut, použít přesný název bez Změna velikosti písmen.

**Třída rozbočovače na serveru s atributem HubName**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample24.cs?highlight=1)]

**Získání odkazu na proxy serveru generovaného klienta pro rozbočovač**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample25.js?highlight=1)]

**Vytvořit proxy klienta pro rozbočovač třídu (bez vygenerovaný proxy server)**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample26.cs?highlight=1)]

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a>Definování metody na straně klienta, která může volat na serveru

Chcete-li definovat metodu, která serveru můžete volat z rozbočovač, přidejte obslužnou rutinu události pro proxy server rozbočovače pomocí `client` vlastnost vygenerovaný proxy server nebo volání `on` metody, pokud nepoužíváte vygenerovaný proxy server. Parametry lze komplexních objektů.

Přidat obslužnou rutinu události před voláním `start` metoda k navázání připojení. (Pokud chcete přidat obslužné rutiny událostí po volání `start` metodu, přečtěte si poznámku v [jak k navázání připojení](#establishconnection) dříve v tomto dokumentu a pomocí syntaxe pro definování metody bez použití vygenerovaný proxy server.)

Shoda názvu metody je velká a malá písmena. Například `Clients.All.addContosoChatMessageToPage` na serveru spustí `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, nebo `addcontosochatmessagetopage` na straně klienta.

**Definujte metodu na klientovi (s vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample27.js?highlight=2)]

**Alternativní způsob, jak definovat metodu na klientovi (s vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample28.js?highlight=1-2)]

**Definujte metodu na klientovi (bez vygenerovaný proxy server, nebo při přidávání po volání metody start)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample29.js?highlight=3)]

**Kód serveru, který volá metodu klienta**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample30.cs?highlight=5)]

Následující příklady zahrnují komplexního objektu jako parametr metody.

**Definujte metodu na klienta, který používá komplexního objektu (s vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample31.js?highlight=2-3)]

**Definujte metodu na klienta, který používá komplexního objektu (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample32.js?highlight=3-4)]

**Kód serveru, který definuje komplexní objekt**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample33.cs?highlight=1)]

**Kód serveru, který volá metodu klienta s použitím komplexní objekt**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample34.cs?highlight=3)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a>Volání metody serveru z klienta

Chcete-li volat metodu serveru z klienta, použijte `server` vlastnost vygenerovaný proxy server nebo `invoke` metodu na proxy server rozbočovače, pokud nepoužíváte vygenerovaný proxy server. Návratová hodnota nebo parametrů může být složité objekty.

Předat ve stylu camelCase verzi název metody rozbočovače. Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript.

Následující příklady ukazují, jak volat metodu serveru, který nemá návratovou hodnotu a volání metody serveru, který nemá návratovou hodnotu.

**Metoda serveru se žádný atribut HubMethodName**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample35.cs?highlight=3)]

**Kód serveru, který definuje komplexní objekt předaný v parametru**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample36.cs)]

**Klientský kód, který vyvolá metodu serveru (se vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample37.js?highlight=1)]

**Klientský kód, který vyvolá metodu serveru (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample38.js?highlight=1)]

Pokud upravena pomocí metody rozbočovače `HubMethodName` atribut, použijte tento název bez Změna velikosti písmen.

**Metoda serveru** s atributem HubMethodName

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample39.cs?highlight=3)]

**Klientský kód, který vyvolá metodu serveru (se vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample40.js?highlight=1)]

**Klientský kód, který vyvolá metodu serveru (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample41.js?highlight=1)]

Předchozí příklady ukazují, jak volat metodu serveru, která nemá žádnou návratovou hodnotu. Následující příklady znázorňují způsob volání metody serveru, který má návratovou hodnotu.

**Serverový kód pro metodu, která nemá návratovou hodnotu**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample42.cs?highlight=3)]

**Třída akcie pro** návratová hodnota

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample43.cs?highlight=1)]

**Klientský kód, který vyvolá metodu serveru (se vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample44.js?highlight=2,4-5)]

**Klientský kód, který vyvolá metodu serveru (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample45.js?highlight=2,4-5)]

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a>Zpracování událostí doby platnosti

Funkce SignalR poskytuje následující připojení události doby života, které dokáže zpracovat:

- `starting`: Vyvoláno před odesláním žádná data přes dané připojení.
- `received`: Vyvoláno, když je veškerá data přijatá v připojení. Poskytuje přijatá data.
- `connectionSlow`: Vyvoláno, když klient zjistí pomalý nebo často vyřazení připojení.
- `reconnecting`: Vyvolá se při přenosu začne znovu obnovovat.
- `reconnected`: Vyvolá se při přenosu má připojen.
- `stateChanged`: Vyvolá se při změně stavu připojení. Poskytuje starý stav a nový stav (připojování, připojeno, znovu připojíte nebo odpojeno).
- `disconnected`: Vyvolá se při připojení se odpojil.

Například, pokud chcete zobrazit upozornění, pokud existují problémy s připojením, které může způsobit znatelné zpoždění při, zpracovat `connectionSlow` událostí.

**Zpracování události connectionSlow (s vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample46.js?highlight=1)]

**Zpracování události connectionSlow (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample47.js?highlight=2)]

Další informace najdete v tématu [principy a zpracování událostí doby platnosti v knihovně SignalR](handling-connection-lifetime-events.md).

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a>Zpracování chyb

Poskytuje klientovi SignalR JavaScript `error` události, které můžete přidat obslužnou rutinu. Také vám pomůže metodu selhání přidejte obslužnou rutinu chyby, které jsou výsledkem volání metody serveru.

Pokud není explicitně povolit podrobné chybové zprávy na serveru, obsahuje objekt výjimky, která vrací SignalR po chybě minimální informace o této chybě. Například, pokud je volání `newContosoChatMessage` selže, chybová zpráva v objektu chyba obsahuje "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" podrobné chybové zprávy pro klienty v produkčním prostředí se nedoporučuje z bezpečnostních důvodů, ale pokud chcete povolit podrobné chybové zprávy pro odesílání na serveru pro účely odstraňování potíží, použijte následující kód.

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample48.cs?highlight=2)]

Následující příklad ukazuje, jak přidat obslužnou rutinu pro událost chyby.

**Přidat obslužnou rutinu chyby (s vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample49.js?highlight=1)]

**Přidat obslužnou rutinu chyby (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample50.js?highlight=2)]

Následující příklad ukazuje, jak zpracovat chybu z volání metody.

**Zpracování chyb z volání metody (s vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample51.js?highlight=2)]

**Zpracování chyb z volání metody (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample52.js?highlight=2)]

Pokud selže volání metody, `error` událost se vyvolá také, takže svůj kód v `error` metoda obslužné rutiny a `.fail` by provedení metody zpětného volání.

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a>Jak povolit protokolování na straně klienta

Chcete-li povolit protokolování na straně klienta na připojení, nastavte `logging` vlastnost u objektu připojení před voláním `start` metoda k navázání připojení.

**Povolit protokolování (pomocí vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample53.js?highlight=1)]

**Povolení protokolování (bez vygenerovaný proxy server)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample54.js?highlight=2)]

Pokud chcete zobrazit protokoly, otevřete vývojářské nástroje v prohlížeči a přejděte na kartu konzoly. Kurz, který zobrazuje podrobné pokyny a obrazovky snímky, které ukazují, jak to provést, najdete v tématu [serverové vysílání s knihovnou ASP.NET Signalr – povolit protokolování](../getting-started/tutorial-server-broadcast-with-signalr.md#enable-logging).

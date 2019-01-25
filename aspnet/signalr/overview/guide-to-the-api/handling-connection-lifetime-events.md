---
uid: signalr/overview/guide-to-the-api/handling-connection-lifetime-events
title: Principy a zpracování událostí doby platnosti v knihovně SignalR | Dokumentace Microsoftu
author: bradygaster
description: Tento článek popisuje, jak pomocí událostí vystavené API rozbočovače.
ms.author: bradyg
ms.date: 01/15/2019
ms.assetid: 03960de2-8d95-4444-9169-4426dcc64913
msc.legacyurl: /signalr/overview/guide-to-the-api/handling-connection-lifetime-events
msc.type: authoredcontent
ms.openlocfilehash: 53151d369a14ef9a6fa034043d40ae30098a5067
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836165"
---
<a name="understanding-and-handling-connection-lifetime-events-in-signalr"></a>Principy a zpracování událostí doby platnosti v knihovně SignalR
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento článek obsahuje přehled funkce SignalR připojení, opětovné připojení a odpojení události, které dokáže zpracovat a nastavení časového limitu a keepalive, které můžete nakonfigurovat.
>
> Tento článek předpokládá, že máte již určitá znalost události doby života SignalR a připojení. Úvod k funkci SignalR naleznete v tématu [Úvod ke knihovně SignalR](../getting-started/introduction-to-signalr.md). Seznam událostí doby platnosti naleznete v následujících zdrojích:
>
> - [Zpracování událostí doby platnosti ve třídě centra](hubs-api-guide-server.md#connectionlifetime)
> - [Zpracování událostí doby platnosti v klientech jazyka JavaScript](hubs-api-guide-javascript-client.md#connectionlifetime)
> - [Zpracování událostí doby platnosti v klientů .NET](hubs-api-guide-net-client.md#connectionlifetime)
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

Tento článek obsahuje následující části:

- [Připojení životnost terminologie a scénáře](#terminology)

    - [Připojení SignalR, připojeními a fyzické připojení](#signalrvstransport)
    - [Scénáře odpojení přenosu](#transportdisconnect)
    - [Scénáře odpojení klienta](#clientdisconnect)
    - [Scénáře odpojení serveru](#serverdisconnect)
- [Nastavení časového limitu a keepalive](#timeoutkeepalive)

    - [Hodnota ConnectionTimeout](#connectiontimeout)
    - [DisconnectTimeout](#disconnecttimeout)
    - [KeepAlive](#keepalive)
    - [Jak změnit nastavení časového limitu a keepalive](#changetimeout)
- [Jak informovat uživatele o odpojení](#notifydisconnect)
- [Průběžně opětovné připojení](#continuousreconnect)
- [Jak odpojení klienta v kódu serveru](#disconnectclientfromserver)
- [Zjišťování důvod pro odpojení](#detectingreasonfordisconnection)

Odkazy na témata, Reference k rozhraní API se API verze rozhraní .NET 4.5. Pokud používáte .NET 4, přečtěte si téma [verze .NET 4 témat API](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).

<a id="terminology"></a>

## <a name="connection-lifetime-terminology-and-scenarios"></a>Připojení životnost terminologie a scénáře

`OnReconnected` Obslužné rutiny události v rozbočovači SignalR můžete spustit přímo po `OnConnected` , ale ne po `OnDisconnected` pro daného klienta. Z důvodu, že máte opětovné připojení bez odpojení je, že existuje několik způsobů, ve kterých se používá slovo "připojení" v knihovně SignalR.

<a id="signalrvstransport"></a>

### <a name="signalr-connections-transport-connections-and-physical-connections"></a>Připojení SignalR, připojeními a fyzické připojení

Tento článek bude rozlišovat mezi *připojení SignalR*, *přenosu připojení*, a *fyzické připojení*:

- **Připojení SignalR** odkazuje na logický vztah mezi klientem a adresu URL serveru, spravuje pomocí rozhraní API SignalR a jednoznačně identifikují pomocí ID připojení. Data o tento vztah se spravuje pomocí SignalR a se používá k navázání připojení přenosu. Elementy end relace a technologie SignalR uvolní dat, když klient volá `Stop` metody nebo časový limit je dosaženo během SignalR je snahy o obnovení připojení přenosu ztraceny.
- **Připojení přenosu** odkazuje na logický vztah mezi klientem a serverem, udržován jednu čtyři přenosu rozhraní API: Protokoly Websocket, události odeslané serverem, navždy rámce nebo dlouhým dotazováním. SignalR k vytvoření připojení přenosu používá přenos rozhraní API a rozhraní API pro přenos závisí na existenci fyzické síťové připojení k vytvoření připojení přenosu. Připojení pro přenos končí při SignalR ukončí ho nebo při přenosu rozhraní API zjistí, že fyzické připojení bylo přerušeno.
- **Fyzické připojení** odkazuje na fyzické síťové odkazy – vodičům stanice, bezdrátové signály, směrovače, atd. –, které usnadňují komunikace mezi klientským počítačem a serveru. Fyzické připojení musí být k dispozici, aby bylo možné navázat připojení přenosu a aby bylo možné navázat připojení SignalR musí navázat připojení přenosu. Ale zásadní fyzické připojení není vždy okamžitě ukončí přenosového připojení nebo připojení SignalR, jak budou vysvětlena dále v tomto tématu.

V následujícím diagramu připojení SignalR je reprezentována rozhraní API rozbočovače a SignalR pro rozhraní API PersistentConnection vrstvy, připojení přenosu je reprezentována přenosy vrstvy a fyzické připojení je reprezentována řádky mezi serverem a klienty.

![Diagram architektury SignalR](handling-connection-lifetime-events/_static/image1.png)

Při volání `Start` metody v klientovi SignalR, tím kód klienta SignalR s všechny informace potřebné pro vytvoření fyzické připojení k serveru. SignalR klientský kód používá tyto informace odeslání požadavku HTTP a naváže fyzické připojení, který používá některou z metod čtyři přenosu. Pokud připojení pro přenos se nezdaří a dojde k selhání serveru, připojení SignalR nebude přejděte okamžitě okamžitě vzhledem k tomu, že klient má stále informace potřebné k automaticky znovu vytvořit nové připojení přenosu pro stejnou adresu URL funkce SignalR. V tomto scénáři nepodílí bez nutnosti zásahu od uživatele aplikace a když SignalR klientský kód vytvoří nové připojení přenosu, se nespustí nové připojení SignalR. Ve skutečnosti se projeví kontinuitu připojení SignalR, která ID připojení, který je vytvořen při volání `Start` metoda, nezmění.

`OnReconnected` Obslužné rutiny události v centru provede, když se poté, co bylo ztraceno automaticky znovu naváže připojení přenosu. `OnDisconnected` Obslužná rutina události provádí na konci připojení SignalR. Připojení SignalR můžete ukončit v některém z následujících způsobů:

- Pokud klient volá `Stop` metody zpráva stop se pošle na server a klientských i serverových připojení SignalR okamžitě ukončí.
- Jakmile dojde ke ztrátě připojení mezi klientem a serverem, klient se pokusí znovu připojit a server čeká na klientovi znovu připojit. Pokud neúspěšné pokusy o připojení a odpojení časový limit ukončení, klient a server ukončit připojení SignalR. Klient zastaví pokus o opětovné připojení, a server odstraňuje její znázornění připojení SignalR.
- Pokud klient přestane fungovat bez nutnosti příležitost k volání `Stop` metody server čeká na klientovi znovu připojit a končí po uplynutí časového limitu odpojení připojení SignalR.
- Pokud server přestane běží, se klient pokusí znovu připojit (znovu vytvořit připojení pro přenos) a končí po uplynutí časového limitu odpojení připojení SignalR.

Když neexistují žádné problémy s připojením a uživatelská aplikace ukončí připojení SignalR voláním `Stop` metodu, připojení SignalR a připojení pro přenos začínají i končí na přibližně ve stejnou dobu. Následující části popisují podrobnější jiné scénáře.

<a id="transportdisconnect"></a>

### <a name="transport-disconnection-scenarios"></a>Scénáře odpojení přenosu

Fyzické připojení může být pomalá nebo může být přerušení připojení. Závisí to na faktorech, jako je délka přerušení může dojít ke ztrátě připojení přenosu. SignalR se pak pokusí znovu navázat připojení přenosu. Někdy přenosového připojení rozhraní API zjistí přerušení a zahodí připojení pro přenos a SignalR dozví okamžitě, že připojení bylo ztraceno. V jiných scénářích přenosového připojení rozhraní API ani SignalR zjistí okamžitě, že připojení bylo ztraceno. Pro všechny přenosy s výjimkou dlouhým dotazováním klientovi SignalR používá funkci s názvem *keepalive* ke kontrole ke ztrátě připojení, který se nedokáže rozpoznat přenosu rozhraní API. Informace o dlouho dotazování připojeních najdete v tématu [nastavení časového limitu a keepalive](#timeoutkeepalive) dále v tomto tématu.

Při připojení je neaktivní, pravidelně server odešle paket keepalive do klienta. K datu, které tento článek se týká je výchozí frekvence každých 10 sekund. Prostřednictvím naslouchání pro tyto pakety, můžete zjistit klienty, pokud dojde k problému připojení. Pokud paketu keepalive neobdrží při očekávání, po krátkou dobu klient předpokládá, že jsou problémy s připojením, jako je například pomalost nebo přerušením. Pokud keepalive není stále přijata po delší dobu, klient předpokládá, že připojení bylo vyřazeno a začne pokusu o opětovné připojení.

Následující diagram znázorňuje klientských a serverových události, které jsou vyvolány v rámci typického scénáře, když dochází k problémům s fyzické připojení, které nejsou rozpoznány okamžitě Transport rozhraní API. Diagram se týká následujících okolností:

- Přenos se protokoly Websocket, navždy rámce nebo události odeslané serverem.
- Existují různé doby přerušení připojení k fyzické síti.
- Přenos rozhraní API se dozvěděli o přerušení, není proto SignalR spoléhá na funkce keepalive vyhledáním.

![Odpojení přenosu](handling-connection-lifetime-events/_static/image2.png)

Pokud klient přejde do režimu opětovné připojení, ale nemůže navázat připojení přenosu v časovém limitu odpojení, server ukončí připojení SignalR. Pokud k tomu dojde, spustí na serveru centra `OnDisconnected` metoda a fronty si zprávu o odpojení k odeslání do klienta v případě, že klient spravuje připojení později. Pokud klient znovu připojit, přijme příkaz pro odpojení a volání `Stop` metody. V tomto scénáři `OnReconnected` není spuštěn, když se klient znovu připojí, a `OnDisconnected` není spuštěn, když klient volá `Stop`. Následující diagram znázorňuje tento scénář.

![Narušení přenosu – časový limit serveru](handling-connection-lifetime-events/_static/image3.png)

Události doby života připojení SignalR, které mohou být vyvolány na straně klienta jsou následující:

- `ConnectionSlow` událost klienta.

    Vyvolá se při přednastavených podíl keepalive časový limit uplynul od poslední zprávu nebo keepalive ping byla přijata. Keepalive výchozího časového limitu upozornění je 2/3 keepalive vypršení časového limitu. Časový limit keepalive je 20 sekund, takže dojde k upozornění na přibližně 13 sekund.

    Ve výchozím nastavení odešle server keepalive příkazy ping pro zjištění každých 10 sekund a klient vyhledává příkazy ping keepalive o každé 2 sekundy (jednu třetinu rozdíl mezi keepalive hodnotu časového limitu a hodnotu keepalive časový limit upozornění).

    Pokud obdrží informace o odpojení přenosu rozhraní API, SignalR může být informováni o odpojení předtím, než časový limit upozornění keepalive předá. V takovém případě `ConnectionSlow` nebude vyvolána událost a SignalR přejde přímo na `Reconnecting` událostí.
- `Reconnecting` událost klienta.

    Vyvoláno, když (a) přenos rozhraní API zjistí, že dojde ke ztrátě nebo připojení nebo (b) keepalive časový limit uplynul od poslední zprávu nebo keepalive ping byla přijata. Kód klienta SignalR se pokusí znovu připojit. Tato událost může zpracovat, pokud má vaše aplikace provést některé akce, když dojde ke ztrátě připojení přenosu. Keepalive výchozího časového limitu je momentálně 20 sekund.

    Pokud váš klientský kód se pokusí o volání metody rozbočovače SignalR je v režimu připojení, se pokusí odeslat příkaz SignalR. Ve většině případů, těchto pokusů selže, ale v některých případech může být úspěšné. Funkce SignalR pro události odeslané serverem, navždy rámce a dlouho dotazování přenosy, používá dva komunikační kanály, které klient používá k odesílání zpráv a ten, který se používá pro příjem zpráv. Kanál, který slouží k příjmu je trvale otevřete jednu, a to je ten, který je uzavřen, když dojde k přerušení připojení k fyzické. Kanál používá k odeslání zůstane k dispozici, takže pokud fyzické připojení se obnoví, volání metody z klienta na server může být úspěšné, než kanál receive je obnoveno. Návratová hodnota nemusí přijmout, dokud SignalR znovu otevře kanál pro příjem.
- `Reconnected` událost klienta.

    Vyvoláno, když se obnoví připojení přenosu. `OnReconnected` Spustí obslužnou rutinu události v centru.
- `Closed` událost klienta (`disconnected` událostí v jazyce JavaScript).

    Vyvolá se při vypršení časového limitu odpojit, zatímco při pokusu o opětovné připojení po ztrátě připojení přenosu je v kódu klienta SignalR. Odpojit výchozí časový limit je 30 sekund. (Tato událost je aktivována také při připojení skončí kvůli tomu, `Stop` metoda je volána.)

Události doby života zapříčinil nemusí jakékoli připojení, způsobit přerušení připojení přenosu, které nejsou zjištěny Transport rozhraní API a příjmu keepalive odesílání příkazu ping ze serveru po dobu delší než časový limit upozornění keepalive není zpoždění.

Některá síťová prostředí záměrně nečinných připojení po zavření a jiné funkce keepalive paketů je Zabraňte to tak, že se tyto sítě vědět, že připojení SignalR je používán. V extrémních případech nemusí být výchozí frekvence odesílání příkazu ping keepalive dostatečná ochrana proti uzavřené připojení. V takovém případě můžete nakonfigurovat odesílání příkazu ping keepalive do odešlou častěji. Další informace najdete v tématu [nastavení časového limitu a keepalive](#timeoutkeepalive) dále v tomto tématu.

> [!NOTE]
>
> **Důležité**: Posloupnost událostí, je zde popsáno, není zaručeno. SignalR je každý pokus o vyvolání událostí doby platnosti v předvídatelné podle tohoto schématu, ale existuje mnoho variant událostí sítě a mnoha způsoby, ve kterých je zpracovávat základní architektury komunikace, jako jsou přenosu rozhraní API. Například `Reconnected` událost nemusí být vyvolána, když klient znovu připojí, nebo `OnConnected` obslužnou rutinu na serveru může spustit, když neúspěšný pokus o navázání připojení. Toto téma popisuje pouze efekty, které by bylo vytvořeno obvykle některé obvyklé okolnosti.


<a id="clientdisconnect"></a>

### <a name="client-disconnection-scenarios"></a>Scénáře odpojení klienta

V prohlížeči klientovi kód klienta SignalR, která udržuje připojení SignalR běží v kontextu JavaScript webové stránky. Který má proto musíme ukončit, pokud přejdete z jednoho připojení SignalR stránce do jiného a že je proč máte víc připojení s ID více připojení Pokud se připojujete z více okna prohlížeče nebo karty. Když uživatel zavře okně nebo záložce prohlížeče, nebo přejde na novou stránku nebo aktualizuje stránku, připojení SignalR okamžitě ukončí, protože kód klienta SignalR zpracovává tento prohlížeč událostí a volání `Stop` metody. V těchto scénářích platí, nebo všechny klientské platformy, když vaše aplikace volá `Stop` metody, `OnDisconnected` obslužná rutina události okamžitě spustí na serveru a klienta vyvolá `Closed` události (událost jmenuje `disconnected` v Jazyk JavaScript).

Pokud klientská aplikace nebo počítač, který je spuštěn na dojde k chybě nebo přejde do režimu spánku (například když uživatel zavírá přenosný počítač), server není informována o co se stalo. Nejdál, co ví, server, ke ztrátě klienta může být z důvodu přerušení připojení a klient může být pokus o opětovné připojení. Proto v těchto scénářích server čeká, chcete dát klientům příležitost dobře se znovu připojit a `OnDisconnected` nespustí až do vypršení časového limitu odpojení (přibližně 30 sekund ve výchozím nastavení). Následující diagram znázorňuje tento scénář.

![Chyba klientského počítače](handling-connection-lifetime-events/_static/image4.png)

<a id="serverdisconnect"></a>

### <a name="server-disconnection-scenarios"></a>Scénáře odpojení serveru

Když server přejde do režimu offline--restartování, selže, doména aplikace recykluje, atd. – může být výsledek podobný došlo ke ztrátě připojení nebo přenosu rozhraní API a technologie SignalR možné, že okamžitě, že server je pryč a SignalR můžou začít vyskytovat, pokus o opětovné připojení bez Vyčkat `ConnectionSlow` událostí. Pokud klient přejde do režimu opětovné připojení a obnoví server nebo restartování nebo nový server je převeden do stavu online předtím, než vyprší časový limit odpojit, klient se znovu připojit k obnovené nebo nový server. V takovém případě bude pokračovat připojení SignalR na straně klienta a `Reconnected` událost se vyvolá. Na prvním serveru `OnDisconnected` není nikdy proveden a na novém serveru `OnReconnected` provádí, i když `OnConnected` byla pro tohoto klienta na tomto serveru před nikdy proveden. (Efekt je, že stejné, pokud klient znovu připojí ke stejnému serveru po recyklaci domény restartování nebo aplikace, protože při jeho restartování serveru nemá žádné paměti aktivita předchozí připojení). Následující diagram se předpokládá, že přenos rozhraní API zjistí došlo ke ztrátě připojení okamžitě, takže `ConnectionSlow` není vyvolána událost.

![Selhání serveru a opětovném připojení](handling-connection-lifetime-events/_static/image5.png)

Pokud server není k dispozici v rámci časového limitu odpojení, ukončí připojení SignalR. V tomto scénáři `Closed` událostí (`disconnected` klientům JavaScript) je vyvolána na straně klienta, ale `OnDisconnected` nebude nikdy volána na serveru. Následující diagram se předpokládá, že přenos rozhraní API nebudou vědět, došlo ke ztrátě připojení, tak zjistí funkce keepalive SignalR a `ConnectionSlow` událost se vyvolá.

![Selhání serveru a vypršení časového limitu](handling-connection-lifetime-events/_static/image6.png)

<a id="timeoutkeepalive"></a>

## <a name="timeout-and-keepalive-settings"></a>Nastavení časového limitu a keepalive

Výchozí hodnota `ConnectionTimeout`, `DisconnectTimeout`, a `KeepAlive` hodnoty jsou vhodné pro většinu scénářů, ale může změnit, pokud vaše prostředí se zvláštními potřebami. Pokud vaše síťové prostředí zavře připojení, které jsou nečinné po dobu 5 sekund, budete muset snížit hodnotu keepalive.

<a id="connectiontimeout"></a>

### <a name="connectiontimeout"></a>Hodnota ConnectionTimeout

Toto nastavení představuje dobu má připojení přenosu zůstat otevřené a čeká na odpověď před zavřením a otevřením nové připojení. Výchozí hodnota je 110 sekund.

Toto nastavení platí, pouze když funkce keepalive je zakázáno, což obvykle platí jenom pro long dotazování přenosu. Následující diagram ukazuje účinek tohoto nastavení na dlouho dotazování připojení přenosu.

![Dlouhé dotazování připojení pro přenos](handling-connection-lifetime-events/_static/image7.png)

<a id="disconnecttimeout"></a>

### <a name="disconnecttimeout"></a>DisconnectTimeout

Toto nastavení představuje dobu čekání po připojení přenosu dojde ke ztrátě vyčkat, než se `Disconnected` událostí. Výchozí hodnota je 30 sekund. Pokud nastavíte `DisconnectTimeout`, `KeepAlive` se automaticky nastaví na 1/3 `DisconnectTimeout` hodnotu.

<a id="keepalive"></a>

### <a name="keepalive"></a>KeepAlive

Toto nastavení představuje dobu čekání před odesláním paketu keepalive přes nečinné připojení. Výchozí hodnota je 10 sekund. Tato hodnota nesmí být více než 1/3 `DisconnectTimeout` hodnotu.

Pokud chcete nastavit i `DisconnectTimeout` a `KeepAlive`, nastavte `KeepAlive` po `DisconnectTimeout`. Jinak vaše `KeepAlive` nastavení se přepíšou, když `DisconnectTimeout` automaticky nastaví `KeepAlive` 1/3 hodnota časového limitu.

Pokud chcete zakázat funkce keepalive, nastavte `KeepAlive` na hodnotu null. Automaticky se vypne funkce Keepalive dlouhou dobu přenosu cyklického dotazování.

<a id="changetimeout"></a>

### <a name="how-to-change-timeout-and-keepalive-settings"></a>Jak změnit nastavení časového limitu a keepalive

Chcete-li změnit výchozí hodnoty pro tato nastavení, je nastavit `Application_Start` ve vašich *Global.asax* souboru, jak je znázorněno v následujícím příkladu. Hodnoty uvedené ve vzorovém kódu jsou stejné jako výchozí hodnoty.

[!code-csharp[Main](handling-connection-lifetime-events/samples/sample1.cs)]

<a id="notifydisconnect"></a>

## <a name="how-to-notify-the-user-about-disconnections"></a>Jak informovat uživatele o odpojení

V některých aplikacích můžete chtít zobrazit zprávu pro uživatele, když nejsou potíže s připojením k. Máte několik možností, jak a kdy se má provést. Následující ukázky kódu jsou pro JavaScript klienta pomocí vygenerovaný proxy server.

- Zpracování `connectionSlow` události pro zobrazení zprávy, jakmile SignalR je seznámen problémy s připojením, než přejde do režimu opětovného připojení.

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample2.js)]
- Zpracování `reconnecting` události a zobrazení zprávy při SignalR ví o odpojení a přechází do režimu opětovného připojení.

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample3.js)]
- Zpracování `disconnected` události pro zobrazení zprávy při pokus o opakované připojení k vypršení časového limitu. V tomto scénáři je jediný způsob, jak znovu navázat spojení se serverem znovu restartování připojení SignalR voláním `Start` metodu, která se vytvoří nové ID připojení. Následující vzorový kód používá příznak, abyste měli jistotu, že se vydáte oznámení až po opětovně se připojujícího vypršení časového limitu, nikoli za normální ukončení připojení SignalR způsobilo voláním `Stop` metody.

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample4.js)]

<a id="continuousreconnect"></a>

## <a name="how-to-continuously-reconnect"></a>Průběžně opětovné připojení

V některých aplikacích můžete automaticky znovu navázat připojení poté, co byl ztracen a pokus o připojení vypršel. K tomuto účelu můžete volat `Start` metodu z vašeho `Closed` obslužné rutiny události (`disconnected` obslužné rutiny události na klientech JavaScript). Chcete počkat určitou dobu před voláním `Start` předejdete tím příliš často při serveru nebo fyzické připojení nejsou k dispozici. Následující ukázka kódu je pro JavaScript klienta pomocí vygenerovaný proxy server.

[!code-javascript[Main](handling-connection-lifetime-events/samples/sample5.js)]

Možný problém je potřeba vědět v mobilních klientů je, že průběžné nastavitelnou pokusy, kdy server nebo fyzické připojení není k dispozici může způsobit zbytečné baterie vyprazdňování.

<a id="disconnectclientfromserver"></a>

## <a name="how-to-disconnect-a-client-in-server-code"></a>Jak odpojení klienta v kódu serveru

Funkce SignalR verze 2 nemá integrovaného serveru rozhraní API pro odpojení klienti. Existují [plány pro přidání této funkce v budoucnu](https://github.com/SignalR/SignalR/issues/2101). V aktuální verzi SignalR je nejjednodušší způsob, jak odpojení klienta od serveru implementovat metodu odpojit na straně klienta a volání metody ze serveru. Následující příklad kódu ukazuje metodu odpojit pro JavaScript klienta pomocí vygenerovaný proxy server.

[!code-javascript[Main](handling-connection-lifetime-events/samples/sample6.js)]

> [!WARNING]
> Zabezpečení – tuto metodu pro odpojení klienti ani rozhraní API navržených integrované bude zabývat scénář napadené klienty se systémem škodlivý kód, protože klienti mohli znovu připojit nebo může dojít k odebrání napadené kód `stopClient` metodu nebo změňte Co to dělá. Na příslušné místo k implementaci stavové ochrany s cílem odepření služby (DOS) je v rámci nebo vrstvy serveru, ale v front-endové infrastruktury.


<a id="detectingreasonfordisconnection"></a>
## <a name="detecting-the-reason-for-a-disconnection"></a>Zjišťování důvod pro odpojení

SignalR 2.1 přidá přetížení k serveru `OnDisconnect` událost označující, pokud úmyslně klient odpojen spíše než vyprší časový limit. `StopCalled` Parametr je hodnota true, pokud klient explicitně uzavřel připojení. V jazyce JavaScript, je-li k chybě serveru vedla klienta se odpojit, informace o chybě se předají ke klientovi jako `$.connection.hub.lastError`.

**Server kód jazyka C#: `stopCalled` parametr**

[!code-csharp[Main](handling-connection-lifetime-events/samples/sample7.cs?highlight=1,3)]

**Kód jazyka JavaScript klienta: přístup k `lastError` v `disconnect` událostí.**

[!code-javascript[Main](handling-connection-lifetime-events/samples/sample8.js?highlight=2-3)]

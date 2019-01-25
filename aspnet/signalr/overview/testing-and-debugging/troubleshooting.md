---
uid: signalr/overview/testing-and-debugging/troubleshooting
title: Řešení potíží s knihovnou SignalR | Dokumentace Microsoftu
author: bradygaster
description: Tento článek popisuje běžné problémy s vývojem aplikací SignalR.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 4b559e6c-4fb0-4a04-9812-45cf08ae5779
msc.legacyurl: /signalr/overview/testing-and-debugging/troubleshooting
msc.type: authoredcontent
ms.openlocfilehash: 38802814fbb748513274f1fd8a33521fafd48ed3
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837205"
---
<a name="signalr-troubleshooting"></a>Řešení potíží s knihovnou SignalR
====================
podle [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento dokument popisuje řešení běžných problémů s knihovnou SignalR.
>
> ## <a name="software-versions-used-in-this-topic"></a>Verze softwaru použitým v tomto tématu
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
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


Tento dokument obsahuje následující části.

- [Volání metod mezi klientem a serverem bez upozornění selže](#connection)
- [Konfigurace služby IIS websockets na příkaz ping/pong ke zjištění dead klienta](#pong)
- [Další problémy s připojením](#other)
- [Chyby kompilace a na straně serveru](#server)
- [Problémy v sadě Visual Studio](#vs)
- [Internetová informační služba problémy](#iis)
- [Problémy s Microsoft Azure](#azure)

<a id="connection"></a>

## <a name="calling-methods-between-the-client-and-server-silently-fails"></a>Volání metod mezi klientem a serverem bez upozornění selže

Tato část popisuje možné příčiny pro volání metody mezi klientem a serverem smysluplné chybovou zprávu v případě selhání. V případě aplikace SignalR server nemá žádné informace o metody, které klient implementuje; Když server volá metodu klienta, metoda název a parametru data se odesílají do klienta a metoda provádí pouze v případě, že existuje ve formátu, který je zadaný server. Pokud je nenašla žádná odpovídající metoda na straně klienta, nic se nestane, a je vyvolána žádná chybová zpráva na serveru.

Aby to prověřili aplikace volá metody klienta, můžete zapnout protokolování před voláním metody start na IOT hub a zobrazit jaké volání pocházejí ze serveru. Povolit protokolování aplikace v jazyce JavaScript, najdete v článku [jak povolit protokolování na straně klienta (verze jazyka JavaScript klienta)](../guide-to-the-api/hubs-api-guide-javascript-client.md#logging). Povolení protokolování v klientské aplikaci .NET, naleznete v tématu [jak povolit protokolování na straně klienta (verze .NET klienta)](../guide-to-the-api/hubs-api-guide-net-client.md#logging).

### <a name="misspelled-method-incorrect-method-signature-or-incorrect-hub-name"></a>Chybně napsaná metody, podpis metody nesprávné nebo název nesprávné centra

Pokud název nebo podpis metody volané metody přesně neshoduje s odpovídající metody na straně klienta, volání se nezdaří. Ověřte, jestli název metody, který volá server odpovídá názvu metody na straně klienta. Navíc SignalR vytvoří proxy server rozbočovače-ve formátu camelCase metody, jako je vhodné v jazyce JavaScript, tedy volána metoda `SendMessage` na serveru by byla volána `sendMessage` v klientovi proxy. Pokud používáte `HubName` atribut v kódu na straně serveru, zkontrolujte, zda název, pomocí kterého odpovídá název používaný k vytvoření rozbočovače na straně klienta. Pokud použijete `HubName` atribut, ověřte, zda je název rozbočovače v klientovi JavaScript – ve formátu camelCase, jako je například chatHub místo ChatHub.

### <a name="duplicate-method-name-on-client"></a>Duplicitní název metody v klientovi

Ověřte, že nemáte duplicitní metoda na straně klienta, který se liší pouze velikostí písma. Pokud vaše klientská aplikace má metodu nazvanou `sendMessage`, ověřte, že není k dispozici také metodu nazvanou `SendMessage` také.

### <a name="missing-json-parser-on-the-client"></a>Chybějící analyzátor JSON na straně klienta

Funkce SignalR vyžaduje JSON analyzátor, aby byly k serializaci volání mezi serverem a klientem. Pokud váš klient nemá vestavěné analyzátor JSON (jako je například Internet Explorer 7), budete muset jeden ve vaší aplikaci. Můžete si stáhnout analyzátor JSON [tady](http://nuget.org/packages/json2).

### <a name="mixing-hub-and-persistentconnection-syntax"></a>Kombinování centra a PersistentConnection syntaxe

Funkce SignalR používá dva modely komunikace: Rozbočovače a PersistentConnections. Syntaxe pro volání těchto dvou komunikačních modelů se liší v kódu klienta. Pokud jste přidali rozbočovač v serverovém kódu, ověřte, že veškerý kód klienta používá syntaxi správné centra.

**JavaScript klientský kód, který vytvoří PersistentConnection v javascriptový klient**

[!code-javascript[Main](troubleshooting/samples/sample1.js)]

**Klientský kód jazyka JavaScript, který vytvoří proxy server rozbočovače v klientovi Javascript**

[!code-javascript[Main](troubleshooting/samples/sample2.js)]

**Kód jazyka C# serveru, který se mapuje PersistentConnection trasy**

[!code-csharp[Main](troubleshooting/samples/sample3.cs)]

**Kód jazyka C# serveru, která mapuje trasu k rozbočovači, nebo více rozbočovače, pokud máte více aplikací**

[!code-css[Main](troubleshooting/samples/sample4.css)]

### <a name="connection-started-before-subscriptions-are-added"></a>Připojení, které jsou spuštěny před předplatná se přidají

Pokud připojení rozbočovače pro spuštění před metody, které lze volat ze serveru se přidají k proxy serveru, nebude přijímat zprávy. Následující kód jazyka JavaScript se nespustí centra správně:

**Nesprávné klientský kód jazyka JavaScript, který neumožní mohlo přijímat zprávy rozbočovače**

[!code-javascript[Main](troubleshooting/samples/sample5.js)]

Místo toho přidáte předplatná metoda před voláním spuštění:

**Klientský kód jazyka JavaScript, který správně přidá předplatných pro Centrum**

[!code-javascript[Main](troubleshooting/samples/sample6.js)]

### <a name="missing-method-name-on-the-hub-proxy"></a>Chybí název metody na proxy server rozbočovače

Ověřte, že je metoda, definována na serveru na straně klienta přihlášen k odběru. I když server definuje metodu, musí pořád přidané k proxy serveru klienta. Metody mohou být přidány do proxy serveru klienta takto (Všimněte si, že metoda je přidána do `client` člen centra centra není přímo):

**Klientský kód jazyka JavaScript, který přidá metody proxy server rozbočovače**

[!code-javascript[Main](troubleshooting/samples/sample7.js)]

### <a name="hub-or-hub-methods-not-declared-as-public"></a>Rozbočovač nebo není deklarovaný jako Public metod rozbočovače

Uvidí na straně klienta, musí být deklarována implementace rozbočovače a metody jako `public`.

### <a name="accessing-hub-from-a-different-application"></a>Přístup k centru z jiné aplikace

Rozbočovače SignalR je přístupný pouze prostřednictvím aplikací, které implementují klienti SignalR. SignalR nemůže spolupracovat s ostatními knihovnami komunikace (jako je protokol SOAP nebo webových služeb WCF.) Pokud není k dispozici pro cílovou platformu žádné klienta SignalR, nelze přímý přístup koncového bodu serveru.

### <a name="manually-serializing-data"></a>Ruční serializaci dat

Funkce SignalR automaticky použije JSON k serializaci metodu parametry neexistuje není potřeba provést sami.

### <a name="remote-hub-method-not-executed-on-client-in-ondisconnected-function"></a>Vzdálené metody rozbočovače na klientovi ve funkci ondisconnected rozbočovače nebyly provedeny

Toto chování je záměrné. Když `OnDisconnected` je volání rozbočovače již přešla `Disconnected` stavu, což nepovoluje další metod rozbočovače, která se má volat.

**Kód jazyka C# serveru, který správně spustí kód v případě ondisconnected rozbočovače**

[!code-csharp[Main](troubleshooting/samples/sample8.cs)]

### <a name="ondisconnect-not-firing-at-consistent-times"></a>OnDisconnect nespustí v čase konzistentní vzhledem k aplikacím

Toto chování je záměrné. Když se uživatel pokusí na stránku s aktivní připojení SignalR opustit, klientovi SignalR provede pokus best effort oznámit serveru, připojení klienta bude zastavena. Pokud je klientovi SignalR best effort nezdaří pokus o přístup k serveru, server bude vyřadit připojení po konfigurovat `DisconnectTimeout` později, po kterém `OnDisconnected` událost se aktivuje. Pokud je klientovi SignalR best effort je pokus úspěšný, `OnDisconnected` událost se aktivuje okamžitě.

Informace o nastavení `DisconnectTimeout` nastavení, najdete v článku [zpracování událostí doby platnosti: DisconnectTimeout](../guide-to-the-api/handling-connection-lifetime-events.md#disconnecttimeout).

### <a name="connection-limit-reached"></a>Dosáhlo se limitu připojení

Při použití plnou verzi služby IIS na operačním systému klienta, jako je Windows 7, je nastaveno z důvodu omezení 10 připojení. Pokud používáte klientský operační systém, použijte službu IIS Express, aby tento limit.

### <a name="cross-domain-connection-not-set-up-properly"></a>Připojení mezi doménami nejsou nastaveny správně

Pokud připojení mezi doménami není správně nastaven (připojení, pro kterou SignalR adresa URL není ve stejné doméně jako stránka hostingu), může připojení selhat a chybová zpráva. Informace o tom, jak povolit komunikaci mezi doménami, najdete v části [jak k navázání připojení mezi doménami](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).

### <a name="connection-using-ntlm-active-directory-not-working-in-net-client"></a>Připojení pomocí protokolu NTLM (Active Directory) nebudou fungovat v rozhraní .NET klienta

Připojení v klientské aplikaci .NET, který používá zabezpečení domény může selhat, pokud připojení není správně nakonfigurována. V prostředí domény používat SignalR, nastavte vlastnost požadavku připojení následujícím způsobem:

**Kód jazyka C# klienta, který implementuje přihlašovací údaje pro připojení**

[!code-csharp[Main](troubleshooting/samples/sample9.cs)]

<a id="pong"></a>

## <a name="configuring-iis-websockets-to-pingpong-to-detect-a-dead-client"></a>Konfigurace služby IIS websockets na příkaz ping/pong ke zjištění dead klienta

Servery SignalR neví, pokud je klient dead nebo není a spoléhají na oznámení z podkladové protokolu websocket pro chyby připojení, to znamená, `OnClose` zpětného volání. Jedním řešením tohoto problému je ke konfiguraci služby IIS websockets provést příkaz ping/pong za vás. Tím se zajistí, že vaše připojení se zavře, pokud to naruší neočekávaně. Další informace najdete v části [tento příspěvek na stackoverflow](http://stackoverflow.com/questions/19502755/websocket-clients-state-not-changing-on-network-loss).

<a id="other"></a>

## <a name="other-connection-issues"></a>Další problémy s připojením

Tato část popisuje příčiny a řešení určitými příznaky nebo chybové zprávy, ke kterým dochází při připojení.

### <a name="start-must-be-called-before-data-can-be-sent-error"></a>Chyba "Start musí být volána před odesláním dat"

Tato chyba obvykle nastává Pokud kód odkazuje na objekty SignalR předtím, než je zahájeno připojení. Wireup pro obslužné rutiny a podobně, že volání metody definované na serveru musí být přidá po dokončení připojení. Všimněte si, že volání `Start` je asynchronní, takže kód následující po volání může být spuštěna dříve, než se dokončí. Nejlepší způsob, jak přidat obslužné rutiny po spuštění připojení zcela je umístíte do zpětného volání funkce, která se předá jako parametr metodě start:

**Klientský kód jazyka JavaScript, který správně přidá obslužné rutiny událostí, které odkazují na objekty SignalR**

[!code-javascript[Main](troubleshooting/samples/sample10.js?highlight=1)]

Tato chyba se zobrazí také v případě připojení přestane při SignalR objekty stále odkazuje.

### <a name="301-moved-permanently-or-302-moved-temporarily-error"></a>"Trvale přesunuto 301" nebo "302 přesunout dočasně" Chyba

Tato chyba může zobrazit, pokud projekt obsahuje složku s názvem SignalR, která bude v konfliktu s proxy automaticky vytvoří. K této chybě předejít, nepoužívejte složku s názvem `SignalR` v aplikaci, nebo vypnout automatické proxy generování vypnout. Zobrazit [The generované Proxy a co to dělá za vás](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy) další podrobnosti.

### <a name="403-forbidden-error-in-net-or-silverlight-client"></a>Chyba "403 Zakázáno" v .NET nebo Silverlight klientu

K této chybě může dojít v prostředí napříč doménami, kde komunikace mezi doménami není povolené vhodným způsobem. Informace o tom, jak povolit komunikaci mezi doménami, najdete v části [jak k navázání připojení mezi doménami](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain). K navázání připojení mezi doménami v klienta programu Silverlight, naleznete v tématu [připojení mezi doménami z klienty prostředí Silverlight](../guide-to-the-api/hubs-api-guide-net-client.md#slcrossdomain).

### <a name="404-not-found-error"></a>Chyba "404 nebyl nalezen."

Existuje několik důvodů, proč tento problém. Ověřte všechny z následujících akcí:

- **Odkaz adresy proxy server rozbočovače není správně naformátované:** Tato chyba obvykle nastává Pokud odkaz na generovaný centra adresa proxy serveru není správně naformátovaná. Ověřte, že odkaz na adresu centra správně vytvořen. Zobrazit [způsob vytvoření odkazu dynamicky generované proxy](../guide-to-the-api/hubs-api-guide-javascript-client.md#dynamicproxy) podrobnosti.
- **Přidání tras pro aplikaci před přidáním trasu rozbočovače:** Pokud vaše aplikace používá jiné trasy, ověřte, zda je první trasa přidaná volání `MapSignalR`.
- **Pomocí služby IIS 7 a 7.5 bez aktualizace pro adresy URL bez přípony:** Pomocí služby IIS 7 a 7.5 vyžaduje aktualizace pro adresy URL bez přípony, tak, aby server může poskytnout přístup k definici rozbočovače na `/signalr/hubs`. Aktualizaci můžete najít [tady](https://support.microsoft.com/kb/980368).
- **Mezipaměť služby IIS je zastaralé nebo poškozený:** Pokud chcete ověřit, že obsah mezipaměti nejsou aktuální, zadejte následující příkaz v okně Powershellu a vymažte mezipaměť:

    [!code-powershell[Main](troubleshooting/samples/sample11.ps1)]

### <a name="500-internal-server-error"></a>"Chyba 500 interní Server"

To je velmi obecná chyba, která by mohla mít nejrůznější příčiny. Podrobnosti o chybě by se měla zobrazit v protokolu událostí serveru, nebo můžete najít pomocí ladění serveru. Když zapnete podrobné chyby na serveru může získat podrobnější informace o chybě. Další informace najdete v tématu [zpracování chyb ve třídě centra](../guide-to-the-api/hubs-api-guide-server.md#handleErrors).

K této chybě také běžně dochází, pokud brána firewall nebo proxy serveru není správně nakonfigurována, způsobí hlavičky požadavku, aby se povolilo. Řešení se ujistěte se, že port 80 je zapnuta brána firewall nebo proxy serveru.

### <a name="unexpected-response-code-500"></a>"Neočekávaný kód: 500"

K této chybě může dojít, pokud verze rozhraní .NET framework používaných v aplikaci se neshoduje verze zadaná v souboru Web.Config. Toto řešení je k ověření, že v nastavení aplikace a souboru Web.Config se používá rozhraní .NET 4.5.

### <a name="typeerror-lthubtypegt-is-undefined-error"></a>"TypeError: &lt;hubType&gt; není definován" Chyba

K této chybě dojde, pokud volání `MapSignalR` není správně vytvořena. Zobrazit [postupem registrace SignalR Middleware a konfiguraci možností SignalR](../guide-to-the-api/hubs-api-guide-server.md#route) Další informace.

### <a name="jsonserializationexception-was-unhandled-by-user-code"></a>JsonSerializationException nebyla ošetřena uživatelským kódem

Ověřte, že parametry, které odesíláte do vaší metody neobsahují neserializovatelná typy (jako jsou popisovače souborů nebo připojení k databázi). Pokud je potřeba použít členy na objekt na straně serveru, který nechcete být zaslána klientovi (buď pro zabezpečení nebo z důvodu serializace), použijte `JSONIgnore` atribut.

### <a name="protocol-error-unknown-transport-error"></a>"Chyba protokolu: Došlo k chybě neznámého přenosu"

K této chybě může dojít, pokud klient nepodporuje přenosy, které používá SignalR. Zobrazit [přenosy a náhrad](../getting-started/introduction-to-signalr.md#transports) informace, na kterém je možné prohlížeče s knihovnou SignalR.

### <a name="javascript-hub-proxy-generation-has-been-disabled"></a>"Byl zakázán jazyk JavaScript rozbočovače proxy generation."

Pokud dojde k této chybě `DisableJavaScriptProxies` nastavit zároveň zahrnuje také odkaz na dynamicky generované proxy serveru na `signalr/hubs`. Další informace o vytvoření proxy serveru ručně, najdete v části [vygenerovaný proxy server a co to dělá za vás](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy).

### <a name="the-connection-id-is-in-the-incorrect-format-or-the-user-identity-cannot-change-during-an-active-signalr-connection-error"></a>"ID připojení je v nesprávném formátu" nebo "identitu uživatele nelze změnit během aktivního připojení SignalR" Chyba

Tato chyba může zobrazit, pokud se používá ověřování a klient je odhlášen před zastavením připojení. Řešení je zastavit před odhlášení klientovi připojení SignalR.

### <a name="uncaught-error-signalr-jquery-not-found-please-ensure-jquery-is-referenced-before-the-signalrjs-file-error"></a>"Nezachycená Chyba: SignalR: jQuery nebyl nalezen. Ujistěte se prosím, že jQuery odkazuje souboru SignalR.js"Chyba

Klient SignalR JavaScript vyžaduje jQuery ke spuštění. Ověřte, že vaši informaci, abyste jQuery je správný, cesta používá je platný a je odkaz na jQuery před referenční dokumentace ke knihovně SignalR.

### <a name="uncaught-typeerror-cannot-read-property-ltpropertygt-of-undefined-error"></a>"Nezachycená TypeError: Nelze přečíst vlastnost '&lt;vlastnost&gt;"undefined" Chyba

Tato chyba je výsledkem nemají jQuery nebo proxy server rozbočovače správně odkazuje. Ověřte, že vaši informaci, jQuery a proxy server rozbočovače je správný, platnost cesty použité a že je odkaz na jQuery před odkaz na proxy server rozbočovače. Výchozí odkaz na proxy server rozbočovače by měl vypadat nějak takto:

**Kód na straně klienta HTML, která správně odkazuje na proxy server rozbočovače**

[!code-html[Main](troubleshooting/samples/sample12.html)]

### <a name="runtimebinderexception-was-unhandled-by-user-code-error"></a>Chyba "RuntimeBinderException nebyla ošetřena uživatelským kódem"

K této chybě může dojít při přetížení nesprávné `Hub.On` se používá. Pokud metoda nemá návratovou hodnotu, návratový typ musí být specifikovaný jako parametr obecného typu:

**Metody definované na straně klienta (bez vygenerovaný proxy server)**

[!code-html[Main](troubleshooting/samples/sample13.html?highlight=1)]

### <a name="connection-id-is-inconsistent-or-connection-breaks-between-page-loads"></a>ID připojení je nekonzistentní nebo přestane fungovat připojení mezi načtení stránky

Toto chování je záměrné. Vzhledem k tomu, že v objektu page je hostovaný objekt rozbočovače, centra je zničen při aktualizaci stránky. Více stránek aplikace potřebuje udržovat spojení mezi uživateli a ID připojení tak, aby byly konzistentní mezi načtení stránky. ID připojení mohou být uloženy na serveru buď `ConcurrentDictionary` objektu nebo databáze.

### <a name="value-cannot-be-null-error"></a>"Hodnota nemůže být null" Chyba

Metody na straně serveru s volitelnými parametry se aktuálně nepodporují; Pokud tento nepovinný parametr se vynechá, metoda se nezdaří. Další informace najdete v tématu [volitelné parametry](https://github.com/SignalR/SignalR/issues/324).

### <a name="firefox-cant-establish-a-connection-to-the-server-at-ltaddressgt-error-in-firebug"></a>"Firefox nemůže navázat připojení k serveru na &lt;adresu&gt;" chyby v Firebug

Tato chybová zpráva lze zobrazit v Firebug, pokud selže vyjednávání protokolu WebSocket přenosu a místo ní se použije jiný přenos. Toto chování je záměrné.

### <a name="the-remote-certificate-is-invalid-according-to-the-validation-procedure-error-in-net-client-application"></a>Chyba "Vzdálený certifikát není platný podle ověřovací procedury" v klientské aplikaci rozhraní .NET

Pokud váš server vyžaduje vlastní klientské certifikáty, pak můžete přidat certifikátu x 509 připojení předtím, než se požadavek. Přidání certifikátu do připojení pomocí `Connection.AddClientCertificate`.

### <a name="connection-drops-after-authentication-times-out"></a>Zrušení připojení vyčkat, až se ověřování vyprší časový limit

Toto chování je záměrné. Přihlašovací údaje pro ověření nelze změnit, když je připojení aktivní; Pokud chcete aktualizovat přihlašovací údaje, musí zastavit, restartovat připojení.

### <a name="onconnected-gets-called-twice-when-using-jquery-mobile"></a>Onconnected rozbočovače volána dvakrát při použití jQuery Mobile

jQuery Mobile pro `initializePage` funkce vynutí skripty na každé stránce, který se znovu spustí, tedy vytvořit druhé připojení. Řešení tohoto problému patří:

- Zahrňte odkaz na architekturu jQuery Mobile před souboru s kódem JavaScript.
- Zakažte `initializePage` funkce tak, že nastavíte `$.mobile.autoInitializePage = false`.
- Počkejte na dokončení inicializace před spuštěním připojení na stránce.

### <a name="messages-are-delayed-in-silverlight-applications-using-server-sent-events"></a>Zprávy jsou zpožděné aplikace Silverlight pomocí události odeslané serverem

Zprávy jsou zpožděné při používání serveru odesílání událostí na programu Silverlight. Pokud chcete vynutit dlouhý interval dotazování, který se má použít místo toho, použijte následující postupy při zahájení připojení:

[!code-css[Main](troubleshooting/samples/sample14.css)]

### <a name="permission-denied-using-forever-frame-protocol"></a>Použití "Oprávnění odepřeno" navždy rámce protokolu

Jedná se o známý problém, popsané [tady](https://github.com/SignalR/SignalR/issues/1963). Tento příznak může zobrazit pomocí nejnovější verze knihovny JQuery; Alternativním řešením je na nižší verzi vaší aplikace do JQuery 1.8.2.

### <a name="invalidoperationexception-not-a-valid-web-socket-request"></a>"InvalidOperationException: Není platný požadavek pro objekt websocket.

K této chybě může dojít, pokud se používá protokol WebSocket, ale síťový proxy server provádí změny v hlavičce požadavku. Řešením je konfigurace proxy serveru na povolení protokolu WebSocket na portu 80.

### <a name="exception-ltmethod-namegt-method-could-not-be-resolved-when-client-calls-method-on-server"></a>"Výjimek: &lt;název metody&gt; metoda nebylo možné přeložit" Když klient volá metodu na serveru

Tato chyba může být následek použití datových typů, které nepůjdou vyhledat v datové části JSON, jako je například pole. Alternativním řešením je použít datový typ, který je ve formátu JSON, jako je například IList zjistitelné. Další informace najdete v tématu [.NET klienta nelze provést volání metod rozbočovače na pole parametrů](https://github.com/SignalR/SignalR/issues/2672).

<a id="server"></a>

## <a name="compilation-and-server-side-errors"></a>Chyby kompilace a na straně serveru

 Následující část obsahuje možná řešení kompilátoru a chyb za běhu na straně serveru.

### <a name="reference-to-hub-instance-is-null"></a>Odkaz na instanci rozbočovače má hodnotu null

Vzhledem k tomu, že pro každé připojení se vytvoří instanci rozbočovače, je nelze vytvořit instanci rozbočovače ve vašem kódu sami. Volání metody v rozbočovači z mimo samotný centra, najdete v článku [klienta volat metody a Správa skupiny mimo třídy rozbočovače](../guide-to-the-api/hubs-api-guide-server.md#callfromoutsidehub) pokyny k získání odkazu na kontext rozbočovače.

### <a name="httpcontextcurrentsession-is-null"></a>HTTPContext.Current.Session má hodnotu null.

Toto chování je záměrné. Funkce SignalR stavu relace ASP.NET nepodporuje, protože stav relace povolení by narušil duplexní zasílání zpráv.

### <a name="no-suitable-method-to-override"></a>Žádná vhodná metoda k přepsání

Tato chyba může zobrazit, pokud používáte kód ze starší dokumentaci nebo v blozích. Ověřte, že nejsou odkazující na názvy metod, které se změnily nebo zastaralý (jako je `OnConnectedAsync`).

### <a name="hostcontextextensionswebsocketserverurl-is-null"></a>HostContextExtensions.WebSocketServerUrl má hodnotu null.

Toto chování je záměrné. Tento člen je zastaralý a neměl by se používat.

### <a name="a-route-named-signalrhubs-is-already-in-the-route-collection-error"></a>Trasa s názvem "signalr.hubs" Chyba "není již v kolekci tras"

Tato chyba se zobrazí, pokud `MapSignalR` je volán dvakrát vaší aplikace. Některé aplikace volání příklad `MapSignalR` přímo ve třídě spuštění; ostatní uskutečnění volání v obálkovou třídu. Ujistěte se, že vaše aplikace není proveďte obojí.

### <a name="websocket-is-not-used"></a>Není použit protokol WebSocket

Pokud jste ověřili, že serverem a klienty splňovat požadavky protokolu websocket (uvedené v [podporované platformy](../getting-started/supported-platforms.md) dokumentu), budete muset povolit objektu websocket na straně serveru. Pokyny, jak to udělat najdete [tady](https://www.iis.net/learn/get-started/whats-new-in-iis-8/iis-80-websocket-protocol-support).

### <a name="connection-is-undefined"></a>není definován $.connection

Tato chyba označuje, že skripty na stránce nebyly správně načteny, nebo proxy server rozbočovače není dostupný nebo je přistupováno nesprávně. Ověřte, že odkazy na skript na stránce odpovídají skripty načtené do vašeho projektu a že /signalr/hubs je přístupná v prohlížeči při spuštění serveru.

### <a name="one-or-more-types-required-to-compile-a-dynamic-expression-cannot-be-found"></a>Nebyl nalezen jeden nebo více typů požadovaných pro kompilaci dynamického výrazu

Tato chyba znamená, `Microsoft.CSharp` chybí knihovna. Přidejte ho **sestavení -&gt;Framework** kartu.

### <a name="caller-state-cannot-be-accessed-from-clientscaller-in-visual-basic-or-in-a-strongly-typed-hub-conversion-from-type-taskof-object-to-type-string-is-not-valid-error"></a>Stav volajícího nelze přistupovat z Clients.Caller v jazyce Visual Basic nebo rozbočovač silného typu; Chyba "Převod z typu 'Task (Object o)' na typ"Řetězec"není platný"

Chcete-li získat přístup k stav volajícího v jazyce Visual Basic nebo rozbočovač silného typu, použijte `Clients.CallerState` vlastnosti (představíme v SignalR 2.1) namísto `Clients.Caller`.

<a id="vs"></a>

## <a name="visual-studio-issues"></a>Problémy v sadě Visual Studio

Tato část popisuje problémy v sadě Visual Studio.

### <a name="script-documents-node-does-not-appear-in-solution-explorer"></a>Uzlu dokumenty skriptu se nezobrazují v Průzkumníku řešení

Některé z našich kurzů nasměrujeme na uzlu "Dokumenty skriptu" v Průzkumníku řešení během ladění. Tento uzel je vytvořen pomocí ladicího programu jazyka JavaScript a zobrazí se pouze při ladění klienty prohlížeče v Internet Exploreru; uzel se nezobrazí, pokud se používají Chrome nebo Firefox. Ladicí program jazyka JavaScript také nespustí, pokud je spuštěn jiný ladicí program klienta, jako je například ladicí program Silverlight.

### <a name="signalr-does-not-work-on-visual-studio-2008-or-earlier"></a>Funkce SignalR nefunguje v sadě Visual Studio 2008 nebo dřívější

Toto chování je záměrné. Funkce SignalR vyžaduje rozhraní .NET Framework 4 nebo novější; Tento postup vyžaduje, aby aplikace knihovnou SignalR vyvíjet v sadě Visual Studio 2010 nebo novější. Součásti serveru pro funkci SignalR vyžaduje rozhraní .NET Framework 4.5.

<a id="iis"></a>

## <a name="iis-issues"></a>Problémy s IIS

Tato část obsahuje problémy se službou IIS.

### <a name="signalr-works-on-visual-studio-development-server-but-not-in-iis"></a>Funkce SignalR funguje na vývojový server sady Visual Studio, ale ne ve službě IIS

SignalR je podporované ve službě IIS 7.0 a 7.5, ale podpora pro adresy URL bez přípony musí být přidán. Přidání podpory pro adresy URL bez přípony, naleznete v tématu [https://support.microsoft.com/kb/980368](https://support.microsoft.com/kb/980368)

Vyžaduje SignalR technologie ASP.NET být nainstalovaný na serveru (není nainstalována technologie ASP.NET ve službě IIS ve výchozím nastavení). Instalace technologie ASP.NET, naleznete v tématu [ASP.NET stáhne](https://www.asp.net/downloads).

<a id="azure"></a>

## <a name="microsoft-azure-issues"></a>Problémy s Microsoft Azure

Tato část obsahuje problémy s Microsoft Azure.

### <a name="fileloadexception-when-hosting-signalr-in-an-azure-worker-role"></a>FileLoadException – při hostování za nástrojem SignalR v roli pracovního procesu Azure

Hostování SignalR v roli pracovního procesu Azure může mít za následek výjimku "nepovedlo se načíst soubor nebo sestavení" Microsoft.Owin, verze = 2.0.0.0 ". Jedná se o známý problém s NuGet; Přesměrování vazeb nejsou automaticky přidáni do Role pracovního procesu Azure projekty. Chcete-li to vyřešit, můžete přidat přesměrování vazby ručně. Přidejte následující řádky do `app.config` soubor pro váš projekt Role pracovního procesu.

[!code-xml[Main](troubleshooting/samples/sample15.xml)]

### <a name="messages-are-not-received-through-the-azure-backplane-after-altering-topic-names"></a>Nejsou přijaty zprávy přes Azure propojovací rozhraní systému po změně názvy témat

Témata používá Azure propojovací rozhraní systému se zachovají interně; Chcete-li být uživatelem konfigurovatelné nejsou určeny.

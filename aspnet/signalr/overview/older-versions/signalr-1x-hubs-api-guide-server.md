---
uid: signalr/overview/older-versions/signalr-1x-hubs-api-guide-server
title: Pokyny k rozhraní API Center SignalR technologie ASP.NET – Server (SignalR 1.x) | Dokumentace Microsoftu
author: pfletcher
description: Tento dokument obsahuje úvod do programování na straně serveru rozhraní API pro rozbočovače SignalR technologie ASP.NET SignalR verze 1.1, s demonstratin ukázky kódu...
ms.author: riande
ms.date: 04/17/2013
ms.assetid: 03e4b9f5-0fea-4d94-959f-014b2762a301
msc.legacyurl: /signalr/overview/older-versions/signalr-1x-hubs-api-guide-server
msc.type: authoredcontent
ms.openlocfilehash: a51a2077e0b6cde80bc679e3a310c0c804d19d68
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53288019"
---
<a name="aspnet-signalr-hubs-api-guide---server-signalr-1x"></a>Pokyny k rozhraní API Center SignalR technologie ASP.NET – Server (SignalR 1.x)
====================
podle [Patrick Fletcher](https://github.com/pfletcher), [Petr Dykstra](https://github.com/tdykstra)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento dokument obsahuje úvod do programování na straně serveru rozhraní API pro rozbočovače SignalR technologie ASP.NET SignalR verze 1.1, s představením běžných možností ukázky kódu.
> 
> Rozhraní API pro rozbočovače SignalR umožňuje vytvářet vzdálených volání procedur (RPC) ze serveru pro připojené klienty a z klientů k serveru. V serverovém kódu můžete definovat metody, které mohou být volány klientů a volat metody, které běží na straně klienta. V klientském kódu můžete definovat metody, které lze volat ze serveru a volání metody, které běží na serveru. Funkce SignalR postará za vás zajistí funkčnost systému klient server.
> 
> Funkce SignalR také nabízí nižší úrovně rozhraní API volá trvalé připojení. Úvod do SignalR, rozbočovačů a trvalá připojení, nebo kurz, který ukazuje, jak sestavit kompletní aplikace SignalR, přečtěte si téma [SignalR – Začínáme](index.md).


## <a name="overview"></a>Přehled

Tento dokument obsahuje následující části:

- [Jak zaregistrovat směrování funkce SignalR a nakonfigurovat možnosti SignalR](#route)

    - [Adresa URL /signalr](#signalrurl)
    - [Konfigurace možností SignalR](#options)
- [Jak vytvořit a použít třídy rozbočovače](#hubclass)

    - [Doba života objektu centra](#transience)
    - [Camel-malých a velkých písmen názvů centra v klientech jazyka JavaScript](#hubnames)
    - [Více rozbočovače](#multiplehubs)
- [Definování metody ve třídě rozbočovače, která může volat klientů](#hubmethods)

    - [Camel-malých a velkých písmen názvů metody do klientů JavaScript](#methodnames)
    - [Kdy se má spustit asynchronně](#asyncmethods)
    - [Definování přetížení](#overloads)
- [Volání metody klienta z třídy rozbočovače](#callfromhub)

    - [Výběr klientů, kteří obdrží vzdálené volání Procedur](#selectingclients)
    - [Žádné názvy metod ověřování za kompilace](#dynamicmethodnames)
    - [Shoda názvu malá a velká písmena – metoda](#caseinsensitive)
    - [Asynchronní provádění](#asyncclient)
- [Správa členství ve skupině ze třídy rozbočovače](#groupsfromhub)

    - [Asynchronní provádění metody přidat a odebrat](#asyncgroupmethods)
    - [Trvalost členství ve skupině](#grouppersistence)
    - [Skupiny jednoho uživatele](#singleusergroups)
- [Zpracování událostí doby platnosti ve třídě centra](#connectionlifetime)

    - [Kdy jsou volány onconnected rozbočovače, ondisconnected rozbočovače a onreconnected rozbočovače](#onreconnected)
    - [Stav volajícího není naplněn.](#nocallerstate)
- [Jak získat informace o klientovi z kontextové vlastnosti](#contextproperty)
- [Jak předávat stavu mezi klienty a třídy rozbočovače](#passstate)
- [Zpracování chyb ve třídě centra](#handleErrors)
- [Jak volat metody klienta a spravovat skupiny mimo třídy rozbočovače](#callfromoutsidehub)

    - [Volání metody klienta](#callingclientsoutsidehub)
    - [Správa členství ve skupině](#managinggroupsoutsidehub)
- [Jak povolit trasování](#tracing)
- [Přizpůsobení kanálu rozbočovače](#hubpipeline)

Dokumentace o tom, jak program klientů naleznete v následujících zdrojích:

- [Pokyny k rozhraní API Center SignalR – javascriptový klient](index.md)
- [Pokyny k rozhraní API Center SignalR – klient .NET](index.md)

Odkazy na témata, Reference k rozhraní API se API verze rozhraní .NET 4.5. Pokud používáte .NET 4, přečtěte si téma [verze .NET 4 témat API](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).

<a id="route"></a>

## <a name="how-to-register-the-signalr-route-and-configure-signalr-options"></a>Jak zaregistrovat směrování funkce SignalR a nakonfigurovat možnosti SignalR

Chcete-li definovat trasy, která budou klienti používat pro připojení k centru, zavolejte [MapHubs](https://msdn.microsoft.com/library/system.web.routing.signalrrouteextensions.maphubs(v=vs.111).aspx) metoda při spuštění aplikace. `MapHubs` je [– metoda rozšíření](https://msdn.microsoft.com/library/vstudio/bb383977.aspx) pro `System.Web.Routing.RouteCollection` třídy. Následující příklad ukazuje, jak definovat trasu rozbočovače SignalR v *Global.asax* souboru.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample1.cs)]

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample2.cs?highlight=5)]

Pokud přidáváte funkce SignalR pro aplikace ASP.NET MVC, ujistěte se, přidá se trasa SignalR před jiným trasám. Další informace najdete v tématu [kurzu: Začínáme s knihovnou SignalR a MVC 4](index.md).

<a id="signalrurl"></a>

### <a name="the-signalr-url"></a>Adresa URL /signalr

Ve výchozím nastavení, je adresa URL trasy, které budou klienti používat pro připojení k vašemu centru "/ signalr". (Nezaměňujte tuto adresu URL s adresou URL "/ signalr/centra", která je automaticky vygenerovaný soubor jazyka JavaScript. Další informace o vygenerovaný proxy server, naleznete v tématu [pokyny k rozhraní API Center SignalR – javascriptový klient - vygenerovaný proxy server a co to dělá za vás](index.md).)

Může být výjimečných případech, které zkontrolujte tuto základní adresu URL nelze použít pro funkci SignalR; například máte složku ve vašem projektu s názvem *signalr* a nechcete, aby změna názvu. V takovém případě můžete změnit základní adresa URL, jak je znázorněno v následujících příkladech (nahradit "/ signalr" ve vzorovém kódu s požadovanou adresu URL).

**Kód serveru, který určuje adresu URL**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample3.cs?highlight=1)]

**Klientský kód jazyka JavaScript, který určuje adresu URL (s vygenerovaný proxy server)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample4.js?highlight=1)]

**Klientský kód jazyka JavaScript, který určuje adresu URL (bez vygenerovaný proxy server)**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample5.js?highlight=1)]

**Klientský kód .NET, který určuje adresu URL**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample6.cs?highlight=1)]

<a id="options"></a>

### <a name="configuring-signalr-options"></a>Konfigurace možností SignalR

Přetížení `MapHubs` metody umožňují určit vlastní adresu URL, překladače vlastní závislost a následující možnosti:

- Povolte volání mezi doménami z prohlížečů klientů.

    Obvykle Pokud prohlížeč načte stránku z `http://contoso.com`, připojení SignalR je ve stejné doméně, v `http://contoso.com/signalr`. Pokud stránku z `http://contoso.com` vytvoří připojení k `http://fabrikam.com/signalr`, to znamená připojení mezi doménami. Z bezpečnostních důvodů jsou ve výchozím nastavení zakázané připojení mezi doménami. Další informace najdete v tématu [ASP.NET pokyny k rozhraní API Center SignalR – javascriptový klient – jak k navázání připojení mezi doménami](index.md).
- Povolte podrobné chybové zprávy.

    Když dojde k chybám, je výchozí chování SignalR klientům odeslat zprávu oznámení bez podrobnosti o co se stalo. Odesílání podrobné informace o chybě pro klienty se nedoporučuje v produkčním prostředí, protože uživatelé se zlými úmysly může být schopni použijte informace v útoků, které vaše aplikace. S řešením problémů, můžete použít tuto možnost dočasně povolit informativnější zasílání zpráv o chybách.
- Zakážete automaticky generovaných souborů proxy server JavaScript.

    Ve výchozím nastavení je soubor JavaScript s proxy pro vaše Centrum třídy vygenerované v reakci na adresu URL "/ signalr/centra". Pokud už nechcete používat třídy proxy JavaScript, nebo pokud chcete vygenerovat soubor ručně a odkazovat na fyzický soubor v vašim klientům, můžete tuto možnost zakažte generování proxy serveru. Další informace najdete v tématu [pokyny k rozhraní API Center SignalR – javascriptový klient – proxy generované vytváření fyzického souboru pro funkci SignalR](index.md).

Následující příklad ukazuje, jak zadat adresu URL připojení SignalR a tyto možnosti ve volání `MapHubs` metody. Chcete-li určit vlastní adresu URL, nahraďte "/ signalr" v příkladu nahraďte adresu URL, kterou chcete použít.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample7.cs)]

<a id="hubclass"></a>

## <a name="how-to-create-and-use-hub-classes"></a>Jak vytvořit a použít třídy rozbočovače

Pokud chcete Centrum vytvořit, vytvořit třídu, která je odvozena z [Microsoft.Aspnet.Signalr.Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx). Následující příklad ukazuje jednoduchý třída rozbočovače pro chatovací aplikaci.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample8.cs)]

V tomto příkladu můžete volat připojený klient `NewContosoChatMessage` metoda, a pokud ano, je přijatá data vysílali pro všechny připojené klienty.

<a id="transience"></a>

### <a name="hub-object-lifetime"></a>Doba života objektu centra

Nevidíte vytvořit instanci třídy rozbočovače nebo volání obsažených metod z vlastního kódu na serveru. všechny možnosti, které se za vás provádí kanálu rozbočovače SignalR. Funkce SignalR vytvoří novou instanci třídy rozbočovače pokaždé, když je potřeba zpracovat operaci rozbočovače, jako je například při připojení, odpojení klienta nebo je volána metoda k serveru.

Protože instance třídy centra jsou přechodné, nelze je použít pro uchování stavu z jedné metody volání na další. Pokaždé, když server přijímá volání metody z klienta, novou instanci třídy procesů centra zprávy. Pro uchování stavu prostřednictvím více připojení a volání metod, pomocí některé jiné metody, jako jsou databáze nebo statická proměnná v třídě rozbočovač nebo jinou třídu, která není odvozena od `Hub`. Pokud můžete uchovávat data v paměti, způsobem například statická proměnná u třídy rozbočovače, data se ztratí dojde k recyklování domény aplikace.

Pokud chcete odesílat zprávy pro klienty z vlastní kód, který běží mimo třídy rozbočovače, nemůžete to udělal po vytvoření instance třídy instanci rozbočovače, ale dělejte to tím, že získáme odkaz na objekt kontextu SignalR pro rozbočovač třídu. Další informace najdete v tématu [klienta volat metody a Správa skupiny mimo třídy rozbočovače](#callfromoutsidehub) dále v tomto tématu.

<a id="hubnames"></a>

### <a name="camel-casing-of-hub-names-in-javascript-clients"></a>Camel-malých a velkých písmen názvů centra v klientech jazyka JavaScript

Ve výchozím nastavení klientů JavaScript odkazovat centra pomocí verze název třídy – ve formátu camelCase. Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript. V předchozím příkladu by se označuje jako `contosoChatHub` v kódu jazyka JavaScript.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample9.cs?highlight=1)]

**JavaScript klienta s použitím vygenerovaný proxy server**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample10.js?highlight=1)]

Pokud chcete zadat jiný název pro klienty použít, přidejte `HubName` atribut. Při použití `HubName` atribut, není žádná změna název na formát camelCase u klientů jazyka JavaScript.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample11.cs?highlight=1)]

**JavaScript klienta s použitím vygenerovaný proxy server**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample12.js?highlight=1)]

<a id="multiplehubs"></a>

### <a name="multiple-hubs"></a>Více rozbočovače

V aplikaci můžete definovat více tříd rozbočovače. Když to uděláte, připojení se sdílí, ale jsou oddělené skupiny:

- Budou všichni klienti používat k navázání připojení SignalR ve vaší službě stejná adresa URL ("/ signalr" nebo vaší vlastní adresu URL, pokud byl zadán), a používá se, že připojení pro všechna centra definovány službou.

    Neexistuje žádné rozdíly ve výkonnosti více hubs ve srovnání s definování všechny funkce centra v jedné třídě.
- Všechna centra získat stejné informace o požadavku HTTP.

    Od všech centrech sdílejí stejné připojení, je pouze informace žádosti HTTP, která vrací na server, co se dodává v původní požadavek protokolu HTTP, která vytváří připojení SignalR. Pokud používáte žádosti o připojení k předávání informací od klienta k serveru zadáním řetězce dotazu, nemůže poskytnout různé řetězce dotazu do různých rozbočovače. Všechna centra obdrží stejné informace.
- Vygenerovaný soubor JavaScript proxy bude obsahovat servery proxy pro všechna centra v jednom souboru.

    Informace o třídy proxy JavaScript naleznete v tématu [pokyny k rozhraní API Center SignalR – javascriptový klient - vygenerovaný proxy server a co to dělá za vás](index.md).
- Skupiny jsou definovány v rámci rozbočovače.

    V knihovně SignalR, které můžete definovat pojmenovaným skupinám na podmnožiny připojených klientů. Skupiny jsou spravovány samostatně pro každý rozbočovač. Například skupina s názvem "Administrators" zahrnuje jednu sadu klientů pro vaše `ContosoChatHub` třídy a stejný název skupiny by odkazovat na jinou sadu klientů pro vaše `StockTickerHub` třídy.

<a id="hubmethods"></a>

## <a name="how-to-define-methods-in-the-hub-class-that-clients-can-call"></a>Definování metody ve třídě rozbočovače, která může volat klientů

Chcete-li vystavit metodu v rozbočovači, které mají být volány z klienta, deklarujte veřejnou metodu, jak je znázorněno v následujícím příkladu.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample13.cs?highlight=3)]

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample14.cs?highlight=3)]

Můžete určit návratový typ a parametry, včetně komplexní typy a pole, stejně jako v jakékoli metodě jazyka C#. Všechna data, která zobrazí v parametrech nebo vrácení volajícímu je komunikace mezi klientem a serverem s použitím souboru JSON a SignalR automaticky zpracovává vazby složité objekty a pole objektů.

<a id="methodnames"></a>

### <a name="camel-casing-of-method-names-in-javascript-clients"></a>Camel-malých a velkých písmen názvů metody do klientů JavaScript

Ve výchozím nastavení klienti JavaScript odkazovat metod rozbočovače na pomocí-ve formátu camelCase verzi název metody. Funkce SignalR tato změna automaticky provede tak, aby kód jazyka JavaScript může odpovídat konvence jazyka JavaScript.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample15.cs?highlight=1)]

**JavaScript klienta s použitím vygenerovaný proxy server**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample16.js?highlight=1)]

Pokud chcete zadat jiný název pro klienty použít, přidejte `HubMethodName` atribut.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample17.cs?highlight=1)]

**JavaScript klienta s použitím vygenerovaný proxy server**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample18.js?highlight=1)]

<a id="asyncmethods"></a>

### <a name="when-to-execute-asynchronously"></a>Kdy se má spustit asynchronně

Metoda bude být dlouhotrvající nebo má práce, které by zahrnovat čekání, jako je například vyhledávání v databázi nebo volání webové služby, vrácením provést asynchronní metody rozbočovače [úloh](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) (místo `void` vrátit) nebo [ Úloha&lt;T&gt; ](https://msdn.microsoft.com/library/dd321424.aspx) objektu (místo `T` návratový typ). Po návratu `Task` objekt z metody SignalR čeká `Task` k dokončení, a pak pošle nezabalené výsledek zpět do klienta, takže není žádný rozdíl v tom, jak kód volání metody v klientovi.

Provedení metody rozbočovače asynchronní se vyhnete blokuje připojení, když používá přenos pomocí protokolu WebSocket. Když je přenos pomocí protokolu WebSocket centra metoda provedena synchronně, následné volání metod rozbočovače ze stejného klienta jsou blokovány, dokud dokončení metody rozbočovače.

Následující příklad ukazuje stejnou metodu naprogramovány tak, aby běžely synchronně nebo asynchronně, za nímž následuje klientský kód jazyka JavaScript, který se dá použít pro volání jedné verze.

**Synchronní**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample19.cs)]

**Asynchronní – ASP.NET 4.5**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample20.cs?highlight=1,7-8)]

**JavaScript klienta s použitím vygenerovaný proxy server**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample21.js)]

Další informace o tom, jak použít asynchronní metody v technologii ASP.NET 4.5 naleznete v tématu [použití asynchronních metod v architektuře ASP.NET MVC 4](../../../mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4.md).

<a id="overloads"></a>

### <a name="defining-overloads"></a>Definování přetížení

Pokud chcete definovat přetížení pro metodu, musí být jiný počet parametrů v každé přetížení. Rozlišení přetížení pouze zadáním různé typy parametrů, bude zkompilována vaše třída rozbočovače, ale služby SignalR vyvolá výjimku v době běhu, když se klienti pokusí volání jednoho z přetížení.

<a id="callfromhub"></a>

## <a name="how-to-call-client-methods-from-the-hub-class"></a>Volání metody klienta z třídy rozbočovače

Chcete-li volat metody klienta ze serveru, použijte `Clients` vlastnost v metodě ve své třídě rozbočovače. Následující příklad ukazuje kód serveru, který volá `addNewMessageToPage` na všechny připojené klienty a klientský kód, který definuje metodu v klientovi JavaScript.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample22.cs?highlight=5)]

**JavaScript klienta s použitím vygenerovaný proxy server**

[!code-html[Main](signalr-1x-hubs-api-guide-server/samples/sample23.html?highlight=1)]

Nelze získat návratovou hodnotu z metody; syntaxe jako `int x = Clients.All.add(1,1)` nefunguje.

Můžete zadat komplexní typy a pole parametrů. Následující příklad předá komplexní typ klienta v parametru metody.

**Kód serveru, který volá metodu klienta pomocí komplexního objektu**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample24.cs?highlight=3)]

**Kód serveru, který definuje komplexní objekt**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample25.cs?highlight=1)]

**JavaScript klienta s použitím vygenerovaný proxy server**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample26.js?highlight=2-3)]

<a id="selectingclients"></a>

### <a name="selecting-which-clients-will-receive-the-rpc"></a>Výběr klientů, kteří obdrží vzdálené volání Procedur

Vrátí vlastnost klientů [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx) objekt, který poskytuje několik možností pro určení, které klienti obdrží vzdálené volání Procedur:

- Všichni připojení klienti.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample27.cs)]
- Pouze volajícího klienta.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample28.cs)]
- Všichni klienti s výjimkou volajícího klienta.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample29.cs)]
- Konkrétního klienta, které identifikují pomocí ID připojení.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample30.css)]

    Tento příklad příkladu volá `addContosoChatMessageToPage` na volajícího klienta a má stejný účinek jako použití `Clients.Caller`.
- Všichni připojení klienti s výjimkou určitých klientech identifikují pomocí ID připojení.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample31.cs)]
- Všichni připojení klienti do zadané skupiny.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample32.css)]
- Všechny připojené klienty v zadané skupině s výjimkou určitých klientech identifikují pomocí ID připojení.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample33.cs)]
- Všichni připojení klienti v zadané skupině s výjimkou volajícího klienta.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample34.css)]

<a id="dynamicmethodnames"></a>

### <a name="no-compile-time-validation-for-method-names"></a>Žádné názvy metod ověřování za kompilace

Název metody, který zadáte, je interpretován jako dynamický objekt, což znamená, že odpadá technologie IntelliSense a ověření za kompilace pro něj. Výraz je vyhodnocen v době běhu. Při volání metody, které spouští, SignalR odešle klientovi názvu metody a hodnoty parametrů, a pokud má klient metodu, která odpovídá názvu, že metoda je volána a hodnoty parametru jsou předány do něj. Pokud na straně klienta se nenašla žádná odpovídající metoda, je vyvolána žádná chyba. Informace o formátu dat, která SignalR odesílá do klienta na pozadí při volání metody klienta najdete v tématu [Úvod ke knihovně SignalR](index.md).

<a id="caseinsensitive"></a>

### <a name="case-insensitive-method-name-matching"></a>Shoda názvu malá a velká písmena – metoda

Shoda názvu metody je velká a malá písmena. Například `Clients.All.addContosoChatMessageToPage` na serveru spustí `AddContosoChatMessageToPage`, `addcontosochatmessagetopage`, nebo `addContosoChatMessageToPage` na straně klienta.

<a id="asyncclient"></a>

### <a name="asynchronous-execution"></a>Asynchronní provádění

Asynchronně provede metodu, kterou je možné volat. Veškerý kód, který se dodává po volání metody do klienta se spustí okamžitě bez čekání na SignalR k dokončení přenosu dat do klientů, není-li určit, že následující řádky kódu by měla počkat na dokončení metody. Následující příklady kódu ukazují, jak provést dvě metody klienta postupně, ji pomocí kódu funkčnosti v rozhraní .NET 4.5 a ji pomocí kódu funkčnosti v rozhraní .NET 4.

**Příklad rozhraní .NET 4.5**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample35.cs?highlight=1,3)]

**Příklad rozhraní .NET 4**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample36.cs?highlight=3-4)]

Pokud používáte `await` nebo `ContinueWith` počkat, dokud se nedokončí metodu klienta předtím, než provede další řádek kódu, který nemusí znamenat, že klienti ve skutečnosti obdrží zprávu před provedením další řádek kódu. "Dokončení" volání metody klienta pouze znamená, že SignalR provedla vše potřebné k odeslání zprávy. Pokud potřebujete ověření, že klienti zobrazila zpráva, budete muset tento mechanismus program sami. Například může kód `MessageReceived` metodu v rozbočovači a v `addContosoChatMessageToPage` metody na straně klienta lze volat `MessageReceived` po provedení činnost, kterou je třeba provést na straně klienta. V `MessageReceived` v centru vám pomůžou práci závisí na skutečným klientem příjem a zpracování původní volání metody.

### <a name="how-to-use-a-string-variable-as-the-method-name"></a>Použití proměnné řetězce jako názvu – metoda

Pokud chcete vyvolat metodu klienta s použitím proměnné řetězce jako názvu metody přetypování `Clients.All` (nebo `Clients.Others`, `Clients.Caller`atd) k `IClientProxy` a následně zavolat [Invoke (methodName, args...) ](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.iclientproxy.invoke(v=vs.111).aspx).

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample37.cs)]

<a id="groupsfromhub"></a>

## <a name="how-to-manage-group-membership-from-the-hub-class"></a>Správa členství ve skupině ze třídy rozbočovače

Skupinami v knihovně SignalR poskytuje metodu pro vysílání zpráv do zadaného podmnožiny připojených klientů. Skupina může mít libovolný počet klientů a klienta můžete mít libovolný počet skupin.

Chcete-li spravovat členství ve skupině, použijte [přidat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) a [odebrat](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) metody poskytované objektem `Groups` vlastnosti třídy rozbočovače. Následující příklad ukazuje `Groups.Add` a `Groups.Remove` metod používaných v metodách rozbočovače, které jsou volány kódem na straně klienta, za nímž následuje klientský kód jazyka JavaScript, který je volá.

**Server**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample38.cs?highlight=5,10)]

**JavaScript klienta s použitím vygenerovaný proxy server**

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample39.js)]

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample40.js)]

Není nutné explicitně vytvářet skupiny. V platnosti skupiny se automaticky vytvoří při prvním zadejte jeho název ve volání `Groups.Add`, a bude odstraněn, když odeberete poslední připojení z členství v ní.

Neexistuje žádné rozhraní API pro získání seznamu členství ve skupinách nebo seznam skupin. Funkce SignalR odesílá zprávy do klientů a skupin na základě [modelu pub/sub](http://en.wikipedia.org/wiki/Publish/subscribe), a server neumožňuje spravovat seznam skupin nebo členství ve skupinách. To pomáhá maximalizovat škálovatelnost, protože pokaždé, když přidáte uzel do webové farmy, jakýkoli stav, který udržuje SignalR musí být rozšířena na nový uzel.

<a id="asyncgroupmethods"></a>

### <a name="asynchronous-execution-of-add-and-remove-methods"></a>Asynchronní provádění metody přidat a odebrat

`Groups.Add` a `Groups.Remove` metody spustit asynchronně. Pokud chcete přidat klienta do skupiny a okamžitě odešle zprávu do klienta pomocí skupiny, budete muset Ujistěte se, že `Groups.Add` metoda skončí jako první. Následující příklady kódu ukazují, jak to udělat, ji pomocí kódu, který funguje v rozhraní .NET 4.5 a pomocí kódu, který funguje v rozhraní .NET 4

**Příklad rozhraní .NET 4.5**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample41.cs?highlight=1,3)]

**Příklad rozhraní .NET 4**

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample42.cs?highlight=3-4)]

<a id="grouppersistence"></a>

### <a name="group-membership-persistence"></a>Trvalost členství ve skupině

Sleduje připojení SignalR, ne s uživateli, takže když chcete uživateli být ve stejné skupině pokaždé, když uživatel vytváří připojení, je nutné volat `Groups.Add` pokaždé, když uživatel vytvoří nové připojení.

Po dočasné ztrátě připojení někdy SignalR můžete obnovit připojení automaticky. V takovém případě SignalR je obnovení stejného připojení není vytvoření nového připojení, a proto se členství ve skupině klienta automaticky obnoví. To je možné i v případě, že dočasné přerušení je restartování serveru nebo selhání, protože stav připojení pro každého klienta, včetně členství ve skupinách, je odbavovaná klientovi. Pokud server přestane fungovat a nahrazuje nový server, než vyprší časový limit připojení, může klient automaticky znovu připojit k novému serveru a znovu zaregistrovat ve skupinách, který je členem skupiny.

Pokud po ztrátě připojení, nelze automaticky obnovit připojení, nebo při připojení vyprší časový limit, nebo při odpojení klienta (například když prohlížeči přejde na novou stránku), členství ve skupinách budou ztraceny. Při příštím připojení uživatele bude nové připojení. Chcete-li udržovat členství ve skupinách, pokud se stejnému uživateli vytvoří nové připojení, sledování přidružení mezi uživatelů a skupin a členství ve skupinách obnovení pokaždé, když uživatel vytvoří nové připojení má vaše aplikace.

Další informace o připojení a opětovná připojení najdete v tématu [způsob zpracování událostí doby platnosti ve třídě centra](#connectionlifetime) dále v tomto tématu.

<a id="singleusergroups"></a>

### <a name="single-user-groups"></a>Skupiny jednoho uživatele

Aplikace, které používají funkci SignalR obvykle nutné udržovat přehled o asociace mezi uživateli a připojení, pokud chcete zjistit, který uživatel odeslal zprávu a které uživatelé měli přijímání zprávy. Skupiny se používají v jednom ze dvou běžně používané vzory pro učinit.

- Skupiny jednoho uživatele.

    Můžete zadat uživatelské jméno jako název skupiny a do skupiny přidat aktuální ID připojení, pokaždé, když uživatel připojí, nebo znovu připojí. K odeslání zprávy pro uživatele, které odesíláte do skupiny. Nevýhodou této metody je, že skupina nemá poskytují způsob, jak zjistit, zda uživatel je online nebo offline.
- Sledujte přidružení mezi uživatelská jména a ID připojení.

    Můžete ukládat přidružení mezi každé uživatelské jméno a ID jeden nebo více připojení ve slovníku nebo v databázi a aktualizace uložených dat pokaždé, když uživatel připojí nebo odpojí. K odeslání zprávy uživateli zadat ID připojení. Nevýhodou této metody je, že přijímá větší množství paměti.

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events-in-the-hub-class"></a>Zpracování událostí doby platnosti ve třídě centra

Obvyklé důvody pro zpracování událostí doby platnosti jsou ke sledování, zda je uživatel připojen nebo Ne a mějte přehled o přidružení mezi uživatelská jména a ID připojení. Váš vlastní kód spustit, když klienti připojovat nebo odpojovat, přepsat `OnConnected`, `OnDisconnected`, a `OnReconnected` třídy virtuální metody rozbočovače, jak je znázorněno v následujícím příkladu.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample43.cs?highlight=3,14,22)]

<a id="onreconnected"></a>

### <a name="when-onconnected-ondisconnected-and-onreconnected-are-called"></a>Kdy jsou volány onconnected rozbočovače, ondisconnected rozbočovače a onreconnected rozbočovače

Pokaždé, když prohlížeč přejde na novou stránku, nové připojení musí být zavedena, což znamená, že se spustí SignalR `OnDisconnected` následuje metoda `OnConnected` metoda. Funkce SignalR vždy vytvoří nové ID připojení, po vytvoření nového připojení.

`OnReconnected` Metoda je volána, když bude zjištěna dočasné přerušení připojení, které funkci SignalR může automaticky zotavit po, například když kabelu dočasně odpojení a opětovném připojení předtím, než vyprší časový limit připojení. `OnDisconnected` Metoda se volá, když je klient odpojen a SignalR nelze znovu připojit automaticky, například pokud prohlížeč přejde na novou stránku. Proto je možné pořadí událostí pro daného klienta `OnConnected`, `OnReconnected`, `OnDisconnected`; nebo `OnConnected`, `OnDisconnected`. Neuvidíte sekvence `OnConnected`, `OnDisconnected`, `OnReconnected` pro dané připojení.

`OnDisconnected` Metoda nebude zavolána v některých případech, například když server ocitne mimo provoz nebo doména aplikace získá recyklován. Když jiný server je v řádku nebo doména aplikace dokončí jeho Koš, může být někteří klienti moci znovu připojit a aktivuje `OnReconnected` událostí.

Další informace najdete v tématu [principy a zpracování událostí doby platnosti v knihovně SignalR](index.md).

<a id="nocallerstate"></a>

### <a name="caller-state-not-populated"></a>Stav volajícího není naplněn.

Metody zpracování událostí životního cyklu připojení se nazývají ze serveru, což znamená, že jakýkoli stav, kam si ukládáte `state` objektu na straně klienta nebude dosazeny `Caller` vlastnost na serveru. Informace o `state` objektu a `Caller` vlastnost, naleznete v tématu [jak předat stavu mezi klienty a třídy rozbočovače](#passstate) dále v tomto tématu.

<a id="contextproperty"></a>

## <a name="how-to-get-information-about-the-client-from-the-context-property"></a>Jak získat informace o klientovi z kontextové vlastnosti

Chcete-li získat informace o klientovi, použijte `Context` vlastnosti třídy rozbočovače. `Context` Vrátí vlastnost [HubCallerContext](https://msdn.microsoft.com/library/jj890883(v=vs.111).aspx) objekt, který poskytuje přístup k následujícím informacím:

- ID připojení volajícího klienta.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample44.cs?highlight=1)]

    ID připojení je identifikátor GUID, který je přiřazen nástrojem SignalR (hodnota nelze zadat ve svém vlastním kódu). Existuje jedno ID připojení pro každé připojení a stejného připojení ID se používá ve všech centrech, pokud máte více Center ve vaší aplikaci.
- Data hlavičky protokolu HTTP.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample45.cs?highlight=1)]

    Můžete také získat hlavičky protokolu HTTP z `Context.Headers`. Z důvodu více odkazů na stejnou věc je, že `Context.Headers` byla vytvořena jako první, `Context.Request` vlastnost byla přidána později, a `Context.Headers` se zachovává kvůli zpětné kompatibilitě.
- Data řetězce dotazu.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample46.cs?highlight=1)]

    Můžete také získat data řetězce dotazu z `Context.QueryString`.

    Řetězec dotazu, který dostanete v této vlastnosti je ten, který byl použit v požadavku HTTP, který navázalo se připojení SignalR. Můžete přidat parametry řetězce dotazu v klientovi nakonfigurováním připojení, které je pohodlný způsob, jak předat data o klientovi z klienta na server. Následující příklad ukazuje jeden způsob, jak přidat řetězec dotazu v jazyce JavaScript klienta při použití vygenerovaný proxy server.

    [!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample47.js?highlight=1)]

    Další informace o nastavení parametrů řetězce dotazu, naleznete v tématu příručky rozhraní API pro [JavaScript](index.md) a [.NET](index.md) klientů.

    Můžete najít metodu přenosu používá pro připojení v data řetězce dotazu spolu s některé jiné hodnoty používaná interně knihovnou SignalR:

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample48.cs)]

    Hodnota `transportMethod` bude "webSockets", "serverSentEvents", "foreverFrame" nebo "longPolling". Poznámka: Pokud tuto hodnotu zkontrolovat `OnConnected` metoda obslužné rutiny událostí, v některých scénářích může být zpočátku získat hodnotu přenosu, který není konečný vyjednávaný přenosu metodu připojení. V takovém případě metoda vyvolá výjimku a zavolá se později po vytvoření finální přepravy.
- Soubory cookie.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample49.cs?highlight=1)]

    Můžete také získat soubory cookie z `Context.RequestCookies`.
- Informace o uživateli.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample50.cs?highlight=1)]
- Objektu HttpContext žádosti:

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample51.cs?highlight=1)]

    Tuto metodu použijte místo zobrazování `HttpContext.Current` zobrazíte `HttpContext` objektu pro připojení k systému SignalR.

<a id="passstate"></a>

## <a name="how-to-pass-state-between-clients-and-the-hub-class"></a>Jak předávat stavu mezi klienty a třídy rozbočovače

Poskytuje proxy serveru klienta `state` objektu, ve kterém můžete ukládat data, která chcete předávat na server se každé volání metody. Na serveru můžete přístup k těmto datům v `Clients.Caller` vlastnost v metodách rozbočovače, které jsou volány klienty. `Clients.Caller` Vlastnost není vyplněný pro metody zpracování událostí životního cyklu připojení `OnConnected`, `OnDisconnected`, a `OnReconnected`.

Vytváření nebo aktualizaci dat v `state` objektu a `Clients.Caller` vlastnost funguje v obou směrech. Hodnoty na serveru můžete aktualizovat a jsou předány zpět do klienta.

Následující příklad ukazuje klientský kód jazyka JavaScript, který ukládá stav přenosu na server se každé volání metody.

[!code-javascript[Main](signalr-1x-hubs-api-guide-server/samples/sample52.js?highlight=1-2)]

Následující příklad ukazuje odpovídající kód v .NET klienta.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample53.cs?highlight=1-2)]

Ve třídě Hub, můžete přístup k těmto datům v `Clients.Caller` vlastnost. Následující příklad ukazuje kód, který načte stav uvedené v předchozím příkladu.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample54.cs?highlight=3-4)]

> [!NOTE]
> Tento mechanismus pro zachování stavu není určena pro velké objemy dat, od všechno, co vložíte do `state` nebo `Clients.Caller` vlastnost je odbavovaná se každé volání metody. Je vhodné pro menší položky, jako jsou uživatelská jména nebo čítače.


<a id="handleErrors"></a>

## <a name="how-to-handle-errors-in-the-hub-class"></a>Zpracování chyb ve třídě centra

Zpracování chyb, ke kterým dochází ve vašich metodách rozbočovače třída, používá jedno nebo obě z následujících metod:

- Zabalit váš kód metody do bloků try-catch a protokolu objekt výjimky. Pro účely ladění může posílat výjimku do klienta, ale pro zabezpečení se nedoporučuje z důvodů odeslání podrobné informace pro klienty v produkčním prostředí.
- Vytvoření modulu kanálu rozbočovače, který zpracovává [OnIncomingError](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubpipelinemodule.onincomingerror(v=vs.111).aspx) metody. Následující příklad ukazuje modulu kanálu, který protokoluje chyby, za nímž následuje kód v souboru Global.asax, který se vloží do kanálu rozbočovače modul.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample55.cs)]

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample56.cs?highlight=3)]

Další informace o modulech kanálu rozbočovače, naleznete v tématu [přizpůsobení kanálu rozbočovače](#hubpipeline) dále v tomto tématu.

<a id="tracing"></a>

## <a name="how-to-enable-tracing"></a>Jak povolit trasování

Povolení trasování na straně serveru, přidejte System.Diagnostics – element do souboru Web.config, jak je znázorněno v tomto příkladu:

[!code-html[Main](signalr-1x-hubs-api-guide-server/samples/sample57.html?highlight=17-72)]

Když aplikaci spouštíte v sadě Visual Studio, můžete zobrazit v protokolech **výstup** okna.

<a id="callfromoutsidehub"></a>

## <a name="how-to-call-client-methods-and-manage-groups-from-outside-the-hub-class"></a>Jak volat metody klienta a spravovat skupiny mimo třídy rozbočovače

Volání metody klienta z jinou třídu než vaše třída rozbočovače, získejte odkaz na objekt kontextu SignalR pro rozbočovač a použijte ho k volání metody na straně klienta nebo spravovat skupiny.

Následující ukázka `StockTicker` třídy získá objekt context, uloží je v instanci třídy, ukládá instance třídy ve statické vlastnosti a používá kontext z instance třídy singleton, aby volala `updateStockPrice` metodu na klienty, kteří jsou připojení k rozbočovači s názvem `StockTickerHub`.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample58.cs?highlight=8,24)]

Pokud budete muset použít kontext více a časy v objektu s dlouhým poločasem rozpadu, získat odkaz na jednou a uložte spíše získání ho znovu pokaždé, když. Po získání kontextu zajistí, že SignalR odesílá zprávy do klientů ve stejném pořadí, ve kterém zkontrolujte svoje metody rozbočovače klienta volání metod. Kurz ukazuje, jak používat objekt context SignalR pro rozbočovač, najdete v tématu [serverové vysílání s knihovnou ASP.NET SignalR](index.md).

<a id="callingclientsoutsidehub"></a>

### <a name="calling-client-methods"></a>Volání metody klienta

Můžete určit, kteří klienti dostanou vzdáleného volání Procedur, ale máte k dispozici méně možností než při volání z třídy rozbočovače. Důvodem je, že kontextu není přidružen konkrétní volání z klienta, tak jakékoli metody, které vyžadují znalost aktuální ID připojení, například `Clients.Others`, nebo `Clients.Caller`, nebo `Clients.OthersInGroup`, nejsou k dispozici. Jsou k dispozici následující možnosti:

- Všichni připojení klienti.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample59.cs)]
- Konkrétního klienta, které identifikují pomocí ID připojení.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample60.css)]
- Všichni připojení klienti s výjimkou určitých klientech identifikují pomocí ID připojení.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample61.cs)]
- Všichni připojení klienti do zadané skupiny.

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample62.css)]
- Všechny připojené klienty v zadané skupině s výjimkou určitých klientech, identifikují pomocí ID připojení.

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample63.cs)]

Při volání do vaší třídy bez centra z metody ve třídě centra, můžete předat ID aktuálního připojení a použít s `Clients.Client`, `Clients.AllExcept`, nebo `Clients.Group` pro simulaci `Clients.Caller`, `Clients.Others`, nebo `Clients.OthersInGroup`. V následujícím příkladu `MoveShapeHub` třídy předá ID připojení pro `Broadcaster` třídy tak, aby `Broadcaster` třídy můžete simulovat `Clients.Others`.

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample64.cs?highlight=12,36)]

<a id="managinggroupsoutsidehub"></a>

### <a name="managing-group-membership"></a>Správa členství ve skupině

Pro správu skupin máte stejné možnosti jako třída rozbočovače.

- Přidání klienta do skupiny

    [!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample65.cs)]
- Odeberte klienta ze skupiny

    [!code-css[Main](signalr-1x-hubs-api-guide-server/samples/sample66.css)]

<a id="hubpipeline"></a>

## <a name="how-to-customize-the-hubs-pipeline"></a>Přizpůsobení kanálu rozbočovače

Funkce SignalR umožňuje vložit vlastní kód do kanálu rozbočovače. Následující příklad ukazuje vlastní modul kanálu rozbočovače, který zaznamenává každou příchozí volání metody přijatých z klienta a odchozích volání metody vyvolat na straně klienta:

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample67.cs)]

Následující kód na *Global.asax* souboru registruje modul pro spuštění v kanálu rozbočovače:

[!code-csharp[Main](signalr-1x-hubs-api-guide-server/samples/sample68.cs?highlight=3)]

Existuje celá řada různých metod, které můžete přepsat. Úplný seznam najdete v tématu [HubPipelineModule metody](https://msdn.microsoft.com/library/jj918633(v=vs.111).aspx).

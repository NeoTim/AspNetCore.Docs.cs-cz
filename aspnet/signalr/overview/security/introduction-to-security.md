---
uid: signalr/overview/security/introduction-to-security
title: Úvod do zabezpečení knihovnou SignalR | Dokumentace Microsoftu
author: pfletcher
description: Popisuje problémy se zabezpečením, které je třeba zvážit při vývoji aplikace SignalR.
ms.author: riande
ms.date: 06/10/2014
ms.assetid: ed562717-8591-4936-8e10-c7e63dcb570a
msc.legacyurl: /signalr/overview/security/introduction-to-security
msc.type: authoredcontent
ms.openlocfilehash: 4386e08d966d32abb0510be7f0393888d85258ac
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53287924"
---
<a name="introduction-to-signalr-security"></a>Úvod do zabezpečení knihovnou SignalR
====================
podle [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Tento článek popisuje problémy se zabezpečením, které je třeba zvážit při vývoji aplikace SignalR.
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


## <a name="overview"></a>Přehled

Tento dokument obsahuje následující části:

- [Koncepty zabezpečení knihovnou SignalR](#concepts)

    - [Ověřování a autorizace](#authentication)
    - [Token připojení](#connectiontoken)
    - [Při obnovování spojení některých skupin](#rejoingroup)
- [Jak SignalR brání padělání žádosti více webů](#csrf)
- [Doporučení zabezpečení knihovnou SignalR](#recommendations)

    - [Zabezpečený protokol soketu vrstvy (SSL)](#ssl)
    - [Nepoužívejte jako vhodný mechanismus zabezpečení skupiny](#groupsecurity)
    - [Bezpečně zpracování vstupu z klientů](#input)
    - [Sjednocování změnu stavu uživatele s aktivní připojení](#reconcile)
    - [Automaticky generované soubory proxy server JavaScript](#autogen)
    - [Výjimky](#exceptions)

<a id="concepts"></a>

## <a name="signalr-security-concepts"></a>Koncepty zabezpečení knihovnou SignalR

<a id="authentication"></a>

### <a name="authentication-and-authorization"></a>Ověřování a autorizace

Funkce SignalR nenabízí žádné funkce pro ověřování uživatelů. Místo toho je integrovat funkce SignalR do existující struktury ověřování pro aplikaci. Při by normálně v aplikaci a práci s výsledky ověřování ve vaší SignalR kódování, ověřování uživatelů. Například můžete ověřovat uživatele pomocí ověřování formulářů ASP.NET a pak vynutit ve vašem Centru, kteří uživatelé nebo role mají oprávnění k volání metody. V centru můžete také předat ověřovací informace, jako je například uživatelské jméno nebo určuje, zda uživatel patří do role, do klienta.

Funkce SignalR poskytuje [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) atribut k určení, kteří mají přístup do centra nebo metody. Můžete použít atribut Authorize rozbočovač nebo konkrétní metody v rozbočovači. Bez atributu Authorize všechny veřejné metody v rozbočovači jsou dostupné pro klienta, který je připojený k rozbočovači. Další informace o centrech najdete v tématu [ověřování a autorizace Center SignalR](hub-authorization.md).

Můžete použít `Authorize` atributu rozbočovače, ale ne trvalé připojení. K vynucení pravidel autorizace, při použití `PersistentConnection` je nutné přepsat `AuthorizeRequest` metody. Další informace o trvalé připojení najdete v tématu [ověřování a autorizace trvalých připojení SignalR](persistent-connection-authorization.md).

<a id="connectiontoken"></a>

### <a name="connection-token"></a>Token připojení

Funkce SignalR snižuje riziko provádění škodlivých příkazů ověřením identity odesílatele. Pro každý požadavek klienta a serveru předat token připojení, který obsahuje id připojení a uživatelské jméno pro ověřené uživatele. Id připojení jednoznačně identifikuje každý připojený klient. Server náhodně generovat id připojení, když se vytvoří nové připojení a trvá tímto identifikátorem po dobu trvání připojení. Mechanismus ověřování pro webovou aplikaci obsahuje uživatelské jméno. Funkce SignalR používá šifrování a digitálním podpisem k ochraně token připojení.

![](introduction-to-security/_static/image2.png)

Pro každý požadavek server ověřuje obsah tokenu k zajištění, že žádost pochází ze zadaného uživatele. Uživatelské jméno musí odpovídat id připojení. Ověřením id připojení a uživatelské jméno SignalR zabrání uživateli se zlými úmysly snadno zosobnění jiného uživatele. Pokud server nemůže ověřit token připojení, požadavek selže.

![](introduction-to-security/_static/image4.png)

Id připojení je součástí procesu ověřování, by neměla zobrazit jeden uživatel id připojení pro jiné uživatele nebo uložení hodnoty na straně klienta, jako například do souboru cookie.

#### <a name="connection-tokens-vs-other-token-types"></a>Připojení tokeny vs. jiné typy tokenů

Připojení tokeny jsou označeny čas od času pomocí nástroje pro zabezpečení, protože zdají být tokeny relace nebo ověřovacích tokenů, které představuje riziko, pokud je vystavené.

Token připojení SignalR není ověřovací token. Používá se pro potvrzení, že uživatel vytvořit tuto žádost je stejný jako ten, který propojení vytvořil. Token připojení je nezbytné, protože funkce SignalR technologie ASP.NET umožňuje připojení k přesunutí mezi servery. Token připojení přidruží konkrétní uživatel ale nebude vyhodnocení identitu uživatele, který zadal žádost. Pro žádost SignalR k ověření správně musí mít další token, který se vyhodnotí identitu uživatele, jako je soubor cookie nebo nosný token. Ale připojení token sám neposkytuje žádné deklarace identity, která byla žádost učiněna daným uživatelem, pouze to, že ID připojení obsažené v tokenu je propojená s tímto uživatelem.

Protože token připojení poskytuje žádná deklarace ověřování vlastní, není to považováno za "relace" nebo "ověřování" token. Pořízení tokenu připojení daného uživatele a přehráním žádost o ověření jako jiný uživatel (nebo neověřené žádosti) se nezdaří, protože nebude odpovídat identitu uživatele požadavku a identit uložených v tokenu.

<a id="rejoingroup"></a>

### <a name="rejoining-groups-when-reconnecting"></a>Při obnovování spojení některých skupin

Ve výchozím nastavení aplikace SignalR se automaticky znovu přiřadit uživatele do příslušných skupin při obnovování spojení z dočasné přerušení, jako je například pokud je připojení vyřadit a znovu vytvořit předtím, než vyprší časový limit připojení. Při obnovování spojení, klient předá skupiny token, který obsahuje id připojení a přiřazených skupin. Skupina tokenu je digitálně podepsané a šifrovaná. Klient zachová stejné id připojení po opětovné připojení; id připojení, které jsou předány z opětovného připojení klienta proto musí odpovídat id předchozího připojení použitou klientem. Toto ověření zabrání předání žádosti o připojení neoprávněné skupiny při obnovování spojení uživatel se zlými úmysly.

Je důležité si uvědomit, že skupina token, nemá prošlou platnost. Pokud uživatel patřil do skupiny v minulosti, ale byl vyloučen z této skupiny, může mít možnost tak, aby napodoboval skupiny token, který obsahuje zakázané skupiny. Pokud potřebujete k bezpečné správě, kteří uživatelé patří do skupiny, které potřebujete k ukládání těchto dat na serveru, jako například v databázi. Pak přidejte logiku pro vaše aplikace, která se ověřuje na serveru, zda uživatel patří do skupiny. Příklad ověření členství ve skupinách najdete v tématu [práce se skupinami](../guide-to-the-api/working-with-groups.md).

Automaticky některých skupin projeví pouze v případě, že připojení je znovu připojeny po dočasné přerušení. Pokud uživatel odpojí podle navigaci pryč z aplikace nebo restartování aplikace, aplikace musí zpracovat přidání tohoto uživatele do správné skupiny. Další informace najdete v tématu [práce se skupinami](../guide-to-the-api/working-with-groups.md).

<a id="csrf"></a>

## <a name="how-signalr-prevents-cross-site-request-forgery"></a>Jak SignalR brání padělání žádosti více webů

Padělání žádosti mezi weby (CSRF) je útok, kde škodlivý web odešle požadavek na zranitelné lokality, kde uživatel je momentálně přihlášený. Funkce SignalR brání CSRF tím, že škodlivý lokality za účelem vytvořte žádost platná pro vaše aplikace SignalR extrémně nepravděpodobné.

### <a name="description-of-csrf-attack"></a>Popis útoku CSRF

Tady je příklad útok CSRF:

1. Přihlášení uživatele do www.example.com, ověřování pomocí formulářů.
2. Server ověřuje uživatele. Odpověď ze serveru obsahuje soubor cookie ověřování.
3. Bez odhlášení, uživatel navštíví škodlivý web. Tento škodlivý web obsahuje následující formulář HTML:

    [!code-html[Main](introduction-to-security/samples/sample1.html)]

   Všimněte si, že akce formuláře zveřejní ohrožených site nechcete škodlivé weby. Toto je část "webů" CSRF.
4. Uživatel klikne na tlačítko Odeslat. Prohlížeč zahrnuje ověřovacího souboru cookie s požadavkem.
5. Požadavek běží na serveru example.com s kontext ověřování uživatele a dělat cokoli, ověřený uživatel smí provádět.

Přestože tento příklad vyžaduje, aby uživatel kliknout na tlačítko formuláře, stejně jako můžete snadno spouštět skript, který odesílá požadavek AJAX do vaší aplikace SignalR může škodlivé stránky. Kromě toho pomocí protokolu SSL nebrání útok CSRF, protože škodlivý web můžete poslat žádost o "https://".

Útokům CSRF jsou obvykle možné proti webové stránky, které používají soubory cookie pro ověřování, protože prohlížeče odesílají všechny relevantní soubory cookie na cílový webový server. Útokům CSRF však nejsou omezené na využívání soubory cookie. Například jsou Basic a Digest ověřování také zranitelné. Po přihlášení uživatele pomocí ověřování základní nebo Digest, prohlížeč automaticky odesílá přihlašovací údaje, dokud se relace neukončí.

### <a name="csrf-mitigations-taken-by-signalr"></a>Zmírnění CSRF provedenou SignalR

Funkce SignalR přijímá následující kroky, aby se zabránilo škodlivým webům ve vytváření platných žádostí na aplikace. SignalR je potřeba provést tyto kroky ve výchozím nastavení, není potřeba provádět žádnou akci ve vašem kódu.

- **Zakázat žádosti napříč doménami** SignalR zakáže žádosti napříč doménami uživatelům zabránit ve volání koncových bodů SignalR z externí domény. Funkce SignalR považuje za jakýkoli požadavek od externí domény není platný a blokuje žádosti. Doporučujeme zachovat toto výchozí chování v opačném případě škodlivým webům může přimět uživatele k odesílání příkazů do vaší lokality. Pokud budete muset použít napříč požadavky domény, přečtěte si téma [jak k navázání připojení mezi doménami](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain) .
- **Předat token připojení v řetězci dotazu, nikoli soubor cookie** SignalR předá token připojení jako hodnotu řetězce dotazu, ne jako soubor cookie. Ukládání token připojení do souboru cookie nebezpečné, protože v prohlížeči může nechtěně předat token připojení vyskytne škodlivý kód. Také předá připojení token v řetězci dotazu zabraňuje token připojení uložením mimo aktuální připojení. Uživatel se zlými úmysly proto nelze vytvořit žádost pod přihlašovacími údaji jiným uživatelem.
- **Ověřit token připojení** jak je popsáno v [token připojení](#connectiontoken) části server ví, u kterého id připojení souvisí s každého ověřeného uživatele. Server není zpracovat jakýkoli požadavek od id připojení, které se neshoduje s uživatelským jménem. Není pravděpodobné, že uživatel se zlými úmysly může uhodnout žádost platná, protože uživatel se zlými úmysly byste museli znát uživatelské jméno a aktuální id náhodně vygenerované připojovací. Toto id připojení se stane neplatným, jako je připojení ukončeno. Anonymní uživatelé neměli mít přístup k žádné citlivé údaje.

<a id="recommendations"></a>

## <a name="signalr-security-recommendations"></a>Doporučení zabezpečení knihovnou SignalR

<a id="ssl"></a>

### <a name="secure-socket-layers-ssl-protocol"></a>Zabezpečený protokol soketu vrstvy (SSL)

Protokol SSL používá šifrování pro zabezpečení přenosu dat mezi klientem a serverem. Pokud vaše aplikace SignalR přenáší citlivých informací mezi klientem a serverem, použijte protokol SSL pro přenos. Další informace o nastavení protokolu SSL najdete v tématu [nastavení protokolu SSL ve službě IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).

<a id="groupsecurity"></a>

### <a name="do-not-use-groups-as-a-security-mechanism"></a>Nepoužívejte jako vhodný mechanismus zabezpečení skupiny

Skupiny představují pohodlný způsob shromažďování související uživatele, ale nejsou zabezpečené mechanismus pro omezení přístupu k citlivým údajům. To platí zejména když uživatelé mohou automaticky znovu připojí skupiny během o obnovení. Místo toho zvažte přidání k roli privilegovaných uživatelů a omezení přístupu k metodě rozbočovače pouze členům této role. Příklad omezení přístupu podle rolí, najdete v části [ověřování a autorizace Center SignalR](hub-authorization.md). Příklad při obnovování spojení kontroly přístupu uživatelů ke skupinám, naleznete v tématu [práce se skupinami](../guide-to-the-api/working-with-groups.md).

<a id="input"></a>

### <a name="safely-handling-input-from-clients"></a>Bezpečně zpracování vstupu z klientů

Aby bylo zajištěno, že uživatel se zlými úmysly neodesílá skript ostatním uživatelům, musí kódovat veškerý vstup od klientů, která je určená pro vysílání na jiných klientů. Vzhledem k tomu, že vaše aplikace SignalR může mít mnoho různých typů klientů, by měl kódování zpráv na přijímající klienty spíše než serveru. Proto kódování HTML funguje pro webového klienta, ale ne pro jiné typy klientů. Například webové metodě klienta pro zobrazení zprávy chatu by bezpečně zpracovat uživatelské jméno a zpráv pomocí volání `html()` funkce.

[!code-html[Main](introduction-to-security/samples/sample2.html?highlight=3-4)]

<a id="reconcile"></a>

### <a name="reconciling-a-change-in-user-status-with-an-active-connection"></a>Sjednocování změnu stavu uživatele s aktivní připojení

Pokud se stav ověření změní existuje aktivní připojení, uživateli se zobrazí chyba s oznámením, "identitu uživatele nelze změnit během aktivního připojení SignalR." V takovém případě vaší aplikace měli znovu připojit k serveru a ujistěte se, že se koordinují id připojení a uživatelské jméno. Například pokud vaše aplikace umožňuje uživateli odhlásit, když existuje aktivní připojení, uživatelské jméno pro připojení se už shodovat s názvem, který je předán pro další požadavek. Bude chcete zastavit připojení, než se uživatel odhlásí a pak ji znovu spusťte.

Je důležité si uvědomit, že většina aplikací nebudete muset ručně zastavit a spustit připojení. Pokud vaše aplikace přesměruje uživatele na samostatné stránce po přihlášení, jako je výchozí chování v aplikaci webových formulářů nebo aplikace MVC nebo aktualizuje aktuální stránku po odhlášení, aktivní připojení se automaticky odpojí a ne vyžadovat žádnou další akci.

Následující příklad ukazuje, jak zastavit a spustit připojení při změně stavu uživatele.

[!code-html[Main](introduction-to-security/samples/sample3.html)]

Nebo, pokud vaše lokalita používá klouzavé vypršení platnosti se ověřování pomocí formulářů a neexistuje žádná aktivita zachovat platný ověřovací soubor cookie se může změnit stav ověření daného uživatele. V takovém případě uživatel se odhlásíte a uživatelské jméno se již nebude odpovídat uživatelské jméno v tokenu připojení. Tento problém můžete vyřešit tak, že přidáte některé skript, který pravidelně požádá o prostředek na webový server a zachovat platný ověřovací soubor cookie. Následující příklad ukazuje, jak vyžádat prostředek každých 30 minut.

[!code-javascript[Main](introduction-to-security/samples/sample4.js)]

<a id="autogen"></a>

### <a name="automatically-generated-javascript-proxy-files"></a>Automaticky generované soubory proxy server JavaScript

Pokud nechcete zahrnout všechny rozbočovače a metody do souboru proxy JavaScript pro každého uživatele, můžete zakázat automatické vygenerování souboru. Tuto možnost můžete zvolit, pokud máte více rozbočovače a metody, ale nechcete, aby každý uživatel, je potřeba vědět všechny metody. Zakázat automatické generování nastavením **EnableJavaScriptProxies** k **false**.

[!code-csharp[Main](introduction-to-security/samples/sample5.cs)]

Další informace o proxy soubory jazyka JavaScript naleznete v tématu [vygenerovaný proxy server a co to dělá za vás](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy). <a id="exceptions"></a>

### <a name="exceptions"></a>Výjimky

Vyhněte se předávání objektů výjimky pro klienty, protože objekty může zveřejnit citlivé informace pro klienty. Místo toho volejte metodu na straně klienta, který se zobrazí odpovídající chybová zpráva.

[!code-csharp[Main](introduction-to-security/samples/sample6.cs)]

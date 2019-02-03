---
uid: web-api/overview/testing-and-debugging/troubleshooting-http-405-errors-after-publishing-web-api-applications
title: Řešení potíží s HTTP 405 chyby po publikování webových aplikací API | Dokumentace Microsoftu
author: rmcmurray
description: Tento kurz popisuje, jak řešit chyby protokolu HTTP 405 po publikování aplikace webového rozhraní API pro produkční webový server.
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 07ec7d37-023f-43ea-b471-60b08ce338f7
msc.legacyurl: /web-api/overview/testing-and-debugging/troubleshooting-http-405-errors-after-publishing-web-api-applications
msc.type: authoredcontent
ms.openlocfilehash: ce5b617cc1032d190cc2450aa554b462ea6f6156
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667411"
---
# <a name="troubleshooting-http-405-errors-after-publishing-web-api-applications"></a>Řešení potíží s chybami HTTP 405 po publikování aplikace webového rozhraní API

> Tento kurz popisuje, jak řešit chyby protokolu HTTP 405 po publikování aplikace webového rozhraní API pro produkční webový server.
> 
> ## <a name="software-used-in-this-tutorial"></a>V tomto kurzu používá software
> 
> 
> - [Internetové informační služby (IIS)](https://www.iis.net/) (verze 7 nebo novější)
> - [Webové rozhraní API](../../index.md) 


Webové rozhraní API aplikace obvykle používají několika běžných příkazů HTTP: GET, POST, PUT, DELETE a někdy oprava. Který říká, vývojáři mohou spustit v situacích, kde tyto akce jsou implementované jiný modul služby IIS na provozním serveru, což vede k situaci, ve kterém kontroler Web API, která funguje správně v sadě Visual Studio nebo na vývojovém serveru vrátí HTTP 405 při nasazení do provozního serveru došlo k chybě. Naštěstí je snadno vyřešit tento problém, ale řešení zaručuje vysvětlení, proč dochází k problému.

## <a name="what-causes-http-405-errors"></a>Co způsobí, že chyby protokolu HTTP 405

Prvním krokem k tom, jak chyby protokolu HTTP 405 pro řešení problémů je pochopit, co se chybu HTTP 405 ve skutečnosti znamená. Primární řídící dokumentu pro HTTP je [dokumentu RFC 2616](http://www.ietf.org/rfc/rfc2616.txt), která definuje stavový kód HTTP 405 jako ***metoda není povolena***a dále popisuje tímto stavovým kódem jako stav kde &quot;metodu zadané v poli řádek požadavku není povolená pro prostředku označeném identifikátorem URI požadavku.&quot; Jinými slovy příkaz HTTP není povolena pro konkrétní adresu URL, který požádal klienta HTTP.

Jako stručný přehled tady jsou některé z nejčastěji používaných metody HTTP jak jsou definovány v dokumentu RFC 2616 RFC 4918 a RFC 5789:

| Metoda HTTP | Popis |
| --- | --- |
| **GET** | Tato metoda se používá k načtení dat z identifikátoru URI a pravděpodobně nejčastěji používaných metoda protokolu HTTP. |
| **HLAVNÍ** | Tato metoda je stejně jako v případě metody GET s tím rozdílem, že ve skutečnosti nelze načíst z identifikátoru URI požadavku – jednoduše načte stav protokolu HTTP. |
| **POST** | Tato metoda se obvykle používá k odeslání nových dat na identifikátor URI; PŘÍSPĚVEK se často používá k odeslání dat formuláře. |
| **PUT** | Tato metoda se obvykle používá k odesílání nezpracovaná data na identifikátor URI; PUT se často používá k odesílání dat JSON nebo XML do aplikace webového rozhraní API. |
| **DELETE** | Tato metoda se používá k odebrání dat z identifikátoru URI. |
| **MOŽNOSTI** | Tato metoda se obvykle používá k získání seznamu metod HTTP, které jsou podporovány pro identifikátor URI. |
| **KOPÍROVÁNÍ PŘESUNUTÍ** | Tyto dvě metody se používají s WebDAV a jejich účelem je zřejmých. |
| **MKCOL** | Tato metoda se používá s WebDAV a používá se k vytvoření kolekce (např. adresář) se zadaným identifikátorem URI. |
| **PROPFIND PROPPATCH** | Tyto dvě metody se používají s WebDAV a používají se k dotazování nebo nastavení vlastností pro identifikátor URI. |
| **ODEMKNOUT UZAMČENÍ** | Tyto dvě metody se používají s WebDAV a používají se k prostředku označeném identifikátorem URI požadavku při vytváření Zamknout/odemknout. |
| **PATCH** | Tato metoda se používá k úpravě existující prostředek HTTP. |

Při jedné z těchto metod HTTP je nakonfigurován pro použití na serveru, server bude reagovat se stavem HTTP a další data, která je vhodná pro daný požadavek. (Například metoda GET může dojít HTTP 200 ***OK*** odpovědi a metodu PUT HTTP 201 dojít ***vytvořeno*** odpověď.)

Pokud metoda HTTP není nakonfigurován pro použití na serveru, server odpoví HTTP 501 ***Neimplementováno*** chyby.

Ale při lze metodu HTTP je nakonfigurován pro použití na serveru, ale je zakázaná pro daný identifikátor URI, server bude reagovat pomocí protokolu HTTP 405 ***metoda není povolena*** chyby.

## <a name="example-http-405-error"></a>Chyba protokolu HTTP 405 příklad

Následující příklad HTTP žádosti a odpovědi ukazuje situaci, kdy při pokusu o VLOŽTE hodnotu do webového rozhraní API aplikace na webovém serveru je v klientovi HTTP, a server vrátí chybu HTTP, které stavy, které metoda PUT není povolený:


Požadavek protokolu HTTP:


[!code-console[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample1.cmd)]


Odpověď HTTP:


[!code-console[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample2.cmd)]


V tomto příkladu klienta HTTP poslali žádost o platný JSON na adresu URL pro aplikaci webového rozhraní API na webový server, ale server vrátil HTTP 405 chybovou zprávu, která označuje, že metodu PUT nebyla povolena pro adresu URL. Pokud identifikátor URI požadavku neodpovídá trasu pro aplikace webového rozhraní API, naproti tomu by server vrátit chybu HTTP 404 ***nebyl nalezen*** chyby.

## <a name="resolve-http-405-errors"></a>Řešení chyb HTTP 405

Tady je několik důvodů, proč nemusí být povolený konkrétní příkaz HTTP, ale neexistuje jeden primární scénář, který je přední příčinou této chyby ve službě IIS: více obslužných rutin, které jsou definovány pro stejný příkaz/metodu a jedna z obslužných rutin blokuje očekávané obslužnou rutinu z zpracování žádosti. Služba IIS zpracovává prostřednictvím vysvětlení, obslužné rutiny z prvního na posledního podle pořadí položek obslužné rutiny v applicationHost.config a souboru web.config souborů, kde se budou používat odpovídající kombinace cestu, příkaz, zdroje atd., žádost zpracovat.

Následující příklad je výpisem ze souboru applicationHost.config pro server služby IIS, který vrátil chybu HTTP 405 při použití metody PUT odesílání dat do aplikace webového rozhraní API. V této výňatek několik obslužných rutin HTTP jsou definovány a Každá obslužná rutina mají různé sady metod HTTP, pro které je nakonfigurované – poslední položku v seznamu je statický obsah obslužnou rutinu, což je výchozí popisovač, který se používá po obslužné rutiny jste využili chanc elektronické prozkoumat žádosti:

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample3.xml)]

V předchozím příkladu je obslužná rutina protokolu WebDAV a obslužná rutina adresy URL bez přípony pro technologii ASP.NET (který se používá pro webové rozhraní API) jasně definované pro samostatné seznamy metod HTTP. Všimněte si, že obslužnou rutinu ISAPI DLL je nakonfigurován pro všechny metody HTTP, i když tato konfigurace nutně nezpůsobí chybu. Však nastavení konfigurace, jako je tato potřeba vzít v úvahu při řešení potíží s chybami HTTP 405.

V příkladu výše obslužnou rutinu ISAPI DLL nebyl problém; ve skutečnosti problém není definovaný v souboru applicationHost.config pro server služby IIS – tento problém byl způsobený položka, která byla vytvořena v souboru web.config, při vytváření aplikace webového rozhraní API v sadě Visual Studio. Následující úryvek ze souboru web.config aplikace ukazuje umístění problému:

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample4.xml)]

V této výňatek předefinovalo se mají zahrnout další metody HTTP, které se použijí v aplikaci webového rozhraní API obslužná rutina adresy URL bez přípony pro technologii ASP.NET. Ale protože podobnou sadu metod HTTP je definovaný pro obslužnou rutinu protokolu WebDAV, dojde ke konfliktu. V tomto konkrétním případě je obslužná rutina protokolu WebDAV definované a načíst službou IIS, i když protokol WebDAV je zakázaná pro web, který zahrnuje aplikaci webového rozhraní API. Během zpracování požadavek HTTP PUT, služba IIS provede volání modulu protokolu WebDAV vzhledem k tomu, že je definována pro operaci PUT. Při volání modulu protokolu WebDAV, ověří jeho konfigurace a vidí, že je zakázaná, takže vrátí HTTP 405 ***metoda není povolena*** všechny požadavky, které se podobá žádost o protokol WebDAV v podrobnostech o chybě. Chcete-li vyřešit tento problém, byste měli odebrat ze seznamu modulů HTTP pro web, kde je definován aplikace webového rozhraní API protokolu WebDAV. Následující příklad ukazuje, co, může vypadat:

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample5.xml)]

Tento scénář se často vyskytují po publikování aplikace z vývojového prostředí do produkčního prostředí a k tomu dochází, je seznam obslužných rutin nebo modulů rozdíly mezi vývojovou a provozní prostředí. Pokud například používáte sadu Visual Studio 2012 nebo novějším k vývoji aplikace webového rozhraní API, služby IIS Express, je výchozí webový server pro účely testování. Tato vývojovému webovému serveru je verze škálovat dolů úplné funkce služby IIS, která se dodává v serveru produktu a tento vývojovému webovému serveru obsahuje několik změn, které byly přidány pro scénáře vývoje. Například protokol WebDAV je často nainstalován modul na produkčním webovém serveru, na kterém běží úplná verze služby IIS, i když nemusí být v praxi. Verzi služby IIS (služba IIS Express), nainstaluje modul protokolu WebDAV, ale položky pro modul protokolu WebDAV jsou záměrně označené jako komentář, takže modulu protokolu WebDAV je vůbec nenačetl ve službě IIS Express, pokud je konkrétně změnit konfiguraci služby IIS Express nastavení přidat funkci protokolu WebDAV na instalaci služby IIS Express. V důsledku toho může webová aplikace ve svém vývojovém počítači fungovat správně, ale při publikování aplikace webového rozhraní API pro produkční webový server pravděpodobně dojde k chybám protokolu HTTP 405.

## <a name="summary"></a>Souhrn

HTTP 405 jsou chyby způsobeny není pro požadovanou adresu URL ve webovém serveru povoleno lze metodu HTTP. Tento stav je často zobrazí, pokud konkrétní obslužná rutina byla definována pro určité operace a tato obslužná rutina přepisuje obslužné rutiny, které očekáváte, že pro zpracování žádosti.

Pokud narazíte na situace, kdy se zobrazí chybová zpráva HTTP 501, což znamená, že konkrétní funkce ještě nebyla implementována na serveru, to často znamená, že neexistuje žádná obslužná rutina definované v nastavení služby IIS, které odpovídá požadavku HTTP, který pravděpodobně znamená, že něco nebyl správně nainstalován ve vašem systému, nebo něco změnil nastavení služby IIS tak, aby nejsou že definované žádné obslužné rutiny metody podpory konkrétní HTTP. Chcete-li tento problém vyřešit, musíte znovu nainstalovat všechny aplikace, které se pokouší použít lze metodu HTTP, pro kterou nemá žádný odpovídající modul nebo obslužná rutina definice.

---
title: Informace o zabezpečení ve funkci SignalR technologie ASP.NET Core
author: tdykstra
description: Další informace o použití ověřování a autorizace v knihovně SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 10/17/2018
uid: signalr/security
ms.openlocfilehash: be1dd24c40327d9a0d8f91bf75300128d3d52725
ms.sourcegitcommit: fc7eb4243188950ae1f1b52669edc007e9d0798d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51225366"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a>Informace o zabezpečení ve funkci SignalR technologie ASP.NET Core

Podle [Andrew Stanton sestry](https://twitter.com/anurse)

Tento článek obsahuje informace o zabezpečení knihovnou SignalR.

## <a name="cross-origin-resource-sharing"></a>Sdílení prostředků různého původu

[Prostředků mezi zdroji (CORS) pro sdílení obsahu](https://www.w3.org/TR/cors/) slouží k povolení připojení SignalR nepůvodního zdroje v prohlížeči. Pokud kód jazyka JavaScript je hostované v jiné doméně aplikace SignalR [middlewarem CORS](xref:security/cors) musí lze povolit JavaScript, který chcete připojit k aplikaci SignalR. Povolení žádostí nepůvodního pouze z domén, které důvěřujete nebo ovládacího prvku. Příklad:

* Je hostitelem vašeho webu `http://www.example.com`
* Je hostitelem vaší aplikace SignalR `http://signalr.example.com`

CORS by měl být nakonfigurovaný v SignalR aplikaci a Povolit jenom původ `www.example.com`.

Další informace o konfiguraci CORS, najdete v části [povolení prostředků různého původů (CORS)](xref:security/cors). Funkce SignalR **vyžaduje** následující zásady CORS:

* Povolte konkrétní očekávané zdroje. Povolení jakýkoli původ je možné, ale je **není** zabezpečení nebo doporučené.
* Metody HTTP `GET` a `POST` musí být povoleno.
* Přihlašovací údaje musí být povolena, i když se ověřování nepoužívá.

Například následující zásadu CORS umožňuje klientovi SignalR prohlížeče hostované na `http://example.com` přístup k aplikaci SignalR hostitelem `http://signalr.example.com`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

> [!NOTE]
> SignalR je nekompatibilní s integrovaná funkce CORS v Azure App Service.

## <a name="websocket-origin-restriction"></a>Omezení objektu websocket na straně zdroje

::: moniker range=">= aspnetcore-2.2"

Poskytovanou CORS se nevztahují na objekty Websocket. Původní omezení na protokoly Websocket, najdete v článku [objekty Websocket původu omezení](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Poskytovanou CORS se nevztahují na objekty Websocket. Prohlížeče **není**:

* Provedení přípravné požadavků CORS.
* Respektujeme zadaná v omezení `Access-Control` záhlaví při provádění požadavků protokolu WebSocket.

Ale prohlížeče odesílají `Origin` záhlaví při vydávání žádostí protokolu WebSocket. Aplikace musí být nakonfigurovaný k ověření tyto hlavičky k zajištění, že jsou povoleny pouze objekty Websocket očekávané původ, odkud pocházejí.

V ASP.NET Core 2.1 nebo novější, hlavičky ověření lze dosáhnout pomocí vlastního middlewaru umístit **před `UseSignalR`a ověřovací middleware** v `Configure`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> `Origin` Záhlaví je řízena klientem a stejně jako `Referer` záhlaví, můžete zfalšovaná. By měla tato záhlaví **není** používat jako mechanismus ověřování.

::: moniker-end

## <a name="access-token-logging"></a>Protokolování token přístupu

Při použití Server-Sent události nebo protokoly Websocket, klientský prohlížeč odesílá přístupový token v řetězci dotazu. Přijetí přístupový token pomocí řetězce dotazu, je obecně stejně bezpečné jako použití standardní `Authorization` záhlaví. Však mnohé webové servery protokolu adresu URL pro každý požadavek, včetně řetězec dotazu. Protokolování adresy URL může protokolovat přístupový token. Osvědčeným postupem je nastavení webového serveru protokolování zabránit protokolování přístupové tokeny.

## <a name="exceptions"></a>Výjimky

Zprávy o výjimkách jsou obvykle považovány za citlivá data, která by neměla být odhalena do klienta. Ve výchozím nastavení nebude funkce SignalR Odeslat podrobnosti výjimky vyvolané metodou rozbočovače klientovi. Místo toho klient obdrží obecná zpráva oznamující, že došlo k chybě. Výjimka doručení zpráv do klienta monitorconfigurationoverride lze přepsat (např. vývojové nebo testovací) [ `EnableDetailedErrors` ](xref:signalr/configuration#configure-server-options). Zprávy o výjimkách by neměly být vystaveny klientům v produkčních aplikacích.

## <a name="buffer-management"></a>Správa vyrovnávací paměti

SignalR na připojení vyrovnávací paměti používá ke správě příchozí a odchozí zprávy. Ve výchozím nastavení omezuje SignalR vyrovnávací paměti na 32 KB. Největší zprávu, kterou můžete poslat klient nebo server je 32 KB. Maximální velikost paměti spotřebované na připojení pro zprávy je 32 KB. Pokud vaše zprávy jsou vždy menší než 32 KB, můžete zkrátit limit, který:

* Brání klientovi tomu nebudou moct odeslat zprávu větší.
* Server nikdy muset přidělit velké vyrovnávací paměti pro příjem zpráv.

Pokud vaše zprávy jsou větší než 32 KB, můžete zvýšit limit. Zvýšit tento limit znamená, že:

* Klient může způsobit server k přidělení vyrovnávací paměti velké paměti.
* Server přidělení velké vyrovnávací paměti může snížit počet souběžných připojení.

Existují omezení pro příchozí a odchozí zprávy, jak lze nakonfigurovat podle [ `HttpConnectionDispatcherOptions` ](xref:signalr/configuration#configure-server-options) objekt gurovaný `MapHub`:

* `ApplicationMaxBufferSize` představuje maximální počet bajtů z klienta, který vyrovnávací paměti serveru. Pokud se klient pokusí odeslat zprávu větší než tento limit, může připojení ukončeno.
* `TransportMaxBufferSize` představuje maximální počet bajtů, které může server odeslat. Pokud se server pokusí odeslat zprávu (včetně návratové hodnoty metod rozbočovače na) větší než tento limit, bude vyvolána výjimka.

Nastavení limitu `0` zakáže limit. Odebrání limitu umožňuje klientovi umožní odeslat zprávu libovolné velikosti. Klientů zasílání velkých zpráv může způsobit nadbytek paměti mají být přiděleny. Využití nadbytek paměti může výrazně snížit počet souběžných připojení.

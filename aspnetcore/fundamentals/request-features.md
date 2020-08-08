---
title: Žádosti o funkce v ASP.NET Core
author: ardalis
description: Přečtěte si o podrobnostech implementace webového serveru souvisejících s požadavky HTTP a odpověďmi, které jsou definované v rozhraních pro ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/request-features
ms.openlocfilehash: 4b6b34724bc848578293e586da12ead63c96cfbd
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016788"
---
# <a name="request-features-in-aspnet-core"></a>Žádosti o funkce v ASP.NET Core

[Steve Smith](https://ardalis.com/)

Podrobnosti implementace webového serveru související s požadavky HTTP a odpověďmi jsou definovány v rozhraních. Tato rozhraní používá implementace serveru a middleware k vytváření a úpravám kanálu hostování aplikace.

## <a name="feature-interfaces"></a>Rozhraní funkcí

ASP.NET Core definuje počet rozhraní funkcí HTTP, ve `Microsoft.AspNetCore.Http.Features` kterých servery používají k identifikaci funkcí, které podporují. Následující rozhraní funkcí řídí požadavky a návratové odpovědi:

`IHttpRequestFeature`Definuje strukturu požadavku HTTP, včetně protokolu, cesty, řetězce dotazu, záhlaví a textu.

`IHttpResponseFeature`Definuje strukturu odpovědi HTTP, včetně stavového kódu, hlaviček a textu odpovědi.

`IHttpAuthenticationFeature`Definuje podporu pro identifikaci uživatelů na základě `ClaimsPrincipal` a určení obslužné rutiny ověřování.

`IHttpUpgradeFeature`Definuje podporu pro [upgrady http](https://tools.ietf.org/html/rfc2616.html#section-14.42), které klientovi umožňují určit, které další protokoly chcete použít, pokud si server přeje přepínat protokoly.

`IHttpBufferingFeature`Definuje metody pro zákaz ukládání požadavků a odpovědí do vyrovnávací paměti.

`IHttpConnectionFeature`Definuje vlastnosti pro místní a vzdálené adresy a porty.

`IHttpRequestLifetimeFeature`Definuje podporu pro přerušení připojení nebo zjištění, zda byl požadavek ukončen předčasně, například odpojením klienta.

`IHttpSendFileFeature`Definuje metodu pro asynchronní posílání souborů.

`IHttpWebSocketFeature`Definuje rozhraní API pro podpůrné webové sokety.

`IHttpRequestIdentifierFeature`Přidá vlastnost, která může být implementována k jednoznačné identifikaci požadavků.

`ISessionFeature`Definuje `ISessionFactory` a `ISession` abstrakce pro podporu uživatelských relací.

`ITlsConnectionFeature`Definuje rozhraní API pro načítání klientských certifikátů.

`ITlsTokenBindingFeature`Definuje metody pro práci s parametry vazby tokenu TLS.

> [!NOTE]
> `ISessionFeature`není funkcí serveru, ale je implementovaná v `SessionMiddleware` (viz [Správa stavu aplikace](app-state.md)).

## <a name="feature-collections"></a>Kolekce funkcí

`Features`Vlastnost `HttpContext` poskytuje rozhraní pro získání a nastavení dostupných funkcí protokolu HTTP pro aktuální požadavek. Vzhledem k tomu, že je kolekce funkcí proměnlivá i v rámci požadavku, je možné použít middleware pro úpravu kolekce a přidání podpory pro další funkce.

## <a name="middleware-and-request-features"></a>Middleware a žádosti o funkce

I když jsou servery zodpovědné za vytvoření kolekce funkcí, může middleware přidat do této kolekce a využívat funkce z kolekce. Například přistupuje k této `StaticFileMiddleware` `IHttpSendFileFeature` funkci. Pokud tato funkce existuje, používá se k odeslání požadovaného statického souboru z jeho fyzické cesty. V opačném případě se k odeslání souboru použije pomalejší alternativní metoda. Pokud je k dispozici, `IHttpSendFileFeature` umožňuje operačnímu systému otevřít soubor a provést přímou kopii režimu jádra na síťové kartě.

Middleware může navíc přidat do kolekce funkcí zřízené serverem. Existující funkce můžou být i nahrazovány middlewarem a umožňují middlewaru rozšířit funkčnost serveru. Funkce přidané do kolekce jsou hned k dispozici pro další middleware nebo podkladové aplikace přímo v kanálu požadavků.

Kombinací vlastních implementací serveru a konkrétních vylepšení middlewaru může být vytvořena přesně sada funkcí, které aplikace vyžaduje. To umožňuje, aby chybějící funkce byly přidány bez nutnosti změny v serveru a zajistila zpřístupnění pouze minimálního množství funkcí, čímž se omezuje prostor pro útoky a zvýšil se výkon.

## <a name="summary"></a>Souhrn

Rozhraní funkcí definují konkrétní funkce protokolu HTTP, které může daný požadavek podporovat. Servery definují kolekce funkcí a počáteční sadu funkcí, které tento server podporuje, ale middleware je možné využít ke zlepšení těchto funkcí.

## <a name="additional-resources"></a>Další zdroje

* [Servery](xref:fundamentals/servers/index)
* [Middleware](xref:fundamentals/middleware/index)
* [Otevřené webové rozhraní pro .NET (OWIN)](xref:fundamentals/owin)

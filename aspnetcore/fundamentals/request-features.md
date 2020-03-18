---
title: Žádosti o funkce v ASP.NET Core
author: ardalis
description: Přečtěte si o podrobnostech implementace webového serveru souvisejících s požadavky HTTP a odpověďmi, které jsou definované v rozhraních pro ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2020
ms.locfileid: "79416224"
---
# <a name="request-features-in-aspnet-core"></a>Žádosti o funkce v ASP.NET Core

[Steve Smith](https://ardalis.com/)

Implementační detaily webového serveru související s HTTP požadavky a odpověďmi jsou definovány v rozhraních. Tato rozhraní používá implementace serveru a middleware k vytváření a úpravám kanálu hostování aplikace.

## <a name="feature-interfaces"></a>Rozhraní funkcí

ASP.NET Core definuje počet rozhraní funkcí HTTP v `Microsoft.AspNetCore.Http.Features` používaných servery k identifikaci funkcí, které podporují. Následující rozhraní funkcí řídí požadavky a návratové odpovědi:

`IHttpRequestFeature` definuje strukturu požadavku HTTP, včetně protokolu, cesty, řetězce dotazu, záhlaví a textu.

`IHttpResponseFeature` definuje strukturu odpovědi HTTP, včetně stavového kódu, hlaviček a textu odpovědi.

`IHttpAuthenticationFeature` definuje podporu pro identifikaci uživatelů na základě `ClaimsPrincipal` a určení obslužné rutiny ověřování.

`IHttpUpgradeFeature` definuje podporu pro [upgrady http](https://tools.ietf.org/html/rfc2616.html#section-14.42). díky tomu může klient určit, které další protokoly chcete použít, pokud si server přeje přepínat protokoly.

`IHttpBufferingFeature` definuje metody pro zákaz ukládání požadavků do vyrovnávací paměti nebo odpovědí.

`IHttpConnectionFeature` definuje vlastnosti pro místní a vzdálené adresy a porty.

`IHttpRequestLifetimeFeature` definuje podporu pro přerušení připojení nebo zjištění, zda byla žádost ukončena předčasně, například odpojením klienta.

`IHttpSendFileFeature` definuje metodu pro asynchronní posílání souborů.

`IHttpWebSocketFeature` definuje rozhraní API pro podpůrné webové sokety.

`IHttpRequestIdentifierFeature` přidá vlastnost, která může být implementována k jednoznačné identifikaci požadavků.

`ISessionFeature` definuje `ISessionFactory` a `ISession` abstrakce pro podporu uživatelských relací.

`ITlsConnectionFeature` definuje rozhraní API pro načítání klientských certifikátů.

`ITlsTokenBindingFeature` definuje metody pro práci s parametry vazby tokenu TLS.

> [!NOTE]
> `ISessionFeature` není funkcí serveru, ale je implementována `SessionMiddleware` (viz [Správa stavu aplikace](app-state.md)).

## <a name="feature-collections"></a>Kolekce funkcí

Vlastnost `Features` `HttpContext` poskytuje rozhraní pro získání a nastavení dostupných funkcí protokolu HTTP pro aktuální požadavek. Vzhledem k tomu, že je kolekce funkcí proměnlivá i v rámci požadavku, je možné použít middleware pro úpravu kolekce a přidání podpory pro další funkce.

## <a name="middleware-and-request-features"></a>Middleware a žádosti o funkce

I když jsou servery zodpovědné za vytvoření kolekce funkcí, může middleware přidat do této kolekce a využívat funkce z kolekce. Například `StaticFileMiddleware` přistupuje k funkci `IHttpSendFileFeature`. Pokud tato funkce existuje, používá se k odeslání požadovaného statického souboru z jeho fyzické cesty. V opačném případě se k odeslání souboru použije pomalejší alternativní metoda. Pokud je k dispozici, `IHttpSendFileFeature` umožňuje operačnímu systému otevřít soubor a provést přímou kopii režimu jádra na síťové kartě.

Middleware může navíc přidat do kolekce funkcí zřízené serverem. Existující funkce můžou být i nahrazovány middlewarem a umožňují middlewaru rozšířit funkčnost serveru. Funkce přidané do kolekce jsou hned k dispozici pro další middleware nebo podkladové aplikace přímo v kanálu požadavků.

Kombinací vlastních implementací serveru a konkrétních vylepšení middlewaru může být vytvořena přesně sada funkcí, které aplikace vyžaduje. To umožňuje, aby chybějící funkce byly přidány bez nutnosti změny v serveru a zajistila zpřístupnění pouze minimálního množství funkcí, čímž se omezuje prostor pro útoky a zvýšil se výkon.

## <a name="summary"></a>Souhrn

Rozhraní funkcí definují konkrétní funkce protokolu HTTP, které může daný požadavek podporovat. Servery definují kolekce funkcí a počáteční sadu funkcí, které tento server podporuje, ale middleware je možné využít ke zlepšení těchto funkcí.

## <a name="additional-resources"></a>Další zdroje informací:

* [Servery](xref:fundamentals/servers/index)
* [Middleware](xref:fundamentals/middleware/index)
* [Otevřené webové rozhraní pro .NET (OWIN)](xref:fundamentals/owin)

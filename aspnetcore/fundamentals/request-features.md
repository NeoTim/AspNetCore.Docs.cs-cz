---
title: Funkce požadavku v ASP.NET jádra
author: ardalis
description: Informace o podrobnostech implementace webového serveru souvisejících s požadavky HTTP a odpověďmi, které jsou definovány v rozhraních pro ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416224"
---
# <a name="request-features-in-aspnet-core"></a>Funkce požadavku v ASP.NET jádra

Podle [Steve Smith](https://ardalis.com/)

Podrobnosti implementace webového serveru související s požadavky HTTP a odpověďmi jsou definovány v rozhraních. Tato rozhraní jsou používány implementace serveru a middleware k vytvoření a úpravě hostitelského kanálu aplikace.

## <a name="feature-interfaces"></a>Rozhraní funkcí

ASP.NET Core definuje řadu rozhraní funkcí `Microsoft.AspNetCore.Http.Features` PROTOKOLU HTTP, ve kterých servery používají k identifikaci funkcí, které podporují. Následující rozhraní funkcí zpracovávají požadavky a vracejí odpovědi:

`IHttpRequestFeature`Definuje strukturu požadavku HTTP, včetně protokolu, cesty, řetězce dotazu, záhlaví a těla.

`IHttpResponseFeature`Definuje strukturu odpovědi HTTP, včetně stavového kódu, záhlaví a těla odpovědi.

`IHttpAuthenticationFeature`Definuje podporu pro identifikaci uživatelů `ClaimsPrincipal` na základě a zadání obslužné rutiny ověřování.

`IHttpUpgradeFeature`Definuje podporu [pro upgrady protokolu HTTP](https://tools.ietf.org/html/rfc2616.html#section-14.42), které umožňují klientovi určit, které další protokoly chce použít, pokud chce server přepínat protokoly.

`IHttpBufferingFeature`Definuje metody pro zakázání ukládání požadavků nebo odpovědí do vyrovnávací paměti.

`IHttpConnectionFeature`Definuje vlastnosti pro místní a vzdálené adresy a porty.

`IHttpRequestLifetimeFeature`Definuje podporu pro přerušení připojení nebo zjištění, zda byl požadavek předčasně ukončen, například odpojením klienta.

`IHttpSendFileFeature`Definuje metodu pro asynchronní odesílání souborů.

`IHttpWebSocketFeature`Definuje rozhraní API pro podporu webových soketů.

`IHttpRequestIdentifierFeature`Přidá vlastnost, která může být implementována k jednoznačné identifikaci požadavků.

`ISessionFeature`Definuje `ISessionFactory` a `ISession` abstrakce pro podporu uživatelských relací.

`ITlsConnectionFeature`Definuje rozhraní API pro načítání klientských certifikátů.

`ITlsTokenBindingFeature`Definuje metody pro práci s parametry vazby tokenu TLS.

> [!NOTE]
> `ISessionFeature`není funkce serveru, ale je implementována `SessionMiddleware` (viz Správa stavu [aplikace](app-state.md)).

## <a name="feature-collections"></a>Kolekce funkcí

Vlastnost `Features` `HttpContext` poskytuje rozhraní pro získání a nastavení dostupných funkcí PROTOKOLU HTTP pro aktuální požadavek. Vzhledem k tomu, že kolekce funkcí je proměnlivá i v rámci požadavku, middlewar lze použít k úpravě kolekce a přidání podpory pro další funkce.

## <a name="middleware-and-request-features"></a>Middleware a funkce požadavků

Zatímco servery jsou zodpovědné za vytváření kolekce funkcí, middleware můžete přidat do této kolekce a využívat funkce z kolekce. Například přistupuje `StaticFileMiddleware` k `IHttpSendFileFeature` funkci. Pokud funkce existuje, slouží k odeslání požadovaného statického souboru z jeho fyzické cesty. V opačném případě se k odeslání souboru použije pomalejší alternativní metoda. Pokud `IHttpSendFileFeature` je k dispozici, umožňuje operačnímu systému otevřít soubor a provést přímou kopii režimu jádra na síťovou kartu.

Kromě toho middleware můžete přidat do kolekce funkcí vytvořených serverem. Stávající funkce mohou být dokonce nahrazeny middleware, což middleware rozšířit funkčnost serveru. Funkce přidané do kolekce jsou okamžitě k dispozici pro jiné middleware nebo základní aplikace sama později v kanálu požadavku.

Kombinací vlastních serverových implementací a specifických vylepšení middlewaru lze vytvořit přesnou sadu funkcí, které aplikace vyžaduje. To umožňuje přidat chybějící funkce bez nutnosti změny na serveru a zajišťuje pouze minimální množství funkcí, které jsou vystaveny, čímž se omezí plocha útoku a zlepší výkon.

## <a name="summary"></a>Souhrn

Rozhraní funkcí definují specifické funkce protokolu HTTP, které může daný požadavek podporovat. Servery definují kolekce funkcí a počáteční sadu funkcí podporovaných tímto serverem, ale middleware lze použít k vylepšení těchto funkcí.

## <a name="additional-resources"></a>Další zdroje

* [Servery](xref:fundamentals/servers/index)
* [Middleware](xref:fundamentals/middleware/index)
* [Otevřené webové rozhraní pro .NET (OWIN)](xref:fundamentals/owin)

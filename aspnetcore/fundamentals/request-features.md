---
title: Požadavky na funkce v ASP.NET Core
author: ardalis
description: Další informace o podrobnosti implementace web server týkající se požadavků HTTP a odpovědí, které jsou definovány v rozhraní ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087023"
---
# <a name="request-features-in-aspnet-core"></a>Požadavky na funkce v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/)

Implementační detaily webového serveru související s HTTP požadavky a odpověďmi jsou definovány v rozhraních. Tato rozhraní jsou používány implementací serveru a middleware pro vytvoření a úprava kanálu hostování vaší aplikace.

## <a name="feature-interfaces"></a>Funkce rozhraní

Definuje počet rozhraní funkce protokolu HTTP v ASP.NET Core `Microsoft.AspNetCore.Http.Features` servery které se používají k identifikaci funkce podporují. Následující funkce rozhraní zpracování požadavků a vrácení odpovědi:

`IHttpRequestFeature` Definuje strukturu požadavek HTTP, včetně protokolu, cesta, řetězec dotazu, záhlaví a text.

`IHttpResponseFeature` Definuje strukturu odpověď HTTP včetně kódů, záhlaví a text odpovědi.

`IHttpAuthenticationFeature` Definuje podporu pro identifikaci uživatelů na základě `ClaimsPrincipal` a určení obslužnou rutinu ověřování.

`IHttpUpgradeFeature` Definuje podporu pro [HTTP upgrady](https://tools.ietf.org/html/rfc2616.html#section-14.42), které umožňují klientovi k určení, které další protokoly se chtěli používat, pokud chce přepnout protokolů serveru.

`IHttpBufferingFeature` Definuje metody pro zakázání ukládání do vyrovnávací paměti požadavky a/nebo odpovědí.

`IHttpConnectionFeature` Definuje vlastnosti pro místní a vzdálené adresy a porty.

`IHttpRequestLifetimeFeature` Definuje podporu pro přerušení připojení nebo zjišťování, pokud žádost se ukončila předčasně ukončen, například po odpojení klienta.

`IHttpSendFileFeature` Definuje metody pro asynchronní odesílání souborů.

`IHttpWebSocketFeature` Definuje rozhraní API pro podporu webové sokety.

`IHttpRequestIdentifierFeature` Přidá vlastnost, která je možné implementovat k jednoznačné identifikaci požadavků.

`ISessionFeature` Definuje `ISessionFactory` a `ISession` abstrakce pro podporu uživatelských relací.

`ITlsConnectionFeature` Definuje rozhraní API pro načítání klientské certifikáty.

`ITlsTokenBindingFeature` Definuje metody pro práci s parametry token vazby protokolu TLS.

> [!NOTE]
> `ISessionFeature` není funkce serveru, ale implementuje ho `SessionMiddleware` (naleznete v tématu [Správa stavu aplikace](app-state.md)).

## <a name="feature-collections"></a>Kolekce funkcí

`Features` Vlastnost `HttpContext` poskytuje rozhraní pro získání a nastavení k dispozici funkce protokolu HTTP pro aktuální požadavek. Protože kolekce funkcí proměnlivé i v rámci kontextu požadavku, middleware je možné změnit kolekci a přidání podpory pro další funkce.

## <a name="middleware-and-request-features"></a>Middleware a žádosti o funkce

I když servery zodpovědná za vytvoření kolekce funkcí, middleware lze do této kolekce přidat i využívat funkce z kolekce. Například `StaticFileMiddleware` přistupuje `IHttpSendFileFeature` funkce. Pokud funkci existuje, se používá k odesílání požadovaných statických souborů z jeho fyzickou cestu. V opačném případě pomalejší alternativní metoda se používá k odeslání souboru. Pokud je k dispozici, `IHttpSendFileFeature` umožňuje operačního systému k otevření souboru a provádění kopie režimu jádra s přímým přístupem k síťové kartě.

Kromě toho middleware lze přidat do kolekce funkce stanovené serveru. Existující funkce lze nahradit i middlewaru umožňuje middleware pro rozšíření funkcí serveru. Funkce přidané do kolekce jsou k dispozici okamžitě na další middleware nebo základní samotná aplikace později v kanálu požadavku.

Díky kombinaci implementace vlastního serveru a vylepšení pro konkrétní middleware, lze sestavit přesnou sadu funkcí, které aplikace vyžaduje. To umožňuje chybějící funkce přidávané bez nutnosti provádění změn na serveru a zajišťuje jsou přístupné jenom minimální množství funkcí, tedy omezení útoku surface oblasti a zlepšení výkonu.

## <a name="summary"></a>Souhrn

Funkce rozhraní definují určité funkce protokolu HTTP, které můžou podporovat daného požadavku. Servery definovat kolekce funkcí a počáteční sadu funkcí podporovaných tímto serverem, ale ke zvýšení tyto funkce můžete použít middlewaru.

## <a name="additional-resources"></a>Další zdroje

* [Servery](xref:fundamentals/servers/index)
* [Middleware](xref:fundamentals/middleware/index)
* [Otevřené webové rozhraní pro .NET (OWIN)](xref:fundamentals/owin)

---
title: Požadavky na zabezpečení v ASP.NET Core SignalR
author: bradygaster
description: Naučte se používat ověřování a autorizaci v ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/security
ms.openlocfilehash: 12293c5cb3dc49d505225f1b44e824e9273cfffc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630988"
---
# <a name="security-considerations-in-aspnet-core-no-locsignalr"></a>Požadavky na zabezpečení v ASP.NET Core SignalR

Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)

Tento článek poskytuje informace o zabezpečení SignalR .

## <a name="cross-origin-resource-sharing"></a>Sdílení prostředků různého původu

[Sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) se dá použít k povolení připojení mezi zdroji SignalR v prohlížeči. Pokud je JavaScriptový kód hostovaný v jiné doméně než SignalR aplikace, musí být povolen [middleware CORS](xref:security/cors) , aby se JavaScript mohl připojit k SignalR aplikaci. Povoluje žádosti mezi zdroji jenom z domén, které důvěřujete nebo ovládáte. Příklad:

* Váš web je hostovaný na `http://www.example.com`
* Vaše SignalR aplikace je hostována na `http://signalr.example.com`

V aplikaci by měla být nakonfigurovaná CORS SignalR , aby povolovala jenom původ `www.example.com` .

Další informace o konfiguraci CORS najdete v tématu [povolení požadavků mezi zdroji (CORS)](xref:security/cors). SignalR**vyžaduje** následující zásady CORS:

* Povolí konkrétní očekávané zdroje. Povolení libovolného zdroje je možné, ale **není zabezpečené nebo** Doporučené.
* Metody HTTP `GET` a `POST` musí být povoleny.
* Přihlašovací údaje musí být povoleny v pořadí, aby cookie správně fungovaly rychlé relace založené na. Musí být povolené i v případě, že se ověřování nepoužívá.

::: moniker range=">= aspnetcore-5.0"

V 5,0 jsme ale v klientovi TypeScript zadali možnost, aby nepoužívala přihlašovací údaje.
Možnost Nepoužívat přihlašovací údaje by se měla používat jenom v případě, že víte 100%, že přihlašovací údaje, jako Cookie je třeba, se ve vašich aplikacích nepotřebují v aplikacích, které cookie používají Azure App Service při použití více serverů pro rychlé relace.

::: moniker-end

Následující zásada CORS například umožňuje klientovi, který je SignalR hostitelem `https://example.com` aplikace, získat přístup k SignalR aplikaci hostované na `https://signalr.example.com` :

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a>Omezení původu protokolu WebSocket

::: moniker range=">= aspnetcore-2.2"

Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets. Pro omezení původu u WebSockets si přečtěte [omezení původu WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets. Prohlížeče **neobsahují:**

* Provádění požadavků CORS v řádu CORS
* Respektujte omezení zadaná v `Access-Control` hlavičkách při vytváření požadavků protokolu WebSocket.

Prohlížeče ale `Origin` při vydávání požadavků protokolu WebSocket odesílají hlavičku. Aplikace by měly být nakonfigurovány pro ověření těchto hlaviček, aby bylo zajištěno, že budou povoleny pouze objekty WebSockets přicházející z očekávaných zdrojů.

V ASP.NET Core 2,1 a novějších lze ověření hlaviček dosáhnout pomocí vlastního middleware, který **je umístěn před `UseSignalR` a middleware ověřování** v `Configure` :

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> `Origin`Záhlaví řídí klient a, podobně jako `Referer` záhlaví, může být falešným. Tato **záhlaví by se neměla používat** jako ověřovací mechanismus.

::: moniker-end

## <a name="connectionid"></a>ConnectionId

`ConnectionId`Pokud SignalR je verze serveru nebo klienta ASP.NET Core 2,2 nebo starší, může vystavení způsobit škodlivou zosobnění. Pokud je SignalR verze serveru a klienta ASP.NET Core 3,0 nebo novější, `ConnectionToken` `ConnectionId` musí být místo toho zachovaná tajná. `ConnectionToken`Nezveřejňuje se v žádném rozhraní API.  Může být obtížné zajistit, aby se starší SignalR klienti nepřipojovali k serveru, takže i v případě SignalR , že je vaše verze serveru ASP.NET Core 3,0 nebo novější, `ConnectionId` neměl by být vystavený.

## <a name="access-token-logging"></a>Protokolování přístupového tokenu

Při použití objektů WebSocket nebo událostí odesílaných serverem klientský prohlížeč odešle přístupový token do řetězce dotazu. Získání přístupového tokenu prostřednictvím řetězce dotazu je obecně bezpečné jako použití standardního `Authorization` záhlaví. K zajištění zabezpečeného koncového připojení mezi klientem a serverem vždy použijte protokol HTTPS. Mnohé webové servery protokolují adresu URL pro každý požadavek včetně řetězce dotazu. Protokolování adres URL může protokolovat přístupový token. ASP.NET Core zaznamená adresu URL pro každý požadavek ve výchozím nastavení, což bude obsahovat řetězec dotazu. Příklad:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

Pokud máte obavy týkající se protokolování těchto dat do protokolů serveru, můžete toto protokolování zcela zakázat nakonfigurováním `Microsoft.AspNetCore.Hosting` protokolovacího nástroje na `Warning` úroveň nebo výše (tyto zprávy jsou zapsány na `Info` úrovni). Další informace najdete v tématu [filtrování protokolu](xref:fundamentals/logging/index#log-filtering) pro další informace. Pokud přesto chcete protokolovat určité informace o žádostech, můžete [napsat middleware](xref:fundamentals/middleware/write) pro protokolování dat, která požadujete, a odfiltrovat `access_token` hodnotu řetězce dotazu (Pokud je k dispozici).

## <a name="exceptions"></a>Výjimky

Zprávy výjimek se obecně považují za citlivá data, která by neměla být odhalena klientovi. Ve výchozím nastavení SignalR neodesílají podrobnosti o výjimce vyvolané metodou centra klientovi. Místo toho klient obdrží obecnou zprávu oznamující, že došlo k chybě. Doručení zprávy výjimky klientovi lze přepsat (například při vývoji nebo testování) pomocí [EnableDetailedErrors](xref:signalr/configuration#configure-server-options). Zprávy výjimek by neměly být vystaveny klientovi v produkčních aplikacích.

## <a name="buffer-management"></a>Správa vyrovnávací paměti

SignalR pro správu příchozích a odchozích zpráv používá vyrovnávací paměti pro připojení. Ve výchozím nastavení SignalR omezuje tyto vyrovnávací paměti na 32 KB. Největší zpráva, kterou může klient nebo server odeslat, je 32 KB. Maximální velikost paměti spotřebované připojením pro zprávy je 32 KB. Pokud jsou vaše zprávy vždycky menší než 32 KB, můžete omezit limit, který:

* Zabraňuje klientovi, aby mohl odeslat větší zprávu.
* Server nikdy nebude muset přidělit velké vyrovnávací paměti pro příjem zpráv.

Pokud jsou vaše zprávy větší než 32 KB, můžete tento limit zvýšit. Zvýšení tohoto limitu znamená:

* Klient může způsobit přidělení rozsáhlých vyrovnávacích pamětí pro server.
* Přidělení serveru pro velké vyrovnávací paměti může snížit počet souběžných připojení.

U příchozích a odchozích zpráv platí omezení, jak je možné nakonfigurovat u objektu [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) nakonfigurovaného v nástroji `MapHub` :

* `ApplicationMaxBufferSize` představuje maximální počet bajtů od klienta, které jsou vyrovnávací paměti serveru. Pokud se klient pokusí odeslat zprávu větší, než je toto omezení, připojení může být zavřeno.
* `TransportMaxBufferSize` představuje maximální počet bajtů, které může server odeslat. Pokud se server pokusí odeslat zprávu (včetně návratových hodnot z metod z rozbočovače) větší, než je tento limit, bude vyvolána výjimka.

Nastavením limitu `0` zakážete tento limit. Odebráním tohoto limitu můžete klientovi poslat zprávu libovolné velikosti. Zlomyslní klienti odesílající velké zprávy můžou způsobit přidělení nadměrné paměti. Nadměrné využití paměti může významně snížit počet souběžných připojení.

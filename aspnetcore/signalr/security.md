---
title: Požadavky na zabezpečení v ASP.NET Core SignalR
author: bradygaster
description: Naučte se používat ověřování a autorizaci v ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: 1bdb8b10a24c65735f49f04285e4129cb77eb3fb
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828942"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a>Požadavky na zabezpečení v ASP.NET Core SignalR

Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)

Tento článek poskytuje informace o zabezpečení SignalR.

## <a name="cross-origin-resource-sharing"></a>Sdílení prostředků různého původu

[Sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) se dá použít k povolení připojení SignalR mezi zdroji v prohlížeči. Pokud je JavaScriptový kód hostovaný v jiné doméně než aplikace SignalR, musí být povolený [middleware CORS](xref:security/cors) , aby se JavaScript mohl připojit k aplikaci SignalR. Povoluje žádosti mezi zdroji jenom z domén, které důvěřujete nebo ovládáte. Příklad:

* Váš web je hostovaný na `http://www.example.com`
* Vaše aplikace SignalR hostuje `http://signalr.example.com`

CORS by měl být nakonfigurovaný v aplikaci SignalR, aby povolovala jenom původní `www.example.com`.

Další informace o konfiguraci CORS najdete v tématu [povolení požadavků mezi zdroji (CORS)](xref:security/cors). SignalR **vyžaduje** následující zásady CORS:

* Povolí konkrétní očekávané zdroje. Povolení libovolného zdroje je možné, ale **není zabezpečené nebo** Doporučené.
* Metody HTTP `GET` a `POST` musí být povoleny.
* Aby rychlé relace založené na souborech cookie fungovaly správně, musí být přihlašovací údaje povolené. Musí být povolené i v případě, že se ověřování nepoužívá.

<!--
::: moniker range=">= aspnetcore-5.0"  // Moniker here just to make sure this doesn't get missed in the 5.0 version update.
However, in 5.0 we have provided an option in the TypeScript client to not use credentials.
The not to use credentials option should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers)

For more info, see https://github.com/aspnet/AspNetCore.Docs/issues/16003
.-->

Následující zásada CORS například umožňuje klientovi SignalR, který je hostovaný na `https://example.com` pro přístup k aplikaci SignalR hostované v `https://signalr.example.com`:

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
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

> [!NOTE]
> SignalR není kompatibilní s integrovanou funkcí CORS v Azure App Service.

## <a name="websocket-origin-restriction"></a>Omezení původu protokolu WebSocket

::: moniker range=">= aspnetcore-2.2"

Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets. Pro omezení původu u WebSockets si přečtěte [omezení původu WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets. Prohlížeče **neobsahují:**

* Provádění požadavků CORS v řádu CORS
* Respektujte omezení zadaná v hlavičkách `Access-Control` při vytváření požadavků protokolu WebSocket.

Prohlížeče ale při vydávání požadavků protokolu WebSocket odesílají hlavičku `Origin`. Aplikace by měly být nakonfigurovány pro ověření těchto hlaviček, aby bylo zajištěno, že budou povoleny pouze objekty WebSockets přicházející z očekávaných zdrojů.

V ASP.NET Core 2,1 a novějších se dá ověřování hlaviček dosáhnout pomocí vlastního middleware, který je umístěn **před `UseSignalR`a ověřovacím middlewarem** v `Configure`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> Záhlaví `Origin` řídí klient a, podobně jako `Referer` záhlaví, může být falešným. Tato **záhlaví by se neměla používat** jako ověřovací mechanismus.

::: moniker-end

## <a name="access-token-logging"></a>Protokolování přístupového tokenu

Při použití objektů WebSocket nebo událostí odesílaných serverem klientský prohlížeč odešle přístupový token do řetězce dotazu. Příjem přístupového tokenu prostřednictvím řetězce dotazu je obecně zabezpečený jako při použití hlavičky Standard `Authorization`. Protokol HTTPS byste měli vždycky používat k zajištění zabezpečeného koncového připojení mezi klientem a serverem. Mnohé webové servery protokolují adresu URL pro každý požadavek včetně řetězce dotazu. Protokolování adres URL může protokolovat přístupový token. ASP.NET Core zaznamená adresu URL pro každý požadavek ve výchozím nastavení, což bude obsahovat řetězec dotazu. Příklad:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

Pokud máte obavy týkající se protokolování těchto dat do protokolů serveru, můžete toto protokolování zcela vypnout nakonfigurováním protokolovacího nástroje `Microsoft.AspNetCore.Hosting` na úroveň `Warning` nebo výše (tyto zprávy jsou napsány na úrovni `Info`). Další informace najdete v dokumentaci o [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) . Pokud pořád chcete protokolovat určité informace o žádostech, můžete [napsat middleware](xref:fundamentals/middleware/write) pro protokolování dat, která požadujete, a odfiltrovat `access_token` hodnotu řetězce dotazu (Pokud je k dispozici).

## <a name="exceptions"></a>Výjimky

Zprávy výjimek se obecně považují za citlivá data, která by neměla být odhalena klientovi. Ve výchozím nastavení SignalR neposílá podrobnosti o výjimce vyvolané metodou centra klientovi. Místo toho klient obdrží obecnou zprávu oznamující, že došlo k chybě. Doručení zprávy výjimky klientovi lze přepsat (například při vývoji nebo testování) pomocí [EnableDetailedErrors](xref:signalr/configuration#configure-server-options). Zprávy výjimek by neměly být vystaveny klientovi v produkčních aplikacích.

## <a name="buffer-management"></a>Správa vyrovnávací paměti

SignalR používá pro správu příchozích a odchozích zpráv vyrovnávací paměti pro připojení. Ve výchozím nastavení SignalR omezuje tyto vyrovnávací paměti na 32 KB. Největší zpráva, kterou může klient nebo server odeslat, je 32 KB. Maximální velikost paměti spotřebované připojením pro zprávy je 32 KB. Pokud jsou vaše zprávy vždycky menší než 32 KB, můžete omezit limit, který:

* Zabraňuje klientovi, aby mohl odeslat větší zprávu.
* Server nikdy nebude muset přidělit velké vyrovnávací paměti pro příjem zpráv.

Pokud jsou vaše zprávy větší než 32 KB, můžete tento limit zvýšit. Zvýšení tohoto limitu znamená:

* Klient může způsobit přidělení rozsáhlých vyrovnávacích pamětí pro server.
* Přidělení serveru pro velké vyrovnávací paměti může snížit počet souběžných připojení.

U příchozích a odchozích zpráv platí omezení, jak je možné nakonfigurovat u objektu [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) nakonfigurovaného v `MapHub`:

* `ApplicationMaxBufferSize` představuje maximální počet bajtů od klienta, které jsou vyrovnávací paměti serveru. Pokud se klient pokusí odeslat zprávu větší, než je toto omezení, připojení může být zavřeno.
* `TransportMaxBufferSize` představuje maximální počet bajtů, které může server odeslat. Pokud se server pokusí odeslat zprávu (včetně návratových hodnot z metod z rozbočovače) větší, než je tento limit, bude vyvolána výjimka.

Nastavení limitu pro `0` zakáže limit. Odebráním tohoto limitu můžete klientovi poslat zprávu libovolné velikosti. Zlomyslní klienti odesílající velké zprávy můžou způsobit přidělení nadměrné paměti. Nadměrné využití paměti může významně snížit počet souběžných připojení.

---
title: Otázky zabezpečení v nástroji ASP.NET Core Signal
author: bradygaster
description: Naučte se používat ověřování a autorizaci v nástroji ASP.NET Core Signal.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/security
ms.openlocfilehash: a52db2ff51c55f7299d63aa3c7398f99727e0694
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746548"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a>Otázky zabezpečení v nástroji ASP.NET Core Signal

Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)

Tento článek poskytuje informace o zabezpečení signalizace.

## <a name="cross-origin-resource-sharing"></a>Sdílení prostředků mezi zdroji

[Sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) se dá použít k povolení připojení k signalizaci mezi zdroji v prohlížeči. Pokud je JavaScriptový kód hostovaný v jiné doméně než aplikace pro signalizaci, musí být povolený [middleware CORS](xref:security/cors) , aby se JavaScript mohl připojit k aplikaci Signal. Povoluje žádosti mezi zdroji jenom z domén, které důvěřujete nebo ovládáte. Příklad:

* Váš web je hostovaný na`http://www.example.com`
* Vaše aplikace Signal je hostovaná na`http://signalr.example.com`

V aplikaci signalizace by měla být nakonfigurovaná CORS, aby bylo možné `www.example.com`jenom původ.

Další informace o konfiguraci CORS najdete v tématu [povolení požadavků mezi zdroji (CORS)](xref:security/cors). Signál **vyžaduje** následující zásady CORS:

* Povolí konkrétní očekávané zdroje. Povolení libovolného zdroje je možné, ale **není zabezpečené nebo** Doporučené.
* Metody `GET` http a `POST` musí být povoleny.
* Přihlašovací údaje musí být povolené, i když se ověřování nepoužívá.

Následující zásada CORS například umožňuje klientovi prohlížeče signálu, `https://example.com` který je hostitelem aplikace, získat přístup k aplikaci Signal, která je hostována na: `https://signalr.example.com`

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
> Návěstí není kompatibilní s integrovanou funkcí CORS v Azure App Service.

## <a name="websocket-origin-restriction"></a>Omezení původu protokolu WebSocket

::: moniker range=">= aspnetcore-2.2"

Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets. Pro omezení původu u WebSockets si přečtěte [omezení původu WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets. Prohlížeče **neobsahují:**

* Provádění požadavků CORS v řádu CORS
* Respektujte omezení zadaná v `Access-Control` hlavičkách při vytváření požadavků protokolu WebSocket.

Prohlížeče ale při vydávání požadavků protokolu `Origin` WebSocket odesílají hlavičku. Aplikace by měly být nakonfigurovány pro ověření těchto hlaviček, aby bylo zajištěno, že budou povoleny pouze objekty WebSockets přicházející z očekávaných zdrojů.

V ASP.NET Core 2,1 a novějších lze ověření hlaviček dosáhnout pomocí vlastního middleware, který **je `UseSignalR`umístěn před a middleware ověřování** v: `Configure`

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> Záhlaví řídí klient a, `Referer` podobně jako záhlaví, může být falešným. `Origin` Tato **záhlaví by se neměla používat** jako ověřovací mechanismus.

::: moniker-end

## <a name="access-token-logging"></a>Protokolování přístupového tokenu

Při použití objektů WebSocket nebo událostí odesílaných serverem klientský prohlížeč odešle přístupový token do řetězce dotazu. Příjem přístupového tokenu prostřednictvím řetězce dotazu je obecně zabezpečený jako při použití `Authorization` standardního záhlaví. Protokol HTTPS byste měli vždycky používat k zajištění zabezpečeného koncového připojení mezi klientem a serverem. Mnohé webové servery protokolují adresu URL pro každý požadavek včetně řetězce dotazu. Protokolování adres URL může protokolovat přístupový token. ASP.NET Core zaznamená adresu URL pro každý požadavek ve výchozím nastavení, což bude obsahovat řetězec dotazu. Příklad:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

Pokud máte obavy týkající se protokolování těchto dat do protokolů serveru, můžete toto protokolování zcela zakázat nakonfigurováním `Microsoft.AspNetCore.Hosting` protokolovacího nástroje `Warning` na úroveň nebo výše (tyto zprávy jsou zapsány na `Info` úrovni). Další informace najdete v dokumentaci o [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) . Pokud přesto chcete protokolovat určité informace o žádostech, můžete [napsat middleware](xref:fundamentals/middleware/write) pro protokolování dat, která požadujete, a odfiltrovat hodnotu řetězce dotazu (Pokud je k `access_token` dispozici).

## <a name="exceptions"></a>Výjimky

Zprávy výjimek se obecně považují za citlivá data, která by neměla být odhalena klientovi. Ve výchozím nastavení signál neodesílá podrobnosti o výjimce vyvolané metodou centra klientovi. Místo toho klient obdrží obecnou zprávu oznamující, že došlo k chybě. Doručení zprávy o výjimce klientovi lze přepsat (například při vývoji nebo testování) pomocí [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options). Zprávy výjimek by neměly být vystaveny klientovi v produkčních aplikacích.

## <a name="buffer-management"></a>Správa vyrovnávací paměti

Signalizace používá pro správu příchozích a odchozích zpráv vyrovnávací paměti vázané na připojení. Ve výchozím nastavení signál omezuje tyto vyrovnávací paměti na 32 KB. Největší zpráva, kterou může klient nebo server odeslat, je 32 KB. Maximální velikost paměti spotřebované připojením pro zprávy je 32 KB. Pokud jsou vaše zprávy vždycky menší než 32 KB, můžete omezit limit, který:

* Zabraňuje klientovi, aby mohl odeslat větší zprávu.
* Server nikdy nebude muset přidělit velké vyrovnávací paměti pro příjem zpráv.

Pokud jsou vaše zprávy větší než 32 KB, můžete tento limit zvýšit. Zvýšení tohoto limitu znamená:

* Klient může způsobit přidělení rozsáhlých vyrovnávacích pamětí pro server.
* Přidělení serveru pro velké vyrovnávací paměti může snížit počet souběžných připojení.

U příchozích a odchozích zpráv platí omezení, jak je možné nakonfigurovat u [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) objektu nakonfigurovaného v: `MapHub`

* `ApplicationMaxBufferSize`představuje maximální počet bajtů od klienta, které jsou vyrovnávací paměti serveru. Pokud se klient pokusí odeslat zprávu větší, než je toto omezení, připojení může být zavřeno.
* `TransportMaxBufferSize`představuje maximální počet bajtů, které může server odeslat. Pokud se server pokusí odeslat zprávu (včetně návratových hodnot z metod z rozbočovače) větší, než je tento limit, bude vyvolána výjimka.

Nastavením limitu `0` zakážete tento limit. Odebráním tohoto limitu můžete klientovi poslat zprávu libovolné velikosti. Zlomyslní klienti odesílající velké zprávy můžou způsobit přidělení nadměrné paměti. Nadměrné využití paměti může významně snížit počet souběžných připojení.

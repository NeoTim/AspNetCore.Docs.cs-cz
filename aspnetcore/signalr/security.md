---
title: Požadavky na zabezpečení v ASP.NET CoreSignalR
author: bradygaster
description: Naučte se používat ověřování a autorizaci v ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/security
ms.openlocfilehash: e004899e334738f723cb98638cb31de8d314a830
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022469"
---
# <a name="security-considerations-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="c659b-103">Požadavky na zabezpečení v ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="c659b-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="c659b-104">Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="c659b-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="c659b-105">Tento článek poskytuje informace o zabezpečení SignalR .</span><span class="sxs-lookup"><span data-stu-id="c659b-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="c659b-106">Sdílení prostředků různého původu</span><span class="sxs-lookup"><span data-stu-id="c659b-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="c659b-107">[Sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) se dá použít k povolení připojení mezi zdroji SignalR v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="c659b-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="c659b-108">Pokud je JavaScriptový kód hostovaný v jiné doméně než SignalR aplikace, musí být povolen [middleware CORS](xref:security/cors) , aby se JavaScript mohl připojit k SignalR aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c659b-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="c659b-109">Povoluje žádosti mezi zdroji jenom z domén, které důvěřujete nebo ovládáte.</span><span class="sxs-lookup"><span data-stu-id="c659b-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="c659b-110">Například:</span><span class="sxs-lookup"><span data-stu-id="c659b-110">For example:</span></span>

* <span data-ttu-id="c659b-111">Váš web je hostovaný na`http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="c659b-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="c659b-112">Vaše SignalR aplikace je hostována na`http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="c659b-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="c659b-113">V aplikaci by měla být nakonfigurovaná CORS SignalR , aby povolovala jenom původ `www.example.com` .</span><span class="sxs-lookup"><span data-stu-id="c659b-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="c659b-114">Další informace o konfiguraci CORS najdete v tématu [povolení požadavků mezi zdroji (CORS)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="c659b-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> <span data-ttu-id="c659b-115">SignalR**vyžaduje** následující zásady CORS:</span><span class="sxs-lookup"><span data-stu-id="c659b-115">SignalR **requires** the following CORS policies:</span></span>

* <span data-ttu-id="c659b-116">Povolí konkrétní očekávané zdroje.</span><span class="sxs-lookup"><span data-stu-id="c659b-116">Allow the specific expected origins.</span></span> <span data-ttu-id="c659b-117">Povolení libovolného zdroje je možné, ale **není zabezpečené nebo** Doporučené.</span><span class="sxs-lookup"><span data-stu-id="c659b-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="c659b-118">Metody HTTP `GET` a `POST` musí být povoleny.</span><span class="sxs-lookup"><span data-stu-id="c659b-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="c659b-119">Přihlašovací údaje musí být povoleny v pořadí, aby cookie správně fungovaly rychlé relace založené na.</span><span class="sxs-lookup"><span data-stu-id="c659b-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="c659b-120">Musí být povolené i v případě, že se ověřování nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="c659b-120">They must be enabled even when authentication isn't used.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c659b-121">V 5,0 jsme ale v klientovi TypeScript zadali možnost, aby nepoužívala přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="c659b-121">However, in 5.0 we have provided an option in the TypeScript client to not use credentials.</span></span>
<span data-ttu-id="c659b-122">Možnost Nepoužívat přihlašovací údaje by se měla používat jenom v případě, že víte 100%, že přihlašovací údaje, jako Cookie je třeba, se ve vašich aplikacích nepotřebují v aplikacích, které cookie používají Azure App Service při použití více serverů pro rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="c659b-122">The option to not use credentials should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers for sticky sessions).</span></span>

::: moniker-end

<span data-ttu-id="c659b-123">Následující zásada CORS například umožňuje klientovi, který je SignalR hostitelem `https://example.com` aplikace, získat přístup k SignalR aplikaci hostované na `https://signalr.example.com` :</span><span class="sxs-lookup"><span data-stu-id="c659b-123">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

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

## <a name="websocket-origin-restriction"></a><span data-ttu-id="c659b-124">Omezení původu protokolu WebSocket</span><span class="sxs-lookup"><span data-stu-id="c659b-124">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="c659b-125">Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c659b-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="c659b-126">Pro omezení původu u WebSockets si přečtěte [omezení původu WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="c659b-126">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c659b-127">Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c659b-127">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="c659b-128">Prohlížeče **neobsahují:**</span><span class="sxs-lookup"><span data-stu-id="c659b-128">Browsers do **not**:</span></span>

* <span data-ttu-id="c659b-129">Provádění požadavků CORS v řádu CORS</span><span class="sxs-lookup"><span data-stu-id="c659b-129">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="c659b-130">Respektujte omezení zadaná v `Access-Control` hlavičkách při vytváření požadavků protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="c659b-130">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="c659b-131">Prohlížeče ale `Origin` při vydávání požadavků protokolu WebSocket odesílají hlavičku.</span><span class="sxs-lookup"><span data-stu-id="c659b-131">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="c659b-132">Aplikace by měly být nakonfigurovány pro ověření těchto hlaviček, aby bylo zajištěno, že budou povoleny pouze objekty WebSockets přicházející z očekávaných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="c659b-132">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="c659b-133">V ASP.NET Core 2,1 a novějších lze ověření hlaviček dosáhnout pomocí vlastního middleware, který **je umístěn před `UseSignalR` a middleware ověřování** v `Configure` :</span><span class="sxs-lookup"><span data-stu-id="c659b-133">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="c659b-134">`Origin`Záhlaví řídí klient a, podobně jako `Referer` záhlaví, může být falešným.</span><span class="sxs-lookup"><span data-stu-id="c659b-134">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="c659b-135">Tato **záhlaví by se neměla používat** jako ověřovací mechanismus.</span><span class="sxs-lookup"><span data-stu-id="c659b-135">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="c659b-136">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="c659b-136">ConnectionId</span></span>

<span data-ttu-id="c659b-137">`ConnectionId`Pokud SignalR je verze serveru nebo klienta ASP.NET Core 2,2 nebo starší, může vystavení způsobit škodlivou zosobnění.</span><span class="sxs-lookup"><span data-stu-id="c659b-137">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="c659b-138">Pokud je SignalR verze serveru a klienta ASP.NET Core 3,0 nebo novější, `ConnectionToken` `ConnectionId` musí být místo toho zachovaná tajná.</span><span class="sxs-lookup"><span data-stu-id="c659b-138">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="c659b-139">`ConnectionToken`Nezveřejňuje se v žádném rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c659b-139">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="c659b-140">Může být obtížné zajistit, aby se starší SignalR klienti nepřipojovali k serveru, takže i v případě SignalR , že je vaše verze serveru ASP.NET Core 3,0 nebo novější, `ConnectionId` neměl by být vystavený.</span><span class="sxs-lookup"><span data-stu-id="c659b-140">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="c659b-141">Protokolování přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="c659b-141">Access token logging</span></span>

<span data-ttu-id="c659b-142">Při použití objektů WebSocket nebo událostí odesílaných serverem klientský prohlížeč odešle přístupový token do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="c659b-142">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="c659b-143">Získání přístupového tokenu prostřednictvím řetězce dotazu je obecně bezpečné jako použití standardního `Authorization` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="c659b-143">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="c659b-144">K zajištění zabezpečeného koncového připojení mezi klientem a serverem vždy použijte protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c659b-144">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="c659b-145">Mnohé webové servery protokolují adresu URL pro každý požadavek včetně řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="c659b-145">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="c659b-146">Protokolování adres URL může protokolovat přístupový token.</span><span class="sxs-lookup"><span data-stu-id="c659b-146">Logging the URLs may log the access token.</span></span> <span data-ttu-id="c659b-147">ASP.NET Core zaznamená adresu URL pro každý požadavek ve výchozím nastavení, což bude obsahovat řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="c659b-147">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="c659b-148">Například:</span><span class="sxs-lookup"><span data-stu-id="c659b-148">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

<span data-ttu-id="c659b-149">Pokud máte obavy týkající se protokolování těchto dat do protokolů serveru, můžete toto protokolování zcela zakázat nakonfigurováním `Microsoft.AspNetCore.Hosting` protokolovacího nástroje na `Warning` úroveň nebo výše (tyto zprávy jsou zapsány na `Info` úrovni).</span><span class="sxs-lookup"><span data-stu-id="c659b-149">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="c659b-150">Další informace najdete v tématu [filtrování protokolu](xref:fundamentals/logging/index#log-filtering) pro další informace.</span><span class="sxs-lookup"><span data-stu-id="c659b-150">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="c659b-151">Pokud přesto chcete protokolovat určité informace o žádostech, můžete [napsat middleware](xref:fundamentals/middleware/write) pro protokolování dat, která požadujete, a odfiltrovat `access_token` hodnotu řetězce dotazu (Pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="c659b-151">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="c659b-152">Výjimky</span><span class="sxs-lookup"><span data-stu-id="c659b-152">Exceptions</span></span>

<span data-ttu-id="c659b-153">Zprávy výjimek se obecně považují za citlivá data, která by neměla být odhalena klientovi.</span><span class="sxs-lookup"><span data-stu-id="c659b-153">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="c659b-154">Ve výchozím nastavení SignalR neodesílají podrobnosti o výjimce vyvolané metodou centra klientovi.</span><span class="sxs-lookup"><span data-stu-id="c659b-154">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="c659b-155">Místo toho klient obdrží obecnou zprávu oznamující, že došlo k chybě.</span><span class="sxs-lookup"><span data-stu-id="c659b-155">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="c659b-156">Doručení zprávy výjimky klientovi lze přepsat (například při vývoji nebo testování) pomocí [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="c659b-156">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="c659b-157">Zprávy výjimek by neměly být vystaveny klientovi v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="c659b-157">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="c659b-158">Správa vyrovnávací paměti</span><span class="sxs-lookup"><span data-stu-id="c659b-158">Buffer management</span></span>

<span data-ttu-id="c659b-159">SignalRpro správu příchozích a odchozích zpráv používá vyrovnávací paměti pro připojení.</span><span class="sxs-lookup"><span data-stu-id="c659b-159">SignalR uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="c659b-160">Ve výchozím nastavení SignalR omezuje tyto vyrovnávací paměti na 32 KB.</span><span class="sxs-lookup"><span data-stu-id="c659b-160">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="c659b-161">Největší zpráva, kterou může klient nebo server odeslat, je 32 KB.</span><span class="sxs-lookup"><span data-stu-id="c659b-161">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="c659b-162">Maximální velikost paměti spotřebované připojením pro zprávy je 32 KB.</span><span class="sxs-lookup"><span data-stu-id="c659b-162">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="c659b-163">Pokud jsou vaše zprávy vždycky menší než 32 KB, můžete omezit limit, který:</span><span class="sxs-lookup"><span data-stu-id="c659b-163">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="c659b-164">Zabraňuje klientovi, aby mohl odeslat větší zprávu.</span><span class="sxs-lookup"><span data-stu-id="c659b-164">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="c659b-165">Server nikdy nebude muset přidělit velké vyrovnávací paměti pro příjem zpráv.</span><span class="sxs-lookup"><span data-stu-id="c659b-165">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="c659b-166">Pokud jsou vaše zprávy větší než 32 KB, můžete tento limit zvýšit.</span><span class="sxs-lookup"><span data-stu-id="c659b-166">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="c659b-167">Zvýšení tohoto limitu znamená:</span><span class="sxs-lookup"><span data-stu-id="c659b-167">Increasing this limit means:</span></span>

* <span data-ttu-id="c659b-168">Klient může způsobit přidělení rozsáhlých vyrovnávacích pamětí pro server.</span><span class="sxs-lookup"><span data-stu-id="c659b-168">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="c659b-169">Přidělení serveru pro velké vyrovnávací paměti může snížit počet souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="c659b-169">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="c659b-170">U příchozích a odchozích zpráv platí omezení, jak je možné nakonfigurovat u objektu [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) nakonfigurovaného v nástroji `MapHub` :</span><span class="sxs-lookup"><span data-stu-id="c659b-170">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="c659b-171">`ApplicationMaxBufferSize`představuje maximální počet bajtů od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="c659b-171">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="c659b-172">Pokud se klient pokusí odeslat zprávu větší, než je toto omezení, připojení může být zavřeno.</span><span class="sxs-lookup"><span data-stu-id="c659b-172">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="c659b-173">`TransportMaxBufferSize`představuje maximální počet bajtů, které může server odeslat.</span><span class="sxs-lookup"><span data-stu-id="c659b-173">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="c659b-174">Pokud se server pokusí odeslat zprávu (včetně návratových hodnot z metod z rozbočovače) větší, než je tento limit, bude vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="c659b-174">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="c659b-175">Nastavením limitu `0` zakážete tento limit.</span><span class="sxs-lookup"><span data-stu-id="c659b-175">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="c659b-176">Odebráním tohoto limitu můžete klientovi poslat zprávu libovolné velikosti.</span><span class="sxs-lookup"><span data-stu-id="c659b-176">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="c659b-177">Zlomyslní klienti odesílající velké zprávy můžou způsobit přidělení nadměrné paměti.</span><span class="sxs-lookup"><span data-stu-id="c659b-177">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="c659b-178">Nadměrné využití paměti může významně snížit počet souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="c659b-178">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>

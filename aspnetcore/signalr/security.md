---
title: Požadavky na zabezpečení v ASP.NET Core SignalR
author: bradygaster
description: Naučte se používat ověřování a autorizaci v ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: 4b27d9abb36938ed8161ff0d3535204e3fa68765
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294708"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="7db7e-103">Požadavky na zabezpečení v ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="7db7e-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="7db7e-104">Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="7db7e-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="7db7e-105">Tento článek poskytuje informace o zabezpečení SignalR.</span><span class="sxs-lookup"><span data-stu-id="7db7e-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="7db7e-106">Sdílení prostředků různého původu</span><span class="sxs-lookup"><span data-stu-id="7db7e-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="7db7e-107">[Sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) se dá použít k povolení připojení SignalR mezi zdroji v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7db7e-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="7db7e-108">Pokud je JavaScriptový kód hostovaný v jiné doméně než aplikace SignalR, musí být povolený [middleware CORS](xref:security/cors) , aby se JavaScript mohl připojit k aplikaci SignalR.</span><span class="sxs-lookup"><span data-stu-id="7db7e-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="7db7e-109">Povoluje žádosti mezi zdroji jenom z domén, které důvěřujete nebo ovládáte.</span><span class="sxs-lookup"><span data-stu-id="7db7e-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="7db7e-110">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7db7e-110">For example:</span></span>

* <span data-ttu-id="7db7e-111">Váš web je hostovaný na `http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="7db7e-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="7db7e-112">Vaše aplikace SignalR hostuje `http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="7db7e-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="7db7e-113">CORS by měl být nakonfigurovaný v aplikaci SignalR, aby povolovala jenom původní `www.example.com`.</span><span class="sxs-lookup"><span data-stu-id="7db7e-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="7db7e-114">Další informace o konfiguraci CORS najdete v tématu [povolení požadavků mezi zdroji (CORS)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="7db7e-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="7db7e-115"> **vyžaduje** následující zásady CORS:</span><span class="sxs-lookup"><span data-stu-id="7db7e-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="7db7e-116">Povolí konkrétní očekávané zdroje.</span><span class="sxs-lookup"><span data-stu-id="7db7e-116">Allow the specific expected origins.</span></span> <span data-ttu-id="7db7e-117">Povolení libovolného zdroje je možné, ale **není zabezpečené nebo** Doporučené.</span><span class="sxs-lookup"><span data-stu-id="7db7e-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="7db7e-118">Metody HTTP `GET` a `POST` musí být povoleny.</span><span class="sxs-lookup"><span data-stu-id="7db7e-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="7db7e-119">Aby rychlé relace založené na souborech cookie fungovaly správně, musí být přihlašovací údaje povolené.</span><span class="sxs-lookup"><span data-stu-id="7db7e-119">Credentials must be allowed in order for cookie-based sticky sessions to work correctly.</span></span> <span data-ttu-id="7db7e-120">Musí být povolené i v případě, že se ověřování nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="7db7e-120">They must be enabled even when authentication isn't used.</span></span>

<!--
::: moniker range=">= aspnetcore-5.0"  // Moniker here just to make sure this doesn't get missed in the 5.0 version update.
However, in 5.0 we have provided an option in the TypeScript client to not use credentials.
The not to use credentials option should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers)

For more info, see https://github.com/aspnet/AspNetCore.Docs/issues/16003
.-->

<span data-ttu-id="7db7e-121">Následující zásada CORS například umožňuje klientovi SignalR, který je hostovaný na `https://example.com` pro přístup k aplikaci SignalR hostované v `https://signalr.example.com`:</span><span class="sxs-lookup"><span data-stu-id="7db7e-121">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

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
> SignalR<span data-ttu-id="7db7e-122"> není kompatibilní s integrovanou funkcí CORS v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="7db7e-122"> is not compatible with the built-in CORS feature in Azure App Service.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="7db7e-123">Omezení původu protokolu WebSocket</span><span class="sxs-lookup"><span data-stu-id="7db7e-123">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7db7e-124">Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7db7e-124">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="7db7e-125">Pro omezení původu u WebSockets si přečtěte [omezení původu WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="7db7e-125">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7db7e-126">Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7db7e-126">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="7db7e-127">Prohlížeče **neobsahují:**</span><span class="sxs-lookup"><span data-stu-id="7db7e-127">Browsers do **not**:</span></span>

* <span data-ttu-id="7db7e-128">Provádění požadavků CORS v řádu CORS</span><span class="sxs-lookup"><span data-stu-id="7db7e-128">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="7db7e-129">Respektujte omezení zadaná v hlavičkách `Access-Control` při vytváření požadavků protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7db7e-129">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="7db7e-130">Prohlížeče ale při vydávání požadavků protokolu WebSocket odesílají hlavičku `Origin`.</span><span class="sxs-lookup"><span data-stu-id="7db7e-130">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="7db7e-131">Aplikace by měly být nakonfigurovány pro ověření těchto hlaviček, aby bylo zajištěno, že budou povoleny pouze objekty WebSockets přicházející z očekávaných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="7db7e-131">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="7db7e-132">V ASP.NET Core 2,1 a novějších se dá ověřování hlaviček dosáhnout pomocí vlastního middleware, který je umístěn **před `UseSignalR`a ověřovacím middlewarem** v `Configure`:</span><span class="sxs-lookup"><span data-stu-id="7db7e-132">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="7db7e-133">Záhlaví `Origin` řídí klient a, podobně jako `Referer` záhlaví, může být falešným.</span><span class="sxs-lookup"><span data-stu-id="7db7e-133">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="7db7e-134">Tato **záhlaví by se neměla používat** jako ověřovací mechanismus.</span><span class="sxs-lookup"><span data-stu-id="7db7e-134">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="7db7e-135">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="7db7e-135">ConnectionId</span></span>

<span data-ttu-id="7db7e-136">Vystavení `ConnectionId` může způsobit škodlivou zosobnění, pokud je SignalR Server nebo Klientská verze ASP.NET Core 2,2 nebo starší.</span><span class="sxs-lookup"><span data-stu-id="7db7e-136">Exposing `ConnectionId` can lead to malicious impersonation if the SignalR server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="7db7e-137">Pokud je SignalR Server a Klientská verze ASP.NET Core 3,0 nebo novější, `ConnectionToken` místo `ConnectionId` musí být zachovány tajné klíče.</span><span class="sxs-lookup"><span data-stu-id="7db7e-137">If the SignalR server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="7db7e-138">`ConnectionToken` nezveřejňuje žádné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="7db7e-138">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="7db7e-139">Může být obtížné zajistit, aby se starší klienti SignalR nepřipojovali k serveru, takže i v případě, že je vaše verze SignalR serveru ASP.NET Core 3,0 nebo novější, `ConnectionId` by neměl být vystavený.</span><span class="sxs-lookup"><span data-stu-id="7db7e-139">It can be difficult to ensure that older SignalR clients aren't connecting to the server, so even if your SignalR server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="7db7e-140">Protokolování přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="7db7e-140">Access token logging</span></span>

<span data-ttu-id="7db7e-141">Při použití objektů WebSocket nebo událostí odesílaných serverem klientský prohlížeč odešle přístupový token do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="7db7e-141">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="7db7e-142">Příjem přístupového tokenu prostřednictvím řetězce dotazu je obecně zabezpečený jako použití standardní hlavičky `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="7db7e-142">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="7db7e-143">K zajištění zabezpečeného koncového připojení mezi klientem a serverem vždy použijte protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7db7e-143">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="7db7e-144">Mnohé webové servery protokolují adresu URL pro každý požadavek včetně řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="7db7e-144">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="7db7e-145">Protokolování adres URL může protokolovat přístupový token.</span><span class="sxs-lookup"><span data-stu-id="7db7e-145">Logging the URLs may log the access token.</span></span> <span data-ttu-id="7db7e-146">ASP.NET Core zaznamená adresu URL pro každý požadavek ve výchozím nastavení, což bude obsahovat řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="7db7e-146">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="7db7e-147">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7db7e-147">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="7db7e-148">Pokud máte obavy týkající se protokolování těchto dat do protokolů serveru, můžete toto protokolování zcela vypnout nakonfigurováním protokolovacího nástroje `Microsoft.AspNetCore.Hosting` na úroveň `Warning` nebo výše (tyto zprávy jsou napsány na úrovni `Info`).</span><span class="sxs-lookup"><span data-stu-id="7db7e-148">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="7db7e-149">Další informace najdete v tématu [filtrování protokolu](xref:fundamentals/logging/index#log-filtering) pro další informace.</span><span class="sxs-lookup"><span data-stu-id="7db7e-149">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="7db7e-150">Pokud pořád chcete protokolovat určité informace o žádostech, můžete [napsat middleware](xref:fundamentals/middleware/write) pro protokolování dat, která požadujete, a odfiltrovat `access_token` hodnotu řetězce dotazu (Pokud je k dispozici).</span><span class="sxs-lookup"><span data-stu-id="7db7e-150">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="7db7e-151">Výjimky</span><span class="sxs-lookup"><span data-stu-id="7db7e-151">Exceptions</span></span>

<span data-ttu-id="7db7e-152">Zprávy výjimek se obecně považují za citlivá data, která by neměla být odhalena klientovi.</span><span class="sxs-lookup"><span data-stu-id="7db7e-152">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="7db7e-153">Ve výchozím nastavení SignalR neposílá podrobnosti o výjimce vyvolané metodou centra klientovi.</span><span class="sxs-lookup"><span data-stu-id="7db7e-153">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="7db7e-154">Místo toho klient obdrží obecnou zprávu oznamující, že došlo k chybě.</span><span class="sxs-lookup"><span data-stu-id="7db7e-154">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="7db7e-155">Doručení zprávy výjimky klientovi lze přepsat (například při vývoji nebo testování) pomocí [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="7db7e-155">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="7db7e-156">Zprávy výjimek by neměly být vystaveny klientovi v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="7db7e-156">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="7db7e-157">Správa vyrovnávací paměti</span><span class="sxs-lookup"><span data-stu-id="7db7e-157">Buffer management</span></span>

SignalR<span data-ttu-id="7db7e-158"> používá pro správu příchozích a odchozích zpráv vyrovnávací paměti pro připojení.</span><span class="sxs-lookup"><span data-stu-id="7db7e-158"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="7db7e-159">Ve výchozím nastavení SignalR omezuje tyto vyrovnávací paměti na 32 KB.</span><span class="sxs-lookup"><span data-stu-id="7db7e-159">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="7db7e-160">Největší zpráva, kterou může klient nebo server odeslat, je 32 KB.</span><span class="sxs-lookup"><span data-stu-id="7db7e-160">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="7db7e-161">Maximální velikost paměti spotřebované připojením pro zprávy je 32 KB.</span><span class="sxs-lookup"><span data-stu-id="7db7e-161">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="7db7e-162">Pokud jsou vaše zprávy vždycky menší než 32 KB, můžete omezit limit, který:</span><span class="sxs-lookup"><span data-stu-id="7db7e-162">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="7db7e-163">Zabraňuje klientovi, aby mohl odeslat větší zprávu.</span><span class="sxs-lookup"><span data-stu-id="7db7e-163">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="7db7e-164">Server nikdy nebude muset přidělit velké vyrovnávací paměti pro příjem zpráv.</span><span class="sxs-lookup"><span data-stu-id="7db7e-164">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="7db7e-165">Pokud jsou vaše zprávy větší než 32 KB, můžete tento limit zvýšit.</span><span class="sxs-lookup"><span data-stu-id="7db7e-165">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="7db7e-166">Zvýšení tohoto limitu znamená:</span><span class="sxs-lookup"><span data-stu-id="7db7e-166">Increasing this limit means:</span></span>

* <span data-ttu-id="7db7e-167">Klient může způsobit přidělení rozsáhlých vyrovnávacích pamětí pro server.</span><span class="sxs-lookup"><span data-stu-id="7db7e-167">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="7db7e-168">Přidělení serveru pro velké vyrovnávací paměti může snížit počet souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="7db7e-168">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="7db7e-169">U příchozích a odchozích zpráv platí omezení, jak je možné nakonfigurovat u objektu [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) nakonfigurovaného v `MapHub`:</span><span class="sxs-lookup"><span data-stu-id="7db7e-169">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="7db7e-170">`ApplicationMaxBufferSize` představuje maximální počet bajtů od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="7db7e-170">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="7db7e-171">Pokud se klient pokusí odeslat zprávu větší, než je toto omezení, připojení může být zavřeno.</span><span class="sxs-lookup"><span data-stu-id="7db7e-171">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="7db7e-172">`TransportMaxBufferSize` představuje maximální počet bajtů, které může server odeslat.</span><span class="sxs-lookup"><span data-stu-id="7db7e-172">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="7db7e-173">Pokud se server pokusí odeslat zprávu (včetně návratových hodnot z metod z rozbočovače) větší, než je tento limit, bude vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="7db7e-173">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="7db7e-174">Nastavení limitu pro `0` zakáže limit.</span><span class="sxs-lookup"><span data-stu-id="7db7e-174">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="7db7e-175">Odebráním tohoto limitu můžete klientovi poslat zprávu libovolné velikosti.</span><span class="sxs-lookup"><span data-stu-id="7db7e-175">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="7db7e-176">Zlomyslní klienti odesílající velké zprávy můžou způsobit přidělení nadměrné paměti.</span><span class="sxs-lookup"><span data-stu-id="7db7e-176">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="7db7e-177">Nadměrné využití paměti může významně snížit počet souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="7db7e-177">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>

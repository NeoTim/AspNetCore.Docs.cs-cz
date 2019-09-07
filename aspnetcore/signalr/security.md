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
# <a name="security-considerations-in-aspnet-core-signalr"></a><span data-ttu-id="b18ea-103">Otázky zabezpečení v nástroji ASP.NET Core Signal</span><span class="sxs-lookup"><span data-stu-id="b18ea-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="b18ea-104">Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b18ea-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="b18ea-105">Tento článek poskytuje informace o zabezpečení signalizace.</span><span class="sxs-lookup"><span data-stu-id="b18ea-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="b18ea-106">Sdílení prostředků mezi zdroji</span><span class="sxs-lookup"><span data-stu-id="b18ea-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="b18ea-107">[Sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/) se dá použít k povolení připojení k signalizaci mezi zdroji v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="b18ea-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="b18ea-108">Pokud je JavaScriptový kód hostovaný v jiné doméně než aplikace pro signalizaci, musí být povolený [middleware CORS](xref:security/cors) , aby se JavaScript mohl připojit k aplikaci Signal.</span><span class="sxs-lookup"><span data-stu-id="b18ea-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="b18ea-109">Povoluje žádosti mezi zdroji jenom z domén, které důvěřujete nebo ovládáte.</span><span class="sxs-lookup"><span data-stu-id="b18ea-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="b18ea-110">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b18ea-110">For example:</span></span>

* <span data-ttu-id="b18ea-111">Váš web je hostovaný na`http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="b18ea-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="b18ea-112">Vaše aplikace Signal je hostovaná na`http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="b18ea-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="b18ea-113">V aplikaci signalizace by měla být nakonfigurovaná CORS, aby bylo možné `www.example.com`jenom původ.</span><span class="sxs-lookup"><span data-stu-id="b18ea-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="b18ea-114">Další informace o konfiguraci CORS najdete v tématu [povolení požadavků mezi zdroji (CORS)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="b18ea-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> <span data-ttu-id="b18ea-115">Signál **vyžaduje** následující zásady CORS:</span><span class="sxs-lookup"><span data-stu-id="b18ea-115">SignalR **requires** the following CORS policies:</span></span>

* <span data-ttu-id="b18ea-116">Povolí konkrétní očekávané zdroje.</span><span class="sxs-lookup"><span data-stu-id="b18ea-116">Allow the specific expected origins.</span></span> <span data-ttu-id="b18ea-117">Povolení libovolného zdroje je možné, ale **není zabezpečené nebo** Doporučené.</span><span class="sxs-lookup"><span data-stu-id="b18ea-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="b18ea-118">Metody `GET` http a `POST` musí být povoleny.</span><span class="sxs-lookup"><span data-stu-id="b18ea-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="b18ea-119">Přihlašovací údaje musí být povolené, i když se ověřování nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="b18ea-119">Credentials must be enabled, even when authentication is not used.</span></span>

<span data-ttu-id="b18ea-120">Následující zásada CORS například umožňuje klientovi prohlížeče signálu, `https://example.com` který je hostitelem aplikace, získat přístup k aplikaci Signal, která je hostována na: `https://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="b18ea-120">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

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
> <span data-ttu-id="b18ea-121">Návěstí není kompatibilní s integrovanou funkcí CORS v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="b18ea-121">SignalR is not compatible with the built-in CORS feature in Azure App Service.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="b18ea-122">Omezení původu protokolu WebSocket</span><span class="sxs-lookup"><span data-stu-id="b18ea-122">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b18ea-123">Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b18ea-123">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="b18ea-124">Pro omezení původu u WebSockets si přečtěte [omezení původu WebSockets](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="b18ea-124">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b18ea-125">Ochrany, které poskytuje CORS, se nevztahují na objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b18ea-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="b18ea-126">Prohlížeče **neobsahují:**</span><span class="sxs-lookup"><span data-stu-id="b18ea-126">Browsers do **not**:</span></span>

* <span data-ttu-id="b18ea-127">Provádění požadavků CORS v řádu CORS</span><span class="sxs-lookup"><span data-stu-id="b18ea-127">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="b18ea-128">Respektujte omezení zadaná v `Access-Control` hlavičkách při vytváření požadavků protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b18ea-128">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="b18ea-129">Prohlížeče ale při vydávání požadavků protokolu `Origin` WebSocket odesílají hlavičku.</span><span class="sxs-lookup"><span data-stu-id="b18ea-129">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="b18ea-130">Aplikace by měly být nakonfigurovány pro ověření těchto hlaviček, aby bylo zajištěno, že budou povoleny pouze objekty WebSockets přicházející z očekávaných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="b18ea-130">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="b18ea-131">V ASP.NET Core 2,1 a novějších lze ověření hlaviček dosáhnout pomocí vlastního middleware, který **je `UseSignalR`umístěn před a middleware ověřování** v: `Configure`</span><span class="sxs-lookup"><span data-stu-id="b18ea-131">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="b18ea-132">Záhlaví řídí klient a, `Referer` podobně jako záhlaví, může být falešným. `Origin`</span><span class="sxs-lookup"><span data-stu-id="b18ea-132">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="b18ea-133">Tato **záhlaví by se neměla používat** jako ověřovací mechanismus.</span><span class="sxs-lookup"><span data-stu-id="b18ea-133">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="access-token-logging"></a><span data-ttu-id="b18ea-134">Protokolování přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="b18ea-134">Access token logging</span></span>

<span data-ttu-id="b18ea-135">Při použití objektů WebSocket nebo událostí odesílaných serverem klientský prohlížeč odešle přístupový token do řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="b18ea-135">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="b18ea-136">Příjem přístupového tokenu prostřednictvím řetězce dotazu je obecně zabezpečený jako při použití `Authorization` standardního záhlaví.</span><span class="sxs-lookup"><span data-stu-id="b18ea-136">Receiving the access token via query string is generally as secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="b18ea-137">Protokol HTTPS byste měli vždycky používat k zajištění zabezpečeného koncového připojení mezi klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="b18ea-137">You should always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="b18ea-138">Mnohé webové servery protokolují adresu URL pro každý požadavek včetně řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="b18ea-138">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="b18ea-139">Protokolování adres URL může protokolovat přístupový token.</span><span class="sxs-lookup"><span data-stu-id="b18ea-139">Logging the URLs may log the access token.</span></span> <span data-ttu-id="b18ea-140">ASP.NET Core zaznamená adresu URL pro každý požadavek ve výchozím nastavení, což bude obsahovat řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="b18ea-140">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="b18ea-141">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b18ea-141">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="b18ea-142">Pokud máte obavy týkající se protokolování těchto dat do protokolů serveru, můžete toto protokolování zcela zakázat nakonfigurováním `Microsoft.AspNetCore.Hosting` protokolovacího nástroje `Warning` na úroveň nebo výše (tyto zprávy jsou zapsány na `Info` úrovni).</span><span class="sxs-lookup"><span data-stu-id="b18ea-142">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="b18ea-143">Další informace najdete v dokumentaci o [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) .</span><span class="sxs-lookup"><span data-stu-id="b18ea-143">See the documentation on [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="b18ea-144">Pokud přesto chcete protokolovat určité informace o žádostech, můžete [napsat middleware](xref:fundamentals/middleware/write) pro protokolování dat, která požadujete, a odfiltrovat hodnotu řetězce dotazu (Pokud je k `access_token` dispozici).</span><span class="sxs-lookup"><span data-stu-id="b18ea-144">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="b18ea-145">Výjimky</span><span class="sxs-lookup"><span data-stu-id="b18ea-145">Exceptions</span></span>

<span data-ttu-id="b18ea-146">Zprávy výjimek se obecně považují za citlivá data, která by neměla být odhalena klientovi.</span><span class="sxs-lookup"><span data-stu-id="b18ea-146">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="b18ea-147">Ve výchozím nastavení signál neodesílá podrobnosti o výjimce vyvolané metodou centra klientovi.</span><span class="sxs-lookup"><span data-stu-id="b18ea-147">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="b18ea-148">Místo toho klient obdrží obecnou zprávu oznamující, že došlo k chybě.</span><span class="sxs-lookup"><span data-stu-id="b18ea-148">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="b18ea-149">Doručení zprávy o výjimce klientovi lze přepsat (například při vývoji nebo testování) pomocí [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="b18ea-149">Exception message delivery to the client can be overridden (for example in development or test) with [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="b18ea-150">Zprávy výjimek by neměly být vystaveny klientovi v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="b18ea-150">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="b18ea-151">Správa vyrovnávací paměti</span><span class="sxs-lookup"><span data-stu-id="b18ea-151">Buffer management</span></span>

<span data-ttu-id="b18ea-152">Signalizace používá pro správu příchozích a odchozích zpráv vyrovnávací paměti vázané na připojení.</span><span class="sxs-lookup"><span data-stu-id="b18ea-152">SignalR uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="b18ea-153">Ve výchozím nastavení signál omezuje tyto vyrovnávací paměti na 32 KB.</span><span class="sxs-lookup"><span data-stu-id="b18ea-153">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="b18ea-154">Největší zpráva, kterou může klient nebo server odeslat, je 32 KB.</span><span class="sxs-lookup"><span data-stu-id="b18ea-154">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="b18ea-155">Maximální velikost paměti spotřebované připojením pro zprávy je 32 KB.</span><span class="sxs-lookup"><span data-stu-id="b18ea-155">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="b18ea-156">Pokud jsou vaše zprávy vždycky menší než 32 KB, můžete omezit limit, který:</span><span class="sxs-lookup"><span data-stu-id="b18ea-156">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="b18ea-157">Zabraňuje klientovi, aby mohl odeslat větší zprávu.</span><span class="sxs-lookup"><span data-stu-id="b18ea-157">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="b18ea-158">Server nikdy nebude muset přidělit velké vyrovnávací paměti pro příjem zpráv.</span><span class="sxs-lookup"><span data-stu-id="b18ea-158">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="b18ea-159">Pokud jsou vaše zprávy větší než 32 KB, můžete tento limit zvýšit.</span><span class="sxs-lookup"><span data-stu-id="b18ea-159">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="b18ea-160">Zvýšení tohoto limitu znamená:</span><span class="sxs-lookup"><span data-stu-id="b18ea-160">Increasing this limit means:</span></span>

* <span data-ttu-id="b18ea-161">Klient může způsobit přidělení rozsáhlých vyrovnávacích pamětí pro server.</span><span class="sxs-lookup"><span data-stu-id="b18ea-161">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="b18ea-162">Přidělení serveru pro velké vyrovnávací paměti může snížit počet souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="b18ea-162">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="b18ea-163">U příchozích a odchozích zpráv platí omezení, jak je možné nakonfigurovat u [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) objektu nakonfigurovaného v: `MapHub`</span><span class="sxs-lookup"><span data-stu-id="b18ea-163">There are limits for incoming and outgoing messages, both can be configured on the [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="b18ea-164">`ApplicationMaxBufferSize`představuje maximální počet bajtů od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="b18ea-164">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="b18ea-165">Pokud se klient pokusí odeslat zprávu větší, než je toto omezení, připojení může být zavřeno.</span><span class="sxs-lookup"><span data-stu-id="b18ea-165">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="b18ea-166">`TransportMaxBufferSize`představuje maximální počet bajtů, které může server odeslat.</span><span class="sxs-lookup"><span data-stu-id="b18ea-166">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="b18ea-167">Pokud se server pokusí odeslat zprávu (včetně návratových hodnot z metod z rozbočovače) větší, než je tento limit, bude vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="b18ea-167">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="b18ea-168">Nastavením limitu `0` zakážete tento limit.</span><span class="sxs-lookup"><span data-stu-id="b18ea-168">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="b18ea-169">Odebráním tohoto limitu můžete klientovi poslat zprávu libovolné velikosti.</span><span class="sxs-lookup"><span data-stu-id="b18ea-169">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="b18ea-170">Zlomyslní klienti odesílající velké zprávy můžou způsobit přidělení nadměrné paměti.</span><span class="sxs-lookup"><span data-stu-id="b18ea-170">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="b18ea-171">Nadměrné využití paměti může významně snížit počet souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="b18ea-171">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>

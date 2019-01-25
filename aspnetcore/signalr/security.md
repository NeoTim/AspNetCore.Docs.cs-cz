---
title: Informace o zabezpečení ve funkci SignalR technologie ASP.NET Core
author: bradygaster
description: Další informace o použití ověřování a autorizace v knihovně SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/security
ms.openlocfilehash: 52cfac6be8e61572acdf0b19dab574b607314d97
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836061"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a><span data-ttu-id="854af-103">Informace o zabezpečení ve funkci SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="854af-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="854af-104">Podle [Andrew Stanton sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="854af-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="854af-105">Tento článek obsahuje informace o zabezpečení knihovnou SignalR.</span><span class="sxs-lookup"><span data-stu-id="854af-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="854af-106">Sdílení prostředků různého původu</span><span class="sxs-lookup"><span data-stu-id="854af-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="854af-107">[Prostředků mezi zdroji (CORS) pro sdílení obsahu](https://www.w3.org/TR/cors/) slouží k povolení připojení SignalR nepůvodního zdroje v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="854af-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="854af-108">Pokud kód jazyka JavaScript je hostované v jiné doméně aplikace SignalR [middlewarem CORS](xref:security/cors) musí lze povolit JavaScript, který chcete připojit k aplikaci SignalR.</span><span class="sxs-lookup"><span data-stu-id="854af-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="854af-109">Povolení žádostí nepůvodního pouze z domén, které důvěřujete nebo ovládacího prvku.</span><span class="sxs-lookup"><span data-stu-id="854af-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="854af-110">Příklad:</span><span class="sxs-lookup"><span data-stu-id="854af-110">For example:</span></span>

* <span data-ttu-id="854af-111">Je hostitelem vašeho webu `http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="854af-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="854af-112">Je hostitelem vaší aplikace SignalR `http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="854af-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="854af-113">CORS by měl být nakonfigurovaný v SignalR aplikaci a Povolit jenom původ `www.example.com`.</span><span class="sxs-lookup"><span data-stu-id="854af-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="854af-114">Další informace o konfiguraci CORS, najdete v části [povolení prostředků různého původů (CORS)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="854af-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> <span data-ttu-id="854af-115">Funkce SignalR **vyžaduje** následující zásady CORS:</span><span class="sxs-lookup"><span data-stu-id="854af-115">SignalR **requires** the following CORS policies:</span></span>

* <span data-ttu-id="854af-116">Povolte konkrétní očekávané zdroje.</span><span class="sxs-lookup"><span data-stu-id="854af-116">Allow the specific expected origins.</span></span> <span data-ttu-id="854af-117">Povolení jakýkoli původ je možné, ale je **není** zabezpečení nebo doporučené.</span><span class="sxs-lookup"><span data-stu-id="854af-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="854af-118">Metody HTTP `GET` a `POST` musí být povoleno.</span><span class="sxs-lookup"><span data-stu-id="854af-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="854af-119">Přihlašovací údaje musí být povolena, i když se ověřování nepoužívá.</span><span class="sxs-lookup"><span data-stu-id="854af-119">Credentials must be enabled, even when authentication is not used.</span></span>

<span data-ttu-id="854af-120">Například následující zásadu CORS umožňuje klientovi SignalR prohlížeče hostované na `https://example.com` přístup k aplikaci SignalR hostitelem `https://signalr.example.com`:</span><span class="sxs-lookup"><span data-stu-id="854af-120">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="854af-121">SignalR je nekompatibilní s integrovaná funkce CORS v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="854af-121">SignalR is not compatible with the built-in CORS feature in Azure App Service.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="854af-122">Omezení objektu websocket na straně zdroje</span><span class="sxs-lookup"><span data-stu-id="854af-122">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="854af-123">Poskytovanou CORS se nevztahují na objekty Websocket.</span><span class="sxs-lookup"><span data-stu-id="854af-123">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="854af-124">Původní omezení na protokoly Websocket, najdete v článku [objekty Websocket původu omezení](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="854af-124">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="854af-125">Poskytovanou CORS se nevztahují na objekty Websocket.</span><span class="sxs-lookup"><span data-stu-id="854af-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="854af-126">Prohlížeče **není**:</span><span class="sxs-lookup"><span data-stu-id="854af-126">Browsers do **not**:</span></span>

* <span data-ttu-id="854af-127">Provedení přípravné požadavků CORS.</span><span class="sxs-lookup"><span data-stu-id="854af-127">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="854af-128">Respektujeme zadaná v omezení `Access-Control` záhlaví při provádění požadavků protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="854af-128">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="854af-129">Ale prohlížeče odesílají `Origin` záhlaví při vydávání žádostí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="854af-129">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="854af-130">Aplikace musí být nakonfigurovaný k ověření tyto hlavičky k zajištění, že jsou povoleny pouze objekty Websocket očekávané původ, odkud pocházejí.</span><span class="sxs-lookup"><span data-stu-id="854af-130">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="854af-131">V ASP.NET Core 2.1 nebo novější, hlavičky ověření lze dosáhnout pomocí vlastního middlewaru umístit **před `UseSignalR`a ověřovací middleware** v `Configure`:</span><span class="sxs-lookup"><span data-stu-id="854af-131">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="854af-132">`Origin` Záhlaví je řízena klientem a stejně jako `Referer` záhlaví, můžete zfalšovaná.</span><span class="sxs-lookup"><span data-stu-id="854af-132">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="854af-133">By měla tato záhlaví **není** používat jako mechanismus ověřování.</span><span class="sxs-lookup"><span data-stu-id="854af-133">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="access-token-logging"></a><span data-ttu-id="854af-134">Protokolování token přístupu</span><span class="sxs-lookup"><span data-stu-id="854af-134">Access token logging</span></span>

<span data-ttu-id="854af-135">Při použití Server-Sent události nebo protokoly Websocket, klientský prohlížeč odesílá přístupový token v řetězci dotazu.</span><span class="sxs-lookup"><span data-stu-id="854af-135">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="854af-136">Přijetí přístupový token pomocí řetězce dotazu, je obecně stejně bezpečné jako použití standardní `Authorization` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="854af-136">Receiving the access token via query string is generally as secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="854af-137">Používejte vždy HTTPS k zajištění zabezpečené připojení mezi klientem a serverem začátku do konce.</span><span class="sxs-lookup"><span data-stu-id="854af-137">You should always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="854af-138">Mnoho webových serverů protokolu adresu URL pro každý požadavek, včetně řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="854af-138">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="854af-139">Protokolování adresy URL může protokolovat přístupový token.</span><span class="sxs-lookup"><span data-stu-id="854af-139">Logging the URLs may log the access token.</span></span> <span data-ttu-id="854af-140">ASP.NET Core protokoly ve výchozím nastavení, která bude obsahovat řetězec dotazu adresy URL pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="854af-140">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="854af-141">Příklad:</span><span class="sxs-lookup"><span data-stu-id="854af-141">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="854af-142">Pokud máte obavy o protokolování tato data pomocí protokolů serveru, můžete zakázat protokolování zcela podle konfigurace `Microsoft.AspNetCore.Hosting` protokolovacího nástroje k `Warning` úroveň nebo novější (tyto zprávy se zapisují na `Info` úroveň).</span><span class="sxs-lookup"><span data-stu-id="854af-142">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="854af-143">Naleznete v dokumentaci [filtrování protokolu](xref:fundamentals/logging/index#log-filtering) Další informace.</span><span class="sxs-lookup"><span data-stu-id="854af-143">See the documentation on [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="854af-144">Pokud stále chcete protokolovat určité informace o žádostech, můžete [zápisu middleware](xref:fundamentals/middleware/index#write-middleware) protokolování dat vyžadují a filtrování `access_token` dotazování řetězcovou hodnotu (pokud existuje).</span><span class="sxs-lookup"><span data-stu-id="854af-144">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/index#write-middleware) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="854af-145">Výjimky</span><span class="sxs-lookup"><span data-stu-id="854af-145">Exceptions</span></span>

<span data-ttu-id="854af-146">Zprávy o výjimkách jsou obvykle považovány za citlivá data, která by neměla být odhalena do klienta.</span><span class="sxs-lookup"><span data-stu-id="854af-146">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="854af-147">Ve výchozím nastavení nebude funkce SignalR Odeslat podrobnosti výjimky vyvolané metodou rozbočovače klientovi.</span><span class="sxs-lookup"><span data-stu-id="854af-147">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="854af-148">Místo toho klient obdrží obecná zpráva oznamující, že došlo k chybě.</span><span class="sxs-lookup"><span data-stu-id="854af-148">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="854af-149">Výjimka doručení zpráv do klienta monitorconfigurationoverride lze přepsat (např. vývojové nebo testovací) [ `EnableDetailedErrors` ](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="854af-149">Exception message delivery to the client can be overridden (for example in development or test) with [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="854af-150">Zprávy o výjimkách by neměly být vystaveny klientům v produkčních aplikacích.</span><span class="sxs-lookup"><span data-stu-id="854af-150">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="854af-151">Správa vyrovnávací paměti</span><span class="sxs-lookup"><span data-stu-id="854af-151">Buffer management</span></span>

<span data-ttu-id="854af-152">SignalR na připojení vyrovnávací paměti používá ke správě příchozí a odchozí zprávy.</span><span class="sxs-lookup"><span data-stu-id="854af-152">SignalR uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="854af-153">Ve výchozím nastavení omezuje SignalR vyrovnávací paměti na 32 KB.</span><span class="sxs-lookup"><span data-stu-id="854af-153">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="854af-154">Největší zprávu, kterou můžete poslat klient nebo server je 32 KB.</span><span class="sxs-lookup"><span data-stu-id="854af-154">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="854af-155">Maximální velikost paměti spotřebované na připojení pro zprávy je 32 KB.</span><span class="sxs-lookup"><span data-stu-id="854af-155">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="854af-156">Pokud vaše zprávy jsou vždy menší než 32 KB, můžete zkrátit limit, který:</span><span class="sxs-lookup"><span data-stu-id="854af-156">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="854af-157">Brání klientovi tomu nebudou moct odeslat zprávu větší.</span><span class="sxs-lookup"><span data-stu-id="854af-157">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="854af-158">Server nikdy muset přidělit velké vyrovnávací paměti pro příjem zpráv.</span><span class="sxs-lookup"><span data-stu-id="854af-158">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="854af-159">Pokud vaše zprávy jsou větší než 32 KB, můžete zvýšit limit.</span><span class="sxs-lookup"><span data-stu-id="854af-159">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="854af-160">Zvýšit tento limit znamená, že:</span><span class="sxs-lookup"><span data-stu-id="854af-160">Increasing this limit means:</span></span>

* <span data-ttu-id="854af-161">Klient může způsobit server k přidělení vyrovnávací paměti velké paměti.</span><span class="sxs-lookup"><span data-stu-id="854af-161">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="854af-162">Server přidělení velké vyrovnávací paměti může snížit počet souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="854af-162">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="854af-163">Existují omezení pro příchozí a odchozí zprávy, jak lze nakonfigurovat podle [ `HttpConnectionDispatcherOptions` ](xref:signalr/configuration#configure-server-options) objekt gurovaný `MapHub`:</span><span class="sxs-lookup"><span data-stu-id="854af-163">There are limits for incoming and outgoing messages, both can be configured on the [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="854af-164">`ApplicationMaxBufferSize` představuje maximální počet bajtů z klienta, který vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="854af-164">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="854af-165">Pokud se klient pokusí odeslat zprávu větší než tento limit, může připojení ukončeno.</span><span class="sxs-lookup"><span data-stu-id="854af-165">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="854af-166">`TransportMaxBufferSize` představuje maximální počet bajtů, které může server odeslat.</span><span class="sxs-lookup"><span data-stu-id="854af-166">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="854af-167">Pokud se server pokusí odeslat zprávu (včetně návratové hodnoty metod rozbočovače na) větší než tento limit, bude vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="854af-167">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="854af-168">Nastavení limitu `0` zakáže limit.</span><span class="sxs-lookup"><span data-stu-id="854af-168">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="854af-169">Odebrání limitu umožňuje klientovi umožní odeslat zprávu libovolné velikosti.</span><span class="sxs-lookup"><span data-stu-id="854af-169">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="854af-170">Klientů zasílání velkých zpráv může způsobit nadbytek paměti mají být přiděleny.</span><span class="sxs-lookup"><span data-stu-id="854af-170">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="854af-171">Využití nadbytek paměti může výrazně snížit počet souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="854af-171">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>

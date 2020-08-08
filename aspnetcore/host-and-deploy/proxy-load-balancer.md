---
title: Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení
author: rick-anderson
description: Přečtěte si o konfiguraci pro aplikace hostované za servery proxy a nástroje pro vyrovnávání zatížení, které často překrývají důležité informace o žádostech.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 75bb92908525d18af57f408e8ebba53c6eec88b2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015514"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="a84e7-103">Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="a84e7-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="a84e7-104">Autor – [Chris Rossův](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="a84e7-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a84e7-105">V Doporučené konfiguraci pro ASP.NET Core se aplikace hostuje pomocí IIS/ASP. Modul NET Core, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="a84e7-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="a84e7-106">Proxy servery, nástroje pro vyrovnávání zatížení a další síťová zařízení často překrývají informace o žádosti předtím, než dosáhnou aplikace:</span><span class="sxs-lookup"><span data-stu-id="a84e7-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="a84e7-107">Pokud jsou požadavky HTTPS proxy serverem přes HTTP, původní schéma (HTTPS) se ztratí a musí se přenášet v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="a84e7-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="a84e7-108">Vzhledem k tomu, že aplikace obdrží požadavek od proxy serveru a ne jeho skutečný zdroj v Internetu nebo podnikové síti, musí být zdrojová adresa IP klienta předána také v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="a84e7-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="a84e7-109">Tyto informace můžou být důležité ve zpracování žádostí, například v přesměrování, ověřování, generování odkazů, vyhodnocení zásad a geografická poloha klienta.</span><span class="sxs-lookup"><span data-stu-id="a84e7-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="a84e7-110">Předávaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="a84e7-110">Forwarded headers</span></span>

<span data-ttu-id="a84e7-111">Podle konvence proxy předávají informace v hlavičkách protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a84e7-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="a84e7-112">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="a84e7-112">Header</span></span> | <span data-ttu-id="a84e7-113">Popis</span><span class="sxs-lookup"><span data-stu-id="a84e7-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="a84e7-114">X-předané – pro</span><span class="sxs-lookup"><span data-stu-id="a84e7-114">X-Forwarded-For</span></span> | <span data-ttu-id="a84e7-115">Uchovává informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="a84e7-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="a84e7-116">Tento parametr může obsahovat IP adresy (a volitelně také čísla portů).</span><span class="sxs-lookup"><span data-stu-id="a84e7-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="a84e7-117">V řetězci proxy serverů označuje první parametr klienta, ve kterém se žádost poprvé nastavila.</span><span class="sxs-lookup"><span data-stu-id="a84e7-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="a84e7-118">Následující identifikátory proxy následují.</span><span class="sxs-lookup"><span data-stu-id="a84e7-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="a84e7-119">Poslední proxy server v řetězci není uveden v seznamu parametrů.</span><span class="sxs-lookup"><span data-stu-id="a84e7-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="a84e7-120">Poslední IP adresa proxy serveru a volitelně je číslo portu, které je k dispozici jako Vzdálená IP adresa v transportní vrstvě.</span><span class="sxs-lookup"><span data-stu-id="a84e7-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="a84e7-121">X-předáno – proto</span><span class="sxs-lookup"><span data-stu-id="a84e7-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="a84e7-122">Hodnota původního schématu (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a84e7-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="a84e7-123">Hodnota může být také seznamem schémat, pokud je požadavek procházen více proxy.</span><span class="sxs-lookup"><span data-stu-id="a84e7-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="a84e7-124">X-předávaný-Host</span><span class="sxs-lookup"><span data-stu-id="a84e7-124">X-Forwarded-Host</span></span> | <span data-ttu-id="a84e7-125">Původní hodnota pole hlavičky hostitele</span><span class="sxs-lookup"><span data-stu-id="a84e7-125">The original value of the Host header field.</span></span> <span data-ttu-id="a84e7-126">Servery proxy většinou nemění hlavičku hostitele.</span><span class="sxs-lookup"><span data-stu-id="a84e7-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="a84e7-127">Viz informační [zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pro informace o chybě zabezpečení zvýšení oprávnění, která má vliv na systémy, ve kterých server proxy neověřuje nebo omezuje hlavičky hostitele na známé správné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a84e7-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="a84e7-128">Middleware předávaných hlaviček z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a vyplní související pole v <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="a84e7-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="a84e7-129">Aktualizace middlewaru:</span><span class="sxs-lookup"><span data-stu-id="a84e7-129">The middleware updates:</span></span>

* <span data-ttu-id="a84e7-130">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): nastavte pomocí `X-Forwarded-For` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="a84e7-131">Další nastavení mají vliv na to, jak middleware nastaví `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="a84e7-132">Podrobnosti najdete v tématu [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="a84e7-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="a84e7-133">[HttpContext. Request. schéma](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): nastavte pomocí `X-Forwarded-Proto` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="a84e7-134">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): nastavte pomocí `X-Forwarded-Host` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="a84e7-135">Je možné nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru u předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a84e7-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="a84e7-136">Výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="a84e7-136">The default settings are:</span></span>

* <span data-ttu-id="a84e7-137">Mezi aplikací a zdrojem požadavků je jenom *jeden proxy server* .</span><span class="sxs-lookup"><span data-stu-id="a84e7-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="a84e7-138">Pro známé proxy servery a známé sítě jsou nakonfigurovány pouze adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="a84e7-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="a84e7-139">Předané hlavičky jsou pojmenovány `X-Forwarded-For` a `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="a84e7-140">Ne všechna síťová zařízení přidávají `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a84e7-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="a84e7-141">Zeptejte se na pokyny výrobce zařízení, jestli proxy požadavky při přístupu do aplikace tyto hlavičky neobsahují.</span><span class="sxs-lookup"><span data-stu-id="a84e7-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="a84e7-142">Pokud zařízení používá jiné názvy hlaviček než `X-Forwarded-For` a `X-Forwarded-Proto` , nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným zařízením.</span><span class="sxs-lookup"><span data-stu-id="a84e7-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="a84e7-143">Další informace najdete v tématu [předané možnosti a konfigurace middlewarových hlaviček](#forwarded-headers-middleware-options) [pro proxy server, který používá jiné názvy hlaviček](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="a84e7-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="a84e7-144">Modul IIS/IIS Express a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a84e7-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="a84e7-145">Middleware předávaných hlaviček je ve výchozím nastavení povolená službou [IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , když je aplikace hostovaná [mimo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) službu IIS a modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a84e7-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="a84e7-146">Middleware předávaných hlaviček se aktivují, aby se nejdříve spouštěl v kanálu middleware s omezenou konfigurací určenou pro modul ASP.NET Core, protože se jedná o obavy týkající se důvěryhodnosti se předanými hlavičkami (například [falšování IP adres](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="a84e7-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="a84e7-147">Middleware je nakonfigurovaná pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a a je omezená na jeden localhost proxy.</span><span class="sxs-lookup"><span data-stu-id="a84e7-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="a84e7-148">Pokud se vyžaduje další konfigurace, přečtěte si téma [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="a84e7-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a84e7-149">Další scénáře proxy server a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="a84e7-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a84e7-150">Bez použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimo procesy](xref:host-and-deploy/iis/index#out-of-process-hosting-model)není middleware předávaných hlaviček ve výchozím nastavení povolená.</span><span class="sxs-lookup"><span data-stu-id="a84e7-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="a84e7-151">Middleware předaných hlaviček musí být povolená, aby aplikace zpracovala předávané hlavičky s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="a84e7-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="a84e7-152">Po povolení middlewaru, pokud není <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zadáno pro middleware, výchozí [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a84e7-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="a84e7-153">Nakonfigurujte middleware s nástrojem <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> na předáte `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="a84e7-154">Pořadí middlewaru u předávaných hlaviček</span><span class="sxs-lookup"><span data-stu-id="a84e7-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="a84e7-155">Middleware předaných hlaviček by se měla spustit před jiným middlewarem.</span><span class="sxs-lookup"><span data-stu-id="a84e7-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="a84e7-156">Toto řazení zajišťuje, aby middleware spoléhající se na předané informace hlaviček mohl spotřebovat hodnoty hlaviček pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="a84e7-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="a84e7-157">Middleware předávaných hlaviček se dá spustit po diagnostice a zpracování chyb, ale musí se spustit před voláním `UseHsts` :</span><span class="sxs-lookup"><span data-stu-id="a84e7-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="a84e7-158">Nebo volejte `UseForwardedHeaders` před diagnostikou:</span><span class="sxs-lookup"><span data-stu-id="a84e7-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="a84e7-159">Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` nebo přímo k metodě rozšíření s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , výchozí hlavičky pro přeposílání jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a84e7-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="a84e7-160"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Vlastnost musí být nakonfigurována s hlavičkou pro přeposílání.</span><span class="sxs-lookup"><span data-stu-id="a84e7-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="a84e7-161">Konfigurace nginx</span><span class="sxs-lookup"><span data-stu-id="a84e7-161">Nginx configuration</span></span>

<span data-ttu-id="a84e7-162">Pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a naleznete informace v tématu <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="a84e7-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="a84e7-163">Další informace najdete v tématu [Nginx: použití předané hlavičky](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="a84e7-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="a84e7-164">Konfigurace Apache</span><span class="sxs-lookup"><span data-stu-id="a84e7-164">Apache configuration</span></span>

<span data-ttu-id="a84e7-165">`X-Forwarded-For`se přidá automaticky (viz část [modulu Apache mod_proxy: hlavičky žádosti reverzního proxy serveru](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="a84e7-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="a84e7-166">Informace o tom, jak přeslat `X-Forwarded-Proto` hlavičku, najdete v tématu <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="a84e7-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="a84e7-167">Možnosti middlewaru předávaných hlaviček</span><span class="sxs-lookup"><span data-stu-id="a84e7-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="a84e7-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>řízení chování middlewaru předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a84e7-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="a84e7-169">Následující příklad změní výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="a84e7-169">The following example changes the default values:</span></span>

* <span data-ttu-id="a84e7-170">Omezte počet záznamů v předaných hlavičkách na `2` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="a84e7-171">Přidejte známou adresu proxy serveru `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="a84e7-172">Změňte název předané hlavičky z výchozí `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="a84e7-173">Možnost</span><span class="sxs-lookup"><span data-stu-id="a84e7-173">Option</span></span> | <span data-ttu-id="a84e7-174">Popis</span><span class="sxs-lookup"><span data-stu-id="a84e7-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="a84e7-175">Omezí hostitele v `X-Forwarded-Host` hlavičce na zadané hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a84e7-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="a84e7-176">Hodnoty se porovnávají pomocí pořadí – ignorovat – případ.</span><span class="sxs-lookup"><span data-stu-id="a84e7-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="a84e7-177">Čísla portů musí být vyloučena.</span><span class="sxs-lookup"><span data-stu-id="a84e7-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="a84e7-178">Pokud je seznam prázdný, jsou povoleny všichni hostitelé.</span><span class="sxs-lookup"><span data-stu-id="a84e7-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="a84e7-179">Zástupný znak nejvyšší úrovně `*` umožňuje všem neprázdným hostitelům.</span><span class="sxs-lookup"><span data-stu-id="a84e7-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="a84e7-180">Zástupné znaky subdomény jsou povolené, ale neodpovídají kořenové doméně.</span><span class="sxs-lookup"><span data-stu-id="a84e7-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="a84e7-181">Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale nikoli ke kořenové doméně `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="a84e7-182">Názvy hostitelů Unicode jsou povoleny, ale jsou převedeny na [Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</span><span class="sxs-lookup"><span data-stu-id="a84e7-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="a84e7-183">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí zahrnovat ohraničující závorky a musí být v [konvenční formě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="a84e7-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="a84e7-184">IPv6 adresy nejsou speciální – použita pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádné kanonikalizace.</span><span class="sxs-lookup"><span data-stu-id="a84e7-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="a84e7-185">Nepodaří-li se omezit povolené hostitele, může útočníkovi umožnit přístup k falešným odkazům generovanými službou.</span><span class="sxs-lookup"><span data-stu-id="a84e7-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="a84e7-186">Výchozí hodnota je prázdná `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="a84e7-187">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="a84e7-188">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-For` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a84e7-189">Výchozí formát je `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="a84e7-190">Určuje, které předávacíci by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="a84e7-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="a84e7-191">Seznam polí, která platí, najdete v tématu [ForwardedHeaders enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="a84e7-192">Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="a84e7-193">Výchozí hodnota je [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a84e7-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="a84e7-194">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="a84e7-195">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Host` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a84e7-196">Výchozí formát je `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="a84e7-197">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="a84e7-198">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Proto` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a84e7-199">Výchozí formát je `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="a84e7-200">Omezuje počet položek v záhlavích, které jsou zpracovávány.</span><span class="sxs-lookup"><span data-stu-id="a84e7-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="a84e7-201">Nastavením `null` tohoto omezení zakážete, ale to by mělo být provedeno pouze v případě, že `KnownProxies` nebo `KnownNetworks` jsou nakonfigurovány.</span><span class="sxs-lookup"><span data-stu-id="a84e7-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="a84e7-202">Nastavení `null` nehodnoty je preventivní opatrnost (ale ne záruka) pro ochranu proti nesprávně konfigurovaným proxy a škodlivým požadavkům přicházejících ze dvoustranných kanálů v síti.</span><span class="sxs-lookup"><span data-stu-id="a84e7-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="a84e7-203">Middleware předávaných hlaviček zpracovává hlavičky v obráceném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="a84e7-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a84e7-204">Pokud je použita výchozí hodnota ( `1` ), bude zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="a84e7-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="a84e7-205">Výchozí formát je `1`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="a84e7-206">Rozsahy adres známých sítí, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="a84e7-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="a84e7-207">Zadejte rozsahy IP adres pomocí zápisu CIDR (Classless prosměrování mezi doménami).</span><span class="sxs-lookup"><span data-stu-id="a84e7-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="a84e7-208">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="a84e7-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a84e7-209">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a84e7-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a84e7-210">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a84e7-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a84e7-211">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-211">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a84e7-212">Výchozí hodnota je `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> obsahující jednu položku pro `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="a84e7-213">Adresy známých proxy adres, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="a84e7-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="a84e7-214">Použijte `KnownProxies` k určení přesné shody IP adres.</span><span class="sxs-lookup"><span data-stu-id="a84e7-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="a84e7-215">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="a84e7-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a84e7-216">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a84e7-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a84e7-217">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a84e7-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a84e7-218">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-218">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a84e7-219">Výchozí hodnota je `IList` \<<xref:System.Net.IPAddress>> obsahující jednu položku pro `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-219">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="a84e7-220">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="a84e7-221">Výchozí formát je `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-221">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="a84e7-222">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="a84e7-223">Výchozí formát je `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-223">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="a84e7-224">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-224">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="a84e7-225">Výchozí formát je `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-225">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="a84e7-226">Vyžaduje, aby byl počet synchronizovaných hodnot hlaviček mezi zpracovávaným [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-226">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="a84e7-227">Výchozí hodnota v ASP.NET Core 1. x je `true` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-227">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="a84e7-228">Výchozí hodnota v ASP.NET Core 2,0 nebo novější je `false` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-228">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="a84e7-229">Scénáře a případy použití</span><span class="sxs-lookup"><span data-stu-id="a84e7-229">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="a84e7-230">Pokud není možné přidat předávané hlavičky a všechny požadavky jsou zabezpečené</span><span class="sxs-lookup"><span data-stu-id="a84e7-230">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="a84e7-231">V některých případech nemusí být možné přidat předávaná záhlaví do požadavků, které jsou proxy k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a84e7-231">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="a84e7-232">Pokud proxy vynucuje, že všechny veřejné externí požadavky jsou HTTPS, můžete schéma ručně nastavit v části `Startup.Configure` před použitím libovolného typu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="a84e7-232">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="a84e7-233">Tento kód může být zakázán pomocí proměnné prostředí nebo jiného nastavení konfigurace ve vývojovém nebo přípravném prostředí.</span><span class="sxs-lookup"><span data-stu-id="a84e7-233">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="a84e7-234">Práce se základními a proxy servery, které mění cestu požadavku</span><span class="sxs-lookup"><span data-stu-id="a84e7-234">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="a84e7-235">Některé proxy servery předávají cestu beze změny, ale se základní cestou aplikace, která by se měla odebrat, aby směrování fungovalo správně.</span><span class="sxs-lookup"><span data-stu-id="a84e7-235">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="a84e7-236">Middleware [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware rozdělí cestu do [HttpRequest. cesta](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace do [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="a84e7-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="a84e7-237">Pokud `/foo` je základní cesta aplikace pro cestu k proxy serveru předaná jako `/foo/api/1` , middleware nastaví `Request.PathBase` na `/foo` a `Request.Path` do `/api/1` pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="a84e7-237">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="a84e7-238">Původní cesta a základ cesty se znovu aplikují, když se middleware znovu zavolá v opačném případě.</span><span class="sxs-lookup"><span data-stu-id="a84e7-238">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="a84e7-239">Další informace o zpracování pořadí middlewaru najdete v tématu <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="a84e7-239">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="a84e7-240">Pokud proxy ořízne cestu (například předávání `/foo/api/1` na `/api/1` ), opravte přesměrování a odkazy nastavením vlastnosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) žádosti:</span><span class="sxs-lookup"><span data-stu-id="a84e7-240">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="a84e7-241">Pokud proxy přidává data cest, zahodí část cesty pro opravu přesměrování a odkazy pomocí <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> a přiřazení k <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a84e7-241">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="a84e7-242">Konfigurace proxy serveru, který používá jiné názvy hlaviček</span><span class="sxs-lookup"><span data-stu-id="a84e7-242">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="a84e7-243">Pokud proxy nepoužívá hlavičky s názvem `X-Forwarded-For` a `X-Forwarded-Proto` k přeposlání informací o adrese proxy/portu a informace o původním schématu, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným proxy serverem:</span><span class="sxs-lookup"><span data-stu-id="a84e7-243">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="a84e7-244">Konfigurace pro adresu IPv4 reprezentovanou jako IPv6 adresa</span><span class="sxs-lookup"><span data-stu-id="a84e7-244">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="a84e7-245">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaného v protokolu IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="a84e7-245">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="a84e7-246">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a84e7-246">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a84e7-247">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a84e7-247">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="a84e7-248">V následujícím příkladu se síťová adresa, která poskytuje předávaná záhlaví, přidá do `KnownNetworks` seznamu ve formátu protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="a84e7-248">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="a84e7-249">Adresa IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="a84e7-249">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="a84e7-250">Převedená adresa IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="a84e7-250">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="a84e7-251">Délka převedené předpony: 104</span><span class="sxs-lookup"><span data-stu-id="a84e7-251">Converted prefix length: 104</span></span>

<span data-ttu-id="a84e7-252">Adresu můžete také uvést v šestnáctkovém formátu ( `10.11.12.1` reprezentované protokolem IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="a84e7-252">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="a84e7-253">Při převodu adresy IPv4 na IPv6 přidejte 96 k délce předpony CIDR ( `8` v příkladu), aby se zohlednila další `::ffff:` předpona IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="a84e7-253">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="a84e7-254">Předejte schéma pro reverzní proxy servery se systémy Linux a non-IIS.</span><span class="sxs-lookup"><span data-stu-id="a84e7-254">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="a84e7-255">Aplikace, které volají <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> a <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umísťují lokalitu do nekonečné smyčky, pokud jsou nasazené do azure Linux App Service, virtuální počítač Azure Linux (VM) nebo za jakýkoli jiný reverzní proxy server, kromě služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a84e7-255">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="a84e7-256">Protokol TLS je ukončen reverzním proxy serverem a Kestrel není informován o správném schématu požadavků.</span><span class="sxs-lookup"><span data-stu-id="a84e7-256">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="a84e7-257">OAuth a OIDC v této konfiguraci selže, protože generují nesprávná přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a84e7-257">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="a84e7-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Přidá a nakonfiguruje middleware předávaných hlaviček při spuštění za IIS, ale neexistuje žádná vyhovující Automatická konfigurace pro Linux (integrace Apache nebo Nginx).</span><span class="sxs-lookup"><span data-stu-id="a84e7-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="a84e7-259">Pro přeposílání schématu od proxy serveru ve scénářích mimo službu IIS přidejte a nakonfigurujte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="a84e7-259">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="a84e7-260">V `Startup.ConfigureServices` použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="a84e7-260">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="a84e7-261">Předávání certifikátů</span><span class="sxs-lookup"><span data-stu-id="a84e7-261">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="a84e7-262">Azure</span><span class="sxs-lookup"><span data-stu-id="a84e7-262">Azure</span></span>

<span data-ttu-id="a84e7-263">Pokud chcete nakonfigurovat Azure App Service pro předávání certifikátů, přečtěte si téma [Konfigurace vzájemného ověřování TLS pro Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="a84e7-263">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="a84e7-264">Následující pokyny se týkají konfigurace aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a84e7-264">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="a84e7-265">V `Startup.Configure` přidejte následující kód před volání do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="a84e7-265">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="a84e7-266">Nakonfigurujte middleware předávání certifikátů a určete název hlavičky, kterou používá Azure.</span><span class="sxs-lookup"><span data-stu-id="a84e7-266">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="a84e7-267">V `Startup.ConfigureServices` přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:</span><span class="sxs-lookup"><span data-stu-id="a84e7-267">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="a84e7-268">Další webové proxy servery</span><span class="sxs-lookup"><span data-stu-id="a84e7-268">Other web proxies</span></span>

<span data-ttu-id="a84e7-269">Pokud se používá proxy server, který není službou IIS nebo Azure App Service směrování žádostí na aplikace (ARR), nakonfigurujte proxy server tak, aby předal certifikát, který přijal v hlavičce protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a84e7-269">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="a84e7-270">V `Startup.Configure` přidejte následující kód před volání do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="a84e7-270">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="a84e7-271">Nakonfigurujte middleware předávání certifikátů a zadejte název záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-271">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="a84e7-272">V `Startup.ConfigureServices` přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:</span><span class="sxs-lookup"><span data-stu-id="a84e7-272">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="a84e7-273">Pokud proxy server nemá kódování Base64 (jako je případ s Nginx), nastavte `HeaderConverter` možnost.</span><span class="sxs-lookup"><span data-stu-id="a84e7-273">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="a84e7-274">Vezměte v úvahu následující příklad `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a84e7-274">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="a84e7-275">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="a84e7-275">Troubleshoot</span></span>

<span data-ttu-id="a84e7-276">Když hlavičky nejsou předávány podle očekávání, povolte [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a84e7-276">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a84e7-277">Pokud protokoly neposkytují dostatek informací pro řešení tohoto problému, vytvořte výčet hlaviček požadavků přijatých serverem.</span><span class="sxs-lookup"><span data-stu-id="a84e7-277">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="a84e7-278">K zápisu hlaviček požadavků do odpovědi aplikace nebo k protokolování hlaviček použijte vložený middlewarový middleware.</span><span class="sxs-lookup"><span data-stu-id="a84e7-278">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="a84e7-279">Chcete-li zapsat hlavičky do odpovědi aplikace, umístěte následující vložený middleware terminálu hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a84e7-279">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="a84e7-280">Do protokolů můžete zapisovat místo těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a84e7-280">You can write to logs instead of the response body.</span></span> <span data-ttu-id="a84e7-281">Zápis do protokolů umožňuje, aby lokalita fungovala normálně během ladění.</span><span class="sxs-lookup"><span data-stu-id="a84e7-281">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="a84e7-282">Zápis protokolů místo textu odpovědi:</span><span class="sxs-lookup"><span data-stu-id="a84e7-282">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="a84e7-283">Vložení `ILogger<Startup>` do `Startup` třídy, jak je popsáno v tématu [vytvoření protokolů při spuštění](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="a84e7-283">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="a84e7-284">Vložte následující vložený middleware hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-284">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="a84e7-285">Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty přesunuty na `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-285">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="a84e7-286">Pokud existuje více hodnot v dané hlavičce, middleware pro přesměrované hlavičky zpracovává záhlaví v opačném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="a84e7-286">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a84e7-287">Výchozí hodnota `ForwardLimit` je `1` (jedna), takže je zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="a84e7-287">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="a84e7-288">Původní Vzdálená IP adresa žádosti se musí shodovat s položkou v `KnownProxies` `KnownNetworks` seznamech nebo před zpracováním předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a84e7-288">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="a84e7-289">To omezuje falšování hlaviček tím, že nepřijímá předávací servery od nedůvěryhodných proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="a84e7-289">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="a84e7-290">Při zjištění neznámého proxy serveru se zobrazí zpráva s informacemi o adrese proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="a84e7-290">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="a84e7-291">V předchozím příkladu je 10.0.0.100 proxy server.</span><span class="sxs-lookup"><span data-stu-id="a84e7-291">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="a84e7-292">Pokud je server důvěryhodným proxy serverem, přidejte IP adresu serveru do `KnownProxies` (nebo přidejte důvěryhodnou síť do `KnownNetworks` ) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-292">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a84e7-293">Další informace najdete v části [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-293">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="a84e7-294">Povoluje pouze důvěryhodné proxy servery a sítě pro přeposílání hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a84e7-294">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="a84e7-295">V opačném případě jsou možné útoky s [falešnou IP adresou](https://www.iplocation.net/ip-spoofing) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-295">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a84e7-296">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a84e7-296">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="a84e7-297">Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET Core zvýšení zabezpečení oprávnění</span><span class="sxs-lookup"><span data-stu-id="a84e7-297">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a84e7-298">V Doporučené konfiguraci pro ASP.NET Core se aplikace hostuje pomocí IIS/ASP. Modul NET Core, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="a84e7-298">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="a84e7-299">Proxy servery, nástroje pro vyrovnávání zatížení a další síťová zařízení často překrývají informace o žádosti předtím, než dosáhnou aplikace:</span><span class="sxs-lookup"><span data-stu-id="a84e7-299">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="a84e7-300">Pokud jsou požadavky HTTPS proxy serverem přes HTTP, původní schéma (HTTPS) se ztratí a musí se přenášet v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="a84e7-300">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="a84e7-301">Vzhledem k tomu, že aplikace obdrží požadavek od proxy serveru a ne jeho skutečný zdroj v Internetu nebo podnikové síti, musí být zdrojová adresa IP klienta předána také v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="a84e7-301">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="a84e7-302">Tyto informace můžou být důležité ve zpracování žádostí, například v přesměrování, ověřování, generování odkazů, vyhodnocení zásad a geografická poloha klienta.</span><span class="sxs-lookup"><span data-stu-id="a84e7-302">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="a84e7-303">Předávaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="a84e7-303">Forwarded headers</span></span>

<span data-ttu-id="a84e7-304">Podle konvence proxy předávají informace v hlavičkách protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a84e7-304">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="a84e7-305">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="a84e7-305">Header</span></span> | <span data-ttu-id="a84e7-306">Popis</span><span class="sxs-lookup"><span data-stu-id="a84e7-306">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="a84e7-307">X-předané – pro</span><span class="sxs-lookup"><span data-stu-id="a84e7-307">X-Forwarded-For</span></span> | <span data-ttu-id="a84e7-308">Uchovává informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="a84e7-308">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="a84e7-309">Tento parametr může obsahovat IP adresy (a volitelně také čísla portů).</span><span class="sxs-lookup"><span data-stu-id="a84e7-309">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="a84e7-310">V řetězci proxy serverů označuje první parametr klienta, ve kterém se žádost poprvé nastavila.</span><span class="sxs-lookup"><span data-stu-id="a84e7-310">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="a84e7-311">Následující identifikátory proxy následují.</span><span class="sxs-lookup"><span data-stu-id="a84e7-311">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="a84e7-312">Poslední proxy server v řetězci není uveden v seznamu parametrů.</span><span class="sxs-lookup"><span data-stu-id="a84e7-312">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="a84e7-313">Poslední IP adresa proxy serveru a volitelně je číslo portu, které je k dispozici jako Vzdálená IP adresa v transportní vrstvě.</span><span class="sxs-lookup"><span data-stu-id="a84e7-313">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="a84e7-314">X-předáno – proto</span><span class="sxs-lookup"><span data-stu-id="a84e7-314">X-Forwarded-Proto</span></span> | <span data-ttu-id="a84e7-315">Hodnota původního schématu (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a84e7-315">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="a84e7-316">Hodnota může být také seznamem schémat, pokud je požadavek procházen více proxy.</span><span class="sxs-lookup"><span data-stu-id="a84e7-316">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="a84e7-317">X-předávaný-Host</span><span class="sxs-lookup"><span data-stu-id="a84e7-317">X-Forwarded-Host</span></span> | <span data-ttu-id="a84e7-318">Původní hodnota pole hlavičky hostitele</span><span class="sxs-lookup"><span data-stu-id="a84e7-318">The original value of the Host header field.</span></span> <span data-ttu-id="a84e7-319">Servery proxy většinou nemění hlavičku hostitele.</span><span class="sxs-lookup"><span data-stu-id="a84e7-319">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="a84e7-320">Viz informační [zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pro informace o chybě zabezpečení zvýšení oprávnění, která má vliv na systémy, ve kterých server proxy neověřuje nebo omezuje hlavičky hostitele na známé správné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a84e7-320">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="a84e7-321">Middleware předávaných hlaviček z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a vyplní související pole v <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="a84e7-321">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="a84e7-322">Aktualizace middlewaru:</span><span class="sxs-lookup"><span data-stu-id="a84e7-322">The middleware updates:</span></span>

* <span data-ttu-id="a84e7-323">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): nastavte pomocí `X-Forwarded-For` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="a84e7-324">Další nastavení mají vliv na to, jak middleware nastaví `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-324">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="a84e7-325">Podrobnosti najdete v tématu [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="a84e7-325">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="a84e7-326">[HttpContext. Request. schéma](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): nastavte pomocí `X-Forwarded-Proto` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="a84e7-327">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): nastavte pomocí `X-Forwarded-Host` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="a84e7-328">Je možné nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru u předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a84e7-328">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="a84e7-329">Výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="a84e7-329">The default settings are:</span></span>

* <span data-ttu-id="a84e7-330">Mezi aplikací a zdrojem požadavků je jenom *jeden proxy server* .</span><span class="sxs-lookup"><span data-stu-id="a84e7-330">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="a84e7-331">Pro známé proxy servery a známé sítě jsou nakonfigurovány pouze adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="a84e7-331">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="a84e7-332">Předané hlavičky jsou pojmenovány `X-Forwarded-For` a `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-332">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="a84e7-333">Ne všechna síťová zařízení přidávají `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a84e7-333">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="a84e7-334">Zeptejte se na pokyny výrobce zařízení, jestli proxy požadavky při přístupu do aplikace tyto hlavičky neobsahují.</span><span class="sxs-lookup"><span data-stu-id="a84e7-334">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="a84e7-335">Pokud zařízení používá jiné názvy hlaviček než `X-Forwarded-For` a `X-Forwarded-Proto` , nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným zařízením.</span><span class="sxs-lookup"><span data-stu-id="a84e7-335">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="a84e7-336">Další informace najdete v tématu [předané možnosti a konfigurace middlewarových hlaviček](#forwarded-headers-middleware-options) [pro proxy server, který používá jiné názvy hlaviček](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="a84e7-336">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="a84e7-337">Modul IIS/IIS Express a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a84e7-337">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="a84e7-338">Middleware předávaných hlaviček je ve výchozím nastavení povolená službou [IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , když je aplikace hostovaná [mimo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) službu IIS a modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a84e7-338">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="a84e7-339">Middleware předávaných hlaviček se aktivují, aby se nejdříve spouštěl v kanálu middleware s omezenou konfigurací určenou pro modul ASP.NET Core, protože se jedná o obavy týkající se důvěryhodnosti se předanými hlavičkami (například [falšování IP adres](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="a84e7-339">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="a84e7-340">Middleware je nakonfigurovaná pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a a je omezená na jeden localhost proxy.</span><span class="sxs-lookup"><span data-stu-id="a84e7-340">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="a84e7-341">Pokud se vyžaduje další konfigurace, přečtěte si téma [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="a84e7-341">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a84e7-342">Další scénáře proxy server a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="a84e7-342">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a84e7-343">Bez použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimo procesy](xref:host-and-deploy/iis/index#out-of-process-hosting-model)není middleware předávaných hlaviček ve výchozím nastavení povolená.</span><span class="sxs-lookup"><span data-stu-id="a84e7-343">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="a84e7-344">Middleware předaných hlaviček musí být povolená, aby aplikace zpracovala předávané hlavičky s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="a84e7-344">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="a84e7-345">Po povolení middlewaru, pokud není <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zadáno pro middleware, výchozí [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a84e7-345">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="a84e7-346">Nakonfigurujte middleware s nástrojem <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> na předáte `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-346">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a84e7-347">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu v `Startup.Configure` před voláním jiného middleware:</span><span class="sxs-lookup"><span data-stu-id="a84e7-347">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="a84e7-348">Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` nebo přímo k metodě rozšíření s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , výchozí hlavičky pro přeposílání jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a84e7-348">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="a84e7-349"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Vlastnost musí být nakonfigurována s hlavičkou pro přeposílání.</span><span class="sxs-lookup"><span data-stu-id="a84e7-349">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="a84e7-350">Konfigurace nginx</span><span class="sxs-lookup"><span data-stu-id="a84e7-350">Nginx configuration</span></span>

<span data-ttu-id="a84e7-351">Pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a naleznete informace v tématu <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="a84e7-351">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="a84e7-352">Další informace najdete v tématu [Nginx: použití předané hlavičky](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="a84e7-352">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="a84e7-353">Konfigurace Apache</span><span class="sxs-lookup"><span data-stu-id="a84e7-353">Apache configuration</span></span>

<span data-ttu-id="a84e7-354">`X-Forwarded-For`se přidá automaticky (viz část [modulu Apache mod_proxy: hlavičky žádosti reverzního proxy serveru](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="a84e7-354">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="a84e7-355">Informace o tom, jak přeslat `X-Forwarded-Proto` hlavičku, najdete v tématu <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="a84e7-355">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="a84e7-356">Možnosti middlewaru předávaných hlaviček</span><span class="sxs-lookup"><span data-stu-id="a84e7-356">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="a84e7-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>řízení chování middlewaru předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a84e7-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="a84e7-358">Následující příklad změní výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="a84e7-358">The following example changes the default values:</span></span>

* <span data-ttu-id="a84e7-359">Omezte počet záznamů v předaných hlavičkách na `2` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-359">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="a84e7-360">Přidejte známou adresu proxy serveru `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-360">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="a84e7-361">Změňte název předané hlavičky z výchozí `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-361">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="a84e7-362">Možnost</span><span class="sxs-lookup"><span data-stu-id="a84e7-362">Option</span></span> | <span data-ttu-id="a84e7-363">Popis</span><span class="sxs-lookup"><span data-stu-id="a84e7-363">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="a84e7-364">Omezí hostitele v `X-Forwarded-Host` hlavičce na zadané hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a84e7-364">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="a84e7-365">Hodnoty se porovnávají pomocí pořadí – ignorovat – případ.</span><span class="sxs-lookup"><span data-stu-id="a84e7-365">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="a84e7-366">Čísla portů musí být vyloučena.</span><span class="sxs-lookup"><span data-stu-id="a84e7-366">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="a84e7-367">Pokud je seznam prázdný, jsou povoleny všichni hostitelé.</span><span class="sxs-lookup"><span data-stu-id="a84e7-367">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="a84e7-368">Zástupný znak nejvyšší úrovně `*` umožňuje všem neprázdným hostitelům.</span><span class="sxs-lookup"><span data-stu-id="a84e7-368">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="a84e7-369">Zástupné znaky subdomény jsou povolené, ale neodpovídají kořenové doméně.</span><span class="sxs-lookup"><span data-stu-id="a84e7-369">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="a84e7-370">Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale nikoli ke kořenové doméně `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-370">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="a84e7-371">Názvy hostitelů Unicode jsou povoleny, ale jsou převedeny na [Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</span><span class="sxs-lookup"><span data-stu-id="a84e7-371">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="a84e7-372">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí zahrnovat ohraničující závorky a musí být v [konvenční formě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="a84e7-372">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="a84e7-373">IPv6 adresy nejsou speciální – použita pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádné kanonikalizace.</span><span class="sxs-lookup"><span data-stu-id="a84e7-373">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="a84e7-374">Nepodaří-li se omezit povolené hostitele, může útočníkovi umožnit přístup k falešným odkazům generovanými službou.</span><span class="sxs-lookup"><span data-stu-id="a84e7-374">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="a84e7-375">Výchozí hodnota je prázdná `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-375">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="a84e7-376">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-376">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="a84e7-377">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-For` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-377">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a84e7-378">Výchozí formát je `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-378">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="a84e7-379">Určuje, které předávacíci by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="a84e7-379">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="a84e7-380">Seznam polí, která platí, najdete v tématu [ForwardedHeaders enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-380">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="a84e7-381">Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-381">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="a84e7-382">Výchozí hodnota je [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a84e7-382">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="a84e7-383">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-383">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="a84e7-384">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Host` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-384">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a84e7-385">Výchozí formát je `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-385">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="a84e7-386">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-386">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="a84e7-387">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Proto` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a84e7-387">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a84e7-388">Výchozí formát je `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-388">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="a84e7-389">Omezuje počet položek v záhlavích, které jsou zpracovávány.</span><span class="sxs-lookup"><span data-stu-id="a84e7-389">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="a84e7-390">Nastavením `null` tohoto omezení zakážete, ale to by mělo být provedeno pouze v případě, že `KnownProxies` nebo `KnownNetworks` jsou nakonfigurovány.</span><span class="sxs-lookup"><span data-stu-id="a84e7-390">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="a84e7-391">Nastavení `null` nehodnoty je preventivní opatrnost (ale ne záruka) pro ochranu proti nesprávně konfigurovaným proxy a škodlivým požadavkům přicházejících ze dvoustranných kanálů v síti.</span><span class="sxs-lookup"><span data-stu-id="a84e7-391">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="a84e7-392">Middleware předávaných hlaviček zpracovává hlavičky v obráceném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="a84e7-392">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a84e7-393">Pokud je použita výchozí hodnota ( `1` ), bude zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="a84e7-393">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="a84e7-394">Výchozí formát je `1`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-394">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="a84e7-395">Rozsahy adres známých sítí, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="a84e7-395">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="a84e7-396">Zadejte rozsahy IP adres pomocí zápisu CIDR (Classless prosměrování mezi doménami).</span><span class="sxs-lookup"><span data-stu-id="a84e7-396">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="a84e7-397">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="a84e7-397">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a84e7-398">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a84e7-398">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a84e7-399">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a84e7-399">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a84e7-400">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-400">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a84e7-401">Výchozí hodnota je `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> obsahující jednu položku pro `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-401">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="a84e7-402">Adresy známých proxy adres, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="a84e7-402">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="a84e7-403">Použijte `KnownProxies` k určení přesné shody IP adres.</span><span class="sxs-lookup"><span data-stu-id="a84e7-403">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="a84e7-404">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="a84e7-404">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a84e7-405">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a84e7-405">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a84e7-406">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a84e7-406">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a84e7-407">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-407">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a84e7-408">Výchozí hodnota je `IList` \<<xref:System.Net.IPAddress>> obsahující jednu položku pro `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-408">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="a84e7-409">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="a84e7-410">Výchozí formát je `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-410">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="a84e7-411">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-411">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="a84e7-412">Výchozí formát je `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-412">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="a84e7-413">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a84e7-413">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="a84e7-414">Výchozí formát je `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="a84e7-414">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="a84e7-415">Vyžaduje, aby byl počet synchronizovaných hodnot hlaviček mezi zpracovávaným [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-415">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="a84e7-416">Výchozí hodnota v ASP.NET Core 1. x je `true` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-416">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="a84e7-417">Výchozí hodnota v ASP.NET Core 2,0 nebo novější je `false` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-417">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="a84e7-418">Scénáře a případy použití</span><span class="sxs-lookup"><span data-stu-id="a84e7-418">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="a84e7-419">Pokud není možné přidat předávané hlavičky a všechny požadavky jsou zabezpečené</span><span class="sxs-lookup"><span data-stu-id="a84e7-419">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="a84e7-420">V některých případech nemusí být možné přidat předávaná záhlaví do požadavků, které jsou proxy k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a84e7-420">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="a84e7-421">Pokud proxy vynucuje, že všechny veřejné externí požadavky jsou HTTPS, můžete schéma ručně nastavit v části `Startup.Configure` před použitím libovolného typu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="a84e7-421">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="a84e7-422">Tento kód může být zakázán pomocí proměnné prostředí nebo jiného nastavení konfigurace ve vývojovém nebo přípravném prostředí.</span><span class="sxs-lookup"><span data-stu-id="a84e7-422">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="a84e7-423">Práce se základními a proxy servery, které mění cestu požadavku</span><span class="sxs-lookup"><span data-stu-id="a84e7-423">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="a84e7-424">Některé proxy servery předávají cestu beze změny, ale se základní cestou aplikace, která by se měla odebrat, aby směrování fungovalo správně.</span><span class="sxs-lookup"><span data-stu-id="a84e7-424">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="a84e7-425">Middleware [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware rozdělí cestu do [HttpRequest. cesta](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace do [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="a84e7-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="a84e7-426">Pokud `/foo` je základní cesta aplikace pro cestu k proxy serveru předaná jako `/foo/api/1` , middleware nastaví `Request.PathBase` na `/foo` a `Request.Path` do `/api/1` pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="a84e7-426">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="a84e7-427">Původní cesta a základ cesty se znovu aplikují, když se middleware znovu zavolá v opačném případě.</span><span class="sxs-lookup"><span data-stu-id="a84e7-427">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="a84e7-428">Další informace o zpracování pořadí middlewaru najdete v tématu <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="a84e7-428">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="a84e7-429">Pokud proxy ořízne cestu (například předávání `/foo/api/1` na `/api/1` ), opravte přesměrování a odkazy nastavením vlastnosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) žádosti:</span><span class="sxs-lookup"><span data-stu-id="a84e7-429">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="a84e7-430">Pokud proxy přidává data cest, zahodí část cesty pro opravu přesměrování a odkazy pomocí <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> a přiřazení k <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a84e7-430">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="a84e7-431">Konfigurace proxy serveru, který používá jiné názvy hlaviček</span><span class="sxs-lookup"><span data-stu-id="a84e7-431">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="a84e7-432">Pokud proxy nepoužívá hlavičky s názvem `X-Forwarded-For` a `X-Forwarded-Proto` k přeposlání informací o adrese proxy/portu a informace o původním schématu, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným proxy serverem:</span><span class="sxs-lookup"><span data-stu-id="a84e7-432">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="a84e7-433">Konfigurace pro adresu IPv4 reprezentovanou jako IPv6 adresa</span><span class="sxs-lookup"><span data-stu-id="a84e7-433">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="a84e7-434">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaného v protokolu IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="a84e7-434">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="a84e7-435">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a84e7-435">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a84e7-436">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a84e7-436">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="a84e7-437">V následujícím příkladu se síťová adresa, která poskytuje předávaná záhlaví, přidá do `KnownNetworks` seznamu ve formátu protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="a84e7-437">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="a84e7-438">Adresa IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="a84e7-438">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="a84e7-439">Převedená adresa IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="a84e7-439">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="a84e7-440">Délka převedené předpony: 104</span><span class="sxs-lookup"><span data-stu-id="a84e7-440">Converted prefix length: 104</span></span>

<span data-ttu-id="a84e7-441">Adresu můžete také uvést v šestnáctkovém formátu ( `10.11.12.1` reprezentované protokolem IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="a84e7-441">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="a84e7-442">Při převodu adresy IPv4 na IPv6 přidejte 96 k délce předpony CIDR ( `8` v příkladu), aby se zohlednila další `::ffff:` předpona IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="a84e7-442">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="a84e7-443">Předejte schéma pro reverzní proxy servery se systémy Linux a non-IIS.</span><span class="sxs-lookup"><span data-stu-id="a84e7-443">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="a84e7-444">Aplikace, které volají <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> a <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umísťují lokalitu do nekonečné smyčky, pokud jsou nasazené do azure Linux App Service, virtuální počítač Azure Linux (VM) nebo za jakýkoli jiný reverzní proxy server, kromě služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a84e7-444">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="a84e7-445">Protokol TLS je ukončen reverzním proxy serverem a Kestrel není informován o správném schématu požadavků.</span><span class="sxs-lookup"><span data-stu-id="a84e7-445">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="a84e7-446">OAuth a OIDC v této konfiguraci selže, protože generují nesprávná přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a84e7-446">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="a84e7-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Přidá a nakonfiguruje middleware předávaných hlaviček při spuštění za IIS, ale neexistuje žádná vyhovující Automatická konfigurace pro Linux (integrace Apache nebo Nginx).</span><span class="sxs-lookup"><span data-stu-id="a84e7-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="a84e7-448">Pro přeposílání schématu od proxy serveru ve scénářích mimo službu IIS přidejte a nakonfigurujte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="a84e7-448">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="a84e7-449">V `Startup.ConfigureServices` použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="a84e7-449">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="a84e7-450">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="a84e7-450">Troubleshoot</span></span>

<span data-ttu-id="a84e7-451">Když hlavičky nejsou předávány podle očekávání, povolte [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a84e7-451">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a84e7-452">Pokud protokoly neposkytují dostatek informací pro řešení tohoto problému, vytvořte výčet hlaviček požadavků přijatých serverem.</span><span class="sxs-lookup"><span data-stu-id="a84e7-452">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="a84e7-453">K zápisu hlaviček požadavků do odpovědi aplikace nebo k protokolování hlaviček použijte vložený middlewarový middleware.</span><span class="sxs-lookup"><span data-stu-id="a84e7-453">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="a84e7-454">Chcete-li zapsat hlavičky do odpovědi aplikace, umístěte následující vložený middleware terminálu hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a84e7-454">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="a84e7-455">Do protokolů můžete zapisovat místo těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a84e7-455">You can write to logs instead of the response body.</span></span> <span data-ttu-id="a84e7-456">Zápis do protokolů umožňuje, aby lokalita fungovala normálně během ladění.</span><span class="sxs-lookup"><span data-stu-id="a84e7-456">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="a84e7-457">Zápis protokolů místo textu odpovědi:</span><span class="sxs-lookup"><span data-stu-id="a84e7-457">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="a84e7-458">Vložení `ILogger<Startup>` do `Startup` třídy, jak je popsáno v tématu [vytvoření protokolů při spuštění](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="a84e7-458">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="a84e7-459">Vložte následující vložený middleware hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-459">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="a84e7-460">Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty přesunuty na `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-460">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="a84e7-461">Pokud existuje více hodnot v dané hlavičce, middleware pro přesměrované hlavičky zpracovává záhlaví v opačném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="a84e7-461">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a84e7-462">Výchozí hodnota `ForwardLimit` je `1` (jedna), takže je zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="a84e7-462">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="a84e7-463">Původní Vzdálená IP adresa žádosti se musí shodovat s položkou v `KnownProxies` `KnownNetworks` seznamech nebo před zpracováním předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a84e7-463">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="a84e7-464">To omezuje falšování hlaviček tím, že nepřijímá předávací servery od nedůvěryhodných proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="a84e7-464">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="a84e7-465">Při zjištění neznámého proxy serveru se zobrazí zpráva s informacemi o adrese proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="a84e7-465">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="a84e7-466">V předchozím příkladu je 10.0.0.100 proxy server.</span><span class="sxs-lookup"><span data-stu-id="a84e7-466">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="a84e7-467">Pokud je server důvěryhodným proxy serverem, přidejte IP adresu serveru do `KnownProxies` (nebo přidejte důvěryhodnou síť do `KnownNetworks` ) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a84e7-467">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a84e7-468">Další informace najdete v části [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-468">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="a84e7-469">Povoluje pouze důvěryhodné proxy servery a sítě pro přeposílání hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a84e7-469">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="a84e7-470">V opačném případě jsou možné útoky s [falešnou IP adresou](https://www.iplocation.net/ip-spoofing) .</span><span class="sxs-lookup"><span data-stu-id="a84e7-470">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a84e7-471">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a84e7-471">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="a84e7-472">Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET Core zvýšení zabezpečení oprávnění</span><span class="sxs-lookup"><span data-stu-id="a84e7-472">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

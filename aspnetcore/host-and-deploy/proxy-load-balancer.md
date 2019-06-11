---
title: Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení
author: guardrex
description: Další informace o konfiguraci aplikací hostovaných za službou proxy servery a nástroje pro vyrovnávání zatížení, které často skryl důležité vyžádat informace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/07/2019
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 582664071e8eb3d817cab10ea12c1df7c6d09ea7
ms.sourcegitcommit: 9691b742134563b662948b0ed63f54ef7186801e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2019
ms.locfileid: "66824818"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="2080c-103">Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="2080c-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="2080c-104">Podle [Luke Latham](https://github.com/guardrex) a [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="2080c-104">By [Luke Latham](https://github.com/guardrex) and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="2080c-105">Doporučenou konfiguraci pro ASP.NET Core je aplikace hostovaná pomocí modulu jádra IIS/ASP.NET, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="2080c-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="2080c-106">Proxy servery, nástroje pro vyrovnávání zatížení a jiných síťových zařízení často skryl informace o požadavku předtím, než dosáhne aplikace:</span><span class="sxs-lookup"><span data-stu-id="2080c-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="2080c-107">Pokud požadavky HTTPS jsou směrovány přes proxy server pomocí protokolu HTTP, původní schéma (HTTPS) ztratí a musí předávat v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="2080c-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="2080c-108">Protože aplikace obdrží žádost z proxy serveru a není true zdroj na Internetu nebo podnikové sítě, musí v hlavičce předané zdrojovou IP adresu klienta.</span><span class="sxs-lookup"><span data-stu-id="2080c-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="2080c-109">Tyto informace mohou být důležité pro zpracování žádostí, například v přesměrování ověřování, generování odkazů, hodnocení zásad a informace o zeměpisné poloze klienta.</span><span class="sxs-lookup"><span data-stu-id="2080c-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="2080c-110">Přesměrovaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="2080c-110">Forwarded headers</span></span>

<span data-ttu-id="2080c-111">Podle konvence proxy předávat informace v hlavičkách protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2080c-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="2080c-112">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="2080c-112">Header</span></span> | <span data-ttu-id="2080c-113">Popis</span><span class="sxs-lookup"><span data-stu-id="2080c-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="2080c-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="2080c-114">X-Forwarded-For</span></span> | <span data-ttu-id="2080c-115">Obsahuje informace o klientovi, který inicioval žádost a následné proxy serverů v řetězci proxy servery.</span><span class="sxs-lookup"><span data-stu-id="2080c-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="2080c-116">Tento parametr může obsahovat IP adresy (a volitelně čísla portů).</span><span class="sxs-lookup"><span data-stu-id="2080c-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="2080c-117">První parametr v řetězci proxy servery, označuje klienta, kde byl první požadavek.</span><span class="sxs-lookup"><span data-stu-id="2080c-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="2080c-118">Postupujte podle dalších proxy identifikátory.</span><span class="sxs-lookup"><span data-stu-id="2080c-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="2080c-119">Poslední proxy server v řetězu není v seznamu parametrů.</span><span class="sxs-lookup"><span data-stu-id="2080c-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="2080c-120">Poslední proxy IP adresu a volitelně čísla portu, jsou k dispozici jako Vzdálená IP adresa na transportní vrstvě.</span><span class="sxs-lookup"><span data-stu-id="2080c-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="2080c-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="2080c-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="2080c-122">Hodnota původní schématu (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2080c-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="2080c-123">Hodnota může být také seznam schémata, pokud požadavek má procházet více proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="2080c-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="2080c-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="2080c-124">X-Forwarded-Host</span></span> | <span data-ttu-id="2080c-125">Původní hodnota pole hlavičky hostitele.</span><span class="sxs-lookup"><span data-stu-id="2080c-125">The original value of the Host header field.</span></span> <span data-ttu-id="2080c-126">Obvykle proxy servery neupravujte hlavičku hostitele.</span><span class="sxs-lookup"><span data-stu-id="2080c-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="2080c-127">Zobrazit [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) informace o zvýšení oprávnění ohrožení zabezpečení, který má vliv na systémy, ve kterém není proxy server ověřit nebo omezit na známé dobré hodnoty hlavičky hostitele.</span><span class="sxs-lookup"><span data-stu-id="2080c-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="2080c-128">Middleware záhlaví předané z [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) balení, načte tyto hlavičky a vyplní přidružených polí na <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="2080c-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="2080c-129">Middleware aktualizace:</span><span class="sxs-lookup"><span data-stu-id="2080c-129">The middleware updates:</span></span>

* <span data-ttu-id="2080c-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; sada s použitím `X-Forwarded-For` hodnota hlavičky.</span><span class="sxs-lookup"><span data-stu-id="2080c-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="2080c-131">Další nastavení ovlivní, jak middleware nastaví `RemoteIpAddress`.</span><span class="sxs-lookup"><span data-stu-id="2080c-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="2080c-132">Podrobnosti najdete v tématu [možnosti předané middlewaru záhlaví](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2080c-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="2080c-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; sada s použitím `X-Forwarded-Proto` hodnota hlavičky.</span><span class="sxs-lookup"><span data-stu-id="2080c-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="2080c-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; sada s použitím `X-Forwarded-Host` hodnota hlavičky.</span><span class="sxs-lookup"><span data-stu-id="2080c-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="2080c-135">Předané záhlaví Middleware [výchozí nastavení](#forwarded-headers-middleware-options) lze nakonfigurovat.</span><span class="sxs-lookup"><span data-stu-id="2080c-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="2080c-136">Výchozí nastavení je:</span><span class="sxs-lookup"><span data-stu-id="2080c-136">The default settings are:</span></span>

* <span data-ttu-id="2080c-137">Existuje pouze *jeden proxy server* mezi aplikací a původu žádosti.</span><span class="sxs-lookup"><span data-stu-id="2080c-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="2080c-138">Pouze adresy zpětné smyčky jsou nakonfigurované pro známé servery proxy a známé sítě.</span><span class="sxs-lookup"><span data-stu-id="2080c-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="2080c-139">Přesměrovaná hlavičky jsou pojmenovány `X-Forwarded-For` a `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2080c-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="2080c-140">Ne všechna síťová zařízení přidat `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="2080c-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="2080c-141">Pokud požadavky směrovány přes proxy server, tyto hlavičky neobsahují, když dosáhnou aplikace, najdete pokyny výrobce vašeho zařízení.</span><span class="sxs-lookup"><span data-stu-id="2080c-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="2080c-142">Pokud zařízení používá odlišnou hlavičku názvy než `X-Forwarded-For` a `X-Forwarded-Proto`, nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> možnosti tak, aby odpovídaly názvy záhlaví používané zařízení.</span><span class="sxs-lookup"><span data-stu-id="2080c-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="2080c-143">Další informace najdete v tématu [možnosti předané middlewaru záhlaví](#forwarded-headers-middleware-options) a [konfiguraci proxy serveru, který používá odlišnou hlavičku názvy](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="2080c-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="2080c-144">Služby IIS/IIS Express a modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2080c-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="2080c-145">Ve výchozím nastavení je povoleno Middleware předaný záhlaví [Middleware pro integraci služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) když je aplikace hostovaná [mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model) za služby IIS a že modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2080c-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:fundamentals/servers/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="2080c-146">Middleware předaný záhlaví se aktivuje při spuštění první middleware kanál s omezenou konfiguraci konkrétní modul ASP.NET Core z důvodu otázky důvěryhodnosti předané hlavičky (například [falšování adresy IP](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="2080c-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="2080c-147">Middleware je nakonfigurovaný pro předávání `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví a je omezená na jednu localhost proxy.</span><span class="sxs-lookup"><span data-stu-id="2080c-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="2080c-148">Pokud je vyžadována další konfigurace, najdete v článku [možnosti předané middlewaru záhlaví](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="2080c-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="2080c-149">Další proxy serveru a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="2080c-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="2080c-150">Kromě použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování za nástrojem [mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model), předané Middleware záhlaví není povolená ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="2080c-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:fundamentals/servers/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="2080c-151">Přesměrovaná Middleware záhlaví musí být povolené pro aplikace pro proces, předá záhlaví s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="2080c-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="2080c-152">Po povolení middleware, pokud žádné <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jsou určené pro middleware, výchozí [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2080c-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="2080c-153">Nakonfigurujte middleware s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> předávat `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2080c-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2080c-154">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metoda `Startup.Configure` před voláním dalším middlewarem:</span><span class="sxs-lookup"><span data-stu-id="2080c-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="2080c-155">Pokud ne <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jsou určené v `Startup.ConfigureServices` nebo přímo na metodu rozšíření pomocí <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, jsou výchozí hlavičky pro předávání [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2080c-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="2080c-156"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> Vlastnosti se musí nakonfigurovat hlavičky pro předávání.</span><span class="sxs-lookup"><span data-stu-id="2080c-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="2080c-157">V konfiguraci serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="2080c-157">Nginx configuration</span></span>

<span data-ttu-id="2080c-158">Předat dál `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví, najdete v článku <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="2080c-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="2080c-159">Další informace najdete v tématu [NGINX: Pomocí hlavičky předané](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="2080c-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="2080c-160">Konfigurace serveru Apache</span><span class="sxs-lookup"><span data-stu-id="2080c-160">Apache configuration</span></span>

<span data-ttu-id="2080c-161">`X-Forwarded-For` je automaticky přidán (viz [mod_proxy Apache modul: Reverzní Proxy hlavičky žádosti](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="2080c-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="2080c-162">Informace o tom, jak přesměrovat `X-Forwarded-Proto` záhlaví, najdete v článku <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="2080c-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="2080c-163">Předané možnosti middlewaru záhlaví</span><span class="sxs-lookup"><span data-stu-id="2080c-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="2080c-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> řídí chování middlewaru předané záhlaví.</span><span class="sxs-lookup"><span data-stu-id="2080c-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="2080c-165">Následující příklad změní výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="2080c-165">The following example changes the default values:</span></span>

* <span data-ttu-id="2080c-166">Omezit počet položek v záhlaví předané do `2`.</span><span class="sxs-lookup"><span data-stu-id="2080c-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="2080c-167">Přidat známé proxy server s adresou `127.0.10.1`.</span><span class="sxs-lookup"><span data-stu-id="2080c-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="2080c-168">Změnit název hlavičky předané z výchozích `X-Forwarded-For` k `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="2080c-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="2080c-169">Možnost</span><span class="sxs-lookup"><span data-stu-id="2080c-169">Option</span></span> | <span data-ttu-id="2080c-170">Popis</span><span class="sxs-lookup"><span data-stu-id="2080c-170">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="2080c-171">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="2080c-171">AllowedHosts</span></span> | <span data-ttu-id="2080c-172">Hostitelé podle omezuje `X-Forwarded-Host` záhlaví zadanými hodnotami.</span><span class="sxs-lookup"><span data-stu-id="2080c-172">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="2080c-173">Hodnoty jsou porovnány pomocí pořadí ignorování případu.</span><span class="sxs-lookup"><span data-stu-id="2080c-173">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="2080c-174">Čísla portů musí být vyloučeny.</span><span class="sxs-lookup"><span data-stu-id="2080c-174">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="2080c-175">Pokud je seznam prázdný, jsou povoleny všechny hostitele.</span><span class="sxs-lookup"><span data-stu-id="2080c-175">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="2080c-176">Nejvyšší úrovně zástupný znak `*` umožňuje všichni hostitelé prázdný.</span><span class="sxs-lookup"><span data-stu-id="2080c-176">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="2080c-177">Subdoména zástupné znaky jsou povolené, ale kořenové domény se neshodují.</span><span class="sxs-lookup"><span data-stu-id="2080c-177">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="2080c-178">Například `*.contoso.com` odpovídá subdoménu `foo.contoso.com` , ale není kořenovou doménu `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="2080c-178">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="2080c-179">Názvy hostitelů Unicode jsou povolené, ale jsou převedeny na [kódování Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</span><span class="sxs-lookup"><span data-stu-id="2080c-179">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="2080c-180">[IPv6 adresy](https://tools.ietf.org/html/rfc4291) musí zahrnovat hranaté závorky ohraničující a je v [konvenční formuláře](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="2080c-180">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="2080c-181">IPv6 adresy nejsou speciální malými a velkými písmeny ke kontrole logické rovnost mezi různými formáty a je provedena bez převodu do kanonického tvaru.</span><span class="sxs-lookup"><span data-stu-id="2080c-181">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="2080c-182">Selhání omezit povolené hostitele může útočníkovi umožnit zfalšovat odkazy vygenerované službou.</span><span class="sxs-lookup"><span data-stu-id="2080c-182">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="2080c-183">Výchozí hodnota je prázdná `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="2080c-183">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="2080c-184">Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2080c-184">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="2080c-185">Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-For` záhlaví, ale používá některé hlavičky k předávání informací.</span><span class="sxs-lookup"><span data-stu-id="2080c-185">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2080c-186">Výchozí hodnota je `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="2080c-186">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="2080c-187">Určuje, které servery pro předávání by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="2080c-187">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="2080c-188">Zobrazit [ForwardedHeaders výčtu](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) seznam polí, které se vztahují.</span><span class="sxs-lookup"><span data-stu-id="2080c-188">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="2080c-189">Typické hodnoty přiřazené k této vlastnosti jsou "ForwardedHeaders.XForwardedFor</span><span class="sxs-lookup"><span data-stu-id="2080c-189">Typical values assigned to this property are \`ForwardedHeaders.XForwardedFor</span></span> | <span data-ttu-id="2080c-190">ForwardedHeaders.XForwardedProto\`.</span><span class="sxs-lookup"><span data-stu-id="2080c-190">ForwardedHeaders.XForwardedProto\`.</span></span><br><br><span data-ttu-id="2080c-191">Výchozí hodnota je [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="2080c-191">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="2080c-192">Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2080c-192">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="2080c-193">Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-Host` záhlaví, ale používá některé hlavičky k předávání informací.</span><span class="sxs-lookup"><span data-stu-id="2080c-193">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2080c-194">Výchozí hodnota je `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="2080c-194">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="2080c-195">Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2080c-195">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="2080c-196">Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-Proto` záhlaví, ale používá některé hlavičky k předávání informací.</span><span class="sxs-lookup"><span data-stu-id="2080c-196">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="2080c-197">Výchozí hodnota je `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2080c-197">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="2080c-198">Omezí počet položek v záhlaví, které se zpracovávají.</span><span class="sxs-lookup"><span data-stu-id="2080c-198">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="2080c-199">Nastavte na `null` zakázat limit, ale to lze provádět pouze pokud `KnownProxies` nebo `KnownNetworks` jsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="2080c-199">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span><br><br><span data-ttu-id="2080c-200">Výchozí hodnota je 1.</span><span class="sxs-lookup"><span data-stu-id="2080c-200">The default is 1.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="2080c-201">Rozsahy známých sítí tak, aby přijímal předané záhlaví z adres.</span><span class="sxs-lookup"><span data-stu-id="2080c-201">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="2080c-202">Zadejte rozsahy IP adres pomocí notace CIDR (Classless Interdomain Routing) zápisu.</span><span class="sxs-lookup"><span data-stu-id="2080c-202">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="2080c-203">Pokud server používá duální sokety, jsou ve formátu protokolu IPv6 zadané adresy IPv4 (například `10.0.0.1` IPv4 v IPv6 jako `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="2080c-203">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="2080c-204">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2080c-204">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2080c-205">Určit, pokud tento formát je požadováno pohledem [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2080c-205">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="2080c-206">Další informace najdete v tématu [konfigurace pro adresu IPv4 přístupnou reprezentovaná jako adresu IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) oddílu.</span><span class="sxs-lookup"><span data-stu-id="2080c-206">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="2080c-207">Výchozí hodnota je `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> obsahující jednu položku pro `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="2080c-207">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="2080c-208">Adresy z předané hlavičky accept známých proxy služby.</span><span class="sxs-lookup"><span data-stu-id="2080c-208">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="2080c-209">Použití `KnownProxies` určit přesné adresy IP odpovídá.</span><span class="sxs-lookup"><span data-stu-id="2080c-209">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="2080c-210">Pokud server používá duální sokety, jsou ve formátu protokolu IPv6 zadané adresy IPv4 (například `10.0.0.1` IPv4 v IPv6 jako `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="2080c-210">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="2080c-211">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2080c-211">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2080c-212">Určit, pokud tento formát je požadováno pohledem [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2080c-212">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="2080c-213">Další informace najdete v tématu [konfigurace pro adresu IPv4 přístupnou reprezentovaná jako adresu IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) oddílu.</span><span class="sxs-lookup"><span data-stu-id="2080c-213">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="2080c-214">Výchozí hodnota je `IList` \< <xref:System.Net.IPAddress>> obsahující jednu položku pro `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="2080c-214">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="2080c-215">Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2080c-215">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="2080c-216">Výchozí hodnota je `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="2080c-216">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="2080c-217">Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2080c-217">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="2080c-218">Výchozí hodnota je `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="2080c-218">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="2080c-219">Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="2080c-219">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="2080c-220">Výchozí hodnota je `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="2080c-220">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="2080c-221">Vyžadovat několik hodnot hlavičky byly synchronizované mezi [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) právě zpracovává.</span><span class="sxs-lookup"><span data-stu-id="2080c-221">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="2080c-222">Výchozí hodnotou v ASP.NET Core 1.x je `true`.</span><span class="sxs-lookup"><span data-stu-id="2080c-222">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="2080c-223">Výchozí hodnota v ASP.NET Core 2.0 nebo novější je `false`.</span><span class="sxs-lookup"><span data-stu-id="2080c-223">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="2080c-224">Scénáře a případy použití</span><span class="sxs-lookup"><span data-stu-id="2080c-224">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="2080c-225">Pokud není možné přidat předané záhlaví a všechny požadavky jsou zabezpečené</span><span class="sxs-lookup"><span data-stu-id="2080c-225">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="2080c-226">V některých případech nemusí být možné přidat záhlaví přesměrované na požadavky směrovány přes proxy server k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2080c-226">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="2080c-227">Pokud proxy server je vynucení, že jsou všechny veřejné externí požadavky HTTPS, schéma můžete ručně nastavit v `Startup.Configure` před použitím libovolného typu middleware:</span><span class="sxs-lookup"><span data-stu-id="2080c-227">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="2080c-228">Tento kód se dá zakázat pomocí proměnné prostředí nebo jiné nastavení konfigurace v vývojové nebo testovací prostředí.</span><span class="sxs-lookup"><span data-stu-id="2080c-228">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="2080c-229">Základ cesty a proxy servery, které se mění cestu požadavku</span><span class="sxs-lookup"><span data-stu-id="2080c-229">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="2080c-230">Některé proxy servery předat cestu beze změn, ale s aplikací základní cestu, která by se měly odebrat tak, aby směrování funguje správně.</span><span class="sxs-lookup"><span data-stu-id="2080c-230">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="2080c-231">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware rozdělí cestu do [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cesty aplikace do [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="2080c-231">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="2080c-232">Pokud `/foo` je základní cesty aplikace pro cestu proxy předán jako `/foo/api/1`, middleware sady `Request.PathBase` k `/foo` a `Request.Path` k `/api/1` pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="2080c-232">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="2080c-233">Původní cestu a základ cesty jsou znovu, když middleware volána znovu v opačném pořadí.</span><span class="sxs-lookup"><span data-stu-id="2080c-233">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="2080c-234">Další informace o zpracování objednávky middleware, naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="2080c-234">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="2080c-235">Pokud proxy server ořízne cestu (například předávání `/foo/api/1` k `/api/1`), oprava přesměruje a odkazy tak, že nastavíte žádosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) vlastnost:</span><span class="sxs-lookup"><span data-stu-id="2080c-235">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="2080c-236">Pokud proxy server je přidání data cesty, zahodit část cesty, chcete-li vyřešit odkazy a přesměrování pomocí <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> a přiřazení <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> vlastnost:</span><span class="sxs-lookup"><span data-stu-id="2080c-236">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="2080c-237">Konfigurace proxy serveru, která používá odlišnou hlavičku názvy</span><span class="sxs-lookup"><span data-stu-id="2080c-237">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="2080c-238">Pokud proxy server nepoužívá záhlaví s názvem `X-Forwarded-For` a `X-Forwarded-Proto` dál adresa/port proxy serveru, které pocházejí informace schéma, nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> možnosti tak, aby odpovídaly názvy záhlaví používaný proxy serverem:</span><span class="sxs-lookup"><span data-stu-id="2080c-238">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="2080c-239">Konfigurace pro IPv4 adresu reprezentovaná jako adresu IPv6</span><span class="sxs-lookup"><span data-stu-id="2080c-239">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="2080c-240">Pokud server používá duální sokety, jsou ve formátu protokolu IPv6 zadané adresy IPv4 (například `10.0.0.1` IPv4 v IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="2080c-240">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="2080c-241">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="2080c-241">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="2080c-242">Určit, pokud tento formát je požadováno pohledem [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="2080c-242">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="2080c-243">V následujícím příkladu je do poskytuje předané záhlaví síťová adresa `KnownNetworks` seznamu ve formátu protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="2080c-243">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="2080c-244">Adresa IPv4: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="2080c-244">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="2080c-245">Převedený IPv6 adresa: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="2080c-245">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="2080c-246">Délka předpony převedený: 104</span><span class="sxs-lookup"><span data-stu-id="2080c-246">Converted prefix length: 104</span></span>

<span data-ttu-id="2080c-247">Můžete také zadat adresu v šestnáctkovém formátu (`10.11.12.1` v IPv6 jako `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="2080c-247">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="2080c-248">Při převodu adresu IPv4 na IPv6, přidejte 96 do délka předpony CIDR (`8` v příkladu) pro další `::ffff:` předponu IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="2080c-248">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="2080c-249">Vpřed schéma pro systémy Linux a nevyužívající službu IIS reverzních proxy serverů</span><span class="sxs-lookup"><span data-stu-id="2080c-249">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="2080c-250">.NET core šablony volání <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> a <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>.</span><span class="sxs-lookup"><span data-stu-id="2080c-250">.NET Core templates call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>.</span></span> <span data-ttu-id="2080c-251">Tyto metody lokality umístěte do nekonečnou smyčku, pokud nasadíte do služby Azure Linux App Service, Azure s Linuxem virtuálních počítačů (VM), nebo za všechny ostatní reverzní proxy server kromě služby IIS.</span><span class="sxs-lookup"><span data-stu-id="2080c-251">These methods put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="2080c-252">Protokol TLS je ukončeno prvkem reverzního proxy serveru a Kestrel není informován o schéma správnou žádost.</span><span class="sxs-lookup"><span data-stu-id="2080c-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="2080c-253">OAuth a OIDC selhat i v této konfiguraci protože, která generují nesprávné přesměrování.</span><span class="sxs-lookup"><span data-stu-id="2080c-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="2080c-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> Přidá a nakonfiguruje předané Middleware záhlaví při spuštění za služby IIS, ale neexistuje žádná odpovídající automatické konfigurace pro Linux (integrace Apache nebo Nginx).</span><span class="sxs-lookup"><span data-stu-id="2080c-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="2080c-255">Pokud chcete dál schéma z proxy serveru v scénáře nevyužívající službu IIS, přidejte a nakonfigurujte předané Middleware záhlaví.</span><span class="sxs-lookup"><span data-stu-id="2080c-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="2080c-256">V `Startup.ConfigureServices`, použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="2080c-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

<span data-ttu-id="2080c-257">Dokud se nová Image kontejnerů jsou k dispozici v Azure, musíte vytvořit nastavení aplikace (proměnnou prostředí) pro `ASPNETCORE_FORWARDEDHEADERS_ENABLED` nastavena na `true`.</span><span class="sxs-lookup"><span data-stu-id="2080c-257">Until new container images are provided in Azure, you must create an app setting (environment variable) for `ASPNETCORE_FORWARDEDHEADERS_ENABLED` set to `true`.</span></span> <span data-ttu-id="2080c-258">Další informace najdete v tématu [šablony nebudou fungovat v systému Linux Antares z důvodu chybějícího předávání schéma (aspnet/AspNetCore #4135)](https://github.com/aspnet/AspNetCore/issues/4135).</span><span class="sxs-lookup"><span data-stu-id="2080c-258">For more information, see [Templates do not work in Antares Linux due to missing scheme forwarders (aspnet/AspNetCore #4135)](https://github.com/aspnet/AspNetCore/issues/4135).</span></span>

::: moniker-end

## <a name="troubleshoot"></a><span data-ttu-id="2080c-259">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="2080c-259">Troubleshoot</span></span>

<span data-ttu-id="2080c-260">Pokud hlavičky nejsou předávány očekávaným, povolit [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="2080c-260">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="2080c-261">Pokud protokoly neposkytují dostatek informací k vyřešení tohoto problému, výčet hlavičky žádosti přijaté serverem.</span><span class="sxs-lookup"><span data-stu-id="2080c-261">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="2080c-262">Použití middlewaru vložených zapsat hlavičky požadavku pro odpověď aplikace nebo záhlaví protokolu.</span><span class="sxs-lookup"><span data-stu-id="2080c-262">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="2080c-263">Do záhlaví se zapíší do odpovědi aplikace, umístěte následující middlewaru vložených terminálu ihned po volání <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="2080c-263">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="2080c-264">Můžete zapisovat do protokolů místo textu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2080c-264">You can write to logs instead of the response body.</span></span> <span data-ttu-id="2080c-265">Zápis do protokolů umožňuje lokality tak, aby funkce obvykle během ladění.</span><span class="sxs-lookup"><span data-stu-id="2080c-265">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="2080c-266">Zápis protokolů, nikoli do datové části odpovědi:</span><span class="sxs-lookup"><span data-stu-id="2080c-266">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="2080c-267">Vložit `ILogger<Startup>` do `Startup` třídy, jak je popsáno v [vytvářet protokoly v spuštění](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="2080c-267">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="2080c-268">Umístěte následující middlewaru vložených ihned po volání <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="2080c-268">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {METHOD}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {SCHEME}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {PATH}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {KEY}: {VALUE}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {REMOTE_IP_ADDRESS}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="2080c-269">Při zpracování `X-Forwarded-{For|Proto|Host}` hodnoty se přesunou do `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="2080c-269">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="2080c-270">Pokud jsou zadaná hlavička více hodnot, mějte na paměti předané Middleware záhlaví procesy záhlaví v obráceném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="2080c-270">If there are multiple values in a given header, note Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="2080c-271">Výchozí `ForwardLimit` je 1 (jeden), takže se zpracuje pouze krajní hodnotu z hlavičky, pokud hodnota `ForwardLimit` se zvýší.</span><span class="sxs-lookup"><span data-stu-id="2080c-271">The default `ForwardLimit` is 1 (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="2080c-272">Původní vzdálenou IP adresu požadavku musí shodovat s položkou v `KnownProxies` nebo `KnownNetworks` uvádí předtím, než se zpracovávají předané záhlaví.</span><span class="sxs-lookup"><span data-stu-id="2080c-272">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="2080c-273">Toto nastavení omezuje záhlaví falšování identity tím, že služby předávání z nedůvěryhodné proxy servery.</span><span class="sxs-lookup"><span data-stu-id="2080c-273">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="2080c-274">Při zjištění neznámé proxy protokolování určuje adresu proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="2080c-274">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="2080c-275">V předchozím příkladu je 10.0.0.100 proxy server.</span><span class="sxs-lookup"><span data-stu-id="2080c-275">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="2080c-276">Pokud je server důvěryhodným proxy serverem, přidat IP adresu serveru `KnownProxies` (nebo přidat k důvěryhodné síti `KnownNetworks`) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2080c-276">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2080c-277">Další informace najdete v tématu [možnosti předané middlewaru záhlaví](#forwarded-headers-middleware-options) oddílu.</span><span class="sxs-lookup"><span data-stu-id="2080c-277">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="2080c-278">Povolte jenom důvěryhodným proxy serverů a sítí přeposílat záhlaví.</span><span class="sxs-lookup"><span data-stu-id="2080c-278">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="2080c-279">V opačném případě [falšování adresy IP](https://www.iplocation.net/ip-spoofing) útoky jsou možné.</span><span class="sxs-lookup"><span data-stu-id="2080c-279">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2080c-280">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2080c-280">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="2080c-281">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core chyba zabezpečení zvýšení oprávnění</span><span class="sxs-lookup"><span data-stu-id="2080c-281">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

---
title: Konfigurace ASP.NET Core pro práci s proxy servery a vyrovnáváním zatížení
author: rick-anderson
description: Přečtěte si o konfiguraci aplikací hostovaných za proxy servery a nástroji pro vyrovnávání zatížení, které často zakrývají důležité informace o požadavcích.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: b5c81e0cfa29cddeb1aeed1119a711fca4d91ae4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659381"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="e1ee9-103">Konfigurace ASP.NET Core pro práci s proxy servery a vyrovnáváním zatížení</span><span class="sxs-lookup"><span data-stu-id="e1ee9-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="e1ee9-104">Podle [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e1ee9-105">V doporučené konfiguraci pro ASP.NET Core je aplikace hostována pomocí core modulu IIS/ASP.NET Core Module, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="e1ee9-106">Proxy servery, vykladače zatížení a další síťová zařízení často zakrývají informace o požadavku před tím, než se dostane do aplikace:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="e1ee9-107">Pokud jsou požadavky HTTPS proxied přes HTTP, původní schéma (HTTPS) je ztracena a musí být předány v záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="e1ee9-108">Vzhledem k tomu, že aplikace obdrží požadavek z proxy serveru a není jeho skutečný zdroj v síti Internet nebo podnikové sítě, původní klient IP adresa musí být také předány v záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="e1ee9-109">Tyto informace mohou být důležité při zpracování požadavků, například při přesměrování, ověřování, generování propojení, vyhodnocení zásad a geografickém umístění klienta.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="e1ee9-110">Předávaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="e1ee9-110">Forwarded headers</span></span>

<span data-ttu-id="e1ee9-111">Podle konvence proxy předávat informace v hlavičkách HTTP.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="e1ee9-112">Hlavička</span><span class="sxs-lookup"><span data-stu-id="e1ee9-112">Header</span></span> | <span data-ttu-id="e1ee9-113">Popis</span><span class="sxs-lookup"><span data-stu-id="e1ee9-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="e1ee9-114">X-Forwarded-Pro</span><span class="sxs-lookup"><span data-stu-id="e1ee9-114">X-Forwarded-For</span></span> | <span data-ttu-id="e1ee9-115">Obsahuje informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="e1ee9-116">Tento parametr může obsahovat IP adresy (a volitelně čísla portů).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="e1ee9-117">V řetězci proxy serverů první parametr označuje klienta, kde byl požadavek poprvé proveden.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="e1ee9-118">Následují následné proxy identifikátory.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="e1ee9-119">Poslední proxy server v řetězci není v seznamu parametrů.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="e1ee9-120">Adresa IP posledního serveru proxy a volitelně číslo portu jsou k dispozici jako vzdálená adresa IP ve vrstvě přenosu.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="e1ee9-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="e1ee9-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="e1ee9-122">Hodnota původního schématu (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="e1ee9-123">Hodnota může být také seznam schémat, pokud požadavek prošel více proxy servery.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="e1ee9-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="e1ee9-124">X-Forwarded-Host</span></span> | <span data-ttu-id="e1ee9-125">Původní hodnota pole záhlaví Hostitele.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-125">The original value of the Host header field.</span></span> <span data-ttu-id="e1ee9-126">Proxy servery obvykle nemění záhlaví Hostitele.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="e1ee9-127">Informace o chybě zabezpečení, která ovlivňuje systémy, kde proxy server neověřuje nebo neomezuje záhlaví hostitele na známé dobré hodnoty, naleznete v [tématu Microsoft Security Advisory CVE-2018-0787.](https://github.com/aspnet/Announcements/issues/295)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="e1ee9-128">Middleware S předaná záhlaví z balíčku [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a <xref:Microsoft.AspNetCore.Http.HttpContext>vyplní přidružená pole na webu .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="e1ee9-129">Middleware aktualizace:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-129">The middleware updates:</span></span>

* <span data-ttu-id="e1ee9-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set `X-Forwarded-For` pomocí hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="e1ee9-131">Další nastavení ovlivňují nastavení `RemoteIpAddress`middlewaru .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="e1ee9-132">Podrobnosti naleznete v možnostech [middlewaru předávaných záhlaví](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="e1ee9-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set `X-Forwarded-Proto` pomocí hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="e1ee9-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set `X-Forwarded-Host` pomocí hodnoty hlavičky.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="e1ee9-135">Lze nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru s předanou hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="e1ee9-136">Výchozí nastavení jsou:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-136">The default settings are:</span></span>

* <span data-ttu-id="e1ee9-137">Existuje pouze *jeden proxy server* mezi aplikací a zdrojem požadavků.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="e1ee9-138">Pouze adresy zpětné smyčky jsou konfigurovány pro známé proxy servery a známé sítě.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="e1ee9-139">Předávaná záhlaví `X-Forwarded-For` `X-Forwarded-Proto`jsou pojmenována a .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="e1ee9-140">Ne všechna síťová `X-Forwarded-For` `X-Forwarded-Proto` zařízení přidávají záhlaví a bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="e1ee9-141">Pokud proxied požadavky neobsahují tyto hlavičky, když se dostanou do aplikace, přečtěte si pokyny výrobce zařízení.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="e1ee9-142">Pokud zařízení používá jiné `X-Forwarded-For` názvy záhlaví než a `X-Forwarded-Proto`, nastavte možnosti <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby odpovídaly názvům záhlaví používaným zařízením.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="e1ee9-143">Další informace naleznete v tématech [Možnosti middlewaru předávaných záhlaví](#forwarded-headers-middleware-options) a [Konfigurace pro proxy server, který používá různé názvy záhlaví](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="e1ee9-144">IIS/IIS Express a základní modul ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e1ee9-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="e1ee9-145">Middleware s předaná záhlaví je ve výchozím nastavení povolen [middlewarem integrace služby IIS,](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) pokud je aplikace hostována mimo proces za [službou](xref:host-and-deploy/iis/index#out-of-process-hosting-model) IIS a ASP.NET core modulem.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="e1ee9-146">Předaných záhlaví Middleware je aktivován pro spuštění jako první v middleware potrubí s omezenou konfiguraci specifickou pro ASP.NET Core Module z důvodu důvěry obavy s předanými záhlaví (například [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="e1ee9-147">Middleware je nakonfigurován `X-Forwarded-For` tak, aby předal a `X-Forwarded-Proto` záhlaví a je omezen na jeden proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="e1ee9-148">Pokud je požadována další konfigurace, podívejte se na [možnosti middlewaru předávaných záhlaví](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e1ee9-149">Další scénáře proxy serveru a vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="e1ee9-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e1ee9-150">Mimo použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimoproces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), není middleware předaných záhlaví ve výchozím nastavení povoleno.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="e1ee9-151">Middleware s předanými záhlavími musí být povolen, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>aby aplikace zpracovala předávaná záhlaví pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="e1ee9-152">Po povolení middlewaru, pokud middlewar u <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nezadaných nejsou, jsou výchozí [položky ForwardedHeaders.ForwardedHeaders.None](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) . [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="e1ee9-153">Nakonfigurujte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> middleware `X-Forwarded-For` `X-Forwarded-Proto` s pro `Startup.ConfigureServices`předávání a záhlaví v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e1ee9-154">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu `Startup.Configure` v před voláním jinémiddleware:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="e1ee9-155">Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` metodě <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>rozšíření nebo přímo k ní , výchozí hlavičky pro předávání jsou [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="e1ee9-156">Vlastnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> musí být nakonfigurována s hlavičkami dopředu.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="e1ee9-157">Konfigurace Nginx</span><span class="sxs-lookup"><span data-stu-id="e1ee9-157">Nginx configuration</span></span>

<span data-ttu-id="e1ee9-158">Chcete-li `X-Forwarded-For` `X-Forwarded-Proto` záhlaví a <xref:host-and-deploy/linux-nginx#configure-nginx>předat dál, přečtěte si informace o tom, zda neobsahuje hlavní a</span><span class="sxs-lookup"><span data-stu-id="e1ee9-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="e1ee9-159">Další informace naleznete v [tématu NGINX: Použití hlavičky Přeposílané](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="e1ee9-160">Konfigurace Apache</span><span class="sxs-lookup"><span data-stu-id="e1ee9-160">Apache configuration</span></span>

<span data-ttu-id="e1ee9-161">`X-Forwarded-For`(viz Apache [Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="e1ee9-162">Informace o tom, `X-Forwarded-Proto` jak předat <xref:host-and-deploy/linux-apache#configure-apache>záhlaví dál, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="e1ee9-163">Možnosti middlewaru předávaných záhlaví</span><span class="sxs-lookup"><span data-stu-id="e1ee9-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="e1ee9-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>chování middlewaru Předávaných záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="e1ee9-165">Následující příklad změní výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-165">The following example changes the default values:</span></span>

* <span data-ttu-id="e1ee9-166">Omezte počet položek v předaných záhlavích na `2`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="e1ee9-167">Přidejte známou `127.0.10.1`adresu proxy aplikace .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="e1ee9-168">Změňte předaný název `X-Forwarded-For` záhlaví `X-Forwarded-For-My-Custom-Header-Name`z výchozího na .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="e1ee9-169">Možnost</span><span class="sxs-lookup"><span data-stu-id="e1ee9-169">Option</span></span> | <span data-ttu-id="e1ee9-170">Popis</span><span class="sxs-lookup"><span data-stu-id="e1ee9-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="e1ee9-171">Omezí hostitele záhlavím `X-Forwarded-Host` na uvedené hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="e1ee9-172">Hodnoty jsou porovnány pomocí ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="e1ee9-173">Čísla portů musí být vyloučena.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="e1ee9-174">Pokud je seznam prázdný, jsou povoleni všichni hostitelé.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="e1ee9-175">Zástupný znak `*` nejvyšší úrovně umožňuje všem neprázdným hostitelům.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="e1ee9-176">Zástupné znaky subdomény jsou povoleny, ale neodpovídají kořenové doméně.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="e1ee9-177">Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale `contoso.com`ne kořenové doméně .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="e1ee9-178">Názvy hostitelů Unicode jsou [povoleny,](https://tools.ietf.org/html/rfc3492) ale jsou převedeny na Punycode pro porovnávání.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="e1ee9-179">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí obsahovat ohraničovací závorky a musí být v [konvenční podobě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="e1ee9-180">Adresy IPv6 nejsou zvláštní pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádná kanonizace.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="e1ee9-181">Neomezení povolených hostitelů může útočníkovi umožnit falšovat odkazy generované službou.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="e1ee9-182">Výchozí hodnota je `IList<string>`prázdná .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="e1ee9-183">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [serverem ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="e1ee9-184">Tato možnost se používá, když proxy/server `X-Forwarded-For` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e1ee9-185">Výchozí formát je `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="e1ee9-186">Určuje, které předávání by mělo být zpracováno.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="e1ee9-187">Seznam polí, která platí, naleznete v [výčtu předávaných záhlaví.](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="e1ee9-188">Typické hodnoty přiřazené `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`k této vlastnosti jsou .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="e1ee9-189">Výchozí hodnota je [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="e1ee9-190">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="e1ee9-191">Tato možnost se používá, když proxy/server `X-Forwarded-Host` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e1ee9-192">Výchozí formát je `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="e1ee9-193">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="e1ee9-194">Tato možnost se používá, když proxy/server `X-Forwarded-Proto` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e1ee9-195">Výchozí formát je `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="e1ee9-196">Omezuje počet položek v hlavičkách, které jsou zpracovány.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="e1ee9-197">Nastavte `null` zakázat limit, ale to by `KnownProxies` mělo `KnownNetworks` být provedeno pouze v případě, nebo jsou nakonfigurovány.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="e1ee9-198">Nastavení nehodnoty`null` je preventivní opatření (ale nikoli záruka) k ochraně před nesprávně nakonfigurovanými proxy servery a škodlivými požadavky přicházejícími z bočních kanálů v síti.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="e1ee9-199">Předávané hlavičky Middleware zpracovává hlavičky v opačném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="e1ee9-200">Pokud je použita výchozí hodnota (`1`), je zpracována pouze hodnota zcela `ForwardLimit` vpravo ze záhlaví, pokud není zvýšena hodnota.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="e1ee9-201">Výchozí formát je `1`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="e1ee9-202">Rozsahy adres známých sítí, ze kterých mají být přijímána předávaná záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="e1ee9-203">Poskytněte rozsahy IP adres pomocí zápisu classless interdomain routing (CIDR).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="e1ee9-204">Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1`(například v ipv4 reprezentovaném v ipv6 jako ).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="e1ee9-205">Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e1ee9-206">Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="e1ee9-207">Další informace naleznete v [části Konfigurace adresy IPv4 reprezentované jako adresa Protokolu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="e1ee9-208">Výchozí hodnota `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> je> obsahující jednu položku pro aplikaci `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="e1ee9-209">Adresy známých proxy serverů, ze kterých mají být přijímána předávaná záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="e1ee9-210">Slouží `KnownProxies` k určení přesných shod IP adres.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="e1ee9-211">Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1`(například v ipv4 reprezentovaném v ipv6 jako ).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="e1ee9-212">Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e1ee9-213">Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="e1ee9-214">Další informace naleznete v [části Konfigurace adresy IPv4 reprezentované jako adresa Protokolu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="e1ee9-215">Výchozí hodnota `IList` \< <xref:System.Net.IPAddress> je> obsahující jednu položku pro aplikaci `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="e1ee9-216">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="e1ee9-217">Výchozí formát je `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="e1ee9-218">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="e1ee9-219">Výchozí formát je `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="e1ee9-220">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="e1ee9-221">Výchozí formát je `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="e1ee9-222">Vyžadovat, aby byl počet hodnot záhlaví synchronizován mezi zpracovávané hlavičky [Předvoj.](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="e1ee9-223">Výchozí hodnota v ASP.NET Core `true`1.x je .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="e1ee9-224">Výchozí hodnota v ASP.NET jádrem `false`2.0 nebo novějším je .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="e1ee9-225">Scénáře a případy použití</span><span class="sxs-lookup"><span data-stu-id="e1ee9-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="e1ee9-226">Pokud není možné přidat předané hlavičky a všechny požadavky jsou zabezpečené</span><span class="sxs-lookup"><span data-stu-id="e1ee9-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="e1ee9-227">V některých případech nemusí být možné přidat předávané záhlaví k požadavkům proxied do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="e1ee9-228">Pokud proxy server vynucuje, že všechny veřejné externí požadavky `Startup.Configure` jsou HTTPS, schéma lze ručně nastavit před použitím libovolného typu middleware:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="e1ee9-229">Tento kód lze zakázat s proměnnou prostředí nebo jiné nastavení konfigurace ve vývojovém nebo pracovním prostředí.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="e1ee9-230">Řešení základny cesty a proxy, které mění cestu požadavku</span><span class="sxs-lookup"><span data-stu-id="e1ee9-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="e1ee9-231">Některé proxy servery předat cestu beze změny, ale se základní cestu aplikace, která by měla být odebrána tak, aby směrování funguje správně.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="e1ee9-232">[Middleware UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) rozdělí cestu na [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace na [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="e1ee9-233">Pokud `/foo` je základní cesta aplikace pro `/foo/api/1`cestu proxy předávané jako , middleware `Request.PathBase` nastaví `/foo` a `Request.Path` `/api/1` s následujícím příkazem:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="e1ee9-234">Původní cesta a základna cesty jsou znovu použity, když middleware je volána znovu v opačném směru.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="e1ee9-235">Další informace o zpracování objednávek <xref:fundamentals/middleware/index>middlewaru naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="e1ee9-236">Pokud proxy server ořízne cestu `/foo/api/1` (například předávání na `/api/1`), opravte přesměrování a odkazy nastavením [vlastnosti PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) požadavku:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="e1ee9-237">Pokud proxy server přidává data cesty, zahoďte část cesty a <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> opravte přesměrování <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> a odkazy pomocí vlastnosti a přiřazováním k ní:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="e1ee9-238">Konfigurace pro proxy server, který používá různé názvy záhlaví</span><span class="sxs-lookup"><span data-stu-id="e1ee9-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="e1ee9-239">Pokud proxy server nepoužívá záhlaví `X-Forwarded-For` s `X-Forwarded-Proto` názvem a předává informace o adrese/portu proxy a původním schématu, nastavte možnosti <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby odpovídaly názvům záhlaví používaným proxy serverem:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="e1ee9-240">Konfigurace adresy IPv4 reprezentované jako adresa IPv6</span><span class="sxs-lookup"><span data-stu-id="e1ee9-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="e1ee9-241">Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1` (například v ipv4 reprezentovaném v ipv6 jako nebo `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="e1ee9-242">Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e1ee9-243">Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="e1ee9-244">V následujícím příkladu je do `KnownNetworks` seznamu ve formátu IPv6 přidána síťová adresa, která dodává předávaná záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="e1ee9-245">Adresa IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="e1ee9-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="e1ee9-246">Převedená adresa IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="e1ee9-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="e1ee9-247">Převedená délka předpony: 104</span><span class="sxs-lookup"><span data-stu-id="e1ee9-247">Converted prefix length: 104</span></span>

<span data-ttu-id="e1ee9-248">Adresu můžete zadat také v šestnáctkovém formátu`10.11.12.1` (reprezentované `::ffff:0a0b:0c01`v iPv6 jako).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="e1ee9-249">Při převodu adresy IPv4 na IPv6 přidejte 96 do`8` délky předpony CIDR `::ffff:` (v příkladu) tak, aby se zohlednila další předpona IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="e1ee9-250">Předání systému pro reverzní proxy servery Linux a jiné systémy iis</span><span class="sxs-lookup"><span data-stu-id="e1ee9-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="e1ee9-251">Aplikace, <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> které <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> volají a umisťují web do nekonečné smyčky, pokud jsou nasazeny do služby Azure Linux App Service, virtuálního počítače Azure Linux (VM) nebo za jakýmkoli jiným reverzním proxy serverem kromě Služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="e1ee9-252">TLS je ukončena reverzní proxy server a Kestrel není informován o správné schéma požadavků.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="e1ee9-253">OAuth a OIDC také nezdaří v této konfiguraci, protože generují nesprávné přesměrování.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="e1ee9-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>přidává a konfiguruje přeposílané záhlaví Middleware při spuštění za IIS, ale neexistuje žádná odpovídající automatická konfigurace pro Linux (Apache nebo Nginx integrace).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="e1ee9-255">Chcete-li předávat schéma z proxy serveru ve scénářích mimo službu IIS, přidejte a nakonfigurujte middleware předávaných záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="e1ee9-256">V `Startup.ConfigureServices`, použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="e1ee9-257">Předávání certifikátů</span><span class="sxs-lookup"><span data-stu-id="e1ee9-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="e1ee9-258">Azure</span><span class="sxs-lookup"><span data-stu-id="e1ee9-258">Azure</span></span>

<span data-ttu-id="e1ee9-259">Pokud chcete nakonfigurovat službu Azure App Service pro předávání certifikátů, přečtěte si informace [o konfiguraci vzájemného ověřování TLS pro službu Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="e1ee9-260">Následující pokyny se týkající se konfigurace aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="e1ee9-261">V `Startup.Configure`oblasti přidejte před volání `app.UseAuthentication();`mj.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="e1ee9-262">Nakonfigurujte Middleware pro předávání certifikátů a zadejte název záhlaví, který Azure používá.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="e1ee9-263">V `Startup.ConfigureServices`aplikaci přidejte následující kód pro konfiguraci záhlaví, ze kterého middleware vytváří certifikát:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="e1ee9-264">Další webové proxy servery</span><span class="sxs-lookup"><span data-stu-id="e1ee9-264">Other web proxies</span></span>

<span data-ttu-id="e1ee9-265">Pokud se používá proxy server, který není Službou IIS nebo směrováním žádostí o žádosti o aplikační aplikace služby Azure App Service (ARR), nakonfigurujte proxy server tak, aby předal certifikát, který obdržel v hlavičce HTTP.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="e1ee9-266">V `Startup.Configure`oblasti přidejte před volání `app.UseAuthentication();`mj.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="e1ee9-267">Nakonfigurujte middleware pro předávání certifikátů tak, aby zadával název záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="e1ee9-268">V `Startup.ConfigureServices`aplikaci přidejte následující kód pro konfiguraci záhlaví, ze kterého middleware vytváří certifikát:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="e1ee9-269">Pokud proxy není base64-kódování certifikátu (jako je tomu v případě `HeaderConverter` Nginx), nastavte možnost.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="e1ee9-270">Vezměme si `Startup.ConfigureServices`následující příklad v :</span><span class="sxs-lookup"><span data-stu-id="e1ee9-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="e1ee9-271">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="e1ee9-271">Troubleshoot</span></span>

<span data-ttu-id="e1ee9-272">Pokud záhlaví nejsou předávána podle očekávání, povolte [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e1ee9-273">Pokud protokoly neposkytují dostatečné informace k řešení problému, výčet záhlaví požadavku přijaté serverem.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="e1ee9-274">Pomocí vřádkového middlewaru zapíšete záhlaví požadavků do odpovědi na aplikaci nebo zaznamenejte záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="e1ee9-275">Chcete-li napište záhlaví do odpovědi aplikace, umístěte následující inline middleware <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`terminálu ihned po volání do aplikace :</span><span class="sxs-lookup"><span data-stu-id="e1ee9-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="e1ee9-276">Můžete zapisovat do protokolů namísto těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="e1ee9-277">Zápis do protokolů umožňuje webu fungovat normálně při ladění.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="e1ee9-278">Chcete-li zapsat protokoly spíše než do těla odpovědi:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="e1ee9-279">Vložte `ILogger<Startup>` `Startup` do třídy, jak je popsáno v [vytvořit protokoly v startupu](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="e1ee9-280">Ihned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> souboru na `Startup.Configure`místo umístěte následující vřaditý middleware.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="e1ee9-281">Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty `X-Original-{For|Proto|Host}`přesunuty do .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="e1ee9-282">Pokud je v dané hlavičce více hodnot, middleware s předaným záhlavím zpracuje záhlaví v opačném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="e1ee9-283">Výchozí `ForwardLimit` hodnota `1` je (jedna), takže je zpracována pouze hodnota zcela `ForwardLimit` vpravo ze záhlaví, pokud není zvýšena hodnota.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="e1ee9-284">Původní vzdálená ip adresa požadavku musí `KnownProxies` odpovídat položce v seznamu nebo `KnownNetworks` před zpracováním předaných záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="e1ee9-285">To omezuje falšování záhlaví tím, že nepřijímá servery pro předávání z nedůvěryhodných serverů proxy.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="e1ee9-286">Pokud je zjištěn neznámý proxy server, protokolování označuje adresu proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="e1ee9-287">V předchozím příkladu je 10.0.0.100 proxy server.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="e1ee9-288">Pokud je server důvěryhodným proxy serverem, přidejte adresu IP serveru `KnownNetworks`do `Startup.ConfigureServices` `KnownProxies` aplikace .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e1ee9-289">Další informace naleznete v části [Možnosti middlewaru s předávaných záhlaví.](#forwarded-headers-middleware-options)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="e1ee9-290">Povolit pouze důvěryhodné proxy servery a sítě předávat záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="e1ee9-291">V opačném případě jsou možné útoky [spoofing IP.](https://www.iplocation.net/ip-spoofing)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1ee9-292">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e1ee9-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="e1ee9-293">Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET základní ohrožení zabezpečení oprávnění</span><span class="sxs-lookup"><span data-stu-id="e1ee9-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e1ee9-294">V doporučené konfiguraci pro ASP.NET Core je aplikace hostována pomocí core modulu IIS/ASP.NET Core Module, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="e1ee9-295">Proxy servery, vykladače zatížení a další síťová zařízení často zakrývají informace o požadavku před tím, než se dostane do aplikace:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="e1ee9-296">Pokud jsou požadavky HTTPS proxied přes HTTP, původní schéma (HTTPS) je ztracena a musí být předány v záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="e1ee9-297">Vzhledem k tomu, že aplikace obdrží požadavek z proxy serveru a není jeho skutečný zdroj v síti Internet nebo podnikové sítě, původní klient IP adresa musí být také předány v záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="e1ee9-298">Tyto informace mohou být důležité při zpracování požadavků, například při přesměrování, ověřování, generování propojení, vyhodnocení zásad a geografickém umístění klienta.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="e1ee9-299">Předávaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="e1ee9-299">Forwarded headers</span></span>

<span data-ttu-id="e1ee9-300">Podle konvence proxy předávat informace v hlavičkách HTTP.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="e1ee9-301">Hlavička</span><span class="sxs-lookup"><span data-stu-id="e1ee9-301">Header</span></span> | <span data-ttu-id="e1ee9-302">Popis</span><span class="sxs-lookup"><span data-stu-id="e1ee9-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="e1ee9-303">X-Forwarded-Pro</span><span class="sxs-lookup"><span data-stu-id="e1ee9-303">X-Forwarded-For</span></span> | <span data-ttu-id="e1ee9-304">Obsahuje informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="e1ee9-305">Tento parametr může obsahovat IP adresy (a volitelně čísla portů).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="e1ee9-306">V řetězci proxy serverů první parametr označuje klienta, kde byl požadavek poprvé proveden.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="e1ee9-307">Následují následné proxy identifikátory.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="e1ee9-308">Poslední proxy server v řetězci není v seznamu parametrů.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="e1ee9-309">Adresa IP posledního serveru proxy a volitelně číslo portu jsou k dispozici jako vzdálená adresa IP ve vrstvě přenosu.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="e1ee9-310">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="e1ee9-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="e1ee9-311">Hodnota původního schématu (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="e1ee9-312">Hodnota může být také seznam schémat, pokud požadavek prošel více proxy servery.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="e1ee9-313">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="e1ee9-313">X-Forwarded-Host</span></span> | <span data-ttu-id="e1ee9-314">Původní hodnota pole záhlaví Hostitele.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-314">The original value of the Host header field.</span></span> <span data-ttu-id="e1ee9-315">Proxy servery obvykle nemění záhlaví Hostitele.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="e1ee9-316">Informace o chybě zabezpečení, která ovlivňuje systémy, kde proxy server neověřuje nebo neomezuje záhlaví hostitele na známé dobré hodnoty, naleznete v [tématu Microsoft Security Advisory CVE-2018-0787.](https://github.com/aspnet/Announcements/issues/295)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="e1ee9-317">Middleware S předaná záhlaví z balíčku [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a <xref:Microsoft.AspNetCore.Http.HttpContext>vyplní přidružená pole na webu .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="e1ee9-318">Middleware aktualizace:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-318">The middleware updates:</span></span>

* <span data-ttu-id="e1ee9-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set `X-Forwarded-For` pomocí hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="e1ee9-320">Další nastavení ovlivňují nastavení `RemoteIpAddress`middlewaru .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="e1ee9-321">Podrobnosti naleznete v možnostech [middlewaru předávaných záhlaví](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="e1ee9-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set `X-Forwarded-Proto` pomocí hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="e1ee9-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set `X-Forwarded-Host` pomocí hodnoty hlavičky.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="e1ee9-324">Lze nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru s předanou hlavičkou.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="e1ee9-325">Výchozí nastavení jsou:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-325">The default settings are:</span></span>

* <span data-ttu-id="e1ee9-326">Existuje pouze *jeden proxy server* mezi aplikací a zdrojem požadavků.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="e1ee9-327">Pouze adresy zpětné smyčky jsou konfigurovány pro známé proxy servery a známé sítě.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="e1ee9-328">Předávaná záhlaví `X-Forwarded-For` `X-Forwarded-Proto`jsou pojmenována a .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="e1ee9-329">Ne všechna síťová `X-Forwarded-For` `X-Forwarded-Proto` zařízení přidávají záhlaví a bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="e1ee9-330">Pokud proxied požadavky neobsahují tyto hlavičky, když se dostanou do aplikace, přečtěte si pokyny výrobce zařízení.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="e1ee9-331">Pokud zařízení používá jiné `X-Forwarded-For` názvy záhlaví než a `X-Forwarded-Proto`, nastavte možnosti <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby odpovídaly názvům záhlaví používaným zařízením.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="e1ee9-332">Další informace naleznete v tématech [Možnosti middlewaru předávaných záhlaví](#forwarded-headers-middleware-options) a [Konfigurace pro proxy server, který používá různé názvy záhlaví](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="e1ee9-333">IIS/IIS Express a základní modul ASP.NET</span><span class="sxs-lookup"><span data-stu-id="e1ee9-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="e1ee9-334">Middleware s předaná záhlaví je ve výchozím nastavení povolen [middlewarem integrace služby IIS,](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) pokud je aplikace hostována mimo proces za [službou](xref:host-and-deploy/iis/index#out-of-process-hosting-model) IIS a ASP.NET core modulem.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="e1ee9-335">Předaných záhlaví Middleware je aktivován pro spuštění jako první v middleware potrubí s omezenou konfiguraci specifickou pro ASP.NET Core Module z důvodu důvěry obavy s předanými záhlaví (například [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="e1ee9-336">Middleware je nakonfigurován `X-Forwarded-For` tak, aby předal a `X-Forwarded-Proto` záhlaví a je omezen na jeden proxy localhost.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="e1ee9-337">Pokud je požadována další konfigurace, podívejte se na [možnosti middlewaru předávaných záhlaví](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e1ee9-338">Další scénáře proxy serveru a vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="e1ee9-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e1ee9-339">Mimo použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimoproces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), není middleware předaných záhlaví ve výchozím nastavení povoleno.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="e1ee9-340">Middleware s předanými záhlavími musí být povolen, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>aby aplikace zpracovala předávaná záhlaví pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="e1ee9-341">Po povolení middlewaru, pokud middlewar u <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nezadaných nejsou, jsou výchozí [položky ForwardedHeaders.ForwardedHeaders.None](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) . [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="e1ee9-342">Nakonfigurujte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> middleware `X-Forwarded-For` `X-Forwarded-Proto` s pro `Startup.ConfigureServices`předávání a záhlaví v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e1ee9-343">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu `Startup.Configure` v před voláním jinémiddleware:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="e1ee9-344">Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` metodě <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>rozšíření nebo přímo k ní , výchozí hlavičky pro předávání jsou [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="e1ee9-345">Vlastnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> musí být nakonfigurována s hlavičkami dopředu.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="e1ee9-346">Konfigurace Nginx</span><span class="sxs-lookup"><span data-stu-id="e1ee9-346">Nginx configuration</span></span>

<span data-ttu-id="e1ee9-347">Chcete-li `X-Forwarded-For` `X-Forwarded-Proto` záhlaví a <xref:host-and-deploy/linux-nginx#configure-nginx>předat dál, přečtěte si informace o tom, zda neobsahuje hlavní a</span><span class="sxs-lookup"><span data-stu-id="e1ee9-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="e1ee9-348">Další informace naleznete v [tématu NGINX: Použití hlavičky Přeposílané](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="e1ee9-349">Konfigurace Apache</span><span class="sxs-lookup"><span data-stu-id="e1ee9-349">Apache configuration</span></span>

<span data-ttu-id="e1ee9-350">`X-Forwarded-For`(viz Apache [Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="e1ee9-351">Informace o tom, `X-Forwarded-Proto` jak předat <xref:host-and-deploy/linux-apache#configure-apache>záhlaví dál, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="e1ee9-352">Možnosti middlewaru předávaných záhlaví</span><span class="sxs-lookup"><span data-stu-id="e1ee9-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="e1ee9-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>chování middlewaru Předávaných záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="e1ee9-354">Následující příklad změní výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-354">The following example changes the default values:</span></span>

* <span data-ttu-id="e1ee9-355">Omezte počet položek v předaných záhlavích na `2`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="e1ee9-356">Přidejte známou `127.0.10.1`adresu proxy aplikace .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="e1ee9-357">Změňte předaný název `X-Forwarded-For` záhlaví `X-Forwarded-For-My-Custom-Header-Name`z výchozího na .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="e1ee9-358">Možnost</span><span class="sxs-lookup"><span data-stu-id="e1ee9-358">Option</span></span> | <span data-ttu-id="e1ee9-359">Popis</span><span class="sxs-lookup"><span data-stu-id="e1ee9-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="e1ee9-360">Omezí hostitele záhlavím `X-Forwarded-Host` na uvedené hodnoty.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="e1ee9-361">Hodnoty jsou porovnány pomocí ordinal-ignore-case.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="e1ee9-362">Čísla portů musí být vyloučena.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="e1ee9-363">Pokud je seznam prázdný, jsou povoleni všichni hostitelé.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="e1ee9-364">Zástupný znak `*` nejvyšší úrovně umožňuje všem neprázdným hostitelům.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="e1ee9-365">Zástupné znaky subdomény jsou povoleny, ale neodpovídají kořenové doméně.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="e1ee9-366">Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale `contoso.com`ne kořenové doméně .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="e1ee9-367">Názvy hostitelů Unicode jsou [povoleny,](https://tools.ietf.org/html/rfc3492) ale jsou převedeny na Punycode pro porovnávání.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="e1ee9-368">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí obsahovat ohraničovací závorky a musí být v [konvenční podobě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="e1ee9-369">Adresy IPv6 nejsou zvláštní pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádná kanonizace.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="e1ee9-370">Neomezení povolených hostitelů může útočníkovi umožnit falšovat odkazy generované službou.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="e1ee9-371">Výchozí hodnota je `IList<string>`prázdná .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="e1ee9-372">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [serverem ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="e1ee9-373">Tato možnost se používá, když proxy/server `X-Forwarded-For` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e1ee9-374">Výchozí formát je `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="e1ee9-375">Určuje, které předávání by mělo být zpracováno.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="e1ee9-376">Seznam polí, která platí, naleznete v [výčtu předávaných záhlaví.](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="e1ee9-377">Typické hodnoty přiřazené `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`k této vlastnosti jsou .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="e1ee9-378">Výchozí hodnota je [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="e1ee9-379">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="e1ee9-380">Tato možnost se používá, když proxy/server `X-Forwarded-Host` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e1ee9-381">Výchozí formát je `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="e1ee9-382">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="e1ee9-383">Tato možnost se používá, když proxy/server `X-Forwarded-Proto` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="e1ee9-384">Výchozí formát je `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="e1ee9-385">Omezuje počet položek v hlavičkách, které jsou zpracovány.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="e1ee9-386">Nastavte `null` zakázat limit, ale to by `KnownProxies` mělo `KnownNetworks` být provedeno pouze v případě, nebo jsou nakonfigurovány.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="e1ee9-387">Nastavení nehodnoty`null` je preventivní opatření (ale nikoli záruka) k ochraně před nesprávně nakonfigurovanými proxy servery a škodlivými požadavky přicházejícími z bočních kanálů v síti.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="e1ee9-388">Předávané hlavičky Middleware zpracovává hlavičky v opačném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="e1ee9-389">Pokud je použita výchozí hodnota (`1`), je zpracována pouze hodnota zcela `ForwardLimit` vpravo ze záhlaví, pokud není zvýšena hodnota.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="e1ee9-390">Výchozí formát je `1`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="e1ee9-391">Rozsahy adres známých sítí, ze kterých mají být přijímána předávaná záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="e1ee9-392">Poskytněte rozsahy IP adres pomocí zápisu classless interdomain routing (CIDR).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="e1ee9-393">Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1`(například v ipv4 reprezentovaném v ipv6 jako ).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="e1ee9-394">Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e1ee9-395">Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="e1ee9-396">Další informace naleznete v [části Konfigurace adresy IPv4 reprezentované jako adresa Protokolu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="e1ee9-397">Výchozí hodnota `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> je> obsahující jednu položku pro aplikaci `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="e1ee9-398">Adresy známých proxy serverů, ze kterých mají být přijímána předávaná záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="e1ee9-399">Slouží `KnownProxies` k určení přesných shod IP adres.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="e1ee9-400">Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1`(například v ipv4 reprezentovaném v ipv6 jako ).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="e1ee9-401">Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e1ee9-402">Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="e1ee9-403">Další informace naleznete v [části Konfigurace adresy IPv4 reprezentované jako adresa Protokolu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="e1ee9-404">Výchozí hodnota `IList` \< <xref:System.Net.IPAddress> je> obsahující jednu položku pro aplikaci `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="e1ee9-405">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="e1ee9-406">Výchozí formát je `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="e1ee9-407">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="e1ee9-408">Výchozí formát je `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="e1ee9-409">Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="e1ee9-410">Výchozí formát je `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="e1ee9-411">Vyžadovat, aby byl počet hodnot záhlaví synchronizován mezi zpracovávané hlavičky [Předvoj.](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="e1ee9-412">Výchozí hodnota v ASP.NET Core `true`1.x je .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="e1ee9-413">Výchozí hodnota v ASP.NET jádrem `false`2.0 nebo novějším je .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="e1ee9-414">Scénáře a případy použití</span><span class="sxs-lookup"><span data-stu-id="e1ee9-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="e1ee9-415">Pokud není možné přidat předané hlavičky a všechny požadavky jsou zabezpečené</span><span class="sxs-lookup"><span data-stu-id="e1ee9-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="e1ee9-416">V některých případech nemusí být možné přidat předávané záhlaví k požadavkům proxied do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="e1ee9-417">Pokud proxy server vynucuje, že všechny veřejné externí požadavky `Startup.Configure` jsou HTTPS, schéma lze ručně nastavit před použitím libovolného typu middleware:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="e1ee9-418">Tento kód lze zakázat s proměnnou prostředí nebo jiné nastavení konfigurace ve vývojovém nebo pracovním prostředí.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="e1ee9-419">Řešení základny cesty a proxy, které mění cestu požadavku</span><span class="sxs-lookup"><span data-stu-id="e1ee9-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="e1ee9-420">Některé proxy servery předat cestu beze změny, ale se základní cestu aplikace, která by měla být odebrána tak, aby směrování funguje správně.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="e1ee9-421">[Middleware UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) rozdělí cestu na [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace na [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="e1ee9-422">Pokud `/foo` je základní cesta aplikace pro `/foo/api/1`cestu proxy předávané jako , middleware `Request.PathBase` nastaví `/foo` a `Request.Path` `/api/1` s následujícím příkazem:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="e1ee9-423">Původní cesta a základna cesty jsou znovu použity, když middleware je volána znovu v opačném směru.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="e1ee9-424">Další informace o zpracování objednávek <xref:fundamentals/middleware/index>middlewaru naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="e1ee9-425">Pokud proxy server ořízne cestu `/foo/api/1` (například předávání na `/api/1`), opravte přesměrování a odkazy nastavením [vlastnosti PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) požadavku:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="e1ee9-426">Pokud proxy server přidává data cesty, zahoďte část cesty a <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> opravte přesměrování <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> a odkazy pomocí vlastnosti a přiřazováním k ní:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="e1ee9-427">Konfigurace pro proxy server, který používá různé názvy záhlaví</span><span class="sxs-lookup"><span data-stu-id="e1ee9-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="e1ee9-428">Pokud proxy server nepoužívá záhlaví `X-Forwarded-For` s `X-Forwarded-Proto` názvem a předává informace o adrese/portu proxy a původním schématu, nastavte možnosti <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby odpovídaly názvům záhlaví používaným proxy serverem:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="e1ee9-429">Konfigurace adresy IPv4 reprezentované jako adresa IPv6</span><span class="sxs-lookup"><span data-stu-id="e1ee9-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="e1ee9-430">Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1` (například v ipv4 reprezentovaném v ipv6 jako nebo `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="e1ee9-431">Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="e1ee9-432">Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="e1ee9-433">V následujícím příkladu je do `KnownNetworks` seznamu ve formátu IPv6 přidána síťová adresa, která dodává předávaná záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="e1ee9-434">Adresa IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="e1ee9-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="e1ee9-435">Převedená adresa IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="e1ee9-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="e1ee9-436">Převedená délka předpony: 104</span><span class="sxs-lookup"><span data-stu-id="e1ee9-436">Converted prefix length: 104</span></span>

<span data-ttu-id="e1ee9-437">Adresu můžete zadat také v šestnáctkovém formátu`10.11.12.1` (reprezentované `::ffff:0a0b:0c01`v iPv6 jako).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="e1ee9-438">Při převodu adresy IPv4 na IPv6 přidejte 96 do`8` délky předpony CIDR `::ffff:` (v příkladu) tak, aby se zohlednila další předpona IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="e1ee9-439">Předání systému pro reverzní proxy servery Linux a jiné systémy iis</span><span class="sxs-lookup"><span data-stu-id="e1ee9-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="e1ee9-440">Aplikace, <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> které <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> volají a umisťují web do nekonečné smyčky, pokud jsou nasazeny do služby Azure Linux App Service, virtuálního počítače Azure Linux (VM) nebo za jakýmkoli jiným reverzním proxy serverem kromě Služby IIS.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="e1ee9-441">TLS je ukončena reverzní proxy server a Kestrel není informován o správné schéma požadavků.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="e1ee9-442">OAuth a OIDC také nezdaří v této konfiguraci, protože generují nesprávné přesměrování.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="e1ee9-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>přidává a konfiguruje přeposílané záhlaví Middleware při spuštění za IIS, ale neexistuje žádná odpovídající automatická konfigurace pro Linux (Apache nebo Nginx integrace).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="e1ee9-444">Chcete-li předávat schéma z proxy serveru ve scénářích mimo službu IIS, přidejte a nakonfigurujte middleware předávaných záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="e1ee9-445">V `Startup.ConfigureServices`, použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-445">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="e1ee9-446">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="e1ee9-446">Troubleshoot</span></span>

<span data-ttu-id="e1ee9-447">Pokud záhlaví nejsou předávána podle očekávání, povolte [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e1ee9-448">Pokud protokoly neposkytují dostatečné informace k řešení problému, výčet záhlaví požadavku přijaté serverem.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="e1ee9-449">Pomocí vřádkového middlewaru zapíšete záhlaví požadavků do odpovědi na aplikaci nebo zaznamenejte záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="e1ee9-450">Chcete-li napište záhlaví do odpovědi aplikace, umístěte následující inline middleware <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`terminálu ihned po volání do aplikace :</span><span class="sxs-lookup"><span data-stu-id="e1ee9-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="e1ee9-451">Můžete zapisovat do protokolů namísto těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="e1ee9-452">Zápis do protokolů umožňuje webu fungovat normálně při ladění.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="e1ee9-453">Chcete-li zapsat protokoly spíše než do těla odpovědi:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="e1ee9-454">Vložte `ILogger<Startup>` `Startup` do třídy, jak je popsáno v [vytvořit protokoly v startupu](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="e1ee9-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="e1ee9-455">Ihned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> souboru na `Startup.Configure`místo umístěte následující vřaditý middleware.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="e1ee9-456">Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty `X-Original-{For|Proto|Host}`přesunuty do .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="e1ee9-457">Pokud je v dané hlavičce více hodnot, middleware s předaným záhlavím zpracuje záhlaví v opačném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="e1ee9-458">Výchozí `ForwardLimit` hodnota `1` je (jedna), takže je zpracována pouze hodnota zcela `ForwardLimit` vpravo ze záhlaví, pokud není zvýšena hodnota.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="e1ee9-459">Původní vzdálená ip adresa požadavku musí `KnownProxies` odpovídat položce v seznamu nebo `KnownNetworks` před zpracováním předaných záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="e1ee9-460">To omezuje falšování záhlaví tím, že nepřijímá servery pro předávání z nedůvěryhodných serverů proxy.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="e1ee9-461">Pokud je zjištěn neznámý proxy server, protokolování označuje adresu proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="e1ee9-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="e1ee9-462">V předchozím příkladu je 10.0.0.100 proxy server.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="e1ee9-463">Pokud je server důvěryhodným proxy serverem, přidejte adresu IP serveru `KnownNetworks`do `Startup.ConfigureServices` `KnownProxies` aplikace .</span><span class="sxs-lookup"><span data-stu-id="e1ee9-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e1ee9-464">Další informace naleznete v části [Možnosti middlewaru s předávaných záhlaví.](#forwarded-headers-middleware-options)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="e1ee9-465">Povolit pouze důvěryhodné proxy servery a sítě předávat záhlaví.</span><span class="sxs-lookup"><span data-stu-id="e1ee9-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="e1ee9-466">V opačném případě jsou možné útoky [spoofing IP.](https://www.iplocation.net/ip-spoofing)</span><span class="sxs-lookup"><span data-stu-id="e1ee9-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e1ee9-467">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e1ee9-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="e1ee9-468">Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET základní ohrožení zabezpečení oprávnění</span><span class="sxs-lookup"><span data-stu-id="e1ee9-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

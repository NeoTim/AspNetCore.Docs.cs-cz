---
title: Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení
author: guardrex
description: Přečtěte si o konfiguraci pro aplikace hostované za servery proxy a nástroje pro vyrovnávání zatížení, které často překrývají důležité informace o žádostech.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/12/2019
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 4f04e6cae120ee88734855252542e2bfc2f194a0
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "67856174"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="120ac-103">Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="120ac-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="120ac-104">Od [Luke Latham](https://github.com/guardrex) a [Chris Rossův](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="120ac-104">By [Luke Latham](https://github.com/guardrex) and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="120ac-105">V Doporučené konfiguraci pro ASP.NET Core se aplikace hostuje pomocí IIS/ASP. Modul NET Core, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="120ac-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="120ac-106">Proxy servery, nástroje pro vyrovnávání zatížení a další síťová zařízení často překrývají informace o žádosti předtím, než dosáhnou aplikace:</span><span class="sxs-lookup"><span data-stu-id="120ac-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="120ac-107">Pokud jsou požadavky HTTPS proxy serverem přes HTTP, původní schéma (HTTPS) se ztratí a musí se přenášet v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="120ac-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="120ac-108">Vzhledem k tomu, že aplikace obdrží požadavek od proxy serveru a ne jeho skutečný zdroj v Internetu nebo podnikové síti, musí být zdrojová adresa IP klienta předána také v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="120ac-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="120ac-109">Tyto informace můžou být důležité ve zpracování žádostí, například v přesměrování, ověřování, generování odkazů, vyhodnocení zásad a geografická poloha klienta.</span><span class="sxs-lookup"><span data-stu-id="120ac-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="120ac-110">Předávaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="120ac-110">Forwarded headers</span></span>

<span data-ttu-id="120ac-111">Podle konvence proxy předávají informace v hlavičkách protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="120ac-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="120ac-112">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="120ac-112">Header</span></span> | <span data-ttu-id="120ac-113">Popis</span><span class="sxs-lookup"><span data-stu-id="120ac-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="120ac-114">X-předané – pro</span><span class="sxs-lookup"><span data-stu-id="120ac-114">X-Forwarded-For</span></span> | <span data-ttu-id="120ac-115">Uchovává informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="120ac-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="120ac-116">Tento parametr může obsahovat IP adresy (a volitelně také čísla portů).</span><span class="sxs-lookup"><span data-stu-id="120ac-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="120ac-117">V řetězci proxy serverů označuje první parametr klienta, ve kterém se žádost poprvé nastavila.</span><span class="sxs-lookup"><span data-stu-id="120ac-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="120ac-118">Následující identifikátory proxy následují.</span><span class="sxs-lookup"><span data-stu-id="120ac-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="120ac-119">Poslední proxy server v řetězci není uveden v seznamu parametrů.</span><span class="sxs-lookup"><span data-stu-id="120ac-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="120ac-120">Poslední IP adresa proxy serveru a volitelně je číslo portu, které je k dispozici jako Vzdálená IP adresa v transportní vrstvě.</span><span class="sxs-lookup"><span data-stu-id="120ac-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="120ac-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="120ac-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="120ac-122">Hodnota původního schématu (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="120ac-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="120ac-123">Hodnota může být také seznamem schémat, pokud je požadavek procházen více proxy.</span><span class="sxs-lookup"><span data-stu-id="120ac-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="120ac-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="120ac-124">X-Forwarded-Host</span></span> | <span data-ttu-id="120ac-125">Původní hodnota pole hlavičky hostitele</span><span class="sxs-lookup"><span data-stu-id="120ac-125">The original value of the Host header field.</span></span> <span data-ttu-id="120ac-126">Servery proxy většinou nemění hlavičku hostitele.</span><span class="sxs-lookup"><span data-stu-id="120ac-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="120ac-127">Viz informační [zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pro informace o chybě zabezpečení zvýšení oprávnění, která má vliv na systémy, ve kterých server proxy neověřuje nebo omezuje hlavičky hostitele na známé správné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="120ac-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="120ac-128">Middleware předávaných hlaviček z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a vyplní související pole <xref:Microsoft.AspNetCore.Http.HttpContext>v.</span><span class="sxs-lookup"><span data-stu-id="120ac-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="120ac-129">Aktualizace middlewaru:</span><span class="sxs-lookup"><span data-stu-id="120ac-129">The middleware updates:</span></span>

* <span data-ttu-id="120ac-130">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; sada s použitím `X-Forwarded-For` hodnoty hlavičky.</span><span class="sxs-lookup"><span data-stu-id="120ac-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="120ac-131">Další nastavení mají vliv na to, `RemoteIpAddress`jak middleware nastaví.</span><span class="sxs-lookup"><span data-stu-id="120ac-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="120ac-132">Podrobnosti najdete v tématu [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="120ac-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="120ac-133">[HttpContext. Request. schématu](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; nastaveného pomocí `X-Forwarded-Proto` hodnoty hlavičky.</span><span class="sxs-lookup"><span data-stu-id="120ac-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="120ac-134">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; sada s použitím `X-Forwarded-Host` hodnoty hlavičky.</span><span class="sxs-lookup"><span data-stu-id="120ac-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="120ac-135">Je možné nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru u předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="120ac-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="120ac-136">Výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="120ac-136">The default settings are:</span></span>

* <span data-ttu-id="120ac-137">Mezi aplikací a zdrojem požadavků je jenom *jeden proxy server* .</span><span class="sxs-lookup"><span data-stu-id="120ac-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="120ac-138">Pro známé proxy servery a známé sítě jsou nakonfigurovány pouze adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="120ac-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="120ac-139">Předané hlavičky jsou `X-Forwarded-For` pojmenovány `X-Forwarded-Proto`a.</span><span class="sxs-lookup"><span data-stu-id="120ac-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="120ac-140">Ne všechna síťová zařízení přidávají `X-Forwarded-For` hlavičky `X-Forwarded-Proto` a bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="120ac-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="120ac-141">Zeptejte se na pokyny výrobce zařízení, jestli proxy požadavky při přístupu do aplikace tyto hlavičky neobsahují.</span><span class="sxs-lookup"><span data-stu-id="120ac-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="120ac-142">Pokud zařízení používá jiné názvy hlaviček `X-Forwarded-For` než a `X-Forwarded-Proto`, nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> možnosti a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby odpovídaly názvům hlaviček používaným zařízením.</span><span class="sxs-lookup"><span data-stu-id="120ac-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="120ac-143">Další informace najdete v tématu [předané možnosti](#forwarded-headers-middleware-options) a konfigurace middlewarových hlaviček [pro proxy server, který používá jiné názvy hlaviček](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="120ac-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="120ac-144">Modul IIS/IIS Express a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="120ac-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="120ac-145">Middleware předávaných hlaviček je ve výchozím nastavení povolená službou [IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , když je aplikace hostovaná [mimo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) službu IIS a modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="120ac-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="120ac-146">Middleware předávaných hlaviček se aktivují, aby se nejdříve spouštěl v kanálu middleware s omezenou konfigurací určenou pro modul ASP.NET Core, protože se jedná o obavy týkající se důvěryhodnosti se předanými hlavičkami (například [falšování IP adres](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="120ac-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="120ac-147">Middleware je nakonfigurovaná pro přeposílání `X-Forwarded-For` hlaviček a `X-Forwarded-Proto` a je omezená na jeden localhost proxy.</span><span class="sxs-lookup"><span data-stu-id="120ac-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="120ac-148">Pokud se vyžaduje další konfigurace, přečtěte si téma [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="120ac-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="120ac-149">Další scénáře proxy server a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="120ac-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="120ac-150">Bez použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimo procesy](xref:host-and-deploy/iis/index#out-of-process-hosting-model)není middleware předávaných hlaviček ve výchozím nastavení povolená.</span><span class="sxs-lookup"><span data-stu-id="120ac-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="120ac-151">Middleware předaných hlaviček musí být povolená, aby aplikace zpracovala předávané hlavičky s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="120ac-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="120ac-152">Po povolení middlewaru, pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> není zadáno pro middleware, výchozí [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="120ac-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="120ac-153">Nakonfigurujte middleware s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nástrojem na `X-Forwarded-For` předáte `X-Forwarded-Proto` hlavičky a `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="120ac-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="120ac-154">Vyvolat metodu v `Startup.Configure` před voláním jiného middleware: <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*></span><span class="sxs-lookup"><span data-stu-id="120ac-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="120ac-155">Pokud nejsou zadány v `Startup.ConfigureServices` nebo přímo k metodě rozšíření s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, výchozí hlavičky pro přeposílání jsou [ForwardedHeaders. None.](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions></span><span class="sxs-lookup"><span data-stu-id="120ac-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="120ac-156"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> Vlastnost musí být nakonfigurována s hlavičkou pro přeposílání.</span><span class="sxs-lookup"><span data-stu-id="120ac-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="120ac-157">Konfigurace nginx</span><span class="sxs-lookup"><span data-stu-id="120ac-157">Nginx configuration</span></span>

<span data-ttu-id="120ac-158">Pro `X-Forwarded-For` přeposílání `X-Forwarded-Proto` hlaviček a naleznete <xref:host-and-deploy/linux-nginx#configure-nginx>informace v tématu.</span><span class="sxs-lookup"><span data-stu-id="120ac-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="120ac-159">Další informace najdete v tématu [Nginx: Používá se předávaná hlavička](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="120ac-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="120ac-160">Konfigurace Apache</span><span class="sxs-lookup"><span data-stu-id="120ac-160">Apache configuration</span></span>

<span data-ttu-id="120ac-161">`X-Forwarded-For`se přidá automaticky (viz [Apache Module mod_proxy: Záhlaví](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)žádosti reverzního proxy serveru).</span><span class="sxs-lookup"><span data-stu-id="120ac-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="120ac-162">Informace o tom, jak přeslat `X-Forwarded-Proto` hlavičku, najdete <xref:host-and-deploy/linux-apache#configure-apache>v tématu.</span><span class="sxs-lookup"><span data-stu-id="120ac-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="120ac-163">Možnosti middlewaru předávaných hlaviček</span><span class="sxs-lookup"><span data-stu-id="120ac-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="120ac-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>řízení chování middlewaru předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="120ac-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="120ac-165">Následující příklad změní výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="120ac-165">The following example changes the default values:</span></span>

* <span data-ttu-id="120ac-166">Omezte počet záznamů v předaných hlavičkách na `2`.</span><span class="sxs-lookup"><span data-stu-id="120ac-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="120ac-167">Přidejte známou adresu `127.0.10.1`proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="120ac-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="120ac-168">Změňte název předané hlavičky z výchozí `X-Forwarded-For` na. `X-Forwarded-For-My-Custom-Header-Name`</span><span class="sxs-lookup"><span data-stu-id="120ac-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="120ac-169">Možnost</span><span class="sxs-lookup"><span data-stu-id="120ac-169">Option</span></span> | <span data-ttu-id="120ac-170">Popis</span><span class="sxs-lookup"><span data-stu-id="120ac-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="120ac-171">Omezí hostitele v `X-Forwarded-Host` hlavičce na zadané hodnoty.</span><span class="sxs-lookup"><span data-stu-id="120ac-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="120ac-172">Hodnoty se porovnávají pomocí pořadí – ignorovat – případ.</span><span class="sxs-lookup"><span data-stu-id="120ac-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="120ac-173">Čísla portů musí být vyloučena.</span><span class="sxs-lookup"><span data-stu-id="120ac-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="120ac-174">Pokud je seznam prázdný, jsou povoleny všichni hostitelé.</span><span class="sxs-lookup"><span data-stu-id="120ac-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="120ac-175">Zástupný znak `*` nejvyšší úrovně umožňuje všem neprázdným hostitelům.</span><span class="sxs-lookup"><span data-stu-id="120ac-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="120ac-176">Zástupné znaky subdomény jsou povolené, ale neodpovídají kořenové doméně.</span><span class="sxs-lookup"><span data-stu-id="120ac-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="120ac-177">Například odpovídá subdoméně `*.contoso.com` `foo.contoso.com` , ale nikoli ke kořenové doméně `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="120ac-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="120ac-178">Názvy hostitelů Unicode jsou povoleny, ale jsou převedeny na [Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</span><span class="sxs-lookup"><span data-stu-id="120ac-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="120ac-179">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí zahrnovat ohraničující závorky a musí být v [konvenční formě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="120ac-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="120ac-180">IPv6 adresy nejsou speciální – použita pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádné kanonikalizace.</span><span class="sxs-lookup"><span data-stu-id="120ac-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="120ac-181">Nepodaří-li se omezit povolené hostitele, může útočníkovi umožnit přístup k falešným odkazům generovanými službou.</span><span class="sxs-lookup"><span data-stu-id="120ac-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="120ac-182">Výchozí hodnota je prázdná `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="120ac-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="120ac-183">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="120ac-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="120ac-184">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-For` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="120ac-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="120ac-185">Výchozí hodnota je `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="120ac-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="120ac-186">Určuje, které předávacíci by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="120ac-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="120ac-187">Seznam polí, která platí, najdete v tématu [ForwardedHeaders enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="120ac-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="120ac-188">Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="120ac-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="120ac-189">Výchozí hodnota je [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="120ac-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="120ac-190">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="120ac-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="120ac-191">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Host` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="120ac-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="120ac-192">Výchozí hodnota je `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="120ac-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="120ac-193">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="120ac-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="120ac-194">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Proto` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="120ac-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="120ac-195">Výchozí hodnota je `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="120ac-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="120ac-196">Omezuje počet položek v záhlavích, které jsou zpracovávány.</span><span class="sxs-lookup"><span data-stu-id="120ac-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="120ac-197">Nastavením tohoto omezení zakážete, ale to by mělo být provedeno pouze v `KnownProxies` případě `KnownNetworks` , že nebo jsou nakonfigurovány. `null`</span><span class="sxs-lookup"><span data-stu-id="120ac-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="120ac-198">`null` Nastavení nehodnoty je preventivní opatrnost (ale ne záruka) pro ochranu proti nesprávně konfigurovaným proxy a škodlivým požadavkům přicházejících ze dvoustranných kanálů v síti.</span><span class="sxs-lookup"><span data-stu-id="120ac-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="120ac-199">Middleware předávaných hlaviček zpracovává hlavičky v obráceném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="120ac-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="120ac-200">Pokud je použita výchozí hodnota`1`(), bude zpracována pouze hodnota zprava z hlaviček, pokud `ForwardLimit` hodnota není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="120ac-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="120ac-201">Výchozí hodnota je `1`.</span><span class="sxs-lookup"><span data-stu-id="120ac-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="120ac-202">Rozsahy adres známých sítí, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="120ac-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="120ac-203">Zadejte rozsahy IP adres pomocí zápisu CIDR (Classless prosměrování mezi doménami).</span><span class="sxs-lookup"><span data-stu-id="120ac-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="120ac-204">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="120ac-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="120ac-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="120ac-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="120ac-206">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="120ac-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="120ac-207">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="120ac-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="120ac-208">Výchozí `IList`hodnota je \< `IPAddress.Loopback`> obsahující jednu položku pro. <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork></span><span class="sxs-lookup"><span data-stu-id="120ac-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="120ac-209">Adresy známých proxy adres, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="120ac-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="120ac-210">Použijte `KnownProxies` k určení přesné shody IP adres.</span><span class="sxs-lookup"><span data-stu-id="120ac-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="120ac-211">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="120ac-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="120ac-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="120ac-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="120ac-213">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="120ac-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="120ac-214">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="120ac-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="120ac-215">Výchozí `IList`hodnota je \< `IPAddress.IPv6Loopback`> obsahující jednu položku pro. <xref:System.Net.IPAddress></span><span class="sxs-lookup"><span data-stu-id="120ac-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="120ac-216">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="120ac-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="120ac-217">Výchozí hodnota je `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="120ac-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="120ac-218">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="120ac-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="120ac-219">Výchozí hodnota je `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="120ac-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="120ac-220">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="120ac-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="120ac-221">Výchozí hodnota je `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="120ac-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="120ac-222">Vyžaduje, aby byl počet synchronizovaných hodnot hlaviček mezi zpracovávaným [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="120ac-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="120ac-223">Výchozí hodnota v ASP.NET Core 1. x je `true`.</span><span class="sxs-lookup"><span data-stu-id="120ac-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="120ac-224">Výchozí hodnota v ASP.NET Core 2,0 nebo novější je `false`.</span><span class="sxs-lookup"><span data-stu-id="120ac-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="120ac-225">Scénáře a případy použití</span><span class="sxs-lookup"><span data-stu-id="120ac-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="120ac-226">Pokud není možné přidat předávané hlavičky a všechny požadavky jsou zabezpečené</span><span class="sxs-lookup"><span data-stu-id="120ac-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="120ac-227">V některých případech nemusí být možné přidat předávaná záhlaví do požadavků, které jsou proxy k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="120ac-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="120ac-228">Pokud proxy vynucuje, že všechny veřejné externí požadavky jsou https, můžete schéma ručně nastavit v `Startup.Configure` části před použitím libovolného typu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="120ac-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="120ac-229">Tento kód může být zakázán pomocí proměnné prostředí nebo jiného nastavení konfigurace ve vývojovém nebo přípravném prostředí.</span><span class="sxs-lookup"><span data-stu-id="120ac-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="120ac-230">Práce se základními a proxy servery, které mění cestu požadavku</span><span class="sxs-lookup"><span data-stu-id="120ac-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="120ac-231">Některé proxy servery předávají cestu beze změny, ale se základní cestou aplikace, která by se měla odebrat, aby směrování fungovalo správně.</span><span class="sxs-lookup"><span data-stu-id="120ac-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="120ac-232">Middleware [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware rozdělí cestu do [HttpRequest. cesta](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace do [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="120ac-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="120ac-233">Pokud `/foo` je základní cesta aplikace pro cestu k proxy serveru předaná `/foo/api/1`jako, middleware `Request.PathBase` nastaví `/foo` na `Request.Path` a `/api/1` do pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="120ac-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="120ac-234">Původní cesta a základ cesty se znovu aplikují, když se middleware znovu zavolá v opačném případě.</span><span class="sxs-lookup"><span data-stu-id="120ac-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="120ac-235">Další informace o zpracování pořadí middlewaru najdete v <xref:fundamentals/middleware/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="120ac-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="120ac-236">Pokud proxy ořízne cestu (například předávání `/foo/api/1` na `/api/1`), opravte přesměrování a odkazy nastavením vlastnosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) žádosti:</span><span class="sxs-lookup"><span data-stu-id="120ac-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="120ac-237">Pokud proxy přidává data cest, zahodí část cesty pro opravu přesměrování a odkazy <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> pomocí a přiřazení <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> k vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="120ac-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="120ac-238">Konfigurace proxy serveru, který používá jiné názvy hlaviček</span><span class="sxs-lookup"><span data-stu-id="120ac-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="120ac-239">Pokud proxy nepoužívá hlavičky s názvem `X-Forwarded-For` a `X-Forwarded-Proto` k přeposlání informací o adrese proxy/portu a informace o původním <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> schématu, nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> možnosti a tak, aby odpovídaly názvům hlaviček používaným proxy serverem:</span><span class="sxs-lookup"><span data-stu-id="120ac-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="120ac-240">Konfigurace pro adresu IPv4 reprezentovanou jako IPv6 adresa</span><span class="sxs-lookup"><span data-stu-id="120ac-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="120ac-241">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaného v protokolu IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1`).</span><span class="sxs-lookup"><span data-stu-id="120ac-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="120ac-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="120ac-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="120ac-243">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="120ac-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="120ac-244">V následujícím příkladu se síťová adresa, která poskytuje předávaná záhlaví, přidá do `KnownNetworks` seznamu ve formátu protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="120ac-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="120ac-245">Adresa IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="120ac-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="120ac-246">Převedená adresa IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="120ac-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="120ac-247">Délka převedené předpony: 104</span><span class="sxs-lookup"><span data-stu-id="120ac-247">Converted prefix length: 104</span></span>

<span data-ttu-id="120ac-248">Adresu můžete také uvést v šestnáctkovém formátu (`10.11.12.1` reprezentované protokolem IPv6 jako `::ffff:0a0b:0c01`).</span><span class="sxs-lookup"><span data-stu-id="120ac-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="120ac-249">Při převodu adresy IPv4 na IPv6 přidejte 96 k délce předpony CIDR (`8` v příkladu), aby se zohlednila další `::ffff:` předpona IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="120ac-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="120ac-250">Předejte schéma pro reverzní proxy servery se systémy Linux a non-IIS.</span><span class="sxs-lookup"><span data-stu-id="120ac-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="120ac-251">Aplikace, které <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> volají <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> a umísťují lokalitu do nekonečné smyčky, pokud jsou nasazené do Azure Linux App Service, virtuální počítač Azure Linux (VM) nebo za jakýkoli jiný reverzní proxy server, kromě služby IIS.</span><span class="sxs-lookup"><span data-stu-id="120ac-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="120ac-252">Protokol TLS je ukončen reverzním proxy serverem a Kestrel není informován o správném schématu požadavků.</span><span class="sxs-lookup"><span data-stu-id="120ac-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="120ac-253">OAuth a OIDC v této konfiguraci selže, protože generují nesprávná přesměrování.</span><span class="sxs-lookup"><span data-stu-id="120ac-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="120ac-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Přidá a nakonfiguruje middleware předávaných hlaviček při spuštění za IIS, ale neexistuje žádná vyhovující Automatická konfigurace pro Linux (integrace Apache nebo Nginx).</span><span class="sxs-lookup"><span data-stu-id="120ac-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="120ac-255">Pro přeposílání schématu od proxy serveru ve scénářích mimo službu IIS přidejte a nakonfigurujte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="120ac-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="120ac-256">V `Startup.ConfigureServices`použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="120ac-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="120ac-257">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="120ac-257">Troubleshoot</span></span>

<span data-ttu-id="120ac-258">Když hlavičky nejsou předávány podle očekávání, povolte [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="120ac-258">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="120ac-259">Pokud protokoly neposkytují dostatek informací pro řešení tohoto problému, vytvořte výčet hlaviček požadavků přijatých serverem.</span><span class="sxs-lookup"><span data-stu-id="120ac-259">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="120ac-260">K zápisu hlaviček požadavků do odpovědi aplikace nebo k protokolování hlaviček použijte vložený middlewarový middleware.</span><span class="sxs-lookup"><span data-stu-id="120ac-260">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="120ac-261">Chcete-li zapsat hlavičky do odpovědi aplikace, umístěte následující vložený middleware terminálu hned po volání <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`do:</span><span class="sxs-lookup"><span data-stu-id="120ac-261">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="120ac-262">Do protokolů můžete zapisovat místo těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="120ac-262">You can write to logs instead of the response body.</span></span> <span data-ttu-id="120ac-263">Zápis do protokolů umožňuje, aby lokalita fungovala normálně během ladění.</span><span class="sxs-lookup"><span data-stu-id="120ac-263">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="120ac-264">Zápis protokolů místo textu odpovědi:</span><span class="sxs-lookup"><span data-stu-id="120ac-264">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="120ac-265">`ILogger<Startup>` Vložení`Startup` do třídy, jak je popsáno v tématu [vytvoření protokolů při spuštění](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="120ac-265">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="120ac-266">Vložte následující vložený middleware hned po volání <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> do v. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="120ac-266">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="120ac-267">Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty přesunuty na `X-Original-{For|Proto|Host}`.</span><span class="sxs-lookup"><span data-stu-id="120ac-267">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="120ac-268">Pokud existuje více hodnot v dané hlavičce, middleware pro přesměrované hlavičky zpracovává záhlaví v opačném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="120ac-268">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="120ac-269">Výchozí `ForwardLimit` hodnota je `1` (jedna), takže je `ForwardLimit` zpracována pouze hodnota zprava z hlaviček, pokud hodnota není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="120ac-269">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="120ac-270">Původní Vzdálená IP adresa žádosti se musí shodovat s položkou v `KnownProxies` seznamech nebo `KnownNetworks` před zpracováním předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="120ac-270">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="120ac-271">To omezuje falšování hlaviček tím, že nepřijímá předávací servery od nedůvěryhodných proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="120ac-271">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="120ac-272">Při zjištění neznámého proxy serveru se zobrazí zpráva s informacemi o adrese proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="120ac-272">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="120ac-273">V předchozím příkladu je 10.0.0.100 proxy server.</span><span class="sxs-lookup"><span data-stu-id="120ac-273">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="120ac-274">Pokud je server důvěryhodným proxy serverem, přidejte IP adresu serveru do `KnownProxies` (nebo přidejte důvěryhodnou síť do `KnownNetworks`) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="120ac-274">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="120ac-275">Další informace najdete v části [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="120ac-275">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="120ac-276">Povoluje pouze důvěryhodné proxy servery a sítě pro přeposílání hlaviček.</span><span class="sxs-lookup"><span data-stu-id="120ac-276">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="120ac-277">V opačném případě jsou možné útoky s [falešnou IP adresou](https://www.iplocation.net/ip-spoofing) .</span><span class="sxs-lookup"><span data-stu-id="120ac-277">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="certificate-forwarding"></a><span data-ttu-id="120ac-278">Předávání certifikátů</span><span class="sxs-lookup"><span data-stu-id="120ac-278">Certificate forwarding</span></span> 

### <a name="on-azure"></a><span data-ttu-id="120ac-279">V Azure</span><span class="sxs-lookup"><span data-stu-id="120ac-279">On Azure</span></span>

<span data-ttu-id="120ac-280">V [dokumentaci k Azure](/azure/app-service/app-service-web-configure-tls-mutual-auth) můžete nakonfigurovat Azure Web Apps.</span><span class="sxs-lookup"><span data-stu-id="120ac-280">See the [Azure documentation](/azure/app-service/app-service-web-configure-tls-mutual-auth) to configure Azure Web Apps.</span></span> <span data-ttu-id="120ac-281">V `Startup.Configure` metodě vaší aplikace přidejte následující kód před `app.UseAuthentication();`voláním:</span><span class="sxs-lookup"><span data-stu-id="120ac-281">In your app's `Startup.Configure` method, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="120ac-282">Také budete muset nakonfigurovat middleware předávání certifikátů a zadat název hlavičky, který Azure používá.</span><span class="sxs-lookup"><span data-stu-id="120ac-282">You'll also need to configure the Certificate Forwarding middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="120ac-283">V `Startup.ConfigureServices` metodě vaší aplikace přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:</span><span class="sxs-lookup"><span data-stu-id="120ac-283">In your app's `Startup.ConfigureServices` method, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="with-other-web-proxies"></a><span data-ttu-id="120ac-284">S dalšími webovými proxy servery</span><span class="sxs-lookup"><span data-stu-id="120ac-284">With other web proxies</span></span>

<span data-ttu-id="120ac-285">Pokud používáte proxy server, který není službou IIS nebo Azure Web Apps v rámci směrování žádostí o aplikace, nakonfigurujte proxy server tak, aby předal certifikát, který přijal v hlavičce protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="120ac-285">If you're using a proxy that isn't IIS or Azure's Web Apps Application Request Routing, configure your proxy to forward the certificate it received in an HTTP header.</span></span> <span data-ttu-id="120ac-286">V `Startup.Configure` metodě vaší aplikace přidejte následující kód před `app.UseAuthentication();`voláním:</span><span class="sxs-lookup"><span data-stu-id="120ac-286">In your app's `Startup.Configure` method, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="120ac-287">Také budete muset nakonfigurovat middleware předávání certifikátů a zadat název hlavičky.</span><span class="sxs-lookup"><span data-stu-id="120ac-287">You'll also need to configure the Certificate Forwarding middleware to specify the header name.</span></span> <span data-ttu-id="120ac-288">V `Startup.ConfigureServices` metodě vaší aplikace přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:</span><span class="sxs-lookup"><span data-stu-id="120ac-288">In your app's `Startup.ConfigureServices` method, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="120ac-289">Nakonec platí, že pokud má proxy něco jiného než kódování Base64 certifikátu (jako je případ s Nginx), nastavte `HeaderConverter` možnost.</span><span class="sxs-lookup"><span data-stu-id="120ac-289">Finally, if the proxy is doing something other than base64 encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="120ac-290">Vezměte v `Startup.ConfigureServices`úvahu následující příklad:</span><span class="sxs-lookup"><span data-stu-id="120ac-290">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="120ac-291">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="120ac-291">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="120ac-292">Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET Core zvýšení oprávnění k ohrožení zabezpečení</span><span class="sxs-lookup"><span data-stu-id="120ac-292">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

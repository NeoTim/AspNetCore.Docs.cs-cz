---
title: Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení
author: rick-anderson
description: Přečtěte si o konfiguraci pro aplikace hostované za servery proxy a nástroje pro vyrovnávání zatížení, které často překrývají důležité informace o žádostech.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 9299117b45a71b7aaf761fc3a0a4e541373dd970
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106294"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="a242c-103">Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="a242c-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="a242c-104">Autor – [Chris Rossův](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="a242c-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a242c-105">V Doporučené konfiguraci pro ASP.NET Core se aplikace hostuje pomocí IIS/ASP. Modul NET Core, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="a242c-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="a242c-106">Proxy servery, nástroje pro vyrovnávání zatížení a další síťová zařízení často překrývají informace o žádosti předtím, než dosáhnou aplikace:</span><span class="sxs-lookup"><span data-stu-id="a242c-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="a242c-107">Pokud jsou požadavky HTTPS proxy serverem přes HTTP, původní schéma (HTTPS) se ztratí a musí se přenášet v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="a242c-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="a242c-108">Vzhledem k tomu, že aplikace obdrží požadavek od proxy serveru a ne jeho skutečný zdroj v Internetu nebo podnikové síti, musí být zdrojová adresa IP klienta předána také v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="a242c-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="a242c-109">Tyto informace můžou být důležité ve zpracování žádostí, například v přesměrování, ověřování, generování odkazů, vyhodnocení zásad a geografická poloha klienta.</span><span class="sxs-lookup"><span data-stu-id="a242c-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="a242c-110">Předávaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="a242c-110">Forwarded headers</span></span>

<span data-ttu-id="a242c-111">Podle konvence proxy předávají informace v hlavičkách protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a242c-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="a242c-112">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="a242c-112">Header</span></span> | <span data-ttu-id="a242c-113">Popis</span><span class="sxs-lookup"><span data-stu-id="a242c-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="a242c-114">X-předané – pro</span><span class="sxs-lookup"><span data-stu-id="a242c-114">X-Forwarded-For</span></span> | <span data-ttu-id="a242c-115">Uchovává informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="a242c-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="a242c-116">Tento parametr může obsahovat IP adresy (a volitelně také čísla portů).</span><span class="sxs-lookup"><span data-stu-id="a242c-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="a242c-117">V řetězci proxy serverů označuje první parametr klienta, ve kterém se žádost poprvé nastavila.</span><span class="sxs-lookup"><span data-stu-id="a242c-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="a242c-118">Následující identifikátory proxy následují.</span><span class="sxs-lookup"><span data-stu-id="a242c-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="a242c-119">Poslední proxy server v řetězci není uveden v seznamu parametrů.</span><span class="sxs-lookup"><span data-stu-id="a242c-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="a242c-120">Poslední IP adresa proxy serveru a volitelně je číslo portu, které je k dispozici jako Vzdálená IP adresa v transportní vrstvě.</span><span class="sxs-lookup"><span data-stu-id="a242c-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="a242c-121">X-předáno – proto</span><span class="sxs-lookup"><span data-stu-id="a242c-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="a242c-122">Hodnota původního schématu (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a242c-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="a242c-123">Hodnota může být také seznamem schémat, pokud je požadavek procházen více proxy.</span><span class="sxs-lookup"><span data-stu-id="a242c-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="a242c-124">X-předávaný-Host</span><span class="sxs-lookup"><span data-stu-id="a242c-124">X-Forwarded-Host</span></span> | <span data-ttu-id="a242c-125">Původní hodnota pole hlavičky hostitele</span><span class="sxs-lookup"><span data-stu-id="a242c-125">The original value of the Host header field.</span></span> <span data-ttu-id="a242c-126">Servery proxy většinou nemění hlavičku hostitele.</span><span class="sxs-lookup"><span data-stu-id="a242c-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="a242c-127">Viz informační [zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pro informace o chybě zabezpečení zvýšení oprávnění, která má vliv na systémy, ve kterých server proxy neověřuje nebo omezuje hlavičky hostitele na známé správné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a242c-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="a242c-128">Middleware předávaných hlaviček z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a vyplní související pole v <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="a242c-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="a242c-129">Aktualizace middlewaru:</span><span class="sxs-lookup"><span data-stu-id="a242c-129">The middleware updates:</span></span>

* <span data-ttu-id="a242c-130">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): nastavte pomocí `X-Forwarded-For` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="a242c-131">Další nastavení mají vliv na to, jak middleware nastaví `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="a242c-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="a242c-132">Podrobnosti najdete v tématu [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="a242c-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="a242c-133">[HttpContext. Request. schéma](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): nastavte pomocí `X-Forwarded-Proto` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="a242c-134">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): nastavte pomocí `X-Forwarded-Host` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="a242c-135">Je možné nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru u předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a242c-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="a242c-136">Výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="a242c-136">The default settings are:</span></span>

* <span data-ttu-id="a242c-137">Mezi aplikací a zdrojem požadavků je jenom *jeden proxy server* .</span><span class="sxs-lookup"><span data-stu-id="a242c-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="a242c-138">Pro známé proxy servery a známé sítě jsou nakonfigurovány pouze adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="a242c-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="a242c-139">Předané hlavičky jsou pojmenovány `X-Forwarded-For` a `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="a242c-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="a242c-140">Ne všechna síťová zařízení přidávají `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a242c-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="a242c-141">Zeptejte se na pokyny výrobce zařízení, jestli proxy požadavky při přístupu do aplikace tyto hlavičky neobsahují.</span><span class="sxs-lookup"><span data-stu-id="a242c-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="a242c-142">Pokud zařízení používá jiné názvy hlaviček než `X-Forwarded-For` a `X-Forwarded-Proto` , nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným zařízením.</span><span class="sxs-lookup"><span data-stu-id="a242c-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="a242c-143">Další informace najdete v tématu [předané možnosti a konfigurace middlewarových hlaviček](#forwarded-headers-middleware-options) [pro proxy server, který používá jiné názvy hlaviček](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="a242c-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="a242c-144">Modul IIS/IIS Express a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a242c-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="a242c-145">Middleware předávaných hlaviček je ve výchozím nastavení povolená službou [IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , když je aplikace hostovaná [mimo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) službu IIS a modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a242c-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="a242c-146">Middleware předávaných hlaviček se aktivují, aby se nejdříve spouštěl v kanálu middleware s omezenou konfigurací určenou pro modul ASP.NET Core, protože se jedná o obavy týkající se důvěryhodnosti se předanými hlavičkami (například [falšování IP adres](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="a242c-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="a242c-147">Middleware je nakonfigurovaná pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a a je omezená na jeden localhost proxy.</span><span class="sxs-lookup"><span data-stu-id="a242c-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="a242c-148">Pokud se vyžaduje další konfigurace, přečtěte si téma [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="a242c-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a242c-149">Další scénáře proxy server a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="a242c-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a242c-150">Bez použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimo procesy](xref:host-and-deploy/iis/index#out-of-process-hosting-model)není middleware předávaných hlaviček ve výchozím nastavení povolená.</span><span class="sxs-lookup"><span data-stu-id="a242c-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="a242c-151">Middleware předaných hlaviček musí být povolená, aby aplikace zpracovala předávané hlavičky s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="a242c-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="a242c-152">Po povolení middlewaru, pokud není <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zadáno pro middleware, výchozí [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a242c-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="a242c-153">Nakonfigurujte middleware s nástrojem <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> na předáte `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a242c-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a242c-154">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu v `Startup.Configure` před voláním jiného middleware:</span><span class="sxs-lookup"><span data-stu-id="a242c-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="a242c-155">Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` nebo přímo k metodě rozšíření s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , výchozí hlavičky pro přeposílání jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a242c-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="a242c-156"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Vlastnost musí být nakonfigurována s hlavičkou pro přeposílání.</span><span class="sxs-lookup"><span data-stu-id="a242c-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="a242c-157">Konfigurace nginx</span><span class="sxs-lookup"><span data-stu-id="a242c-157">Nginx configuration</span></span>

<span data-ttu-id="a242c-158">Pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a naleznete informace v tématu <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="a242c-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="a242c-159">Další informace najdete v tématu [Nginx: použití předané hlavičky](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="a242c-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="a242c-160">Konfigurace Apache</span><span class="sxs-lookup"><span data-stu-id="a242c-160">Apache configuration</span></span>

<span data-ttu-id="a242c-161">`X-Forwarded-For`se přidá automaticky (viz část [modulu Apache mod_proxy: hlavičky žádosti reverzního proxy serveru](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="a242c-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="a242c-162">Informace o tom, jak přeslat `X-Forwarded-Proto` hlavičku, najdete v tématu <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="a242c-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="a242c-163">Možnosti middlewaru předávaných hlaviček</span><span class="sxs-lookup"><span data-stu-id="a242c-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="a242c-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>řízení chování middlewaru předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a242c-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="a242c-165">Následující příklad změní výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="a242c-165">The following example changes the default values:</span></span>

* <span data-ttu-id="a242c-166">Omezte počet záznamů v předaných hlavičkách na `2` .</span><span class="sxs-lookup"><span data-stu-id="a242c-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="a242c-167">Přidejte známou adresu proxy serveru `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="a242c-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="a242c-168">Změňte název předané hlavičky z výchozí `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="a242c-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="a242c-169">Možnost</span><span class="sxs-lookup"><span data-stu-id="a242c-169">Option</span></span> | <span data-ttu-id="a242c-170">Popis</span><span class="sxs-lookup"><span data-stu-id="a242c-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="a242c-171">Omezí hostitele v `X-Forwarded-Host` hlavičce na zadané hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a242c-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="a242c-172">Hodnoty se porovnávají pomocí pořadí – ignorovat – případ.</span><span class="sxs-lookup"><span data-stu-id="a242c-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="a242c-173">Čísla portů musí být vyloučena.</span><span class="sxs-lookup"><span data-stu-id="a242c-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="a242c-174">Pokud je seznam prázdný, jsou povoleny všichni hostitelé.</span><span class="sxs-lookup"><span data-stu-id="a242c-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="a242c-175">Zástupný znak nejvyšší úrovně `*` umožňuje všem neprázdným hostitelům.</span><span class="sxs-lookup"><span data-stu-id="a242c-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="a242c-176">Zástupné znaky subdomény jsou povolené, ale neodpovídají kořenové doméně.</span><span class="sxs-lookup"><span data-stu-id="a242c-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="a242c-177">Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale nikoli ke kořenové doméně `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="a242c-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="a242c-178">Názvy hostitelů Unicode jsou povoleny, ale jsou převedeny na [Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</span><span class="sxs-lookup"><span data-stu-id="a242c-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="a242c-179">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí zahrnovat ohraničující závorky a musí být v [konvenční formě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="a242c-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="a242c-180">IPv6 adresy nejsou speciální – použita pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádné kanonikalizace.</span><span class="sxs-lookup"><span data-stu-id="a242c-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="a242c-181">Nepodaří-li se omezit povolené hostitele, může útočníkovi umožnit přístup k falešným odkazům generovanými službou.</span><span class="sxs-lookup"><span data-stu-id="a242c-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="a242c-182">Výchozí hodnota je prázdná `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="a242c-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="a242c-183">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="a242c-184">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-For` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a242c-185">Výchozí formát je `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="a242c-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="a242c-186">Určuje, které předávacíci by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="a242c-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="a242c-187">Seznam polí, která platí, najdete v tématu [ForwardedHeaders enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="a242c-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="a242c-188">Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="a242c-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="a242c-189">Výchozí hodnota je [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a242c-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="a242c-190">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="a242c-191">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Host` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a242c-192">Výchozí formát je `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="a242c-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="a242c-193">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="a242c-194">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Proto` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a242c-195">Výchozí formát je `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="a242c-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="a242c-196">Omezuje počet položek v záhlavích, které jsou zpracovávány.</span><span class="sxs-lookup"><span data-stu-id="a242c-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="a242c-197">Nastavením `null` tohoto omezení zakážete, ale to by mělo být provedeno pouze v případě, že `KnownProxies` nebo `KnownNetworks` jsou nakonfigurovány.</span><span class="sxs-lookup"><span data-stu-id="a242c-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="a242c-198">Nastavení `null` nehodnoty je preventivní opatrnost (ale ne záruka) pro ochranu proti nesprávně konfigurovaným proxy a škodlivým požadavkům přicházejících ze dvoustranných kanálů v síti.</span><span class="sxs-lookup"><span data-stu-id="a242c-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="a242c-199">Middleware předávaných hlaviček zpracovává hlavičky v obráceném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="a242c-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a242c-200">Pokud je použita výchozí hodnota ( `1` ), bude zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="a242c-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="a242c-201">Výchozí formát je `1`.</span><span class="sxs-lookup"><span data-stu-id="a242c-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="a242c-202">Rozsahy adres známých sítí, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="a242c-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="a242c-203">Zadejte rozsahy IP adres pomocí zápisu CIDR (Classless prosměrování mezi doménami).</span><span class="sxs-lookup"><span data-stu-id="a242c-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="a242c-204">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="a242c-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a242c-205">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a242c-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a242c-206">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a242c-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a242c-207">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="a242c-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a242c-208">Výchozí hodnota je `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> obsahující jednu položku pro `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="a242c-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="a242c-209">Adresy známých proxy adres, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="a242c-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="a242c-210">Použijte `KnownProxies` k určení přesné shody IP adres.</span><span class="sxs-lookup"><span data-stu-id="a242c-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="a242c-211">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="a242c-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a242c-212">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a242c-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a242c-213">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a242c-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a242c-214">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="a242c-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a242c-215">Výchozí hodnota je `IList` \<<xref:System.Net.IPAddress>> obsahující jednu položku pro `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="a242c-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="a242c-216">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="a242c-217">Výchozí formát je `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="a242c-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="a242c-218">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="a242c-219">Výchozí formát je `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="a242c-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="a242c-220">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="a242c-221">Výchozí formát je `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="a242c-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="a242c-222">Vyžaduje, aby byl počet synchronizovaných hodnot hlaviček mezi zpracovávaným [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="a242c-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="a242c-223">Výchozí hodnota v ASP.NET Core 1. x je `true` .</span><span class="sxs-lookup"><span data-stu-id="a242c-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="a242c-224">Výchozí hodnota v ASP.NET Core 2,0 nebo novější je `false` .</span><span class="sxs-lookup"><span data-stu-id="a242c-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="a242c-225">Scénáře a případy použití</span><span class="sxs-lookup"><span data-stu-id="a242c-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="a242c-226">Pokud není možné přidat předávané hlavičky a všechny požadavky jsou zabezpečené</span><span class="sxs-lookup"><span data-stu-id="a242c-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="a242c-227">V některých případech nemusí být možné přidat předávaná záhlaví do požadavků, které jsou proxy k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a242c-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="a242c-228">Pokud proxy vynucuje, že všechny veřejné externí požadavky jsou HTTPS, můžete schéma ručně nastavit v části `Startup.Configure` před použitím libovolného typu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="a242c-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="a242c-229">Tento kód může být zakázán pomocí proměnné prostředí nebo jiného nastavení konfigurace ve vývojovém nebo přípravném prostředí.</span><span class="sxs-lookup"><span data-stu-id="a242c-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="a242c-230">Práce se základními a proxy servery, které mění cestu požadavku</span><span class="sxs-lookup"><span data-stu-id="a242c-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="a242c-231">Některé proxy servery předávají cestu beze změny, ale se základní cestou aplikace, která by se měla odebrat, aby směrování fungovalo správně.</span><span class="sxs-lookup"><span data-stu-id="a242c-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="a242c-232">Middleware [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware rozdělí cestu do [HttpRequest. cesta](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace do [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="a242c-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="a242c-233">Pokud `/foo` je základní cesta aplikace pro cestu k proxy serveru předaná jako `/foo/api/1` , middleware nastaví `Request.PathBase` na `/foo` a `Request.Path` do `/api/1` pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="a242c-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="a242c-234">Původní cesta a základ cesty se znovu aplikují, když se middleware znovu zavolá v opačném případě.</span><span class="sxs-lookup"><span data-stu-id="a242c-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="a242c-235">Další informace o zpracování pořadí middlewaru najdete v tématu <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="a242c-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="a242c-236">Pokud proxy ořízne cestu (například předávání `/foo/api/1` na `/api/1` ), opravte přesměrování a odkazy nastavením vlastnosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) žádosti:</span><span class="sxs-lookup"><span data-stu-id="a242c-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="a242c-237">Pokud proxy přidává data cest, zahodí část cesty pro opravu přesměrování a odkazy pomocí <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> a přiřazení k <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a242c-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="a242c-238">Konfigurace proxy serveru, který používá jiné názvy hlaviček</span><span class="sxs-lookup"><span data-stu-id="a242c-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="a242c-239">Pokud proxy nepoužívá hlavičky s názvem `X-Forwarded-For` a `X-Forwarded-Proto` k přeposlání informací o adrese proxy/portu a informace o původním schématu, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným proxy serverem:</span><span class="sxs-lookup"><span data-stu-id="a242c-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="a242c-240">Konfigurace pro adresu IPv4 reprezentovanou jako IPv6 adresa</span><span class="sxs-lookup"><span data-stu-id="a242c-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="a242c-241">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaného v protokolu IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="a242c-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="a242c-242">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a242c-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a242c-243">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a242c-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="a242c-244">V následujícím příkladu se síťová adresa, která poskytuje předávaná záhlaví, přidá do `KnownNetworks` seznamu ve formátu protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="a242c-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="a242c-245">Adresa IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="a242c-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="a242c-246">Převedená adresa IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="a242c-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="a242c-247">Délka převedené předpony: 104</span><span class="sxs-lookup"><span data-stu-id="a242c-247">Converted prefix length: 104</span></span>

<span data-ttu-id="a242c-248">Adresu můžete také uvést v šestnáctkovém formátu ( `10.11.12.1` reprezentované protokolem IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="a242c-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="a242c-249">Při převodu adresy IPv4 na IPv6 přidejte 96 k délce předpony CIDR ( `8` v příkladu), aby se zohlednila další `::ffff:` předpona IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="a242c-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="a242c-250">Předejte schéma pro reverzní proxy servery se systémy Linux a non-IIS.</span><span class="sxs-lookup"><span data-stu-id="a242c-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="a242c-251">Aplikace, které volají <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> a <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umísťují lokalitu do nekonečné smyčky, pokud jsou nasazené do azure Linux App Service, virtuální počítač Azure Linux (VM) nebo za jakýkoli jiný reverzní proxy server, kromě služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a242c-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="a242c-252">Protokol TLS je ukončen reverzním proxy serverem a Kestrel není informován o správném schématu požadavků.</span><span class="sxs-lookup"><span data-stu-id="a242c-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="a242c-253">OAuth a OIDC v této konfiguraci selže, protože generují nesprávná přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a242c-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="a242c-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Přidá a nakonfiguruje middleware předávaných hlaviček při spuštění za IIS, ale neexistuje žádná vyhovující Automatická konfigurace pro Linux (integrace Apache nebo Nginx).</span><span class="sxs-lookup"><span data-stu-id="a242c-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="a242c-255">Pro přeposílání schématu od proxy serveru ve scénářích mimo službu IIS přidejte a nakonfigurujte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="a242c-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="a242c-256">V `Startup.ConfigureServices` použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="a242c-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="a242c-257">Předávání certifikátů</span><span class="sxs-lookup"><span data-stu-id="a242c-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="a242c-258">Azure</span><span class="sxs-lookup"><span data-stu-id="a242c-258">Azure</span></span>

<span data-ttu-id="a242c-259">Pokud chcete nakonfigurovat Azure App Service pro předávání certifikátů, přečtěte si téma [Konfigurace vzájemného ověřování TLS pro Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="a242c-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="a242c-260">Následující pokyny se týkají konfigurace aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a242c-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="a242c-261">V `Startup.Configure` přidejte následující kód před volání do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="a242c-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="a242c-262">Nakonfigurujte middleware předávání certifikátů a určete název hlavičky, kterou používá Azure.</span><span class="sxs-lookup"><span data-stu-id="a242c-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="a242c-263">V `Startup.ConfigureServices` přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:</span><span class="sxs-lookup"><span data-stu-id="a242c-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="a242c-264">Další webové proxy servery</span><span class="sxs-lookup"><span data-stu-id="a242c-264">Other web proxies</span></span>

<span data-ttu-id="a242c-265">Pokud se používá proxy server, který není službou IIS nebo Azure App Service směrování žádostí na aplikace (ARR), nakonfigurujte proxy server tak, aby předal certifikát, který přijal v hlavičce protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a242c-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="a242c-266">V `Startup.Configure` přidejte následující kód před volání do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="a242c-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="a242c-267">Nakonfigurujte middleware předávání certifikátů a zadejte název záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="a242c-268">V `Startup.ConfigureServices` přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:</span><span class="sxs-lookup"><span data-stu-id="a242c-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="a242c-269">Pokud proxy server nemá kódování Base64 (jako je případ s Nginx), nastavte `HeaderConverter` možnost.</span><span class="sxs-lookup"><span data-stu-id="a242c-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="a242c-270">Vezměte v úvahu následující příklad `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a242c-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="a242c-271">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="a242c-271">Troubleshoot</span></span>

<span data-ttu-id="a242c-272">Když hlavičky nejsou předávány podle očekávání, povolte [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a242c-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a242c-273">Pokud protokoly neposkytují dostatek informací pro řešení tohoto problému, vytvořte výčet hlaviček požadavků přijatých serverem.</span><span class="sxs-lookup"><span data-stu-id="a242c-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="a242c-274">K zápisu hlaviček požadavků do odpovědi aplikace nebo k protokolování hlaviček použijte vložený middlewarový middleware.</span><span class="sxs-lookup"><span data-stu-id="a242c-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="a242c-275">Chcete-li zapsat hlavičky do odpovědi aplikace, umístěte následující vložený middleware terminálu hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a242c-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="a242c-276">Do protokolů můžete zapisovat místo těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a242c-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="a242c-277">Zápis do protokolů umožňuje, aby lokalita fungovala normálně během ladění.</span><span class="sxs-lookup"><span data-stu-id="a242c-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="a242c-278">Zápis protokolů místo textu odpovědi:</span><span class="sxs-lookup"><span data-stu-id="a242c-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="a242c-279">Vložení `ILogger<Startup>` do `Startup` třídy, jak je popsáno v tématu [vytvoření protokolů při spuštění](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="a242c-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="a242c-280">Vložte následující vložený middleware hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a242c-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a242c-281">Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty přesunuty na `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="a242c-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="a242c-282">Pokud existuje více hodnot v dané hlavičce, middleware pro přesměrované hlavičky zpracovává záhlaví v opačném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="a242c-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a242c-283">Výchozí hodnota `ForwardLimit` je `1` (jedna), takže je zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="a242c-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="a242c-284">Původní Vzdálená IP adresa žádosti se musí shodovat s položkou v `KnownProxies` `KnownNetworks` seznamech nebo před zpracováním předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a242c-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="a242c-285">To omezuje falšování hlaviček tím, že nepřijímá předávací servery od nedůvěryhodných proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="a242c-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="a242c-286">Při zjištění neznámého proxy serveru se zobrazí zpráva s informacemi o adrese proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="a242c-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="a242c-287">V předchozím příkladu je 10.0.0.100 proxy server.</span><span class="sxs-lookup"><span data-stu-id="a242c-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="a242c-288">Pokud je server důvěryhodným proxy serverem, přidejte IP adresu serveru do `KnownProxies` (nebo přidejte důvěryhodnou síť do `KnownNetworks` ) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a242c-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a242c-289">Další informace najdete v části [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="a242c-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="a242c-290">Povoluje pouze důvěryhodné proxy servery a sítě pro přeposílání hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a242c-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="a242c-291">V opačném případě jsou možné útoky s [falešnou IP adresou](https://www.iplocation.net/ip-spoofing) .</span><span class="sxs-lookup"><span data-stu-id="a242c-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a242c-292">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a242c-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="a242c-293">Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET Core zvýšení zabezpečení oprávnění</span><span class="sxs-lookup"><span data-stu-id="a242c-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a242c-294">V Doporučené konfiguraci pro ASP.NET Core se aplikace hostuje pomocí IIS/ASP. Modul NET Core, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="a242c-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="a242c-295">Proxy servery, nástroje pro vyrovnávání zatížení a další síťová zařízení často překrývají informace o žádosti předtím, než dosáhnou aplikace:</span><span class="sxs-lookup"><span data-stu-id="a242c-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="a242c-296">Pokud jsou požadavky HTTPS proxy serverem přes HTTP, původní schéma (HTTPS) se ztratí a musí se přenášet v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="a242c-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="a242c-297">Vzhledem k tomu, že aplikace obdrží požadavek od proxy serveru a ne jeho skutečný zdroj v Internetu nebo podnikové síti, musí být zdrojová adresa IP klienta předána také v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="a242c-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="a242c-298">Tyto informace můžou být důležité ve zpracování žádostí, například v přesměrování, ověřování, generování odkazů, vyhodnocení zásad a geografická poloha klienta.</span><span class="sxs-lookup"><span data-stu-id="a242c-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="a242c-299">Předávaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="a242c-299">Forwarded headers</span></span>

<span data-ttu-id="a242c-300">Podle konvence proxy předávají informace v hlavičkách protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a242c-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="a242c-301">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="a242c-301">Header</span></span> | <span data-ttu-id="a242c-302">Popis</span><span class="sxs-lookup"><span data-stu-id="a242c-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="a242c-303">X-předané – pro</span><span class="sxs-lookup"><span data-stu-id="a242c-303">X-Forwarded-For</span></span> | <span data-ttu-id="a242c-304">Uchovává informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="a242c-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="a242c-305">Tento parametr může obsahovat IP adresy (a volitelně také čísla portů).</span><span class="sxs-lookup"><span data-stu-id="a242c-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="a242c-306">V řetězci proxy serverů označuje první parametr klienta, ve kterém se žádost poprvé nastavila.</span><span class="sxs-lookup"><span data-stu-id="a242c-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="a242c-307">Následující identifikátory proxy následují.</span><span class="sxs-lookup"><span data-stu-id="a242c-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="a242c-308">Poslední proxy server v řetězci není uveden v seznamu parametrů.</span><span class="sxs-lookup"><span data-stu-id="a242c-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="a242c-309">Poslední IP adresa proxy serveru a volitelně je číslo portu, které je k dispozici jako Vzdálená IP adresa v transportní vrstvě.</span><span class="sxs-lookup"><span data-stu-id="a242c-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="a242c-310">X-předáno – proto</span><span class="sxs-lookup"><span data-stu-id="a242c-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="a242c-311">Hodnota původního schématu (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a242c-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="a242c-312">Hodnota může být také seznamem schémat, pokud je požadavek procházen více proxy.</span><span class="sxs-lookup"><span data-stu-id="a242c-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="a242c-313">X-předávaný-Host</span><span class="sxs-lookup"><span data-stu-id="a242c-313">X-Forwarded-Host</span></span> | <span data-ttu-id="a242c-314">Původní hodnota pole hlavičky hostitele</span><span class="sxs-lookup"><span data-stu-id="a242c-314">The original value of the Host header field.</span></span> <span data-ttu-id="a242c-315">Servery proxy většinou nemění hlavičku hostitele.</span><span class="sxs-lookup"><span data-stu-id="a242c-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="a242c-316">Viz informační [zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pro informace o chybě zabezpečení zvýšení oprávnění, která má vliv na systémy, ve kterých server proxy neověřuje nebo omezuje hlavičky hostitele na známé správné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a242c-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="a242c-317">Middleware předávaných hlaviček z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a vyplní související pole v <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="a242c-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="a242c-318">Aktualizace middlewaru:</span><span class="sxs-lookup"><span data-stu-id="a242c-318">The middleware updates:</span></span>

* <span data-ttu-id="a242c-319">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): nastavte pomocí `X-Forwarded-For` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="a242c-320">Další nastavení mají vliv na to, jak middleware nastaví `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="a242c-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="a242c-321">Podrobnosti najdete v tématu [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="a242c-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="a242c-322">[HttpContext. Request. schéma](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): nastavte pomocí `X-Forwarded-Proto` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="a242c-323">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): nastavte pomocí `X-Forwarded-Host` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="a242c-324">Je možné nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru u předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a242c-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="a242c-325">Výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="a242c-325">The default settings are:</span></span>

* <span data-ttu-id="a242c-326">Mezi aplikací a zdrojem požadavků je jenom *jeden proxy server* .</span><span class="sxs-lookup"><span data-stu-id="a242c-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="a242c-327">Pro známé proxy servery a známé sítě jsou nakonfigurovány pouze adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="a242c-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="a242c-328">Předané hlavičky jsou pojmenovány `X-Forwarded-For` a `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="a242c-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="a242c-329">Ne všechna síťová zařízení přidávají `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a242c-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="a242c-330">Zeptejte se na pokyny výrobce zařízení, jestli proxy požadavky při přístupu do aplikace tyto hlavičky neobsahují.</span><span class="sxs-lookup"><span data-stu-id="a242c-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="a242c-331">Pokud zařízení používá jiné názvy hlaviček než `X-Forwarded-For` a `X-Forwarded-Proto` , nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným zařízením.</span><span class="sxs-lookup"><span data-stu-id="a242c-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="a242c-332">Další informace najdete v tématu [předané možnosti a konfigurace middlewarových hlaviček](#forwarded-headers-middleware-options) [pro proxy server, který používá jiné názvy hlaviček](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="a242c-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="a242c-333">Modul IIS/IIS Express a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a242c-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="a242c-334">Middleware předávaných hlaviček je ve výchozím nastavení povolená službou [IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , když je aplikace hostovaná [mimo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) službu IIS a modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a242c-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="a242c-335">Middleware předávaných hlaviček se aktivují, aby se nejdříve spouštěl v kanálu middleware s omezenou konfigurací určenou pro modul ASP.NET Core, protože se jedná o obavy týkající se důvěryhodnosti se předanými hlavičkami (například [falšování IP adres](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="a242c-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="a242c-336">Middleware je nakonfigurovaná pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a a je omezená na jeden localhost proxy.</span><span class="sxs-lookup"><span data-stu-id="a242c-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="a242c-337">Pokud se vyžaduje další konfigurace, přečtěte si téma [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="a242c-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a242c-338">Další scénáře proxy server a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="a242c-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a242c-339">Bez použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimo procesy](xref:host-and-deploy/iis/index#out-of-process-hosting-model)není middleware předávaných hlaviček ve výchozím nastavení povolená.</span><span class="sxs-lookup"><span data-stu-id="a242c-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="a242c-340">Middleware předaných hlaviček musí být povolená, aby aplikace zpracovala předávané hlavičky s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="a242c-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="a242c-341">Po povolení middlewaru, pokud není <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zadáno pro middleware, výchozí [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a242c-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="a242c-342">Nakonfigurujte middleware s nástrojem <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> na předáte `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a242c-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a242c-343">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu v `Startup.Configure` před voláním jiného middleware:</span><span class="sxs-lookup"><span data-stu-id="a242c-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="a242c-344">Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` nebo přímo k metodě rozšíření s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , výchozí hlavičky pro přeposílání jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a242c-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="a242c-345"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Vlastnost musí být nakonfigurována s hlavičkou pro přeposílání.</span><span class="sxs-lookup"><span data-stu-id="a242c-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="a242c-346">Konfigurace nginx</span><span class="sxs-lookup"><span data-stu-id="a242c-346">Nginx configuration</span></span>

<span data-ttu-id="a242c-347">Pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a naleznete informace v tématu <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="a242c-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="a242c-348">Další informace najdete v tématu [Nginx: použití předané hlavičky](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="a242c-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="a242c-349">Konfigurace Apache</span><span class="sxs-lookup"><span data-stu-id="a242c-349">Apache configuration</span></span>

<span data-ttu-id="a242c-350">`X-Forwarded-For`se přidá automaticky (viz část [modulu Apache mod_proxy: hlavičky žádosti reverzního proxy serveru](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="a242c-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="a242c-351">Informace o tom, jak přeslat `X-Forwarded-Proto` hlavičku, najdete v tématu <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="a242c-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="a242c-352">Možnosti middlewaru předávaných hlaviček</span><span class="sxs-lookup"><span data-stu-id="a242c-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="a242c-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>řízení chování middlewaru předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a242c-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="a242c-354">Následující příklad změní výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="a242c-354">The following example changes the default values:</span></span>

* <span data-ttu-id="a242c-355">Omezte počet záznamů v předaných hlavičkách na `2` .</span><span class="sxs-lookup"><span data-stu-id="a242c-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="a242c-356">Přidejte známou adresu proxy serveru `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="a242c-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="a242c-357">Změňte název předané hlavičky z výchozí `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="a242c-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="a242c-358">Možnost</span><span class="sxs-lookup"><span data-stu-id="a242c-358">Option</span></span> | <span data-ttu-id="a242c-359">Popis</span><span class="sxs-lookup"><span data-stu-id="a242c-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="a242c-360">Omezí hostitele v `X-Forwarded-Host` hlavičce na zadané hodnoty.</span><span class="sxs-lookup"><span data-stu-id="a242c-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="a242c-361">Hodnoty se porovnávají pomocí pořadí – ignorovat – případ.</span><span class="sxs-lookup"><span data-stu-id="a242c-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="a242c-362">Čísla portů musí být vyloučena.</span><span class="sxs-lookup"><span data-stu-id="a242c-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="a242c-363">Pokud je seznam prázdný, jsou povoleny všichni hostitelé.</span><span class="sxs-lookup"><span data-stu-id="a242c-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="a242c-364">Zástupný znak nejvyšší úrovně `*` umožňuje všem neprázdným hostitelům.</span><span class="sxs-lookup"><span data-stu-id="a242c-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="a242c-365">Zástupné znaky subdomény jsou povolené, ale neodpovídají kořenové doméně.</span><span class="sxs-lookup"><span data-stu-id="a242c-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="a242c-366">Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale nikoli ke kořenové doméně `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="a242c-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="a242c-367">Názvy hostitelů Unicode jsou povoleny, ale jsou převedeny na [Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</span><span class="sxs-lookup"><span data-stu-id="a242c-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="a242c-368">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí zahrnovat ohraničující závorky a musí být v [konvenční formě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="a242c-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="a242c-369">IPv6 adresy nejsou speciální – použita pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádné kanonikalizace.</span><span class="sxs-lookup"><span data-stu-id="a242c-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="a242c-370">Nepodaří-li se omezit povolené hostitele, může útočníkovi umožnit přístup k falešným odkazům generovanými službou.</span><span class="sxs-lookup"><span data-stu-id="a242c-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="a242c-371">Výchozí hodnota je prázdná `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="a242c-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="a242c-372">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="a242c-373">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-For` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a242c-374">Výchozí formát je `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="a242c-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="a242c-375">Určuje, které předávacíci by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="a242c-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="a242c-376">Seznam polí, která platí, najdete v tématu [ForwardedHeaders enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="a242c-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="a242c-377">Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="a242c-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="a242c-378">Výchozí hodnota je [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="a242c-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="a242c-379">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="a242c-380">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Host` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a242c-381">Výchozí formát je `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="a242c-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="a242c-382">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="a242c-383">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Proto` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a242c-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="a242c-384">Výchozí formát je `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="a242c-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="a242c-385">Omezuje počet položek v záhlavích, které jsou zpracovávány.</span><span class="sxs-lookup"><span data-stu-id="a242c-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="a242c-386">Nastavením `null` tohoto omezení zakážete, ale to by mělo být provedeno pouze v případě, že `KnownProxies` nebo `KnownNetworks` jsou nakonfigurovány.</span><span class="sxs-lookup"><span data-stu-id="a242c-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="a242c-387">Nastavení `null` nehodnoty je preventivní opatrnost (ale ne záruka) pro ochranu proti nesprávně konfigurovaným proxy a škodlivým požadavkům přicházejících ze dvoustranných kanálů v síti.</span><span class="sxs-lookup"><span data-stu-id="a242c-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="a242c-388">Middleware předávaných hlaviček zpracovává hlavičky v obráceném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="a242c-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a242c-389">Pokud je použita výchozí hodnota ( `1` ), bude zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="a242c-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="a242c-390">Výchozí formát je `1`.</span><span class="sxs-lookup"><span data-stu-id="a242c-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="a242c-391">Rozsahy adres známých sítí, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="a242c-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="a242c-392">Zadejte rozsahy IP adres pomocí zápisu CIDR (Classless prosměrování mezi doménami).</span><span class="sxs-lookup"><span data-stu-id="a242c-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="a242c-393">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="a242c-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a242c-394">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a242c-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a242c-395">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a242c-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a242c-396">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="a242c-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a242c-397">Výchozí hodnota je `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> obsahující jednu položku pro `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="a242c-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="a242c-398">Adresy známých proxy adres, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="a242c-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="a242c-399">Použijte `KnownProxies` k určení přesné shody IP adres.</span><span class="sxs-lookup"><span data-stu-id="a242c-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="a242c-400">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="a242c-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="a242c-401">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a242c-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a242c-402">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a242c-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="a242c-403">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="a242c-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="a242c-404">Výchozí hodnota je `IList` \<<xref:System.Net.IPAddress>> obsahující jednu položku pro `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="a242c-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="a242c-405">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="a242c-406">Výchozí formát je `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="a242c-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="a242c-407">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="a242c-408">Výchozí formát je `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="a242c-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="a242c-409">Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="a242c-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="a242c-410">Výchozí formát je `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="a242c-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="a242c-411">Vyžaduje, aby byl počet synchronizovaných hodnot hlaviček mezi zpracovávaným [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="a242c-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="a242c-412">Výchozí hodnota v ASP.NET Core 1. x je `true` .</span><span class="sxs-lookup"><span data-stu-id="a242c-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="a242c-413">Výchozí hodnota v ASP.NET Core 2,0 nebo novější je `false` .</span><span class="sxs-lookup"><span data-stu-id="a242c-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="a242c-414">Scénáře a případy použití</span><span class="sxs-lookup"><span data-stu-id="a242c-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="a242c-415">Pokud není možné přidat předávané hlavičky a všechny požadavky jsou zabezpečené</span><span class="sxs-lookup"><span data-stu-id="a242c-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="a242c-416">V některých případech nemusí být možné přidat předávaná záhlaví do požadavků, které jsou proxy k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a242c-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="a242c-417">Pokud proxy vynucuje, že všechny veřejné externí požadavky jsou HTTPS, můžete schéma ručně nastavit v části `Startup.Configure` před použitím libovolného typu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="a242c-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="a242c-418">Tento kód může být zakázán pomocí proměnné prostředí nebo jiného nastavení konfigurace ve vývojovém nebo přípravném prostředí.</span><span class="sxs-lookup"><span data-stu-id="a242c-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="a242c-419">Práce se základními a proxy servery, které mění cestu požadavku</span><span class="sxs-lookup"><span data-stu-id="a242c-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="a242c-420">Některé proxy servery předávají cestu beze změny, ale se základní cestou aplikace, která by se měla odebrat, aby směrování fungovalo správně.</span><span class="sxs-lookup"><span data-stu-id="a242c-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="a242c-421">Middleware [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware rozdělí cestu do [HttpRequest. cesta](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace do [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="a242c-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="a242c-422">Pokud `/foo` je základní cesta aplikace pro cestu k proxy serveru předaná jako `/foo/api/1` , middleware nastaví `Request.PathBase` na `/foo` a `Request.Path` do `/api/1` pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="a242c-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="a242c-423">Původní cesta a základ cesty se znovu aplikují, když se middleware znovu zavolá v opačném případě.</span><span class="sxs-lookup"><span data-stu-id="a242c-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="a242c-424">Další informace o zpracování pořadí middlewaru najdete v tématu <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="a242c-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="a242c-425">Pokud proxy ořízne cestu (například předávání `/foo/api/1` na `/api/1` ), opravte přesměrování a odkazy nastavením vlastnosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) žádosti:</span><span class="sxs-lookup"><span data-stu-id="a242c-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="a242c-426">Pokud proxy přidává data cest, zahodí část cesty pro opravu přesměrování a odkazy pomocí <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> a přiřazení k <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a242c-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="a242c-427">Konfigurace proxy serveru, který používá jiné názvy hlaviček</span><span class="sxs-lookup"><span data-stu-id="a242c-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="a242c-428">Pokud proxy nepoužívá hlavičky s názvem `X-Forwarded-For` a `X-Forwarded-Proto` k přeposlání informací o adrese proxy/portu a informace o původním schématu, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným proxy serverem:</span><span class="sxs-lookup"><span data-stu-id="a242c-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="a242c-429">Konfigurace pro adresu IPv4 reprezentovanou jako IPv6 adresa</span><span class="sxs-lookup"><span data-stu-id="a242c-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="a242c-430">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaného v protokolu IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="a242c-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="a242c-431">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="a242c-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="a242c-432">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="a242c-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="a242c-433">V následujícím příkladu se síťová adresa, která poskytuje předávaná záhlaví, přidá do `KnownNetworks` seznamu ve formátu protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="a242c-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="a242c-434">Adresa IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="a242c-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="a242c-435">Převedená adresa IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="a242c-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="a242c-436">Délka převedené předpony: 104</span><span class="sxs-lookup"><span data-stu-id="a242c-436">Converted prefix length: 104</span></span>

<span data-ttu-id="a242c-437">Adresu můžete také uvést v šestnáctkovém formátu ( `10.11.12.1` reprezentované protokolem IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="a242c-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="a242c-438">Při převodu adresy IPv4 na IPv6 přidejte 96 k délce předpony CIDR ( `8` v příkladu), aby se zohlednila další `::ffff:` předpona IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="a242c-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="a242c-439">Předejte schéma pro reverzní proxy servery se systémy Linux a non-IIS.</span><span class="sxs-lookup"><span data-stu-id="a242c-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="a242c-440">Aplikace, které volají <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> a <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umísťují lokalitu do nekonečné smyčky, pokud jsou nasazené do azure Linux App Service, virtuální počítač Azure Linux (VM) nebo za jakýkoli jiný reverzní proxy server, kromě služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a242c-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="a242c-441">Protokol TLS je ukončen reverzním proxy serverem a Kestrel není informován o správném schématu požadavků.</span><span class="sxs-lookup"><span data-stu-id="a242c-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="a242c-442">OAuth a OIDC v této konfiguraci selže, protože generují nesprávná přesměrování.</span><span class="sxs-lookup"><span data-stu-id="a242c-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="a242c-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Přidá a nakonfiguruje middleware předávaných hlaviček při spuštění za IIS, ale neexistuje žádná vyhovující Automatická konfigurace pro Linux (integrace Apache nebo Nginx).</span><span class="sxs-lookup"><span data-stu-id="a242c-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="a242c-444">Pro přeposílání schématu od proxy serveru ve scénářích mimo službu IIS přidejte a nakonfigurujte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="a242c-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="a242c-445">V `Startup.ConfigureServices` použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="a242c-445">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="a242c-446">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="a242c-446">Troubleshoot</span></span>

<span data-ttu-id="a242c-447">Když hlavičky nejsou předávány podle očekávání, povolte [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a242c-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a242c-448">Pokud protokoly neposkytují dostatek informací pro řešení tohoto problému, vytvořte výčet hlaviček požadavků přijatých serverem.</span><span class="sxs-lookup"><span data-stu-id="a242c-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="a242c-449">K zápisu hlaviček požadavků do odpovědi aplikace nebo k protokolování hlaviček použijte vložený middlewarový middleware.</span><span class="sxs-lookup"><span data-stu-id="a242c-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="a242c-450">Chcete-li zapsat hlavičky do odpovědi aplikace, umístěte následující vložený middleware terminálu hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="a242c-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="a242c-451">Do protokolů můžete zapisovat místo těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a242c-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="a242c-452">Zápis do protokolů umožňuje, aby lokalita fungovala normálně během ladění.</span><span class="sxs-lookup"><span data-stu-id="a242c-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="a242c-453">Zápis protokolů místo textu odpovědi:</span><span class="sxs-lookup"><span data-stu-id="a242c-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="a242c-454">Vložení `ILogger<Startup>` do `Startup` třídy, jak je popsáno v tématu [vytvoření protokolů při spuštění](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="a242c-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="a242c-455">Vložte následující vložený middleware hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="a242c-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a242c-456">Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty přesunuty na `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="a242c-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="a242c-457">Pokud existuje více hodnot v dané hlavičce, middleware pro přesměrované hlavičky zpracovává záhlaví v opačném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="a242c-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="a242c-458">Výchozí hodnota `ForwardLimit` je `1` (jedna), takže je zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="a242c-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="a242c-459">Původní Vzdálená IP adresa žádosti se musí shodovat s položkou v `KnownProxies` `KnownNetworks` seznamech nebo před zpracováním předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a242c-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="a242c-460">To omezuje falšování hlaviček tím, že nepřijímá předávací servery od nedůvěryhodných proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="a242c-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="a242c-461">Při zjištění neznámého proxy serveru se zobrazí zpráva s informacemi o adrese proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="a242c-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="a242c-462">V předchozím příkladu je 10.0.0.100 proxy server.</span><span class="sxs-lookup"><span data-stu-id="a242c-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="a242c-463">Pokud je server důvěryhodným proxy serverem, přidejte IP adresu serveru do `KnownProxies` (nebo přidejte důvěryhodnou síť do `KnownNetworks` ) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a242c-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a242c-464">Další informace najdete v části [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="a242c-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="a242c-465">Povoluje pouze důvěryhodné proxy servery a sítě pro přeposílání hlaviček.</span><span class="sxs-lookup"><span data-stu-id="a242c-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="a242c-466">V opačném případě jsou možné útoky s [falešnou IP adresou](https://www.iplocation.net/ip-spoofing) .</span><span class="sxs-lookup"><span data-stu-id="a242c-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a242c-467">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a242c-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="a242c-468">Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET Core zvýšení zabezpečení oprávnění</span><span class="sxs-lookup"><span data-stu-id="a242c-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

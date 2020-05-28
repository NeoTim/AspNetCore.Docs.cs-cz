---
<span data-ttu-id="3c946-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-102">'Blazor'</span></span>
- <span data-ttu-id="3c946-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-103">'Identity'</span></span>
- <span data-ttu-id="3c946-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-105">'Razor'</span></span>
- <span data-ttu-id="3c946-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-106">'SignalR' uid:</span></span> 

---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="3c946-107">Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="3c946-107">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="3c946-108">Autor – [Chris Rossův](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="3c946-108">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3c946-109">V Doporučené konfiguraci pro ASP.NET Core se aplikace hostuje pomocí IIS/ASP. Modul NET Core, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="3c946-109">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="3c946-110">Proxy servery, nástroje pro vyrovnávání zatížení a další síťová zařízení často překrývají informace o žádosti předtím, než dosáhnou aplikace:</span><span class="sxs-lookup"><span data-stu-id="3c946-110">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="3c946-111">Pokud jsou požadavky HTTPS proxy serverem přes HTTP, původní schéma (HTTPS) se ztratí a musí se přenášet v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="3c946-111">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="3c946-112">Vzhledem k tomu, že aplikace obdrží požadavek od proxy serveru a ne jeho skutečný zdroj v Internetu nebo podnikové síti, musí být zdrojová adresa IP klienta předána také v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="3c946-112">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="3c946-113">Tyto informace můžou být důležité ve zpracování žádostí, například v přesměrování, ověřování, generování odkazů, vyhodnocení zásad a geografická poloha klienta.</span><span class="sxs-lookup"><span data-stu-id="3c946-113">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="3c946-114">Předávaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="3c946-114">Forwarded headers</span></span>

<span data-ttu-id="3c946-115">Podle konvence proxy předávají informace v hlavičkách protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="3c946-115">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="3c946-116">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="3c946-116">Header</span></span> | <span data-ttu-id="3c946-117">Description</span><span class="sxs-lookup"><span data-stu-id="3c946-117">Description</span></span> |
| ---
<span data-ttu-id="3c946-118">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-119">'Blazor'</span></span>
- <span data-ttu-id="3c946-120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-120">'Identity'</span></span>
- <span data-ttu-id="3c946-121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-121">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-122">'Razor'</span></span>
- <span data-ttu-id="3c946-123">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-123">'SignalR' uid:</span></span> 

<span data-ttu-id="3c946-124">--- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-125">'Blazor'</span></span>
- <span data-ttu-id="3c946-126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-126">'Identity'</span></span>
- <span data-ttu-id="3c946-127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-127">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-128">'Razor'</span></span>
- <span data-ttu-id="3c946-129">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3c946-130">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-131">'Blazor'</span></span>
- <span data-ttu-id="3c946-132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-132">'Identity'</span></span>
- <span data-ttu-id="3c946-133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-133">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-134">'Razor'</span></span>
- <span data-ttu-id="3c946-135">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3c946-136">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-137">'Blazor'</span></span>
- <span data-ttu-id="3c946-138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-138">'Identity'</span></span>
- <span data-ttu-id="3c946-139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-139">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-140">'Razor'</span></span>
- <span data-ttu-id="3c946-141">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-141">'SignalR' uid:</span></span> 

<span data-ttu-id="3c946-142">------ | | X-předáno – pro | Uchovává informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="3c946-142">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="3c946-143">Tento parametr může obsahovat IP adresy (a volitelně také čísla portů).</span><span class="sxs-lookup"><span data-stu-id="3c946-143">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="3c946-144">V řetězci proxy serverů označuje první parametr klienta, ve kterém se žádost poprvé nastavila.</span><span class="sxs-lookup"><span data-stu-id="3c946-144">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="3c946-145">Následující identifikátory proxy následují.</span><span class="sxs-lookup"><span data-stu-id="3c946-145">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="3c946-146">Poslední proxy server v řetězci není uveden v seznamu parametrů.</span><span class="sxs-lookup"><span data-stu-id="3c946-146">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="3c946-147">Poslední IP adresa proxy serveru a volitelně je číslo portu, které je k dispozici jako Vzdálená IP adresa v transportní vrstvě.</span><span class="sxs-lookup"><span data-stu-id="3c946-147">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="3c946-148">| | X-předané – proto | Hodnota původního schématu (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="3c946-148">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="3c946-149">Hodnota může být také seznamem schémat, pokud je požadavek procházen více proxy.</span><span class="sxs-lookup"><span data-stu-id="3c946-149">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="3c946-150">| | X-předávaný-Host | Původní hodnota pole hlavičky hostitele</span><span class="sxs-lookup"><span data-stu-id="3c946-150">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="3c946-151">Servery proxy většinou nemění hlavičku hostitele.</span><span class="sxs-lookup"><span data-stu-id="3c946-151">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="3c946-152">Viz informační [zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pro informace o chybě zabezpečení zvýšení oprávnění, která má vliv na systémy, ve kterých server proxy neověřuje nebo omezuje hlavičky hostitele na známé správné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3c946-152">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="3c946-153">Middleware předávaných hlaviček z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a vyplní související pole v <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="3c946-153">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="3c946-154">Aktualizace middlewaru:</span><span class="sxs-lookup"><span data-stu-id="3c946-154">The middleware updates:</span></span>

* <span data-ttu-id="3c946-155">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): nastavte pomocí `X-Forwarded-For` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="3c946-156">Další nastavení mají vliv na to, jak middleware nastaví `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="3c946-156">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="3c946-157">Podrobnosti najdete v tématu [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="3c946-157">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="3c946-158">[HttpContext. Request. schéma](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): nastavte pomocí `X-Forwarded-Proto` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="3c946-159">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): nastavte pomocí `X-Forwarded-Host` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="3c946-160">Je možné nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru u předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="3c946-160">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="3c946-161">Výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="3c946-161">The default settings are:</span></span>

* <span data-ttu-id="3c946-162">Mezi aplikací a zdrojem požadavků je jenom *jeden proxy server* .</span><span class="sxs-lookup"><span data-stu-id="3c946-162">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="3c946-163">Pro známé proxy servery a známé sítě jsou nakonfigurovány pouze adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="3c946-163">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="3c946-164">Předané hlavičky jsou pojmenovány `X-Forwarded-For` a `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="3c946-164">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="3c946-165">Ne všechna síťová zařízení přidávají `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3c946-165">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="3c946-166">Zeptejte se na pokyny výrobce zařízení, jestli proxy požadavky při přístupu do aplikace tyto hlavičky neobsahují.</span><span class="sxs-lookup"><span data-stu-id="3c946-166">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="3c946-167">Pokud zařízení používá jiné názvy hlaviček než `X-Forwarded-For` a `X-Forwarded-Proto` , nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným zařízením.</span><span class="sxs-lookup"><span data-stu-id="3c946-167">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="3c946-168">Další informace najdete v tématu [předané možnosti a konfigurace middlewarových hlaviček](#forwarded-headers-middleware-options) [pro proxy server, který používá jiné názvy hlaviček](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="3c946-168">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="3c946-169">Modul IIS/IIS Express a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c946-169">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="3c946-170">Middleware předávaných hlaviček je ve výchozím nastavení povolená službou [IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , když je aplikace hostovaná [mimo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) službu IIS a modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c946-170">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="3c946-171">Middleware předávaných hlaviček se aktivují, aby se nejdříve spouštěl v kanálu middleware s omezenou konfigurací určenou pro modul ASP.NET Core, protože se jedná o obavy týkající se důvěryhodnosti se předanými hlavičkami (například [falšování IP adres](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="3c946-171">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="3c946-172">Middleware je nakonfigurovaná pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a a je omezená na jeden localhost proxy.</span><span class="sxs-lookup"><span data-stu-id="3c946-172">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="3c946-173">Pokud se vyžaduje další konfigurace, přečtěte si téma [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="3c946-173">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="3c946-174">Další scénáře proxy server a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="3c946-174">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="3c946-175">Bez použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimo procesy](xref:host-and-deploy/iis/index#out-of-process-hosting-model)není middleware předávaných hlaviček ve výchozím nastavení povolená.</span><span class="sxs-lookup"><span data-stu-id="3c946-175">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="3c946-176">Middleware předaných hlaviček musí být povolená, aby aplikace zpracovala předávané hlavičky s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="3c946-176">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="3c946-177">Po povolení middlewaru, pokud není <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zadáno pro middleware, výchozí [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="3c946-177">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="3c946-178">Nakonfigurujte middleware s nástrojem <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> na předáte `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3c946-178">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3c946-179">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu v `Startup.Configure` před voláním jiného middleware:</span><span class="sxs-lookup"><span data-stu-id="3c946-179">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="3c946-180">Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` nebo přímo k metodě rozšíření s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , výchozí hlavičky pro přeposílání jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="3c946-180">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="3c946-181"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Vlastnost musí být nakonfigurována s hlavičkou pro přeposílání.</span><span class="sxs-lookup"><span data-stu-id="3c946-181">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="3c946-182">Konfigurace nginx</span><span class="sxs-lookup"><span data-stu-id="3c946-182">Nginx configuration</span></span>

<span data-ttu-id="3c946-183">Pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a naleznete informace v tématu <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="3c946-183">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="3c946-184">Další informace najdete v tématu [Nginx: použití předané hlavičky](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="3c946-184">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="3c946-185">Konfigurace Apache</span><span class="sxs-lookup"><span data-stu-id="3c946-185">Apache configuration</span></span>

<span data-ttu-id="3c946-186">`X-Forwarded-For`se přidá automaticky (viz část [modulu Apache mod_proxy: hlavičky žádosti reverzního proxy serveru](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="3c946-186">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="3c946-187">Informace o tom, jak přeslat `X-Forwarded-Proto` hlavičku, najdete v tématu <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="3c946-187">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="3c946-188">Možnosti middlewaru předávaných hlaviček</span><span class="sxs-lookup"><span data-stu-id="3c946-188">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="3c946-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>řízení chování middlewaru předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="3c946-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="3c946-190">Následující příklad změní výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="3c946-190">The following example changes the default values:</span></span>

* <span data-ttu-id="3c946-191">Omezte počet záznamů v předaných hlavičkách na `2` .</span><span class="sxs-lookup"><span data-stu-id="3c946-191">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="3c946-192">Přidejte známou adresu proxy serveru `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="3c946-192">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="3c946-193">Změňte název předané hlavičky z výchozí `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="3c946-193">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="3c946-194">Možnost</span><span class="sxs-lookup"><span data-stu-id="3c946-194">Option</span></span> | <span data-ttu-id="3c946-195">Description</span><span class="sxs-lookup"><span data-stu-id="3c946-195">Description</span></span> |
| ---
<span data-ttu-id="3c946-196">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-197">'Blazor'</span></span>
- <span data-ttu-id="3c946-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-198">'Identity'</span></span>
- <span data-ttu-id="3c946-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-200">'Razor'</span></span>
- <span data-ttu-id="3c946-201">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-201">'SignalR' uid:</span></span> 

<span data-ttu-id="3c946-202">--- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-203">'Blazor'</span></span>
- <span data-ttu-id="3c946-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-204">'Identity'</span></span>
- <span data-ttu-id="3c946-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-206">'Razor'</span></span>
- <span data-ttu-id="3c946-207">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3c946-208">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-209">'Blazor'</span></span>
- <span data-ttu-id="3c946-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-210">'Identity'</span></span>
- <span data-ttu-id="3c946-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-212">'Razor'</span></span>
- <span data-ttu-id="3c946-213">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3c946-214">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-215">'Blazor'</span></span>
- <span data-ttu-id="3c946-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-216">'Identity'</span></span>
- <span data-ttu-id="3c946-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-218">'Razor'</span></span>
- <span data-ttu-id="3c946-219">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-219">'SignalR' uid:</span></span> 

<span data-ttu-id="3c946-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Omezí hostitele v `X-Forwarded-Host` hlavičce na zadané hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3c946-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="3c946-221">Hodnoty se porovnávají pomocí pořadí – ignorovat – případ.</span><span class="sxs-lookup"><span data-stu-id="3c946-221">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="3c946-222">Čísla portů musí být vyloučena.</span><span class="sxs-lookup"><span data-stu-id="3c946-222">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="3c946-223">Pokud je seznam prázdný, jsou povoleny všichni hostitelé.</span><span class="sxs-lookup"><span data-stu-id="3c946-223">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="3c946-224">Zástupný znak nejvyšší úrovně `*` umožňuje všem neprázdným hostitelům.</span><span class="sxs-lookup"><span data-stu-id="3c946-224">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="3c946-225">Zástupné znaky subdomény jsou povolené, ale neodpovídají kořenové doméně.</span><span class="sxs-lookup"><span data-stu-id="3c946-225">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="3c946-226">Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale nikoli ke kořenové doméně `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="3c946-226">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="3c946-227">Názvy hostitelů Unicode jsou povoleny, ale jsou převedeny na [Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</span><span class="sxs-lookup"><span data-stu-id="3c946-227">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="3c946-228">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí zahrnovat ohraničující závorky a musí být v [konvenční formě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="3c946-228">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="3c946-229">IPv6 adresy nejsou speciální – použita pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádné kanonikalizace.</span><span class="sxs-lookup"><span data-stu-id="3c946-229">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="3c946-230">Nepodaří-li se omezit povolené hostitele, může útočníkovi umožnit přístup k falešným odkazům generovanými službou.</span><span class="sxs-lookup"><span data-stu-id="3c946-230">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="3c946-231">Výchozí hodnota je prázdná `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="3c946-231">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="3c946-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="3c946-233">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-For` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-233">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="3c946-234">Výchozí formát je `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="3c946-234">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="3c946-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Určuje, které předávacíci by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="3c946-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="3c946-236">Seznam polí, která platí, najdete v tématu [ForwardedHeaders enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="3c946-236">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="3c946-237">Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="3c946-237">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="3c946-238">Výchozí hodnota je [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="3c946-238">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="3c946-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="3c946-240">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Host` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-240">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="3c946-241">Výchozí formát je `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="3c946-241">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="3c946-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="3c946-243">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Proto` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-243">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="3c946-244">Výchozí formát je `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="3c946-244">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="3c946-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Omezuje počet položek v záhlavích, které jsou zpracovávány.</span><span class="sxs-lookup"><span data-stu-id="3c946-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="3c946-246">Nastavením `null` tohoto omezení zakážete, ale to by mělo být provedeno pouze v případě, že `KnownProxies` nebo `KnownNetworks` jsou nakonfigurovány.</span><span class="sxs-lookup"><span data-stu-id="3c946-246">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="3c946-247">Nastavení `null` nehodnoty je preventivní opatrnost (ale ne záruka) pro ochranu proti nesprávně konfigurovaným proxy a škodlivým požadavkům přicházejících ze dvoustranných kanálů v síti.</span><span class="sxs-lookup"><span data-stu-id="3c946-247">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="3c946-248">Middleware předávaných hlaviček zpracovává hlavičky v obráceném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="3c946-248">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="3c946-249">Pokud je použita výchozí hodnota ( `1` ), bude zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="3c946-249">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="3c946-250">Výchozí formát je `1`.</span><span class="sxs-lookup"><span data-stu-id="3c946-250">The default is `1`.</span></span> <span data-ttu-id="3c946-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Rozsahy adres známých sítí, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="3c946-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="3c946-252">Zadejte rozsahy IP adres pomocí zápisu CIDR (Classless prosměrování mezi doménami).</span><span class="sxs-lookup"><span data-stu-id="3c946-252">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="3c946-253">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="3c946-253">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="3c946-254">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="3c946-254">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="3c946-255">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="3c946-255">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="3c946-256">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="3c946-256">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="3c946-257">Výchozí hodnota je `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> obsahující jednu položku pro `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="3c946-257">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="3c946-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy známých proxy adres, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="3c946-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="3c946-259">Použijte `KnownProxies` k určení přesné shody IP adres.</span><span class="sxs-lookup"><span data-stu-id="3c946-259">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="3c946-260">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="3c946-260">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="3c946-261">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="3c946-261">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="3c946-262">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="3c946-262">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="3c946-263">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="3c946-263">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="3c946-264">Výchozí hodnota je `IList` \<<xref:System.Net.IPAddress>> obsahující jednu položku pro `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="3c946-264">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="3c946-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="3c946-266">Výchozí formát je `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="3c946-266">The default is `X-Original-For`.</span></span> <span data-ttu-id="3c946-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="3c946-268">Výchozí formát je `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="3c946-268">The default is `X-Original-Host`.</span></span> <span data-ttu-id="3c946-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="3c946-270">Výchozí formát je `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="3c946-270">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="3c946-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Vyžaduje, aby byl počet synchronizovaných hodnot hlaviček mezi zpracovávaným [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="3c946-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="3c946-272">Výchozí hodnota v ASP.NET Core 1. x je `true` .</span><span class="sxs-lookup"><span data-stu-id="3c946-272">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="3c946-273">Výchozí hodnota v ASP.NET Core 2,0 nebo novější je `false` .</span><span class="sxs-lookup"><span data-stu-id="3c946-273">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="3c946-274">Scénáře a případy použití</span><span class="sxs-lookup"><span data-stu-id="3c946-274">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="3c946-275">Pokud není možné přidat předávané hlavičky a všechny požadavky jsou zabezpečené</span><span class="sxs-lookup"><span data-stu-id="3c946-275">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="3c946-276">V některých případech nemusí být možné přidat předávaná záhlaví do požadavků, které jsou proxy k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3c946-276">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="3c946-277">Pokud proxy vynucuje, že všechny veřejné externí požadavky jsou HTTPS, můžete schéma ručně nastavit v části `Startup.Configure` před použitím libovolného typu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="3c946-277">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="3c946-278">Tento kód může být zakázán pomocí proměnné prostředí nebo jiného nastavení konfigurace ve vývojovém nebo přípravném prostředí.</span><span class="sxs-lookup"><span data-stu-id="3c946-278">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="3c946-279">Práce se základními a proxy servery, které mění cestu požadavku</span><span class="sxs-lookup"><span data-stu-id="3c946-279">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="3c946-280">Některé proxy servery předávají cestu beze změny, ale se základní cestou aplikace, která by se měla odebrat, aby směrování fungovalo správně.</span><span class="sxs-lookup"><span data-stu-id="3c946-280">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="3c946-281">Middleware [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware rozdělí cestu do [HttpRequest. cesta](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace do [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="3c946-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="3c946-282">Pokud `/foo` je základní cesta aplikace pro cestu k proxy serveru předaná jako `/foo/api/1` , middleware nastaví `Request.PathBase` na `/foo` a `Request.Path` do `/api/1` pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="3c946-282">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="3c946-283">Původní cesta a základ cesty se znovu aplikují, když se middleware znovu zavolá v opačném případě.</span><span class="sxs-lookup"><span data-stu-id="3c946-283">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="3c946-284">Další informace o zpracování pořadí middlewaru najdete v tématu <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="3c946-284">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="3c946-285">Pokud proxy ořízne cestu (například předávání `/foo/api/1` na `/api/1` ), opravte přesměrování a odkazy nastavením vlastnosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) žádosti:</span><span class="sxs-lookup"><span data-stu-id="3c946-285">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="3c946-286">Pokud proxy přidává data cest, zahodí část cesty pro opravu přesměrování a odkazy pomocí <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> a přiřazení k <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3c946-286">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="3c946-287">Konfigurace proxy serveru, který používá jiné názvy hlaviček</span><span class="sxs-lookup"><span data-stu-id="3c946-287">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="3c946-288">Pokud proxy nepoužívá hlavičky s názvem `X-Forwarded-For` a `X-Forwarded-Proto` k přeposlání informací o adrese proxy/portu a informace o původním schématu, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným proxy serverem:</span><span class="sxs-lookup"><span data-stu-id="3c946-288">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="3c946-289">Konfigurace pro adresu IPv4 reprezentovanou jako IPv6 adresa</span><span class="sxs-lookup"><span data-stu-id="3c946-289">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="3c946-290">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaného v protokolu IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="3c946-290">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="3c946-291">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="3c946-291">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="3c946-292">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="3c946-292">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="3c946-293">V následujícím příkladu se síťová adresa, která poskytuje předávaná záhlaví, přidá do `KnownNetworks` seznamu ve formátu protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="3c946-293">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="3c946-294">Adresa IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="3c946-294">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="3c946-295">Převedená adresa IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="3c946-295">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="3c946-296">Délka převedené předpony: 104</span><span class="sxs-lookup"><span data-stu-id="3c946-296">Converted prefix length: 104</span></span>

<span data-ttu-id="3c946-297">Adresu můžete také uvést v šestnáctkovém formátu ( `10.11.12.1` reprezentované protokolem IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="3c946-297">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="3c946-298">Při převodu adresy IPv4 na IPv6 přidejte 96 k délce předpony CIDR ( `8` v příkladu), aby se zohlednila další `::ffff:` předpona IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="3c946-298">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="3c946-299">Předejte schéma pro reverzní proxy servery se systémy Linux a non-IIS.</span><span class="sxs-lookup"><span data-stu-id="3c946-299">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="3c946-300">Aplikace, které volají <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> a <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umísťují lokalitu do nekonečné smyčky, pokud jsou nasazené do azure Linux App Service, virtuální počítač Azure Linux (VM) nebo za jakýkoli jiný reverzní proxy server, kromě služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3c946-300">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="3c946-301">Protokol TLS je ukončen reverzním proxy serverem a Kestrel není informován o správném schématu požadavků.</span><span class="sxs-lookup"><span data-stu-id="3c946-301">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="3c946-302">OAuth a OIDC v této konfiguraci selže, protože generují nesprávná přesměrování.</span><span class="sxs-lookup"><span data-stu-id="3c946-302">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="3c946-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Přidá a nakonfiguruje middleware předávaných hlaviček při spuštění za IIS, ale neexistuje žádná vyhovující Automatická konfigurace pro Linux (integrace Apache nebo Nginx).</span><span class="sxs-lookup"><span data-stu-id="3c946-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="3c946-304">Pro přeposílání schématu od proxy serveru ve scénářích mimo službu IIS přidejte a nakonfigurujte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="3c946-304">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="3c946-305">V `Startup.ConfigureServices` použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="3c946-305">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="3c946-306">Předávání certifikátů</span><span class="sxs-lookup"><span data-stu-id="3c946-306">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="3c946-307">Azure</span><span class="sxs-lookup"><span data-stu-id="3c946-307">Azure</span></span>

<span data-ttu-id="3c946-308">Pokud chcete nakonfigurovat Azure App Service pro předávání certifikátů, přečtěte si téma [Konfigurace vzájemného ověřování TLS pro Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="3c946-308">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="3c946-309">Následující pokyny se týkají konfigurace aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c946-309">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="3c946-310">V `Startup.Configure` přidejte následující kód před volání do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="3c946-310">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="3c946-311">Nakonfigurujte middleware předávání certifikátů a určete název hlavičky, kterou používá Azure.</span><span class="sxs-lookup"><span data-stu-id="3c946-311">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="3c946-312">V `Startup.ConfigureServices` přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:</span><span class="sxs-lookup"><span data-stu-id="3c946-312">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="3c946-313">Další webové proxy servery</span><span class="sxs-lookup"><span data-stu-id="3c946-313">Other web proxies</span></span>

<span data-ttu-id="3c946-314">Pokud se používá proxy server, který není službou IIS nebo Azure App Service směrování žádostí na aplikace (ARR), nakonfigurujte proxy server tak, aby předal certifikát, který přijal v hlavičce protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="3c946-314">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="3c946-315">V `Startup.Configure` přidejte následující kód před volání do `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="3c946-315">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="3c946-316">Nakonfigurujte middleware předávání certifikátů a zadejte název záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-316">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="3c946-317">V `Startup.ConfigureServices` přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:</span><span class="sxs-lookup"><span data-stu-id="3c946-317">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="3c946-318">Pokud proxy server nemá kódování Base64 (jako je případ s Nginx), nastavte `HeaderConverter` možnost.</span><span class="sxs-lookup"><span data-stu-id="3c946-318">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="3c946-319">Vezměte v úvahu následující příklad `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3c946-319">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="3c946-320">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="3c946-320">Troubleshoot</span></span>

<span data-ttu-id="3c946-321">Když hlavičky nejsou předávány podle očekávání, povolte [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="3c946-321">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3c946-322">Pokud protokoly neposkytují dostatek informací pro řešení tohoto problému, vytvořte výčet hlaviček požadavků přijatých serverem.</span><span class="sxs-lookup"><span data-stu-id="3c946-322">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="3c946-323">K zápisu hlaviček požadavků do odpovědi aplikace nebo k protokolování hlaviček použijte vložený middlewarový middleware.</span><span class="sxs-lookup"><span data-stu-id="3c946-323">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="3c946-324">Chcete-li zapsat hlavičky do odpovědi aplikace, umístěte následující vložený middleware terminálu hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3c946-324">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="3c946-325">Do protokolů můžete zapisovat místo těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3c946-325">You can write to logs instead of the response body.</span></span> <span data-ttu-id="3c946-326">Zápis do protokolů umožňuje, aby lokalita fungovala normálně během ladění.</span><span class="sxs-lookup"><span data-stu-id="3c946-326">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="3c946-327">Zápis protokolů místo textu odpovědi:</span><span class="sxs-lookup"><span data-stu-id="3c946-327">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="3c946-328">Vložení `ILogger<Startup>` do `Startup` třídy, jak je popsáno v tématu [vytvoření protokolů při spuštění](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="3c946-328">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="3c946-329">Vložte následující vložený middleware hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="3c946-329">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="3c946-330">Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty přesunuty na `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="3c946-330">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="3c946-331">Pokud existuje více hodnot v dané hlavičce, middleware pro přesměrované hlavičky zpracovává záhlaví v opačném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="3c946-331">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="3c946-332">Výchozí hodnota `ForwardLimit` je `1` (jedna), takže je zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="3c946-332">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="3c946-333">Původní Vzdálená IP adresa žádosti se musí shodovat s položkou v `KnownProxies` `KnownNetworks` seznamech nebo před zpracováním předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="3c946-333">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="3c946-334">To omezuje falšování hlaviček tím, že nepřijímá předávací servery od nedůvěryhodných proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="3c946-334">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="3c946-335">Při zjištění neznámého proxy serveru se zobrazí zpráva s informacemi o adrese proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="3c946-335">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="3c946-336">V předchozím příkladu je 10.0.0.100 proxy server.</span><span class="sxs-lookup"><span data-stu-id="3c946-336">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="3c946-337">Pokud je server důvěryhodným proxy serverem, přidejte IP adresu serveru do `KnownProxies` (nebo přidejte důvěryhodnou síť do `KnownNetworks` ) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3c946-337">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3c946-338">Další informace najdete v části [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="3c946-338">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="3c946-339">Povoluje pouze důvěryhodné proxy servery a sítě pro přeposílání hlaviček.</span><span class="sxs-lookup"><span data-stu-id="3c946-339">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="3c946-340">V opačném případě jsou možné útoky s [falešnou IP adresou](https://www.iplocation.net/ip-spoofing) .</span><span class="sxs-lookup"><span data-stu-id="3c946-340">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c946-341">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3c946-341">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="3c946-342">Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET Core zvýšení zabezpečení oprávnění</span><span class="sxs-lookup"><span data-stu-id="3c946-342">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3c946-343">V Doporučené konfiguraci pro ASP.NET Core se aplikace hostuje pomocí IIS/ASP. Modul NET Core, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="3c946-343">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="3c946-344">Proxy servery, nástroje pro vyrovnávání zatížení a další síťová zařízení často překrývají informace o žádosti předtím, než dosáhnou aplikace:</span><span class="sxs-lookup"><span data-stu-id="3c946-344">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="3c946-345">Pokud jsou požadavky HTTPS proxy serverem přes HTTP, původní schéma (HTTPS) se ztratí a musí se přenášet v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="3c946-345">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="3c946-346">Vzhledem k tomu, že aplikace obdrží požadavek od proxy serveru a ne jeho skutečný zdroj v Internetu nebo podnikové síti, musí být zdrojová adresa IP klienta předána také v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="3c946-346">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="3c946-347">Tyto informace můžou být důležité ve zpracování žádostí, například v přesměrování, ověřování, generování odkazů, vyhodnocení zásad a geografická poloha klienta.</span><span class="sxs-lookup"><span data-stu-id="3c946-347">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="3c946-348">Předávaná záhlaví</span><span class="sxs-lookup"><span data-stu-id="3c946-348">Forwarded headers</span></span>

<span data-ttu-id="3c946-349">Podle konvence proxy předávají informace v hlavičkách protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="3c946-349">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="3c946-350">Záhlaví</span><span class="sxs-lookup"><span data-stu-id="3c946-350">Header</span></span> | <span data-ttu-id="3c946-351">Description</span><span class="sxs-lookup"><span data-stu-id="3c946-351">Description</span></span> |
| ---
<span data-ttu-id="3c946-352">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-353">'Blazor'</span></span>
- <span data-ttu-id="3c946-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-354">'Identity'</span></span>
- <span data-ttu-id="3c946-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-356">'Razor'</span></span>
- <span data-ttu-id="3c946-357">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-357">'SignalR' uid:</span></span> 

<span data-ttu-id="3c946-358">--- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-359">'Blazor'</span></span>
- <span data-ttu-id="3c946-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-360">'Identity'</span></span>
- <span data-ttu-id="3c946-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-362">'Razor'</span></span>
- <span data-ttu-id="3c946-363">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3c946-364">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-365">'Blazor'</span></span>
- <span data-ttu-id="3c946-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-366">'Identity'</span></span>
- <span data-ttu-id="3c946-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-368">'Razor'</span></span>
- <span data-ttu-id="3c946-369">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3c946-370">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-371">'Blazor'</span></span>
- <span data-ttu-id="3c946-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-372">'Identity'</span></span>
- <span data-ttu-id="3c946-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-374">'Razor'</span></span>
- <span data-ttu-id="3c946-375">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-375">'SignalR' uid:</span></span> 

<span data-ttu-id="3c946-376">------ | | X-předáno – pro | Uchovává informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="3c946-376">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="3c946-377">Tento parametr může obsahovat IP adresy (a volitelně také čísla portů).</span><span class="sxs-lookup"><span data-stu-id="3c946-377">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="3c946-378">V řetězci proxy serverů označuje první parametr klienta, ve kterém se žádost poprvé nastavila.</span><span class="sxs-lookup"><span data-stu-id="3c946-378">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="3c946-379">Následující identifikátory proxy následují.</span><span class="sxs-lookup"><span data-stu-id="3c946-379">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="3c946-380">Poslední proxy server v řetězci není uveden v seznamu parametrů.</span><span class="sxs-lookup"><span data-stu-id="3c946-380">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="3c946-381">Poslední IP adresa proxy serveru a volitelně je číslo portu, které je k dispozici jako Vzdálená IP adresa v transportní vrstvě.</span><span class="sxs-lookup"><span data-stu-id="3c946-381">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="3c946-382">| | X-předané – proto | Hodnota původního schématu (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="3c946-382">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="3c946-383">Hodnota může být také seznamem schémat, pokud je požadavek procházen více proxy.</span><span class="sxs-lookup"><span data-stu-id="3c946-383">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="3c946-384">| | X-předávaný-Host | Původní hodnota pole hlavičky hostitele</span><span class="sxs-lookup"><span data-stu-id="3c946-384">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="3c946-385">Servery proxy většinou nemění hlavičku hostitele.</span><span class="sxs-lookup"><span data-stu-id="3c946-385">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="3c946-386">Viz informační [zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pro informace o chybě zabezpečení zvýšení oprávnění, která má vliv na systémy, ve kterých server proxy neověřuje nebo omezuje hlavičky hostitele na známé správné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3c946-386">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="3c946-387">Middleware předávaných hlaviček z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a vyplní související pole v <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="3c946-387">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="3c946-388">Aktualizace middlewaru:</span><span class="sxs-lookup"><span data-stu-id="3c946-388">The middleware updates:</span></span>

* <span data-ttu-id="3c946-389">[HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): nastavte pomocí `X-Forwarded-For` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="3c946-390">Další nastavení mají vliv na to, jak middleware nastaví `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="3c946-390">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="3c946-391">Podrobnosti najdete v tématu [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="3c946-391">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="3c946-392">[HttpContext. Request. schéma](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): nastavte pomocí `X-Forwarded-Proto` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="3c946-393">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): nastavte pomocí `X-Forwarded-Host` hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="3c946-394">Je možné nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru u předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="3c946-394">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="3c946-395">Výchozí nastavení:</span><span class="sxs-lookup"><span data-stu-id="3c946-395">The default settings are:</span></span>

* <span data-ttu-id="3c946-396">Mezi aplikací a zdrojem požadavků je jenom *jeden proxy server* .</span><span class="sxs-lookup"><span data-stu-id="3c946-396">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="3c946-397">Pro známé proxy servery a známé sítě jsou nakonfigurovány pouze adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="3c946-397">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="3c946-398">Předané hlavičky jsou pojmenovány `X-Forwarded-For` a `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="3c946-398">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="3c946-399">Ne všechna síťová zařízení přidávají `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a bez další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3c946-399">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="3c946-400">Zeptejte se na pokyny výrobce zařízení, jestli proxy požadavky při přístupu do aplikace tyto hlavičky neobsahují.</span><span class="sxs-lookup"><span data-stu-id="3c946-400">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="3c946-401">Pokud zařízení používá jiné názvy hlaviček než `X-Forwarded-For` a `X-Forwarded-Proto` , nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným zařízením.</span><span class="sxs-lookup"><span data-stu-id="3c946-401">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="3c946-402">Další informace najdete v tématu [předané možnosti a konfigurace middlewarových hlaviček](#forwarded-headers-middleware-options) [pro proxy server, který používá jiné názvy hlaviček](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="3c946-402">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="3c946-403">Modul IIS/IIS Express a ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c946-403">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="3c946-404">Middleware předávaných hlaviček je ve výchozím nastavení povolená službou [IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , když je aplikace hostovaná [mimo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) službu IIS a modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3c946-404">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="3c946-405">Middleware předávaných hlaviček se aktivují, aby se nejdříve spouštěl v kanálu middleware s omezenou konfigurací určenou pro modul ASP.NET Core, protože se jedná o obavy týkající se důvěryhodnosti se předanými hlavičkami (například [falšování IP adres](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="3c946-405">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="3c946-406">Middleware je nakonfigurovaná pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a a je omezená na jeden localhost proxy.</span><span class="sxs-lookup"><span data-stu-id="3c946-406">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="3c946-407">Pokud se vyžaduje další konfigurace, přečtěte si téma [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="3c946-407">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="3c946-408">Další scénáře proxy server a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="3c946-408">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="3c946-409">Bez použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimo procesy](xref:host-and-deploy/iis/index#out-of-process-hosting-model)není middleware předávaných hlaviček ve výchozím nastavení povolená.</span><span class="sxs-lookup"><span data-stu-id="3c946-409">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="3c946-410">Middleware předaných hlaviček musí být povolená, aby aplikace zpracovala předávané hlavičky s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="3c946-410">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="3c946-411">Po povolení middlewaru, pokud není <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zadáno pro middleware, výchozí [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="3c946-411">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="3c946-412">Nakonfigurujte middleware s nástrojem <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> na předáte `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3c946-412">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3c946-413">Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu v `Startup.Configure` před voláním jiného middleware:</span><span class="sxs-lookup"><span data-stu-id="3c946-413">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="3c946-414">Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` nebo přímo k metodě rozšíření s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , výchozí hlavičky pro přeposílání jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="3c946-414">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="3c946-415"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Vlastnost musí být nakonfigurována s hlavičkou pro přeposílání.</span><span class="sxs-lookup"><span data-stu-id="3c946-415">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="3c946-416">Konfigurace nginx</span><span class="sxs-lookup"><span data-stu-id="3c946-416">Nginx configuration</span></span>

<span data-ttu-id="3c946-417">Pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a naleznete informace v tématu <xref:host-and-deploy/linux-nginx#configure-nginx> .</span><span class="sxs-lookup"><span data-stu-id="3c946-417">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="3c946-418">Další informace najdete v tématu [Nginx: použití předané hlavičky](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="3c946-418">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="3c946-419">Konfigurace Apache</span><span class="sxs-lookup"><span data-stu-id="3c946-419">Apache configuration</span></span>

<span data-ttu-id="3c946-420">`X-Forwarded-For`se přidá automaticky (viz část [modulu Apache mod_proxy: hlavičky žádosti reverzního proxy serveru](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="3c946-420">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="3c946-421">Informace o tom, jak přeslat `X-Forwarded-Proto` hlavičku, najdete v tématu <xref:host-and-deploy/linux-apache#configure-apache> .</span><span class="sxs-lookup"><span data-stu-id="3c946-421">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="3c946-422">Možnosti middlewaru předávaných hlaviček</span><span class="sxs-lookup"><span data-stu-id="3c946-422">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="3c946-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>řízení chování middlewaru předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="3c946-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="3c946-424">Následující příklad změní výchozí hodnoty:</span><span class="sxs-lookup"><span data-stu-id="3c946-424">The following example changes the default values:</span></span>

* <span data-ttu-id="3c946-425">Omezte počet záznamů v předaných hlavičkách na `2` .</span><span class="sxs-lookup"><span data-stu-id="3c946-425">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="3c946-426">Přidejte známou adresu proxy serveru `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="3c946-426">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="3c946-427">Změňte název předané hlavičky z výchozí `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="3c946-427">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="3c946-428">Možnost</span><span class="sxs-lookup"><span data-stu-id="3c946-428">Option</span></span> | <span data-ttu-id="3c946-429">Description</span><span class="sxs-lookup"><span data-stu-id="3c946-429">Description</span></span> |
| ---
<span data-ttu-id="3c946-430">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-431">'Blazor'</span></span>
- <span data-ttu-id="3c946-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-432">'Identity'</span></span>
- <span data-ttu-id="3c946-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-434">'Razor'</span></span>
- <span data-ttu-id="3c946-435">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-435">'SignalR' uid:</span></span> 

<span data-ttu-id="3c946-436">--- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-437">'Blazor'</span></span>
- <span data-ttu-id="3c946-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-438">'Identity'</span></span>
- <span data-ttu-id="3c946-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-440">'Razor'</span></span>
- <span data-ttu-id="3c946-441">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3c946-442">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-443">'Blazor'</span></span>
- <span data-ttu-id="3c946-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-444">'Identity'</span></span>
- <span data-ttu-id="3c946-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-446">'Razor'</span></span>
- <span data-ttu-id="3c946-447">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3c946-448">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="3c946-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3c946-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3c946-449">'Blazor'</span></span>
- <span data-ttu-id="3c946-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3c946-450">'Identity'</span></span>
- <span data-ttu-id="3c946-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3c946-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="3c946-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3c946-452">'Razor'</span></span>
- <span data-ttu-id="3c946-453">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="3c946-453">'SignalR' uid:</span></span> 

<span data-ttu-id="3c946-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Omezí hostitele v `X-Forwarded-Host` hlavičce na zadané hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3c946-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="3c946-455">Hodnoty se porovnávají pomocí pořadí – ignorovat – případ.</span><span class="sxs-lookup"><span data-stu-id="3c946-455">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="3c946-456">Čísla portů musí být vyloučena.</span><span class="sxs-lookup"><span data-stu-id="3c946-456">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="3c946-457">Pokud je seznam prázdný, jsou povoleny všichni hostitelé.</span><span class="sxs-lookup"><span data-stu-id="3c946-457">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="3c946-458">Zástupný znak nejvyšší úrovně `*` umožňuje všem neprázdným hostitelům.</span><span class="sxs-lookup"><span data-stu-id="3c946-458">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="3c946-459">Zástupné znaky subdomény jsou povolené, ale neodpovídají kořenové doméně.</span><span class="sxs-lookup"><span data-stu-id="3c946-459">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="3c946-460">Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale nikoli ke kořenové doméně `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="3c946-460">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="3c946-461">Názvy hostitelů Unicode jsou povoleny, ale jsou převedeny na [Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</span><span class="sxs-lookup"><span data-stu-id="3c946-461">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="3c946-462">[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí zahrnovat ohraničující závorky a musí být v [konvenční formě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="3c946-462">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="3c946-463">IPv6 adresy nejsou speciální – použita pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádné kanonikalizace.</span><span class="sxs-lookup"><span data-stu-id="3c946-463">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="3c946-464">Nepodaří-li se omezit povolené hostitele, může útočníkovi umožnit přístup k falešným odkazům generovanými službou.</span><span class="sxs-lookup"><span data-stu-id="3c946-464">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="3c946-465">Výchozí hodnota je prázdná `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="3c946-465">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="3c946-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="3c946-467">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-For` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-467">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="3c946-468">Výchozí formát je `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="3c946-468">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="3c946-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Určuje, které předávacíci by se měly zpracovat.</span><span class="sxs-lookup"><span data-stu-id="3c946-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="3c946-470">Seznam polí, která platí, najdete v tématu [ForwardedHeaders enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="3c946-470">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="3c946-471">Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="3c946-471">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="3c946-472">Výchozí hodnota je [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="3c946-472">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="3c946-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="3c946-474">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Host` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-474">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="3c946-475">Výchozí formát je `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="3c946-475">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="3c946-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="3c946-477">Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Proto` hlavičku, ale k přeposílání informací používá jiné záhlaví.</span><span class="sxs-lookup"><span data-stu-id="3c946-477">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="3c946-478">Výchozí formát je `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="3c946-478">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="3c946-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Omezuje počet položek v záhlavích, které jsou zpracovávány.</span><span class="sxs-lookup"><span data-stu-id="3c946-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="3c946-480">Nastavením `null` tohoto omezení zakážete, ale to by mělo být provedeno pouze v případě, že `KnownProxies` nebo `KnownNetworks` jsou nakonfigurovány.</span><span class="sxs-lookup"><span data-stu-id="3c946-480">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="3c946-481">Nastavení `null` nehodnoty je preventivní opatrnost (ale ne záruka) pro ochranu proti nesprávně konfigurovaným proxy a škodlivým požadavkům přicházejících ze dvoustranných kanálů v síti.</span><span class="sxs-lookup"><span data-stu-id="3c946-481">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="3c946-482">Middleware předávaných hlaviček zpracovává hlavičky v obráceném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="3c946-482">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="3c946-483">Pokud je použita výchozí hodnota ( `1` ), bude zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="3c946-483">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="3c946-484">Výchozí formát je `1`.</span><span class="sxs-lookup"><span data-stu-id="3c946-484">The default is `1`.</span></span> <span data-ttu-id="3c946-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Rozsahy adres známých sítí, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="3c946-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="3c946-486">Zadejte rozsahy IP adres pomocí zápisu CIDR (Classless prosměrování mezi doménami).</span><span class="sxs-lookup"><span data-stu-id="3c946-486">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="3c946-487">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="3c946-487">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="3c946-488">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="3c946-488">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="3c946-489">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="3c946-489">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="3c946-490">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="3c946-490">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="3c946-491">Výchozí hodnota je `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> obsahující jednu položku pro `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="3c946-491">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="3c946-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy známých proxy adres, ze kterých se mají přijímat předávací hlavičky.</span><span class="sxs-lookup"><span data-stu-id="3c946-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="3c946-493">Použijte `KnownProxies` k určení přesné shody IP adres.</span><span class="sxs-lookup"><span data-stu-id="3c946-493">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="3c946-494">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="3c946-494">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="3c946-495">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="3c946-495">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="3c946-496">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="3c946-496">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="3c946-497">Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .</span><span class="sxs-lookup"><span data-stu-id="3c946-497">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="3c946-498">Výchozí hodnota je `IList` \<<xref:System.Net.IPAddress>> obsahující jednu položku pro `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="3c946-498">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="3c946-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="3c946-500">Výchozí formát je `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="3c946-500">The default is `X-Original-For`.</span></span> <span data-ttu-id="3c946-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="3c946-502">Výchozí formát je `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="3c946-502">The default is `X-Original-Host`.</span></span> <span data-ttu-id="3c946-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span><span class="sxs-lookup"><span data-stu-id="3c946-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="3c946-504">Výchozí formát je `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="3c946-504">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="3c946-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Vyžaduje, aby byl počet synchronizovaných hodnot hlaviček mezi zpracovávaným [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .</span><span class="sxs-lookup"><span data-stu-id="3c946-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="3c946-506">Výchozí hodnota v ASP.NET Core 1. x je `true` .</span><span class="sxs-lookup"><span data-stu-id="3c946-506">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="3c946-507">Výchozí hodnota v ASP.NET Core 2,0 nebo novější je `false` .</span><span class="sxs-lookup"><span data-stu-id="3c946-507">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="3c946-508">Scénáře a případy použití</span><span class="sxs-lookup"><span data-stu-id="3c946-508">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="3c946-509">Pokud není možné přidat předávané hlavičky a všechny požadavky jsou zabezpečené</span><span class="sxs-lookup"><span data-stu-id="3c946-509">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="3c946-510">V některých případech nemusí být možné přidat předávaná záhlaví do požadavků, které jsou proxy k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3c946-510">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="3c946-511">Pokud proxy vynucuje, že všechny veřejné externí požadavky jsou HTTPS, můžete schéma ručně nastavit v části `Startup.Configure` před použitím libovolného typu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="3c946-511">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="3c946-512">Tento kód může být zakázán pomocí proměnné prostředí nebo jiného nastavení konfigurace ve vývojovém nebo přípravném prostředí.</span><span class="sxs-lookup"><span data-stu-id="3c946-512">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="3c946-513">Práce se základními a proxy servery, které mění cestu požadavku</span><span class="sxs-lookup"><span data-stu-id="3c946-513">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="3c946-514">Některé proxy servery předávají cestu beze změny, ale se základní cestou aplikace, která by se měla odebrat, aby směrování fungovalo správně.</span><span class="sxs-lookup"><span data-stu-id="3c946-514">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="3c946-515">Middleware [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware rozdělí cestu do [HttpRequest. cesta](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace do [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="3c946-515">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="3c946-516">Pokud `/foo` je základní cesta aplikace pro cestu k proxy serveru předaná jako `/foo/api/1` , middleware nastaví `Request.PathBase` na `/foo` a `Request.Path` do `/api/1` pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="3c946-516">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="3c946-517">Původní cesta a základ cesty se znovu aplikují, když se middleware znovu zavolá v opačném případě.</span><span class="sxs-lookup"><span data-stu-id="3c946-517">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="3c946-518">Další informace o zpracování pořadí middlewaru najdete v tématu <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="3c946-518">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="3c946-519">Pokud proxy ořízne cestu (například předávání `/foo/api/1` na `/api/1` ), opravte přesměrování a odkazy nastavením vlastnosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) žádosti:</span><span class="sxs-lookup"><span data-stu-id="3c946-519">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="3c946-520">Pokud proxy přidává data cest, zahodí část cesty pro opravu přesměrování a odkazy pomocí <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> a přiřazení k <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3c946-520">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="3c946-521">Konfigurace proxy serveru, který používá jiné názvy hlaviček</span><span class="sxs-lookup"><span data-stu-id="3c946-521">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="3c946-522">Pokud proxy nepoužívá hlavičky s názvem `X-Forwarded-For` a `X-Forwarded-Proto` k přeposlání informací o adrese proxy/portu a informace o původním schématu, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným proxy serverem:</span><span class="sxs-lookup"><span data-stu-id="3c946-522">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="3c946-523">Konfigurace pro adresu IPv4 reprezentovanou jako IPv6 adresa</span><span class="sxs-lookup"><span data-stu-id="3c946-523">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="3c946-524">Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaného v protokolu IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="3c946-524">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="3c946-525">Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="3c946-525">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="3c946-526">V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.</span><span class="sxs-lookup"><span data-stu-id="3c946-526">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="3c946-527">V následujícím příkladu se síťová adresa, která poskytuje předávaná záhlaví, přidá do `KnownNetworks` seznamu ve formátu protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="3c946-527">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="3c946-528">Adresa IPv4:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="3c946-528">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="3c946-529">Převedená adresa IPv6:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="3c946-529">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="3c946-530">Délka převedené předpony: 104</span><span class="sxs-lookup"><span data-stu-id="3c946-530">Converted prefix length: 104</span></span>

<span data-ttu-id="3c946-531">Adresu můžete také uvést v šestnáctkovém formátu ( `10.11.12.1` reprezentované protokolem IPv6 jako `::ffff:0a0b:0c01` ).</span><span class="sxs-lookup"><span data-stu-id="3c946-531">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="3c946-532">Při převodu adresy IPv4 na IPv6 přidejte 96 k délce předpony CIDR ( `8` v příkladu), aby se zohlednila další `::ffff:` předpona IPv6 (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="3c946-532">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="3c946-533">Předejte schéma pro reverzní proxy servery se systémy Linux a non-IIS.</span><span class="sxs-lookup"><span data-stu-id="3c946-533">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="3c946-534">Aplikace, které volají <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> a <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umísťují lokalitu do nekonečné smyčky, pokud jsou nasazené do azure Linux App Service, virtuální počítač Azure Linux (VM) nebo za jakýkoli jiný reverzní proxy server, kromě služby IIS.</span><span class="sxs-lookup"><span data-stu-id="3c946-534">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="3c946-535">Protokol TLS je ukončen reverzním proxy serverem a Kestrel není informován o správném schématu požadavků.</span><span class="sxs-lookup"><span data-stu-id="3c946-535">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="3c946-536">OAuth a OIDC v této konfiguraci selže, protože generují nesprávná přesměrování.</span><span class="sxs-lookup"><span data-stu-id="3c946-536">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="3c946-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Přidá a nakonfiguruje middleware předávaných hlaviček při spuštění za IIS, ale neexistuje žádná vyhovující Automatická konfigurace pro Linux (integrace Apache nebo Nginx).</span><span class="sxs-lookup"><span data-stu-id="3c946-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="3c946-538">Pro přeposílání schématu od proxy serveru ve scénářích mimo službu IIS přidejte a nakonfigurujte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="3c946-538">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="3c946-539">V `Startup.ConfigureServices` použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="3c946-539">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="3c946-540">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="3c946-540">Troubleshoot</span></span>

<span data-ttu-id="3c946-541">Když hlavičky nejsou předávány podle očekávání, povolte [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="3c946-541">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3c946-542">Pokud protokoly neposkytují dostatek informací pro řešení tohoto problému, vytvořte výčet hlaviček požadavků přijatých serverem.</span><span class="sxs-lookup"><span data-stu-id="3c946-542">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="3c946-543">K zápisu hlaviček požadavků do odpovědi aplikace nebo k protokolování hlaviček použijte vložený middlewarový middleware.</span><span class="sxs-lookup"><span data-stu-id="3c946-543">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="3c946-544">Chcete-li zapsat hlavičky do odpovědi aplikace, umístěte následující vložený middleware terminálu hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3c946-544">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="3c946-545">Do protokolů můžete zapisovat místo těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="3c946-545">You can write to logs instead of the response body.</span></span> <span data-ttu-id="3c946-546">Zápis do protokolů umožňuje, aby lokalita fungovala normálně během ladění.</span><span class="sxs-lookup"><span data-stu-id="3c946-546">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="3c946-547">Zápis protokolů místo textu odpovědi:</span><span class="sxs-lookup"><span data-stu-id="3c946-547">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="3c946-548">Vložení `ILogger<Startup>` do `Startup` třídy, jak je popsáno v tématu [vytvoření protokolů při spuštění](xref:fundamentals/logging/index#create-logs-in-startup).</span><span class="sxs-lookup"><span data-stu-id="3c946-548">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="3c946-549">Vložte následující vložený middleware hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="3c946-549">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="3c946-550">Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty přesunuty na `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="3c946-550">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="3c946-551">Pokud existuje více hodnot v dané hlavičce, middleware pro přesměrované hlavičky zpracovává záhlaví v opačném pořadí zprava doleva.</span><span class="sxs-lookup"><span data-stu-id="3c946-551">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="3c946-552">Výchozí hodnota `ForwardLimit` je `1` (jedna), takže je zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.</span><span class="sxs-lookup"><span data-stu-id="3c946-552">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="3c946-553">Původní Vzdálená IP adresa žádosti se musí shodovat s položkou v `KnownProxies` `KnownNetworks` seznamech nebo před zpracováním předávaných hlaviček.</span><span class="sxs-lookup"><span data-stu-id="3c946-553">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="3c946-554">To omezuje falšování hlaviček tím, že nepřijímá předávací servery od nedůvěryhodných proxy serverů.</span><span class="sxs-lookup"><span data-stu-id="3c946-554">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="3c946-555">Při zjištění neznámého proxy serveru se zobrazí zpráva s informacemi o adrese proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="3c946-555">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="3c946-556">V předchozím příkladu je 10.0.0.100 proxy server.</span><span class="sxs-lookup"><span data-stu-id="3c946-556">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="3c946-557">Pokud je server důvěryhodným proxy serverem, přidejte IP adresu serveru do `KnownProxies` (nebo přidejte důvěryhodnou síť do `KnownNetworks` ) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3c946-557">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3c946-558">Další informace najdete v části [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options) .</span><span class="sxs-lookup"><span data-stu-id="3c946-558">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="3c946-559">Povoluje pouze důvěryhodné proxy servery a sítě pro přeposílání hlaviček.</span><span class="sxs-lookup"><span data-stu-id="3c946-559">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="3c946-560">V opačném případě jsou možné útoky s [falešnou IP adresou](https://www.iplocation.net/ip-spoofing) .</span><span class="sxs-lookup"><span data-stu-id="3c946-560">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c946-561">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3c946-561">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="3c946-562">Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET Core zvýšení zabezpečení oprávnění</span><span class="sxs-lookup"><span data-stu-id="3c946-562">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

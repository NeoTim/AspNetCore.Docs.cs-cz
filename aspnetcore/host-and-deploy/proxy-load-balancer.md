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
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>Konfigurace ASP.NET Core pro práci s proxy servery a vyrovnáváním zatížení

Podle [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.0"

V doporučené konfiguraci pro ASP.NET Core je aplikace hostována pomocí core modulu IIS/ASP.NET Core Module, Nginx nebo Apache. Proxy servery, vykladače zatížení a další síťová zařízení často zakrývají informace o požadavku před tím, než se dostane do aplikace:

* Pokud jsou požadavky HTTPS proxied přes HTTP, původní schéma (HTTPS) je ztracena a musí být předány v záhlaví.
* Vzhledem k tomu, že aplikace obdrží požadavek z proxy serveru a není jeho skutečný zdroj v síti Internet nebo podnikové sítě, původní klient IP adresa musí být také předány v záhlaví.

Tyto informace mohou být důležité při zpracování požadavků, například při přesměrování, ověřování, generování propojení, vyhodnocení zásad a geografickém umístění klienta.

## <a name="forwarded-headers"></a>Předávaná záhlaví

Podle konvence proxy předávat informace v hlavičkách HTTP.

| Hlavička | Popis |
| ------ | ----------- |
| X-Forwarded-Pro | Obsahuje informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy. Tento parametr může obsahovat IP adresy (a volitelně čísla portů). V řetězci proxy serverů první parametr označuje klienta, kde byl požadavek poprvé proveden. Následují následné proxy identifikátory. Poslední proxy server v řetězci není v seznamu parametrů. Adresa IP posledního serveru proxy a volitelně číslo portu jsou k dispozici jako vzdálená adresa IP ve vrstvě přenosu. |
| X-Forwarded-Proto | Hodnota původního schématu (HTTP/HTTPS). Hodnota může být také seznam schémat, pokud požadavek prošel více proxy servery. |
| X-Forwarded-Host | Původní hodnota pole záhlaví Hostitele. Proxy servery obvykle nemění záhlaví Hostitele. Informace o chybě zabezpečení, která ovlivňuje systémy, kde proxy server neověřuje nebo neomezuje záhlaví hostitele na známé dobré hodnoty, naleznete v [tématu Microsoft Security Advisory CVE-2018-0787.](https://github.com/aspnet/Announcements/issues/295) |

Middleware S předaná záhlaví z balíčku [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a <xref:Microsoft.AspNetCore.Http.HttpContext>vyplní přidružená pole na webu .

Middleware aktualizace:

* [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set `X-Forwarded-For` pomocí hodnoty záhlaví. Další nastavení ovlivňují nastavení `RemoteIpAddress`middlewaru . Podrobnosti naleznete v možnostech [middlewaru předávaných záhlaví](#forwarded-headers-middleware-options).
* [HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set `X-Forwarded-Proto` pomocí hodnoty záhlaví.
* [HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set `X-Forwarded-Host` pomocí hodnoty hlavičky.

Lze nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru s předanou hlavičkou. Výchozí nastavení jsou:

* Existuje pouze *jeden proxy server* mezi aplikací a zdrojem požadavků.
* Pouze adresy zpětné smyčky jsou konfigurovány pro známé proxy servery a známé sítě.
* Předávaná záhlaví `X-Forwarded-For` `X-Forwarded-Proto`jsou pojmenována a .

Ne všechna síťová `X-Forwarded-For` `X-Forwarded-Proto` zařízení přidávají záhlaví a bez další konfigurace. Pokud proxied požadavky neobsahují tyto hlavičky, když se dostanou do aplikace, přečtěte si pokyny výrobce zařízení. Pokud zařízení používá jiné `X-Forwarded-For` názvy záhlaví než a `X-Forwarded-Proto`, nastavte možnosti <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby odpovídaly názvům záhlaví používaným zařízením. Další informace naleznete v tématech [Možnosti middlewaru předávaných záhlaví](#forwarded-headers-middleware-options) a [Konfigurace pro proxy server, který používá různé názvy záhlaví](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>IIS/IIS Express a základní modul ASP.NET

Middleware s předaná záhlaví je ve výchozím nastavení povolen [middlewarem integrace služby IIS,](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) pokud je aplikace hostována mimo proces za [službou](xref:host-and-deploy/iis/index#out-of-process-hosting-model) IIS a ASP.NET core modulem. Předaných záhlaví Middleware je aktivován pro spuštění jako první v middleware potrubí s omezenou konfiguraci specifickou pro ASP.NET Core Module z důvodu důvěry obavy s předanými záhlaví (například [IP spoofing](https://www.iplocation.net/ip-spoofing)). Middleware je nakonfigurován `X-Forwarded-For` tak, aby předal a `X-Forwarded-Proto` záhlaví a je omezen na jeden proxy localhost. Pokud je požadována další konfigurace, podívejte se na [možnosti middlewaru předávaných záhlaví](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Další scénáře proxy serveru a vyrovnávání zatížení

Mimo použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimoproces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), není middleware předaných záhlaví ve výchozím nastavení povoleno. Middleware s předanými záhlavími musí být povolen, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>aby aplikace zpracovala předávaná záhlaví pomocí aplikace . Po povolení middlewaru, pokud middlewar u <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nezadaných nejsou, jsou výchozí [položky ForwardedHeaders.ForwardedHeaders.None](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) . [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)

Nakonfigurujte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> middleware `X-Forwarded-For` `X-Forwarded-Proto` s pro `Startup.ConfigureServices`předávání a záhlaví v aplikaci . Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu `Startup.Configure` v před voláním jinémiddleware:

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
> Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` metodě <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>rozšíření nebo přímo k ní , výchozí hlavičky pro předávání jsou [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). Vlastnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> musí být nakonfigurována s hlavičkami dopředu.

## <a name="nginx-configuration"></a>Konfigurace Nginx

Chcete-li `X-Forwarded-For` `X-Forwarded-Proto` záhlaví a <xref:host-and-deploy/linux-nginx#configure-nginx>předat dál, přečtěte si informace o tom, zda neobsahuje hlavní a Další informace naleznete v [tématu NGINX: Použití hlavičky Přeposílané](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Konfigurace Apache

`X-Forwarded-For`(viz Apache [Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Informace o tom, `X-Forwarded-Proto` jak předat <xref:host-and-deploy/linux-apache#configure-apache>záhlaví dál, naleznete v tématu .

## <a name="forwarded-headers-middleware-options"></a>Možnosti middlewaru předávaných záhlaví

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>chování middlewaru Předávaných záhlaví. Následující příklad změní výchozí hodnoty:

* Omezte počet položek v předaných záhlavích na `2`.
* Přidejte známou `127.0.10.1`adresu proxy aplikace .
* Změňte předaný název `X-Forwarded-For` záhlaví `X-Forwarded-For-My-Custom-Header-Name`z výchozího na .

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Možnost | Popis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Omezí hostitele záhlavím `X-Forwarded-Host` na uvedené hodnoty.<ul><li>Hodnoty jsou porovnány pomocí ordinal-ignore-case.</li><li>Čísla portů musí být vyloučena.</li><li>Pokud je seznam prázdný, jsou povoleni všichni hostitelé.</li><li>Zástupný znak `*` nejvyšší úrovně umožňuje všem neprázdným hostitelům.</li><li>Zástupné znaky subdomény jsou povoleny, ale neodpovídají kořenové doméně. Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale `contoso.com`ne kořenové doméně .</li><li>Názvy hostitelů Unicode jsou [povoleny,](https://tools.ietf.org/html/rfc3492) ale jsou převedeny na Punycode pro porovnávání.</li><li>[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí obsahovat ohraničovací závorky a musí být v [konvenční podobě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). Adresy IPv6 nejsou zvláštní pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádná kanonizace.</li><li>Neomezení povolených hostitelů může útočníkovi umožnit falšovat odkazy generované službou.</li></ul>Výchozí hodnota je `IList<string>`prázdná . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [serverem ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Tato možnost se používá, když proxy/server `X-Forwarded-For` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.<br><br>Výchozí formát je `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Určuje, které předávání by mělo být zpracováno. Seznam polí, která platí, naleznete v [výčtu předávaných záhlaví.](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) Typické hodnoty přiřazené `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`k této vlastnosti jsou .<br><br>Výchozí hodnota je [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Tato možnost se používá, když proxy/server `X-Forwarded-Host` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.<br><br>Výchozí formát je `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Tato možnost se používá, když proxy/server `X-Forwarded-Proto` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.<br><br>Výchozí formát je `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Omezuje počet položek v hlavičkách, které jsou zpracovány. Nastavte `null` zakázat limit, ale to by `KnownProxies` mělo `KnownNetworks` být provedeno pouze v případě, nebo jsou nakonfigurovány. Nastavení nehodnoty`null` je preventivní opatření (ale nikoli záruka) k ochraně před nesprávně nakonfigurovanými proxy servery a škodlivými požadavky přicházejícími z bočních kanálů v síti.<br><br>Předávané hlavičky Middleware zpracovává hlavičky v opačném pořadí zprava doleva. Pokud je použita výchozí hodnota (`1`), je zpracována pouze hodnota zcela `ForwardLimit` vpravo ze záhlaví, pokud není zvýšena hodnota.<br><br>Výchozí formát je `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Rozsahy adres známých sítí, ze kterých mají být přijímána předávaná záhlaví. Poskytněte rozsahy IP adres pomocí zápisu classless interdomain routing (CIDR).<br><br>Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1`(například v ipv4 reprezentovaném v ipv6 jako ). Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Další informace naleznete v [části Konfigurace adresy IPv4 reprezentované jako adresa Protokolu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)<br><br>Výchozí hodnota `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> je> obsahující jednu položku pro aplikaci `IPAddress.Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy známých proxy serverů, ze kterých mají být přijímána předávaná záhlaví. Slouží `KnownProxies` k určení přesných shod IP adres.<br><br>Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1`(například v ipv4 reprezentovaném v ipv6 jako ). Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Další informace naleznete v [části Konfigurace adresy IPv4 reprezentované jako adresa Protokolu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)<br><br>Výchozí hodnota `IList` \< <xref:System.Net.IPAddress> je> obsahující jednu položku pro aplikaci `IPAddress.IPv6Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Výchozí formát je `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Výchozí formát je `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Výchozí formát je `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Vyžadovat, aby byl počet hodnot záhlaví synchronizován mezi zpracovávané hlavičky [Předvoj.](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)<br><br>Výchozí hodnota v ASP.NET Core `true`1.x je . Výchozí hodnota v ASP.NET jádrem `false`2.0 nebo novějším je . |

## <a name="scenarios-and-use-cases"></a>Scénáře a případy použití

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Pokud není možné přidat předané hlavičky a všechny požadavky jsou zabezpečené

V některých případech nemusí být možné přidat předávané záhlaví k požadavkům proxied do aplikace. Pokud proxy server vynucuje, že všechny veřejné externí požadavky `Startup.Configure` jsou HTTPS, schéma lze ručně nastavit před použitím libovolného typu middleware:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Tento kód lze zakázat s proměnnou prostředí nebo jiné nastavení konfigurace ve vývojovém nebo pracovním prostředí.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Řešení základny cesty a proxy, které mění cestu požadavku

Některé proxy servery předat cestu beze změny, ale se základní cestu aplikace, která by měla být odebrána tak, aby směrování funguje správně. [Middleware UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) rozdělí cestu na [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace na [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Pokud `/foo` je základní cesta aplikace pro `/foo/api/1`cestu proxy předávané jako , middleware `Request.PathBase` nastaví `/foo` a `Request.Path` `/api/1` s následujícím příkazem:

```csharp
app.UsePathBase("/foo");
```

Původní cesta a základna cesty jsou znovu použity, když middleware je volána znovu v opačném směru. Další informace o zpracování objednávek <xref:fundamentals/middleware/index>middlewaru naleznete v tématu .

Pokud proxy server ořízne cestu `/foo/api/1` (například předávání na `/api/1`), opravte přesměrování a odkazy nastavením [vlastnosti PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) požadavku:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Pokud proxy server přidává data cesty, zahoďte část cesty a <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> opravte přesměrování <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> a odkazy pomocí vlastnosti a přiřazováním k ní:

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Konfigurace pro proxy server, který používá různé názvy záhlaví

Pokud proxy server nepoužívá záhlaví `X-Forwarded-For` s `X-Forwarded-Proto` názvem a předává informace o adrese/portu proxy a původním schématu, nastavte možnosti <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby odpovídaly názvům záhlaví používaným proxy serverem:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Konfigurace adresy IPv4 reprezentované jako adresa IPv6

Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1` (například v ipv4 reprezentovaném v ipv6 jako nebo `::ffff:a00:1`). Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).

V následujícím příkladu je do `KnownNetworks` seznamu ve formátu IPv6 přidána síťová adresa, která dodává předávaná záhlaví.

Adresa IPv4:`10.11.12.1/8`

Převedená adresa IPv6:`::ffff:10.11.12.1`  
Převedená délka předpony: 104

Adresu můžete zadat také v šestnáctkovém formátu`10.11.12.1` (reprezentované `::ffff:0a0b:0c01`v iPv6 jako). Při převodu adresy IPv4 na IPv6 přidejte 96 do`8` délky předpony CIDR `::ffff:` (v příkladu) tak, aby se zohlednila další předpona IPv6 (8 + 96 = 104). 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Předání systému pro reverzní proxy servery Linux a jiné systémy iis

Aplikace, <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> které <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> volají a umisťují web do nekonečné smyčky, pokud jsou nasazeny do služby Azure Linux App Service, virtuálního počítače Azure Linux (VM) nebo za jakýmkoli jiným reverzním proxy serverem kromě Služby IIS. TLS je ukončena reverzní proxy server a Kestrel není informován o správné schéma požadavků. OAuth a OIDC také nezdaří v této konfiguraci, protože generují nesprávné přesměrování. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>přidává a konfiguruje přeposílané záhlaví Middleware při spuštění za IIS, ale neexistuje žádná odpovídající automatická konfigurace pro Linux (Apache nebo Nginx integrace).

Chcete-li předávat schéma z proxy serveru ve scénářích mimo službu IIS, přidejte a nakonfigurujte middleware předávaných záhlaví. V `Startup.ConfigureServices`, použijte následující kód:

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

## <a name="certificate-forwarding"></a>Předávání certifikátů 

### <a name="azure"></a>Azure

Pokud chcete nakonfigurovat službu Azure App Service pro předávání certifikátů, přečtěte si informace [o konfiguraci vzájemného ověřování TLS pro službu Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth). Následující pokyny se týkající se konfigurace aplikace ASP.NET Core.

V `Startup.Configure`oblasti přidejte před volání `app.UseAuthentication();`mj.

```csharp
app.UseCertificateForwarding();
```


Nakonfigurujte Middleware pro předávání certifikátů a zadejte název záhlaví, který Azure používá. V `Startup.ConfigureServices`aplikaci přidejte následující kód pro konfiguraci záhlaví, ze kterého middleware vytváří certifikát:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a>Další webové proxy servery

Pokud se používá proxy server, který není Službou IIS nebo směrováním žádostí o žádosti o aplikační aplikace služby Azure App Service (ARR), nakonfigurujte proxy server tak, aby předal certifikát, který obdržel v hlavičce HTTP. V `Startup.Configure`oblasti přidejte před volání `app.UseAuthentication();`mj.

```csharp
app.UseCertificateForwarding();
```

Nakonfigurujte middleware pro předávání certifikátů tak, aby zadával název záhlaví. V `Startup.ConfigureServices`aplikaci přidejte následující kód pro konfiguraci záhlaví, ze kterého middleware vytváří certifikát:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

Pokud proxy není base64-kódování certifikátu (jako je tomu v případě `HeaderConverter` Nginx), nastavte možnost. Vezměme si `Startup.ConfigureServices`následující příklad v :

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

## <a name="troubleshoot"></a>Řešení potíží

Pokud záhlaví nejsou předávána podle očekávání, povolte [protokolování](xref:fundamentals/logging/index). Pokud protokoly neposkytují dostatečné informace k řešení problému, výčet záhlaví požadavku přijaté serverem. Pomocí vřádkového middlewaru zapíšete záhlaví požadavků do odpovědi na aplikaci nebo zaznamenejte záhlaví. 

Chcete-li napište záhlaví do odpovědi aplikace, umístěte následující inline middleware <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`terminálu ihned po volání do aplikace :

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

Můžete zapisovat do protokolů namísto těla odpovědi. Zápis do protokolů umožňuje webu fungovat normálně při ladění.

Chcete-li zapsat protokoly spíše než do těla odpovědi:

* Vložte `ILogger<Startup>` `Startup` do třídy, jak je popsáno v [vytvořit protokoly v startupu](xref:fundamentals/logging/index#create-logs-in-startup).
* Ihned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> souboru na `Startup.Configure`místo umístěte následující vřaditý middleware.

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

Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty `X-Original-{For|Proto|Host}`přesunuty do . Pokud je v dané hlavičce více hodnot, middleware s předaným záhlavím zpracuje záhlaví v opačném pořadí zprava doleva. Výchozí `ForwardLimit` hodnota `1` je (jedna), takže je zpracována pouze hodnota zcela `ForwardLimit` vpravo ze záhlaví, pokud není zvýšena hodnota.

Původní vzdálená ip adresa požadavku musí `KnownProxies` odpovídat položce v seznamu nebo `KnownNetworks` před zpracováním předaných záhlaví. To omezuje falšování záhlaví tím, že nepřijímá servery pro předávání z nedůvěryhodných serverů proxy. Pokud je zjištěn neznámý proxy server, protokolování označuje adresu proxy serveru:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

V předchozím příkladu je 10.0.0.100 proxy server. Pokud je server důvěryhodným proxy serverem, přidejte adresu IP serveru `KnownNetworks`do `Startup.ConfigureServices` `KnownProxies` aplikace . Další informace naleznete v části [Možnosti middlewaru s předávaných záhlaví.](#forwarded-headers-middleware-options)

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Povolit pouze důvěryhodné proxy servery a sítě předávat záhlaví. V opačném případě jsou možné útoky [spoofing IP.](https://www.iplocation.net/ip-spoofing)

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/web-farm>
* [Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET základní ohrožení zabezpečení oprávnění](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V doporučené konfiguraci pro ASP.NET Core je aplikace hostována pomocí core modulu IIS/ASP.NET Core Module, Nginx nebo Apache. Proxy servery, vykladače zatížení a další síťová zařízení často zakrývají informace o požadavku před tím, než se dostane do aplikace:

* Pokud jsou požadavky HTTPS proxied přes HTTP, původní schéma (HTTPS) je ztracena a musí být předány v záhlaví.
* Vzhledem k tomu, že aplikace obdrží požadavek z proxy serveru a není jeho skutečný zdroj v síti Internet nebo podnikové sítě, původní klient IP adresa musí být také předány v záhlaví.

Tyto informace mohou být důležité při zpracování požadavků, například při přesměrování, ověřování, generování propojení, vyhodnocení zásad a geografickém umístění klienta.

## <a name="forwarded-headers"></a>Předávaná záhlaví

Podle konvence proxy předávat informace v hlavičkách HTTP.

| Hlavička | Popis |
| ------ | ----------- |
| X-Forwarded-Pro | Obsahuje informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy. Tento parametr může obsahovat IP adresy (a volitelně čísla portů). V řetězci proxy serverů první parametr označuje klienta, kde byl požadavek poprvé proveden. Následují následné proxy identifikátory. Poslední proxy server v řetězci není v seznamu parametrů. Adresa IP posledního serveru proxy a volitelně číslo portu jsou k dispozici jako vzdálená adresa IP ve vrstvě přenosu. |
| X-Forwarded-Proto | Hodnota původního schématu (HTTP/HTTPS). Hodnota může být také seznam schémat, pokud požadavek prošel více proxy servery. |
| X-Forwarded-Host | Původní hodnota pole záhlaví Hostitele. Proxy servery obvykle nemění záhlaví Hostitele. Informace o chybě zabezpečení, která ovlivňuje systémy, kde proxy server neověřuje nebo neomezuje záhlaví hostitele na známé dobré hodnoty, naleznete v [tématu Microsoft Security Advisory CVE-2018-0787.](https://github.com/aspnet/Announcements/issues/295) |

Middleware S předaná záhlaví z balíčku [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a <xref:Microsoft.AspNetCore.Http.HttpContext>vyplní přidružená pole na webu .

Middleware aktualizace:

* [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set `X-Forwarded-For` pomocí hodnoty záhlaví. Další nastavení ovlivňují nastavení `RemoteIpAddress`middlewaru . Podrobnosti naleznete v možnostech [middlewaru předávaných záhlaví](#forwarded-headers-middleware-options).
* [HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set `X-Forwarded-Proto` pomocí hodnoty záhlaví.
* [HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set `X-Forwarded-Host` pomocí hodnoty hlavičky.

Lze nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru s předanou hlavičkou. Výchozí nastavení jsou:

* Existuje pouze *jeden proxy server* mezi aplikací a zdrojem požadavků.
* Pouze adresy zpětné smyčky jsou konfigurovány pro známé proxy servery a známé sítě.
* Předávaná záhlaví `X-Forwarded-For` `X-Forwarded-Proto`jsou pojmenována a .

Ne všechna síťová `X-Forwarded-For` `X-Forwarded-Proto` zařízení přidávají záhlaví a bez další konfigurace. Pokud proxied požadavky neobsahují tyto hlavičky, když se dostanou do aplikace, přečtěte si pokyny výrobce zařízení. Pokud zařízení používá jiné `X-Forwarded-For` názvy záhlaví než a `X-Forwarded-Proto`, nastavte možnosti <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby odpovídaly názvům záhlaví používaným zařízením. Další informace naleznete v tématech [Možnosti middlewaru předávaných záhlaví](#forwarded-headers-middleware-options) a [Konfigurace pro proxy server, který používá různé názvy záhlaví](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>IIS/IIS Express a základní modul ASP.NET

Middleware s předaná záhlaví je ve výchozím nastavení povolen [middlewarem integrace služby IIS,](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) pokud je aplikace hostována mimo proces za [službou](xref:host-and-deploy/iis/index#out-of-process-hosting-model) IIS a ASP.NET core modulem. Předaných záhlaví Middleware je aktivován pro spuštění jako první v middleware potrubí s omezenou konfiguraci specifickou pro ASP.NET Core Module z důvodu důvěry obavy s předanými záhlaví (například [IP spoofing](https://www.iplocation.net/ip-spoofing)). Middleware je nakonfigurován `X-Forwarded-For` tak, aby předal a `X-Forwarded-Proto` záhlaví a je omezen na jeden proxy localhost. Pokud je požadována další konfigurace, podívejte se na [možnosti middlewaru předávaných záhlaví](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Další scénáře proxy serveru a vyrovnávání zatížení

Mimo použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimoproces](xref:host-and-deploy/iis/index#out-of-process-hosting-model), není middleware předaných záhlaví ve výchozím nastavení povoleno. Middleware s předanými záhlavími musí být povolen, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>aby aplikace zpracovala předávaná záhlaví pomocí aplikace . Po povolení middlewaru, pokud middlewar u <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nezadaných nejsou, jsou výchozí [položky ForwardedHeaders.ForwardedHeaders.None](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) . [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)

Nakonfigurujte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> middleware `X-Forwarded-For` `X-Forwarded-Proto` s pro `Startup.ConfigureServices`předávání a záhlaví v aplikaci . Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu `Startup.Configure` v před voláním jinémiddleware:

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
> Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` metodě <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>rozšíření nebo přímo k ní , výchozí hlavičky pro předávání jsou [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). Vlastnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> musí být nakonfigurována s hlavičkami dopředu.

## <a name="nginx-configuration"></a>Konfigurace Nginx

Chcete-li `X-Forwarded-For` `X-Forwarded-Proto` záhlaví a <xref:host-and-deploy/linux-nginx#configure-nginx>předat dál, přečtěte si informace o tom, zda neobsahuje hlavní a Další informace naleznete v [tématu NGINX: Použití hlavičky Přeposílané](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Konfigurace Apache

`X-Forwarded-For`(viz Apache [Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Informace o tom, `X-Forwarded-Proto` jak předat <xref:host-and-deploy/linux-apache#configure-apache>záhlaví dál, naleznete v tématu .

## <a name="forwarded-headers-middleware-options"></a>Možnosti middlewaru předávaných záhlaví

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>chování middlewaru Předávaných záhlaví. Následující příklad změní výchozí hodnoty:

* Omezte počet položek v předaných záhlavích na `2`.
* Přidejte známou `127.0.10.1`adresu proxy aplikace .
* Změňte předaný název `X-Forwarded-For` záhlaví `X-Forwarded-For-My-Custom-Header-Name`z výchozího na .

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Možnost | Popis |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Omezí hostitele záhlavím `X-Forwarded-Host` na uvedené hodnoty.<ul><li>Hodnoty jsou porovnány pomocí ordinal-ignore-case.</li><li>Čísla portů musí být vyloučena.</li><li>Pokud je seznam prázdný, jsou povoleni všichni hostitelé.</li><li>Zástupný znak `*` nejvyšší úrovně umožňuje všem neprázdným hostitelům.</li><li>Zástupné znaky subdomény jsou povoleny, ale neodpovídají kořenové doméně. Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale `contoso.com`ne kořenové doméně .</li><li>Názvy hostitelů Unicode jsou [povoleny,](https://tools.ietf.org/html/rfc3492) ale jsou převedeny na Punycode pro porovnávání.</li><li>[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí obsahovat ohraničovací závorky a musí být v [konvenční podobě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). Adresy IPv6 nejsou zvláštní pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádná kanonizace.</li><li>Neomezení povolených hostitelů může útočníkovi umožnit falšovat odkazy generované službou.</li></ul>Výchozí hodnota je `IList<string>`prázdná . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [serverem ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Tato možnost se používá, když proxy/server `X-Forwarded-For` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.<br><br>Výchozí formát je `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Určuje, které předávání by mělo být zpracováno. Seznam polí, která platí, naleznete v [výčtu předávaných záhlaví.](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) Typické hodnoty přiřazené `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`k této vlastnosti jsou .<br><br>Výchozí hodnota je [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Tato možnost se používá, když proxy/server `X-Forwarded-Host` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.<br><br>Výchozí formát je `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Tato možnost se používá, když proxy/server `X-Forwarded-Proto` pro předávání nepoužívá záhlaví, ale používá některé jiné záhlaví k předání informací.<br><br>Výchozí formát je `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Omezuje počet položek v hlavičkách, které jsou zpracovány. Nastavte `null` zakázat limit, ale to by `KnownProxies` mělo `KnownNetworks` být provedeno pouze v případě, nebo jsou nakonfigurovány. Nastavení nehodnoty`null` je preventivní opatření (ale nikoli záruka) k ochraně před nesprávně nakonfigurovanými proxy servery a škodlivými požadavky přicházejícími z bočních kanálů v síti.<br><br>Předávané hlavičky Middleware zpracovává hlavičky v opačném pořadí zprava doleva. Pokud je použita výchozí hodnota (`1`), je zpracována pouze hodnota zcela `ForwardLimit` vpravo ze záhlaví, pokud není zvýšena hodnota.<br><br>Výchozí formát je `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Rozsahy adres známých sítí, ze kterých mají být přijímána předávaná záhlaví. Poskytněte rozsahy IP adres pomocí zápisu classless interdomain routing (CIDR).<br><br>Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1`(například v ipv4 reprezentovaném v ipv6 jako ). Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Další informace naleznete v [části Konfigurace adresy IPv4 reprezentované jako adresa Protokolu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)<br><br>Výchozí hodnota `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> je> obsahující jednu položku pro aplikaci `IPAddress.Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy známých proxy serverů, ze kterých mají být přijímána předávaná záhlaví. Slouží `KnownProxies` k určení přesných shod IP adres.<br><br>Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1`(například v ipv4 reprezentovaném v ipv6 jako ). Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Další informace naleznete v [části Konfigurace adresy IPv4 reprezentované jako adresa Protokolu IPv6.](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address)<br><br>Výchozí hodnota `IList` \< <xref:System.Net.IPAddress> je> obsahující jednu položku pro aplikaci `IPAddress.IPv6Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Výchozí formát je `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Výchozí formát je `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Použijte záhlaví určené touto vlastností namísto záhlaví určeného [parametrem ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Výchozí formát je `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Vyžadovat, aby byl počet hodnot záhlaví synchronizován mezi zpracovávané hlavičky [Předvoj.](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders)<br><br>Výchozí hodnota v ASP.NET Core `true`1.x je . Výchozí hodnota v ASP.NET jádrem `false`2.0 nebo novějším je . |

## <a name="scenarios-and-use-cases"></a>Scénáře a případy použití

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Pokud není možné přidat předané hlavičky a všechny požadavky jsou zabezpečené

V některých případech nemusí být možné přidat předávané záhlaví k požadavkům proxied do aplikace. Pokud proxy server vynucuje, že všechny veřejné externí požadavky `Startup.Configure` jsou HTTPS, schéma lze ručně nastavit před použitím libovolného typu middleware:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Tento kód lze zakázat s proměnnou prostředí nebo jiné nastavení konfigurace ve vývojovém nebo pracovním prostředí.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Řešení základny cesty a proxy, které mění cestu požadavku

Některé proxy servery předat cestu beze změny, ale se základní cestu aplikace, která by měla být odebrána tak, aby směrování funguje správně. [Middleware UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) rozdělí cestu na [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace na [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Pokud `/foo` je základní cesta aplikace pro `/foo/api/1`cestu proxy předávané jako , middleware `Request.PathBase` nastaví `/foo` a `Request.Path` `/api/1` s následujícím příkazem:

```csharp
app.UsePathBase("/foo");
```

Původní cesta a základna cesty jsou znovu použity, když middleware je volána znovu v opačném směru. Další informace o zpracování objednávek <xref:fundamentals/middleware/index>middlewaru naleznete v tématu .

Pokud proxy server ořízne cestu `/foo/api/1` (například předávání na `/api/1`), opravte přesměrování a odkazy nastavením [vlastnosti PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) požadavku:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Pokud proxy server přidává data cesty, zahoďte část cesty a <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> opravte přesměrování <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> a odkazy pomocí vlastnosti a přiřazováním k ní:

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Konfigurace pro proxy server, který používá různé názvy záhlaví

Pokud proxy server nepoužívá záhlaví `X-Forwarded-For` s `X-Forwarded-Proto` názvem a předává informace o adrese/portu proxy a původním schématu, nastavte možnosti <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby odpovídaly názvům záhlaví používaným proxy serverem:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Konfigurace adresy IPv4 reprezentované jako adresa IPv6

Pokud server používá sokety s duálním režimem, jsou adresy IPv4 `10.0.0.1` dodávány ve formátu IPv6 `::ffff:10.0.0.1` (například v ipv4 reprezentovaném v ipv6 jako nebo `::ffff:a00:1`). Viz [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Zjistěte, zda je tento formát vyžadován na adrese [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).

V následujícím příkladu je do `KnownNetworks` seznamu ve formátu IPv6 přidána síťová adresa, která dodává předávaná záhlaví.

Adresa IPv4:`10.11.12.1/8`

Převedená adresa IPv6:`::ffff:10.11.12.1`  
Převedená délka předpony: 104

Adresu můžete zadat také v šestnáctkovém formátu`10.11.12.1` (reprezentované `::ffff:0a0b:0c01`v iPv6 jako). Při převodu adresy IPv4 na IPv6 přidejte 96 do`8` délky předpony CIDR `::ffff:` (v příkladu) tak, aby se zohlednila další předpona IPv6 (8 + 96 = 104). 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Předání systému pro reverzní proxy servery Linux a jiné systémy iis

Aplikace, <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> které <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> volají a umisťují web do nekonečné smyčky, pokud jsou nasazeny do služby Azure Linux App Service, virtuálního počítače Azure Linux (VM) nebo za jakýmkoli jiným reverzním proxy serverem kromě Služby IIS. TLS je ukončena reverzní proxy server a Kestrel není informován o správné schéma požadavků. OAuth a OIDC také nezdaří v této konfiguraci, protože generují nesprávné přesměrování. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>přidává a konfiguruje přeposílané záhlaví Middleware při spuštění za IIS, ale neexistuje žádná odpovídající automatická konfigurace pro Linux (Apache nebo Nginx integrace).

Chcete-li předávat schéma z proxy serveru ve scénářích mimo službu IIS, přidejte a nakonfigurujte middleware předávaných záhlaví. V `Startup.ConfigureServices`, použijte následující kód:

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

## <a name="troubleshoot"></a>Řešení potíží

Pokud záhlaví nejsou předávána podle očekávání, povolte [protokolování](xref:fundamentals/logging/index). Pokud protokoly neposkytují dostatečné informace k řešení problému, výčet záhlaví požadavku přijaté serverem. Pomocí vřádkového middlewaru zapíšete záhlaví požadavků do odpovědi na aplikaci nebo zaznamenejte záhlaví. 

Chcete-li napište záhlaví do odpovědi aplikace, umístěte následující inline middleware <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`terminálu ihned po volání do aplikace :

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

Můžete zapisovat do protokolů namísto těla odpovědi. Zápis do protokolů umožňuje webu fungovat normálně při ladění.

Chcete-li zapsat protokoly spíše než do těla odpovědi:

* Vložte `ILogger<Startup>` `Startup` do třídy, jak je popsáno v [vytvořit protokoly v startupu](xref:fundamentals/logging/index#create-logs-in-startup).
* Ihned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> souboru na `Startup.Configure`místo umístěte následující vřaditý middleware.

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

Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty `X-Original-{For|Proto|Host}`přesunuty do . Pokud je v dané hlavičce více hodnot, middleware s předaným záhlavím zpracuje záhlaví v opačném pořadí zprava doleva. Výchozí `ForwardLimit` hodnota `1` je (jedna), takže je zpracována pouze hodnota zcela `ForwardLimit` vpravo ze záhlaví, pokud není zvýšena hodnota.

Původní vzdálená ip adresa požadavku musí `KnownProxies` odpovídat položce v seznamu nebo `KnownNetworks` před zpracováním předaných záhlaví. To omezuje falšování záhlaví tím, že nepřijímá servery pro předávání z nedůvěryhodných serverů proxy. Pokud je zjištěn neznámý proxy server, protokolování označuje adresu proxy serveru:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

V předchozím příkladu je 10.0.0.100 proxy server. Pokud je server důvěryhodným proxy serverem, přidejte adresu IP serveru `KnownNetworks`do `Startup.ConfigureServices` `KnownProxies` aplikace . Další informace naleznete v části [Možnosti middlewaru s předávaných záhlaví.](#forwarded-headers-middleware-options)

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Povolit pouze důvěryhodné proxy servery a sítě předávat záhlaví. V opačném případě jsou možné útoky [spoofing IP.](https://www.iplocation.net/ip-spoofing)

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/web-farm>
* [Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET základní ohrožení zabezpečení oprávnění](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

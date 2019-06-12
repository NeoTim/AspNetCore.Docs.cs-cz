---
title: Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení
author: guardrex
description: Další informace o konfiguraci aplikací hostovaných za službou proxy servery a nástroje pro vyrovnávání zatížení, které často skryl důležité vyžádat informace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: bcafc33b8faf81912d536d3df8941d196685ecad
ms.sourcegitcommit: 1bb3f3f1905b4e7d4ca1b314f2ce6ee5dd8be75f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2019
ms.locfileid: "66837368"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení

Podle [Luke Latham](https://github.com/guardrex) a [Chris Ross](https://github.com/Tratcher)

Doporučenou konfiguraci pro ASP.NET Core je aplikace hostovaná pomocí modulu jádra IIS/ASP.NET, Nginx nebo Apache. Proxy servery, nástroje pro vyrovnávání zatížení a jiných síťových zařízení často skryl informace o požadavku předtím, než dosáhne aplikace:

* Pokud požadavky HTTPS jsou směrovány přes proxy server pomocí protokolu HTTP, původní schéma (HTTPS) ztratí a musí předávat v hlavičce.
* Protože aplikace obdrží žádost z proxy serveru a není true zdroj na Internetu nebo podnikové sítě, musí v hlavičce předané zdrojovou IP adresu klienta.

Tyto informace mohou být důležité pro zpracování žádostí, například v přesměrování ověřování, generování odkazů, hodnocení zásad a informace o zeměpisné poloze klienta.

## <a name="forwarded-headers"></a>Přesměrovaná záhlaví

Podle konvence proxy předávat informace v hlavičkách protokolu HTTP.

| Záhlaví | Popis |
| ------ | ----------- |
| X-Forwarded-For | Obsahuje informace o klientovi, který inicioval žádost a následné proxy serverů v řetězci proxy servery. Tento parametr může obsahovat IP adresy (a volitelně čísla portů). První parametr v řetězci proxy servery, označuje klienta, kde byl první požadavek. Postupujte podle dalších proxy identifikátory. Poslední proxy server v řetězu není v seznamu parametrů. Poslední proxy IP adresu a volitelně čísla portu, jsou k dispozici jako Vzdálená IP adresa na transportní vrstvě. |
| X-Forwarded-Proto | Hodnota původní schématu (HTTP/HTTPS). Hodnota může být také seznam schémata, pokud požadavek má procházet více proxy serverů. |
| X-Forwarded-Host | Původní hodnota pole hlavičky hostitele. Obvykle proxy servery neupravujte hlavičku hostitele. Zobrazit [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) informace o zvýšení oprávnění ohrožení zabezpečení, který má vliv na systémy, ve kterém není proxy server ověřit nebo omezit na známé dobré hodnoty hlavičky hostitele. |

Middleware záhlaví předané z [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) balení, načte tyto hlavičky a vyplní přidružených polí na <xref:Microsoft.AspNetCore.Http.HttpContext>.

Middleware aktualizace:

* [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; sada s použitím `X-Forwarded-For` hodnota hlavičky. Další nastavení ovlivní, jak middleware nastaví `RemoteIpAddress`. Podrobnosti najdete v tématu [možnosti předané middlewaru záhlaví](#forwarded-headers-middleware-options).
* [HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; sada s použitím `X-Forwarded-Proto` hodnota hlavičky.
* [HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; sada s použitím `X-Forwarded-Host` hodnota hlavičky.

Předané záhlaví Middleware [výchozí nastavení](#forwarded-headers-middleware-options) lze nakonfigurovat. Výchozí nastavení je:

* Existuje pouze *jeden proxy server* mezi aplikací a původu žádosti.
* Pouze adresy zpětné smyčky jsou nakonfigurované pro známé servery proxy a známé sítě.
* Přesměrovaná hlavičky jsou pojmenovány `X-Forwarded-For` a `X-Forwarded-Proto`.

Ne všechna síťová zařízení přidat `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví bez další konfigurace. Pokud požadavky směrovány přes proxy server, tyto hlavičky neobsahují, když dosáhnou aplikace, najdete pokyny výrobce vašeho zařízení. Pokud zařízení používá odlišnou hlavičku názvy než `X-Forwarded-For` a `X-Forwarded-Proto`, nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> možnosti tak, aby odpovídaly názvy záhlaví používané zařízení. Další informace najdete v tématu [možnosti předané middlewaru záhlaví](#forwarded-headers-middleware-options) a [konfiguraci proxy serveru, který používá odlišnou hlavičku názvy](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>Služby IIS/IIS Express a modul ASP.NET Core

Ve výchozím nastavení je povoleno Middleware předaný záhlaví [Middleware pro integraci služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) když je aplikace hostovaná [mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model) za služby IIS a že modul ASP.NET Core. Middleware předaný záhlaví se aktivuje při spuštění první middleware kanál s omezenou konfiguraci konkrétní modul ASP.NET Core z důvodu otázky důvěryhodnosti předané hlavičky (například [falšování adresy IP](https://www.iplocation.net/ip-spoofing)). Middleware je nakonfigurovaný pro předávání `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví a je omezená na jednu localhost proxy. Pokud je vyžadována další konfigurace, najdete v článku [možnosti předané middlewaru záhlaví](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Další proxy serveru a scénáře pro nástroj pro vyrovnávání zatížení

Kromě použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování za nástrojem [mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model), předané Middleware záhlaví není povolená ve výchozím nastavení. Přesměrovaná Middleware záhlaví musí být povolené pro aplikace pro proces, předá záhlaví s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>. Po povolení middleware, pokud žádné <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jsou určené pro middleware, výchozí [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Nakonfigurujte middleware s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> předávat `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví v `Startup.ConfigureServices`. Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metoda `Startup.Configure` před voláním dalším middlewarem:

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
> Pokud ne <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> jsou určené v `Startup.ConfigureServices` nebo přímo na metodu rozšíření pomocí <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, jsou výchozí hlavičky pro předávání [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> Vlastnosti se musí nakonfigurovat hlavičky pro předávání.

## <a name="nginx-configuration"></a>V konfiguraci serveru Nginx

Předat dál `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví, najdete v článku <xref:host-and-deploy/linux-nginx#configure-nginx>. Další informace najdete v tématu [NGINX: Pomocí hlavičky předané](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Konfigurace serveru Apache

`X-Forwarded-For` je automaticky přidán (viz [mod_proxy Apache modul: Reverzní Proxy hlavičky žádosti](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Informace o tom, jak přesměrovat `X-Forwarded-Proto` záhlaví, najdete v článku <xref:host-and-deploy/linux-apache#configure-apache>.

## <a name="forwarded-headers-middleware-options"></a>Předané možnosti middlewaru záhlaví

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> řídí chování middlewaru předané záhlaví. Následující příklad změní výchozí hodnoty:

* Omezit počet položek v záhlaví předané do `2`.
* Přidat známé proxy server s adresou `127.0.10.1`.
* Změnit název hlavičky předané z výchozích `X-Forwarded-For` k `X-Forwarded-For-My-Custom-Header-Name`.

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
| AllowedHosts | Hostitelé podle omezuje `X-Forwarded-Host` záhlaví zadanými hodnotami.<ul><li>Hodnoty jsou porovnány pomocí pořadí ignorování případu.</li><li>Čísla portů musí být vyloučeny.</li><li>Pokud je seznam prázdný, jsou povoleny všechny hostitele.</li><li>Nejvyšší úrovně zástupný znak `*` umožňuje všichni hostitelé prázdný.</li><li>Subdoména zástupné znaky jsou povolené, ale kořenové domény se neshodují. Například `*.contoso.com` odpovídá subdoménu `foo.contoso.com` , ale není kořenovou doménu `contoso.com`.</li><li>Názvy hostitelů Unicode jsou povolené, ale jsou převedeny na [kódování Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</li><li>[IPv6 adresy](https://tools.ietf.org/html/rfc4291) musí zahrnovat hranaté závorky ohraničující a je v [konvenční formuláře](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). IPv6 adresy nejsou speciální malými a velkými písmeny ke kontrole logické rovnost mezi různými formáty a je provedena bez převodu do kanonického tvaru.</li><li>Selhání omezit povolené hostitele může útočníkovi umožnit zfalšovat odkazy vygenerované službou.</li></ul>Výchozí hodnota je prázdná `IList<string>`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-For` záhlaví, ale používá některé hlavičky k předávání informací.<br><br>Výchozí hodnota je `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Určuje, které servery pro předávání by se měly zpracovat. Zobrazit [ForwardedHeaders výčtu](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) seznam polí, které se vztahují. Typické hodnoty přiřazené k této vlastnosti jsou "ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.<br><br>Výchozí hodnota je [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-Host` záhlaví, ale používá některé hlavičky k předávání informací.<br><br>Výchozí hodnota je `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-Proto` záhlaví, ale používá některé hlavičky k předávání informací.<br><br>Výchozí hodnota je `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Omezí počet položek v záhlaví, které se zpracovávají. Nastavte na `null` zakázat limit, ale to lze provádět pouze pokud `KnownProxies` nebo `KnownNetworks` jsou nakonfigurované.<br><br>Výchozí hodnota je 1. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Rozsahy známých sítí tak, aby přijímal předané záhlaví z adres. Zadejte rozsahy IP adres pomocí notace CIDR (Classless Interdomain Routing) zápisu.<br><br>Pokud server používá duální sokety, jsou ve formátu protokolu IPv6 zadané adresy IPv4 (například `10.0.0.1` IPv4 v IPv6 jako `::ffff:10.0.0.1`). See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Určit, pokud tento formát je požadováno pohledem [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Další informace najdete v tématu [konfigurace pro adresu IPv4 přístupnou reprezentovaná jako adresu IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) oddílu.<br><br>Výchozí hodnota je `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> obsahující jednu položku pro `IPAddress.Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy z předané hlavičky accept známých proxy služby. Použití `KnownProxies` určit přesné adresy IP odpovídá.<br><br>Pokud server používá duální sokety, jsou ve formátu protokolu IPv6 zadané adresy IPv4 (například `10.0.0.1` IPv4 v IPv6 jako `::ffff:10.0.0.1`). See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Určit, pokud tento formát je požadováno pohledem [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Další informace najdete v tématu [konfigurace pro adresu IPv4 přístupnou reprezentovaná jako adresu IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) oddílu.<br><br>Výchozí hodnota je `IList` \< <xref:System.Net.IPAddress>> obsahující jednu položku pro `IPAddress.IPv6Loopback`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Výchozí hodnota je `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Výchozí hodnota je `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Výchozí hodnota je `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Vyžadovat několik hodnot hlavičky byly synchronizované mezi [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) právě zpracovává.<br><br>Výchozí hodnotou v ASP.NET Core 1.x je `true`. Výchozí hodnota v ASP.NET Core 2.0 nebo novější je `false`. |

## <a name="scenarios-and-use-cases"></a>Scénáře a případy použití

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Pokud není možné přidat předané záhlaví a všechny požadavky jsou zabezpečené

V některých případech nemusí být možné přidat záhlaví přesměrované na požadavky směrovány přes proxy server k aplikaci. Pokud proxy server je vynucení, že jsou všechny veřejné externí požadavky HTTPS, schéma můžete ručně nastavit v `Startup.Configure` před použitím libovolného typu middleware:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Tento kód se dá zakázat pomocí proměnné prostředí nebo jiné nastavení konfigurace v vývojové nebo testovací prostředí.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Základ cesty a proxy servery, které se mění cestu požadavku

Některé proxy servery předat cestu beze změn, ale s aplikací základní cestu, která by se měly odebrat tak, aby směrování funguje správně. [UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware rozdělí cestu do [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cesty aplikace do [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Pokud `/foo` je základní cesty aplikace pro cestu proxy předán jako `/foo/api/1`, middleware sady `Request.PathBase` k `/foo` a `Request.Path` k `/api/1` pomocí následujícího příkazu:

```csharp
app.UsePathBase("/foo");
```

Původní cestu a základ cesty jsou znovu, když middleware volána znovu v opačném pořadí. Další informace o zpracování objednávky middleware, naleznete v tématu <xref:fundamentals/middleware/index>.

Pokud proxy server ořízne cestu (například předávání `/foo/api/1` k `/api/1`), oprava přesměruje a odkazy tak, že nastavíte žádosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) vlastnost:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Pokud proxy server je přidání data cesty, zahodit část cesty, chcete-li vyřešit odkazy a přesměrování pomocí <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> a přiřazení <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> vlastnost:

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Konfigurace proxy serveru, která používá odlišnou hlavičku názvy

Pokud proxy server nepoužívá záhlaví s názvem `X-Forwarded-For` a `X-Forwarded-Proto` dál adresa/port proxy serveru, které pocházejí informace schéma, nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> možnosti tak, aby odpovídaly názvy záhlaví používaný proxy serverem:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Konfigurace pro IPv4 adresu reprezentovaná jako adresu IPv6

Pokud server používá duální sokety, jsou ve formátu protokolu IPv6 zadané adresy IPv4 (například `10.0.0.1` IPv4 v IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1`). See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Určit, pokud tento formát je požadováno pohledem [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).

V následujícím příkladu je do poskytuje předané záhlaví síťová adresa `KnownNetworks` seznamu ve formátu protokolu IPv6.

Adresa IPv4: `10.11.12.1/8`

Převedený IPv6 adresa: `::ffff:10.11.12.1`  
Délka předpony převedený: 104

Můžete také zadat adresu v šestnáctkovém formátu (`10.11.12.1` v IPv6 jako `::ffff:0a0b:0c01`). Při převodu adresu IPv4 na IPv6, přidejte 96 do délka předpony CIDR (`8` v příkladu) pro další `::ffff:` předponu IPv6 (8 + 96 = 104). 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Vpřed schéma pro systémy Linux a nevyužívající službu IIS reverzních proxy serverů

.NET core šablony volání <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> a <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>. Tyto metody lokality umístěte do nekonečnou smyčku, pokud nasadíte do služby Azure Linux App Service, Azure s Linuxem virtuálních počítačů (VM), nebo za všechny ostatní reverzní proxy server kromě služby IIS. Protokol TLS je ukončeno prvkem reverzního proxy serveru a Kestrel není informován o schéma správnou žádost. OAuth a OIDC selhat i v této konfiguraci protože, která generují nesprávné přesměrování. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> Přidá a nakonfiguruje předané Middleware záhlaví při spuštění za služby IIS, ale neexistuje žádná odpovídající automatické konfigurace pro Linux (integrace Apache nebo Nginx).

Pokud chcete dál schéma z proxy serveru v scénáře nevyužívající službu IIS, přidejte a nakonfigurujte předané Middleware záhlaví. V `Startup.ConfigureServices`, použijte následující kód:

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

Dokud se nová Image kontejnerů jsou k dispozici v Azure, musíte vytvořit nastavení aplikace (proměnnou prostředí) pro `ASPNETCORE_FORWARDEDHEADERS_ENABLED` nastavena na `true`. Další informace najdete v tématu [šablony nebudou fungovat v systému Linux Antares z důvodu chybějícího předávání schéma (aspnet/AspNetCore #4135)](https://github.com/aspnet/AspNetCore/issues/4135).

::: moniker-end

## <a name="troubleshoot"></a>Řešení potíží

Pokud hlavičky nejsou předávány očekávaným, povolit [protokolování](xref:fundamentals/logging/index). Pokud protokoly neposkytují dostatek informací k vyřešení tohoto problému, výčet hlavičky žádosti přijaté serverem. Použití middlewaru vložených zapsat hlavičky požadavku pro odpověď aplikace nebo záhlaví protokolu. 

Do záhlaví se zapíší do odpovědi aplikace, umístěte následující middlewaru vložených terminálu ihned po volání <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure`:

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

Můžete zapisovat do protokolů místo textu odpovědi. Zápis do protokolů umožňuje lokality tak, aby funkce obvykle během ladění.

Zápis protokolů, nikoli do datové části odpovědi:

* Vložit `ILogger<Startup>` do `Startup` třídy, jak je popsáno v [vytvářet protokoly v spuštění](xref:fundamentals/logging/index#create-logs-in-startup).
* Umístěte následující middlewaru vložených ihned po volání <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure`.

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

Při zpracování `X-Forwarded-{For|Proto|Host}` hodnoty se přesunou do `X-Original-{For|Proto|Host}`. Pokud jsou zadaná hlavička více hodnot, mějte na paměti předané Middleware záhlaví procesy záhlaví v obráceném pořadí zprava doleva. Výchozí `ForwardLimit` je 1 (jeden), takže se zpracuje pouze krajní hodnotu z hlavičky, pokud hodnota `ForwardLimit` se zvýší.

Původní vzdálenou IP adresu požadavku musí shodovat s položkou v `KnownProxies` nebo `KnownNetworks` uvádí předtím, než se zpracovávají předané záhlaví. Toto nastavení omezuje záhlaví falšování identity tím, že služby předávání z nedůvěryhodné proxy servery. Při zjištění neznámé proxy protokolování určuje adresu proxy serveru:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

V předchozím příkladu je 10.0.0.100 proxy server. Pokud je server důvěryhodným proxy serverem, přidat IP adresu serveru `KnownProxies` (nebo přidat k důvěryhodné síti `KnownNetworks`) v `Startup.ConfigureServices`. Další informace najdete v tématu [možnosti předané middlewaru záhlaví](#forwarded-headers-middleware-options) oddílu.

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Povolte jenom důvěryhodným proxy serverů a sítí přeposílat záhlaví. V opačném případě [falšování adresy IP](https://www.iplocation.net/ip-spoofing) útoky jsou možné.

## <a name="certificate-forwarding"></a>Předávání certifikátu 

### <a name="on-azure"></a>V Azure

Zobrazit [dokumentace ke službě Azure](/azure/app-service/app-service-web-configure-tls-mutual-auth) ke konfiguraci Azure Web Apps. Ve vaší aplikaci `Startup.Configure` metodu, přidejte následující kód před voláním `app.UseAuthentication();`:

```csharp
app.UseCertificateForwarding();
```

Také budete muset nakonfigurovat certifikát předávání middlewarem k určení názvu záhlaví, která používá Azure. Ve vaší aplikaci `Startup.ConfigureServices` metodu, přidejte následující kód ke konfiguraci záhlaví, ze kterého middleware vytvoří certifikát:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="with-other-web-proxies"></a>Pomocí dalších webových proxy serverů

Pokud používáte proxy server, který není služby IIS nebo na Azure Web Apps směrování žádostí na aplikace, nakonfigurujte váš proxy server pro předávání certifikát, který obdržel v hlavičce HTTP. Ve vaší aplikaci `Startup.Configure` metodu, přidejte následující kód před voláním `app.UseAuthentication();`:

```csharp
app.UseCertificateForwarding();
```

Také budete muset nakonfigurovat certifikát předávání middlewarem k určení názvu záhlaví. Ve vaší aplikaci `Startup.ConfigureServices` metodu, přidejte následující kód ke konfiguraci záhlaví, ze kterého middleware vytvoří certifikát:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

Nakonec pokud proxy server dělá něco jiného než base64 kódování certifikátu (jako je tomu u serveru Nginx), můžete nastavit `HeaderConverter` možnost. Prohlédněte si následující příklad na `Startup.ConfigureServices`:

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

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/web-farm>
* [Microsoft Security Advisory CVE-2018-0787: ASP.NET Core chyba zabezpečení zvýšení oprávnění](https://github.com/aspnet/Announcements/issues/295)

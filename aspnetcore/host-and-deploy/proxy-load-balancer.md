---
title: Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení
author: guardrex
description: Další informace o konfiguraci aplikací hostovaných za službou proxy servery a nástroje pro vyrovnávání zatížení, které často skryl důležité vyžádat informace.
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2018
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 3ac67f0cb0c7b472e7192f684b1a8fc9685794ce
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901743"
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

Middleware záhlaví předané z [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) balení, načte tyto hlavičky a vyplní přidružených polí na [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).

Middleware aktualizace:

* [HttpContext.Connection.RemoteIpAddress](/dotnet/api/microsoft.aspnetcore.http.connectioninfo.remoteipaddress) &ndash; sada s použitím `X-Forwarded-For` hodnota hlavičky. Další nastavení ovlivní, jak middleware nastaví `RemoteIpAddress`. Podrobnosti najdete v tématu [možnosti předané middlewaru záhlaví](#forwarded-headers-middleware-options).
* [HttpContext.Request.Scheme](/dotnet/api/microsoft.aspnetcore.http.httprequest.scheme) &ndash; sada s použitím `X-Forwarded-Proto` hodnota hlavičky.
* [HttpContext.Request.Host](/dotnet/api/microsoft.aspnetcore.http.httprequest.host) &ndash; sada s použitím `X-Forwarded-Host` hodnota hlavičky.

Předané záhlaví Middleware [výchozí nastavení](#forwarded-headers-middleware-options) lze nakonfigurovat. Výchozí nastavení je:

* Existuje pouze *jeden proxy server* mezi aplikací a původu žádosti.
* Pouze adresy zpětné smyčky jsou nakonfigurované pro známé servery proxy a známé sítě.
* Přesměrovaná hlavičky jsou pojmenovány `X-Forwarded-For` a `X-Forwarded-Proto`.

Ne všechna síťová zařízení přidat `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví bez další konfigurace. Pokud požadavky směrovány přes proxy server, tyto hlavičky neobsahují, když dosáhnou aplikace, najdete pokyny výrobce vašeho zařízení. Pokud zařízení používá odlišnou hlavičku názvy než `X-Forwarded-For` a `X-Forwarded-Proto`, nastavte [ForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedforheadername) a [ForwardedProtoHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedprotoheadername) možnosti tak, aby odpovídaly názvy záhlaví, používá zařízení. Další informace najdete v tématu [možnosti předané middlewaru záhlaví](#forwarded-headers-middleware-options) a [konfiguraci proxy serveru, který používá odlišnou hlavičku názvy](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>Služby IIS/IIS Express a modul ASP.NET Core

Přesměrovaná záhlaví middlewaru je povolena ve výchozím nastavení Middleware pro integraci služby IIS při spuštění aplikace služby IIS a že modul ASP.NET Core. Middleware předaný záhlaví se aktivuje při spuštění první middleware kanál s omezenou konfiguraci konkrétní modul ASP.NET Core z důvodu otázky důvěryhodnosti předané hlavičky (například [falšování adresy IP](https://www.iplocation.net/ip-spoofing)). Middleware je nakonfigurovaný pro předávání `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví a je omezená na jednu localhost proxy. Pokud je vyžadována další konfigurace, najdete v článku [možnosti předané middlewaru záhlaví](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Další proxy serveru a scénáře pro nástroj pro vyrovnávání zatížení

Kromě použití Middleware pro integraci služby IIS, předané Middleware záhlaví není standardně povolená. Přesměrovaná Middleware záhlaví musí být povolené pro aplikace pro proces, předá záhlaví s [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders). Po povolení middleware, pokud žádné [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) jsou určené pro middleware, výchozí [ForwardedHeadersOptions.ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) jsou [ForwardedHeaders.None ](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders).

Nakonfigurujte middleware s [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) předávat `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví v `Startup.ConfigureServices`. Vyvolat [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) metoda `Startup.Configure` před voláním dalším middlewarem:

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
> Pokud ne [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) jsou určené v `Startup.ConfigureServices` nebo přímo na metodu rozšíření pomocí [UseForwardedHeaders (IApplicationBuilder, ForwardedHeadersOptions)](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_ForwardedHeadersExtensions_UseForwardedHeaders_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_ForwardedHeadersOptions_), výchozí hodnota předávat s hlavičkami [ForwardedHeaders.None](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders). [ForwardedHeadersOptions.ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) vlastnosti se musí nakonfigurovat hlavičky pro předávání.

## <a name="nginx-configuration"></a>V konfiguraci serveru Nginx

Předat dál `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví, najdete v článku <xref:host-and-deploy/linux-nginx#configure-nginx>. Další informace najdete v tématu [NGINX: Pomocí hlavičky předané](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Konfigurace serveru Apache

`X-Forwarded-For` je automaticky přidán (viz [mod_proxy Apache modul: Reverzní Proxy hlavičky žádosti](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Informace o tom, jak přesměrovat `X-Forwarded-Proto` záhlaví, najdete v článku <xref:host-and-deploy/linux-apache#configure-apache>.

## <a name="forwarded-headers-middleware-options"></a>Předané možnosti middlewaru záhlaví

[ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) řídí chování middlewaru předané záhlaví. Následující příklad změní výchozí hodnoty:

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

::: moniker range=">= aspnetcore-2.1"

| Možnost | Popis |
| ------ | ----------- |
| AllowedHosts | Hostitelé podle omezuje `X-Forwarded-Host` záhlaví zadanými hodnotami.<ul><li>Hodnoty jsou porovnány pomocí pořadí ignorování případu.</li><li>Čísla portů musí být vyloučeny.</li><li>Pokud je seznam prázdný, jsou povoleny všechny hostitele.</li><li>Nejvyšší úrovně zástupný znak `*` umožňuje všichni hostitelé prázdný.</li><li>Subdoména zástupné znaky jsou povolené, ale kořenové domény se neshodují. Například `*.contoso.com` odpovídá subdoménu `foo.contoso.com` , ale není kořenovou doménu `contoso.com`.</li><li>Názvy hostitelů Unicode jsou povolené, ale jsou převedeny na [kódování Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</li><li>[IPv6 adresy](https://tools.ietf.org/html/rfc4291) musí zahrnovat hranaté závorky ohraničující a je v [konvenční formuláře](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). IPv6 adresy nejsou speciální malými a velkými písmeny ke kontrole logické rovnost mezi různými formáty a je provedena bez převodu do kanonického tvaru.</li><li>Selhání omezit povolené hostitele může útočníkovi umožnit zfalšovat odkazy vygenerované službou.</li></ul>Výchozí hodnota je prázdná [IList\<řetězec >](/dotnet/api/system.collections.generic.ilist-1). |
| [ForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedforheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedforheadername). Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-For` záhlaví, ale používá některé hlavičky k předávání informací.<br><br>Výchozí hodnota je `X-Forwarded-For`. |
| [ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) | Určuje, které servery pro předávání by se měly zpracovat. Zobrazit [ForwardedHeaders výčtu](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders) seznam polí, které se vztahují. Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.<br><br>Výchozí hodnota je [ForwardedHeaders.None](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders). |
| [ForwardedHostHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedhostheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedHostHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedhostheadername). Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-Host` záhlaví, ale používá některé hlavičky k předávání informací.<br><br>Výchozí hodnota je `X-Forwarded-Host`. |
| [ForwardedProtoHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedprotoheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedProtoHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedprotoheadername). Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-Proto` záhlaví, ale používá některé hlavičky k předávání informací.<br><br>Výchozí hodnota je `X-Forwarded-Proto`. |
| [ForwardLimit](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardlimit) | Omezí počet položek v záhlaví, které se zpracovávají. Nastavte na `null` zakázat limit, ale to lze provádět pouze pokud `KnownProxies` nebo `KnownNetworks` jsou nakonfigurované.<br><br>Výchozí hodnota je 1. |
| [KnownNetworks](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.knownnetworks) | Rozsahy známých sítí tak, aby přijímal předané záhlaví z adres. Zadejte rozsahy IP adres pomocí notace CIDR (Classless Interdomain Routing) zápisu.<br><br>Pokud server používá duální sokety, jsou ve formátu protokolu IPv6 zadané adresy IPv4 (například `10.0.0.1` IPv4 v IPv6 jako `::ffff:10.0.0.1`). See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Určit, pokud tento formát je požadováno pohledem [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Další informace najdete v tématu [konfigurace pro adresu IPv4 přístupnou reprezentovaná jako adresu IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) oddílu.<br><br>Výchozí hodnota je [IList](/dotnet/api/system.collections.generic.ilist-1)\<[třídu ipnetwork –](/dotnet/api/microsoft.aspnetcore.httpoverrides.ipnetwork)> obsahující jednu položku pro `IPAddress.Loopback`. |
| [KnownProxies](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.knownproxies) | Adresy z předané hlavičky accept známých proxy služby. Použití `KnownProxies` určit přesné adresy IP odpovídá.<br><br>Pokud server používá duální sokety, jsou ve formátu protokolu IPv6 zadané adresy IPv4 (například `10.0.0.1` IPv4 v IPv6 jako `::ffff:10.0.0.1`). See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Určit, pokud tento formát je požadováno pohledem [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Další informace najdete v tématu [konfigurace pro adresu IPv4 přístupnou reprezentovaná jako adresu IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) oddílu.<br><br>Výchozí hodnota je [IList](/dotnet/api/system.collections.generic.ilist-1)\<[IPAddress](/dotnet/api/system.net.ipaddress)> obsahující jednu položku pro `IPAddress.IPv6Loopback`. |
| [OriginalForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalforheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalForHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalforheadername).<br><br>Výchozí hodnota je `X-Original-For`. |
| [OriginalHostHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalhostheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalHostHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalhostheadername).<br><br>Výchozí hodnota je `X-Original-Host`. |
| [OriginalProtoHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalprotoheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalProtoHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalprotoheadername).<br><br>Výchozí hodnota je `X-Original-Proto`. |
| [RequireHeaderSymmetry](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.requireheadersymmetry) | Vyžadovat několik hodnot hlavičky byly synchronizované mezi [ForwardedHeadersOptions.ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) právě zpracovává.<br><br>Výchozí hodnotou v ASP.NET Core 1.x je `true`. Výchozí hodnota v ASP.NET Core 2.0 nebo novější je `false`. |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| Možnost | Popis |
| ------ | ----------- |
| [ForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedforheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedforheadername). Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-For` záhlaví, ale používá některé hlavičky k předávání informací.<br><br>Výchozí hodnota je `X-Forwarded-For`. |
| [ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) | Určuje, které servery pro předávání by se měly zpracovat. Zobrazit [ForwardedHeaders výčtu](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders) seznam polí, které se vztahují. Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.<br><br>Výchozí hodnota je [ForwardedHeaders.None](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheaders). |
| [ForwardedHostHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedhostheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedHostHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedhostheadername). Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-Host` záhlaví, ale používá některé hlavičky k předávání informací.<br><br>Výchozí hodnota je `X-Forwarded-Host`. |
| [ForwardedProtoHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedprotoheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XForwardedProtoHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xforwardedprotoheadername). Tato možnost se používá, když nebude používat proxy server pro předávání `X-Forwarded-Proto` záhlaví, ale používá některé hlavičky k předávání informací.<br><br>Výchozí hodnota je `X-Forwarded-Proto`. |
| [ForwardLimit](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardlimit) | Omezí počet položek v záhlaví, které se zpracovávají. Nastavte na `null` zakázat limit, ale to lze provádět pouze pokud `KnownProxies` nebo `KnownNetworks` jsou nakonfigurované.<br><br>Výchozí hodnota je 1. |
| [KnownNetworks](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.knownnetworks) | Rozsahy známých sítí tak, aby přijímal předané záhlaví z adres. Zadejte rozsahy IP adres pomocí notace CIDR (Classless Interdomain Routing) zápisu.<br><br>Pokud server používá duální sokety, jsou ve formátu protokolu IPv6 zadané adresy IPv4 (například `10.0.0.1` IPv4 v IPv6 jako `::ffff:10.0.0.1`). See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Určit, pokud tento formát je požadováno pohledem [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Další informace najdete v tématu [konfigurace pro adresu IPv4 přístupnou reprezentovaná jako adresu IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) oddílu.<br><br>Výchozí hodnota je [IList](/dotnet/api/system.collections.generic.ilist-1)\<[třídu ipnetwork –](/dotnet/api/microsoft.aspnetcore.httpoverrides.ipnetwork)> obsahující jednu položku pro `IPAddress.Loopback`. |
| [KnownProxies](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.knownproxies) | Adresy z předané hlavičky accept známých proxy služby. Použití `KnownProxies` určit přesné adresy IP odpovídá.<br><br>Pokud server používá duální sokety, jsou ve formátu protokolu IPv6 zadané adresy IPv4 (například `10.0.0.1` IPv4 v IPv6 jako `::ffff:10.0.0.1`). See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). Určit, pokud tento formát je požadováno pohledem [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*). Další informace najdete v tématu [konfigurace pro adresu IPv4 přístupnou reprezentovaná jako adresu IPv6](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) oddílu.<br><br>Výchozí hodnota je [IList](/dotnet/api/system.collections.generic.ilist-1)\<[IPAddress](/dotnet/api/system.net.ipaddress)> obsahující jednu položku pro `IPAddress.IPv6Loopback`. |
| [OriginalForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalforheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalForHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalforheadername).<br><br>Výchozí hodnota je `X-Original-For`. |
| [OriginalHostHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalhostheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalHostHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalhostheadername).<br><br>Výchozí hodnota je `X-Original-Host`. |
| [OriginalProtoHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.originalprotoheadername) | Použít header této vlastnosti místo jednoho určeného parametrem [ForwardedHeadersDefaults.XOriginalProtoHeaderName](/dotnet/api/microsoft.aspnetcore.httpoverrides.forwardedheadersdefaults.xoriginalprotoheadername).<br><br>Výchozí hodnota je `X-Original-Proto`. |
| [RequireHeaderSymmetry](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.requireheadersymmetry) | Vyžadovat několik hodnot hlavičky byly synchronizované mezi [ForwardedHeadersOptions.ForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedheaders) právě zpracovává.<br><br>Výchozí hodnotou v ASP.NET Core 1.x je `true`. Výchozí hodnota v ASP.NET Core 2.0 nebo novější je `false`. |

::: moniker-end

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

Některé proxy servery předat cestu beze změn, ale s aplikací základní cestu, která by se měly odebrat tak, aby směrování funguje správně. [UsePathBaseExtensions.UsePathBase](/dotnet/api/microsoft.aspnetcore.builder.usepathbaseextensions.usepathbase) middleware rozdělí cestu do [HttpRequest.Path](/dotnet/api/microsoft.aspnetcore.http.httprequest.path) a základní cesty aplikace do [HttpRequest.PathBase](/dotnet/api/microsoft.aspnetcore.http.httprequest.pathbase).

Pokud `/foo` je základní cesty aplikace pro cestu proxy předán jako `/foo/api/1`, middleware sady `Request.PathBase` k `/foo` a `Request.Path` k `/api/1` pomocí následujícího příkazu:

```csharp
app.UsePathBase("/foo");
```

Původní cestu a základ cesty jsou znovu, když middleware volána znovu v opačném pořadí. Další informace o zpracování objednávky middleware, naleznete v tématu <xref:fundamentals/middleware/index>.

Pokud proxy server ořízne cestu (například předávání `/foo/api/1` k `/api/1`), oprava přesměruje a odkazy tak, že nastavíte žádosti [PathBase](/dotnet/api/microsoft.aspnetcore.http.httprequest.pathbase) vlastnost:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Pokud proxy server je přidání data cesty, zahodit část cesty, chcete-li vyřešit odkazy a přesměrování pomocí [StartsWithSegments (PathString, PathString)](/dotnet/api/microsoft.aspnetcore.http.pathstring.startswithsegments#Microsoft_AspNetCore_Http_PathString_StartsWithSegments_Microsoft_AspNetCore_Http_PathString_Microsoft_AspNetCore_Http_PathString__) a přiřazení [cesta](/dotnet/api/microsoft.aspnetcore.http.httprequest.path) vlastnost:

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

Pokud proxy server nepoužívá záhlaví s názvem `X-Forwarded-For` a `X-Forwarded-Proto` dál adresa/port proxy serveru, které pocházejí informace schéma, nastavte [ForwardedForHeaderName](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedforheadername) a [ForwardedProtoHeaderName ](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.forwardedprotoheadername) možnosti tak, aby odpovídaly názvy záhlaví používaný proxy serverem:

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

## <a name="troubleshoot"></a>Řešení potíží

Pokud hlavičky nejsou předávány očekávaným, povolit [protokolování](xref:fundamentals/logging/index). Pokud protokoly neposkytují dostatek informací k vyřešení tohoto problému, výčet hlavičky žádosti přijaté serverem. Použití middlewaru vložených zapsat hlavičky požadavku pro odpověď aplikace nebo záhlaví protokolu. Umístit některý z následujících příkladů kódu ihned po volání <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure`.

Do záhlaví se zapíší do odpovědi aplikaci, použijte následující middlewaru vložených terminálu:

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

Můžete taky zapisovat do protokolů místo text odpovědi s použitím následujících middlewaru vložených. To umožňuje lokality tak, aby fungoval při ladění.

```csharp
var logger = _loggerFactory.CreateLogger<Startup>();

app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    logger.LogDebug("Request Method: {METHOD}", context.Request.Method);
    logger.LogDebug("Request Scheme: {SCHEME}", context.Request.Scheme);
    logger.LogDebug("Request Path: {PATH}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        logger.LogDebug("Header: {KEY}: {VALUE}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    logger.LogDebug("Request RemoteIp: {REMOTE_IP_ADDRESS}", 
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

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/web-farm>
* [Microsoft Security Advisory CVE-2018-0787: ASP.NET Core chyba zabezpečení zvýšení oprávnění](https://github.com/aspnet/Announcements/issues/295)

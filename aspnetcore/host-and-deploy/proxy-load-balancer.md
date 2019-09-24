---
title: Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení
author: guardrex
description: Přečtěte si o konfiguraci pro aplikace hostované za servery proxy a nástroje pro vyrovnávání zatížení, které často překrývají důležité informace o žádostech.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/12/2019
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 3243f5d3254e6585ff9ca48900a3326aa9b6f502
ms.sourcegitcommit: 8a36be1bfee02eba3b07b7a86085ec25c38bae6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219172"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení

Od [Luke Latham](https://github.com/guardrex) a [Chris Rossův](https://github.com/Tratcher)

V Doporučené konfiguraci pro ASP.NET Core se aplikace hostuje pomocí IIS/ASP. Modul NET Core, Nginx nebo Apache. Proxy servery, nástroje pro vyrovnávání zatížení a další síťová zařízení často překrývají informace o žádosti předtím, než dosáhnou aplikace:

* Pokud jsou požadavky HTTPS proxy serverem přes HTTP, původní schéma (HTTPS) se ztratí a musí se přenášet v hlavičce.
* Vzhledem k tomu, že aplikace obdrží požadavek od proxy serveru a ne jeho skutečný zdroj v Internetu nebo podnikové síti, musí být zdrojová adresa IP klienta předána také v hlavičce.

Tyto informace můžou být důležité ve zpracování žádostí, například v přesměrování, ověřování, generování odkazů, vyhodnocení zásad a geografická poloha klienta.

## <a name="forwarded-headers"></a>Předávaná záhlaví

Podle konvence proxy předávají informace v hlavičkách protokolu HTTP.

| Záhlaví | Popis |
| ------ | ----------- |
| X-předané – pro | Uchovává informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy serverů. Tento parametr může obsahovat IP adresy (a volitelně také čísla portů). V řetězci proxy serverů označuje první parametr klienta, ve kterém se žádost poprvé nastavila. Následující identifikátory proxy následují. Poslední proxy server v řetězci není uveden v seznamu parametrů. Poslední IP adresa proxy serveru a volitelně je číslo portu, které je k dispozici jako Vzdálená IP adresa v transportní vrstvě. |
| X-Forwarded-Proto | Hodnota původního schématu (HTTP/HTTPS). Hodnota může být také seznamem schémat, pokud je požadavek procházen více proxy. |
| X-Forwarded-Host | Původní hodnota pole hlavičky hostitele Servery proxy většinou nemění hlavičku hostitele. Viz informační [zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pro informace o chybě zabezpečení zvýšení oprávnění, která má vliv na systémy, ve kterých server proxy neověřuje nebo omezuje hlavičky hostitele na známé správné hodnoty. |

Middleware předávaných hlaviček z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a vyplní související pole <xref:Microsoft.AspNetCore.Http.HttpContext>v.

Aktualizace middlewaru:

* [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; sada s použitím `X-Forwarded-For` hodnoty hlavičky. Další nastavení mají vliv na to, `RemoteIpAddress`jak middleware nastaví. Podrobnosti najdete v tématu [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).
* [HttpContext. Request. schématu](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; nastaveného pomocí `X-Forwarded-Proto` hodnoty hlavičky.
* [HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; sada s použitím `X-Forwarded-Host` hodnoty hlavičky.

Je možné nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru u předávaných hlaviček. Výchozí nastavení:

* Mezi aplikací a zdrojem požadavků je jenom *jeden proxy server* .
* Pro známé proxy servery a známé sítě jsou nakonfigurovány pouze adresy zpětné smyčky.
* Předané hlavičky jsou `X-Forwarded-For` pojmenovány `X-Forwarded-Proto`a.

Ne všechna síťová zařízení přidávají `X-Forwarded-For` hlavičky `X-Forwarded-Proto` a bez další konfigurace. Zeptejte se na pokyny výrobce zařízení, jestli proxy požadavky při přístupu do aplikace tyto hlavičky neobsahují. Pokud zařízení používá jiné názvy hlaviček `X-Forwarded-For` než a `X-Forwarded-Proto`, nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> možnosti a <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> tak, aby odpovídaly názvům hlaviček používaným zařízením. Další informace najdete v tématu [předané možnosti](#forwarded-headers-middleware-options) a konfigurace middlewarových hlaviček [pro proxy server, který používá jiné názvy hlaviček](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>Modul IIS/IIS Express a ASP.NET Core

Middleware předávaných hlaviček je ve výchozím nastavení povolená službou [IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , když je aplikace hostovaná [mimo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) službu IIS a modul ASP.NET Core. Middleware předávaných hlaviček se aktivují, aby se nejdříve spouštěl v kanálu middleware s omezenou konfigurací určenou pro modul ASP.NET Core, protože se jedná o obavy týkající se důvěryhodnosti se předanými hlavičkami (například [falšování IP adres](https://www.iplocation.net/ip-spoofing)). Middleware je nakonfigurovaná pro přeposílání `X-Forwarded-For` hlaviček a `X-Forwarded-Proto` a je omezená na jeden localhost proxy. Pokud se vyžaduje další konfigurace, přečtěte si téma [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Další scénáře proxy server a nástroje pro vyrovnávání zatížení

Bez použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimo procesy](xref:host-and-deploy/iis/index#out-of-process-hosting-model)není middleware předávaných hlaviček ve výchozím nastavení povolená. Middleware předaných hlaviček musí být povolená, aby aplikace zpracovala předávané hlavičky s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>. Po povolení middlewaru, pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> není zadáno pro middleware, výchozí [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Nakonfigurujte middleware s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nástrojem na `X-Forwarded-For` předáte `X-Forwarded-Proto` hlavičky a `Startup.ConfigureServices`v. Vyvolat metodu v `Startup.Configure` před voláním jiného middleware: <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>

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
> Pokud nejsou zadány v `Startup.ConfigureServices` nebo přímo k metodě rozšíření s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, výchozí hlavičky pro přeposílání jsou [ForwardedHeaders. None.](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> Vlastnost musí být nakonfigurována s hlavičkou pro přeposílání.

## <a name="nginx-configuration"></a>Konfigurace nginx

Pro `X-Forwarded-For` přeposílání `X-Forwarded-Proto` hlaviček a naleznete <xref:host-and-deploy/linux-nginx#configure-nginx>informace v tématu. Další informace najdete v tématu [Nginx: Používá se předávaná hlavička](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Konfigurace Apache

`X-Forwarded-For`se přidá automaticky (viz [Apache Module mod_proxy: Záhlaví](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)žádosti reverzního proxy serveru). Informace o tom, jak přeslat `X-Forwarded-Proto` hlavičku, najdete <xref:host-and-deploy/linux-apache#configure-apache>v tématu.

## <a name="forwarded-headers-middleware-options"></a>Možnosti middlewaru předávaných hlaviček

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>řízení chování middlewaru předávaných hlaviček. Následující příklad změní výchozí hodnoty:

* Omezte počet záznamů v předaných hlavičkách na `2`.
* Přidejte známou adresu `127.0.10.1`proxy serveru.
* Změňte název předané hlavičky z výchozí `X-Forwarded-For` na. `X-Forwarded-For-My-Custom-Header-Name`

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
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Omezí hostitele v `X-Forwarded-Host` hlavičce na zadané hodnoty.<ul><li>Hodnoty se porovnávají pomocí pořadí – ignorovat – případ.</li><li>Čísla portů musí být vyloučena.</li><li>Pokud je seznam prázdný, jsou povoleny všichni hostitelé.</li><li>Zástupný znak `*` nejvyšší úrovně umožňuje všem neprázdným hostitelům.</li><li>Zástupné znaky subdomény jsou povolené, ale neodpovídají kořenové doméně. Například odpovídá subdoméně `*.contoso.com` `foo.contoso.com` , ale nikoli ke kořenové doméně `contoso.com`.</li><li>Názvy hostitelů Unicode jsou povoleny, ale jsou převedeny na [Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</li><li>[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí zahrnovat ohraničující závorky a musí být v [konvenční formě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`). IPv6 adresy nejsou speciální – použita pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádné kanonikalizace.</li><li>Nepodaří-li se omezit povolené hostitele, může útočníkovi umožnit přístup k falešným odkazům generovanými službou.</li></ul>Výchozí hodnota je prázdná `IList<string>`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-For` hlavičku, ale k přeposílání informací používá jiné záhlaví.<br><br>Výchozí hodnota je `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Určuje, které předávacíci by se měly zpracovat. Seznam polí, která platí, najdete v tématu [ForwardedHeaders enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) . Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.<br><br>Výchozí hodnota je [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Host` hlavičku, ale k přeposílání informací používá jiné záhlaví.<br><br>Výchozí hodnota je `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Proto` hlavičku, ale k přeposílání informací používá jiné záhlaví.<br><br>Výchozí hodnota je `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Omezuje počet položek v záhlavích, které jsou zpracovávány. Nastavením tohoto omezení zakážete, ale to by mělo být provedeno pouze v `KnownProxies` případě `KnownNetworks` , že nebo jsou nakonfigurovány. `null` `null` Nastavení nehodnoty je preventivní opatrnost (ale ne záruka) pro ochranu proti nesprávně konfigurovaným proxy a škodlivým požadavkům přicházejících ze dvoustranných kanálů v síti.<br><br>Middleware předávaných hlaviček zpracovává hlavičky v obráceném pořadí zprava doleva. Pokud je použita výchozí hodnota`1`(), bude zpracována pouze hodnota zprava z hlaviček, pokud `ForwardLimit` hodnota není zvýšena.<br><br>Výchozí hodnota je `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Rozsahy adres známých sítí, ze kterých se mají přijímat předávací hlavičky. Zadejte rozsahy IP adres pomocí zápisu CIDR (Classless prosměrování mezi doménami).<br><br>Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1`). See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován. Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .<br><br>Výchozí `IList`hodnota je \< `IPAddress.Loopback`> obsahující jednu položku pro. <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy známých proxy adres, ze kterých se mají přijímat předávací hlavičky. Použijte `KnownProxies` k určení přesné shody IP adres.<br><br>Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1`). See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován. Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .<br><br>Výchozí `IList`hodnota je \< `IPAddress.IPv6Loopback`> obsahující jednu položku pro. <xref:System.Net.IPAddress> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Výchozí hodnota je `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Výchozí hodnota je `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Výchozí hodnota je `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Vyžaduje, aby byl počet synchronizovaných hodnot hlaviček mezi zpracovávaným [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .<br><br>Výchozí hodnota v ASP.NET Core 1. x je `true`. Výchozí hodnota v ASP.NET Core 2,0 nebo novější je `false`. |

## <a name="scenarios-and-use-cases"></a>Scénáře a případy použití

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Pokud není možné přidat předávané hlavičky a všechny požadavky jsou zabezpečené

V některých případech nemusí být možné přidat předávaná záhlaví do požadavků, které jsou proxy k aplikaci. Pokud proxy vynucuje, že všechny veřejné externí požadavky jsou https, můžete schéma ručně nastavit v `Startup.Configure` části před použitím libovolného typu middlewaru:

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

Tento kód může být zakázán pomocí proměnné prostředí nebo jiného nastavení konfigurace ve vývojovém nebo přípravném prostředí.

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a>Práce se základními a proxy servery, které mění cestu požadavku

Některé proxy servery předávají cestu beze změny, ale se základní cestou aplikace, která by se měla odebrat, aby směrování fungovalo správně. Middleware [UsePathBaseExtensions. UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware rozdělí cestu do [HttpRequest. cesta](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) a základní cestu aplikace do [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).

Pokud `/foo` je základní cesta aplikace pro cestu k proxy serveru předaná `/foo/api/1`jako, middleware `Request.PathBase` nastaví `/foo` na `Request.Path` a `/api/1` do pomocí následujícího příkazu:

```csharp
app.UsePathBase("/foo");
```

Původní cesta a základ cesty se znovu aplikují, když se middleware znovu zavolá v opačném případě. Další informace o zpracování pořadí middlewaru najdete v <xref:fundamentals/middleware/index>tématu.

Pokud proxy ořízne cestu (například předávání `/foo/api/1` na `/api/1`), opravte přesměrování a odkazy nastavením vlastnosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) žádosti:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Pokud proxy přidává data cest, zahodí část cesty pro opravu přesměrování a odkazy <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> pomocí a přiřazení <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> k vlastnosti:

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a>Konfigurace proxy serveru, který používá jiné názvy hlaviček

Pokud proxy nepoužívá hlavičky s názvem `X-Forwarded-For` a `X-Forwarded-Proto` k přeposlání informací o adrese proxy/portu a informace o původním <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> schématu, nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> možnosti a tak, aby odpovídaly názvům hlaviček používaným proxy serverem:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Konfigurace pro adresu IPv4 reprezentovanou jako IPv6 adresa

Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaného v protokolu IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1`). See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.

V následujícím příkladu se síťová adresa, která poskytuje předávaná záhlaví, přidá do `KnownNetworks` seznamu ve formátu protokolu IPv6.

Adresa IPv4:`10.11.12.1/8`

Převedená adresa IPv6:`::ffff:10.11.12.1`  
Délka převedené předpony: 104

Adresu můžete také uvést v šestnáctkovém formátu (`10.11.12.1` reprezentované protokolem IPv6 jako `::ffff:0a0b:0c01`). Při převodu adresy IPv4 na IPv6 přidejte 96 k délce předpony CIDR (`8` v příkladu), aby se zohlednila další `::ffff:` předpona IPv6 (8 + 96 = 104). 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a>Předejte schéma pro reverzní proxy servery se systémy Linux a non-IIS.

Aplikace, které <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> volají <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> a umísťují lokalitu do nekonečné smyčky, pokud jsou nasazené do Azure Linux App Service, virtuální počítač Azure Linux (VM) nebo za jakýkoli jiný reverzní proxy server, kromě služby IIS. Protokol TLS je ukončen reverzním proxy serverem a Kestrel není informován o správném schématu požadavků. OAuth a OIDC v této konfiguraci selže, protože generují nesprávná přesměrování. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Přidá a nakonfiguruje middleware předávaných hlaviček při spuštění za IIS, ale neexistuje žádná vyhovující Automatická konfigurace pro Linux (integrace Apache nebo Nginx).

Pro přeposílání schématu od proxy serveru ve scénářích mimo službu IIS přidejte a nakonfigurujte middleware s předanými hlavičkami. V `Startup.ConfigureServices`použijte následující kód:

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

Když hlavičky nejsou předávány podle očekávání, povolte [protokolování](xref:fundamentals/logging/index). Pokud protokoly neposkytují dostatek informací pro řešení tohoto problému, vytvořte výčet hlaviček požadavků přijatých serverem. K zápisu hlaviček požadavků do odpovědi aplikace nebo k protokolování hlaviček použijte vložený middlewarový middleware. 

Chcete-li zapsat hlavičky do odpovědi aplikace, umístěte následující vložený middleware terminálu hned po volání <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`do:

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

Do protokolů můžete zapisovat místo těla odpovědi. Zápis do protokolů umožňuje, aby lokalita fungovala normálně během ladění.

Zápis protokolů místo textu odpovědi:

* `ILogger<Startup>` Vložení`Startup` do třídy, jak je popsáno v tématu [vytvoření protokolů při spuštění](xref:fundamentals/logging/index#create-logs-in-startup).
* Vložte následující vložený middleware hned po volání <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> do v. `Startup.Configure`

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

Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty přesunuty na `X-Original-{For|Proto|Host}`. Pokud existuje více hodnot v dané hlavičce, middleware pro přesměrované hlavičky zpracovává záhlaví v opačném pořadí zprava doleva. Výchozí `ForwardLimit` hodnota je `1` (jedna), takže je `ForwardLimit` zpracována pouze hodnota zprava z hlaviček, pokud hodnota není zvýšena.

Původní Vzdálená IP adresa žádosti se musí shodovat s položkou v `KnownProxies` seznamech nebo `KnownNetworks` před zpracováním předávaných hlaviček. To omezuje falšování hlaviček tím, že nepřijímá předávací servery od nedůvěryhodných proxy serverů. Při zjištění neznámého proxy serveru se zobrazí zpráva s informacemi o adrese proxy serveru:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

V předchozím příkladu je 10.0.0.100 proxy server. Pokud je server důvěryhodným proxy serverem, přidejte IP adresu serveru do `KnownProxies` (nebo přidejte důvěryhodnou síť do `KnownNetworks`) v `Startup.ConfigureServices`. Další informace najdete v části [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options) .

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Povoluje pouze důvěryhodné proxy servery a sítě pro přeposílání hlaviček. V opačném případě jsou možné útoky s [falešnou IP adresou](https://www.iplocation.net/ip-spoofing) .

## <a name="certificate-forwarding"></a>Předávání certifikátů 

### <a name="on-azure"></a>V Azure

V [dokumentaci k Azure](/azure/app-service/app-service-web-configure-tls-mutual-auth) můžete nakonfigurovat Azure Web Apps. V `Startup.Configure` metodě vaší aplikace přidejte následující kód před `app.UseAuthentication();`voláním:

```csharp
app.UseCertificateForwarding();
```

Také budete muset nakonfigurovat middleware předávání certifikátů a zadat název hlavičky, který Azure používá. V `Startup.ConfigureServices` metodě vaší aplikace přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="with-other-web-proxies"></a>S dalšími webovými proxy servery

Pokud používáte proxy server, který není službou IIS nebo Azure Web Apps v rámci směrování žádostí o aplikace, nakonfigurujte proxy server tak, aby předal certifikát, který přijal v hlavičce protokolu HTTP. V `Startup.Configure` metodě vaší aplikace přidejte následující kód před `app.UseAuthentication();`voláním:

```csharp
app.UseCertificateForwarding();
```

Také budete muset nakonfigurovat middleware předávání certifikátů a zadat název hlavičky. V `Startup.ConfigureServices` metodě vaší aplikace přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

Nakonec platí, že pokud má proxy něco jiného než kódování Base64 certifikátu (jako je případ s Nginx), nastavte `HeaderConverter` možnost. Vezměte v `Startup.ConfigureServices`úvahu následující příklad:

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
* [Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET Core zvýšení oprávnění k ohrožení zabezpečení](https://github.com/aspnet/Announcements/issues/295)

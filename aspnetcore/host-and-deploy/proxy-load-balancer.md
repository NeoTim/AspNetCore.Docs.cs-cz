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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: aab0ec0d142d840ae2b480add9e0416fb27e378e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403908"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a>Konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení

Autor – [Chris Rossův](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.0"

V Doporučené konfiguraci pro ASP.NET Core se aplikace hostuje pomocí IIS/ASP. Modul NET Core, Nginx nebo Apache. Proxy servery, nástroje pro vyrovnávání zatížení a další síťová zařízení často překrývají informace o žádosti předtím, než dosáhnou aplikace:

* Pokud jsou požadavky HTTPS proxy serverem přes HTTP, původní schéma (HTTPS) se ztratí a musí se přenášet v hlavičce.
* Vzhledem k tomu, že aplikace obdrží požadavek od proxy serveru a ne jeho skutečný zdroj v Internetu nebo podnikové síti, musí být zdrojová adresa IP klienta předána také v hlavičce.

Tyto informace můžou být důležité ve zpracování žádostí, například v přesměrování, ověřování, generování odkazů, vyhodnocení zásad a geografická poloha klienta.

## <a name="forwarded-headers"></a>Předávaná záhlaví

Podle konvence proxy předávají informace v hlavičkách protokolu HTTP.

| Záhlaví | Description |
| ------ | ----------- |
| X-předané – pro | Uchovává informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy serverů. Tento parametr může obsahovat IP adresy (a volitelně také čísla portů). V řetězci proxy serverů označuje první parametr klienta, ve kterém se žádost poprvé nastavila. Následující identifikátory proxy následují. Poslední proxy server v řetězci není uveden v seznamu parametrů. Poslední IP adresa proxy serveru a volitelně je číslo portu, které je k dispozici jako Vzdálená IP adresa v transportní vrstvě. |
| X-předáno – proto | Hodnota původního schématu (HTTP/HTTPS). Hodnota může být také seznamem schémat, pokud je požadavek procházen více proxy. |
| X-předávaný-Host | Původní hodnota pole hlavičky hostitele Servery proxy většinou nemění hlavičku hostitele. Viz informační [zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pro informace o chybě zabezpečení zvýšení oprávnění, která má vliv na systémy, ve kterých server proxy neověřuje nebo omezuje hlavičky hostitele na známé správné hodnoty. |

Middleware předávaných hlaviček z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a vyplní související pole v <xref:Microsoft.AspNetCore.Http.HttpContext> .

Aktualizace middlewaru:

* [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): nastavte pomocí `X-Forwarded-For` hodnoty záhlaví. Další nastavení mají vliv na to, jak middleware nastaví `RemoteIpAddress` . Podrobnosti najdete v tématu [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).
* [HttpContext. Request. schéma](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): nastavte pomocí `X-Forwarded-Proto` hodnoty záhlaví.
* [HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): nastavte pomocí `X-Forwarded-Host` hodnoty záhlaví.

Je možné nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru u předávaných hlaviček. Výchozí nastavení:

* Mezi aplikací a zdrojem požadavků je jenom *jeden proxy server* .
* Pro známé proxy servery a známé sítě jsou nakonfigurovány pouze adresy zpětné smyčky.
* Předané hlavičky jsou pojmenovány `X-Forwarded-For` a `X-Forwarded-Proto` .

Ne všechna síťová zařízení přidávají `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a bez další konfigurace. Zeptejte se na pokyny výrobce zařízení, jestli proxy požadavky při přístupu do aplikace tyto hlavičky neobsahují. Pokud zařízení používá jiné názvy hlaviček než `X-Forwarded-For` a `X-Forwarded-Proto` , nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným zařízením. Další informace najdete v tématu [předané možnosti a konfigurace middlewarových hlaviček](#forwarded-headers-middleware-options) [pro proxy server, který používá jiné názvy hlaviček](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>Modul IIS/IIS Express a ASP.NET Core

Middleware předávaných hlaviček je ve výchozím nastavení povolená službou [IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , když je aplikace hostovaná [mimo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) službu IIS a modul ASP.NET Core. Middleware předávaných hlaviček se aktivují, aby se nejdříve spouštěl v kanálu middleware s omezenou konfigurací určenou pro modul ASP.NET Core, protože se jedná o obavy týkající se důvěryhodnosti se předanými hlavičkami (například [falšování IP adres](https://www.iplocation.net/ip-spoofing)). Middleware je nakonfigurovaná pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a a je omezená na jeden localhost proxy. Pokud se vyžaduje další konfigurace, přečtěte si téma [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Další scénáře proxy server a nástroje pro vyrovnávání zatížení

Bez použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimo procesy](xref:host-and-deploy/iis/index#out-of-process-hosting-model)není middleware předávaných hlaviček ve výchozím nastavení povolená. Middleware předaných hlaviček musí být povolená, aby aplikace zpracovala předávané hlavičky s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> . Po povolení middlewaru, pokud není <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zadáno pro middleware, výchozí [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Nakonfigurujte middleware s nástrojem <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> na předáte `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a v `Startup.ConfigureServices` .

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a>Pořadí middlewaru u předávaných hlaviček

Middleware předaných hlaviček by se měla spustit před jiným middlewarem. Toto řazení zajišťuje, aby middleware spoléhající se na předané informace hlaviček mohl spotřebovat hodnoty hlaviček pro zpracování. Middleware předávaných hlaviček se dá spustit po diagnostice a zpracování chyb, ale musí se spustit před voláním `UseHsts` :

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

Nebo volejte `UseForwardedHeaders` před diagnostikou:

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` nebo přímo k metodě rozšíření s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , výchozí hlavičky pro přeposílání jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Vlastnost musí být nakonfigurována s hlavičkou pro přeposílání.

## <a name="nginx-configuration"></a>Konfigurace nginx

Pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a naleznete informace v tématu <xref:host-and-deploy/linux-nginx#configure-nginx> . Další informace najdete v tématu [Nginx: použití předané hlavičky](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Konfigurace Apache

`X-Forwarded-For`se přidá automaticky (viz část [modulu Apache mod_proxy: hlavičky žádosti reverzního proxy serveru](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Informace o tom, jak přeslat `X-Forwarded-Proto` hlavičku, najdete v tématu <xref:host-and-deploy/linux-apache#configure-apache> .

## <a name="forwarded-headers-middleware-options"></a>Možnosti middlewaru předávaných hlaviček

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>řízení chování middlewaru předávaných hlaviček. Následující příklad změní výchozí hodnoty:

* Omezte počet záznamů v předaných hlavičkách na `2` .
* Přidejte známou adresu proxy serveru `127.0.10.1` .
* Změňte název předané hlavičky z výchozí `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Možnost | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Omezí hostitele v `X-Forwarded-Host` hlavičce na zadané hodnoty.<ul><li>Hodnoty se porovnávají pomocí pořadí – ignorovat – případ.</li><li>Čísla portů musí být vyloučena.</li><li>Pokud je seznam prázdný, jsou povoleny všichni hostitelé.</li><li>Zástupný znak nejvyšší úrovně `*` umožňuje všem neprázdným hostitelům.</li><li>Zástupné znaky subdomény jsou povolené, ale neodpovídají kořenové doméně. Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale nikoli ke kořenové doméně `contoso.com` .</li><li>Názvy hostitelů Unicode jsou povoleny, ale jsou převedeny na [Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</li><li>[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí zahrnovat ohraničující závorky a musí být v [konvenční formě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ). IPv6 adresy nejsou speciální – použita pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádné kanonikalizace.</li><li>Nepodaří-li se omezit povolené hostitele, může útočníkovi umožnit přístup k falešným odkazům generovanými službou.</li></ul>Výchozí hodnota je prázdná `IList<string>` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-For` hlavičku, ale k přeposílání informací používá jiné záhlaví.<br><br>Výchozí formát je `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Určuje, které předávacíci by se měly zpracovat. Seznam polí, která platí, najdete v tématu [ForwardedHeaders enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) . Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .<br><br>Výchozí hodnota je [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Host` hlavičku, ale k přeposílání informací používá jiné záhlaví.<br><br>Výchozí formát je `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Proto` hlavičku, ale k přeposílání informací používá jiné záhlaví.<br><br>Výchozí formát je `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Omezuje počet položek v záhlavích, které jsou zpracovávány. Nastavením `null` tohoto omezení zakážete, ale to by mělo být provedeno pouze v případě, že `KnownProxies` nebo `KnownNetworks` jsou nakonfigurovány. Nastavení `null` nehodnoty je preventivní opatrnost (ale ne záruka) pro ochranu proti nesprávně konfigurovaným proxy a škodlivým požadavkům přicházejících ze dvoustranných kanálů v síti.<br><br>Middleware předávaných hlaviček zpracovává hlavičky v obráceném pořadí zprava doleva. Pokud je použita výchozí hodnota ( `1` ), bude zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.<br><br>Výchozí formát je `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Rozsahy adres známých sítí, ze kterých se mají přijímat předávací hlavičky. Zadejte rozsahy IP adres pomocí zápisu CIDR (Classless prosměrování mezi doménami).<br><br>Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ). Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován. Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .<br><br>Výchozí hodnota je `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> obsahující jednu položku pro `IPAddress.Loopback` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy známých proxy adres, ze kterých se mají přijímat předávací hlavičky. Použijte `KnownProxies` k určení přesné shody IP adres.<br><br>Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ). Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován. Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .<br><br>Výchozí hodnota je `IList` \<<xref:System.Net.IPAddress>> obsahující jednu položku pro `IPAddress.IPv6Loopback` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Výchozí formát je `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Výchozí formát je `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Výchozí formát je `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Vyžaduje, aby byl počet synchronizovaných hodnot hlaviček mezi zpracovávaným [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .<br><br>Výchozí hodnota v ASP.NET Core 1. x je `true` . Výchozí hodnota v ASP.NET Core 2,0 nebo novější je `false` . |

## <a name="scenarios-and-use-cases"></a>Scénáře a případy použití

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Pokud není možné přidat předávané hlavičky a všechny požadavky jsou zabezpečené

V některých případech nemusí být možné přidat předávaná záhlaví do požadavků, které jsou proxy k aplikaci. Pokud proxy vynucuje, že všechny veřejné externí požadavky jsou HTTPS, můžete schéma ručně nastavit v části `Startup.Configure` před použitím libovolného typu middlewaru:

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

Pokud `/foo` je základní cesta aplikace pro cestu k proxy serveru předaná jako `/foo/api/1` , middleware nastaví `Request.PathBase` na `/foo` a `Request.Path` do `/api/1` pomocí následujícího příkazu:

```csharp
app.UsePathBase("/foo");
```

Původní cesta a základ cesty se znovu aplikují, když se middleware znovu zavolá v opačném případě. Další informace o zpracování pořadí middlewaru najdete v tématu <xref:fundamentals/middleware/index> .

Pokud proxy ořízne cestu (například předávání `/foo/api/1` na `/api/1` ), opravte přesměrování a odkazy nastavením vlastnosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) žádosti:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Pokud proxy přidává data cest, zahodí část cesty pro opravu přesměrování a odkazy pomocí <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> a přiřazení k <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> vlastnosti:

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

Pokud proxy nepoužívá hlavičky s názvem `X-Forwarded-For` a `X-Forwarded-Proto` k přeposlání informací o adrese proxy/portu a informace o původním schématu, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným proxy serverem:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Konfigurace pro adresu IPv4 reprezentovanou jako IPv6 adresa

Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaného v protokolu IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1` ). Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.

V následujícím příkladu se síťová adresa, která poskytuje předávaná záhlaví, přidá do `KnownNetworks` seznamu ve formátu protokolu IPv6.

Adresa IPv4:`10.11.12.1/8`

Převedená adresa IPv6:`::ffff:10.11.12.1`  
Délka převedené předpony: 104

Adresu můžete také uvést v šestnáctkovém formátu ( `10.11.12.1` reprezentované protokolem IPv6 jako `::ffff:0a0b:0c01` ). Při převodu adresy IPv4 na IPv6 přidejte 96 k délce předpony CIDR ( `8` v příkladu), aby se zohlednila další `::ffff:` předpona IPv6 (8 + 96 = 104). 

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

Aplikace, které volají <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> a <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umísťují lokalitu do nekonečné smyčky, pokud jsou nasazené do azure Linux App Service, virtuální počítač Azure Linux (VM) nebo za jakýkoli jiný reverzní proxy server, kromě služby IIS. Protokol TLS je ukončen reverzním proxy serverem a Kestrel není informován o správném schématu požadavků. OAuth a OIDC v této konfiguraci selže, protože generují nesprávná přesměrování. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Přidá a nakonfiguruje middleware předávaných hlaviček při spuštění za IIS, ale neexistuje žádná vyhovující Automatická konfigurace pro Linux (integrace Apache nebo Nginx).

Pro přeposílání schématu od proxy serveru ve scénářích mimo službu IIS přidejte a nakonfigurujte middleware s předanými hlavičkami. V `Startup.ConfigureServices` použijte následující kód:

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

Pokud chcete nakonfigurovat Azure App Service pro předávání certifikátů, přečtěte si téma [Konfigurace vzájemného ověřování TLS pro Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth). Následující pokyny se týkají konfigurace aplikace ASP.NET Core.

V `Startup.Configure` přidejte následující kód před volání do `app.UseAuthentication();` :

```csharp
app.UseCertificateForwarding();
```


Nakonfigurujte middleware předávání certifikátů a určete název hlavičky, kterou používá Azure. V `Startup.ConfigureServices` přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a>Další webové proxy servery

Pokud se používá proxy server, který není službou IIS nebo Azure App Service směrování žádostí na aplikace (ARR), nakonfigurujte proxy server tak, aby předal certifikát, který přijal v hlavičce protokolu HTTP. V `Startup.Configure` přidejte následující kód před volání do `app.UseAuthentication();` :

```csharp
app.UseCertificateForwarding();
```

Nakonfigurujte middleware předávání certifikátů a zadejte název záhlaví. V `Startup.ConfigureServices` přidejte následující kód pro konfiguraci hlavičky, ze které middleware vytváří certifikát:

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

Pokud proxy server nemá kódování Base64 (jako je případ s Nginx), nastavte `HeaderConverter` možnost. Vezměte v úvahu následující příklad `Startup.ConfigureServices` :

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

Když hlavičky nejsou předávány podle očekávání, povolte [protokolování](xref:fundamentals/logging/index). Pokud protokoly neposkytují dostatek informací pro řešení tohoto problému, vytvořte výčet hlaviček požadavků přijatých serverem. K zápisu hlaviček požadavků do odpovědi aplikace nebo k protokolování hlaviček použijte vložený middlewarový middleware. 

Chcete-li zapsat hlavičky do odpovědi aplikace, umístěte následující vložený middleware terminálu hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :

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

* Vložení `ILogger<Startup>` do `Startup` třídy, jak je popsáno v tématu [vytvoření protokolů při spuštění](xref:fundamentals/logging/index#create-logs-in-startup).
* Vložte následující vložený middleware hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure` .

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

Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty přesunuty na `X-Original-{For|Proto|Host}` . Pokud existuje více hodnot v dané hlavičce, middleware pro přesměrované hlavičky zpracovává záhlaví v opačném pořadí zprava doleva. Výchozí hodnota `ForwardLimit` je `1` (jedna), takže je zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.

Původní Vzdálená IP adresa žádosti se musí shodovat s položkou v `KnownProxies` `KnownNetworks` seznamech nebo před zpracováním předávaných hlaviček. To omezuje falšování hlaviček tím, že nepřijímá předávací servery od nedůvěryhodných proxy serverů. Při zjištění neznámého proxy serveru se zobrazí zpráva s informacemi o adrese proxy serveru:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

V předchozím příkladu je 10.0.0.100 proxy server. Pokud je server důvěryhodným proxy serverem, přidejte IP adresu serveru do `KnownProxies` (nebo přidejte důvěryhodnou síť do `KnownNetworks` ) v `Startup.ConfigureServices` . Další informace najdete v části [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options) .

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Povoluje pouze důvěryhodné proxy servery a sítě pro přeposílání hlaviček. V opačném případě jsou možné útoky s [falešnou IP adresou](https://www.iplocation.net/ip-spoofing) .

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/web-farm>
* [Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET Core zvýšení zabezpečení oprávnění](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V Doporučené konfiguraci pro ASP.NET Core se aplikace hostuje pomocí IIS/ASP. Modul NET Core, Nginx nebo Apache. Proxy servery, nástroje pro vyrovnávání zatížení a další síťová zařízení často překrývají informace o žádosti předtím, než dosáhnou aplikace:

* Pokud jsou požadavky HTTPS proxy serverem přes HTTP, původní schéma (HTTPS) se ztratí a musí se přenášet v hlavičce.
* Vzhledem k tomu, že aplikace obdrží požadavek od proxy serveru a ne jeho skutečný zdroj v Internetu nebo podnikové síti, musí být zdrojová adresa IP klienta předána také v hlavičce.

Tyto informace můžou být důležité ve zpracování žádostí, například v přesměrování, ověřování, generování odkazů, vyhodnocení zásad a geografická poloha klienta.

## <a name="forwarded-headers"></a>Předávaná záhlaví

Podle konvence proxy předávají informace v hlavičkách protokolu HTTP.

| Záhlaví | Description |
| ------ | ----------- |
| X-předané – pro | Uchovává informace o klientovi, který inicioval požadavek a následné proxy servery v řetězci proxy serverů. Tento parametr může obsahovat IP adresy (a volitelně také čísla portů). V řetězci proxy serverů označuje první parametr klienta, ve kterém se žádost poprvé nastavila. Následující identifikátory proxy následují. Poslední proxy server v řetězci není uveden v seznamu parametrů. Poslední IP adresa proxy serveru a volitelně je číslo portu, které je k dispozici jako Vzdálená IP adresa v transportní vrstvě. |
| X-předáno – proto | Hodnota původního schématu (HTTP/HTTPS). Hodnota může být také seznamem schémat, pokud je požadavek procházen více proxy. |
| X-předávaný-Host | Původní hodnota pole hlavičky hostitele Servery proxy většinou nemění hlavičku hostitele. Viz informační [zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) pro informace o chybě zabezpečení zvýšení oprávnění, která má vliv na systémy, ve kterých server proxy neověřuje nebo omezuje hlavičky hostitele na známé správné hodnoty. |

Middleware předávaných hlaviček z balíčku [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) přečte tato záhlaví a vyplní související pole v <xref:Microsoft.AspNetCore.Http.HttpContext> .

Aktualizace middlewaru:

* [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): nastavte pomocí `X-Forwarded-For` hodnoty záhlaví. Další nastavení mají vliv na to, jak middleware nastaví `RemoteIpAddress` . Podrobnosti najdete v tématu [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).
* [HttpContext. Request. schéma](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): nastavte pomocí `X-Forwarded-Proto` hodnoty záhlaví.
* [HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): nastavte pomocí `X-Forwarded-Host` hodnoty záhlaví.

Je možné nakonfigurovat [výchozí nastavení](#forwarded-headers-middleware-options) middlewaru u předávaných hlaviček. Výchozí nastavení:

* Mezi aplikací a zdrojem požadavků je jenom *jeden proxy server* .
* Pro známé proxy servery a známé sítě jsou nakonfigurovány pouze adresy zpětné smyčky.
* Předané hlavičky jsou pojmenovány `X-Forwarded-For` a `X-Forwarded-Proto` .

Ne všechna síťová zařízení přidávají `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a bez další konfigurace. Zeptejte se na pokyny výrobce zařízení, jestli proxy požadavky při přístupu do aplikace tyto hlavičky neobsahují. Pokud zařízení používá jiné názvy hlaviček než `X-Forwarded-For` a `X-Forwarded-Proto` , nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným zařízením. Další informace najdete v tématu [předané možnosti a konfigurace middlewarových hlaviček](#forwarded-headers-middleware-options) [pro proxy server, který používá jiné názvy hlaviček](#configuration-for-a-proxy-that-uses-different-header-names).

## <a name="iisiis-express-and-aspnet-core-module"></a>Modul IIS/IIS Express a ASP.NET Core

Middleware předávaných hlaviček je ve výchozím nastavení povolená službou [IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) , když je aplikace hostovaná [mimo](xref:host-and-deploy/iis/index#out-of-process-hosting-model) službu IIS a modul ASP.NET Core. Middleware předávaných hlaviček se aktivují, aby se nejdříve spouštěl v kanálu middleware s omezenou konfigurací určenou pro modul ASP.NET Core, protože se jedná o obavy týkající se důvěryhodnosti se předanými hlavičkami (například [falšování IP adres](https://www.iplocation.net/ip-spoofing)). Middleware je nakonfigurovaná pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a a je omezená na jeden localhost proxy. Pokud se vyžaduje další konfigurace, přečtěte si téma [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options).

## <a name="other-proxy-server-and-load-balancer-scenarios"></a>Další scénáře proxy server a nástroje pro vyrovnávání zatížení

Bez použití [integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) při hostování [mimo procesy](xref:host-and-deploy/iis/index#out-of-process-hosting-model)není middleware předávaných hlaviček ve výchozím nastavení povolená. Middleware předaných hlaviček musí být povolená, aby aplikace zpracovala předávané hlavičky s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> . Po povolení middlewaru, pokud není <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> zadáno pro middleware, výchozí [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).

Nakonfigurujte middleware s nástrojem <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> na předáte `X-Forwarded-For` `X-Forwarded-Proto` hlavičky a v `Startup.ConfigureServices` . Vyvolat <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> metodu v `Startup.Configure` před voláním jiného middleware:

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
> Pokud <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> nejsou zadány v `Startup.ConfigureServices` nebo přímo k metodě rozšíření s <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , výchozí hlavičky pro přeposílání jsou [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Vlastnost musí být nakonfigurována s hlavičkou pro přeposílání.

## <a name="nginx-configuration"></a>Konfigurace nginx

Pro přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a naleznete informace v tématu <xref:host-and-deploy/linux-nginx#configure-nginx> . Další informace najdete v tématu [Nginx: použití předané hlavičky](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).

## <a name="apache-configuration"></a>Konfigurace Apache

`X-Forwarded-For`se přidá automaticky (viz část [modulu Apache mod_proxy: hlavičky žádosti reverzního proxy serveru](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)). Informace o tom, jak přeslat `X-Forwarded-Proto` hlavičku, najdete v tématu <xref:host-and-deploy/linux-apache#configure-apache> .

## <a name="forwarded-headers-middleware-options"></a>Možnosti middlewaru předávaných hlaviček

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>řízení chování middlewaru předávaných hlaviček. Následující příklad změní výchozí hodnoty:

* Omezte počet záznamů v předaných hlavičkách na `2` .
* Přidejte známou adresu proxy serveru `127.0.10.1` .
* Změňte název předané hlavičky z výchozí `X-Forwarded-For` na `X-Forwarded-For-My-Custom-Header-Name` .

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| Možnost | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Omezí hostitele v `X-Forwarded-Host` hlavičce na zadané hodnoty.<ul><li>Hodnoty se porovnávají pomocí pořadí – ignorovat – případ.</li><li>Čísla portů musí být vyloučena.</li><li>Pokud je seznam prázdný, jsou povoleny všichni hostitelé.</li><li>Zástupný znak nejvyšší úrovně `*` umožňuje všem neprázdným hostitelům.</li><li>Zástupné znaky subdomény jsou povolené, ale neodpovídají kořenové doméně. Například `*.contoso.com` odpovídá subdoméně, `foo.contoso.com` ale nikoli ke kořenové doméně `contoso.com` .</li><li>Názvy hostitelů Unicode jsou povoleny, ale jsou převedeny na [Punycode](https://tools.ietf.org/html/rfc3492) pro porovnání.</li><li>[Adresy IPv6](https://tools.ietf.org/html/rfc4291) musí zahrnovat ohraničující závorky a musí být v [konvenční formě](https://tools.ietf.org/html/rfc4291#section-2.2) (například `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ). IPv6 adresy nejsou speciální – použita pro kontrolu logické rovnosti mezi různými formáty a neprovádí se žádné kanonikalizace.</li><li>Nepodaří-li se omezit povolené hostitele, může útočníkovi umožnit přístup k falešným odkazům generovanými službou.</li></ul>Výchozí hodnota je prázdná `IList<string>` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName). Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-For` hlavičku, ale k přeposílání informací používá jiné záhlaví.<br><br>Výchozí formát je `X-Forwarded-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Určuje, které předávacíci by se měly zpracovat. Seznam polí, která platí, najdete v tématu [ForwardedHeaders enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) . Typické hodnoty přiřazené k této vlastnosti jsou `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .<br><br>Výchozí hodnota je [ForwardedHeaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders). |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName). Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Host` hlavičku, ale k přeposílání informací používá jiné záhlaví.<br><br>Výchozí formát je `X-Forwarded-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName). Tato možnost se používá, když proxy nebo předávací server nepoužívá `X-Forwarded-Proto` hlavičku, ale k přeposílání informací používá jiné záhlaví.<br><br>Výchozí formát je `X-Forwarded-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Omezuje počet položek v záhlavích, které jsou zpracovávány. Nastavením `null` tohoto omezení zakážete, ale to by mělo být provedeno pouze v případě, že `KnownProxies` nebo `KnownNetworks` jsou nakonfigurovány. Nastavení `null` nehodnoty je preventivní opatrnost (ale ne záruka) pro ochranu proti nesprávně konfigurovaným proxy a škodlivým požadavkům přicházejících ze dvoustranných kanálů v síti.<br><br>Middleware předávaných hlaviček zpracovává hlavičky v obráceném pořadí zprava doleva. Pokud je použita výchozí hodnota ( `1` ), bude zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.<br><br>Výchozí formát je `1`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Rozsahy adres známých sítí, ze kterých se mají přijímat předávací hlavičky. Zadejte rozsahy IP adres pomocí zápisu CIDR (Classless prosměrování mezi doménami).<br><br>Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ). Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován. Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .<br><br>Výchozí hodnota je `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> obsahující jednu položku pro `IPAddress.Loopback` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Adresy známých proxy adres, ze kterých se mají přijímat předávací hlavičky. Použijte `KnownProxies` k určení přesné shody IP adres.<br><br>Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaném v protokolu IPv6 jako `::ffff:10.0.0.1` ). Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován. Další informace najdete v části [konfigurace pro adresu IPv4 reprezentovanou jako oddíl IPv6 adresa](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) .<br><br>Výchozí hodnota je `IList` \<<xref:System.Net.IPAddress>> obsahující jednu položku pro `IPAddress.IPv6Loopback` . |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).<br><br>Výchozí formát je `X-Original-For`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).<br><br>Výchozí formát je `X-Original-Host`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Použijte hlavičku určenou touto vlastností místo od ta, kterou Určuje [ForwardedHeadersDefaults. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).<br><br>Výchozí formát je `X-Original-Proto`. |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Vyžaduje, aby byl počet synchronizovaných hodnot hlaviček mezi zpracovávaným [ForwardedHeadersOptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) .<br><br>Výchozí hodnota v ASP.NET Core 1. x je `true` . Výchozí hodnota v ASP.NET Core 2,0 nebo novější je `false` . |

## <a name="scenarios-and-use-cases"></a>Scénáře a případy použití

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a>Pokud není možné přidat předávané hlavičky a všechny požadavky jsou zabezpečené

V některých případech nemusí být možné přidat předávaná záhlaví do požadavků, které jsou proxy k aplikaci. Pokud proxy vynucuje, že všechny veřejné externí požadavky jsou HTTPS, můžete schéma ručně nastavit v části `Startup.Configure` před použitím libovolného typu middlewaru:

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

Pokud `/foo` je základní cesta aplikace pro cestu k proxy serveru předaná jako `/foo/api/1` , middleware nastaví `Request.PathBase` na `/foo` a `Request.Path` do `/api/1` pomocí následujícího příkazu:

```csharp
app.UsePathBase("/foo");
```

Původní cesta a základ cesty se znovu aplikují, když se middleware znovu zavolá v opačném případě. Další informace o zpracování pořadí middlewaru najdete v tématu <xref:fundamentals/middleware/index> .

Pokud proxy ořízne cestu (například předávání `/foo/api/1` na `/api/1` ), opravte přesměrování a odkazy nastavením vlastnosti [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) žádosti:

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

Pokud proxy přidává data cest, zahodí část cesty pro opravu přesměrování a odkazy pomocí <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> a přiřazení k <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> vlastnosti:

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

Pokud proxy nepoužívá hlavičky s názvem `X-Forwarded-For` a `X-Forwarded-Proto` k přeposlání informací o adrese proxy/portu a informace o původním schématu, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> nastavte <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> Možnosti a tak, aby odpovídaly názvům hlaviček používaným proxy serverem:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a>Konfigurace pro adresu IPv4 reprezentovanou jako IPv6 adresa

Pokud server používá sokety s duálním režimem, adresy IPv4 jsou zadány ve formátu IPv6 (například `10.0.0.1` v protokolu IPv4 reprezentovaného v protokolu IPv6 jako `::ffff:10.0.0.1` nebo `::ffff:a00:1` ). Viz [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*). V části [HttpContext. Connection. RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)Zjistěte, jestli je tento formát vyžadován.

V následujícím příkladu se síťová adresa, která poskytuje předávaná záhlaví, přidá do `KnownNetworks` seznamu ve formátu protokolu IPv6.

Adresa IPv4:`10.11.12.1/8`

Převedená adresa IPv6:`::ffff:10.11.12.1`  
Délka převedené předpony: 104

Adresu můžete také uvést v šestnáctkovém formátu ( `10.11.12.1` reprezentované protokolem IPv6 jako `::ffff:0a0b:0c01` ). Při převodu adresy IPv4 na IPv6 přidejte 96 k délce předpony CIDR ( `8` v příkladu), aby se zohlednila další `::ffff:` předpona IPv6 (8 + 96 = 104). 

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

Aplikace, které volají <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> a <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> umísťují lokalitu do nekonečné smyčky, pokud jsou nasazené do azure Linux App Service, virtuální počítač Azure Linux (VM) nebo za jakýkoli jiný reverzní proxy server, kromě služby IIS. Protokol TLS je ukončen reverzním proxy serverem a Kestrel není informován o správném schématu požadavků. OAuth a OIDC v této konfiguraci selže, protože generují nesprávná přesměrování. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>Přidá a nakonfiguruje middleware předávaných hlaviček při spuštění za IIS, ale neexistuje žádná vyhovující Automatická konfigurace pro Linux (integrace Apache nebo Nginx).

Pro přeposílání schématu od proxy serveru ve scénářích mimo službu IIS přidejte a nakonfigurujte middleware s předanými hlavičkami. V `Startup.ConfigureServices` použijte následující kód:

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

Chcete-li zapsat hlavičky do odpovědi aplikace, umístěte následující vložený middleware terminálu hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :

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

* Vložení `ILogger<Startup>` do `Startup` třídy, jak je popsáno v tématu [vytvoření protokolů při spuštění](xref:fundamentals/logging/index#create-logs-in-startup).
* Vložte následující vložený middleware hned po volání do <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> v `Startup.Configure` .

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

Při zpracování `X-Forwarded-{For|Proto|Host}` jsou hodnoty přesunuty na `X-Original-{For|Proto|Host}` . Pokud existuje více hodnot v dané hlavičce, middleware pro přesměrované hlavičky zpracovává záhlaví v opačném pořadí zprava doleva. Výchozí hodnota `ForwardLimit` je `1` (jedna), takže je zpracována pouze hodnota zprava z hlaviček, pokud hodnota `ForwardLimit` není zvýšena.

Původní Vzdálená IP adresa žádosti se musí shodovat s položkou v `KnownProxies` `KnownNetworks` seznamech nebo před zpracováním předávaných hlaviček. To omezuje falšování hlaviček tím, že nepřijímá předávací servery od nedůvěryhodných proxy serverů. Při zjištění neznámého proxy serveru se zobrazí zpráva s informacemi o adrese proxy serveru:

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

V předchozím příkladu je 10.0.0.100 proxy server. Pokud je server důvěryhodným proxy serverem, přidejte IP adresu serveru do `KnownProxies` (nebo přidejte důvěryhodnou síť do `KnownNetworks` ) v `Startup.ConfigureServices` . Další informace najdete v části [Možnosti middlewaru předávaných hlaviček](#forwarded-headers-middleware-options) .

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> Povoluje pouze důvěryhodné proxy servery a sítě pro přeposílání hlaviček. V opačném případě jsou možné útoky s [falešnou IP adresou](https://www.iplocation.net/ip-spoofing) .

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/web-farm>
* [Informační zpravodaj zabezpečení společnosti Microsoft CVE-2018-0787: ASP.NET Core zvýšení zabezpečení oprávnění](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

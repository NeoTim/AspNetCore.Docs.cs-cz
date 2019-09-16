---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: guardrex
description: Přečtěte si o Kestrel, webovém serveru pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/13/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: b1c28f084e67d9cce74258433aa0c884878c2520
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011166"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a>Implementace webového serveru Kestrel v ASP.NET Core

Tím, že [Dykstra](https://github.com/tdykstra), [Chris Rossův](https://github.com/Tratcher), [Stephen zastavili](https://twitter.com/halter73)a [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.

Kestrel podporuje následující scénáře:

* HTTPS
* Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)
* Technologie UNIX Sockets s vysokým výkonem za Nginx
* HTTP/2 (kromě macOS&dagger;)

&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.

Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>Podpora HTTP/2

[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:

* Operační systém&dagger;
  * Windows Server 2016/Windows 10 nebo novější&Dagger;
  * Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)
* Cílová architektura: .NET Core 2,2 nebo novější
* Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* Protokol TLS 1.2 nebo vyšší připojení

&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.
&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený. Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).

Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.

HTTP/2 je ve výchozím nastavení zakázané. Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kdy použít Kestrel s reverzním proxy serverem

Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.

Kestrel použitý jako hraniční webový server (internetový):

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

Kestrel se používá v konfiguraci reverzního proxy serveru:

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.

Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy. Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků. Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.

I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.

Reverzní proxy:

* Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.
* Poskytněte další vrstvu konfigurace a obrany.
* Může se lépe integrovat se stávající infrastrukturou.
* Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS). Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.

> [!WARNING]
> Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Použití Kestrel v aplikacích ASP.NET Core

ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel. V *program.cs*kód šablony volá <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Pro zajištění další konfigurace po volání `CreateDefaultBuilder` a `ConfigureWebHostDefaults`použijte `ConfigureKestrel`:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

`CreateDefaultBuilder` Pokud aplikace nevolá pro nastavení hostitele, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> zavolejte **před** voláním `ConfigureKestrel`:

```csharp
public static void Main(string[] args)
{
    var host = new HostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseIISIntegration()
            .UseStartup<Startup>();
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a>Kestrel možnosti

Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.

Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy. Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>třídy. `Limits`

V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

### <a name="keep-alive-timeout"></a>Časový limit Keep-Alive

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5). Výchozí hodnota je 2 minuty.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>Maximální počet připojení klientů

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets). Po upgradu připojení se nepočítá na `MaxConcurrentConnections` základě limitu.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Ve výchozím nastavení je maximální počet připojení neomezené (null).

### <a name="maximum-request-body-size"></a>Maximální velikost textu požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.

Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Přepsat nastavení u konkrétního požadavku v middleware:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost. Existuje vlastnost, která určuje, `MaxRequestBodySize` zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu. `IsReadOnly`

Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.

### <a name="minimum-request-body-data-rate"></a>Minimální rychlost těla zprávy požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu. Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována. Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.

Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.

Pro odpověď platí také minimální sazba. Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou `RequestBody` , že existují nebo `Response` v názvech vlastností a rozhraní.

Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Odkazovaná v předchozí ukázce není `HttpContext.Features` k dispozici pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků je obecně Nepodporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> `HttpContext.Features` `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null`Propožadavky http/2 sealestálepoužívá,protožeomezeníčetnostičtenímůžebýtpořádzcelazakázanénazákladějednotlivýchpožadavkůnastavenímnaipropožadavekHTTP/2.<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> Když se pokusíte o jeho navýšení `null` `NotSupportedException` nebopokusojehonastavenínajinouhodnotunež,vygenerujesemupožadavek`IHttpMinRequestBodyDataRateFeature.MinDataRate` http/2.

Omezení přenosové rychlosti pro všechny servery `KestrelServerOptions.Limits` jsou nakonfigurovaná přes protokol HTTP/1. x i pro připojení HTTP/2.

### <a name="request-headers-timeout"></a>Časový limit hlaviček žádostí

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků. Výchozí hodnota je 30 sekund.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a>Maximální počet proudů na připojení

`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2. Nadbytečné proudy jsou odmítnuty.

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

Výchozí hodnota je 100.

### <a name="header-table-size"></a>Velikost tabulky hlaviček

Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2. `Http2.HeaderTableSize`omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK. Hodnota je uvedena v oktetech a musí být větší než nula (0).

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

Výchozí hodnota je 4096.

### <a name="maximum-frame-size"></a>Maximální velikost rámce

`Http2.MaxFrameSize`Určuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijatého nebo odesílaného serverem. Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

Výchozí hodnota je 2 ^ 14 (16 384).

### <a name="maximum-request-header-size"></a>Maximální velikost hlavičky požadavku

`Http2.MaxRequestHeaderFieldSize`Určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku. Toto omezení se vztahuje na název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentujcích. Hodnota musí být větší než nula (0).

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

Výchozí hodnota je 8 192.

### <a name="initial-connection-window-size"></a>Velikost okna počátečního připojení

`Http2.InitialConnectionWindowSize`Určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení. Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`. Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

Výchozí hodnota je 128 KB (131 072).

### <a name="initial-stream-window-size"></a>Počáteční velikost okna datového proudu

`Http2.InitialStreamWindowSize`Určuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě. Žádosti jsou také omezené pomocí `Http2.InitialConnectionWindowSize`. Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

Výchozí hodnota je 96 KB (98 304).

### <a name="synchronous-io"></a>Synchronní v/v

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, zda je pro požadavek a odpověď povolena synchronní v/v. Výchozí hodnota je `false`.

> [!WARNING]
> Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje. Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní v/v.

Následující příklad povoluje synchronní vstupně-výstupní operace:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Informace o dalších možnostech a omezeních Kestrel najdete v tématech:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Ve výchozím nastavení ASP.NET Core váže k:

* `http://localhost:5000`
* `https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)

Zadejte adresy URL pomocí:

* `ASPNETCORE_URLS` proměnné prostředí.
* `--urls`argument příkazového řádku.
* `urls`konfigurační klíč hostitele.
* `UseUrls`Metoda rozšíření

Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).

Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).

Vytvoří se vývojový certifikát:

* Po instalaci [.NET Core SDK](/dotnet/core/sdk) .
* [Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.

Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).

Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pro konfiguraci předpon adres URL a portů pro Kestrel.

`UseUrls`, argument `urls` `ASPNETCORE_URLS` příkazového řádku, konfigurační klíč hostitele a proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro koncový bod HTTPS musí být dostupný výchozí certifikát.) `--urls` konfigurace).

`KestrelServerOptions`rozšířeného

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (akce\<ListenOptions >)

Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod. Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                serverOptions.ConfigureEndpointDefaults(listenOptions =>
                {
                    // Configure endpoint defaults
                });
            })
            .UseStartup<Startup>();
        });
}
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (akce\<HttpsConnectionAdapterOptions >)

Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS. Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                serverOptions.ConfigureHttpsDefaults(listenOptions =>
                {
                    // certificate is an X509Certificate2
                    listenOptions.ServerCertificate = certificate;
                });
            })
            .UseStartup<Startup>();
        });
}
```

### <a name="configureiconfiguration"></a>Konfigurace (IConfiguration)

Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup. Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.

`ListenOptions.UseHttps`SND

* `UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps`ukazatelů

* `filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.
* `password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.
* `configureOptions``Action` je ke`HttpsConnectionAdapterOptions`konfiguraci. `ListenOptions`Vrátí.
* `storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.
* `subject`je název subjektu certifikátu.
* `allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.
* `location`je umístěním úložiště, ze kterého se má certifikát načíst.
* `serverCertificate`je certifikát X. 509.

V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný. Musí být zadán minimálně výchozí certifikát.

Podporované konfigurace popsané dál:

* Žádná konfigurace
* Nahradit výchozí certifikát z konfigurace
* Změna výchozích hodnot v kódu

*Žádná konfigurace*

Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).

<a name="configuration"></a>

*Nahradit výchozí certifikát z konfigurace*

`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá. Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS. Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.

V následujícím příkladu *appSettings. JSON* :

* Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).
* Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů. Například > **výchozí** certifikát může být zadán jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Poznámky ke schématu:

* V názvech koncových bodů se nerozlišují malá a velká písmena. Například `HTTPS` a`Https` jsou platné.
* U každého koncového bodu je vyžadován parametr.`Url` Formát pro tento parametr je stejný jako konfigurační parametr nejvyšší úrovně `Urls` s tím rozdílem, že je omezen na jedinou hodnotu.
* Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů. Koncové body definované v kódu `Listen` prostřednictvím jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.
* `Certificate` Oddíl je nepovinný. Pokud není `Certificate` oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.
* &ndash;&ndash; Oddíl podporuje jak heslo cesty, tak certifikáty úložiště subjektu. `Certificate`
* V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))``KestrelConfigurationLoader` vrátí metodu`.Endpoint(string name, listenOptions => { })` s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseKestrel((context, serverOptions) =>
            {
                serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                    .Endpoint("HTTPS", listenOptions =>
                    {
                        listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                    });
            });
        });
```

`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ten, který poskytuje.

* Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.
* Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem. Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích. Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.
* `KestrelConfigurationLoader`zrcadlí `KestrelServerOptions` `Endpoint` rodinu rozhraní API z as přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. `Listen` Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                serverOptions.ConfigureEndpointDefaults(listenOptions =>
                {
                    // Configure endpoint defaults
                });

                serverOptions.ConfigureHttpsDefaults(listenOptions =>
                {
                    listenOptions.SslProtocols = SslProtocols.Tls12;
                });
            });
        });
```

*Podpora Kestrel pro SNI*

[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu. Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát. Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.

Kestrel podporuje sni prostřednictvím `ServerCertificateSelector` zpětného volání. Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším. V `net461` systému nebo novějším je zpětné volání vyvoláno, `null` `name` ale je vždy. `name` Je také`null` v případě, že klient v TLS handshake neposkytne parametr názvu hostitele.
* Všechny weby běží na stejné instanci Kestrel. Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                serverOptions.ListenAnyIP(5005, listenOptions =>
                {
                    listenOptions.UseHttps(httpsOptions =>
                    {
                        var localhostCert = CertificateLoader.LoadFromStoreCert(
                            "localhost", "My", StoreLocation.CurrentUser,
                            allowInvalid: true);
                        var exampleCert = CertificateLoader.LoadFromStoreCert(
                            "example.com", "My", StoreLocation.CurrentUser,
                            allowInvalid: true);
                        var subExampleCert = CertificateLoader.LoadFromStoreCert(
                            "sub.example.com", "My", StoreLocation.CurrentUser,
                            allowInvalid: true);
                        var certs = new Dictionary<string, X509Certificate2>(
                            StringComparer.OrdinalIgnoreCase);
                        certs["localhost"] = localhostCert;
                        certs["example.com"] = exampleCert;
                        certs["sub.example.com"] = subExampleCert;
    
                        httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                        {
                            if (name != null && certs.TryGetValue(name, out var cert))
                            {
                                return cert;
                            }
    
                            return exampleCert;
                        };
                    });
                });
            })
            .UseStartup<Startup>();
        });
```

### <a name="bind-to-a-tcp-socket"></a>Vytvoření vazby na soket TCP

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vytvoření vazby na soket UNIX

Naslouchat na soketu <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> se systémem UNIX, aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a>Port 0

Po zadání čísla `0` portu se Kestrel dynamicky váže k dostupnému portu. Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Omezení

Konfigurace koncových bodů pomocí následujících přístupů:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`argument příkazového řádku
* `urls`konfigurační klíč hostitele
* `ASPNETCORE_URLS`Proměnná prostředí

Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel. Mějte ale na paměti následující omezení:

* Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například `KestrelServerOptions` pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při současném `Listen` `UseUrls` použitíobou`UseUrls` přístupů a koncovýchbodůsekoncovébodypřepisují.`Listen`

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls`. Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .

### <a name="listenoptionsprotocols"></a>ListenOptions. Protocols

Vlastnost určuje protokoly HTTP (`HttpProtocols`) povolené pro koncový bod připojení nebo pro server. `Protocols` Přiřaďte hodnotu `Protocols` vlastnosti `HttpProtocols` z výčtu.

| `HttpProtocols`hodnota výčtu | Povolený protokol připojení |
| -------------------------- | ----------------------------- |
| `Http1`                    | Pouze HTTP/1.1. Dá se použít s TLS nebo bez něj. |
| `Http2`                    | Pouze HTTP/2. Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 a HTTP/2. HTTP/2 vyžaduje, aby klient v rámci metody handshake protokolu TLS [(ALPN)](https://tools.ietf.org/html/rfc7301#section-3) vybral http/2. v opačném případě se připojení nastaví jako výchozí HTTP/1.1. |

Výchozí `ListenOptions.Protocols` hodnota pro libovolný koncový bod je `HttpProtocols.Http1AndHttp2`.

Omezení TLS pro HTTP/2:

* TLS verze 1,2 nebo novější
* Opětovné vyjednávání je zakázané.
* Komprese vypnuta
* Minimální velikosti dočasného výměny klíčů:
  * Eliptická křivka Diffie-Hellman (ECDH &lbrack;) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bity minima
  * Omezené pole Diffie- &lbrack; Hellman (DHE) `TLS12` &rbrack; &ndash; 2048 bity minima
* Šifrovací sada není zakázaná.

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; `FIPS186` &rbrack; ve výchozím nastavení je podporována eliptická křivka P-256. &lbrack; `TLS-ECDHE` &rbrack;

Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000. Připojení TLS zabezpečuje pomocí zadaného certifikátu:

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

Volitelně můžete pomocí middleware připojení filtrovat handshake TLS na jednotlivých připojeních pro konkrétní šifry:

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseConnectionHandler<TlsFilterConnectionHandler>();
    });
});
```

```csharp
using System;
using System.Buffers;
using System.Security.Authentication;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Connections;
using Microsoft.AspNetCore.Connections.Features;

public class TlsFilterConnectionHandler : ConnectionHandler
{
    public override async Task OnConnectedAsync(ConnectionContext connection)
    {
        var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // A ITlsHandshakeFeature.CipherAlgorithm of CipherAlgorithmType.Null
        // indicates that no cipher algorithm supported by Kestrel matches the
        // requested algorithm(s).
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        while (true)
        {
            var result = await connection.Transport.Input.ReadAsync();
            var buffer = result.Buffer;

            if (!buffer.IsEmpty)
            {
                await connection.Transport.Output.WriteAsync(buffer.ToArray());
            }
            else if (result.IsCompleted)
            {
                break;
            }

            connection.Transport.Input.AdvanceTo(buffer.End);
        }
    }
}
```

*Nastavit protokol z konfigurace*

`CreateDefaultBuilder`Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.

Následující příklad *appSettings. JSON* vytvoří HTTP/1.1 jako výchozí protokol připojení pro všechny koncové body:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

Následující příklad *appSettings. JSON* vytvoří protokol připojení HTTP/1.1 pro konkrétní koncový bod:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace

## <a name="transport-configuration"></a>Konfigurace přenosu

Pro projekty, které vyžadují použití Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):

* Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> Zavolat`IWebHostBuilder`na:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a>Předpony adresy URL

Při použití `UseUrls`, `--urls` argumentu příkazového řádku `urls` , konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.

Platné jsou pouze předpony adresy URL protokolu HTTP. Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`je ekvivalentem protokolu IPv4 `0.0.0.0`pro protokol IPv6.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy `*`hostitelů, a `+`nejsou speciální. Cokoli se nerozpoznalo jako platná IP adresa `localhost` nebo se váže ke všem IP adresám IPv4 a IPv6. Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

* Název `localhost` hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo. Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.

## <a name="host-filtering"></a>Filtrování hostitele

I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000`, jako je, Kestrel převážně ignoruje název hostitele. Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky. Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy. `Host`hlavičky nejsou ověřené.

Jako alternativní řešení použijte middleware pro filtrování hostitele. Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je implicitně k dispozici pro ASP.NET Core aplikace. Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, `AllowedHosts` Definujte klíč v souboru *appSettings. JSON*/ *\< appSettings. Environment >. JSON*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> také možnost. Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné `Host` v případě, že hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení. Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo `Host` když se hlavička přímo přepošle.
>
> Další informace o middlewaru předávaných hlaviček najdete <xref:host-and-deploy/proxy-load-balancer>v tématu.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.

Kestrel podporuje následující scénáře:

* HTTPS
* Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)
* Technologie UNIX Sockets s vysokým výkonem za Nginx
* HTTP/2 (kromě macOS&dagger;)

&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.

Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>Podpora HTTP/2

[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:

* Operační systém&dagger;
  * Windows Server 2016/Windows 10 nebo novější&Dagger;
  * Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)
* Cílová architektura: .NET Core 2,2 nebo novější
* Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* Protokol TLS 1.2 nebo vyšší připojení

&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.
&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený. Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).

Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.

HTTP/2 je ve výchozím nastavení zakázané. Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kdy použít Kestrel s reverzním proxy serverem

Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.

Kestrel použitý jako hraniční webový server (internetový):

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

Kestrel se používá v konfiguraci reverzního proxy serveru:

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.

Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy. Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků. Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.

I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.

Reverzní proxy:

* Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.
* Poskytněte další vrstvu konfigurace a obrany.
* Může se lépe integrovat se stávající infrastrukturou.
* Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS). Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.

> [!WARNING]
> Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Použití Kestrel v aplikacích ASP.NET Core

Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel. V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Chcete-li po volání `CreateDefaultBuilder`poskytnout další konfiguraci, použijte: `ConfigureKestrel`

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

`CreateDefaultBuilder` Pokud aplikace nevolá pro nastavení hostitele, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> zavolejte **před** voláním `ConfigureKestrel`:

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a>Kestrel možnosti

Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.

Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy. Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>třídy. `Limits`

V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

### <a name="keep-alive-timeout"></a>Časový limit Keep-Alive

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5). Výchozí hodnota je 2 minuty.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a>Maximální počet připojení klientů

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets). Po upgradu připojení se nepočítá na `MaxConcurrentConnections` základě limitu.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Ve výchozím nastavení je maximální počet připojení neomezené (null).

### <a name="maximum-request-body-size"></a>Maximální velikost textu požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.

Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Přepsat nastavení u konkrétního požadavku v middleware:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost. Existuje vlastnost, která určuje, `MaxRequestBodySize` zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu. `IsReadOnly`

Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.

### <a name="minimum-request-body-data-rate"></a>Minimální rychlost těla zprávy požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu. Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována. Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.

Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.

Pro odpověď platí také minimální sazba. Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou `RequestBody` , že existují nebo `Response` v názvech vlastností a rozhraní.

Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Pro požadavky HTTP/2 se v předchozí ukázce neodkazuje žádná míra funkce, `HttpContext.Features` protože Změna omezení přenosové rychlosti na základě jednotlivých požadavků není podporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků. Omezení přenosové rychlosti pro všechny servery `KestrelServerOptions.Limits` jsou nakonfigurovaná přes protokol HTTP/1. x i pro připojení HTTP/2.

### <a name="request-headers-timeout"></a>Časový limit hlaviček žádostí

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků. Výchozí hodnota je 30 sekund.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a>Maximální počet proudů na připojení

`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2. Nadbytečné proudy jsou odmítnuty.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

Výchozí hodnota je 100.

### <a name="header-table-size"></a>Velikost tabulky hlaviček

Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2. `Http2.HeaderTableSize`omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK. Hodnota je uvedena v oktetech a musí být větší než nula (0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

Výchozí hodnota je 4096.

### <a name="maximum-frame-size"></a>Maximální velikost rámce

`Http2.MaxFrameSize`Určuje maximální velikost datové části rámce připojení HTTP/2, která se má přijmout. Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

Výchozí hodnota je 2 ^ 14 (16 384).

### <a name="maximum-request-header-size"></a>Maximální velikost hlavičky požadavku

`Http2.MaxRequestHeaderFieldSize`Určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku. Toto omezení platí pro názvy i hodnoty společně v komprimovaných a nekomprimovaných reprezentujcích. Hodnota musí být větší než nula (0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

Výchozí hodnota je 8 192.

### <a name="initial-connection-window-size"></a>Velikost okna počátečního připojení

`Http2.InitialConnectionWindowSize`Určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení. Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`. Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

Výchozí hodnota je 128 KB (131 072).

### <a name="initial-stream-window-size"></a>Počáteční velikost okna datového proudu

`Http2.InitialStreamWindowSize`Určuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě. Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`. Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

Výchozí hodnota je 96 KB (98 304).

### <a name="synchronous-io"></a>Synchronní v/v

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, zda je pro požadavek a odpověď povolena synchronní v/v. Výchozí hodnota je `true`.

> [!WARNING]
> Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje. Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní v/v.

Následující příklad povoluje synchronní vstupně-výstupní operace:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Informace o dalších možnostech a omezeních Kestrel najdete v tématech:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Ve výchozím nastavení ASP.NET Core váže k:

* `http://localhost:5000`
* `https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)

Zadejte adresy URL pomocí:

* `ASPNETCORE_URLS` proměnné prostředí.
* `--urls`argument příkazového řádku.
* `urls`konfigurační klíč hostitele.
* `UseUrls`Metoda rozšíření

Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).

Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).

Vytvoří se vývojový certifikát:

* Po instalaci [.NET Core SDK](/dotnet/core/sdk) .
* [Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.

Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).

Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pro konfiguraci předpon adres URL a portů pro Kestrel.

`UseUrls`, argument `urls` `ASPNETCORE_URLS` příkazového řádku, konfigurační klíč hostitele a proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro koncový bod HTTPS musí být dostupný výchozí certifikát.) `--urls` konfigurace).

`KestrelServerOptions`rozšířeného

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (akce\<ListenOptions >)

Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod. Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (akce\<HttpsConnectionAdapterOptions >)

Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS. Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

### <a name="configureiconfiguration"></a>Konfigurace (IConfiguration)

Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup. Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.

`ListenOptions.UseHttps`SND

* `UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps`ukazatelů

* `filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.
* `password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.
* `configureOptions``Action` je ke`HttpsConnectionAdapterOptions`konfiguraci. `ListenOptions`Vrátí.
* `storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.
* `subject`je název subjektu certifikátu.
* `allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.
* `location`je umístěním úložiště, ze kterého se má certifikát načíst.
* `serverCertificate`je certifikát X. 509.

V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný. Musí být zadán minimálně výchozí certifikát.

Podporované konfigurace popsané dál:

* Žádná konfigurace
* Nahradit výchozí certifikát z konfigurace
* Změna výchozích hodnot v kódu

*Žádná konfigurace*

Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).

<a name="configuration"></a>

*Nahradit výchozí certifikát z konfigurace*

`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá. Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS. Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.

V následujícím příkladu *appSettings. JSON* :

* Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).
* Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů. Například > **výchozí** certifikát může být zadán jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Poznámky ke schématu:

* V názvech koncových bodů se nerozlišují malá a velká písmena. Například `HTTPS` a`Https` jsou platné.
* U každého koncového bodu je vyžadován parametr.`Url` Formát pro tento parametr je stejný jako konfigurační parametr nejvyšší úrovně `Urls` s tím rozdílem, že je omezen na jedinou hodnotu.
* Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů. Koncové body definované v kódu `Listen` prostřednictvím jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.
* `Certificate` Oddíl je nepovinný. Pokud není `Certificate` oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.
* &ndash;&ndash; Oddíl podporuje jak heslo cesty, tak certifikáty úložiště subjektu. `Certificate`
* V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))``KestrelConfigurationLoader` vrátí metodu`.Endpoint(string name, listenOptions => { })` s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ten, který poskytuje.

* Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.
* Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem. Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích. Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.
* `KestrelConfigurationLoader`zrcadlí `KestrelServerOptions` `Endpoint` rodinu rozhraní API z as přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. `Listen` Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

*Podpora Kestrel pro SNI*

[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu. Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát. Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.

Kestrel podporuje sni prostřednictvím `ServerCertificateSelector` zpětného volání. Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším. V `net461` systému nebo novějším je zpětné volání vyvoláno, `null` `name` ale je vždy. `name` Je také`null` v případě, že klient v TLS handshake neposkytne parametr názvu hostitele.
* Všechny weby běží na stejné instanci Kestrel. Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="bind-to-a-tcp-socket"></a>Vytvoření vazby na soket TCP

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vytvoření vazby na soket UNIX

Naslouchat na soketu <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> se systémem UNIX, aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a>Port 0

Po zadání čísla `0` portu se Kestrel dynamicky váže k dostupnému portu. Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Omezení

Konfigurace koncových bodů pomocí následujících přístupů:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`argument příkazového řádku
* `urls`konfigurační klíč hostitele
* `ASPNETCORE_URLS`Proměnná prostředí

Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel. Mějte ale na paměti následující omezení:

* Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například `KestrelServerOptions` pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při současném `Listen` `UseUrls` použitíobou`UseUrls` přístupů a koncovýchbodůsekoncovébodypřepisují.`Listen`

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls`. Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .

### <a name="listenoptionsprotocols"></a>ListenOptions. Protocols

Vlastnost určuje protokoly HTTP (`HttpProtocols`) povolené pro koncový bod připojení nebo pro server. `Protocols` Přiřaďte hodnotu `Protocols` vlastnosti `HttpProtocols` z výčtu.

| `HttpProtocols`hodnota výčtu | Povolený protokol připojení |
| -------------------------- | ----------------------------- |
| `Http1`                    | Pouze HTTP/1.1. Dá se použít s TLS nebo bez něj. |
| `Http2`                    | Pouze HTTP/2. Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 a HTTP/2. HTTP/2 vyžaduje připojení TLS a [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ; v opačném případě se připojení nastaví jako výchozí HTTP/1.1. |

Výchozí protokol HTTP/1.1.

Omezení TLS pro HTTP/2:

* TLS verze 1,2 nebo novější
* Opětovné vyjednávání je zakázané.
* Komprese vypnuta
* Minimální velikosti dočasného výměny klíčů:
  * Eliptická křivka Diffie-Hellman (ECDH &lbrack;) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bity minima
  * Omezené pole Diffie- &lbrack; Hellman (DHE) `TLS12` &rbrack; &ndash; 2048 bity minima
* Šifrovací sada není zakázaná.

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; `FIPS186` &rbrack; ve výchozím nastavení je podporována eliptická křivka P-256. &lbrack; `TLS-ECDHE` &rbrack;

Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000. Připojení TLS zabezpečuje pomocí zadaného certifikátu:

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

Volitelně můžete vytvořit `IConnectionAdapter` implementaci pro filtrování ověřování TLS metodou handshake na základě jednotlivých připojení pro konkrétní šifry:

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // A ITlsHandshakeFeature.CipherAlgorithm of CipherAlgorithmType.Null
        // indicates that no cipher algorithm supported by Kestrel matches the
        // requested algorithm(s).
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

*Nastavit protokol z konfigurace*

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.

V následujícím příkladu *appSettings. JSON* se pro všechny koncové body Kestrel vytvoří výchozí protokol připojení (http/1.1 a HTTP/2):

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

Následující příklad konfiguračního souboru vytvoří protokol připojení pro konkrétní koncový bod:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace

## <a name="transport-configuration"></a>Konfigurace přenosu

S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů. Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> které volá a závisí na jednom z následujících balíčků:

* [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Pro projekty, které vyžadují použití Libuv:

* Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a>Předpony adresy URL

Při použití `UseUrls`, `--urls` argumentu příkazového řádku `urls` , konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.

Platné jsou pouze předpony adresy URL protokolu HTTP. Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`je ekvivalentem protokolu IPv4 `0.0.0.0`pro protokol IPv6.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy `*`hostitelů, a `+`nejsou speciální. Cokoli se nerozpoznalo jako platná IP adresa `localhost` nebo se váže ke všem IP adresám IPv4 a IPv6. Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

* Název `localhost` hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo. Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.

## <a name="host-filtering"></a>Filtrování hostitele

I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000`, jako je, Kestrel převážně ignoruje název hostitele. Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky. Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy. `Host`hlavičky nejsou ověřené.

Jako alternativní řešení použijte middleware pro filtrování hostitele. Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2). Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, `AllowedHosts` Definujte klíč v souboru *appSettings. JSON*/ *\< appSettings. Environment >. JSON*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> také možnost. Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné `Host` v případě, že hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení. Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo `Host` když se hlavička přímo přepošle.
>
> Další informace o middlewaru předávaných hlaviček najdete <xref:host-and-deploy/proxy-load-balancer>v tématu.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.

Kestrel podporuje následující scénáře:

* HTTPS
* Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)
* Technologie UNIX Sockets s vysokým výkonem za Nginx

Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kdy použít Kestrel s reverzním proxy serverem

Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.

Kestrel použitý jako hraniční webový server (internetový):

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

Kestrel se používá v konfiguraci reverzního proxy serveru:

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.

Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy. Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků. Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.

I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.

Reverzní proxy:

* Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.
* Poskytněte další vrstvu konfigurace a obrany.
* Může se lépe integrovat se stávající infrastrukturou.
* Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS). Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.

> [!WARNING]
> Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Použití Kestrel v aplikacích ASP.NET Core

Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel. V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.

Chcete-li po volání `CreateDefaultBuilder`poskytnout další konfiguraci, zavolejte: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

## <a name="kestrel-options"></a>Kestrel možnosti

Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.

Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy. Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>třídy. `Limits`

V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

### <a name="keep-alive-timeout"></a>Časový limit Keep-Alive

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5). Výchozí hodnota je 2 minuty.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a>Maximální počet připojení klientů

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets). Po upgradu připojení se nepočítá na `MaxConcurrentConnections` základě limitu.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

Ve výchozím nastavení je maximální počet připojení neomezené (null).

### <a name="maximum-request-body-size"></a>Maximální velikost textu požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.

Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

Přepsat nastavení u konkrétního požadavku v middleware:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost. Existuje vlastnost, která určuje, `MaxRequestBodySize` zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu. `IsReadOnly`

Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.

### <a name="minimum-request-body-data-rate"></a>Minimální rychlost těla zprávy požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu. Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována. Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.

Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.

Pro odpověď platí také minimální sazba. Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou `RequestBody` , že existují nebo `Response` v názvech vlastností a rozhraní.

Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a>Časový limit hlaviček žádostí

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků. Výchozí hodnota je 30 sekund.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a>Synchronní v/v

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, zda je pro požadavek a odpověď povolena synchronní v/v. Výchozí hodnota je `true`.

> [!WARNING]
> Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje. Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní v/v.

Následující příklad zakáže synchronní v/v:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

Informace o dalších možnostech a omezeních Kestrel najdete v tématech:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Ve výchozím nastavení ASP.NET Core váže k:

* `http://localhost:5000`
* `https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)

Zadejte adresy URL pomocí:

* `ASPNETCORE_URLS` proměnné prostředí.
* `--urls`argument příkazového řádku.
* `urls`konfigurační klíč hostitele.
* `UseUrls`Metoda rozšíření

Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).

Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).

Vytvoří se vývojový certifikát:

* Po instalaci [.NET Core SDK](/dotnet/core/sdk) .
* [Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.

Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).

Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pro konfiguraci předpon adres URL a portů pro Kestrel.

`UseUrls`, argument `urls` `ASPNETCORE_URLS` příkazového řádku, konfigurační klíč hostitele a proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro koncový bod HTTPS musí být dostupný výchozí certifikát.) `--urls` konfigurace).

`KestrelServerOptions`rozšířeného

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (akce\<ListenOptions >)

Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod. Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (akce\<HttpsConnectionAdapterOptions >)

Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS. Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

### <a name="configureiconfiguration"></a>Konfigurace (IConfiguration)

Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup. Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.

`ListenOptions.UseHttps`SND

* `UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps`ukazatelů

* `filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.
* `password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.
* `configureOptions``Action` je ke`HttpsConnectionAdapterOptions`konfiguraci. `ListenOptions`Vrátí.
* `storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.
* `subject`je název subjektu certifikátu.
* `allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.
* `location`je umístěním úložiště, ze kterého se má certifikát načíst.
* `serverCertificate`je certifikát X. 509.

V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný. Musí být zadán minimálně výchozí certifikát.

Podporované konfigurace popsané dál:

* Žádná konfigurace
* Nahradit výchozí certifikát z konfigurace
* Změna výchozích hodnot v kódu

*Žádná konfigurace*

Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).

<a name="configuration"></a>

*Nahradit výchozí certifikát z konfigurace*

`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá. Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS. Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.

V následujícím příkladu *appSettings. JSON* :

* Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).
* Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů. Například > **výchozí** certifikát může být zadán jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Poznámky ke schématu:

* V názvech koncových bodů se nerozlišují malá a velká písmena. Například `HTTPS` a`Https` jsou platné.
* U každého koncového bodu je vyžadován parametr.`Url` Formát pro tento parametr je stejný jako konfigurační parametr nejvyšší úrovně `Urls` s tím rozdílem, že je omezen na jedinou hodnotu.
* Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů. Koncové body definované v kódu `Listen` prostřednictvím jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.
* `Certificate` Oddíl je nepovinný. Pokud není `Certificate` oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.
* &ndash;&ndash; Oddíl podporuje jak heslo cesty, tak certifikáty úložiště subjektu. `Certificate`
* V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))``KestrelConfigurationLoader` vrátí metodu`.Endpoint(string name, listenOptions => { })` s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ten, který poskytuje.

* Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.
* Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem. Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích. Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.
* `KestrelConfigurationLoader`zrcadlí `KestrelServerOptions` `Endpoint` rodinu rozhraní API z as přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. `Listen` Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

*Podpora Kestrel pro SNI*

[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu. Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát. Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.

Kestrel podporuje sni prostřednictvím `ServerCertificateSelector` zpětného volání. Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším. V `net461` systému nebo novějším je zpětné volání vyvoláno, `null` `name` ale je vždy. `name` Je také`null` v případě, že klient v TLS handshake neposkytne parametr názvu hostitele.
* Všechny weby běží na stejné instanci Kestrel. Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="bind-to-a-tcp-socket"></a>Vytvoření vazby na soket TCP

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vytvoření vazby na soket UNIX

Naslouchat na soketu <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> se systémem UNIX, aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

### <a name="port-0"></a>Port 0

Po zadání čísla `0` portu se Kestrel dynamicky váže k dostupnému portu. Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Omezení

Konfigurace koncových bodů pomocí následujících přístupů:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`argument příkazového řádku
* `urls`konfigurační klíč hostitele
* `ASPNETCORE_URLS`Proměnná prostředí

Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel. Mějte ale na paměti následující omezení:

* Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například `KestrelServerOptions` pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při současném `Listen` `UseUrls` použitíobou`UseUrls` přístupů a koncovýchbodůsekoncovébodypřepisují.`Listen`

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls`. Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .

## <a name="transport-configuration"></a>Konfigurace přenosu

S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů. Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> které volá a závisí na jednom z následujících balíčků:

* [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Pro projekty, které vyžadují použití Libuv:

* Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a>Předpony adresy URL

Při použití `UseUrls`, `--urls` argumentu příkazového řádku `urls` , konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.

Platné jsou pouze předpony adresy URL protokolu HTTP. Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`je ekvivalentem protokolu IPv4 `0.0.0.0`pro protokol IPv6.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy `*`hostitelů, a `+`nejsou speciální. Cokoli se nerozpoznalo jako platná IP adresa `localhost` nebo se váže ke všem IP adresám IPv4 a IPv6. Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

* Název `localhost` hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo. Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.

## <a name="host-filtering"></a>Filtrování hostitele

I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000`, jako je, Kestrel převážně ignoruje název hostitele. Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky. Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy. `Host`hlavičky nejsou ověřené.

Jako alternativní řešení použijte middleware pro filtrování hostitele. Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2). Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, `AllowedHosts` Definujte klíč v souboru *appSettings. JSON*/ *\< appSettings. Environment >. JSON*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> také možnost. Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné `Host` v případě, že hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení. Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo `Host` když se hlavička přímo přepošle.
>
> Další informace o middlewaru předávaných hlaviček najdete <xref:host-and-deploy/proxy-load-balancer>v tématu.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [RFC 7230: Syntaxe zprávy a směrování (oddíl 5,4: Provoz](https://tools.ietf.org/html/rfc7230#section-5.4)

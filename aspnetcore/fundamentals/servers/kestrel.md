---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: guardrex
description: Přečtěte si o Kestrel, webovém serveru pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: beaf6ac49359adfdc2dc24221eab04cc853646a9
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143441"
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

&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.

Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>Podpora HTTP/2

[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:

* &dagger; operačního systému
  * Windows Server 2016/Windows 10 nebo novější&Dagger;
  * Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)
* Cílová architektura: .NET Core 2,2 nebo novější
* Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* Připojení TLS 1,2 nebo novější

&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.
&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený. Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).

Pokud je navázáno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.

HTTP/2 je ve výchozím nastavení zakázané. Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kdy použít Kestrel s reverzním proxy serverem

Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.

Kestrel použitý jako hraniční webový server (internetový):

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

Kestrel se používá v konfiguraci reverzního proxy serveru:

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.

Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy. Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`. Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.

I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.

Reverzní proxy:

* Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.
* Poskytněte další vrstvu konfigurace a obrany.
* Může se lépe integrovat se stávající infrastrukturou.
* Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS). Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.

> [!WARNING]
> Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Použití Kestrel v aplikacích ASP.NET Core

ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel. V *program.cs*kód šablony volá <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Další informace o `CreateDefaultBuilder` a vytváření hostitele najdete v oddílech nastavení <xref:fundamentals/host/generic-host#set-up-a-host>nastavení *hostitele* a *výchozího tvůrce* .

Pokud chcete po volání `CreateDefaultBuilder` a `ConfigureWebHostDefaults` zadat další konfiguraci, použijte `ConfigureKestrel`:

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

Pokud aplikace nevolá `CreateDefaultBuilder` pro nastavení hostitele, **před** voláním `ConfigureKestrel` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:

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

Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>. Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.

V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index). Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

Použijte **jeden** z následujících přístupů:

* Konfigurace Kestrel v `Startup.ConfigureServices`:

  1. Vloží instanci `IConfiguration` do třídy `Startup`. Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.
  2. V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* Nakonfigurovat Kestrel při sestavování hostitele:

  V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).

### <a name="keep-alive-timeout"></a>Časový limit Keep-Alive

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5). Výchozí hodnota je 2 minuty.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>Maximální počet připojení klientů

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets). Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Ve výchozím nastavení je maximální počet připojení neomezené (null).

### <a name="maximum-request-body-size"></a>Maximální velikost textu požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.

Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Přepsat nastavení u konkrétního požadavku v middleware:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost. Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.

Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.

### <a name="minimum-request-body-data-rate"></a>Minimální rychlost těla zprávy požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu. Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována. Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.

Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.

Pro odpověď platí také minimální sazba. Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.

Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>, na které se odkazuje v předchozí ukázce, není k dispozici v `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků je obecně Nepodporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> však stále existují `HttpContext.Features` požadavků HTTP/2, protože omezení četnosti čtení je stále možné *úplně zakázat* na základě jednotlivých požadavků nastavením `IHttpMinRequestBodyDataRateFeature.MinDataRate` na `null` i pro požadavek HTTP/2. Při pokusu o čtení `IHttpMinRequestBodyDataRateFeature.MinDataRate` nebo pokusu o její nastavení na jinou hodnotu než `null` bude výsledkem `NotSupportedException` vyjímka požadavku HTTP/2.

Omezení přenosové rychlosti pro všechny servery nakonfigurovaná přes `KestrelServerOptions.Limits` se stále vztahují na připojení HTTP/1. x i HTTP/2.

### <a name="request-headers-timeout"></a>Časový limit hlaviček žádostí

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků. Výchozí hodnota je 30 sekund.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a>Maximální počet proudů na připojení

`Http2.MaxStreamsPerConnection` omezuje počet souběžných proudů požadavků na připojení HTTP/2. Nadbytečné proudy jsou odmítnuty.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

Výchozí hodnota je 100.

### <a name="header-table-size"></a>Velikost tabulky hlaviček

Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2. `Http2.HeaderTableSize` omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK. Hodnota je uvedena v oktetech a musí být větší než nula (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

Výchozí hodnota je 4096.

### <a name="maximum-frame-size"></a>Maximální velikost rámce

`Http2.MaxFrameSize` označuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijatého nebo odesílaného serverem. Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

Výchozí hodnota je 2 ^ 14 (16 384).

### <a name="maximum-request-header-size"></a>Maximální velikost hlavičky požadavku

`Http2.MaxRequestHeaderFieldSize` označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku. Toto omezení se vztahuje na název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentujcích. Hodnota musí být větší než nula (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

Výchozí hodnota je 8 192.

### <a name="initial-connection-window-size"></a>Velikost okna počátečního připojení

`Http2.InitialConnectionWindowSize` označuje maximální velikost textu žádosti v bajtech. vyrovnávací paměti serveru se v jednom okamžiku agreguje napříč všemi požadavky (datovými proudy) na připojení. Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`. Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

Výchozí hodnota je 128 KB (131 072).

### <a name="initial-stream-window-size"></a>Počáteční velikost okna datového proudu

`Http2.InitialStreamWindowSize` označuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě. Žádosti jsou také omezené pomocí `Http2.InitialConnectionWindowSize`. Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

Výchozí hodnota je 96 KB (98 304).

### <a name="synchronous-io"></a>Synchronní v/v

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v. Výchozí hodnota je `false`.

> [!WARNING]
> Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje. Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.

Následující příklad povoluje synchronní vstupně-výstupní operace:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Informace o dalších možnostech a omezeních Kestrel najdete v tématech:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Ve výchozím nastavení ASP.NET Core váže k:

* `http://localhost:5000`
* `https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)

Zadejte adresy URL pomocí:

* `ASPNETCORE_URLS` proměnnou prostředí.
* argument příkazového řádku `--urls`
* `urls` konfigurační klíč hostitele.
* Metoda rozšíření `UseUrls`.

Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).

Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).

Vytvoří se vývojový certifikát:

* Po instalaci [.NET Core SDK](/dotnet/core/sdk) .
* [Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.

Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).

Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.

`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).

Konfigurace `KestrelServerOptions`:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (Action\<ListenOptions >)

Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod. Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)

Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS. Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

### <a name="configureiconfiguration"></a>Konfigurace (IConfiguration)

Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>. Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.

rozšíření `ListenOptions.UseHttps`:

* `UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
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

parametry `ListenOptions.UseHttps`:

* `filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.
* `password` je heslo vyžadované pro přístup k datům certifikátu X. 509.
* `configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`. Vrátí `ListenOptions`.
* `storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.
* `subject` je název subjektu certifikátu.
* `allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.
* `location` je umístění úložiště, ze kterého se má načíst certifikát.
* `serverCertificate` je certifikát X. 509.

V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný. Musí být zadán minimálně výchozí certifikát.

Podporované konfigurace popsané dál:

* Žádná konfigurace
* Nahradit výchozí certifikát z konfigurace
* Změna výchozích hodnot v kódu

*Žádná konfigurace*

Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).

<a name="configuration"></a>

*Nahradit výchozí certifikát z konfigurace*

`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel. Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS. Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.

V následujícím příkladu *appSettings. JSON* :

* Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).
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

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů. Například **certifikáty** > **výchozí** certifikát lze zadat jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Poznámky ke schématu:

* V názvech koncových bodů se nerozlišují malá a velká písmena. Například `HTTPS` a `Https` jsou platné.
* U každého koncového bodu je vyžadován parametr `Url`. Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.
* Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů. Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.
* Oddíl `Certificate` je nepovinný. Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.
* Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .
* V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.

* Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.
* Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem. Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance. Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.
* `KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.

```csharp
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
```

*Podpora Kestrel pro SNI*

[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu. Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát. Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.

Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`. Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější. V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`. `name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.
* Všechny weby běží na stejné instanci Kestrel. Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.

```csharp
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
});
```

### <a name="connection-logging"></a>Protokolování připojení

Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení. Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery. Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz. Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Vytvoření vazby na soket TCP

Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vytvoření vazby na soket UNIX

Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a>Port 0

Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu. Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Omezení

Konfigurace koncových bodů pomocí následujících přístupů:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* argument příkazového řádku `--urls`
* konfigurační klíč hostitele `urls`
* `ASPNETCORE_URLS` proměnná prostředí

Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel. Mějte ale na paměti následující omezení:

* Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`. Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .

### <a name="listenoptionsprotocols"></a>ListenOptions. Protocols

Vlastnost `Protocols` vytváří protokoly HTTP (`HttpProtocols`) povolené na koncovém bodu připojení nebo pro server. Přiřaďte hodnotu vlastnosti `Protocols` ze výčtu `HttpProtocols`.

| hodnota výčtu `HttpProtocols` | Povolený protokol připojení |
| -------------------------- | ----------------------------- |
| `Http1`                    | Pouze HTTP/1.1. Dá se použít s TLS nebo bez něj. |
| `Http2`                    | Pouze HTTP/2. Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 a HTTP/2. HTTP/2 vyžaduje, aby klient v rámci metody handshake protokolu TLS [(ALPN)](https://tools.ietf.org/html/rfc7301#section-3) vybral http/2. v opačném případě se připojení nastaví jako výchozí HTTP/1.1. |

Výchozí hodnota `ListenOptions.Protocols` pro libovolný koncový bod je `HttpProtocols.Http1AndHttp2`.

Omezení TLS pro HTTP/2:

* TLS verze 1,2 nebo novější
* Opětovné vyjednávání je zakázané.
* Komprese vypnuta
* Minimální velikosti dočasného výměny klíčů:
  * Eliptická křivka Diffie-Hellman (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bity minima
  * Omezené pole Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bity minima
* Šifrovací sada není zakázaná.

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; s eliptickou křivkou P-256 &lbrack;`FIPS186`&rbrack; je ve výchozím nastavení podporován.

Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000. Připojení TLS zabezpečuje pomocí zadaného certifikátu:

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

Pomocí middleware připojení můžete v případě potřeby filtrovat handshake na základě jednotlivých připojení pro konkrétní šifry.

Následující příklad vyvolá <xref:System.NotSupportedException> pro jakýkoli šifrovací algoritmus, který aplikace nepodporuje. Případně můžete definovat a porovnat [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) se seznamem přijatelných šifrovacích sad.

Pro šifrovací algoritmus [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) se nepoužívá žádné šifrování.

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

Filtrování připojení lze také nakonfigurovat prostřednictvím lambda <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

V systému Linux lze pomocí <xref:System.Net.Security.CipherSuitesPolicy> filtrovat handshake TLS na jednotlivých připojeních:

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

*Nastavit protokol z konfigurace*

`CreateDefaultBuilder` volá ve výchozím nastavení `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.

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

* Na `IWebHostBuilder` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:

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

Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.

Platné jsou pouze předpony adresy URL protokolu HTTP. Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy hostitelů `*` a `+` nejsou speciální. Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6. Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

* Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo. Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.

## <a name="host-filtering"></a>Filtrování hostitele

I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele. Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky. Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy. hlavičky `Host` nejsou ověřeny.

Jako alternativní řešení použijte middleware pro filtrování hostitele. Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je implicitně k dispozici pro ASP.NET Core aplikace. Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appSettings. JSON*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>. Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení. Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.
>
> Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.

Kestrel podporuje následující scénáře:

* HTTPS
* Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)
* Technologie UNIX Sockets s vysokým výkonem za Nginx
* HTTP/2 (kromě macOS&dagger;)

&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.

Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>Podpora HTTP/2

[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:

* &dagger; operačního systému
  * Windows Server 2016/Windows 10 nebo novější&Dagger;
  * Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)
* Cílová architektura: .NET Core 2,2 nebo novější
* Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* Připojení TLS 1,2 nebo novější

&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.
&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený. Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).

Pokud je navázáno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.

HTTP/2 je ve výchozím nastavení zakázané. Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kdy použít Kestrel s reverzním proxy serverem

Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.

Kestrel použitý jako hraniční webový server (internetový):

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

Kestrel se používá v konfiguraci reverzního proxy serveru:

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.

Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy. Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`. Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.

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

ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel. V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Další informace o `CreateDefaultBuilder` a vytváření hostitele najdete v části *Nastavení hostitele* v <xref:fundamentals/host/web-host#set-up-a-host>.

Pokud chcete po volání `CreateDefaultBuilder` zadat další konfiguraci, použijte `ConfigureKestrel`:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

Pokud aplikace nevolá `CreateDefaultBuilder` pro nastavení hostitele, **před** voláním `ConfigureKestrel` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:

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

Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>. Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.

V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index). Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

Použijte **jeden** z následujících přístupů:

* Konfigurace Kestrel v `Startup.ConfigureServices`:

  1. Vloží instanci `IConfiguration` do třídy `Startup`. Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.
  2. V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* Nakonfigurovat Kestrel při sestavování hostitele:

  V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).

### <a name="keep-alive-timeout"></a>Časový limit Keep-Alive

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5). Výchozí hodnota je 2 minuty.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a>Maximální počet připojení klientů

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets). Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Ve výchozím nastavení je maximální počet připojení neomezené (null).

### <a name="maximum-request-body-size"></a>Maximální velikost textu požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.

Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Přepsat nastavení u konkrétního požadavku v middleware:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost. Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.

Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.

### <a name="minimum-request-body-data-rate"></a>Minimální rychlost těla zprávy požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu. Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována. Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.

Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.

Pro odpověď platí také minimální sazba. Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.

Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

V předchozí ukázce není k dispozici žádná míra – funkce `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků není pro protokol HTTP/2 podporovaná kvůli podpoře protokolu pro multiplexování požadavků. Omezení přenosové rychlosti pro všechny servery nakonfigurovaná přes `KestrelServerOptions.Limits` se stále vztahují na připojení HTTP/1. x i HTTP/2.

### <a name="request-headers-timeout"></a>Časový limit hlaviček žádostí

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků. Výchozí hodnota je 30 sekund.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a>Maximální počet proudů na připojení

`Http2.MaxStreamsPerConnection` omezuje počet souběžných proudů požadavků na připojení HTTP/2. Nadbytečné proudy jsou odmítnuty.

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

Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2. `Http2.HeaderTableSize` omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK. Hodnota je uvedena v oktetech a musí být větší než nula (0).

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

`Http2.MaxFrameSize` označuje maximální velikost datové části rámce připojení HTTP/2, která se má přijmout. Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).

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

`Http2.MaxRequestHeaderFieldSize` označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku. Toto omezení platí pro názvy i hodnoty společně v komprimovaných a nekomprimovaných reprezentujcích. Hodnota musí být větší než nula (0).

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

`Http2.InitialConnectionWindowSize` označuje maximální velikost textu žádosti v bajtech. vyrovnávací paměti serveru se v jednom okamžiku agreguje napříč všemi požadavky (datovými proudy) na připojení. Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`. Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

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

`Http2.InitialStreamWindowSize` označuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě. Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`. Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

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

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v. Výchozí hodnota je `true`.

> [!WARNING]
> Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje. Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.

Následující příklad povoluje synchronní vstupně-výstupní operace:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Informace o dalších možnostech a omezeních Kestrel najdete v tématech:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Ve výchozím nastavení ASP.NET Core váže k:

* `http://localhost:5000`
* `https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)

Zadejte adresy URL pomocí:

* `ASPNETCORE_URLS` proměnnou prostředí.
* argument příkazového řádku `--urls`
* `urls` konfigurační klíč hostitele.
* Metoda rozšíření `UseUrls`.

Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).

Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).

Vytvoří se vývojový certifikát:

* Po instalaci [.NET Core SDK](/dotnet/core/sdk) .
* [Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.

Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).

Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.

`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).

Konfigurace `KestrelServerOptions`:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (Action\<ListenOptions >)

Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod. Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)

Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS. Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.

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

Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>. Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.

rozšíření `ListenOptions.UseHttps`:

* `UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
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

parametry `ListenOptions.UseHttps`:

* `filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.
* `password` je heslo vyžadované pro přístup k datům certifikátu X. 509.
* `configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`. Vrátí `ListenOptions`.
* `storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.
* `subject` je název subjektu certifikátu.
* `allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.
* `location` je umístění úložiště, ze kterého se má načíst certifikát.
* `serverCertificate` je certifikát X. 509.

V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný. Musí být zadán minimálně výchozí certifikát.

Podporované konfigurace popsané dál:

* Žádná konfigurace
* Nahradit výchozí certifikát z konfigurace
* Změna výchozích hodnot v kódu

*Žádná konfigurace*

Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).

<a name="configuration"></a>

*Nahradit výchozí certifikát z konfigurace*

`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel. Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS. Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.

V následujícím příkladu *appSettings. JSON* :

* Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).
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

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů. Například **certifikáty** > **výchozí** certifikát lze zadat jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Poznámky ke schématu:

* V názvech koncových bodů se nerozlišují malá a velká písmena. Například `HTTPS` a `Https` jsou platné.
* U každého koncového bodu je vyžadován parametr `Url`. Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.
* Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů. Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.
* Oddíl `Certificate` je nepovinný. Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.
* Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .
* V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:

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

`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.

* Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.
* Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem. Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance. Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.
* `KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.

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

Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`. Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější. V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`. `name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.
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

### <a name="connection-logging"></a>Protokolování připojení

Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení. Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery. Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz. Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Vytvoření vazby na soket TCP

Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vytvoření vazby na soket UNIX

Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a>Port 0

Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu. Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Omezení

Konfigurace koncových bodů pomocí následujících přístupů:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* argument příkazového řádku `--urls`
* konfigurační klíč hostitele `urls`
* `ASPNETCORE_URLS` proměnná prostředí

Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel. Mějte ale na paměti následující omezení:

* Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`. Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .

### <a name="listenoptionsprotocols"></a>ListenOptions. Protocols

Vlastnost `Protocols` vytváří protokoly HTTP (`HttpProtocols`) povolené na koncovém bodu připojení nebo pro server. Přiřaďte hodnotu vlastnosti `Protocols` ze výčtu `HttpProtocols`.

| hodnota výčtu `HttpProtocols` | Povolený protokol připojení |
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
  * Eliptická křivka Diffie-Hellman (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bity minima
  * Omezené pole Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bity minima
* Šifrovací sada není zakázaná.

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; s eliptickou křivkou P-256 &lbrack;`FIPS186`&rbrack; je ve výchozím nastavení podporován.

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

Volitelně můžete vytvořit implementaci `IConnectionAdapter` pro filtrování ověřování TLS na základě jednotlivých připojení pro konkrétní šifry:

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
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
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

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> volá ve výchozím nastavení `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.

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

S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů. Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:

* [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)
* [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

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

Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.

Platné jsou pouze předpony adresy URL protokolu HTTP. Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy hostitelů `*` a `+` nejsou speciální. Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6. Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

* Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo. Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.

## <a name="host-filtering"></a>Filtrování hostitele

I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele. Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky. Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy. hlavičky `Host` nejsou ověřeny.

Jako alternativní řešení použijte middleware pro filtrování hostitele. Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2). Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appSettings. JSON*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>. Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení. Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.
>
> Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.

Kestrel podporuje následující scénáře:

* HTTPS
* Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)
* Technologie UNIX Sockets s vysokým výkonem za Nginx

Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kdy použít Kestrel s reverzním proxy serverem

Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.

Kestrel použitý jako hraniční webový server (internetový):

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

Kestrel se používá v konfiguraci reverzního proxy serveru:

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.

Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy. Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`. Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.

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

ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel. V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.

Pro poskytnutí další konfigurace po volání `CreateDefaultBuilder` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

Další informace o `CreateDefaultBuilder` a vytváření hostitele najdete v části *Nastavení hostitele* v <xref:fundamentals/host/web-host#set-up-a-host>.

## <a name="kestrel-options"></a>Kestrel možnosti

Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.

Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>. Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.

V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index). Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

Použijte **jeden** z následujících přístupů:

* Konfigurace Kestrel v `Startup.ConfigureServices`:

  1. Vloží instanci `IConfiguration` do třídy `Startup`. Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.
  2. V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* Nakonfigurovat Kestrel při sestavování hostitele:

  V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).

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

Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets). Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.

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

Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:

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

Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost. Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.

Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.

### <a name="minimum-request-body-data-rate"></a>Minimální rychlost těla zprávy požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu. Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována. Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.

Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.

Pro odpověď platí také minimální sazba. Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.

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

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v. Výchozí hodnota je `true`.

> [!WARNING]
> Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje. Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.

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
* `https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)

Zadejte adresy URL pomocí:

* `ASPNETCORE_URLS` proměnnou prostředí.
* argument příkazového řádku `--urls`
* `urls` konfigurační klíč hostitele.
* Metoda rozšíření `UseUrls`.

Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).

Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).

Vytvoří se vývojový certifikát:

* Po instalaci [.NET Core SDK](/dotnet/core/sdk) .
* [Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.

Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).

Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.

`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).

Konfigurace `KestrelServerOptions`:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (Action\<ListenOptions >)

Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod. Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)

Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS. Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.

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

Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>. Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.

rozšíření `ListenOptions.UseHttps`:

* `UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
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

parametry `ListenOptions.UseHttps`:

* `filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.
* `password` je heslo vyžadované pro přístup k datům certifikátu X. 509.
* `configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`. Vrátí `ListenOptions`.
* `storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.
* `subject` je název subjektu certifikátu.
* `allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.
* `location` je umístění úložiště, ze kterého se má načíst certifikát.
* `serverCertificate` je certifikát X. 509.

V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný. Musí být zadán minimálně výchozí certifikát.

Podporované konfigurace popsané dál:

* Žádná konfigurace
* Nahradit výchozí certifikát z konfigurace
* Změna výchozích hodnot v kódu

*Žádná konfigurace*

Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).

<a name="configuration"></a>

*Nahradit výchozí certifikát z konfigurace*

`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel. Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS. Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.

V následujícím příkladu *appSettings. JSON* :

* Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).
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

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů. Například **certifikáty** > **výchozí** certifikát lze zadat jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Poznámky ke schématu:

* V názvech koncových bodů se nerozlišují malá a velká písmena. Například `HTTPS` a `Https` jsou platné.
* U každého koncového bodu je vyžadován parametr `Url`. Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.
* Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů. Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.
* Oddíl `Certificate` je nepovinný. Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.
* Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .
* V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:

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

`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.

* Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.
* Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem. Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance. Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.
* `KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.

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

Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`. Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější. V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`. `name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.
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

### <a name="connection-logging"></a>Protokolování připojení

Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení. Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery. Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz. Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Vytvoření vazby na soket TCP

Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:

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

Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vytvoření vazby na soket UNIX

Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:

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

Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu. Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Omezení

Konfigurace koncových bodů pomocí následujících přístupů:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* argument příkazového řádku `--urls`
* konfigurační klíč hostitele `urls`
* `ASPNETCORE_URLS` proměnná prostředí

Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel. Mějte ale na paměti následující omezení:

* Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`. Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .

## <a name="transport-configuration"></a>Konfigurace přenosu

S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů. Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:

* [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)
* [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

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

Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.

Platné jsou pouze předpony adresy URL protokolu HTTP. Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy hostitelů `*` a `+` nejsou speciální. Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6. Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

* Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo. Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.

## <a name="host-filtering"></a>Filtrování hostitele

I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele. Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky. Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy. hlavičky `Host` nejsou ověřeny.

Jako alternativní řešení použijte middleware pro filtrování hostitele. Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2). Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appSettings. JSON*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>. Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení. Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.
>
> Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [RFC 7230: syntaxe zprávy a směrování (oddíl 5,4: hostitel)](https://tools.ietf.org/html/rfc7230#section-5.4)

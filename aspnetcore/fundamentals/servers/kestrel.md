---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: rick-anderson
description: Přečtěte si o Kestrel, webovém serveru pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/kestrel
ms.openlocfilehash: fd0db90e897a813e0127ce1c751569158efa8fdf
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074174"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a>Implementace webového serveru Kestrel v ASP.NET Core

Díky [Dykstra](https://github.com/tdykstra), [Novák Rossův](https://github.com/Tratcher)a zablokování [Stephen](https://twitter.com/halter73)

::: moniker range=">= aspnetcore-3.0"

Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.

Kestrel podporuje následující scénáře:

* HTTPS
* Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)
* Technologie UNIX Sockets s vysokým výkonem za Nginx
* HTTP/2 (kromě macOS &dagger; )

&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.

Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>Podpora HTTP/2

[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:

* Operační systém&dagger;
  * Windows Server 2016/Windows 10 nebo novější&Dagger;
  * Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)
* Cílová architektura: .NET Core 2,2 nebo novější
* Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* Připojení TLS 1,2 nebo novější

&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.
&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený. Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).

Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .

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

## <a name="kestrel-in-aspnet-core-apps"></a>Kestrel v aplikacích ASP.NET Core

ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel. V *program.cs* <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> metoda volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

Další informace o vytváření hostitele najdete v oddílech nastavení *hostitele* a *výchozí tvůrce nastavení* v tématu <xref:fundamentals/host/generic-host#set-up-a-host> .

Chcete-li po volání poskytnout další konfiguraci `ConfigureWebHostDefaults` , použijte `ConfigureKestrel` :

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

## <a name="kestrel-options"></a>Kestrel možnosti

Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.

Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy. `Limits`Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.

V následujících příkladech se používá <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obor názvů:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

V příkladech, které jsou uvedeny dále v tomto článku, jsou možnosti Kestrel konfigurovány v kódu jazyka C#. Možnosti Kestrel lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index). [Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider) může například načíst konfiguraci Kestrel z *appsettings.jsna* nebo *appSettings. { Soubor Environment}. JSON* :

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

> [!NOTE]
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[Konfigurace koncových bodů](#endpoint-configuration) se dá konfigurovat z poskytovatelů konfigurace. Zbývající konfigurace Kestrel musí být nakonfigurovaná v kódu C#.

Použijte **jeden** z následujících přístupů:

* Nakonfigurovat Kestrel v `Startup.ConfigureServices` :

  1. Vloží instanci `IConfiguration` do `Startup` třídy. Následující příklad předpokládá, že vložená konfigurace je přiřazena `Configuration` Vlastnosti.
  2. V aplikaci `Startup.ConfigureServices` načtěte `Kestrel` část konfigurace do konfigurace Kestrel:

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* Nakonfigurovat Kestrel při sestavování hostitele:

  V *program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrel:

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

Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets). Po upgradu připojení se nepočítá na základě `MaxConcurrentConnections` limitu.

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

Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost. Existuje `IsReadOnly` vlastnost, která určuje, zda `MaxRequestBodySize` je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.

Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.

### <a name="minimum-request-body-data-rate"></a>Minimální rychlost těla zprávy požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu. Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována. Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.

Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.

Pro odpověď platí také minimální sazba. Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou, `RequestBody` že existují nebo `Response` v názvech vlastností a rozhraní.

Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Odkazovaná v předchozí ukázce není k dispozici `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků je obecně Nepodporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> `HttpContext.Features` Pro požadavky HTTP/2 se ale stále používá, protože omezení četnosti čtení může být pořád *zcela zakázané* na základě jednotlivých požadavků nastavením `IHttpMinRequestBodyDataRateFeature.MinDataRate` na `null` i pro požadavek HTTP/2. Když se pokusíte o jeho navýšení `IHttpMinRequestBodyDataRateFeature.MinDataRate` nebo pokus o jeho nastavení na jinou hodnotu než, `null` vygeneruje se `NotSupportedException` mu požadavek HTTP/2.

Omezení přenosové rychlosti pro všechny servery jsou nakonfigurovaná přes `KestrelServerOptions.Limits` protokol HTTP/1. x i pro připojení HTTP/2.

### <a name="request-headers-timeout"></a>Časový limit hlaviček žádostí

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků. Výchozí hodnota je 30 sekund.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a>Maximální počet proudů na připojení

`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2. Nadbytečné proudy jsou odmítnuty.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

Výchozí hodnota je 100.

### <a name="header-table-size"></a>Velikost tabulky hlaviček

Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2. `Http2.HeaderTableSize`omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK. Hodnota je uvedena v oktetech a musí být větší než nula (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

Výchozí hodnota je 4096.

### <a name="maximum-frame-size"></a>Maximální velikost rámce

`Http2.MaxFrameSize`Určuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijatého nebo odesílaného serverem. Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

Výchozí hodnota je 2 ^ 14 (16 384).

### <a name="maximum-request-header-size"></a>Maximální velikost hlavičky požadavku

`Http2.MaxRequestHeaderFieldSize`Určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku. Toto omezení se vztahuje na název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentujcích. Hodnota musí být větší než nula (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

Výchozí hodnota je 8 192.

### <a name="initial-connection-window-size"></a>Velikost okna počátečního připojení

`Http2.InitialConnectionWindowSize`Určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení. Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize` . Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

Výchozí hodnota je 128 KB (131 072).

### <a name="initial-stream-window-size"></a>Počáteční velikost okna datového proudu

`Http2.InitialStreamWindowSize`Určuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě. Žádosti jsou také omezené pomocí `Http2.InitialConnectionWindowSize` . Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

Výchozí hodnota je 96 KB (98 304).

### <a name="synchronous-io"></a>Synchronní vstupně-výstupní operace

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, jestli je pro požadavek a odpověď povolený synchronní vstup/výstup. Výchozí hodnota je `false`.

> [!WARNING]
> Velký počet blokujících synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje. Povolit pouze `AllowSynchronousIO` při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.

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

* `ASPNETCORE_URLS`Proměnná prostředí.
* `--urls`argument příkazového řádku.
* `urls`konfigurační klíč hostitele.
* `UseUrls`Metoda rozšíření

Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000;http://localhost:8001"` ).

Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).

Vytvoří se vývojový certifikát:

* Po instalaci [.NET Core SDK](/dotnet/core/sdk) .
* [Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.

Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).

Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> konfiguraci předpon adres URL a portů pro Kestrel.

`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` Proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu https musí být k dispozici výchozí certifikát).

`KestrelServerOptions`rozšířeného

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (akce \<ListenOptions> )

Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod. Volání několikrát `ConfigureEndpointDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (akce \<HttpsConnectionAdapterOptions> )

Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS. Volání několikrát `ConfigureHttpsDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.

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

> [!NOTE]
> Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.

### <a name="configureiconfiguration"></a>Konfigurace (IConfiguration)

Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup. Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.

`ListenOptions.UseHttps`SND

* `UseHttps`: Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
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
* `configureOptions`je `Action` ke konfiguraci `HttpsConnectionAdapterOptions` . Vrátí `ListenOptions` .
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

V následujícím *appsettings.js* například:

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

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů. Například **Certificates**  >  **výchozí** certifikát může být zadán jako:

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
* `Url`U každého koncového bodu je vyžadován parametr. Formát pro tento parametr je stejný jako `Urls` konfigurační parametr nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.
* Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů. Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.
* `Certificate`Oddíl je nepovinný. Pokud `Certificate` není oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.
* `Certificate`Oddíl podporuje jak heslo **cesty** &ndash; **Password** , tak **Subject** &ndash; certifikáty**úložiště** subjektu.
* V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`Vrátí `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodu s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:

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

`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .

* Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.
* Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem. Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích. Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.
* `KestrelConfigurationLoader`zrcadlí `Listen` rodinu rozhraní API z `KestrelServerOptions` as `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions` , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.

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

Kestrel podporuje SNI prostřednictvím `ServerCertificateSelector` zpětného volání. Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším. V systému `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null` . `name`Je také v `null` případě, že klient v TLS handshake neposkytne parametr názvu hostitele.
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

Volá <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> se, aby se vygenerovaly protokoly na úrovni ladění pro komunikaci na úrovni bajtového připojení. Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery. Pokud `UseConnectionLogging` je umístěn před `UseHttps` , zaprotokoluje se zašifrovaný provoz. Pokud `UseConnectionLogging` je umístěn po `UseHttps` , zaprotokoluje se dešifrovaný provoz.

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

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> . Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vytvoření vazby na soket UNIX

Naslouchat na soketu se systémem UNIX <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> , aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* V konfiguračním souboru Nginx nastavte `server`  >  `location`  >  `proxy_pass` položku na `http://unix:/tmp/{KESTREL SOCKET}:/;` . `{KESTREL SOCKET}`je název soketu poskytnutý <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).
* Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock` ).

### <a name="port-0"></a>Port 0

Po zadání čísla portu se `0` Kestrel dynamicky váže k dostupnému portu. Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:

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

* Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí `KestrelServerOptions` Konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při `Listen` `UseUrls` současném použití obou přístupů a `Listen` koncových bodů se koncové body přepisují `UseUrls` .

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls` . Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .

### <a name="listenoptionsprotocols"></a>ListenOptions. Protocols

`Protocols`Vlastnost určuje protokoly HTTP ( `HttpProtocols` ) povolené pro koncový bod připojení nebo pro server. Přiřaďte hodnotu `Protocols` vlastnosti z `HttpProtocols` výčtu.

| `HttpProtocols`hodnota výčtu | Povolený protokol připojení |
| -------------------------- | ----------------------------- |
| `Http1`                    | Pouze HTTP/1.1. Dá se použít s TLS nebo bez něj. |
| `Http2`                    | Pouze HTTP/2. Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 a HTTP/2. HTTP/2 vyžaduje, aby klient v rámci metody handshake protokolu TLS [(ALPN)](https://tools.ietf.org/html/rfc7301#section-3) vybral http/2. v opačném případě se připojení nastaví jako výchozí HTTP/1.1. |

Výchozí `ListenOptions.Protocols` hodnota pro libovolný koncový bod je `HttpProtocols.Http1AndHttp2` .

Omezení TLS pro HTTP/2:

* TLS verze 1,2 nebo novější
* Opětovné vyjednávání je zakázané.
* Komprese vypnuta
* Minimální velikosti dočasného výměny klíčů:
  * Eliptická křivka Diffie-Hellman (ECDH) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bity minima
  * Omezené pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : minimální počet bitů 2048
* Šifrovací sada není zakázaná. 

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; &lbrack; `FIPS186` &rbrack; ve výchozím nastavení je podporována eliptická křivka P-256.

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

Následující příklad vyvolá <xref:System.NotSupportedException> jakýkoli šifrovací algoritmus, který aplikace nepodporuje. Případně můžete definovat a porovnat [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) se seznamem přijatelných šifrovacích sad.

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

Filtrování připojení lze také nakonfigurovat prostřednictvím <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:

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

V systému Linux se <xref:System.Net.Security.CipherSuitesPolicy> dá použít k filtrování ověřování TLS metodou handshake na základě jednotlivých připojení:

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

`CreateDefaultBuilder`Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.

Následující *appsettings.jsv* příkladě vytvoří protokol HTTP/1.1 jako výchozí protokol připojení pro všechny koncové body:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

Následující *appsettings.jsv* příkladu vytvoří protokol připojení HTTP/1.1 pro konkrétní koncový bod:

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

Pro projekty, které vyžadují použití Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ):

* Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* Zavolat <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> na `IWebHostBuilder` :

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

Při použití `UseUrls` , `--urls` argumentu příkazového řádku, `urls` konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.

Platné jsou pouze předpony adresy URL protokolu HTTP. Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls` .

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`je ekvivalentem protokolu IPv4 pro protokol IPv6 `0.0.0.0` .

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy hostitelů, `*` a `+` nejsou speciální. Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6. Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [HTTP.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

* `localhost`Název hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo. Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.

## <a name="host-filtering"></a>Filtrování hostitele

I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000` , jako je, Kestrel převážně ignoruje název hostitele. Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky. Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy. `Host`hlavičky nejsou ověřené.

Jako alternativní řešení použijte middleware pro filtrování hostitele. Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je implicitně k dispozici pro ASP.NET Core aplikace. Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.jsna* / *appSettings. \<EnvironmentName> . JSON*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appsettings.js*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má také <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> možnost. Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné v případě, že `Host` Hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení. Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo když se `Host` Hlavička přímo přepošle.
>
> Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer> .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.

Kestrel podporuje následující scénáře:

* HTTPS
* Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)
* Technologie UNIX Sockets s vysokým výkonem za Nginx
* HTTP/2 (kromě macOS &dagger; )

&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.

Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>Podpora HTTP/2

[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:

* Operační systém&dagger;
  * Windows Server 2016/Windows 10 nebo novější&Dagger;
  * Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)
* Cílová architektura: .NET Core 2,2 nebo novější
* Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)
* Připojení TLS 1,2 nebo novější

&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.
&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený. Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).

Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .

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

ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel. V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá na pozadí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> .

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Další informace o `CreateDefaultBuilder` a sestavení hostitele naleznete v části *Nastavení hostitele* v tématu <xref:fundamentals/host/web-host#set-up-a-host> .

Chcete-li po volání poskytnout další konfiguraci `CreateDefaultBuilder` , použijte `ConfigureKestrel` :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

Pokud aplikace nevolá `CreateDefaultBuilder` pro nastavení hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **před** voláním `ConfigureKestrel` :

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

Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy. `Limits`Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.

V následujících příkladech se používá <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obor názvů:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Možnosti Kestrel, které jsou konfigurovány v kódu jazyka C# v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index). Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z *appsettings.jsna* nebo *appSettings. { Soubor Environment}. JSON* :

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

* Nakonfigurovat Kestrel v `Startup.ConfigureServices` :

  1. Vloží instanci `IConfiguration` do `Startup` třídy. Následující příklad předpokládá, že vložená konfigurace je přiřazena `Configuration` Vlastnosti.
  2. V aplikaci `Startup.ConfigureServices` načtěte `Kestrel` část konfigurace do konfigurace Kestrel:

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* Nakonfigurovat Kestrel při sestavování hostitele:

  V *program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrel:

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

Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets). Po upgradu připojení se nepočítá na základě `MaxConcurrentConnections` limitu.

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

Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost. Existuje `IsReadOnly` vlastnost, která určuje, zda `MaxRequestBodySize` je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.

Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.

### <a name="minimum-request-body-data-rate"></a>Minimální rychlost těla zprávy požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu. Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována. Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.

Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.

Pro odpověď platí také minimální sazba. Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou, `RequestBody` že existují nebo `Response` v názvech vlastností a rozhraní.

Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Pro požadavky HTTP/2 se v předchozí ukázce neodkazuje žádná míra funkce, `HttpContext.Features` protože Změna omezení přenosové rychlosti na základě jednotlivých požadavků není podporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků. Omezení přenosové rychlosti pro všechny servery jsou nakonfigurovaná přes `KestrelServerOptions.Limits` protokol HTTP/1. x i pro připojení HTTP/2.

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

`Http2.InitialConnectionWindowSize`Určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení. Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize` . Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

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

`Http2.InitialStreamWindowSize`Určuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě. Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize` . Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).

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

### <a name="synchronous-io"></a>Synchronní vstupně-výstupní operace

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, jestli je pro požadavek a odpověď povolený synchronní vstup/výstup. Výchozí hodnota je `true` .

> [!WARNING]
> Velký počet blokujících synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje. Povolit pouze `AllowSynchronousIO` při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.

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

* `ASPNETCORE_URLS`Proměnná prostředí.
* `--urls`argument příkazového řádku.
* `urls`konfigurační klíč hostitele.
* `UseUrls`Metoda rozšíření

Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000;http://localhost:8001"` ).

Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).

Vytvoří se vývojový certifikát:

* Po instalaci [.NET Core SDK](/dotnet/core/sdk) .
* [Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.

Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).

Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> konfiguraci předpon adres URL a portů pro Kestrel.

`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` Proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu https musí být k dispozici výchozí certifikát).

`KestrelServerOptions`rozšířeného

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (akce \<ListenOptions> )

Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod. Volání několikrát `ConfigureEndpointDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.

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

> [!NOTE]
> Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (akce \<HttpsConnectionAdapterOptions> )

Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS. Volání několikrát `ConfigureHttpsDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.

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

> [!NOTE]
> Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.


### <a name="configureiconfiguration"></a>Konfigurace (IConfiguration)

Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup. Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.

`ListenOptions.UseHttps`SND

* `UseHttps`: Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
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
* `configureOptions`je `Action` ke konfiguraci `HttpsConnectionAdapterOptions` . Vrátí `ListenOptions` .
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

V následujícím *appsettings.js* například:

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

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů. Například **Certificates**  >  **výchozí** certifikát může být zadán jako:

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
* `Url`U každého koncového bodu je vyžadován parametr. Formát pro tento parametr je stejný jako `Urls` konfigurační parametr nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.
* Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů. Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.
* `Certificate`Oddíl je nepovinný. Pokud `Certificate` není oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.
* `Certificate`Oddíl podporuje jak heslo **cesty** &ndash; **Password** , tak **Subject** &ndash; certifikáty**úložiště** subjektu.
* V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`Vrátí `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodu s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:

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

`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .

* Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.
* Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem. Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích. Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.
* `KestrelConfigurationLoader`zrcadlí `Listen` rodinu rozhraní API z `KestrelServerOptions` as `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions` , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.

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

Kestrel podporuje SNI prostřednictvím `ServerCertificateSelector` zpětného volání. Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším. V systému `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null` . `name`Je také v `null` případě, že klient v TLS handshake neposkytne parametr názvu hostitele.
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

Volá <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> se, aby se vygenerovaly protokoly na úrovni ladění pro komunikaci na úrovni bajtového připojení. Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery. Pokud `UseConnectionLogging` je umístěn před `UseHttps` , zaprotokoluje se zašifrovaný provoz. Pokud `UseConnectionLogging` je umístěn po `UseHttps` , zaprotokoluje se dešifrovaný provoz.

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

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> . Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vytvoření vazby na soket UNIX

Naslouchat na soketu se systémem UNIX <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> , aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* V souboru Nginx confiuguration nastavte `server`  >  `location`  >  `proxy_pass` položku na `http://unix:/tmp/{KESTREL SOCKET}:/;` . `{KESTREL SOCKET}`je název soketu poskytnutý <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).
* Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock` ). 

### <a name="port-0"></a>Port 0

Po zadání čísla portu se `0` Kestrel dynamicky váže k dostupnému portu. Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:

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

* Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí `KestrelServerOptions` Konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při `Listen` `UseUrls` současném použití obou přístupů a `Listen` koncových bodů se koncové body přepisují `UseUrls` .

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls` . Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .

### <a name="listenoptionsprotocols"></a>ListenOptions. Protocols

`Protocols`Vlastnost určuje protokoly HTTP ( `HttpProtocols` ) povolené pro koncový bod připojení nebo pro server. Přiřaďte hodnotu `Protocols` vlastnosti z `HttpProtocols` výčtu.

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
  * Eliptická křivka Diffie-Hellman (ECDH) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bity minima
  * Omezené pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : minimální počet bitů 2048
* Šifrovací sada není zakázaná.

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; &lbrack; `FIPS186` &rbrack; ve výchozím nastavení je podporována eliptická křivka P-256.

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

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.

V následujících *appsettings.js* jsou například pro všechny koncové body Kestrel zavedeny výchozí protokoly připojení (http/1.1 a HTTP/2):

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

S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů. Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, které volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:

* [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)
* [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Pro projekty, které vyžadují použití Libuv:

* Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :

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

Při použití `UseUrls` , `--urls` argumentu příkazového řádku, `urls` konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.

Platné jsou pouze předpony adresy URL protokolu HTTP. Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls` .

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`je ekvivalentem protokolu IPv4 pro protokol IPv6 `0.0.0.0` .

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy hostitelů, `*` a `+` nejsou speciální. Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6. Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [HTTP.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

* `localhost`Název hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo. Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.

## <a name="host-filtering"></a>Filtrování hostitele

I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000` , jako je, Kestrel převážně ignoruje název hostitele. Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky. Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy. `Host`hlavičky nejsou ověřené.

Jako alternativní řešení použijte middleware pro filtrování hostitele. Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2). Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.jsna* / *appSettings. \<EnvironmentName> . JSON*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appsettings.js*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má také <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> možnost. Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné v případě, že `Host` Hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení. Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo když se `Host` Hlavička přímo přepošle.
>
> Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer> .

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.

Kestrel podporuje následující scénáře:

* HTTPS
* Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)
* Technologie UNIX Sockets s vysokým výkonem za Nginx

Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

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

ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel. V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá na pozadí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> .

Chcete-li po volání poskytnout další konfiguraci `CreateDefaultBuilder` , zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

Další informace o `CreateDefaultBuilder` a sestavení hostitele naleznete v části *Nastavení hostitele* v tématu <xref:fundamentals/host/web-host#set-up-a-host> .

## <a name="kestrel-options"></a>Kestrel možnosti

Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.

Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy. `Limits`Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.

V následujících příkladech se používá <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obor názvů:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Možnosti Kestrel, které jsou konfigurovány v kódu jazyka C# v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index). Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z *appsettings.jsna* nebo *appSettings. { Soubor Environment}. JSON* :

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

* Nakonfigurovat Kestrel v `Startup.ConfigureServices` :

  1. Vloží instanci `IConfiguration` do `Startup` třídy. Následující příklad předpokládá, že vložená konfigurace je přiřazena `Configuration` Vlastnosti.
  2. V aplikaci `Startup.ConfigureServices` načtěte `Kestrel` část konfigurace do konfigurace Kestrel:

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* Nakonfigurovat Kestrel při sestavování hostitele:

  V *program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrel:

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

Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets). Po upgradu připojení se nepočítá na základě `MaxConcurrentConnections` limitu.

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

Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost. Existuje `IsReadOnly` vlastnost, která určuje, zda `MaxRequestBodySize` je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.

Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.

### <a name="minimum-request-body-data-rate"></a>Minimální rychlost těla zprávy požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu. Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována. Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.

Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.

Pro odpověď platí také minimální sazba. Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou, `RequestBody` že existují nebo `Response` v názvech vlastností a rozhraní.

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

### <a name="synchronous-io"></a>Synchronní vstupně-výstupní operace

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, jestli je pro požadavek a odpověď povolený synchronní vstup/výstup. Výchozí hodnota je `true` .

> [!WARNING]
> Velký počet blokujících synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje. Povolit pouze `AllowSynchronousIO` při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.

Následující příklad zakáže synchronní vstupně-výstupní operace:

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

* `ASPNETCORE_URLS`Proměnná prostředí.
* `--urls`argument příkazového řádku.
* `urls`konfigurační klíč hostitele.
* `UseUrls`Metoda rozšíření

Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000;http://localhost:8001"` ).

Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).

Vytvoří se vývojový certifikát:

* Po instalaci [.NET Core SDK](/dotnet/core/sdk) .
* [Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.

Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).

Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> konfiguraci předpon adres URL a portů pro Kestrel.

`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` Proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu https musí být k dispozici výchozí certifikát).

`KestrelServerOptions`rozšířeného

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (akce \<ListenOptions> )

Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod. Volání několikrát `ConfigureEndpointDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.

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

> [!NOTE]
> Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (akce \<HttpsConnectionAdapterOptions> )

Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS. Volání několikrát `ConfigureHttpsDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.

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

> [!NOTE]
> Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.

### <a name="configureiconfiguration"></a>Konfigurace (IConfiguration)

Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup. Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.

`ListenOptions.UseHttps`SND

* `UseHttps`: Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
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
* `configureOptions`je `Action` ke konfiguraci `HttpsConnectionAdapterOptions` . Vrátí `ListenOptions` .
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

V následujícím *appsettings.js* například:

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

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů. Například **Certificates**  >  **výchozí** certifikát může být zadán jako:

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
* `Url`U každého koncového bodu je vyžadován parametr. Formát pro tento parametr je stejný jako `Urls` konfigurační parametr nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.
* Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů. Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.
* `Certificate`Oddíl je nepovinný. Pokud `Certificate` není oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.
* `Certificate`Oddíl podporuje jak heslo **cesty** &ndash; **Password** , tak **Subject** &ndash; certifikáty**úložiště** subjektu.
* V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`Vrátí `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodu s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:

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

`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .

* Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.
* Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem. Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích. Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.
* `KestrelConfigurationLoader`zrcadlí `Listen` rodinu rozhraní API z `KestrelServerOptions` as `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions` , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.

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

Kestrel podporuje SNI prostřednictvím `ServerCertificateSelector` zpětného volání. Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším. V systému `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null` . `name`Je také v `null` případě, že klient v TLS handshake neposkytne parametr názvu hostitele.
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

Volá <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> se, aby se vygenerovaly protokoly na úrovni ladění pro komunikaci na úrovni bajtového připojení. Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery. Pokud `UseConnectionLogging` je umístěn před `UseHttps` , zaprotokoluje se zašifrovaný provoz. Pokud `UseConnectionLogging` je umístěn po `UseHttps` , zaprotokoluje se dešifrovaný provoz.

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

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:

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

Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> . Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vytvoření vazby na soket UNIX

Naslouchat na soketu se systémem UNIX <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> , aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:

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

* V souboru Nginx confiuguration nastavte `server`  >  `location`  >  `proxy_pass` položku na `http://unix:/tmp/{KESTREL SOCKET}:/;` . `{KESTREL SOCKET}`je název soketu poskytnutý <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).
* Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock` ). 

### <a name="port-0"></a>Port 0

Po zadání čísla portu se `0` Kestrel dynamicky váže k dostupnému portu. Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:

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

* Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí `KestrelServerOptions` Konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při `Listen` `UseUrls` současném použití obou přístupů a `Listen` koncových bodů se koncové body přepisují `UseUrls` .

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls` . Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .

## <a name="transport-configuration"></a>Konfigurace přenosu

S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů. Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, které volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:

* [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)
* [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Pro projekty, které vyžadují použití Libuv:

* Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :

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

Při použití `UseUrls` , `--urls` argumentu příkazového řádku, `urls` konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.

Platné jsou pouze předpony adresy URL protokolu HTTP. Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls` .

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`je ekvivalentem protokolu IPv4 pro protokol IPv6 `0.0.0.0` .

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy hostitelů, `*` a `+` nejsou speciální. Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6. Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [HTTP.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).

* `localhost`Název hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo. Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.

## <a name="host-filtering"></a>Filtrování hostitele

I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000` , jako je, Kestrel převážně ignoruje název hostitele. Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky. Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy. `Host`hlavičky nejsou ověřené.

Jako alternativní řešení použijte middleware pro filtrování hostitele. Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2). Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.jsna* / *appSettings. \<EnvironmentName> . JSON*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appsettings.js*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má také <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> možnost. Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné v případě, že `Host` Hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení. Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo když se `Host` Hlavička přímo přepošle.
>
> Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer> .

::: moniker-end

## <a name="http11-request-draining"></a>Vyprazdňování požadavku HTTP/1.1

Otevírání připojení HTTP je časově náročné. V případě protokolu HTTPS je také náročný na prostředky. Proto se Kestrel pokusí znovu použít připojení na protokol HTTP/1.1. Aby bylo možné připojení znovu použít, musí být text žádosti plně spotřebovaný. Aplikace nespotřebovává text žádosti vždycky, například `POST` žádosti, kde server vrací přesměrování nebo odpověď 404. V `POST` případě případu přesměrování:

* Klient již mohl odeslat část `POST` dat.
* Server zapisuje odpověď 301.
* Připojení nelze použít pro novou žádost, dokud nebudou `POST` data z předchozího textu požadavku plně přečtena.
* Kestrel se pokusí vyprázdnit tělo žádosti. Vyprázdnit tělo žádosti znamená čtení a zahození dat bez jejich zpracování.

Proces vyprazdňování vytváří tradoff mezi povolením opětovného použití připojení a časem potřebným k vyprázdnění zbývajících dat:

* Vyprázdnění má časový limit pět sekund, což není konfigurovatelné.
* Pokud nebyla všechna data zadaná v `Content-Length` `Transfer-Encoding` hlavičce nebo přečtena před časovým limitem, připojení je zavřeno.

Někdy je možné, že budete chtít žádost ukončit hned nebo po zápisu odpovědi. Například klienti mohou mít omezující data, takže omezení nahraných dat může být prioritou. V takových případech pro ukončení žádosti zavolejte [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) z kontroleru, Razor stránky nebo middlewaru.

Existují upozornění pro volání `Abort` :

* Vytváření nových připojení může být pomalé a nákladné.
* Není zaručeno, že klient před zavřením připojení přečetl odpověď.
* Volání `Abort` by mělo být vzácné a vyhrazené pro závažné chybové případy, ne běžné chyby.
  * Volá se jenom v případě, že je `Abort` potřeba vyřešit konkrétní problém. Například volejte, `Abort` Pokud se zlomyslní klienti pokoušejí `POST` data nebo když dojde k chybě v kódu klienta, která způsobuje velké nebo velké požadavky.
  * Nevolejte `Abort` pro běžné chybové situace, například HTTP 404 (Nenalezeno).

Volání [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) před voláním `Abort` zajistí, že server dokončil zápis odpovědi. Nicméně chování klienta není předvídatelné a nemusí před přerušením připojení číst odpověď.

Tento proces se liší od protokolu HTTP/2, protože protokol podporuje přerušování jednotlivých datových proudů požadavků bez ukončení připojení. Nedá se použít 5 sekundový časový limit vyprázdnění. Pokud po dokončení odpovědi dojde k nepřečteným datům textu žádosti, server odešle snímek HTTP/2 RST. Další datové snímky požadavku jsou ignorovány.

Pokud je to možné, je lepší, aby klienti využili hlavičku žádosti [očekávat: 100-pokračovat](https://developer.mozilla.org/docs/Web/HTTP/Status/100) a čekali na odpověď serveru před zahájením odesílání textu žádosti. Díky tomu může klient příležitost před odesláním nepotřebných dat ověřit odpověď a zrušit jejich přerušení.

## <a name="additional-resources"></a>Další zdroje

* Při použití soketů UNIX v systému Linux se soket při vypnutí aplikace automaticky neodstraní. Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/aspnetcore/issues/14134).
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [RFC 7230: syntaxe zprávy a směrování (oddíl 5,4: hostitel)](https://tools.ietf.org/html/rfc7230#section-5.4)

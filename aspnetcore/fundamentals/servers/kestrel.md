---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: rick-anderson
description: Seznamte se s Kestrelem, webovým serverem pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 18846d60fd5c29f17cb4e59192795fd92251e2d0
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976765"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a>Implementace webového serveru Kestrel v ASP.NET Core

Tom [Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), a Stephen [Halter](https://twitter.com/halter73)

::: moniker range=">= aspnetcore-3.0"

Kestrel je multiplatformní [webový server pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je ve výchozím nastavení zahrnut v šablonách projektu ASP.NET Core.

Poštolka podporuje následující scénáře:

* HTTPS
* Neprůhledný upgrade používaný k povolení [websocketů](https://github.com/aspnet/websockets)
* Unixové zásuvky pro vysoký výkon za Nginx
* HTTP/2 (kromě macOS)&dagger;

&dagger;HTTP/2 bude v budoucí verzi v systému macOS podporován.

Kestrel je podporován na všech platformách a verzích, které podporuje .NET Core.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="http2-support"></a>Podpora HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET základní aplikace, pokud jsou splněny následující základní požadavky:

* Operační systém&dagger;
  * Windows Server 2016/Windows 10 nebo novější&Dagger;
  * Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16.04 nebo novější)
* Cílová architektura: .NET Core 2.2 nebo novější
* Připojení [protokolu ap (APPLICATION-Layer Protocol) (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Připojení TLS 1.2 nebo novější

&dagger;HTTP/2 bude v budoucí verzi v systému macOS podporován.
&Dagger;Kestrel má omezenou podporu pro HTTP/2 v systémech Windows Server 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS dostupných v těchto operačních systémech je omezený. K zabezpečení připojení TLS může být vyžadován certifikát generovaný pomocí algoritmu digitálního podpisu eliptické křivky (ECDSA).

Pokud je navázáno připojení HTTP/2, `HTTP/2`protokol [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) hlásí .

Protokol HTTP/2 je ve výchozím nastavení zakázán. Další informace o konfiguraci naleznete v části [Možnosti kestrelu](#kestrel-options) a [ListenOptions.Protocols.](#listenoptionsprotocols)

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kdy použít Kestrel s reverzním proxy serverem

Poštolka může být použita sama o sobě nebo s *reverzním proxy serverem*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP ze sítě a předá je ke strel.

Poštolka používá jako okraj (Internet-čelí) webový server:

![Poštolka komunikuje přímo s Internetem bez reverzního proxy serveru](kestrel/_static/kestrel-to-internet2.png)

Poštolka použitá v konfiguraci reverzního proxy serveru:

![Poštolka komunikuje nepřímo s Internetem prostřednictvím reverzního proxy serveru, jako je služba IIS, Nginx nebo Apache](kestrel/_static/kestrel-to-internet.png)

Konfigurace, s reverzním proxy serverem nebo bez něj, je podporovaná konfigurace hostování.

Kestrel používaný jako hraniční server bez reverzního proxy serveru nepodporuje sdílení stejné IP a portu mezi více procesy. Když kestrel je nakonfigurován pro poslech na portu, Kestrel zpracovává všechny přenosy pro tento port bez ohledu na hlavičky požadavků.When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers. Reverzní proxy server, který může sdílet porty, má schopnost předávat požadavky Kestrelu na jedinečné IP a portu.

I v případě, že reverzní proxy server není vyžadován, použití reverzní proxy server může být dobrou volbou.

Reverzní proxy server:

* Může omezit exponované veřejné plochy aplikací, které hostuje.
* Poskytněte další vrstvu konfigurace a obrany.
* Mohlo by se lépe integrovat se stávající infrastrukturou.
* Zjednodušte vyrovnávání zatížení a zabezpečenou komunikaci (HTTPS). Pouze reverzní proxy server vyžaduje certifikát X.509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.

> [!WARNING]
> Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).

## <a name="kestrel-in-aspnet-core-apps"></a>Poštolka v aplikacích ASP.NET Core

ASP.NET Šablony projektu Core používají kestrel ve výchozím nastavení. V *Program.cs* <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>metody :

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

Další informace o vytváření hostitele najdete v tématu Nastavení <xref:fundamentals/host/generic-host#set-up-a-host> *hostitele* a Výchozí nastavení *tvůrce* .

Chcete-li po `ConfigureWebHostDefaults`volání `ConfigureKestrel`poskytnout další konfiguraci , použijte :

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

## <a name="kestrel-options"></a>Možnosti poštolky

Webový server Kestrel má možnosti konfigurace omezení, které jsou užitečné zejména v internetových nasazeních.

Nastavte omezení vlastnosti <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Vlastnost `Limits` obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.

V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

V příkladech uvedených dále v tomto článku kestrel možnosti jsou konfigurovány v kódu Jazyka C#. Možnosti kestrelu lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index). Zprostředkovatel konfigurace [souborů](xref:fundamentals/configuration/index#file-configuration-provider) může například načíst konfiguraci kestrelu z souboru *appsettings.json* nebo *appsettings.{ Prostředí}.json:*

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[a konfigurace koncového bodu](#endpoint-configuration) jsou konfigurovatelné od poskytovatelů konfigurace. Zbývající kestrel konfigurace musí být nakonfigurován v kódu Jazyka C#.

Použijte **jeden** z následujících přístupů:

* Konfigurovat poštolek `Startup.ConfigureServices`v :

  1. Vstříkněte `IConfiguration` `Startup` instanci do třídy. Následující příklad předpokládá, že vstřikovaná `Configuration` konfigurace je přiřazena vlastnosti.
  2. V `Startup.ConfigureServices`, `Kestrel` načtěte část konfigurace do konfigurace Kestrel:

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

* Konfigurace kestrelu při vytváření hostitele:

  V *Program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrelu:

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

Oba předchozí přístupy pracují s libovolným [zprostředkovatelem konfigurace](xref:fundamentals/configuration/index).

### <a name="keep-alive-timeout"></a>Časový limit udržování života

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Získá nebo nastaví [časový limit keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5). Výchozí hodnota je 2 minuty.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>Maximální počet připojení klientů

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Maximální počet souběžných otevřených připojení TCP lze nastavit pro celou aplikaci s následujícím kódem:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Existuje samostatné omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na požadavek WebSockets). Po upgradu připojení se nezapočítává `MaxConcurrentConnections` do limitu.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Maximální počet připojení je ve výchozím nastavení neomezený (null).

### <a name="maximum-request-body-size"></a>Maximální velikost těla požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Výchozí maximální velikost těla požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.

Doporučený přístup k přepsání limitu v aplikaci ASP.NET <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> Core MVC je použití atributu na metodě akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci při každém požadavku:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Přepište nastavení na konkrétní požadavek v middlewaru:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Výjimka je vyvolána, pokud aplikace konfiguruje limit na požadavek poté, co aplikace začala číst požadavek. Je `IsReadOnly` vlastnost, která označuje, `MaxRequestBodySize` pokud je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě na konfiguraci limitu.

Pokud je aplikace [spuštěna mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [základnímodul](xref:host-and-deploy/aspnet-core-module), kestrel požadavek body limit je zakázán, protože iis již nastaví limit.

### <a name="minimum-request-body-data-rate"></a>Minimální přenosová rychlost tělo požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Poštolka kontroluje každou sekundu, pokud data přicházejí na zadanou rychlost v bajtů za sekundu. Pokud rychlost klesne pod minimum, je časový limit připojení. Doba odkladu je doba, kterou Kestrel dává klientovi ke zvýšení míry odeslání až na minimum; během této doby není sazba kontrolována. Období odkladu pomáhá zabránit přetažení připojení, které jsou zpočátku odesílání dat pomalým tempem z důvodu tcp pomalé spuštění.

Výchozí minimální sazba je 240 bajtů za sekundu s 5 sekundovou lhůtou odkladu.

Minimální sazba se vztahuje také na odpověď. Kód pro nastavení limitu požadavku a limitu odpovědi `RequestBody` `Response` je stejný s výjimkou s nebo v názvy vlastností a rozhraní.

Zde je příklad, který ukazuje, jak nakonfigurovat minimální rychlost dat v *Program.cs*:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Přepsat minimální limity rychlosti na požadavek v middlewaru:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Odkazováno <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> v předchozím vzorku není `HttpContext.Features` k dispozici v pro požadavky HTTP/2, protože změna omezení rychlosti na základě požadavku není obecně podporována pro HTTP/2 z důvodu podpory protokolu pro multiplexování požadavků. Je <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> však stále `HttpContext.Features` k dispozici pro požadavky HTTP/2, protože limit rychlosti čtení lze stále `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` *zcela zakázat* na základě požadavku nastavením i pro požadavek HTTP/2. Pokus o `IHttpMinRequestBodyDataRateFeature.MinDataRate` čtení nebo pokus u jeho nastavení `null` na jinou hodnotu bude mít za následek `NotSupportedException` vyvolání daného požadavku HTTP/2.

Limity rychlosti na `KestrelServerOptions.Limits` celém serveru nakonfigurované prostřednictvím připojení HTTP/1.x i HTTP/2 stále platí.

### <a name="request-headers-timeout"></a>Časový výtažek záhlaví požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Získá nebo nastaví maximální množství času, které server stráví příjem hlavičky požadavku. Výchozí hodnota je 30 sekund.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a>Maximální počet datových proudů na připojení

`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2. Přebytečné proudy jsou odmítnuty.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

Výchozí hodnota je 100.

### <a name="header-table-size"></a>Velikost tabulky záhlaví

Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2. `Http2.HeaderTableSize`omezuje velikost tabulky komprese záhlaví, kterou dekodér HPACK používá. Hodnota je uvedena v oktetech a musí být větší než nula (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

Výchozí hodnota je 4096.

### <a name="maximum-frame-size"></a>Maximální velikost rámu

`Http2.MaxFrameSize`označuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijaté nebo odeslané serverem. Hodnota je uvedena v oktetech a musí být mezi 2^14 (16 384) a 2^24-1 (16 777 215).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

Výchozí hodnota je 2^14 (16 384).

### <a name="maximum-request-header-size"></a>Maximální velikost hlavičky požadavku

`Http2.MaxRequestHeaderFieldSize`označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku. Toto omezení platí pro název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentacích. Hodnota musí být větší než nula (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

Výchozí hodnota je 8 192.

### <a name="initial-connection-window-size"></a>Počáteční velikost okna připojení

`Http2.InitialConnectionWindowSize`označuje maximální data těla požadavků v bajtech vyrovnávacích pamětí serveru najednou agregované ve všech požadavcích (datových proudech) na připojení. Žádosti jsou také `Http2.InitialStreamWindowSize`omezeny . Hodnota musí být větší nebo rovna 65 535 a menší než 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

Výchozí hodnota je 128 kB (131 072).

### <a name="initial-stream-window-size"></a>Počáteční velikost okna datového proudu

`Http2.InitialStreamWindowSize`označuje maximální data těla požadavku v bajtů vyrovnávacích pamětí serveru najednou na jeden požadavek (datový proud). Žádosti jsou také `Http2.InitialConnectionWindowSize`omezeny . Hodnota musí být větší nebo rovna 65 535 a menší než 2^31 (2 147 483 648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

Výchozí hodnota je 96 KB (98 304).

### <a name="synchronous-io"></a>Synchronní vstupně-výstupní operace

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>určuje, zda je pro požadavek a odpověď povolena synchronní vstupně-va. Výchozí hodnota je `false`.

> [!WARNING]
> Velký počet blokování synchronní vstupně-va operace může vést k nedostatku fondu vláken, což způsobí, že aplikace nereaguje. Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní vstupně-v.A/O.

Následující příklad umožňuje synchronní vstupně-va:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Informace o dalších možnostech a limitech kestrelu naleznete v těchto tématech:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Ve výchozím nastavení se ASP.NET jádro váže na:

* `http://localhost:5000`
* `https://localhost:5001`(pokud je k dispozici certifikát místního rozvoje)

Zadejte adresy URL pomocí:

* `ASPNETCORE_URLS`proměnné prostředí.
* `--urls`argument příkazového řádku.
* `urls`konfiguračního klíče hostitele.
* `UseUrls`rozšíření.

Hodnota poskytovaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený `"Urls": "http://localhost:8000;http://localhost:8001"`středníkem (například).

Další informace o těchto přístupech naleznete v [tématu Adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [konfigurace override](xref:fundamentals/host/web-host#override-configuration).

Je vytvořen vývojový certifikát:

* Při instalaci sady [.NET Core SDK.](/dotnet/core/sdk)
* Nástroj [dev-certs](xref:aspnetcore-2.1#https) se používá k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení výslovného oprávnění důvěřovat certifikátu místního vývoje.

Šablony projektů konfigurují aplikace tak, aby se ve výchozím nastavení spouštěly na protokolu HTTPS a zahrnovaly [přesměrování HTTPS a podporu HSTS](xref:security/enforcing-ssl).

Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody na konfiguraci URL předpony a porty pro Kestrel.

`UseUrls`, `--urls` argument příkazového `urls` řádku, konfigurační klíč hostitele a proměnná `ASPNETCORE_URLS` prostředí také fungují, ale omezení jsou zaznamenána dále v této části (výchozí certifikát musí být k dispozici pro konfiguraci koncového bodu HTTPS).

`KestrelServerOptions`Konfigurace:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (akce\<ListenOptions>)

Určuje konfiguraci, `Action` která má být spuštěna pro každý zadaný koncový bod. Volání `ConfigureEndpointDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.

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
> Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou mít výchozí hodnoty.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>KonfigurovatHttpsDefaults (akce\<HttpsConnectionAdapterOptions>)

Určuje konfiguraci, `Action` která má být spuštěna pro každý koncový bod HTTPS. Volání `ConfigureHttpsDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.

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
> Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou mít výchozí hodnoty.

### <a name="configureiconfiguration"></a>Configure(IKonfigurace)

Vytvoří konfigurační zavaděč pro <xref:Microsoft.Extensions.Configuration.IConfiguration> nastavení Kestrel, který bere jako vstup. Konfigurace musí být vymezena na konfigurační sekci pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel pro použití protokolu HTTPS.

`ListenOptions.UseHttps`Rozšíření:

* `UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal protokol HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
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

`ListenOptions.UseHttps`Parametry:

* `filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.
* `password`je heslo potřebné pro přístup k datům certifikátu X.509.
* `configureOptions`je `Action` konfigurace rozhraní `HttpsConnectionAdapterOptions`. Vrátí `ListenOptions`hodnotu .
* `storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.
* `subject`je název subjektu certifikátu.
* `allowInvalid`označuje, zda by měly být považovány za neplatné certifikáty, například certifikáty podepsané svým držitelem.
* `location`je umístění úložiště, ze kterých se má certifikát načíst.
* `serverCertificate`je certifikát X.509.

V produkčním prostředí musí být protokol HTTPS explicitně nakonfigurován. Musí být poskytnut minimálně výchozí certifikát.

Podporované konfigurace popsané dále:

* Žádná konfigurace
* Nahrazení výchozího certifikátu z konfigurace
* Změna výchozích hodnot v kódu

*Žádná konfigurace*

Poštolka naslouchá `http://localhost:5000` dál `https://localhost:5001` a (pokud je k dispozici výchozí certifikát).

<a name="configuration"></a>

*Nahrazení výchozího certifikátu z konfigurace*

`CreateDefaultBuilder`ve `Configure(context.Configuration.GetSection("Kestrel"))` výchozím nastavení načítá konfiguraci Kestrel. Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS. Nakonfigurujte více koncových bodů, včetně adres URL a certifikátů, které mají být používány, ze souboru na disku nebo z úložiště certifikátů.

V následujícím příkladu *appsettings.json:*

* Nastavte **AllowInvalid** na `true` povolení použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).
* Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu) přejde zpět na certifikát definovaný v části **Výchozí** **certifikáty** > nebo vývojový certifikát.

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

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je určení certifikátu pomocí polí úložiště certifikátů. **Například certifikát výchozí** certifikát **y** > lze zadat jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Poznámky schématu:

* Názvy koncových bodů nerozlišují malá a velká písmena. Například `HTTPS` a `Https` jsou platné.
* Parametr `Url` je vyžadován pro každý koncový bod. Formát tohoto parametru je stejný jako `Urls` parametr konfigurace nejvyšší úrovně s tím rozdílem, že je omezen na jednu hodnotu.
* Tyto koncové body nahradit ty definované `Urls` v konfiguraci nejvyšší úrovně, nikoli jejich přidání. Koncové body definované `Listen` v kódu prostřednictvím jsou kumulativní s koncovými body definovanými v konfigurační části.
* Sekce `Certificate` je nepovinná. Pokud `Certificate` oddíl není zadán, použijí se výchozí hodnoty definované v předchozích scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, server vyvolá výjimku a nepodaří se spustit.
* V `Certificate` této části jsou certifikáty**hesel** **cesty**&ndash;i**úložiště** **předmětů.**&ndash;
* Tímto způsobem může být definován libovolný počet koncových bodů, pokud nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`vrátí `KestrelConfigurationLoader` metodu `.Endpoint(string name, listenOptions => { })` a s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:

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

`KestrelServerOptions.ConfigurationLoader`lze přímo přistupovat k pokračování iterace na stávajícím nakladači, jako je například ten, který <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>poskytuje .

* Konfigurační oddíl pro každý koncový bod `Endpoint` je k dispozici na možnosti v metodě tak, aby vlastní nastavení může být číst.
* Více konfigurací může být `options.Configure(context.Configuration.GetSection("{SECTION}"))` načteno voláním znovu s jiným oddílem. Používá se pouze poslední `Load` konfigurace, pokud není explicitně volána na předchozí instance. Metabalíček nevolá, `Load` takže jeho výchozí konfigurační část může být nahrazena.
* `KestrelConfigurationLoader`zrcadlí `Listen` rodinu API z `KestrelServerOptions` `Endpoint` jako přetížení, takže kód a koncové body konfigurace mohou být nakonfigurovány na stejném místě. Tato přetížení nepoužívají názvy a spotřebovávají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` lze použít ke změně `ListenOptions` `HttpsConnectionAdapterOptions`výchozího nastavení pro a , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací všech koncových bodů.

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

*Podpora pro Kestrel pro SNI*

[Označení názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné adrese IP a portu. Aby sni fungovalo, klient odešle název hostitele pro zabezpečenou relaci na server během tls handshake tak, aby server mohl poskytnout správný certifikát. Klient používá zařízený certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje po handshake TLS.

Kestrel podporuje SNI `ServerCertificateSelector` prostřednictvím zpětného volání. Zpětné volání je vyvoláno jednou za připojení, aby aplikace mohla zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Spuštění na `netcoreapp2.1` cílové rozhraní nebo později. Na `net461` nebo novější zpětné volání je `name` vyvolána, ale je vždy `null`. Je `name` také `null` v případě, že klient neposkytuje parametr název hostitele v tls handshake.
* Všechny webové stránky běží na stejné instanci Kestrel. Kestrel nepodporuje sdílení IP adresy a portu ve více instancích bez reverzního proxy serveru.

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

Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> vyzařovat protokoly úrovně ladění pro komunikaci na úrovni bajtů na připojení. Protokolování připojení je užitečné při řešení problémů v nízkoúrovňové komunikaci, například během šifrování TLS a za proxy servery. Pokud `UseConnectionLogging` je `UseHttps`umístěn před , šifrovaný provoz je zaznamenána. Pokud `UseConnectionLogging` je `UseHttps`umístěn po , dešifrovaný provoz je zaznamenána.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Vazba na soket TCP

Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže na soket TCP a možnosti lambda umožňuje konfiguraci certifikátu X.509:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

Příklad konfiguruje protokol <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS pro koncový bod s aplikací . Stejné rozhraní API použijte ke konfiguraci dalších nastavení kestrelu pro konkrétní koncové body.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vazba na unixovou zásuvku

Poslouchejte na unixovém soketu s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro lepší výkon s Nginx, jak je znázorněno v tomto příkladu:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* V konfiguračním souboru `server`  >  `location`  >  `proxy_pass` Nginx nastavte položku na `http://unix:/tmp/{KESTREL SOCKET}:/;`. `{KESTREL SOCKET}`je název soketu k <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> dispozici (například `kestrel-test.sock` v předchozím příkladu).
* Ujistěte se, že soket je zapisovatelný Nginx `chmod go+w /tmp/kestrel-test.sock`(například).

### <a name="port-0"></a>Port 0

Je-li `0` zadáno číslo portu, kestrel dynamicky se váže na dostupný port. Následující příklad ukazuje, jak určit, který port Kestrel skutečně vázán za běhu:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Při spuštění aplikace výstup okna konzoly označuje dynamický port, kde lze aplikaci dosáhnout:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Omezení

Nakonfigurujte koncové body pomocí následujících přístupů:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`argument příkazového řádku
* `urls`konfigurační klíč hostitele
* `ASPNETCORE_URLS`proměnná prostředí

Tyto metody jsou užitečné pro práci s kódem s jinými servery než Kestrel. Mějte však na paměti následující omezení:

* Protokol HTTPS nelze použít s těmito přístupy, pokud výchozí certifikát je k `KestrelServerOptions` dispozici v konfiguraci koncového bodu HTTPS (například pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při obou `Listen` `UseUrls` a přístupy jsou `Listen` používány současně, `UseUrls` koncové body přepsat koncové body.

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adres URL pro `Listen` `UseUrls`vazby přepsání služby IIS nastaveny buď nebo . Další informace naleznete v [tématu ASP.NET základní modul.](xref:host-and-deploy/aspnet-core-module)

### <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

Vlastnost `Protocols` vytvoří protokoly HTTP`HttpProtocols`( ) povolené na koncovém bodu připojení nebo pro server. Přiřaďte `Protocols` hodnotu `HttpProtocols` vlastnosti z výčtu.

| `HttpProtocols`Hodnota výčtu | Protokol připojení povolen |
| -------------------------- | ----------------------------- |
| `Http1`                    | pouze http/1.1. Lze použít s TLS nebo bez něj. |
| `Http2`                    | pouze http/2. Bez TLS lze použít pouze v případě, že klient podporuje [režim předchozí znalosti](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 a HTTP/2. Protokol HTTP/2 vyžaduje, aby klient v příkazu handshake (TLS [Application-Layer Protocol)](https://tools.ietf.org/html/rfc7301#section-3) vybral protokol HTTP/2. v opačném případě je výchozí připojení http/1.1. |

Výchozí `ListenOptions.Protocols` hodnota pro libovolný `HttpProtocols.Http1AndHttp2`koncový bod je .

Omezení TLS pro HTTP/2:

* TLS verze 1.2 nebo novější
* Opětovné vyjednávání zakázáno.
* Komprese zakázána
* Minimální velikosti dočasných klíčů:
  * Eliptická křivka Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bitů minimum
  * Konečné pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; minimálně 2048 bitů
* Šifrovací sada není na černé listině

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; s eliptickou křivkou P-256 je ve výchozím nastavení podporována. `TLS-ECDHE` &rbrack;

Následující příklad umožňuje připojení HTTP/1.1 a HTTP/2 na portu 8000. Připojení jsou zabezpečena TLS s dodaným certifikátem:

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

Pomocí middlewaru připojení můžete v případě potřeby filtrovat tls handshakes na základě jednotlivých připojení pro konkrétní šifry.

Následující příklad vyvolá <xref:System.NotSupportedException> jakýkoli algoritmus šifry, který aplikace nepodporuje. Alternativně definujte a porovnejte [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) se seznamem přijatelných šifrovacích sad.

S šifrovacím algoritmem [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) se nepoužívá žádné šifrování.

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

Filtrování připojení lze také nakonfigurovat pomocí <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:

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

Na Linuxu, <xref:System.Net.Security.CipherSuitesPolicy> lze filtrovat TLS handshakes na základě připojení:

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

*Nastavení protokolu z konfigurace*

`CreateDefaultBuilder`ve `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` výchozím nastavení načítá konfiguraci Kestrel.

Následující příklad *appsettings.json* vytvoří HTTP/1.1 jako výchozí protokol připojení pro všechny koncové body:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

Následující příklad *appsettings.json* vytvoří protokol připojení HTTP/1.1 pro konkrétní koncový bod:

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

Protokoly zadané v hodnotách přepsání kódu nastavených konfigurací.

## <a name="transport-configuration"></a>Konfigurace přenosu

Pro projekty, které vyžadují použití<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>Libuv ( ):

* Přidejte závislost pro balíček [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do souboru projektu aplikace:

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* Volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> na `IWebHostBuilder`:

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

### <a name="url-prefixes"></a>Předpony adres URL

Při `UseUrls`použití `--urls` , argument `urls` příkazového řádku, `ASPNETCORE_URLS` konfigurační klíč hostitele nebo proměnné prostředí, předpony URL může být v některém z následujících formátů.

Platné jsou pouze předpony adresy URL HTTP. Kestrel nepodporuje protokol HTTPS při konfiguraci `UseUrls`vazeb URL pomocí .

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`je zvláštní případ, který se váže na všechny adresy IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`je ekvivalent IPv6 iPv4 `0.0.0.0`.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy `*`hostitelů `+`, a , nejsou zvláštní. Cokoli, co není rozpoznáno `localhost` jako platná adresa IP nebo se váže na všechny IP adresy IPv4 a IPv6. Chcete-li svázat různé názvy hostitelů s různými aplikacemi ASP.NET Core na stejném portu, použijte [http.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je Služba IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).

* Název `localhost` hostitele s číslem portu nebo ip adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Je-li `localhost` zadáno, kestrel se pokusí vytvořit vazbu na rozhraní zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na obou rozhraních zpětné smyčky, kestrel se nepodaří spustit. Pokud buď rozhraní zpětné smyčky není k dispozici z jakéhokoli jiného důvodu (nejčastěji proto, že Není podporován protokol Empv6), Kestrel protokoly upozornění.

## <a name="host-filtering"></a>Filtrování hostitelů

Zatímco Kestrel podporuje konfiguraci založenou na předponami, jako je například `http://example.com:5000`, Kestrel do značné míry ignoruje název hostitele. Hostitel `localhost` je zvláštní případ používaný pro vazbu na adresy zpětné smyčky. Jakýkoli hostitel jiný než explicitní adresa IP se váže na všechny veřejné IP adresy. `Host`záhlaví nejsou ověřena.

Jako řešení použijte middleware filtrování hostitelů. Middleware filtrování hostitelů poskytuje balíček [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) který je implicitně k dispozici pro ASP.NET aplikace Core. Middleware je přidán <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>který volá :

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware filtrování hostitelů je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.json*/*appsettings.\< EnvironmentName>.json*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Předávaných záhlaví Middleware](xref:host-and-deploy/proxy-load-balancer) má také možnost. <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> Předané hlavičky Middleware a Host Filtrování Middleware mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` pomocí middlewaru s předanými hlavičkami je vhodné, pokud `Host` záhlaví není zachováno při předávání požadavků pomocí reverzního proxy serveru nebo vyrovnávání zatížení. Nastavení `AllowedHosts` s Middleware filtrování hostitelů je vhodné, pokud je Kestrel `Host` používán jako veřejný hraniční server nebo když je záhlaví přímo předáno dál.
>
> Další informace o middlewaru s <xref:host-and-deploy/proxy-load-balancer>předaných záhlaví naleznete v tématu .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kestrel je multiplatformní [webový server pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je ve výchozím nastavení zahrnut v šablonách projektu ASP.NET Core.

Poštolka podporuje následující scénáře:

* HTTPS
* Neprůhledný upgrade používaný k povolení [websocketů](https://github.com/aspnet/websockets)
* Unixové zásuvky pro vysoký výkon za Nginx
* HTTP/2 (kromě macOS)&dagger;

&dagger;HTTP/2 bude v budoucí verzi v systému macOS podporován.

Kestrel je podporován na všech platformách a verzích, které podporuje .NET Core.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="http2-support"></a>Podpora HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET základní aplikace, pokud jsou splněny následující základní požadavky:

* Operační systém&dagger;
  * Windows Server 2016/Windows 10 nebo novější&Dagger;
  * Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16.04 nebo novější)
* Cílová architektura: .NET Core 2.2 nebo novější
* Připojení [protokolu ap (APPLICATION-Layer Protocol) (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)
* Připojení TLS 1.2 nebo novější

&dagger;HTTP/2 bude v budoucí verzi v systému macOS podporován.
&Dagger;Kestrel má omezenou podporu pro HTTP/2 v systémech Windows Server 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS dostupných v těchto operačních systémech je omezený. K zabezpečení připojení TLS může být vyžadován certifikát generovaný pomocí algoritmu digitálního podpisu eliptické křivky (ECDSA).

Pokud je navázáno připojení HTTP/2, `HTTP/2`protokol [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) hlásí .

Protokol HTTP/2 je ve výchozím nastavení zakázán. Další informace o konfiguraci naleznete v části [Možnosti kestrelu](#kestrel-options) a [ListenOptions.Protocols.](#listenoptionsprotocols)

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kdy použít Kestrel s reverzním proxy serverem

Poštolka může být použita sama o sobě nebo s *reverzním proxy serverem*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP ze sítě a předá je ke strel.

Poštolka používá jako okraj (Internet-čelí) webový server:

![Poštolka komunikuje přímo s Internetem bez reverzního proxy serveru](kestrel/_static/kestrel-to-internet2.png)

Poštolka použitá v konfiguraci reverzního proxy serveru:

![Poštolka komunikuje nepřímo s Internetem prostřednictvím reverzního proxy serveru, jako je služba IIS, Nginx nebo Apache](kestrel/_static/kestrel-to-internet.png)

Konfigurace, s reverzním proxy serverem nebo bez něj, je podporovaná konfigurace hostování.

Kestrel používaný jako hraniční server bez reverzního proxy serveru nepodporuje sdílení stejné IP a portu mezi více procesy. Když kestrel je nakonfigurován pro poslech na portu, Kestrel zpracovává všechny přenosy pro tento port bez ohledu na hlavičky požadavků.When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers. Reverzní proxy server, který může sdílet porty, má schopnost předávat požadavky Kestrelu na jedinečné IP a portu.

I v případě, že reverzní proxy server není vyžadován, použití reverzní proxy server může být dobrou volbou.

Reverzní proxy server:

* Může omezit exponované veřejné plochy aplikací, které hostuje.
* Poskytněte další vrstvu konfigurace a obrany.
* Mohlo by se lépe integrovat se stávající infrastrukturou.
* Zjednodušte vyrovnávání zatížení a zabezpečenou komunikaci (HTTPS). Pouze reverzní proxy server vyžaduje certifikát X.509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.

> [!WARNING]
> Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Jak používat Kestrel v aplikacích ASP.NET Core

Balíček [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

ASP.NET Šablony projektu Core používají kestrel ve výchozím nastavení. V *Program.cs*volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>kódu šablony <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , který volá na pozadí.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Další informace `CreateDefaultBuilder` o hostiteli a jeho vytváření naleznete <xref:fundamentals/host/web-host#set-up-a-host>v části Nastavení *hostitele* v .

Chcete-li po `CreateDefaultBuilder`volání `ConfigureKestrel`poskytnout další konfiguraci , použijte :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

Pokud aplikace `CreateDefaultBuilder` nevolá pro nastavení hostitele, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> zavolejte `ConfigureKestrel` **před** voláním :

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

## <a name="kestrel-options"></a>Možnosti poštolky

Webový server Kestrel má možnosti konfigurace omezení, které jsou užitečné zejména v internetových nasazeních.

Nastavte omezení vlastnosti <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Vlastnost `Limits` obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.

V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Možnosti kestrelu, které jsou konfigurovány v kódu Jazyka C# v následujících příkladech, lze také nastavit pomocí [zprostředkovatele konfigurace](xref:fundamentals/configuration/index). Zprostředkovatel konfigurace souborů může například načíst konfiguraci kestrelu z souboru *appsettings.json* nebo *appsettings.{ Prostředí}.json:*

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

* Konfigurovat poštolek `Startup.ConfigureServices`v :

  1. Vstříkněte `IConfiguration` `Startup` instanci do třídy. Následující příklad předpokládá, že vstřikovaná `Configuration` konfigurace je přiřazena vlastnosti.
  2. V `Startup.ConfigureServices`, `Kestrel` načtěte část konfigurace do konfigurace Kestrel:

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

* Konfigurace kestrelu při vytváření hostitele:

  V *Program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrelu:

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

Oba předchozí přístupy pracují s libovolným [zprostředkovatelem konfigurace](xref:fundamentals/configuration/index).

### <a name="keep-alive-timeout"></a>Časový limit udržování života

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Získá nebo nastaví [časový limit keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5). Výchozí hodnota je 2 minuty.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a>Maximální počet připojení klientů

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Maximální počet souběžných otevřených připojení TCP lze nastavit pro celou aplikaci s následujícím kódem:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Existuje samostatné omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na požadavek WebSockets). Po upgradu připojení se nezapočítává `MaxConcurrentConnections` do limitu.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Maximální počet připojení je ve výchozím nastavení neomezený (null).

### <a name="maximum-request-body-size"></a>Maximální velikost těla požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Výchozí maximální velikost těla požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.

Doporučený přístup k přepsání limitu v aplikaci ASP.NET <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> Core MVC je použití atributu na metodě akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci při každém požadavku:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Přepište nastavení na konkrétní požadavek v middlewaru:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Výjimka je vyvolána, pokud aplikace konfiguruje limit na požadavek poté, co aplikace začala číst požadavek. Je `IsReadOnly` vlastnost, která označuje, `MaxRequestBodySize` pokud je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě na konfiguraci limitu.

Pokud je aplikace [spuštěna mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [základnímodul](xref:host-and-deploy/aspnet-core-module), kestrel požadavek body limit je zakázán, protože iis již nastaví limit.

### <a name="minimum-request-body-data-rate"></a>Minimální přenosová rychlost tělo požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Poštolka kontroluje každou sekundu, pokud data přicházejí na zadanou rychlost v bajtů za sekundu. Pokud rychlost klesne pod minimum, je časový limit připojení. Doba odkladu je doba, kterou Kestrel dává klientovi ke zvýšení míry odeslání až na minimum; během této doby není sazba kontrolována. Období odkladu pomáhá zabránit přetažení připojení, které jsou zpočátku odesílání dat pomalým tempem z důvodu tcp pomalé spuštění.

Výchozí minimální sazba je 240 bajtů za sekundu s 5 sekundovou lhůtou odkladu.

Minimální sazba se vztahuje také na odpověď. Kód pro nastavení limitu požadavku a limitu odpovědi `RequestBody` `Response` je stejný s výjimkou s nebo v názvy vlastností a rozhraní.

Zde je příklad, který ukazuje, jak nakonfigurovat minimální rychlost dat v *Program.cs*:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

Přepsat minimální limity rychlosti na požadavek v middlewaru:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Pro požadavky HTTP/2 nejsou k dispozici `HttpContext.Features` ani funkce rychlosti, na kterou odkazuje předchozí ukázka, protože úprava omezení rychlosti na základě požadavku není pro protokol HTTP/2 podporována z důvodu podpory protokolu pro multiplexování požadavků. Limity rychlosti na `KestrelServerOptions.Limits` celém serveru nakonfigurované prostřednictvím připojení HTTP/1.x i HTTP/2 stále platí.

### <a name="request-headers-timeout"></a>Časový výtažek záhlaví požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Získá nebo nastaví maximální množství času, které server stráví příjem hlavičky požadavku. Výchozí hodnota je 30 sekund.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a>Maximální počet datových proudů na připojení

`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2. Přebytečné proudy jsou odmítnuty.

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

### <a name="header-table-size"></a>Velikost tabulky záhlaví

Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2. `Http2.HeaderTableSize`omezuje velikost tabulky komprese záhlaví, kterou dekodér HPACK používá. Hodnota je uvedena v oktetech a musí být větší než nula (0).

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

### <a name="maximum-frame-size"></a>Maximální velikost rámu

`Http2.MaxFrameSize`označuje maximální velikost datové části rámce připojení HTTP/2, kterou má být přijímána. Hodnota je uvedena v oktetech a musí být mezi 2^14 (16 384) a 2^24-1 (16 777 215).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

Výchozí hodnota je 2^14 (16 384).

### <a name="maximum-request-header-size"></a>Maximální velikost hlavičky požadavku

`Http2.MaxRequestHeaderFieldSize`označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku. Toto omezení platí pro název i hodnotu společně v jejich komprimované a nekomprimované reprezentace. Hodnota musí být větší než nula (0).

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

### <a name="initial-connection-window-size"></a>Počáteční velikost okna připojení

`Http2.InitialConnectionWindowSize`označuje maximální data těla požadavků v bajtech vyrovnávacích pamětí serveru najednou agregované ve všech požadavcích (datových proudech) na připojení. Žádosti jsou také `Http2.InitialStreamWindowSize`omezeny . Hodnota musí být větší nebo rovna 65 535 a menší než 2^31 (2 147 483 648).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

Výchozí hodnota je 128 kB (131 072).

### <a name="initial-stream-window-size"></a>Počáteční velikost okna datového proudu

`Http2.InitialStreamWindowSize`označuje maximální data těla požadavku v bajtů vyrovnávacích pamětí serveru najednou na jeden požadavek (datový proud). Žádosti jsou také `Http2.InitialStreamWindowSize`omezeny . Hodnota musí být větší nebo rovna 65 535 a menší než 2^31 (2 147 483 648).

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

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>určuje, zda je pro požadavek a odpověď povolena synchronní vstupně-va. Výchozí hodnota `true`je .

> [!WARNING]
> Velký počet blokování synchronní vstupně-va operace může vést k nedostatku fondu vláken, což způsobí, že aplikace nereaguje. Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní vstupně-v.A/O.

Následující příklad umožňuje synchronní vstupně-va:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Informace o dalších možnostech a limitech kestrelu naleznete v těchto tématech:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Ve výchozím nastavení se ASP.NET jádro váže na:

* `http://localhost:5000`
* `https://localhost:5001`(pokud je k dispozici certifikát místního rozvoje)

Zadejte adresy URL pomocí:

* `ASPNETCORE_URLS`proměnné prostředí.
* `--urls`argument příkazového řádku.
* `urls`konfiguračního klíče hostitele.
* `UseUrls`rozšíření.

Hodnota poskytovaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený `"Urls": "http://localhost:8000;http://localhost:8001"`středníkem (například).

Další informace o těchto přístupech naleznete v [tématu Adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [konfigurace override](xref:fundamentals/host/web-host#override-configuration).

Je vytvořen vývojový certifikát:

* Při instalaci sady [.NET Core SDK.](/dotnet/core/sdk)
* Nástroj [dev-certs](xref:aspnetcore-2.1#https) se používá k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení výslovného oprávnění důvěřovat certifikátu místního vývoje.

Šablony projektů konfigurují aplikace tak, aby se ve výchozím nastavení spouštěly na protokolu HTTPS a zahrnovaly [přesměrování HTTPS a podporu HSTS](xref:security/enforcing-ssl).

Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody na konfiguraci URL předpony a porty pro Kestrel.

`UseUrls`, `--urls` argument příkazového `urls` řádku, konfigurační klíč hostitele a proměnná `ASPNETCORE_URLS` prostředí také fungují, ale omezení jsou zaznamenána dále v této části (výchozí certifikát musí být k dispozici pro konfiguraci koncového bodu HTTPS).

`KestrelServerOptions`Konfigurace:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (akce\<ListenOptions>)

Určuje konfiguraci, `Action` která má být spuštěna pro každý zadaný koncový bod. Volání `ConfigureEndpointDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.

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
> Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou mít výchozí hodnoty.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>KonfigurovatHttpsDefaults (akce\<HttpsConnectionAdapterOptions>)

Určuje konfiguraci, `Action` která má být spuštěna pro každý koncový bod HTTPS. Volání `ConfigureHttpsDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.

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
> Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou mít výchozí hodnoty.


### <a name="configureiconfiguration"></a>Configure(IKonfigurace)

Vytvoří konfigurační zavaděč pro <xref:Microsoft.Extensions.Configuration.IConfiguration> nastavení Kestrel, který bere jako vstup. Konfigurace musí být vymezena na konfigurační sekci pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel pro použití protokolu HTTPS.

`ListenOptions.UseHttps`Rozšíření:

* `UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal protokol HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
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

`ListenOptions.UseHttps`Parametry:

* `filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.
* `password`je heslo potřebné pro přístup k datům certifikátu X.509.
* `configureOptions`je `Action` konfigurace rozhraní `HttpsConnectionAdapterOptions`. Vrátí `ListenOptions`hodnotu .
* `storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.
* `subject`je název subjektu certifikátu.
* `allowInvalid`označuje, zda by měly být považovány za neplatné certifikáty, například certifikáty podepsané svým držitelem.
* `location`je umístění úložiště, ze kterých se má certifikát načíst.
* `serverCertificate`je certifikát X.509.

V produkčním prostředí musí být protokol HTTPS explicitně nakonfigurován. Musí být poskytnut minimálně výchozí certifikát.

Podporované konfigurace popsané dále:

* Žádná konfigurace
* Nahrazení výchozího certifikátu z konfigurace
* Změna výchozích hodnot v kódu

*Žádná konfigurace*

Poštolka naslouchá `http://localhost:5000` dál `https://localhost:5001` a (pokud je k dispozici výchozí certifikát).

<a name="configuration"></a>

*Nahrazení výchozího certifikátu z konfigurace*

`CreateDefaultBuilder`ve `Configure(context.Configuration.GetSection("Kestrel"))` výchozím nastavení načítá konfiguraci Kestrel. Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS. Nakonfigurujte více koncových bodů, včetně adres URL a certifikátů, které mají být používány, ze souboru na disku nebo z úložiště certifikátů.

V následujícím příkladu *appsettings.json:*

* Nastavte **AllowInvalid** na `true` povolení použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).
* Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu) přejde zpět na certifikát definovaný v části **Výchozí** **certifikáty** > nebo vývojový certifikát.

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

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je určení certifikátu pomocí polí úložiště certifikátů. **Například certifikát výchozí** certifikát **y** > lze zadat jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Poznámky schématu:

* Názvy koncových bodů nerozlišují malá a velká písmena. Například `HTTPS` a `Https` jsou platné.
* Parametr `Url` je vyžadován pro každý koncový bod. Formát tohoto parametru je stejný jako `Urls` parametr konfigurace nejvyšší úrovně s tím rozdílem, že je omezen na jednu hodnotu.
* Tyto koncové body nahradit ty definované `Urls` v konfiguraci nejvyšší úrovně, nikoli jejich přidání. Koncové body definované `Listen` v kódu prostřednictvím jsou kumulativní s koncovými body definovanými v konfigurační části.
* Sekce `Certificate` je nepovinná. Pokud `Certificate` oddíl není zadán, použijí se výchozí hodnoty definované v předchozích scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, server vyvolá výjimku a nepodaří se spustit.
* V `Certificate` této části jsou certifikáty**hesel** **cesty**&ndash;i**úložiště** **předmětů.**&ndash;
* Tímto způsobem může být definován libovolný počet koncových bodů, pokud nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`vrátí `KestrelConfigurationLoader` metodu `.Endpoint(string name, listenOptions => { })` a s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:

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

`KestrelServerOptions.ConfigurationLoader`lze přímo přistupovat k pokračování iterace na stávajícím nakladači, jako je například ten, který <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>poskytuje .

* Konfigurační oddíl pro každý koncový bod `Endpoint` je k dispozici na možnosti v metodě tak, aby vlastní nastavení může být číst.
* Více konfigurací může být `options.Configure(context.Configuration.GetSection("{SECTION}"))` načteno voláním znovu s jiným oddílem. Používá se pouze poslední `Load` konfigurace, pokud není explicitně volána na předchozí instance. Metabalíček nevolá, `Load` takže jeho výchozí konfigurační část může být nahrazena.
* `KestrelConfigurationLoader`zrcadlí `Listen` rodinu API z `KestrelServerOptions` `Endpoint` jako přetížení, takže kód a koncové body konfigurace mohou být nakonfigurovány na stejném místě. Tato přetížení nepoužívají názvy a spotřebovávají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` lze použít ke změně `ListenOptions` `HttpsConnectionAdapterOptions`výchozího nastavení pro a , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací všech koncových bodů.

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

*Podpora pro Kestrel pro SNI*

[Označení názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné adrese IP a portu. Aby sni fungovalo, klient odešle název hostitele pro zabezpečenou relaci na server během tls handshake tak, aby server mohl poskytnout správný certifikát. Klient používá zařízený certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje po handshake TLS.

Kestrel podporuje SNI `ServerCertificateSelector` prostřednictvím zpětného volání. Zpětné volání je vyvoláno jednou za připojení, aby aplikace mohla zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Spuštění na `netcoreapp2.1` cílové rozhraní nebo později. Na `net461` nebo novější zpětné volání je `name` vyvolána, ale je vždy `null`. Je `name` také `null` v případě, že klient neposkytuje parametr název hostitele v tls handshake.
* Všechny webové stránky běží na stejné instanci Kestrel. Kestrel nepodporuje sdílení IP adresy a portu ve více instancích bez reverzního proxy serveru.

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

Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> vyzařovat protokoly úrovně ladění pro komunikaci na úrovni bajtů na připojení. Protokolování připojení je užitečné při řešení problémů v nízkoúrovňové komunikaci, například během šifrování TLS a za proxy servery. Pokud `UseConnectionLogging` je `UseHttps`umístěn před , šifrovaný provoz je zaznamenána. Pokud `UseConnectionLogging` je `UseHttps`umístěn po , dešifrovaný provoz je zaznamenána.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Vazba na soket TCP

Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže na soket TCP a možnosti lambda umožňuje konfiguraci certifikátu X.509:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

Příklad konfiguruje protokol <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS pro koncový bod s aplikací . Stejné rozhraní API použijte ke konfiguraci dalších nastavení kestrelu pro konkrétní koncové body.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vazba na unixovou zásuvku

Poslouchejte na unixovém soketu s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro lepší výkon s Nginx, jak je znázorněno v tomto příkladu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* V souboru Konfiguration Nginx `server`  >  `location`  >  `proxy_pass` nastavte `http://unix:/tmp/{KESTREL SOCKET}:/;`položku na . `{KESTREL SOCKET}`je název soketu k <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> dispozici (například `kestrel-test.sock` v předchozím příkladu).
* Ujistěte se, že soket je zapisovatelný Nginx `chmod go+w /tmp/kestrel-test.sock`(například). 

### <a name="port-0"></a>Port 0

Je-li `0` zadáno číslo portu, kestrel dynamicky se váže na dostupný port. Následující příklad ukazuje, jak určit, který port Kestrel skutečně vázán za běhu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Při spuštění aplikace výstup okna konzoly označuje dynamický port, kde lze aplikaci dosáhnout:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Omezení

Nakonfigurujte koncové body pomocí následujících přístupů:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`argument příkazového řádku
* `urls`konfigurační klíč hostitele
* `ASPNETCORE_URLS`proměnná prostředí

Tyto metody jsou užitečné pro práci s kódem s jinými servery než Kestrel. Mějte však na paměti následující omezení:

* Protokol HTTPS nelze použít s těmito přístupy, pokud výchozí certifikát je k `KestrelServerOptions` dispozici v konfiguraci koncového bodu HTTPS (například pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při obou `Listen` `UseUrls` a přístupy jsou `Listen` používány současně, `UseUrls` koncové body přepsat koncové body.

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adres URL pro `Listen` `UseUrls`vazby přepsání služby IIS nastaveny buď nebo . Další informace naleznete v [tématu ASP.NET základní modul.](xref:host-and-deploy/aspnet-core-module)

### <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

Vlastnost `Protocols` vytvoří protokoly HTTP`HttpProtocols`( ) povolené na koncovém bodu připojení nebo pro server. Přiřaďte `Protocols` hodnotu `HttpProtocols` vlastnosti z výčtu.

| `HttpProtocols`Hodnota výčtu | Protokol připojení povolen |
| -------------------------- | ----------------------------- |
| `Http1`                    | pouze http/1.1. Lze použít s TLS nebo bez něj. |
| `Http2`                    | pouze http/2. Bez TLS lze použít pouze v případě, že klient podporuje [režim předchozí znalosti](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 a HTTP/2. Protokol HTTP/2 vyžaduje připojení pro vyjednávání protokolu TLS a [aplikační vrstvy (ALPN).](https://tools.ietf.org/html/rfc7301#section-3) v opačném případě je výchozí připojení http/1.1. |

Výchozí protokol je HTTP/1.1.

Omezení TLS pro HTTP/2:

* TLS verze 1.2 nebo novější
* Opětovné vyjednávání zakázáno.
* Komprese zakázána
* Minimální velikosti dočasných klíčů:
  * Eliptická křivka Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bitů minimum
  * Konečné pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; minimálně 2048 bitů
* Šifrovací sada není na černé listině

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; s eliptickou křivkou P-256 je ve výchozím nastavení podporována. `TLS-ECDHE` &rbrack;

Následující příklad umožňuje připojení HTTP/1.1 a HTTP/2 na portu 8000. Připojení jsou zabezpečena TLS s dodaným certifikátem:

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

Volitelně vytvořte implementaci `IConnectionAdapter` pro filtrování handshake s TLS pro jednotlivé připojení pro konkrétní šifry:

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

*Nastavení protokolu z konfigurace*

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ve `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` výchozím nastavení načítá konfiguraci Kestrel.

V následujícím příkladu *appsettings.json* je pro všechny koncové body Kestrelu vytvořen výchozí protokol připojení (HTTP/1.1 a HTTP/2):

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

Protokoly zadané v hodnotách přepsání kódu nastavených konfigurací.

## <a name="transport-configuration"></a>Konfigurace přenosu

S vydáním ASP.NET Core 2.1, Kestrel výchozí přenos již není založen na Libuv, ale místo toho na spravované sokety. Toto je zásadní změna pro ASP.NET aplikace Core 2.0 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> upgradující na 2.1, které volají a závisí na jednom z následujících balíčků:

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Pro projekty, které vyžadují použití Libuv:

* Přidejte závislost pro balíček [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do souboru projektu aplikace:

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:

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

### <a name="url-prefixes"></a>Předpony adres URL

Při `UseUrls`použití `--urls` , argument `urls` příkazového řádku, `ASPNETCORE_URLS` konfigurační klíč hostitele nebo proměnné prostředí, předpony URL může být v některém z následujících formátů.

Platné jsou pouze předpony adresy URL HTTP. Kestrel nepodporuje protokol HTTPS při konfiguraci `UseUrls`vazeb URL pomocí .

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`je zvláštní případ, který se váže na všechny adresy IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`je ekvivalent IPv6 iPv4 `0.0.0.0`.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy `*`hostitelů `+`, a , nejsou zvláštní. Cokoli, co není rozpoznáno `localhost` jako platná adresa IP nebo se váže na všechny IP adresy IPv4 a IPv6. Chcete-li svázat různé názvy hostitelů s různými aplikacemi ASP.NET Core na stejném portu, použijte [http.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je Služba IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).

* Název `localhost` hostitele s číslem portu nebo ip adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Je-li `localhost` zadáno, kestrel se pokusí vytvořit vazbu na rozhraní zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na obou rozhraních zpětné smyčky, kestrel se nepodaří spustit. Pokud buď rozhraní zpětné smyčky není k dispozici z jakéhokoli jiného důvodu (nejčastěji proto, že Není podporován protokol Empv6), Kestrel protokoly upozornění.

## <a name="host-filtering"></a>Filtrování hostitelů

Zatímco Kestrel podporuje konfiguraci založenou na předponami, jako je například `http://example.com:5000`, Kestrel do značné míry ignoruje název hostitele. Hostitel `localhost` je zvláštní případ používaný pro vazbu na adresy zpětné smyčky. Jakýkoli hostitel jiný než explicitní adresa IP se váže na všechny veřejné IP adresy. `Host`záhlaví nejsou ověřena.

Jako řešení použijte middleware filtrování hostitelů. Middleware filtrování hostitelů poskytuje balíček [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) který je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo 2.2). Middleware je přidán <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>který volá :

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware filtrování hostitelů je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.json*/*appsettings.\< EnvironmentName>.json*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Předávaných záhlaví Middleware](xref:host-and-deploy/proxy-load-balancer) má také možnost. <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> Předané hlavičky Middleware a Host Filtrování Middleware mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` pomocí middlewaru s předanými hlavičkami je vhodné, pokud `Host` záhlaví není zachováno při předávání požadavků pomocí reverzního proxy serveru nebo vyrovnávání zatížení. Nastavení `AllowedHosts` s Middleware filtrování hostitelů je vhodné, pokud je Kestrel `Host` používán jako veřejný hraniční server nebo když je záhlaví přímo předáno dál.
>
> Další informace o middlewaru s <xref:host-and-deploy/proxy-load-balancer>předaných záhlaví naleznete v tématu .

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Kestrel je multiplatformní [webový server pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je ve výchozím nastavení zahrnut v šablonách projektu ASP.NET Core.

Poštolka podporuje následující scénáře:

* HTTPS
* Neprůhledný upgrade používaný k povolení [websocketů](https://github.com/aspnet/websockets)
* Unixové zásuvky pro vysoký výkon za Nginx

Kestrel je podporován na všech platformách a verzích, které podporuje .NET Core.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kdy použít Kestrel s reverzním proxy serverem

Poštolka může být použita sama o sobě nebo s *reverzním proxy serverem*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP ze sítě a předá je ke strel.

Poštolka používá jako okraj (Internet-čelí) webový server:

![Poštolka komunikuje přímo s Internetem bez reverzního proxy serveru](kestrel/_static/kestrel-to-internet2.png)

Poštolka použitá v konfiguraci reverzního proxy serveru:

![Poštolka komunikuje nepřímo s Internetem prostřednictvím reverzního proxy serveru, jako je služba IIS, Nginx nebo Apache](kestrel/_static/kestrel-to-internet.png)

Konfigurace, s reverzním proxy serverem nebo bez něj, je podporovaná konfigurace hostování.

Kestrel používaný jako hraniční server bez reverzního proxy serveru nepodporuje sdílení stejné IP a portu mezi více procesy. Když kestrel je nakonfigurován pro poslech na portu, Kestrel zpracovává všechny přenosy pro tento port bez ohledu na hlavičky požadavků.When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers. Reverzní proxy server, který může sdílet porty, má schopnost předávat požadavky Kestrelu na jedinečné IP a portu.

I v případě, že reverzní proxy server není vyžadován, použití reverzní proxy server může být dobrou volbou.

Reverzní proxy server:

* Může omezit exponované veřejné plochy aplikací, které hostuje.
* Poskytněte další vrstvu konfigurace a obrany.
* Mohlo by se lépe integrovat se stávající infrastrukturou.
* Zjednodušte vyrovnávání zatížení a zabezpečenou komunikaci (HTTPS). Pouze reverzní proxy server vyžaduje certifikát X.509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.

> [!WARNING]
> Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Jak používat Kestrel v aplikacích ASP.NET Core

Balíček [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

ASP.NET Šablony projektu Core používají kestrel ve výchozím nastavení. V *Program.cs*volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>kódu šablony <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , který volá na pozadí.

Chcete-li po `CreateDefaultBuilder`zavolání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>poskytnout další konfiguraci , volejte :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

Další informace `CreateDefaultBuilder` o hostiteli a jeho vytváření naleznete <xref:fundamentals/host/web-host#set-up-a-host>v části Nastavení *hostitele* v .

## <a name="kestrel-options"></a>Možnosti poštolky

Webový server Kestrel má možnosti konfigurace omezení, které jsou užitečné zejména v internetových nasazeních.

Nastavte omezení vlastnosti <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Vlastnost `Limits` obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.

V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Možnosti kestrelu, které jsou konfigurovány v kódu Jazyka C# v následujících příkladech, lze také nastavit pomocí [zprostředkovatele konfigurace](xref:fundamentals/configuration/index). Zprostředkovatel konfigurace souborů může například načíst konfiguraci kestrelu z souboru *appsettings.json* nebo *appsettings.{ Prostředí}.json:*

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

* Konfigurovat poštolek `Startup.ConfigureServices`v :

  1. Vstříkněte `IConfiguration` `Startup` instanci do třídy. Následující příklad předpokládá, že vstřikovaná `Configuration` konfigurace je přiřazena vlastnosti.
  2. V `Startup.ConfigureServices`, `Kestrel` načtěte část konfigurace do konfigurace Kestrel:

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

* Konfigurace kestrelu při vytváření hostitele:

  V *Program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrelu:

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

Oba předchozí přístupy pracují s libovolným [zprostředkovatelem konfigurace](xref:fundamentals/configuration/index).

### <a name="keep-alive-timeout"></a>Časový limit udržování života

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Získá nebo nastaví [časový limit keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5). Výchozí hodnota je 2 minuty.

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

Maximální počet souběžných otevřených připojení TCP lze nastavit pro celou aplikaci s následujícím kódem:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

Existuje samostatné omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na požadavek WebSockets). Po upgradu připojení se nezapočítává `MaxConcurrentConnections` do limitu.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

Maximální počet připojení je ve výchozím nastavení neomezený (null).

### <a name="maximum-request-body-size"></a>Maximální velikost těla požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Výchozí maximální velikost těla požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.

Doporučený přístup k přepsání limitu v aplikaci ASP.NET <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> Core MVC je použití atributu na metodě akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci při každém požadavku:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

Přepište nastavení na konkrétní požadavek v middlewaru:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Výjimka je vyvolána, pokud aplikace konfiguruje limit na požadavek poté, co aplikace začala číst požadavek. Je `IsReadOnly` vlastnost, která označuje, `MaxRequestBodySize` pokud je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě na konfiguraci limitu.

Pokud je aplikace [spuštěna mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [základnímodul](xref:host-and-deploy/aspnet-core-module), kestrel požadavek body limit je zakázán, protože iis již nastaví limit.

### <a name="minimum-request-body-data-rate"></a>Minimální přenosová rychlost tělo požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Poštolka kontroluje každou sekundu, pokud data přicházejí na zadanou rychlost v bajtů za sekundu. Pokud rychlost klesne pod minimum, je časový limit připojení. Doba odkladu je doba, kterou Kestrel dává klientovi ke zvýšení míry odeslání až na minimum; během této doby není sazba kontrolována. Období odkladu pomáhá zabránit přetažení připojení, které jsou zpočátku odesílání dat pomalým tempem z důvodu tcp pomalé spuštění.

Výchozí minimální sazba je 240 bajtů za sekundu s 5 sekundovou lhůtou odkladu.

Minimální sazba se vztahuje také na odpověď. Kód pro nastavení limitu požadavku a limitu odpovědi `RequestBody` `Response` je stejný s výjimkou s nebo v názvy vlastností a rozhraní.

Zde je příklad, který ukazuje, jak nakonfigurovat minimální rychlost dat v *Program.cs*:

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

### <a name="request-headers-timeout"></a>Časový výtažek záhlaví požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Získá nebo nastaví maximální množství času, které server stráví příjem hlavičky požadavku. Výchozí hodnota je 30 sekund.

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

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>určuje, zda je pro požadavek a odpověď povolena synchronní vstupně-va. Výchozí hodnota `true`je .

> [!WARNING]
> Velký počet blokování synchronní vstupně-va operace může vést k nedostatku fondu vláken, což způsobí, že aplikace nereaguje. Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní vstupně-v.A/O.

Následující příklad zakáže synchronní vstupně-va:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

Informace o dalších možnostech a limitech kestrelu naleznete v těchto tématech:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Ve výchozím nastavení se ASP.NET jádro váže na:

* `http://localhost:5000`
* `https://localhost:5001`(pokud je k dispozici certifikát místního rozvoje)

Zadejte adresy URL pomocí:

* `ASPNETCORE_URLS`proměnné prostředí.
* `--urls`argument příkazového řádku.
* `urls`konfiguračního klíče hostitele.
* `UseUrls`rozšíření.

Hodnota poskytovaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát). Nakonfigurujte hodnotu jako seznam oddělený `"Urls": "http://localhost:8000;http://localhost:8001"`středníkem (například).

Další informace o těchto přístupech naleznete v [tématu Adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [konfigurace override](xref:fundamentals/host/web-host#override-configuration).

Je vytvořen vývojový certifikát:

* Při instalaci sady [.NET Core SDK.](/dotnet/core/sdk)
* Nástroj [dev-certs](xref:aspnetcore-2.1#https) se používá k vytvoření certifikátu.

Některé prohlížeče vyžadují udělení výslovného oprávnění důvěřovat certifikátu místního vývoje.

Šablony projektů konfigurují aplikace tak, aby se ve výchozím nastavení spouštěly na protokolu HTTPS a zahrnovaly [přesměrování HTTPS a podporu HSTS](xref:security/enforcing-ssl).

Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody na konfiguraci URL předpony a porty pro Kestrel.

`UseUrls`, `--urls` argument příkazového `urls` řádku, konfigurační klíč hostitele a proměnná `ASPNETCORE_URLS` prostředí také fungují, ale omezení jsou zaznamenána dále v této části (výchozí certifikát musí být k dispozici pro konfiguraci koncového bodu HTTPS).

`KestrelServerOptions`Konfigurace:

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults (akce\<ListenOptions>)

Určuje konfiguraci, `Action` která má být spuštěna pro každý zadaný koncový bod. Volání `ConfigureEndpointDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.

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
> Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou mít výchozí hodnoty.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>KonfigurovatHttpsDefaults (akce\<HttpsConnectionAdapterOptions>)

Určuje konfiguraci, `Action` která má být spuštěna pro každý koncový bod HTTPS. Volání `ConfigureHttpsDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.

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
> Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou mít výchozí hodnoty.

### <a name="configureiconfiguration"></a>Configure(IKonfigurace)

Vytvoří konfigurační zavaděč pro <xref:Microsoft.Extensions.Configuration.IConfiguration> nastavení Kestrel, který bere jako vstup. Konfigurace musí být vymezena na konfigurační sekci pro Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel pro použití protokolu HTTPS.

`ListenOptions.UseHttps`Rozšíření:

* `UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal protokol HTTPS s výchozím certifikátem. Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.
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

`ListenOptions.UseHttps`Parametry:

* `filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.
* `password`je heslo potřebné pro přístup k datům certifikátu X.509.
* `configureOptions`je `Action` konfigurace rozhraní `HttpsConnectionAdapterOptions`. Vrátí `ListenOptions`hodnotu .
* `storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.
* `subject`je název subjektu certifikátu.
* `allowInvalid`označuje, zda by měly být považovány za neplatné certifikáty, například certifikáty podepsané svým držitelem.
* `location`je umístění úložiště, ze kterých se má certifikát načíst.
* `serverCertificate`je certifikát X.509.

V produkčním prostředí musí být protokol HTTPS explicitně nakonfigurován. Musí být poskytnut minimálně výchozí certifikát.

Podporované konfigurace popsané dále:

* Žádná konfigurace
* Nahrazení výchozího certifikátu z konfigurace
* Změna výchozích hodnot v kódu

*Žádná konfigurace*

Poštolka naslouchá `http://localhost:5000` dál `https://localhost:5001` a (pokud je k dispozici výchozí certifikát).

<a name="configuration"></a>

*Nahrazení výchozího certifikátu z konfigurace*

`CreateDefaultBuilder`ve `Configure(context.Configuration.GetSection("Kestrel"))` výchozím nastavení načítá konfiguraci Kestrel. Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS. Nakonfigurujte více koncových bodů, včetně adres URL a certifikátů, které mají být používány, ze souboru na disku nebo z úložiště certifikátů.

V následujícím příkladu *appsettings.json:*

* Nastavte **AllowInvalid** na `true` povolení použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).
* Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu) přejde zpět na certifikát definovaný v části **Výchozí** **certifikáty** > nebo vývojový certifikát.

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

Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je určení certifikátu pomocí polí úložiště certifikátů. **Například certifikát výchozí** certifikát **y** > lze zadat jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Poznámky schématu:

* Názvy koncových bodů nerozlišují malá a velká písmena. Například `HTTPS` a `Https` jsou platné.
* Parametr `Url` je vyžadován pro každý koncový bod. Formát tohoto parametru je stejný jako `Urls` parametr konfigurace nejvyšší úrovně s tím rozdílem, že je omezen na jednu hodnotu.
* Tyto koncové body nahradit ty definované `Urls` v konfiguraci nejvyšší úrovně, nikoli jejich přidání. Koncové body definované `Listen` v kódu prostřednictvím jsou kumulativní s koncovými body definovanými v konfigurační části.
* Sekce `Certificate` je nepovinná. Pokud `Certificate` oddíl není zadán, použijí se výchozí hodnoty definované v předchozích scénářích. Pokud nejsou k dispozici žádné výchozí hodnoty, server vyvolá výjimku a nepodaří se spustit.
* V `Certificate` této části jsou certifikáty**hesel** **cesty**&ndash;i**úložiště** **předmětů.**&ndash;
* Tímto způsobem může být definován libovolný počet koncových bodů, pokud nezpůsobí konflikty portů.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`vrátí `KestrelConfigurationLoader` metodu `.Endpoint(string name, listenOptions => { })` a s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:

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

`KestrelServerOptions.ConfigurationLoader`lze přímo přistupovat k pokračování iterace na stávajícím nakladači, jako je například ten, který <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>poskytuje .

* Konfigurační oddíl pro každý koncový bod `Endpoint` je k dispozici na možnosti v metodě tak, aby vlastní nastavení může být číst.
* Více konfigurací může být `options.Configure(context.Configuration.GetSection("{SECTION}"))` načteno voláním znovu s jiným oddílem. Používá se pouze poslední `Load` konfigurace, pokud není explicitně volána na předchozí instance. Metabalíček nevolá, `Load` takže jeho výchozí konfigurační část může být nahrazena.
* `KestrelConfigurationLoader`zrcadlí `Listen` rodinu API z `KestrelServerOptions` `Endpoint` jako přetížení, takže kód a koncové body konfigurace mohou být nakonfigurovány na stejném místě. Tato přetížení nepoužívají názvy a spotřebovávají pouze výchozí nastavení z konfigurace.

*Změna výchozích hodnot v kódu*

`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` lze použít ke změně `ListenOptions` `HttpsConnectionAdapterOptions`výchozího nastavení pro a , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři. `ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací všech koncových bodů.

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

*Podpora pro Kestrel pro SNI*

[Označení názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné adrese IP a portu. Aby sni fungovalo, klient odešle název hostitele pro zabezpečenou relaci na server během tls handshake tak, aby server mohl poskytnout správný certifikát. Klient používá zařízený certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje po handshake TLS.

Kestrel podporuje SNI `ServerCertificateSelector` prostřednictvím zpětného volání. Zpětné volání je vyvoláno jednou za připojení, aby aplikace mohla zkontrolovat název hostitele a vybrat příslušný certifikát.

Podpora SNI vyžaduje:

* Spuštění na `netcoreapp2.1` cílové rozhraní nebo později. Na `net461` nebo novější zpětné volání je `name` vyvolána, ale je vždy `null`. Je `name` také `null` v případě, že klient neposkytuje parametr název hostitele v tls handshake.
* Všechny webové stránky běží na stejné instanci Kestrel. Kestrel nepodporuje sdílení IP adresy a portu ve více instancích bez reverzního proxy serveru.

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

Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> vyzařovat protokoly úrovně ladění pro komunikaci na úrovni bajtů na připojení. Protokolování připojení je užitečné při řešení problémů v nízkoúrovňové komunikaci, například během šifrování TLS a za proxy servery. Pokud `UseConnectionLogging` je `UseHttps`umístěn před , šifrovaný provoz je zaznamenána. Pokud `UseConnectionLogging` je `UseHttps`umístěn po , dešifrovaný provoz je zaznamenána.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>Vazba na soket TCP

Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže na soket TCP a možnosti lambda umožňuje konfiguraci certifikátu X.509:

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

Příklad konfiguruje protokol <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS pro koncový bod s aplikací . Stejné rozhraní API použijte ke konfiguraci dalších nastavení kestrelu pro konkrétní koncové body.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vazba na unixovou zásuvku

Poslouchejte na unixovém soketu s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro lepší výkon s Nginx, jak je znázorněno v tomto příkladu:

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

* V souboru Konfiguration Nginx `server`  >  `location`  >  `proxy_pass` nastavte `http://unix:/tmp/{KESTREL SOCKET}:/;`položku na . `{KESTREL SOCKET}`je název soketu k <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> dispozici (například `kestrel-test.sock` v předchozím příkladu).
* Ujistěte se, že soket je zapisovatelný Nginx `chmod go+w /tmp/kestrel-test.sock`(například). 

### <a name="port-0"></a>Port 0

Je-li `0` zadáno číslo portu, kestrel dynamicky se váže na dostupný port. Následující příklad ukazuje, jak určit, který port Kestrel skutečně vázán za běhu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Při spuštění aplikace výstup okna konzoly označuje dynamický port, kde lze aplikaci dosáhnout:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Omezení

Nakonfigurujte koncové body pomocí následujících přístupů:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`argument příkazového řádku
* `urls`konfigurační klíč hostitele
* `ASPNETCORE_URLS`proměnná prostředí

Tyto metody jsou užitečné pro práci s kódem s jinými servery než Kestrel. Mějte však na paměti následující omezení:

* Protokol HTTPS nelze použít s těmito přístupy, pokud výchozí certifikát je k `KestrelServerOptions` dispozici v konfiguraci koncového bodu HTTPS (například pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).
* Při obou `Listen` `UseUrls` a přístupy jsou `Listen` používány současně, `UseUrls` koncové body přepsat koncové body.

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS jsou vazby adres URL pro `Listen` `UseUrls`vazby přepsání služby IIS nastaveny buď nebo . Další informace naleznete v [tématu ASP.NET základní modul.](xref:host-and-deploy/aspnet-core-module)

## <a name="transport-configuration"></a>Konfigurace přenosu

S vydáním ASP.NET Core 2.1, Kestrel výchozí přenos již není založen na Libuv, ale místo toho na spravované sokety. Toto je zásadní změna pro ASP.NET aplikace Core 2.0 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> upgradující na 2.1, které volají a závisí na jednom z následujících balíčků:

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Pro projekty, které vyžadují použití Libuv:

* Přidejte závislost pro balíček [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do souboru projektu aplikace:

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:

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

### <a name="url-prefixes"></a>Předpony adres URL

Při `UseUrls`použití `--urls` , argument `urls` příkazového řádku, `ASPNETCORE_URLS` konfigurační klíč hostitele nebo proměnné prostředí, předpony URL může být v některém z následujících formátů.

Platné jsou pouze předpony adresy URL HTTP. Kestrel nepodporuje protokol HTTPS při konfiguraci `UseUrls`vazeb URL pomocí .

* Adresa IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`je zvláštní případ, který se váže na všechny adresy IPv4.

* Adresa IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`je ekvivalent IPv6 iPv4 `0.0.0.0`.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy `*`hostitelů `+`, a , nejsou zvláštní. Cokoli, co není rozpoznáno `localhost` jako platná adresa IP nebo se váže na všechny IP adresy IPv4 a IPv6. Chcete-li svázat různé názvy hostitelů s různými aplikacemi ASP.NET Core na stejném portu, použijte [http.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je Služba IIS, Nginx nebo Apache.

  > [!WARNING]
  > Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).

* Název `localhost` hostitele s číslem portu nebo ip adresou zpětné smyčky s číslem portu

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Je-li `localhost` zadáno, kestrel se pokusí vytvořit vazbu na rozhraní zpětné smyčky IPv4 i IPv6. Pokud je požadovaný port používán jinou službou na obou rozhraních zpětné smyčky, kestrel se nepodaří spustit. Pokud buď rozhraní zpětné smyčky není k dispozici z jakéhokoli jiného důvodu (nejčastěji proto, že Není podporován protokol Empv6), Kestrel protokoly upozornění.

## <a name="host-filtering"></a>Filtrování hostitelů

Zatímco Kestrel podporuje konfiguraci založenou na předponami, jako je například `http://example.com:5000`, Kestrel do značné míry ignoruje název hostitele. Hostitel `localhost` je zvláštní případ používaný pro vazbu na adresy zpětné smyčky. Jakýkoli hostitel jiný než explicitní adresa IP se váže na všechny veřejné IP adresy. `Host`záhlaví nejsou ověřena.

Jako řešení použijte middleware filtrování hostitelů. Middleware filtrování hostitelů poskytuje balíček [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) který je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo 2.2). Middleware je přidán <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>který volá :

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware filtrování hostitelů je ve výchozím nastavení zakázáno. Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.json*/*appsettings.\< EnvironmentName>.json*. Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Předávaných záhlaví Middleware](xref:host-and-deploy/proxy-load-balancer) má také možnost. <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> Předané hlavičky Middleware a Host Filtrování Middleware mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` pomocí middlewaru s předanými hlavičkami je vhodné, pokud `Host` záhlaví není zachováno při předávání požadavků pomocí reverzního proxy serveru nebo vyrovnávání zatížení. Nastavení `AllowedHosts` s Middleware filtrování hostitelů je vhodné, pokud je Kestrel `Host` používán jako veřejný hraniční server nebo když je záhlaví přímo předáno dál.
>
> Další informace o middlewaru s <xref:host-and-deploy/proxy-load-balancer>předaných záhlaví naleznete v tématu .

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* Při použití unixových soketů v Systému Linux se soket při vypnutí aplikace automaticky neodstraní. Další informace naleznete v [tomto problému GitHub](https://github.com/dotnet/aspnetcore/issues/14134).
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [RFC 7230: Syntaxe a směrování zpráv (oddíl 5.4: Host)](https://tools.ietf.org/html/rfc7230#section-5.4)

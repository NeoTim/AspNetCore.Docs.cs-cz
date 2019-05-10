---
title: Implementace serveru webové kestrel v ASP.NET Core
author: guardrex
description: Další informace o Kestrel, napříč platformami webový server pro ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/28/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: b5b05dbd553124cecac2ec7ddb55c939cb91c8ad
ms.sourcegitcommit: a3926eae3f687013027a2828830c12a89add701f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65450989"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a>Implementace serveru webové kestrel v ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), a [Stephen Halter](https://twitter.com/halter73)

::: moniker range="<= aspnetcore-1.1"

1.1 verzi tohoto tématu, stáhněte si [Kestrel webového serveru provedení v ASP.NET Core (verze 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Kestrel_1.1.pdf).

::: moniker-end

Kestrel je platformově univerzální [webového serveru pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je obsažen ve výchozím nastavení v šablonách projektů ASP.NET Core.

Kestrel podporuje následující scénáře:

::: moniker range=">= aspnetcore-2.2"

* HTTPS
* Neprůhledný upgrade nepoužívá k zapnutí [WebSockets](https://github.com/aspnet/websockets)
* Soketů systému UNIX pro vysoký výkon za serveru Nginx
* HTTP/2 (s výjimkou v systému macOS&dagger;)

&dagger;HTTP/2 budou podporované v systému macOS v budoucí verzi.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* HTTPS
* Neprůhledný upgrade nepoužívá k zapnutí [WebSockets](https://github.com/aspnet/websockets)
* Soketů systému UNIX pro vysoký výkon za serveru Nginx

::: moniker-end

Kestrel se podporuje na všech platformách a verze, které podporuje .NET Core.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([stažení](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-2.2"

## <a name="http2-support"></a>Podpora HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro aplikace ASP.NET Core, pokud tyto požadavky byly splněny:

* Operační systém&dagger;
  * Windows Server 2016 nebo Windows 10 nebo novější&Dagger;
  * Linux s OpenSSL 1.0.2 nebo novější (například Ubuntu 16.04 nebo novější)
* Cílová architektura: .NET Core 2.2 nebo vyšší
* [Vyjednávání protokolu v aplikační vrstvě (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) připojení
* Protokol TLS 1.2 nebo vyšší připojení

&dagger;HTTP/2 budou podporované v systému macOS v budoucí verzi.
&Dagger;Kestrel má omezenou podporu pro HTTP/2 pro systém Windows Server 2012 R2 a Windows 8.1. Podpora je omezená, protože seznam podporovaných šifer TLS sady k dispozici v těchto operačních systémech je omezen. Certifikát vytvořený pomocí křivky digitální podpis algoritmů ECDSA (Elliptic) může být nutné zabezpečení připojení protokol TLS.

Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.

HTTP/2 je ve výchozím nastavení zakázána. Další informace o konfiguraci, najdete v článku [Kestrel možnosti](#kestrel-options) a [ListenOptions.Protocols](#listenoptionsprotocols) oddíly.

::: moniker-end

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kdy použít Kestrel pomocí reverzního proxy serveru

Kestrel můžete použít samostatně nebo se *reverzní proxy server*, jako například [Internetové informační služby (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), nebo [Apache](https://httpd.apache.org/). Reverzní proxy server přijímá požadavky HTTP ze sítě a předává je na Kestrel.

Kestrel použít jako webového serveru edge (internetový):

![Kestrel komunikuje přímo s Internetu bez reverzní proxy server](kestrel/_static/kestrel-to-internet2.png)

Kestrel používá v konfiguraci reverzního proxy serveru:

![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

Buď konfiguraci&mdash;s nebo bez něj reverzní proxy server&mdash;je hostování podporovanou konfiguraci pro ASP.NET Core 2.1 nebo novější aplikace, které přijímají požadavky z Internetu.

Kestrel použít jako hraniční server bez reverzní proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy. Když Kestrel je nakonfigurovaná k naslouchání na portu, Kestrel zpracovává veškerý síťový provoz na tento port bez ohledu na to požadavky `Host` záhlaví. Reverzní proxy server, který můžete sdílet porty má schopnost Kestrel na jedinečné IP adresy a portu směrování žádostí.

I v případě reverzního proxy serveru není povinné, pomocí reverzního proxy serveru může být dobrou volbou.

Reverzní proxy server:

* Můžete omezit vystavené veřejné útoku na aplikace, které hostuje.
* Poskytuje další úroveň ochrany a konfigurace.
* Může být lepší integrace se stávající infrastrukturou.
* Zjednodušení Vyrovnávání zatížení a konfigurace zabezpečené komunikace (HTTPS). Reverzní proxy server vyžaduje certifikát X.509, a tento server může komunikovat s aplikačních serverů v interní síti přes standardní HTTP.

> [!WARNING]
> Hostování v konfiguraci reverzní proxy server vyžaduje [hostitele filtrování](#host-filtering).

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Jak používat Kestrel v aplikacích ASP.NET Core

[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí balíčku [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější).

Šablony projektů ASP.NET Core pomocí Kestrel ve výchozím nastavení. V *Program.cs*, kód volání šablony <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

::: moniker range=">= aspnetcore-2.2"

Poskytnout další konfigurace po volání `CreateDefaultBuilder`, použijte `ConfigureKestrel`:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        });
```

Pokud aplikace nevolá `CreateDefaultBuilder` nastavení hostitele, volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **před** volání `ConfigureKestrel`:

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        })
        .Build();

    host.Run();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Poskytnout další konfigurace po volání `CreateDefaultBuilder`, volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            // Set properties and call methods on options
        });
```

::: moniker-end

## <a name="kestrel-options"></a>Možnosti kestrel

Webový server Kestrel má omezení možnosti konfigurace, které jsou obzvláště užitečné v nasazeních s přístupem k Internetu.

Nastavit na omezení <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy. `Limits` Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.

### <a name="maximum-client-connections"></a>Maximální počet klientských připojení

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>  
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Lze nastavit maximální počet souběžných otevřená připojení TCP pro celou aplikaci s následujícím kódem:

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentConnections = 100;
        });
```

::: moniker-end

Neexistuje samostatné limit pro připojení, která se upgradovaly z protokolu HTTP nebo HTTPS na jiné protokol (například v požadavku Websocket). Po dokončení upgradu spojení se započítává `MaxConcurrentConnections` limit.

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

::: moniker-end

Maximální počet připojení je neomezený počet (null) ve výchozím nastavení.

### <a name="maximum-request-body-size"></a>Velikost textu maximální požadavku

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Výchozí velikost těla maximální požadavku je 30,000,000 bajtů, což je přibližně 28.6 MB.

Doporučený postup, chcete-li přepsat omezení v aplikaci ASP.NET Core MVC je použít <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu na metodu akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikace u každého požadavku:

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

Můžete přepsat nastavení pro konkrétní žádost a v middlewaru:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

::: moniker-end

Pokud se pokusíte nakonfigurovat limit na vyžádání po spuštění aplikace k přečtení požadavku, je vyvolána výjimka. Je `IsReadOnly` vlastnost, která označuje, zda `MaxRequestBodySize` vlastnost je ve stavu jen pro čtení, což znamená, je příliš pozdě Konfigurace limitu.

### <a name="minimum-request-body-data-rate"></a>Minimální požadavek tělo přenosová rychlost

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>  
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel kontroluje každou sekundu, pokud je dat přicházejících u určenou míru v bajtech/sekundu. Pokud míra klesne pod minimální, vypršení časového limitu připojení. Období odkladu je množství času, aby Kestrel poskytuje klienta ke zvýšení jeho míra odesílání z až Toto minimum, frekvence není kontrolován během této doby. Období odkladu pomáhá předejít, vyřadit připojení, která původně odesílají data s nízkou rychlostí kvůli zpomalit TCP-start.

Výchozí minimální rychlost je 240 bajtů za sekundu s období odkladu 5 sekund.

Minimální sazba platí také pro odpověď. Kód pro nastavení limitu požadavků a omezení odpovědi je stejná s výjimkou s `RequestBody` nebo `Response` v názvech vlastností a interface.

Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové sazby v *Program.cs*:

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            options.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

::: moniker-end

Omezení pro minimální rychlost žádosti v middlewaru můžete přepsat:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

::: moniker range=">= aspnetcore-2.2"

Ani jedna kurzu funkce odkazované v předchozí ukázce se nacházejí v `HttpContext.Features` pro požadavky HTTP/2, protože úprava šířku pásma na základě žádosti se nepodporuje pro HTTP/2 z důvodu multiplexing žádost o podporu protokolu. Omezení přenosové rychlosti úrovni serveru, které jsou nakonfigurované přes `KestrelServerOptions.Limits` stále použít k připojení HTTP/1.x a HTTP/2.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="maximum-streams-per-connection"></a>Maximální datových proudů za připojení

`Http2.MaxStreamsPerConnection` omezuje počet datových proudů souběžný požadavek na připojení HTTP/2. Nadbytečné datové proudy jsou odmítnuta.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

Výchozí hodnota je 100.

### <a name="header-table-size"></a>Velikost záhlaví tabulky

Dekodér HPACK dekomprimuje hlavičky protokolu HTTP pro připojení HTTP/2. `Http2.HeaderTableSize` omezuje velikost tabulky záhlaví komprese, který používá HPACK dekodéru. Hodnota je k dispozici v oktetech a musí být větší než nula (0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.HeaderTableSize = 4096;
        });
```

Výchozí hodnota je 4096.

### <a name="maximum-frame-size"></a>Maximální velikost rámce

`Http2.MaxFrameSize` Určuje maximální velikost datové rámce připojení HTTP/2 pro příjem. Hodnota je k dispozici v oktetech a musí být mezi 2 ^ 14 (16384) a 2 ^ 16 777 24-1 (215).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxFrameSize = 16384;
        });
```

Výchozí hodnota je 2 ^ 14 (16384).

### <a name="maximum-request-header-size"></a>Požadavek na maximální velikost záhlaví

`Http2.MaxRequestHeaderFieldSize` Určuje maximální povolenou velikost v oktetech hodnot hlavičky žádosti. Toto omezení platí pro název a hodnotu společně v jejich reprezentace nekomprimovaný a komprimovaný. Hodnota musí být větší než nula (0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

Výchozí hodnota je 8192.

### <a name="initial-connection-window-size"></a>Velikost okna počátečního připojení

`Http2.InitialConnectionWindowSize` Určuje maximální žádosti o data těla bajtovou vyrovnávací paměti serveru v jednom okamžiku agregované napříč všemi požadavky (datové proudy) za připojení. Požadavky jsou také omezit pomocí `Http2.InitialStreamWindowSize`. Hodnota musí být větší než nebo rovna hodnotě 65 535 a menší než 2 ^ 31 (2 147 483 648).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

Výchozí hodnota je 128 KB (131,072).

### <a name="initial-stream-window-size"></a>Datový proud počáteční velikost okna

`Http2.InitialStreamWindowSize` Určuje maximální žádosti o data těla v bajtech vyrovnávacích pamětí serveru současně za žádost (datový proud). Požadavky jsou také omezit pomocí `Http2.InitialStreamWindowSize`. Hodnota musí být větší než nebo rovna hodnotě 65 535 a menší než 2 ^ 31 (2 147 483 648).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

Výchozí hodnota je velikosti 96 KB (98,304).

::: moniker-end

Informace o dalších možnostech Kestrel a omezení najdete tady:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Konfigurace koncového bodu

Ve výchozím nastavení ASP.NET Core váže na:

* `http://localhost:5000`
* `https://localhost:5001` (Pokud je místní vývojový certifikát k dispozici)

Určení adres URL pomocí:

* `ASPNETCORE_URLS` proměnné prostředí.
* `--urls` argument příkazového řádku.
* `urls` Konfigurační klíč hostitele.
* `UseUrls` metody rozšíření.

Hodnota zadaná pomocí těchto přístupů může být jeden nebo více HTTP a HTTPS koncové body (HTTPS Pokud je k dispozici výchozí cert). Nakonfigurujte tuto hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).

Další informace o těchto přístupů najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [konfigurace přepisování](xref:fundamentals/host/web-host#override-configuration).

Certifikát pro vývoj se vytvoří:

* Když [.NET Core SDK](/dotnet/core/sdk) je nainstalována.
* [Nástroji dev-certs](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.

Některé prohlížeče vyžadují, abyste udělili explicitní oprávnění pro prohlížeč důvěřovat certifikátům místní vývoj.

ASP.NET Core 2.1 a vyšší šablony projektů konfigurace aplikací ve výchozím nastavení spouští na protokol HTTPS a zahrnout [přesměrování protokolu HTTPS a HSTS podporují](xref:security/enforcing-ssl).

Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> konfigurace předpony adres URL a portů pro Kestrel.

`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` proměnnou prostředí také pracovní ale mají omezení, později uvedené v této části (výchozí certifikát musí být k dispozici pro koncový bod HTTPS Konfigurace).

ASP.NET Core 2.1 nebo novější `KestrelServerOptions` konfigurace:

### <a name="configureendpointdefaultsactionltlistenoptionsgt"></a>ConfigureEndpointDefaults (akce&lt;ListenOptions&gt;)

Určuje konfiguraci `Action` pro spuštění v každý zadaný koncový bod. Volání `ConfigureEndpointDefaults` více než jednou nahradí předchozí `Action`s poslední `Action` zadané.

::: moniker range=">= aspnetcore-3.0"

```csharp
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.ConfigureKestrel(serverOptions =>
        {
            serverOptions.ConfigureEndpointDefaults(configureOptions =>
            {
                configureOptions.NoDelay = true;
            });
        });
        webBuilder.UseStartup<Startup>();
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ConfigureEndpointDefaults(configureOptions =>
            {
                configureOptions.NoDelay = true;
            });
        });
```

::: moniker-end

### <a name="configurehttpsdefaultsactionlthttpsconnectionadapteroptionsgt"></a>ConfigureHttpsDefaults(Action&lt;HttpsConnectionAdapterOptions&gt;)

Určuje konfiguraci `Action` ke spuštění pro každý koncový bod HTTPS. Volání `ConfigureHttpsDefaults` více než jednou nahradí předchozí `Action`s poslední `Action` zadané.

::: moniker range=">= aspnetcore-3.0"

```csharp
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.ConfigureKestrel(serverOptions =>
        {
            serverOptions.ConfigureHttpsDefaults(options =>
            {
                // certificate is an X509Certificate2
                options.ServerCertificate = certificate;
            });
        });
        webBuilder.UseStartup<Startup>();
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ConfigureHttpsDefaults(httpsOptions =>
            {
                // certificate is an X509Certificate2
                httpsOptions.ServerCertificate = certificate;
            });
        });
```

::: moniker-end

### <a name="configureiconfiguration"></a>Configure(IConfiguration)

Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup. Konfigurace musí být určená ke konfiguračnímu oddílu Kestrel.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Nakonfigurujte Kestrel k používání HTTPS.

`ListenOptions.UseHttps` rozšíření:

* `UseHttps` &ndash; Nakonfigurujte Kestrel k používání HTTPS pomocí certifikátu výchozí. Vyvolá výjimku, pokud je nakonfigurovaný žádný výchozí certifikát.
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

`ListenOptions.UseHttps` Parametry:

* `filename` je název a cesta k souboru soubor certifikátu, relativní k adresáři, který obsahuje soubory obsahu aplikace.
* `password` je heslo pro přístup k datům certifikátu X.509.
* `configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`. Vrátí `ListenOptions`.
* `storeName` je do úložiště certifikátů, ze kterého se má načíst certifikát.
* `subject` je název subjektu certifikátu.
* `allowInvalid` Označuje, pokud neplatné certifikáty by měl být, jako jsou certifikáty podepsané svým držitelem.
* `location` je umístění úložiště pro načtení certifikátu.
* `serverCertificate` je certifikát X.509.

V produkčním prostředí musí být explicitně nakonfigurován protokol HTTPS. Minimálně je třeba zadat výchozího certifikátu.

Podporované konfigurace je popsáno dále:

* Žádná konfigurace
* Nahraďte výchozí certifikát z konfigurace
* Změnit výchozí nastavení v kódu

*Žádná konfigurace*

Naslouchá kestrel `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí cert).

<a name="configuration"></a>

*Nahraďte výchozí certifikát z konfigurace*

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> volání `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` ve výchozím nastavení se načíst konfiguraci Kestrel. Schéma konfigurace k nastavení výchozí HTTPS aplikace je k dispozici pro Kestrel. Konfigurovat několik koncových bodů, včetně adresy URL a certifikáty, které chcete použít, ze souboru na disku nebo z úložiště certifikátů.

V následujícím *appsettings.json* příkladu:

* Nastavte **AllowInvalid** k `true` tak, aby povolovala použití neplatné certifikáty (například certifikáty podepsané svým držitelem).
* Libovolný koncový bod HTTPS, který nemá určenou certifikát (**HttpsDefaultCert** v následujícím příkladu) spadne zpět na cert definované v části **certifikáty** > **výchozí** nebo certifikát pro vývoj.

```json
{
"Kestrel": {
  "EndPoints": {
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
        "Store": "<certificate store; defaults to My>",
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

O alternativu k použití **cesta** a **heslo** pro jakýkoliv certifikát je uzel a určete certifikát, pomocí polí úložiště certifikátů. Například **certifikáty** > **výchozí** certifikát lze zadat jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; defaults to My>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Schéma poznámek:

* Koncové body názvy jsou malá a velká písmena. Například `HTTPS` a `Https` jsou platné.
* `Url` Parametr je povinný pro každý koncový bod. Formát pro tento parametr je stejné jako na nejvyšší úrovni `Urls` parametr konfigurace s výjimkou, že je omezena na jednu hodnotu.
* Nahraďte tyto koncové body jsou definované na nejvyšší úrovni `Urls` místo přidávání k nim. Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní se koncové body definované v konfiguračním oddílu.
* `Certificate` Část je nepovinná. Pokud `Certificate` oddílu není zadán, budou použity výchozí hodnoty definované v předchozích případech. Pokud nejsou k dispozici žádné výchozí hodnoty, vyvolá výjimku, server a nepodaří spustit.
* `Certificate` Části podporuje obě **cesta**&ndash;**heslo** a **subjektu**&ndash;**Store** certifikáty.
* Tímto způsobem lze definovat libovolný počet koncových bodů tak dlouho, dokud není způsobují konflikty portu.
* `options.Configure(context.Configuration.GetSection("Kestrel"))` Vrátí `KestrelConfigurationLoader` s `.Endpoint(string name, options => { })` metodu, která slouží k doplnění nastavení konfigurovaný koncový bod:

  ```csharp
  options.Configure(context.Configuration.GetSection("Kestrel"))
      .Endpoint("HTTPS", opt =>
      {
          opt.HttpsOptions.SslProtocols = SslProtocols.Tls12;
      });
  ```

  Můžete také přímý přístup k `KestrelServerOptions.ConfigurationLoader` chcete zachovat iterace na existující zavaděč, jako je ta poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.

* Oddíl konfigurace pro každý koncový bod je k dispozici na možnosti v `Endpoint` metodu tak, aby mohou číst vlastní nastavení.
* Více konfigurací může být načten voláním `options.Configure(context.Configuration.GetSection("Kestrel"))` znovu s jinou část. Pouze poslední konfigurace se použije, pokud `Load` je explicitně zavolán v předchozích instancí. Microsoft.aspnetcore.all nevolá `Load` tak, aby jeho výchozí konfigurační oddíl může být nahrazen.
* `KestrelConfigurationLoader` zrcadlení `Listen` rozhraní API z řady `KestrelServerOptions` jako `Endpoint` přetížení, takže koncové body kódu a konfiguračním může být nakonfigurována na stejném místě. Tato přetížení nepoužívejte názvy a využívat pouze výchozí nastavení z konfigurace.

*Změnit výchozí nastavení v kódu*

`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` můžete použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepisuje výchozí certifikát uvedený v předchozím scénáři. `ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měla být volána před všechny koncové body se konfigurují.

```csharp
options.ConfigureEndpointDefaults(opt =>
{
    opt.NoDelay = true;
});

options.ConfigureHttpsDefaults(httpsOptions =>
{
    httpsOptions.SslProtocols = SslProtocols.Tls12;
});
```

*Podpora kestrel SNI*

[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu. Pro SNI na funkci klient odešle název hostitele pro zabezpečenou relaci na server během provádění metody handshake TLS server může poskytnout správný certifikát. Klient použije certifikát zařízená šifrovanou komunikaci se serverem během zabezpečené relace, který následuje TLS handshake.

Podporuje SNI přes kestrel `ServerCertificateSelector` zpětného volání. Zpětné volání je vyvolat jednou za připojení, aby mohla aplikace ke kontrole názvu hostitele a vyberte příslušný certifikát.

Podpora SNI vyžaduje:

* Běží na rozhraní .NET framework `netcoreapp2.1`. Na `netcoreapp2.0` a `net461`, zpětné volání vyvolat ale `name` je vždy `null`. `name` Je také `null` Pokud klienta neobsahuje hostitelský název parametru v TLS handshake.
* Spustit všechny weby na stejnou instanci Kestrel. Kestrel nepodporuje sdílení IP adresu a port ve více instancích bez reverzního proxy serveru.

::: moniker range=">= aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
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

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
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

::: moniker-end

### <a name="bind-to-a-tcp-socket"></a>Vytvoření vazby na soket TCP

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Metoda vytvoří vazbu na soket TCP a možnosti lambda umožňuje konfiguraci certifikátu X.509:

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
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
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

::: moniker-end

Tento příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>. Nakonfigurujte další nastavení Kestrel pro konkrétní koncové body pomocí stejného rozhraní API.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Vytvoření vazby k Unixovému soketu

Naslouchání soketu Unix s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> dosáhnout tak vyššího výkonu se serverem Nginx, jak je znázorněno v tomto příkladu:

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.ListenUnixSocket("/tmp/kestrel-test.sock");
            options.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

::: moniker-end

### <a name="port-0"></a>Port 0

Když číslo portu `0` určena Kestrel dynamicky váže dostupný port. Následující příklad ukazuje, jak určit port, který Kestrel vázán skutečně za běhu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Při spuštění aplikace Určuje výstup okna konzoly dynamický port, kde se dá kontaktovat aplikace:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Omezení

Konfigurace koncových bodů pomocí následujících postupů:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls` argument příkazového řádku
* `urls` Konfigurační klíč hostitele
* `ASPNETCORE_URLS` Proměnná prostředí

Tyto metody jsou užitečné pro provádění kódu práce se servery než Kestrel. Nicméně mějte na paměti následující omezení:

* HTTPS nelze použít s těchto přístupů, pokud výchozí certifikát je součástí konfigurace koncového bodu HTTPS (například pomocí `KestrelServerOptions` konfigurace nebo konfiguračního souboru, jak je znázorněno výše v tomto tématu).
* Při i `Listen` a `UseUrls` přístupy se využívat současně, `Listen` přepsat koncových bodů `UseUrls` koncových bodů.

### <a name="iis-endpoint-configuration"></a>Konfigurace koncového bodu služby IIS

Při použití služby IIS, adresa URL vazeb pro služby IIS změnit vazby jsou nastaveny buď `Listen` nebo `UseUrls`. Další informace najdete v tématu [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) tématu.

::: moniker range=">= aspnetcore-2.2"

### <a name="listenoptionsprotocols"></a>ListenOptions.Protocols

`Protocols` Vlastnost vytvoří protokol HTTP (`HttpProtocols`) povolený koncový bod připojení nebo na serveru. Přiřaďte hodnotu `Protocols` vlastnost z `HttpProtocols` výčtu.

| `HttpProtocols` Hodnota výčtu | Protokol připojení povoleno |
| -------------------------- | ----------------------------- |
| `Http1`                    | HTTP/1.1 pouze. Je možné s nebo bez protokolu TLS. |
| `Http2`                    | HTTP/2 pouze. Používá se především s TLS. Může použít bez TLS pouze v případě, že klient podporuje [předchozí znalosti režimu](https://tools.ietf.org/html/rfc7540#section-3.4). |
| `Http1AndHttp2`            | HTTP/1.1 a HTTP/2. Vyžaduje TLS a [vyjednávání protokolu v aplikační vrstvě (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) připojení pro vyjednávání protokolu HTTP/2; v opačném případě výchozí nastavení připojení k protokolu HTTP/1.1. |

Výchozí protokol je HTTP/1.1.

Omezení TLS pro HTTP/2:

* TLS verze 1.2 nebo vyšší
* Opakované vyjednávání zakázáno
* Komprese vypnuta
* Velikost minimální dočasné výměny klíčů:
  * Eliptické křivky Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bits minimální
  * Konečná pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; minimální hodnotě 2 048 bitů
* Šifrovací sada není na seznamu zakázaných adres

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; s eliptické křivky p-256 &lbrack; `FIPS186` &rbrack; se podporuje ve výchozím nastavení.

Následující příklad umožňuje HTTP/1.1 a připojení HTTP/2 na portu 8000. Připojení jsou zabezpečená pomocí protokolu TLS s poskytnutý certifikát:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
            {
                listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

Volitelně můžete vytvořit `IConnectionAdapter` implementace pro filtrování počet metod Handshake TLS na jednotlivá připojení pro konkrétní šifrování:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
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

        // Throw NotSupportedException for any cipher algorithm that you don't 
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

*Nastavení protokolu z konfigurace*

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> volání `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` ve výchozím nastavení se načíst konfiguraci Kestrel.

V následujícím *appsettings.json* pokládáme stav pro všechny koncové body pro Kestrel například výchozí připojení protokol (HTTP/1.1 a HTTP/2):

```json
{
  "Kestrel": {
    "EndPointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

Následující příklad souboru konfigurace naváže připojení protokolu pro určitý koncový bod:

```json
{
  "Kestrel": {
    "EndPoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

Protokoly se zadat v kódu přepsat hodnoty nastavené v konfiguraci.

::: moniker-end

## <a name="transport-configuration"></a>Konfigurace přenosu

Verze technologie ASP.NET Core 2.1 Kestrel pro výchozí přenos je již podle Libuv ale místo toho podle spravované sokety. Toto je zásadní změny pro aplikace ASP.NET Core 2.0 upgrade na verzi 2.1, které volají <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednu z následujících balíčků:

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Pro ASP.NET Core 2.1 nebo novější projekty, které používají [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) a vyžadují použití Libuv:

* Přidat závislost [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) balíček do souboru projektu vaší aplikace:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv" 
                      Version="<LATEST_VERSION>" />
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

### <a name="url-prefixes"></a>Předpony adres URL

Při použití `UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele, nebo `ASPNETCORE_URLS` proměnné prostředí, předpony adres URL může být v některém z následujících formátů.

Platné jsou pouze předpony adres URL protokolu HTTP. Nepodporuje kestrel HTTPS při konfiguraci adresy URL vazby, které používají `UseUrls`.

* Adresu IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` je zvláštní případ s vazbou na všechny adresy IPv4.

* Adresa protokolu IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` je ekvivalentem IPv6 IPv4 `0.0.0.0`.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy hostitelů `*`, a `+`, nejsou speciální. Nic není rozpoznán jako platná IP adresa nebo `localhost` vytvoří vazbu pro všechny IP adresy IPv6 a IPv4. Pro vázání názvů jiného hostitele a různé aplikace ASP.NET Core na stejném portu, použijte [HTTP.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je například Apache, IIS nebo Nginx.

  > [!WARNING]
  > Hostování v konfiguraci reverzní proxy server vyžaduje [hostitele filtrování](#host-filtering).

* Hostitel `localhost` název portu s číslem portu číslo nebo adresu zpětné smyčky IP

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Když `localhost` určena Kestrel pokusí o připojení k rozhraní zpětné smyčky protokolu IPv4 a IPv6. Pokud požadovaný port je používán jinou službou buď rozhraní zpětné smyčky, Kestrel nepodaří spustit. Pokud je buď rozhraní zpětné smyčky není k dispozici z jiného důvodu (většinu běžně, protože protokol IPv6 není podporován), Kestrel protokoluje upozornění.

## <a name="host-filtering"></a>Hostitel filtrování

I když Kestrel podporuje konfigurace, například podle předpon `http://example.com:5000`, Kestrel do značné míry ignoruje název hostitele. Hostitel `localhost` je zvláštní případ použité pro vazbu na adresu zpětné smyčky adresy. Všechny hostitele, jiné než explicitních IP adresu vytvoří vazbu na všechny veřejné IP adresy. `Host` záhlaví pořadí úloh se neověřuje.

Jako alternativní řešení použijte hostitele filtrování middlewaru. Poskytuje Middleware filtrování hostitele [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) balíček, který je součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější). Přidá middleware <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware filtrování hostitele je ve výchozím nastavení zakázána. Chcete-li povolit middleware, definujte `AllowedHosts` klíče v *appsettings.json*/*appsettings.\< EnvironmentName > .json*. Hodnota je středníkem oddělený seznam názvů hostitele bez čísla portů:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Předané záhlaví Middleware](xref:host-and-deploy/proxy-load-balancer) má také <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> možnost. Přesměrovaná záhlaví Middleware a filtrování Middleware hostitele mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` se předávají Middleware hlavičky je vhodné, když `Host` záhlaví nezachová při předávání žádostí s reverzní proxy server nebo nástroje pro vyrovnávání zatížení. Nastavení `AllowedHosts` s Middlewarem filtrování hostitele je vhodné při Kestrel slouží jako veřejnou hraniční server, nebo když `Host` záhlaví předána přímo.
>
> Další informace o předávaných Middleware záhlaví, naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.

## <a name="additional-resources"></a>Další zdroje

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [Kestrel zdrojového kódu](https://github.com/aspnet/AspNetCore/tree/master/src/Servers/Kestrel)
* [RFC 7230: Syntaxe a směrování zpráv (oddíl 5.4: Hostitel)](https://tools.ietf.org/html/rfc7230#section-5.4)

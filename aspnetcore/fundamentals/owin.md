---
title: Otevřít webové rozhraní pro rozhraní .NET (OWIN) s ASP.NET jádrem
author: ardalis
description: Zjistěte, jak ASP.NET Core podporuje otevřené webové rozhraní pro rozhraní .NET (OWIN), které umožňuje oddělení webových aplikací od webových serverů.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 14b23ba6d284413e20417bbd4142e19a656350ac
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666682"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a>Otevřít webové rozhraní pro rozhraní .NET (OWIN) s ASP.NET jádrem

[Steve Smith](https://ardalis.com/) a [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core podporuje otevřené webové rozhraní pro rozhraní .NET (OWIN). OWIN umožňuje oddělení webových aplikací od webových serverů. Definuje standardní způsob, jak middleware, které mají být použity v kanálu pro zpracování požadavků a přidružené odpovědi. ASP.NET Core aplikace a middleware mohou spolupracovat s aplikacemi, servery a middlewarem založenými na OWIN.

OWIN poskytuje vrstvu oddělení, která umožňuje použití dvou rámců s různorodými objektovými modely. Balíček `Microsoft.AspNetCore.Owin` obsahuje dvě implementace adaptéru:

* ASP.NET jádro pro OWIN 
* OWIN na ASP.NET jádro

To umožňuje ASP.NET Core, které mají být umístěny na vrcholu OWIN kompatibilní server / host nebo pro jiné OWIN kompatibilní komponenty, které mají být spuštěny na vrcholu ASP.NET Core.

> [!NOTE]
> Použití těchto adaptérů je dodáváno s náklady na výkon. Aplikace, které používají jenom ASP.NET `Microsoft.AspNetCore.Owin` komponenty Core, by neměly používat balíček nebo adaptéry.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a>Spuštění middlewaru OWIN v potrubí ASP.NET Core

ASP.NET owin podpora Core je nasazenjako `Microsoft.AspNetCore.Owin` součást balíčku. Podporu OWIN můžete importovat do projektu instalací tohoto balíčku.

Middleware OWIN odpovídá [specifikaci OWIN](https://owin.org/spec/spec/owin-1.0.0.html) `Func<IDictionary<string, object>, Task>` , která vyžaduje rozhraní, a `owin.ResponseBody`nastaví se konkrétní klíče (například ). Následující jednoduchý middleware OWIN zobrazuje "Hello World":

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

Ukázkový podpis `Task` vrátí a `IDictionary<string, object>` přijme podle požadavků OWIN.

Následující kód ukazuje, jak `OwinHello` přidat middleware (viz výše) do `UseOwin` kanálu ASP.NET Core s metodou rozšíření.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

Můžete nakonfigurovat další akce, které se uskuteční v rámci kanálu OWIN.

> [!NOTE]
> Hlavičky odpovědí by měly být změněny pouze před prvním zápisem do datového proudu odpovědí.

> [!NOTE]
> Více volání `UseOwin` do se nedoporučuje z důvodů výkonu. Komponenty OWIN budou fungovat nejlépe, pokud budou seskupeny.

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a>Použití ASP.NET Core Hosting na serveru založeném na OWIN

Servery založené na OWIN mohou hostovat ASP.NET aplikace Core. Jeden takový server je [Nowin](https://github.com/Bobris/Nowin), .NET OWIN webový server. V ukázce pro tento článek jsem zahrnul projekt, který odkazuje `IServer` na Nowin a používá jej k vytvoření schopnosti vlastního hostování ASP.NET Core.

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

`IServer`je rozhraní, které `Features` vyžaduje `Start` vlastnost a metodu.

`Start`je zodpovědný za konfiguraci a spuštění serveru, což se v tomto případě provádí prostřednictvím řady fluent API volání, která nastavit adresy analyzované z IServerAddressesFeature. Všimněte si, že `_builder` plynulá konfigurace proměnné určuje, `appFunc` že požadavky budou zpracovány definované dříve v metodě. To `Func` se nazývá na každý požadavek na zpracování příchozích požadavků.

Přidáme také rozšíření, `IWebHostBuilder` které vám usnadní přidání a konfiguraci serveru Nowin.

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

S tímto na místě, vyvolat rozšíření v *Program.cs* spustit aplikaci ASP.NET Core pomocí tohoto vlastního serveru:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

Další informace o [ASP.NET základních serverech](xref:fundamentals/servers/index).

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>Spuštění ASP.NET jádra na serveru založeném na OWIN a použití jeho podpory websockets

Dalším příkladem toho, jak funkce serverů založených na OWIN mohou být využívány ASP.NET Core je přístup k funkcím, jako je WebSockets. Webový server .NET OWIN použitý v předchozím příkladu má vestavěnou podporu webových soketů, kterou může využít aplikace ASP.NET Core. Následující příklad ukazuje jednoduchou webovou aplikaci, která podporuje webové sokety a vrací zpět vše odeslané na server prostřednictvím websockets.

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

Tato [ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) je `NowinServer` konfigurována pomocí stejného jako předchozí – jediný rozdíl `Configure` je v tom, jak je aplikace konfigurována ve své metodě. Test pomocí [jednoduchého klienta websocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstruje aplikaci:

![Testovací klient webového soketu](owin/_static/websocket-test.png)

## <a name="owin-environment"></a>Prostředí OWIN

Prostředí OWIN můžete vytvořit `HttpContext`pomocí .

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>OWIN klíče

OWIN závisí `IDictionary<string,object>` na objektu pro komunikaci informací v rámci výměny požadavků a odpovědí HTTP. ASP.NET Core implementuje klíče uvedené níže. Podívejte se na [primární specifikace, rozšíření](https://owin.org/#spec)a [OWIN klíčové pokyny a společné klíče](https://owin.org/spec/spec/CommonKeys.html).

### <a name="request-data-owin-v100"></a>Údaje o požadavku (OWIN v1.0.0)

| Klíč               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| owin. RequestScheme | `String` |  |
| owin. RequestMethod  | `String` | |    
| owin. RequestPathBase  | `String` | |    
| owin. Cesta aplikace RequestPath | `String` | |     
| owin. PožadavekNařetězec  | `String` | |    
| owin. Protokol RequestProtocol  | `String` | |    
| owin. Hlavičky požadavků | `IDictionary<string,string[]>`  | |
| owin. RequestBody | `Stream`  | |

### <a name="request-data-owin-v110"></a>Údaje o požadavku (OWIN v1.1.0)

| Klíč               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| owin. Requestid | `String` | Nepovinné |

### <a name="response-data-owin-v100"></a>Údaje o odezvě (OWIN v1.0.0)

| Klíč               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| owin. ResponseStatusCode | `int` | Nepovinné |
| owin. ResponseReasonPhrase | `String` | Nepovinné |
| owin. Hlavičky odpovědí | `IDictionary<string,string[]>`  | |
| owin. ResponseBody | `Stream`  | |

### <a name="other-data-owin-v100"></a>Další údaje (OWIN v1.0.0)

| Klíč               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| owin. Volání zrušeno | `CancellationToken` |  |
| owin. Verze  | `String` | |   

### <a name="common-keys"></a>Běžné klávesy

| Klíč               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| Ssl. Klientský certifikát | `X509Certificate` |  |
| Ssl. LoadClientCertAsync  | `Func<Task>` | |    
| Server. Vzdálená ipadresa  | `String` | |    
| Server. Vzdálený port | `String` | |     
| Server. Adresa LocalIpAddress  | `String` | |    
| Server. LocalPort  | `String` | |    
| Server. IsMístní  | `bool` | |    
| Server. OnSendingHeaders  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| Klíč               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| souboru odeslání. Sendasync | Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | Na požadavek |

### <a name="opaque-v030"></a>Neprůhledné v0.3.0

| Klíč               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| Neprůhledné. Verze | `String` |  |
| Neprůhledné. Upgrade | `OpaqueUpgrade` | Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| Neprůhledné. Proudu | `Stream` |  |
| Neprůhledné. Volání zrušeno | `CancellationToken` |  |

### <a name="websocket-v030"></a>WebSocket v0.3.0

| Klíč               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| websocket. Verze | `String` |  |
| websocket. Přijmout | `WebSocketAccept` | Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| websocket. Přijmoutalt |  | Nespecifikace |
| websocket. Podprotokol | `String` | Viz [RFC6455 Oddíl 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Krok 5.5 |
| websocket. Sendasync | `WebSocketSendAsync` | Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket. ReceiveAsync | `WebSocketReceiveAsync` | Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket. CloseAsync | `WebSocketCloseAsync` | Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket. Volání zrušeno | `CancellationToken` |  |
| websocket. Stav klienta CloseStatus | `int` | Nepovinné |
| websocket. Popis klienta | `String` | Nepovinné |

## <a name="additional-resources"></a>Další zdroje

* [Middleware](xref:fundamentals/middleware/index)
* [Servery](xref:fundamentals/servers/index)

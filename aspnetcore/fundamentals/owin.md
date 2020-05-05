---
title: Otevřete web interface for .NET (OWIN) s ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core podporuje rozhraní Open Web Interface for .NET (OWIN), které umožňuje oddělit webové aplikace od webových serverů.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/owin
ms.openlocfilehash: defbf428634a04e6fa3862926136dc98b88da589
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775957"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a>Otevřete web interface for .NET (OWIN) s ASP.NET Core

[Steve Smith](https://ardalis.com/) a [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core podporuje otevřené webové rozhraní pro .NET (OWIN). OWIN umožňuje oddělit webové aplikace od webových serverů. Definuje standardní způsob, jakým se má middleware použít v kanálu ke zpracování požadavků a přidružených odpovědí. ASP.NET Core aplikace a middleware se můžou vzájemně spolupracovat s aplikacemi, servery a middlewarem založeným na OWIN.

OWIN poskytuje rozvětvenou vrstvu, která umožňuje používat dvě architektury s různorodými objektovými modely. `Microsoft.AspNetCore.Owin` Balíček poskytuje dva implementace adaptérů:

* ASP.NET Core OWIN 
* OWIN na ASP.NET Core

To umožňuje, aby se ASP.NET Core hostovat na serveru nebo hostiteli kompatibilním s OWIN nebo pro jiné součásti kompatibilní s OWIN, které se mají spustit nad ASP.NET Core.

> [!NOTE]
> Používání těchto adaptérů se dodává s náklady na výkon. Aplikace, které `Microsoft.AspNetCore.Owin` používají jenom ASP.NET Core komponenty, by neměly používat balíčky ani adaptéry.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a>Spuštění middlewaru OWIN v kanálu ASP.NET Core

Podpora OWIN ASP.NET Core je nasazena jako součást `Microsoft.AspNetCore.Owin` balíčku. Podporu OWIN můžete do svého projektu importovat instalací tohoto balíčku.

Middleware OWIN je v souladu se [specifikací Owin](https://owin.org/spec/spec/owin-1.0.0.html), která vyžaduje `Func<IDictionary<string, object>, Task>` rozhraní a nastavené konkrétní klíče (například `owin.ResponseBody`). Následující jednoduchý middleware OWIN zobrazí "Hello World":

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

Vzorový podpis vrátí `Task` a přijme, `IDictionary<string, object>` jak vyžaduje Owin.

Následující kód ukazuje, jak přidat `OwinHello` middleware (viz výše) do kanálu ASP.NET Core s metodou `UseOwin` rozšíření.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

Můžete nakonfigurovat další akce, které budou provedeny v rámci kanálu OWIN.

> [!NOTE]
> Hlavičky odpovědi by se měly upravovat jenom před prvním zápisem do datového proudu odpovědí.

> [!NOTE]
> Z důvodů výkonu `UseOwin` není doporučeno více volání. Komponenty OWIN budou fungovat nejlépe, pokud jsou seskupeny dohromady.

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a>Použití ASP.NET Core hostování na serveru založeném na OWIN

Servery založené na OWIN můžou hostovat aplikace ASP.NET Core. Jeden takový server je [Nowin](https://github.com/Bobris/Nowin), webový server .NET Owin. V ukázce tohoto článku jsme zahrnuli projekt, který odkazuje na Nowin a používá ho k vytvoření ASP.NET Core `IServer` schopného samoobslužného hostování.

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

`IServer`je rozhraní, které vyžaduje `Features` vlastnost a `Start` metodu.

`Start`zodpovídá za konfiguraci a spuštění serveru, který v tomto případě provádí řada volání rozhraní Fluent API, která nastavila adresy přeložené z IServerAddressesFeature. Všimněte si, že konfigurace Fluent `_builder` proměnné určuje, že požadavky budou zpracovávány `appFunc` definované dříve v metodě. Tato `Func` metoda se volá u každé žádosti o zpracování příchozích požadavků.

Přidáme také `IWebHostBuilder` rozšíření, které usnadňuje přidání a konfiguraci serveru Nowin.

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

Když to zavoláte, vyvolejte rozšíření v *program.cs* a spusťte aplikaci ASP.NET Core s použitím tohoto vlastního serveru:

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

Přečtěte si další informace o [ASP.NET Corech serverech](xref:fundamentals/servers/index).

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>Spusťte ASP.NET Core na serveru založeném na OWIN a využijte podporu WebSockets.

Dalším příkladem použití ASP.NET Core funkcí serverů založených na OWIN je přístup k funkcím, jako jsou WebSockets. Webový server .NET OWIN použitý v předchozím příkladu obsahuje podporu pro integrované webové sokety, které může využívat ASP.NET Core aplikace. Níže uvedený příklad ukazuje jednoduchou webovou aplikaci, která podporuje webové sokety, a vrátí zpátky vše odeslané na server prostřednictvím objektů WebSockets.

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

Tato [Ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) je nakonfigurována pomocí stejné `NowinServer` jako předchozí, jediný rozdíl je v tom, jak je aplikace konfigurována v příslušné `Configure` metodě. Test použití [jednoduchého klienta protokolu WebSocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) ukazuje aplikaci:

![Testovací klient webového soketu](owin/_static/websocket-test.png)

## <a name="owin-environment"></a>Prostředí OWIN

Prostředí OWIN můžete vytvořit pomocí `HttpContext`.

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>OWIN klíče

OWIN závisí na `IDictionary<string,object>` objektu, který komunikuje informace v rámci výměny požadavků a odpovědí HTTP. ASP.NET Core implementuje níže uvedené klíče. Prohlédněte si [základní pravidla specifikace, rozšíření](https://owin.org/#spec)a [Owin Key a běžné klíče](https://owin.org/spec/spec/CommonKeys.html).

### <a name="request-data-owin-v100"></a>Data požadavku (OWIN v 1.0.0)

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| Owin. RequestScheme | `String` |  |
| Owin. RequestMethod  | `String` | |    
| Owin. RequestPathBase  | `String` | |    
| Owin. RequestPath | `String` | |     
| Owin. RequestQueryString  | `String` | |    
| Owin. RequestProtocol  | `String` | |    
| Owin. RequestHeaders hostitele | `IDictionary<string,string[]>`  | |
| Owin. Částmi | `Stream`  | |

### <a name="request-data-owin-v110"></a>Data požadavku (OWIN v 1.1.0)

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| Owin. Identifikátor | `String` | Nepovinné |

### <a name="response-data-owin-v100"></a>Data odpovědi (OWIN v 1.0.0)

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| Owin. ResponseStatusCode | `int` | Nepovinné |
| Owin. ResponseReasonPhrase | `String` | Nepovinné |
| Owin. ResponseHeaders hostitele | `IDictionary<string,string[]>`  | |
| Owin. ResponseBody | `Stream`  | |

### <a name="other-data-owin-v100"></a>Jiná data (OWIN v 1.0.0)

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| Owin. CallCancelled | `CancellationToken` |  |
| Owin. Znění  | `String` | |   

### <a name="common-keys"></a>Společné klíče

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| zabezpečení. ClientCertificate | `X509Certificate` |  |
| zabezpečení. LoadClientCertAsync  | `Func<Task>` | |    
| WebServer. RemoteIpAddress  | `String` | |    
| WebServer. RemotePort | `String` | |     
| WebServer. LocalIpAddress  | `String` | |    
| WebServer. LocalPort  | `String` | |    
| WebServer. Místní  | `bool` | |    
| WebServer. OnSendingHeaders  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a>SendFiles v 0.3.0

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| sendfile. SendAsync | Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | Za požadavek |

### <a name="opaque-v030"></a>Neprůhledný v 0.3.0

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| krytím. Znění | `String` |  |
| krytím. Přejít | `OpaqueUpgrade` | Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| krytím. Stream | `Stream` |  |
| krytím. CallCancelled | `CancellationToken` |  |

### <a name="websocket-v030"></a>WebSocket v 0.3.0

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| protokolu WebSocket. Znění | `String` |  |
| protokolu WebSocket. Vyjádřit | `WebSocketAccept` | Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| protokolu WebSocket. AcceptAlt |  | Bez specifikace |
| protokolu WebSocket. Dílčí protokol | `String` | Viz [část RFC6455 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) krok 5,5 |
| protokolu WebSocket. SendAsync | `WebSocketSendAsync` | Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| protokolu WebSocket. Metody ReceiveAsync | `WebSocketReceiveAsync` | Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| protokolu WebSocket. CloseAsync | `WebSocketCloseAsync` | Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| protokolu WebSocket. CallCancelled | `CancellationToken` |  |
| protokolu WebSocket. ClientCloseStatus | `int` | Nepovinné |
| protokolu WebSocket. ClientCloseDescription | `String` | Nepovinné |

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Middleware](xref:fundamentals/middleware/index)
* [Servery](xref:fundamentals/servers/index)

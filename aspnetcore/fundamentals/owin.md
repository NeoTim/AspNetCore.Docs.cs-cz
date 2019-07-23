---
title: Otevřete Web Interface pro .NET (OWIN) s ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core podporuje Open Web Interface pro .NET (OWIN), což umožní webové aplikace k oddělení od webových serverů.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 7edb4db026f1b778d43ac72883690a0b2a18ee31
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814899"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a>Otevřete Web Interface pro .NET (OWIN) s ASP.NET Core

Podle [Steve Smith](https://ardalis.com/) a [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core podporuje Open Web Interface pro .NET (OWIN). OWIN umožňuje webové aplikace k oddělení od webových serverů. Definuje standardní způsob pro middleware v kanálu použít ke zpracování požadavků a související odpovědi. Aplikace ASP.NET Core a middleware dokáže spolupracovat s aplikací OWIN, servery a middlewaru.

OWIN poskytuje oddělovací vrstvu, která umožňuje dvě architektury s různorodých objektové modely, který se má použít společně. `Microsoft.AspNetCore.Owin` Balíček poskytuje dvě implementace adaptér:

* ASP.NET Core do OWIN 
* OWIN k ASP.NET Core

To umožňuje zajistit také jejich hostování nad OWIN kompatibilní serveru/hostitele služby nebo pro jiné komponenty kompatibilní OWIN pro spuštění nad ASP.NET Core ASP.NET Core.

> [!NOTE]
> Pomocí těchto adaptérů součástí nákladů na výkon. Neměli byste používat aplikace s využitím pouze součásti ASP.NET Core `Microsoft.AspNetCore.Owin` balíčku nebo adaptéry.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a>Spuštění OWIN middleware v kanálu ASP.NET Core

Podpora OWIN ASP.NET Core je nasazen jako součást `Microsoft.AspNetCore.Owin` balíčku. Podpora OWIN můžete importovat do projektu po instalaci tohoto balíčku.

Middlewaru OWIN, který odpovídá [specifikace OWIN](https://owin.org/spec/spec/owin-1.0.0.html), což vyžaduje `Func<IDictionary<string, object>, Task>` nastaví rozhraní a konkrétní klíče (například `owin.ResponseBody`). Následující jednoduchý middlewaru OWIN, který se zobrazí "Hello World":

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

Vrátí vzorek podpis `Task` a přijímá `IDictionary<string, object>` podle požadavku OWIN.

Následující kód ukazuje, jak přidat `OwinHello` middleware (popsaný výš) do kanálu ASP.NET Core s `UseOwin` – metoda rozšíření.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

Můžete nakonfigurovat další akce, aby proběhla v rámci kanálu OWIN.

> [!NOTE]
> Hlavičky odpovědí by měl být upraven pouze před prvním zápisu do datového proudu odpovědi.

> [!NOTE]
> Více volání `UseOwin` se nedoporučuje kvůli výkonu. Komponenty OWIN bude fungovat nejlíp Pokud seskupené dohromady.

```csharp
app.UseOwin(pipeline =>
{
    pipeline(async (next) =>
    {
        // do something before
        await OwinHello(new OwinEnvironment(HttpContext));
        // do something after
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a>Použití hostování v technologii ASP.NET Core na server s procesorem OWIN

Na základě OWIN servery můžou hostovat aplikace ASP.NET Core. Jeden takový server je [Nowin](https://github.com/Bobris/Nowin), webový server .NET OWIN. V ukázce pro účely tohoto článku, můžu zahrnuli projekt, který odkazuje na Nowin a použije ho k vytvoření `IServer` dokáže ASP.NET Core s vlastním hostováním.

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

`IServer` je rozhraní, která vyžaduje `Features` vlastnost a `Start` metoda.

`Start` je zodpovědná za konfiguraci a spuštění serveru, který v tomto případě se provádí prostřednictvím řady fluent volání rozhraní API, které nastavit adresy analyzovat z IServerAddressesFeature. Všimněte si, že fluent konfiguraci `_builder` proměnná Určuje, že se zpracovat požadavky `appFunc` definovaný dříve v metodě. To `Func` je volána pro každý požadavek na zpracování příchozích požadavků.

Přidáme také `IWebHostBuilder` rozšíření, aby byly snadno přidávat a konfigurovat Nowin server.

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

Díky tomu na místě vyvolat rozšíření v *Program.cs* ke spuštění aplikace ASP.NET Core pomocí tohoto vlastního serveru:

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

Další informace o [ASP.NET Core servery](xref:fundamentals/servers/index).

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>Spustit ASP.NET Core na server s procesorem OWIN a použít jeho podporu Websocket

Další příklad, jak na základě OWIN servery funkcí mohou využívat technologie ASP.NET Core je přístup k funkcím, jako jsou objekty Websocket. Webový server .NET OWIN použitých v předchozím příkladu obsahuje podporu pro webové sokety součástí, které mohou využívat aplikace ASP.NET Core. Následující příklad ukazuje jednoduché webové aplikace, která podporuje objekty Websocket a vrátí zpět vše, co odeslána pro server Websocket.

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

To [ukázka](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) je nakonfigurovaný pomocí stejných `NowinServer` jako předchozí - jediný rozdíl je v konfiguraci aplikace v jeho `Configure` metoda. Test pomocí [jednoduchého objektu websocket na straně klienta](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) ukazuje aplikace:

![Testovací klient webových soketů](owin/_static/websocket-test.png)

## <a name="owin-environment"></a>Prostředí OWIN

Prostředí OWIN pomocí můžete sestavit `HttpContext`.

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>Klíče OWIN

OWIN závisí `IDictionary<string,object>` objekt ke sdělování informací v celé výměně požadavků/odpovědí HTTP. ASP.NET Core implementuje klíče uvedených níže. Zobrazit [primární specifikace, rozšíření](https://owin.org/#spec), a [pokyny klíč OWIN a společné klíče](https://owin.org/spec/spec/CommonKeys.html).

### <a name="request-data-owin-v100"></a>Data žádosti (OWIN v1.0.0)

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| owin.RequestScheme | `String` |  |
| owin. RequestMethod  | `String` | |    
| owin.RequestPathBase  | `String` | |    
| owin.RequestPath | `String` | |     
| owin.RequestQueryString  | `String` | |    
| owin.RequestProtocol  | `String` | |    
| owin. RequestHeaders | `IDictionary<string,string[]>`  | |
| owin.RequestBody | `Stream`  | |

### <a name="request-data-owin-v110"></a>Data žádosti (OWIN v1.1.0)

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| owin.RequestId | `String` | volitelná, |

### <a name="response-data-owin-v100"></a>Data odpovědi (OWIN v1.0.0)

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| owin.ResponseStatusCode | `int` | volitelná, |
| owin.ResponseReasonPhrase | `String` | volitelná, |
| owin.ResponseHeaders | `IDictionary<string,string[]>`  | |
| owin.ResponseBody | `Stream`  | |

### <a name="other-data-owin-v100"></a>Další data (OWIN v1.0.0)

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| owin.CallCancelled | `CancellationToken` |  |
| owin.Version  | `String` | |   

### <a name="common-keys"></a>Společné klíče

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| ssl.ClientCertificate | `X509Certificate` |  |
| ssl.LoadClientCertAsync  | `Func<Task>` | |    
| server.RemoteIpAddress  | `String` | |    
| server.RemotePort | `String` | |     
| server.LocalIpAddress  | `String` | |    
| server.LocalPort  | `String` | |    
| server.IsLocal  | `bool` | |    
| server.OnSendingHeaders  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| sendfile.SendAsync | Zobrazit [signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | Každý požadavek |

### <a name="opaque-v030"></a>Neprůhledný v0.3.0

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| opaque.Version | `String` |  |
| opaque.Upgrade | `OpaqueUpgrade` | Zobrazit [signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| opaque.Stream | `Stream` |  |
| opaque.CallCancelled | `CancellationToken` |  |

### <a name="websocket-v030"></a>WebSocket v0.3.0

| Key               | Hodnota (typ) | Popis |
| ----------------- | ------------ | ----------- |
| websocket.Version | `String` |  |
| objekt websocket. Přijmout | `WebSocketAccept` | Zobrazit [signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| websocket.AcceptAlt |  | Bez specifikace |
| objekt websocket. Dílčí protokol | `String` | Zobrazit [RFC6455 části 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) krok 5.5 |
| websocket.SendAsync | `WebSocketSendAsync` | Zobrazit [signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.ReceiveAsync | `WebSocketReceiveAsync` | Zobrazit [signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.CloseAsync | `WebSocketCloseAsync` | Zobrazit [signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| objekt websocket. CallCancelled | `CancellationToken` |  |
| websocket.ClientCloseStatus | `int` | volitelná, |
| websocket.ClientCloseDescription | `String` | volitelná, |

## <a name="additional-resources"></a>Další zdroje

* [Middleware](xref:fundamentals/middleware/index)
* [Servery](xref:fundamentals/servers/index)

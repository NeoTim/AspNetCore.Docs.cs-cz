---
title: Otevřete web interface for .NET (OWIN) s ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core podporuje rozhraní Open Web Interface for .NET (OWIN), které umožňuje oddělit webové aplikace od webových serverů.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/owin
ms.openlocfilehash: d766ba3387edbfb9298b6f3cf8a485738b7d7139
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628596"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="f3da8-103">Otevřete web interface for .NET (OWIN) s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3da8-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="f3da8-104">[Steve Smith](https://ardalis.com/) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f3da8-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f3da8-105">ASP.NET Core podporuje otevřené webové rozhraní pro .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="f3da8-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="f3da8-106">OWIN umožňuje oddělit webové aplikace od webových serverů.</span><span class="sxs-lookup"><span data-stu-id="f3da8-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="f3da8-107">Definuje standardní způsob, jakým se má middleware použít v kanálu ke zpracování požadavků a přidružených odpovědí.</span><span class="sxs-lookup"><span data-stu-id="f3da8-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="f3da8-108">ASP.NET Core aplikace a middleware se můžou vzájemně spolupracovat s aplikacemi, servery a middlewarem založeným na OWIN.</span><span class="sxs-lookup"><span data-stu-id="f3da8-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="f3da8-109">OWIN poskytuje rozvětvenou vrstvu, která umožňuje používat dvě architektury s různorodými objektovými modely.</span><span class="sxs-lookup"><span data-stu-id="f3da8-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="f3da8-110">`Microsoft.AspNetCore.Owin`Balíček poskytuje dva implementace adaptérů:</span><span class="sxs-lookup"><span data-stu-id="f3da8-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="f3da8-111">ASP.NET Core OWIN</span><span class="sxs-lookup"><span data-stu-id="f3da8-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="f3da8-112">OWIN na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3da8-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="f3da8-113">To umožňuje, aby se ASP.NET Core hostovat na serveru nebo hostiteli kompatibilním s OWIN nebo pro jiné součásti kompatibilní s OWIN, které se mají spustit nad ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3da8-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="f3da8-114">Používání těchto adaptérů se dodává s náklady na výkon.</span><span class="sxs-lookup"><span data-stu-id="f3da8-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="f3da8-115">Aplikace, které používají jenom ASP.NET Core komponenty, by neměly používat `Microsoft.AspNetCore.Owin` balíčky ani adaptéry.</span><span class="sxs-lookup"><span data-stu-id="f3da8-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="f3da8-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f3da8-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="f3da8-117">Spuštění middlewaru OWIN v kanálu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f3da8-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="f3da8-118">Podpora OWIN ASP.NET Core je nasazena jako součást `Microsoft.AspNetCore.Owin` balíčku.</span><span class="sxs-lookup"><span data-stu-id="f3da8-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="f3da8-119">Podporu OWIN můžete do svého projektu importovat instalací tohoto balíčku.</span><span class="sxs-lookup"><span data-stu-id="f3da8-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="f3da8-120">Middleware OWIN je v souladu se [specifikací Owin](https://owin.org/spec/spec/owin-1.0.0.html), která vyžaduje `Func<IDictionary<string, object>, Task>` rozhraní a nastavené konkrétní klíče (například `owin.ResponseBody` ).</span><span class="sxs-lookup"><span data-stu-id="f3da8-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="f3da8-121">Následující jednoduchý middleware OWIN zobrazí "Hello World":</span><span class="sxs-lookup"><span data-stu-id="f3da8-121">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="f3da8-122">Vzorový podpis vrátí `Task` a přijme, `IDictionary<string, object>` jak vyžaduje Owin.</span><span class="sxs-lookup"><span data-stu-id="f3da8-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="f3da8-123">Následující kód ukazuje, jak přidat `OwinHello` middleware (viz výše) do kanálu ASP.NET Core s `UseOwin` metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f3da8-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="f3da8-124">Můžete nakonfigurovat další akce, které budou provedeny v rámci kanálu OWIN.</span><span class="sxs-lookup"><span data-stu-id="f3da8-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="f3da8-125">Hlavičky odpovědi by se měly upravovat jenom před prvním zápisem do datového proudu odpovědí.</span><span class="sxs-lookup"><span data-stu-id="f3da8-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="f3da8-126">`UseOwin`Z důvodů výkonu není doporučeno více volání.</span><span class="sxs-lookup"><span data-stu-id="f3da8-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="f3da8-127">Komponenty OWIN budou fungovat nejlépe, pokud jsou seskupeny dohromady.</span><span class="sxs-lookup"><span data-stu-id="f3da8-127">OWIN components will operate best if grouped together.</span></span>

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="f3da8-128">Použití ASP.NET Core hostování na serveru založeném na OWIN</span><span class="sxs-lookup"><span data-stu-id="f3da8-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="f3da8-129">Servery založené na OWIN můžou hostovat aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f3da8-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="f3da8-130">Jeden takový server je [Nowin](https://github.com/Bobris/Nowin), webový server .NET Owin.</span><span class="sxs-lookup"><span data-stu-id="f3da8-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="f3da8-131">V ukázce tohoto článku jsme zahrnuli projekt, který odkazuje na Nowin a používá ho k vytvoření `IServer` ASP.NET Core schopného samoobslužného hostování.</span><span class="sxs-lookup"><span data-stu-id="f3da8-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="f3da8-132">`IServer` je rozhraní, které vyžaduje `Features` vlastnost a `Start` metodu.</span><span class="sxs-lookup"><span data-stu-id="f3da8-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="f3da8-133">`Start` zodpovídá za konfiguraci a spuštění serveru, který v tomto případě provádí řada volání rozhraní Fluent API, která nastavila adresy přeložené z IServerAddressesFeature.</span><span class="sxs-lookup"><span data-stu-id="f3da8-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="f3da8-134">Všimněte si, že konfigurace Fluent `_builder` proměnné určuje, že požadavky budou zpracovávány `appFunc` definované dříve v metodě.</span><span class="sxs-lookup"><span data-stu-id="f3da8-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="f3da8-135">Tato `Func` metoda se volá u každé žádosti o zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="f3da8-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="f3da8-136">Přidáme také `IWebHostBuilder` rozšíření, které usnadňuje přidání a konfiguraci serveru Nowin.</span><span class="sxs-lookup"><span data-stu-id="f3da8-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

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

<span data-ttu-id="f3da8-137">Když to zavoláte, vyvolejte rozšíření v *program.cs* a spusťte aplikaci ASP.NET Core s použitím tohoto vlastního serveru:</span><span class="sxs-lookup"><span data-stu-id="f3da8-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

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

<span data-ttu-id="f3da8-138">Přečtěte si další informace o [ASP.NET Corech serverech](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="f3da8-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="f3da8-139">Spusťte ASP.NET Core na serveru založeném na OWIN a využijte podporu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f3da8-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="f3da8-140">Dalším příkladem použití ASP.NET Core funkcí serverů založených na OWIN je přístup k funkcím, jako jsou WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f3da8-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="f3da8-141">Webový server .NET OWIN použitý v předchozím příkladu obsahuje podporu pro integrované webové sokety, které může využívat ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="f3da8-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="f3da8-142">Níže uvedený příklad ukazuje jednoduchou webovou aplikaci, která podporuje webové sokety, a vrátí zpátky vše odeslané na server prostřednictvím objektů WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f3da8-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

<span data-ttu-id="f3da8-143">Tato [Ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) je nakonfigurována pomocí stejné `NowinServer` jako předchozí, jediný rozdíl je v tom, jak je aplikace konfigurována v příslušné `Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="f3da8-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="f3da8-144">Test použití [jednoduchého klienta protokolu WebSocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) ukazuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="f3da8-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Testovací klient webového soketu](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="f3da8-146">Prostředí OWIN</span><span class="sxs-lookup"><span data-stu-id="f3da8-146">OWIN environment</span></span>

<span data-ttu-id="f3da8-147">Prostředí OWIN můžete vytvořit pomocí `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="f3da8-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="f3da8-148">OWIN klíče</span><span class="sxs-lookup"><span data-stu-id="f3da8-148">OWIN keys</span></span>

<span data-ttu-id="f3da8-149">OWIN závisí na `IDictionary<string,object>` objektu, který komunikuje informace v rámci výměny požadavků a odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="f3da8-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="f3da8-150">ASP.NET Core implementuje níže uvedené klíče.</span><span class="sxs-lookup"><span data-stu-id="f3da8-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="f3da8-151">Prohlédněte si [základní pravidla specifikace, rozšíření](https://owin.org/#spec)a [Owin Key a běžné klíče](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="f3da8-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="f3da8-152">Data požadavku (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="f3da8-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="f3da8-153">Klíč</span><span class="sxs-lookup"><span data-stu-id="f3da8-153">Key</span></span>               | <span data-ttu-id="f3da8-154">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f3da8-154">Value (type)</span></span> | <span data-ttu-id="f3da8-155">Popis</span><span class="sxs-lookup"><span data-stu-id="f3da8-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f3da8-156">Owin. RequestScheme</span><span class="sxs-lookup"><span data-stu-id="f3da8-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="f3da8-157">Owin. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="f3da8-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="f3da8-158">Owin. RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="f3da8-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="f3da8-159">Owin. RequestPath</span><span class="sxs-lookup"><span data-stu-id="f3da8-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="f3da8-160">Owin. RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="f3da8-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="f3da8-161">Owin. RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="f3da8-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="f3da8-162">Owin. RequestHeaders hostitele</span><span class="sxs-lookup"><span data-stu-id="f3da8-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="f3da8-163">Owin. Částmi</span><span class="sxs-lookup"><span data-stu-id="f3da8-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="f3da8-164">Data požadavku (OWIN v 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="f3da8-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="f3da8-165">Klíč</span><span class="sxs-lookup"><span data-stu-id="f3da8-165">Key</span></span>               | <span data-ttu-id="f3da8-166">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f3da8-166">Value (type)</span></span> | <span data-ttu-id="f3da8-167">Popis</span><span class="sxs-lookup"><span data-stu-id="f3da8-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f3da8-168">Owin. Identifikátor</span><span class="sxs-lookup"><span data-stu-id="f3da8-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="f3da8-169">Volitelné</span><span class="sxs-lookup"><span data-stu-id="f3da8-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="f3da8-170">Data odpovědi (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="f3da8-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="f3da8-171">Klíč</span><span class="sxs-lookup"><span data-stu-id="f3da8-171">Key</span></span>               | <span data-ttu-id="f3da8-172">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f3da8-172">Value (type)</span></span> | <span data-ttu-id="f3da8-173">Popis</span><span class="sxs-lookup"><span data-stu-id="f3da8-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f3da8-174">Owin. ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="f3da8-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="f3da8-175">Volitelné</span><span class="sxs-lookup"><span data-stu-id="f3da8-175">Optional</span></span> |
| <span data-ttu-id="f3da8-176">Owin. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="f3da8-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="f3da8-177">Volitelné</span><span class="sxs-lookup"><span data-stu-id="f3da8-177">Optional</span></span> |
| <span data-ttu-id="f3da8-178">Owin. ResponseHeaders hostitele</span><span class="sxs-lookup"><span data-stu-id="f3da8-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="f3da8-179">Owin. ResponseBody</span><span class="sxs-lookup"><span data-stu-id="f3da8-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="f3da8-180">Jiná data (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="f3da8-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="f3da8-181">Klíč</span><span class="sxs-lookup"><span data-stu-id="f3da8-181">Key</span></span>               | <span data-ttu-id="f3da8-182">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f3da8-182">Value (type)</span></span> | <span data-ttu-id="f3da8-183">Popis</span><span class="sxs-lookup"><span data-stu-id="f3da8-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f3da8-184">Owin. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="f3da8-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="f3da8-185">Owin. Znění</span><span class="sxs-lookup"><span data-stu-id="f3da8-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="f3da8-186">Společné klíče</span><span class="sxs-lookup"><span data-stu-id="f3da8-186">Common keys</span></span>

| <span data-ttu-id="f3da8-187">Klíč</span><span class="sxs-lookup"><span data-stu-id="f3da8-187">Key</span></span>               | <span data-ttu-id="f3da8-188">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f3da8-188">Value (type)</span></span> | <span data-ttu-id="f3da8-189">Popis</span><span class="sxs-lookup"><span data-stu-id="f3da8-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f3da8-190">zabezpečení. ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="f3da8-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="f3da8-191">zabezpečení. LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="f3da8-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="f3da8-192">WebServer. RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="f3da8-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="f3da8-193">WebServer. RemotePort</span><span class="sxs-lookup"><span data-stu-id="f3da8-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="f3da8-194">WebServer. LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="f3da8-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="f3da8-195">WebServer. LocalPort</span><span class="sxs-lookup"><span data-stu-id="f3da8-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="f3da8-196">WebServer. Místní</span><span class="sxs-lookup"><span data-stu-id="f3da8-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="f3da8-197">WebServer. OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="f3da8-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="f3da8-198">SendFiles v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="f3da8-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="f3da8-199">Klíč</span><span class="sxs-lookup"><span data-stu-id="f3da8-199">Key</span></span>               | <span data-ttu-id="f3da8-200">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f3da8-200">Value (type)</span></span> | <span data-ttu-id="f3da8-201">Popis</span><span class="sxs-lookup"><span data-stu-id="f3da8-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f3da8-202">sendfile. SendAsync</span><span class="sxs-lookup"><span data-stu-id="f3da8-202">sendfile.SendAsync</span></span> | <span data-ttu-id="f3da8-203">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f3da8-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="f3da8-204">Za požadavek</span><span class="sxs-lookup"><span data-stu-id="f3da8-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="f3da8-205">Neprůhledný v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="f3da8-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="f3da8-206">Klíč</span><span class="sxs-lookup"><span data-stu-id="f3da8-206">Key</span></span>               | <span data-ttu-id="f3da8-207">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f3da8-207">Value (type)</span></span> | <span data-ttu-id="f3da8-208">Popis</span><span class="sxs-lookup"><span data-stu-id="f3da8-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f3da8-209">krytím. Znění</span><span class="sxs-lookup"><span data-stu-id="f3da8-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="f3da8-210">krytím. Přejít</span><span class="sxs-lookup"><span data-stu-id="f3da8-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="f3da8-211">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f3da8-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="f3da8-212">krytím. Stream</span><span class="sxs-lookup"><span data-stu-id="f3da8-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="f3da8-213">krytím. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="f3da8-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="f3da8-214">WebSocket v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="f3da8-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="f3da8-215">Klíč</span><span class="sxs-lookup"><span data-stu-id="f3da8-215">Key</span></span>               | <span data-ttu-id="f3da8-216">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f3da8-216">Value (type)</span></span> | <span data-ttu-id="f3da8-217">Popis</span><span class="sxs-lookup"><span data-stu-id="f3da8-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f3da8-218">protokolu WebSocket. Znění</span><span class="sxs-lookup"><span data-stu-id="f3da8-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="f3da8-219">protokolu WebSocket. Vyjádřit</span><span class="sxs-lookup"><span data-stu-id="f3da8-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="f3da8-220">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f3da8-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="f3da8-221">protokolu WebSocket. AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="f3da8-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="f3da8-222">Bez specifikace</span><span class="sxs-lookup"><span data-stu-id="f3da8-222">Non-spec</span></span> |
| <span data-ttu-id="f3da8-223">protokolu WebSocket. Dílčí protokol</span><span class="sxs-lookup"><span data-stu-id="f3da8-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="f3da8-224">Viz [část RFC6455 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) krok 5,5</span><span class="sxs-lookup"><span data-stu-id="f3da8-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="f3da8-225">protokolu WebSocket. SendAsync</span><span class="sxs-lookup"><span data-stu-id="f3da8-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="f3da8-226">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f3da8-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="f3da8-227">protokolu WebSocket. Metody ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="f3da8-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="f3da8-228">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f3da8-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="f3da8-229">protokolu WebSocket. CloseAsync</span><span class="sxs-lookup"><span data-stu-id="f3da8-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="f3da8-230">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f3da8-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="f3da8-231">protokolu WebSocket. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="f3da8-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="f3da8-232">protokolu WebSocket. ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="f3da8-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="f3da8-233">Volitelné</span><span class="sxs-lookup"><span data-stu-id="f3da8-233">Optional</span></span> |
| <span data-ttu-id="f3da8-234">protokolu WebSocket. ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="f3da8-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="f3da8-235">Volitelné</span><span class="sxs-lookup"><span data-stu-id="f3da8-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="f3da8-236">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f3da8-236">Additional resources</span></span>

* [<span data-ttu-id="f3da8-237">Middleware</span><span class="sxs-lookup"><span data-stu-id="f3da8-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="f3da8-238">Servery</span><span class="sxs-lookup"><span data-stu-id="f3da8-238">Servers</span></span>](xref:fundamentals/servers/index)

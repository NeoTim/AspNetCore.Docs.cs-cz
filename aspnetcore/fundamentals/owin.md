---
title: Otevřete Web Interface pro .NET (OWIN) s ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core podporuje Open Web Interface pro .NET (OWIN), což umožní webové aplikace k oddělení od webových serverů.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 9d6ce79c15fe768c260c6361ac3babecab5f3f9b
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087304"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="d22ba-103">Otevřete Web Interface pro .NET (OWIN) s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d22ba-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="d22ba-104">Podle [Steve Smith](https://ardalis.com/) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d22ba-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d22ba-105">ASP.NET Core podporuje Open Web Interface pro .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="d22ba-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="d22ba-106">OWIN umožňuje webové aplikace k oddělení od webových serverů.</span><span class="sxs-lookup"><span data-stu-id="d22ba-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="d22ba-107">Definuje standardní způsob pro middleware v kanálu použít ke zpracování požadavků a související odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d22ba-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="d22ba-108">Aplikace ASP.NET Core a middleware dokáže spolupracovat s aplikací OWIN, servery a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="d22ba-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="d22ba-109">OWIN poskytuje oddělovací vrstvu, která umožňuje dvě architektury s různorodých objektové modely, který se má použít společně.</span><span class="sxs-lookup"><span data-stu-id="d22ba-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="d22ba-110">`Microsoft.AspNetCore.Owin` Balíček poskytuje dvě implementace adaptér:</span><span class="sxs-lookup"><span data-stu-id="d22ba-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="d22ba-111">ASP.NET Core do OWIN</span><span class="sxs-lookup"><span data-stu-id="d22ba-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="d22ba-112">OWIN k ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d22ba-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="d22ba-113">To umožňuje zajistit také jejich hostování nad OWIN kompatibilní serveru/hostitele služby nebo pro jiné komponenty kompatibilní OWIN pro spuštění nad ASP.NET Core ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d22ba-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="d22ba-114">Pomocí těchto adaptérů součástí nákladů na výkon.</span><span class="sxs-lookup"><span data-stu-id="d22ba-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="d22ba-115">Neměli byste používat aplikace s využitím pouze součásti ASP.NET Core `Microsoft.AspNetCore.Owin` balíčku nebo adaptéry.</span><span class="sxs-lookup"><span data-stu-id="d22ba-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="d22ba-116">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d22ba-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="d22ba-117">Spuštění OWIN middleware v kanálu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d22ba-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="d22ba-118">Podpora OWIN ASP.NET Core je nasazen jako součást `Microsoft.AspNetCore.Owin` balíčku.</span><span class="sxs-lookup"><span data-stu-id="d22ba-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="d22ba-119">Podpora OWIN můžete importovat do projektu po instalaci tohoto balíčku.</span><span class="sxs-lookup"><span data-stu-id="d22ba-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="d22ba-120">Middlewaru OWIN, který odpovídá [specifikace OWIN](http://owin.org/spec/spec/owin-1.0.0.html), což vyžaduje `Func<IDictionary<string, object>, Task>` nastaví rozhraní a konkrétní klíče (například `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="d22ba-120">OWIN middleware conforms to the [OWIN specification](http://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="d22ba-121">Následující jednoduchý middlewaru OWIN, který se zobrazí "Hello World":</span><span class="sxs-lookup"><span data-stu-id="d22ba-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: http://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="d22ba-122">Vrátí vzorek podpis `Task` a přijímá `IDictionary<string, object>` podle požadavku OWIN.</span><span class="sxs-lookup"><span data-stu-id="d22ba-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="d22ba-123">Následující kód ukazuje, jak přidat `OwinHello` middleware (popsaný výš) do kanálu ASP.NET Core s `UseOwin` – metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="d22ba-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="d22ba-124">Můžete nakonfigurovat další akce, aby proběhla v rámci kanálu OWIN.</span><span class="sxs-lookup"><span data-stu-id="d22ba-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="d22ba-125">Hlavičky odpovědí by měl být upraven pouze před prvním zápisu do datového proudu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d22ba-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="d22ba-126">Více volání `UseOwin` se nedoporučuje kvůli výkonu.</span><span class="sxs-lookup"><span data-stu-id="d22ba-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="d22ba-127">Komponenty OWIN bude fungovat nejlíp Pokud seskupené dohromady.</span><span class="sxs-lookup"><span data-stu-id="d22ba-127">OWIN components will operate best if grouped together.</span></span>

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="d22ba-128">Použití hostování v technologii ASP.NET Core na server s procesorem OWIN</span><span class="sxs-lookup"><span data-stu-id="d22ba-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="d22ba-129">Na základě OWIN servery můžou hostovat aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d22ba-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="d22ba-130">Jeden takový server je [Nowin](https://github.com/Bobris/Nowin), webový server .NET OWIN.</span><span class="sxs-lookup"><span data-stu-id="d22ba-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="d22ba-131">V ukázce pro účely tohoto článku, můžu zahrnuli projekt, který odkazuje na Nowin a použije ho k vytvoření `IServer` dokáže ASP.NET Core s vlastním hostováním.</span><span class="sxs-lookup"><span data-stu-id="d22ba-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="d22ba-132">`IServer` je rozhraní, která vyžaduje `Features` vlastnost a `Start` metoda.</span><span class="sxs-lookup"><span data-stu-id="d22ba-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="d22ba-133">`Start` je zodpovědná za konfiguraci a spuštění serveru, který v tomto případě se provádí prostřednictvím řady fluent volání rozhraní API, které nastavit adresy analyzovat z IServerAddressesFeature.</span><span class="sxs-lookup"><span data-stu-id="d22ba-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="d22ba-134">Všimněte si, že fluent konfiguraci `_builder` proměnná Určuje, že se zpracovat požadavky `appFunc` definovaný dříve v metodě.</span><span class="sxs-lookup"><span data-stu-id="d22ba-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="d22ba-135">To `Func` je volána pro každý požadavek na zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="d22ba-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="d22ba-136">Přidáme také `IWebHostBuilder` rozšíření, aby byly snadno přidávat a konfigurovat Nowin server.</span><span class="sxs-lookup"><span data-stu-id="d22ba-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

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

<span data-ttu-id="d22ba-137">Díky tomu na místě vyvolat rozšíření v *Program.cs* ke spuštění aplikace ASP.NET Core pomocí tohoto vlastního serveru:</span><span class="sxs-lookup"><span data-stu-id="d22ba-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

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

<span data-ttu-id="d22ba-138">Další informace o [ASP.NET Core servery](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="d22ba-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="d22ba-139">Spustit ASP.NET Core na server s procesorem OWIN a použít jeho podporu Websocket</span><span class="sxs-lookup"><span data-stu-id="d22ba-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="d22ba-140">Další příklad, jak na základě OWIN servery funkcí mohou využívat technologie ASP.NET Core je přístup k funkcím, jako jsou objekty Websocket.</span><span class="sxs-lookup"><span data-stu-id="d22ba-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="d22ba-141">Webový server .NET OWIN použitých v předchozím příkladu obsahuje podporu pro webové sokety součástí, které mohou využívat aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d22ba-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="d22ba-142">Následující příklad ukazuje jednoduché webové aplikace, která podporuje objekty Websocket a vrátí zpět vše, co odeslána pro server Websocket.</span><span class="sxs-lookup"><span data-stu-id="d22ba-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

<span data-ttu-id="d22ba-143">To [ukázka](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) je nakonfigurovaný pomocí stejných `NowinServer` jako předchozí - jediný rozdíl je v konfiguraci aplikace v jeho `Configure` metoda.</span><span class="sxs-lookup"><span data-stu-id="d22ba-143">This [sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="d22ba-144">Test pomocí [jednoduchého objektu websocket na straně klienta](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) ukazuje aplikace:</span><span class="sxs-lookup"><span data-stu-id="d22ba-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Testovací klient webových soketů](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="d22ba-146">Prostředí OWIN</span><span class="sxs-lookup"><span data-stu-id="d22ba-146">OWIN environment</span></span>

<span data-ttu-id="d22ba-147">Prostředí OWIN pomocí můžete sestavit `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="d22ba-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="d22ba-148">Klíče OWIN</span><span class="sxs-lookup"><span data-stu-id="d22ba-148">OWIN keys</span></span>

<span data-ttu-id="d22ba-149">OWIN závisí `IDictionary<string,object>` objekt ke sdělování informací v celé výměně požadavků/odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="d22ba-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="d22ba-150">ASP.NET Core implementuje klíče uvedených níže.</span><span class="sxs-lookup"><span data-stu-id="d22ba-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="d22ba-151">Zobrazit [primární specifikace, rozšíření](http://owin.org/#spec), a [pokyny klíč OWIN a společné klíče](http://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="d22ba-151">See the [primary specification, extensions](http://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](http://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="d22ba-152">Data žádosti (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d22ba-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d22ba-153">Key</span><span class="sxs-lookup"><span data-stu-id="d22ba-153">Key</span></span>               | <span data-ttu-id="d22ba-154">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d22ba-154">Value (type)</span></span> | <span data-ttu-id="d22ba-155">Popis</span><span class="sxs-lookup"><span data-stu-id="d22ba-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d22ba-156">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="d22ba-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="d22ba-157">owin. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="d22ba-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="d22ba-158">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="d22ba-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="d22ba-159">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="d22ba-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="d22ba-160">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="d22ba-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="d22ba-161">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="d22ba-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="d22ba-162">owin. RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="d22ba-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="d22ba-163">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="d22ba-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="d22ba-164">Data žádosti (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="d22ba-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="d22ba-165">Key</span><span class="sxs-lookup"><span data-stu-id="d22ba-165">Key</span></span>               | <span data-ttu-id="d22ba-166">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d22ba-166">Value (type)</span></span> | <span data-ttu-id="d22ba-167">Popis</span><span class="sxs-lookup"><span data-stu-id="d22ba-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d22ba-168">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="d22ba-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="d22ba-169">volitelná,</span><span class="sxs-lookup"><span data-stu-id="d22ba-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="d22ba-170">Data odpovědi (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d22ba-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d22ba-171">Key</span><span class="sxs-lookup"><span data-stu-id="d22ba-171">Key</span></span>               | <span data-ttu-id="d22ba-172">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d22ba-172">Value (type)</span></span> | <span data-ttu-id="d22ba-173">Popis</span><span class="sxs-lookup"><span data-stu-id="d22ba-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d22ba-174">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="d22ba-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="d22ba-175">volitelná,</span><span class="sxs-lookup"><span data-stu-id="d22ba-175">Optional</span></span> |
| <span data-ttu-id="d22ba-176">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="d22ba-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="d22ba-177">volitelná,</span><span class="sxs-lookup"><span data-stu-id="d22ba-177">Optional</span></span> |
| <span data-ttu-id="d22ba-178">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="d22ba-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="d22ba-179">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="d22ba-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="d22ba-180">Další data (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d22ba-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d22ba-181">Key</span><span class="sxs-lookup"><span data-stu-id="d22ba-181">Key</span></span>               | <span data-ttu-id="d22ba-182">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d22ba-182">Value (type)</span></span> | <span data-ttu-id="d22ba-183">Popis</span><span class="sxs-lookup"><span data-stu-id="d22ba-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d22ba-184">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d22ba-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="d22ba-185">owin.Version</span><span class="sxs-lookup"><span data-stu-id="d22ba-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="d22ba-186">Společné klíče</span><span class="sxs-lookup"><span data-stu-id="d22ba-186">Common keys</span></span>

| <span data-ttu-id="d22ba-187">Key</span><span class="sxs-lookup"><span data-stu-id="d22ba-187">Key</span></span>               | <span data-ttu-id="d22ba-188">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d22ba-188">Value (type)</span></span> | <span data-ttu-id="d22ba-189">Popis</span><span class="sxs-lookup"><span data-stu-id="d22ba-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d22ba-190">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="d22ba-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="d22ba-191">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="d22ba-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="d22ba-192">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="d22ba-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="d22ba-193">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="d22ba-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="d22ba-194">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="d22ba-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="d22ba-195">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="d22ba-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="d22ba-196">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="d22ba-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="d22ba-197">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="d22ba-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="d22ba-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="d22ba-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="d22ba-199">Key</span><span class="sxs-lookup"><span data-stu-id="d22ba-199">Key</span></span>               | <span data-ttu-id="d22ba-200">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d22ba-200">Value (type)</span></span> | <span data-ttu-id="d22ba-201">Popis</span><span class="sxs-lookup"><span data-stu-id="d22ba-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d22ba-202">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="d22ba-202">sendfile.SendAsync</span></span> | <span data-ttu-id="d22ba-203">Zobrazit [signatura delegáta](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d22ba-203">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="d22ba-204">Každý požadavek</span><span class="sxs-lookup"><span data-stu-id="d22ba-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="d22ba-205">Neprůhledný v0.3.0</span><span class="sxs-lookup"><span data-stu-id="d22ba-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="d22ba-206">Key</span><span class="sxs-lookup"><span data-stu-id="d22ba-206">Key</span></span>               | <span data-ttu-id="d22ba-207">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d22ba-207">Value (type)</span></span> | <span data-ttu-id="d22ba-208">Popis</span><span class="sxs-lookup"><span data-stu-id="d22ba-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d22ba-209">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="d22ba-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="d22ba-210">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="d22ba-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="d22ba-211">Zobrazit [signatura delegáta](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d22ba-211">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="d22ba-212">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="d22ba-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="d22ba-213">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d22ba-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="d22ba-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="d22ba-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="d22ba-215">Key</span><span class="sxs-lookup"><span data-stu-id="d22ba-215">Key</span></span>               | <span data-ttu-id="d22ba-216">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d22ba-216">Value (type)</span></span> | <span data-ttu-id="d22ba-217">Popis</span><span class="sxs-lookup"><span data-stu-id="d22ba-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d22ba-218">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="d22ba-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="d22ba-219">objekt websocket. Přijmout</span><span class="sxs-lookup"><span data-stu-id="d22ba-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="d22ba-220">Zobrazit [signatura delegáta](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d22ba-220">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="d22ba-221">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="d22ba-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="d22ba-222">Bez specifikace</span><span class="sxs-lookup"><span data-stu-id="d22ba-222">Non-spec</span></span> |
| <span data-ttu-id="d22ba-223">objekt websocket. Dílčí protokol</span><span class="sxs-lookup"><span data-stu-id="d22ba-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="d22ba-224">Zobrazit [RFC6455 části 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) krok 5.5</span><span class="sxs-lookup"><span data-stu-id="d22ba-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="d22ba-225">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="d22ba-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="d22ba-226">Zobrazit [signatura delegáta](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d22ba-226">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d22ba-227">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="d22ba-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="d22ba-228">Zobrazit [signatura delegáta](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d22ba-228">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d22ba-229">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="d22ba-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="d22ba-230">Zobrazit [signatura delegáta](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d22ba-230">See [delegate signature](http://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d22ba-231">objekt websocket. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d22ba-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="d22ba-232">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="d22ba-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="d22ba-233">volitelná,</span><span class="sxs-lookup"><span data-stu-id="d22ba-233">Optional</span></span> |
| <span data-ttu-id="d22ba-234">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="d22ba-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="d22ba-235">volitelná,</span><span class="sxs-lookup"><span data-stu-id="d22ba-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d22ba-236">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d22ba-236">Additional resources</span></span>

* [<span data-ttu-id="d22ba-237">Middleware</span><span class="sxs-lookup"><span data-stu-id="d22ba-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="d22ba-238">Servery</span><span class="sxs-lookup"><span data-stu-id="d22ba-238">Servers</span></span>](xref:fundamentals/servers/index)

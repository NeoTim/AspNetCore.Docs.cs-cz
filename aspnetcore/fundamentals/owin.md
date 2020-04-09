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
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="c3505-103">Otevřít webové rozhraní pro rozhraní .NET (OWIN) s ASP.NET jádrem</span><span class="sxs-lookup"><span data-stu-id="c3505-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="c3505-104">[Steve Smith](https://ardalis.com/) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c3505-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c3505-105">ASP.NET Core podporuje otevřené webové rozhraní pro rozhraní .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="c3505-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="c3505-106">OWIN umožňuje oddělení webových aplikací od webových serverů.</span><span class="sxs-lookup"><span data-stu-id="c3505-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="c3505-107">Definuje standardní způsob, jak middleware, které mají být použity v kanálu pro zpracování požadavků a přidružené odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c3505-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="c3505-108">ASP.NET Core aplikace a middleware mohou spolupracovat s aplikacemi, servery a middlewarem založenými na OWIN.</span><span class="sxs-lookup"><span data-stu-id="c3505-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="c3505-109">OWIN poskytuje vrstvu oddělení, která umožňuje použití dvou rámců s různorodými objektovými modely.</span><span class="sxs-lookup"><span data-stu-id="c3505-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="c3505-110">Balíček `Microsoft.AspNetCore.Owin` obsahuje dvě implementace adaptéru:</span><span class="sxs-lookup"><span data-stu-id="c3505-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="c3505-111">ASP.NET jádro pro OWIN</span><span class="sxs-lookup"><span data-stu-id="c3505-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="c3505-112">OWIN na ASP.NET jádro</span><span class="sxs-lookup"><span data-stu-id="c3505-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="c3505-113">To umožňuje ASP.NET Core, které mají být umístěny na vrcholu OWIN kompatibilní server / host nebo pro jiné OWIN kompatibilní komponenty, které mají být spuštěny na vrcholu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c3505-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="c3505-114">Použití těchto adaptérů je dodáváno s náklady na výkon.</span><span class="sxs-lookup"><span data-stu-id="c3505-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="c3505-115">Aplikace, které používají jenom ASP.NET `Microsoft.AspNetCore.Owin` komponenty Core, by neměly používat balíček nebo adaptéry.</span><span class="sxs-lookup"><span data-stu-id="c3505-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="c3505-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c3505-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="c3505-117">Spuštění middlewaru OWIN v potrubí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3505-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="c3505-118">ASP.NET owin podpora Core je nasazenjako `Microsoft.AspNetCore.Owin` součást balíčku.</span><span class="sxs-lookup"><span data-stu-id="c3505-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="c3505-119">Podporu OWIN můžete importovat do projektu instalací tohoto balíčku.</span><span class="sxs-lookup"><span data-stu-id="c3505-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="c3505-120">Middleware OWIN odpovídá [specifikaci OWIN](https://owin.org/spec/spec/owin-1.0.0.html) `Func<IDictionary<string, object>, Task>` , která vyžaduje rozhraní, a `owin.ResponseBody`nastaví se konkrétní klíče (například ).</span><span class="sxs-lookup"><span data-stu-id="c3505-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="c3505-121">Následující jednoduchý middleware OWIN zobrazuje "Hello World":</span><span class="sxs-lookup"><span data-stu-id="c3505-121">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="c3505-122">Ukázkový podpis `Task` vrátí a `IDictionary<string, object>` přijme podle požadavků OWIN.</span><span class="sxs-lookup"><span data-stu-id="c3505-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="c3505-123">Následující kód ukazuje, jak `OwinHello` přidat middleware (viz výše) do `UseOwin` kanálu ASP.NET Core s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="c3505-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="c3505-124">Můžete nakonfigurovat další akce, které se uskuteční v rámci kanálu OWIN.</span><span class="sxs-lookup"><span data-stu-id="c3505-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="c3505-125">Hlavičky odpovědí by měly být změněny pouze před prvním zápisem do datového proudu odpovědí.</span><span class="sxs-lookup"><span data-stu-id="c3505-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="c3505-126">Více volání `UseOwin` do se nedoporučuje z důvodů výkonu.</span><span class="sxs-lookup"><span data-stu-id="c3505-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="c3505-127">Komponenty OWIN budou fungovat nejlépe, pokud budou seskupeny.</span><span class="sxs-lookup"><span data-stu-id="c3505-127">OWIN components will operate best if grouped together.</span></span>

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="c3505-128">Použití ASP.NET Core Hosting na serveru založeném na OWIN</span><span class="sxs-lookup"><span data-stu-id="c3505-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="c3505-129">Servery založené na OWIN mohou hostovat ASP.NET aplikace Core.</span><span class="sxs-lookup"><span data-stu-id="c3505-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="c3505-130">Jeden takový server je [Nowin](https://github.com/Bobris/Nowin), .NET OWIN webový server.</span><span class="sxs-lookup"><span data-stu-id="c3505-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="c3505-131">V ukázce pro tento článek jsem zahrnul projekt, který odkazuje `IServer` na Nowin a používá jej k vytvoření schopnosti vlastního hostování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c3505-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="c3505-132">`IServer`je rozhraní, které `Features` vyžaduje `Start` vlastnost a metodu.</span><span class="sxs-lookup"><span data-stu-id="c3505-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="c3505-133">`Start`je zodpovědný za konfiguraci a spuštění serveru, což se v tomto případě provádí prostřednictvím řady fluent API volání, která nastavit adresy analyzované z IServerAddressesFeature.</span><span class="sxs-lookup"><span data-stu-id="c3505-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="c3505-134">Všimněte si, že `_builder` plynulá konfigurace proměnné určuje, `appFunc` že požadavky budou zpracovány definované dříve v metodě.</span><span class="sxs-lookup"><span data-stu-id="c3505-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="c3505-135">To `Func` se nazývá na každý požadavek na zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="c3505-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="c3505-136">Přidáme také rozšíření, `IWebHostBuilder` které vám usnadní přidání a konfiguraci serveru Nowin.</span><span class="sxs-lookup"><span data-stu-id="c3505-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

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

<span data-ttu-id="c3505-137">S tímto na místě, vyvolat rozšíření v *Program.cs* spustit aplikaci ASP.NET Core pomocí tohoto vlastního serveru:</span><span class="sxs-lookup"><span data-stu-id="c3505-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

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

<span data-ttu-id="c3505-138">Další informace o [ASP.NET základních serverech](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="c3505-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="c3505-139">Spuštění ASP.NET jádra na serveru založeném na OWIN a použití jeho podpory websockets</span><span class="sxs-lookup"><span data-stu-id="c3505-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="c3505-140">Dalším příkladem toho, jak funkce serverů založených na OWIN mohou být využívány ASP.NET Core je přístup k funkcím, jako je WebSockets.</span><span class="sxs-lookup"><span data-stu-id="c3505-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="c3505-141">Webový server .NET OWIN použitý v předchozím příkladu má vestavěnou podporu webových soketů, kterou může využít aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c3505-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="c3505-142">Následující příklad ukazuje jednoduchou webovou aplikaci, která podporuje webové sokety a vrací zpět vše odeslané na server prostřednictvím websockets.</span><span class="sxs-lookup"><span data-stu-id="c3505-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

<span data-ttu-id="c3505-143">Tato [ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) je `NowinServer` konfigurována pomocí stejného jako předchozí – jediný rozdíl `Configure` je v tom, jak je aplikace konfigurována ve své metodě.</span><span class="sxs-lookup"><span data-stu-id="c3505-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="c3505-144">Test pomocí [jednoduchého klienta websocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstruje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="c3505-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Testovací klient webového soketu](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="c3505-146">Prostředí OWIN</span><span class="sxs-lookup"><span data-stu-id="c3505-146">OWIN environment</span></span>

<span data-ttu-id="c3505-147">Prostředí OWIN můžete vytvořit `HttpContext`pomocí .</span><span class="sxs-lookup"><span data-stu-id="c3505-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="c3505-148">OWIN klíče</span><span class="sxs-lookup"><span data-stu-id="c3505-148">OWIN keys</span></span>

<span data-ttu-id="c3505-149">OWIN závisí `IDictionary<string,object>` na objektu pro komunikaci informací v rámci výměny požadavků a odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="c3505-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="c3505-150">ASP.NET Core implementuje klíče uvedené níže.</span><span class="sxs-lookup"><span data-stu-id="c3505-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="c3505-151">Podívejte se na [primární specifikace, rozšíření](https://owin.org/#spec)a [OWIN klíčové pokyny a společné klíče](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="c3505-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="c3505-152">Údaje o požadavku (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="c3505-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="c3505-153">Klíč</span><span class="sxs-lookup"><span data-stu-id="c3505-153">Key</span></span>               | <span data-ttu-id="c3505-154">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="c3505-154">Value (type)</span></span> | <span data-ttu-id="c3505-155">Popis</span><span class="sxs-lookup"><span data-stu-id="c3505-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c3505-156">owin. RequestScheme</span><span class="sxs-lookup"><span data-stu-id="c3505-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="c3505-157">owin. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="c3505-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="c3505-158">owin. RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="c3505-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="c3505-159">owin. Cesta aplikace RequestPath</span><span class="sxs-lookup"><span data-stu-id="c3505-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="c3505-160">owin. PožadavekNařetězec</span><span class="sxs-lookup"><span data-stu-id="c3505-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="c3505-161">owin. Protokol RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="c3505-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="c3505-162">owin. Hlavičky požadavků</span><span class="sxs-lookup"><span data-stu-id="c3505-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="c3505-163">owin. RequestBody</span><span class="sxs-lookup"><span data-stu-id="c3505-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="c3505-164">Údaje o požadavku (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="c3505-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="c3505-165">Klíč</span><span class="sxs-lookup"><span data-stu-id="c3505-165">Key</span></span>               | <span data-ttu-id="c3505-166">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="c3505-166">Value (type)</span></span> | <span data-ttu-id="c3505-167">Popis</span><span class="sxs-lookup"><span data-stu-id="c3505-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c3505-168">owin. Requestid</span><span class="sxs-lookup"><span data-stu-id="c3505-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="c3505-169">Nepovinné</span><span class="sxs-lookup"><span data-stu-id="c3505-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="c3505-170">Údaje o odezvě (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="c3505-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="c3505-171">Klíč</span><span class="sxs-lookup"><span data-stu-id="c3505-171">Key</span></span>               | <span data-ttu-id="c3505-172">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="c3505-172">Value (type)</span></span> | <span data-ttu-id="c3505-173">Popis</span><span class="sxs-lookup"><span data-stu-id="c3505-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c3505-174">owin. ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="c3505-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="c3505-175">Nepovinné</span><span class="sxs-lookup"><span data-stu-id="c3505-175">Optional</span></span> |
| <span data-ttu-id="c3505-176">owin. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="c3505-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="c3505-177">Nepovinné</span><span class="sxs-lookup"><span data-stu-id="c3505-177">Optional</span></span> |
| <span data-ttu-id="c3505-178">owin. Hlavičky odpovědí</span><span class="sxs-lookup"><span data-stu-id="c3505-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="c3505-179">owin. ResponseBody</span><span class="sxs-lookup"><span data-stu-id="c3505-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="c3505-180">Další údaje (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="c3505-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="c3505-181">Klíč</span><span class="sxs-lookup"><span data-stu-id="c3505-181">Key</span></span>               | <span data-ttu-id="c3505-182">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="c3505-182">Value (type)</span></span> | <span data-ttu-id="c3505-183">Popis</span><span class="sxs-lookup"><span data-stu-id="c3505-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c3505-184">owin. Volání zrušeno</span><span class="sxs-lookup"><span data-stu-id="c3505-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="c3505-185">owin. Verze</span><span class="sxs-lookup"><span data-stu-id="c3505-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="c3505-186">Běžné klávesy</span><span class="sxs-lookup"><span data-stu-id="c3505-186">Common keys</span></span>

| <span data-ttu-id="c3505-187">Klíč</span><span class="sxs-lookup"><span data-stu-id="c3505-187">Key</span></span>               | <span data-ttu-id="c3505-188">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="c3505-188">Value (type)</span></span> | <span data-ttu-id="c3505-189">Popis</span><span class="sxs-lookup"><span data-stu-id="c3505-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c3505-190">Ssl. Klientský certifikát</span><span class="sxs-lookup"><span data-stu-id="c3505-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="c3505-191">Ssl. LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="c3505-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="c3505-192">Server. Vzdálená ipadresa</span><span class="sxs-lookup"><span data-stu-id="c3505-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="c3505-193">Server. Vzdálený port</span><span class="sxs-lookup"><span data-stu-id="c3505-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="c3505-194">Server. Adresa LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="c3505-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="c3505-195">Server. LocalPort</span><span class="sxs-lookup"><span data-stu-id="c3505-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="c3505-196">Server. IsMístní</span><span class="sxs-lookup"><span data-stu-id="c3505-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="c3505-197">Server. OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="c3505-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="c3505-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="c3505-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="c3505-199">Klíč</span><span class="sxs-lookup"><span data-stu-id="c3505-199">Key</span></span>               | <span data-ttu-id="c3505-200">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="c3505-200">Value (type)</span></span> | <span data-ttu-id="c3505-201">Popis</span><span class="sxs-lookup"><span data-stu-id="c3505-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c3505-202">souboru odeslání. Sendasync</span><span class="sxs-lookup"><span data-stu-id="c3505-202">sendfile.SendAsync</span></span> | <span data-ttu-id="c3505-203">Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c3505-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="c3505-204">Na požadavek</span><span class="sxs-lookup"><span data-stu-id="c3505-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="c3505-205">Neprůhledné v0.3.0</span><span class="sxs-lookup"><span data-stu-id="c3505-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="c3505-206">Klíč</span><span class="sxs-lookup"><span data-stu-id="c3505-206">Key</span></span>               | <span data-ttu-id="c3505-207">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="c3505-207">Value (type)</span></span> | <span data-ttu-id="c3505-208">Popis</span><span class="sxs-lookup"><span data-stu-id="c3505-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c3505-209">Neprůhledné. Verze</span><span class="sxs-lookup"><span data-stu-id="c3505-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="c3505-210">Neprůhledné. Upgrade</span><span class="sxs-lookup"><span data-stu-id="c3505-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="c3505-211">Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c3505-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="c3505-212">Neprůhledné. Proudu</span><span class="sxs-lookup"><span data-stu-id="c3505-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="c3505-213">Neprůhledné. Volání zrušeno</span><span class="sxs-lookup"><span data-stu-id="c3505-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="c3505-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="c3505-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="c3505-215">Klíč</span><span class="sxs-lookup"><span data-stu-id="c3505-215">Key</span></span>               | <span data-ttu-id="c3505-216">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="c3505-216">Value (type)</span></span> | <span data-ttu-id="c3505-217">Popis</span><span class="sxs-lookup"><span data-stu-id="c3505-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="c3505-218">websocket. Verze</span><span class="sxs-lookup"><span data-stu-id="c3505-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="c3505-219">websocket. Přijmout</span><span class="sxs-lookup"><span data-stu-id="c3505-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="c3505-220">Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c3505-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="c3505-221">websocket. Přijmoutalt</span><span class="sxs-lookup"><span data-stu-id="c3505-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="c3505-222">Nespecifikace</span><span class="sxs-lookup"><span data-stu-id="c3505-222">Non-spec</span></span> |
| <span data-ttu-id="c3505-223">websocket. Podprotokol</span><span class="sxs-lookup"><span data-stu-id="c3505-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="c3505-224">Viz [RFC6455 Oddíl 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Krok 5.5</span><span class="sxs-lookup"><span data-stu-id="c3505-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="c3505-225">websocket. Sendasync</span><span class="sxs-lookup"><span data-stu-id="c3505-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="c3505-226">Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c3505-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="c3505-227">websocket. ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="c3505-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="c3505-228">Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c3505-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="c3505-229">websocket. CloseAsync</span><span class="sxs-lookup"><span data-stu-id="c3505-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="c3505-230">Viz [podpis delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="c3505-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="c3505-231">websocket. Volání zrušeno</span><span class="sxs-lookup"><span data-stu-id="c3505-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="c3505-232">websocket. Stav klienta CloseStatus</span><span class="sxs-lookup"><span data-stu-id="c3505-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="c3505-233">Nepovinné</span><span class="sxs-lookup"><span data-stu-id="c3505-233">Optional</span></span> |
| <span data-ttu-id="c3505-234">websocket. Popis klienta</span><span class="sxs-lookup"><span data-stu-id="c3505-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="c3505-235">Nepovinné</span><span class="sxs-lookup"><span data-stu-id="c3505-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="c3505-236">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c3505-236">Additional resources</span></span>

* [<span data-ttu-id="c3505-237">Middleware</span><span class="sxs-lookup"><span data-stu-id="c3505-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="c3505-238">Servery</span><span class="sxs-lookup"><span data-stu-id="c3505-238">Servers</span></span>](xref:fundamentals/servers/index)

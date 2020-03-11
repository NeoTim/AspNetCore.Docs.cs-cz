---
title: Otevřete web interface for .NET (OWIN) s ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core podporuje rozhraní Open Web Interface for .NET (OWIN), které umožňuje oddělit webové aplikace od webových serverů.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
uid: fundamentals/owin
ms.openlocfilehash: 14b23ba6d284413e20417bbd4142e19a656350ac
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666682"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="d0b07-103">Otevřete web interface for .NET (OWIN) s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0b07-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="d0b07-104">[Steve Smith](https://ardalis.com/) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d0b07-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d0b07-105">ASP.NET Core podporuje otevřené webové rozhraní pro .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="d0b07-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="d0b07-106">OWIN umožňuje oddělit webové aplikace od webových serverů.</span><span class="sxs-lookup"><span data-stu-id="d0b07-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="d0b07-107">Definuje standardní způsob, jakým se má middleware použít v kanálu ke zpracování požadavků a přidružených odpovědí.</span><span class="sxs-lookup"><span data-stu-id="d0b07-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="d0b07-108">ASP.NET Core aplikace a middleware se můžou vzájemně spolupracovat s aplikacemi, servery a middlewarem založeným na OWIN.</span><span class="sxs-lookup"><span data-stu-id="d0b07-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="d0b07-109">OWIN poskytuje rozvětvenou vrstvu, která umožňuje používat dvě architektury s různorodými objektovými modely.</span><span class="sxs-lookup"><span data-stu-id="d0b07-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="d0b07-110">Balíček `Microsoft.AspNetCore.Owin` poskytuje dva implementace adaptérů:</span><span class="sxs-lookup"><span data-stu-id="d0b07-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="d0b07-111">ASP.NET Core OWIN</span><span class="sxs-lookup"><span data-stu-id="d0b07-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="d0b07-112">OWIN na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0b07-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="d0b07-113">To umožňuje, aby se ASP.NET Core hostovat na serveru nebo hostiteli kompatibilním s OWIN nebo pro jiné součásti kompatibilní s OWIN, které se mají spustit nad ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0b07-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="d0b07-114">Používání těchto adaptérů se dodává s náklady na výkon.</span><span class="sxs-lookup"><span data-stu-id="d0b07-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="d0b07-115">Aplikace, které používají jenom ASP.NET Core komponenty, by neměly používat `Microsoft.AspNetCore.Owin` balíčky ani adaptéry.</span><span class="sxs-lookup"><span data-stu-id="d0b07-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="d0b07-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d0b07-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="d0b07-117">Spuštění middlewaru OWIN v kanálu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d0b07-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="d0b07-118">Podpora OWIN ASP.NET Core je nasazena jako součást balíčku `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="d0b07-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="d0b07-119">Podporu OWIN můžete do svého projektu importovat instalací tohoto balíčku.</span><span class="sxs-lookup"><span data-stu-id="d0b07-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="d0b07-120">Middleware OWIN je v souladu se [specifikací Owin](https://owin.org/spec/spec/owin-1.0.0.html), která vyžaduje rozhraní `Func<IDictionary<string, object>, Task>` a nastavené konkrétní klíče (například `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="d0b07-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="d0b07-121">Následující jednoduchý middleware OWIN zobrazí "Hello World":</span><span class="sxs-lookup"><span data-stu-id="d0b07-121">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="d0b07-122">Vzorový podpis vrátí `Task` a přijímá `IDictionary<string, object>` podle požadavků OWIN.</span><span class="sxs-lookup"><span data-stu-id="d0b07-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="d0b07-123">Následující kód ukazuje, jak přidat `OwinHello` middleware (viz výše) do kanálu ASP.NET Core pomocí metody rozšíření `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="d0b07-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="d0b07-124">Můžete nakonfigurovat další akce, které budou provedeny v rámci kanálu OWIN.</span><span class="sxs-lookup"><span data-stu-id="d0b07-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="d0b07-125">Hlavičky odpovědi by se měly upravovat jenom před prvním zápisem do datového proudu odpovědí.</span><span class="sxs-lookup"><span data-stu-id="d0b07-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="d0b07-126">Z důvodů výkonu se nedoporučuje více volání `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="d0b07-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="d0b07-127">Komponenty OWIN budou fungovat nejlépe, pokud jsou seskupeny dohromady.</span><span class="sxs-lookup"><span data-stu-id="d0b07-127">OWIN components will operate best if grouped together.</span></span>

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="d0b07-128">Použití ASP.NET Core hostování na serveru založeném na OWIN</span><span class="sxs-lookup"><span data-stu-id="d0b07-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="d0b07-129">Servery založené na OWIN můžou hostovat aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0b07-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="d0b07-130">Jeden takový server je [Nowin](https://github.com/Bobris/Nowin), webový server .NET Owin.</span><span class="sxs-lookup"><span data-stu-id="d0b07-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="d0b07-131">V ukázce tohoto článku jsem zahrnul projekt, který odkazuje na Nowin a používá ho k vytvoření `IServer` schopného ASP.NET Coreho hostování.</span><span class="sxs-lookup"><span data-stu-id="d0b07-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="d0b07-132">`IServer` je rozhraní, které vyžaduje vlastnost `Features` a metodu `Start`.</span><span class="sxs-lookup"><span data-stu-id="d0b07-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="d0b07-133">`Start` zodpovídá za konfiguraci a spuštění serveru, které v tomto případě provádí řada volání rozhraní Fluent API, která nastavují adresy přeložené z IServerAddressesFeature.</span><span class="sxs-lookup"><span data-stu-id="d0b07-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="d0b07-134">Všimněte si, že konfigurace Fluent proměnné `_builder` určuje, že žádosti budou zpracovány `appFunc` definovaným dříve v metodě.</span><span class="sxs-lookup"><span data-stu-id="d0b07-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="d0b07-135">Tato `Func` se volá u každé žádosti o zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="d0b07-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="d0b07-136">Přidáme také rozšíření `IWebHostBuilder`, které usnadňuje přidání a konfiguraci serveru Nowin.</span><span class="sxs-lookup"><span data-stu-id="d0b07-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

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

<span data-ttu-id="d0b07-137">Když to zavoláte, vyvolejte rozšíření v *program.cs* a spusťte aplikaci ASP.NET Core s použitím tohoto vlastního serveru:</span><span class="sxs-lookup"><span data-stu-id="d0b07-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

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

<span data-ttu-id="d0b07-138">Přečtěte si další informace o [ASP.NET Corech serverech](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="d0b07-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="d0b07-139">Spusťte ASP.NET Core na serveru založeném na OWIN a využijte podporu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0b07-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="d0b07-140">Dalším příkladem použití ASP.NET Core funkcí serverů založených na OWIN je přístup k funkcím, jako jsou WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0b07-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="d0b07-141">Webový server .NET OWIN použitý v předchozím příkladu obsahuje podporu pro integrované webové sokety, které může využívat ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="d0b07-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="d0b07-142">Níže uvedený příklad ukazuje jednoduchou webovou aplikaci, která podporuje webové sokety, a vrátí zpátky vše odeslané na server prostřednictvím objektů WebSockets.</span><span class="sxs-lookup"><span data-stu-id="d0b07-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

<span data-ttu-id="d0b07-143">Tato [Ukázka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) je nakonfigurovaná pomocí stejné `NowinServer` jako v předchozím příkladu. jediným rozdílem je to, jak je aplikace nakonfigurovaná ve své `Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="d0b07-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="d0b07-144">Test použití [jednoduchého klienta protokolu WebSocket](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) ukazuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="d0b07-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Testovací klient webového soketu](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="d0b07-146">Prostředí OWIN</span><span class="sxs-lookup"><span data-stu-id="d0b07-146">OWIN environment</span></span>

<span data-ttu-id="d0b07-147">Prostředí OWIN můžete vytvořit pomocí `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="d0b07-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="d0b07-148">OWIN klíče</span><span class="sxs-lookup"><span data-stu-id="d0b07-148">OWIN keys</span></span>

<span data-ttu-id="d0b07-149">OWIN závisí na objektu `IDictionary<string,object>` ke sdělování informací v rámci výměny požadavků a odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0b07-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="d0b07-150">ASP.NET Core implementuje níže uvedené klíče.</span><span class="sxs-lookup"><span data-stu-id="d0b07-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="d0b07-151">Prohlédněte si [základní pravidla specifikace, rozšíření](https://owin.org/#spec)a [Owin Key a běžné klíče](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="d0b07-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="d0b07-152">Data požadavku (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d0b07-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d0b07-153">Klíč</span><span class="sxs-lookup"><span data-stu-id="d0b07-153">Key</span></span>               | <span data-ttu-id="d0b07-154">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d0b07-154">Value (type)</span></span> | <span data-ttu-id="d0b07-155">Popis</span><span class="sxs-lookup"><span data-stu-id="d0b07-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0b07-156">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="d0b07-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="d0b07-157">Owin. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="d0b07-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="d0b07-158">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="d0b07-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="d0b07-159">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="d0b07-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="d0b07-160">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="d0b07-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="d0b07-161">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="d0b07-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="d0b07-162">Owin. RequestHeaders hostitele</span><span class="sxs-lookup"><span data-stu-id="d0b07-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="d0b07-163">Owin. Částmi</span><span class="sxs-lookup"><span data-stu-id="d0b07-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="d0b07-164">Data požadavku (OWIN v 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="d0b07-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="d0b07-165">Klíč</span><span class="sxs-lookup"><span data-stu-id="d0b07-165">Key</span></span>               | <span data-ttu-id="d0b07-166">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d0b07-166">Value (type)</span></span> | <span data-ttu-id="d0b07-167">Popis</span><span class="sxs-lookup"><span data-stu-id="d0b07-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0b07-168">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="d0b07-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="d0b07-169">Nepovinné</span><span class="sxs-lookup"><span data-stu-id="d0b07-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="d0b07-170">Data odpovědi (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d0b07-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d0b07-171">Klíč</span><span class="sxs-lookup"><span data-stu-id="d0b07-171">Key</span></span>               | <span data-ttu-id="d0b07-172">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d0b07-172">Value (type)</span></span> | <span data-ttu-id="d0b07-173">Popis</span><span class="sxs-lookup"><span data-stu-id="d0b07-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0b07-174">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="d0b07-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="d0b07-175">Nepovinné</span><span class="sxs-lookup"><span data-stu-id="d0b07-175">Optional</span></span> |
| <span data-ttu-id="d0b07-176">Owin. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="d0b07-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="d0b07-177">Nepovinné</span><span class="sxs-lookup"><span data-stu-id="d0b07-177">Optional</span></span> |
| <span data-ttu-id="d0b07-178">Owin. ResponseHeaders hostitele</span><span class="sxs-lookup"><span data-stu-id="d0b07-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="d0b07-179">Owin. ResponseBody</span><span class="sxs-lookup"><span data-stu-id="d0b07-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="d0b07-180">Jiná data (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="d0b07-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="d0b07-181">Klíč</span><span class="sxs-lookup"><span data-stu-id="d0b07-181">Key</span></span>               | <span data-ttu-id="d0b07-182">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d0b07-182">Value (type)</span></span> | <span data-ttu-id="d0b07-183">Popis</span><span class="sxs-lookup"><span data-stu-id="d0b07-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0b07-184">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d0b07-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="d0b07-185">Owin. Znění</span><span class="sxs-lookup"><span data-stu-id="d0b07-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="d0b07-186">Společné klíče</span><span class="sxs-lookup"><span data-stu-id="d0b07-186">Common keys</span></span>

| <span data-ttu-id="d0b07-187">Klíč</span><span class="sxs-lookup"><span data-stu-id="d0b07-187">Key</span></span>               | <span data-ttu-id="d0b07-188">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d0b07-188">Value (type)</span></span> | <span data-ttu-id="d0b07-189">Popis</span><span class="sxs-lookup"><span data-stu-id="d0b07-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0b07-190">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="d0b07-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="d0b07-191">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="d0b07-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="d0b07-192">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="d0b07-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="d0b07-193">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="d0b07-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="d0b07-194">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="d0b07-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="d0b07-195">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="d0b07-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="d0b07-196">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="d0b07-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="d0b07-197">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="d0b07-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="d0b07-198">SendFiles v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="d0b07-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="d0b07-199">Klíč</span><span class="sxs-lookup"><span data-stu-id="d0b07-199">Key</span></span>               | <span data-ttu-id="d0b07-200">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d0b07-200">Value (type)</span></span> | <span data-ttu-id="d0b07-201">Popis</span><span class="sxs-lookup"><span data-stu-id="d0b07-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0b07-202">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="d0b07-202">sendfile.SendAsync</span></span> | <span data-ttu-id="d0b07-203">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0b07-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="d0b07-204">Za požadavek</span><span class="sxs-lookup"><span data-stu-id="d0b07-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="d0b07-205">Neprůhledný v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="d0b07-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="d0b07-206">Klíč</span><span class="sxs-lookup"><span data-stu-id="d0b07-206">Key</span></span>               | <span data-ttu-id="d0b07-207">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d0b07-207">Value (type)</span></span> | <span data-ttu-id="d0b07-208">Popis</span><span class="sxs-lookup"><span data-stu-id="d0b07-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0b07-209">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="d0b07-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="d0b07-210">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="d0b07-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="d0b07-211">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0b07-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="d0b07-212">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="d0b07-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="d0b07-213">krytím. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d0b07-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="d0b07-214">WebSocket v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="d0b07-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="d0b07-215">Klíč</span><span class="sxs-lookup"><span data-stu-id="d0b07-215">Key</span></span>               | <span data-ttu-id="d0b07-216">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="d0b07-216">Value (type)</span></span> | <span data-ttu-id="d0b07-217">Popis</span><span class="sxs-lookup"><span data-stu-id="d0b07-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="d0b07-218">protokolu WebSocket. Znění</span><span class="sxs-lookup"><span data-stu-id="d0b07-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="d0b07-219">protokolu WebSocket. Vyjádřit</span><span class="sxs-lookup"><span data-stu-id="d0b07-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="d0b07-220">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0b07-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="d0b07-221">protokolu WebSocket. AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="d0b07-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="d0b07-222">Bez specifikace</span><span class="sxs-lookup"><span data-stu-id="d0b07-222">Non-spec</span></span> |
| <span data-ttu-id="d0b07-223">protokolu WebSocket. Dílčí protokol</span><span class="sxs-lookup"><span data-stu-id="d0b07-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="d0b07-224">Viz [část RFC6455 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) krok 5,5</span><span class="sxs-lookup"><span data-stu-id="d0b07-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="d0b07-225">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="d0b07-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="d0b07-226">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0b07-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d0b07-227">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="d0b07-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="d0b07-228">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0b07-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d0b07-229">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="d0b07-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="d0b07-230">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="d0b07-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="d0b07-231">protokolu WebSocket. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="d0b07-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="d0b07-232">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="d0b07-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="d0b07-233">Nepovinné</span><span class="sxs-lookup"><span data-stu-id="d0b07-233">Optional</span></span> |
| <span data-ttu-id="d0b07-234">protokolu WebSocket. ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="d0b07-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="d0b07-235">Nepovinné</span><span class="sxs-lookup"><span data-stu-id="d0b07-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="d0b07-236">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d0b07-236">Additional resources</span></span>

* [<span data-ttu-id="d0b07-237">Middleware</span><span class="sxs-lookup"><span data-stu-id="d0b07-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="d0b07-238">Servery</span><span class="sxs-lookup"><span data-stu-id="d0b07-238">Servers</span></span>](xref:fundamentals/servers/index)

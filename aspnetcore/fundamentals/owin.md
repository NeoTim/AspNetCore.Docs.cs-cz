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
ms.openlocfilehash: 817eb652f4feedf19dd60873b480917c320272a3
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102754"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="f39cc-103">Otevřete web interface for .NET (OWIN) s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f39cc-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="f39cc-104">[Steve Smith](https://ardalis.com/) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f39cc-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f39cc-105">ASP.NET Core podporuje otevřené webové rozhraní pro .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="f39cc-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="f39cc-106">OWIN umožňuje oddělit webové aplikace od webových serverů.</span><span class="sxs-lookup"><span data-stu-id="f39cc-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="f39cc-107">Definuje standardní způsob, jakým se má middleware použít v kanálu ke zpracování požadavků a přidružených odpovědí.</span><span class="sxs-lookup"><span data-stu-id="f39cc-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="f39cc-108">ASP.NET Core aplikace a middleware se můžou vzájemně spolupracovat s aplikacemi, servery a middlewarem založeným na OWIN.</span><span class="sxs-lookup"><span data-stu-id="f39cc-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="f39cc-109">OWIN poskytuje rozvětvenou vrstvu, která umožňuje používat dvě architektury s různorodými objektovými modely.</span><span class="sxs-lookup"><span data-stu-id="f39cc-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="f39cc-110">`Microsoft.AspNetCore.Owin`Balíček poskytuje dva implementace adaptérů:</span><span class="sxs-lookup"><span data-stu-id="f39cc-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="f39cc-111">ASP.NET Core OWIN</span><span class="sxs-lookup"><span data-stu-id="f39cc-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="f39cc-112">OWIN na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f39cc-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="f39cc-113">To umožňuje, aby se ASP.NET Core hostovat na serveru nebo hostiteli kompatibilním s OWIN nebo pro jiné součásti kompatibilní s OWIN, které se mají spustit nad ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f39cc-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="f39cc-114">Používání těchto adaptérů se dodává s náklady na výkon.</span><span class="sxs-lookup"><span data-stu-id="f39cc-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="f39cc-115">Aplikace, které používají jenom ASP.NET Core komponenty, by neměly používat `Microsoft.AspNetCore.Owin` balíčky ani adaptéry.</span><span class="sxs-lookup"><span data-stu-id="f39cc-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="f39cc-116">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f39cc-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="f39cc-117">Spuštění middlewaru OWIN v kanálu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f39cc-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="f39cc-118">Podpora OWIN ASP.NET Core je nasazena jako součást `Microsoft.AspNetCore.Owin` balíčku.</span><span class="sxs-lookup"><span data-stu-id="f39cc-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="f39cc-119">Podporu OWIN můžete do svého projektu importovat instalací tohoto balíčku.</span><span class="sxs-lookup"><span data-stu-id="f39cc-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="f39cc-120">Middleware OWIN je v souladu se [specifikací Owin](https://owin.org/spec/spec/owin-1.0.0.html), která vyžaduje `Func<IDictionary<string, object>, Task>` rozhraní a nastavené konkrétní klíče (například `owin.ResponseBody` ).</span><span class="sxs-lookup"><span data-stu-id="f39cc-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="f39cc-121">Následující jednoduchý middleware OWIN zobrazí "Hello World":</span><span class="sxs-lookup"><span data-stu-id="f39cc-121">The following simple OWIN middleware displays "Hello World":</span></span>

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

<span data-ttu-id="f39cc-122">Vzorový podpis vrátí `Task` a přijme, `IDictionary<string, object>` jak vyžaduje Owin.</span><span class="sxs-lookup"><span data-stu-id="f39cc-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="f39cc-123">Následující kód ukazuje, jak přidat `OwinHello` middleware (viz výše) do kanálu ASP.NET Core s `UseOwin` metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f39cc-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="f39cc-124">Můžete nakonfigurovat další akce, které budou provedeny v rámci kanálu OWIN.</span><span class="sxs-lookup"><span data-stu-id="f39cc-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="f39cc-125">Hlavičky odpovědi by se měly upravovat jenom před prvním zápisem do datového proudu odpovědí.</span><span class="sxs-lookup"><span data-stu-id="f39cc-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="f39cc-126">`UseOwin`Z důvodů výkonu není doporučeno více volání.</span><span class="sxs-lookup"><span data-stu-id="f39cc-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="f39cc-127">Komponenty OWIN budou fungovat nejlépe, pokud jsou seskupeny dohromady.</span><span class="sxs-lookup"><span data-stu-id="f39cc-127">OWIN components will operate best if grouped together.</span></span>

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

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="f39cc-128">Spusťte ASP.NET Core na serveru založeném na OWIN a využijte podporu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f39cc-128">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="f39cc-129">Dalším příkladem použití ASP.NET Core funkcí serverů založených na OWIN je přístup k funkcím, jako jsou WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f39cc-129">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="f39cc-130">Webový server .NET OWIN použitý v předchozím příkladu obsahuje podporu pro integrované webové sokety, které může využívat ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="f39cc-130">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="f39cc-131">Níže uvedený příklad ukazuje jednoduchou webovou aplikaci, která podporuje webové sokety, a vrátí zpátky vše odeslané na server prostřednictvím objektů WebSockets.</span><span class="sxs-lookup"><span data-stu-id="f39cc-131">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

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

## <a name="owin-environment"></a><span data-ttu-id="f39cc-132">Prostředí OWIN</span><span class="sxs-lookup"><span data-stu-id="f39cc-132">OWIN environment</span></span>

<span data-ttu-id="f39cc-133">Prostředí OWIN můžete vytvořit pomocí `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="f39cc-133">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="f39cc-134">OWIN klíče</span><span class="sxs-lookup"><span data-stu-id="f39cc-134">OWIN keys</span></span>

<span data-ttu-id="f39cc-135">OWIN závisí na `IDictionary<string,object>` objektu, který komunikuje informace v rámci výměny požadavků a odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="f39cc-135">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="f39cc-136">ASP.NET Core implementuje níže uvedené klíče.</span><span class="sxs-lookup"><span data-stu-id="f39cc-136">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="f39cc-137">Prohlédněte si [základní pravidla specifikace, rozšíření](https://owin.org/#spec)a [Owin Key a běžné klíče](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="f39cc-137">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="f39cc-138">Data požadavku (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="f39cc-138">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="f39cc-139">Klíč</span><span class="sxs-lookup"><span data-stu-id="f39cc-139">Key</span></span>               | <span data-ttu-id="f39cc-140">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f39cc-140">Value (type)</span></span> | <span data-ttu-id="f39cc-141">Popis</span><span class="sxs-lookup"><span data-stu-id="f39cc-141">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f39cc-142">Owin. RequestScheme</span><span class="sxs-lookup"><span data-stu-id="f39cc-142">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="f39cc-143">Owin. RequestMethod</span><span class="sxs-lookup"><span data-stu-id="f39cc-143">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="f39cc-144">Owin. RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="f39cc-144">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="f39cc-145">Owin. RequestPath</span><span class="sxs-lookup"><span data-stu-id="f39cc-145">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="f39cc-146">Owin. RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="f39cc-146">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="f39cc-147">Owin. RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="f39cc-147">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="f39cc-148">Owin. RequestHeaders hostitele</span><span class="sxs-lookup"><span data-stu-id="f39cc-148">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="f39cc-149">Owin. Částmi</span><span class="sxs-lookup"><span data-stu-id="f39cc-149">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="f39cc-150">Data požadavku (OWIN v 1.1.0)</span><span class="sxs-lookup"><span data-stu-id="f39cc-150">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="f39cc-151">Klíč</span><span class="sxs-lookup"><span data-stu-id="f39cc-151">Key</span></span>               | <span data-ttu-id="f39cc-152">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f39cc-152">Value (type)</span></span> | <span data-ttu-id="f39cc-153">Popis</span><span class="sxs-lookup"><span data-stu-id="f39cc-153">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f39cc-154">Owin. Identifikátor</span><span class="sxs-lookup"><span data-stu-id="f39cc-154">owin.RequestId</span></span> | `String` | <span data-ttu-id="f39cc-155">Volitelné</span><span class="sxs-lookup"><span data-stu-id="f39cc-155">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="f39cc-156">Data odpovědi (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="f39cc-156">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="f39cc-157">Klíč</span><span class="sxs-lookup"><span data-stu-id="f39cc-157">Key</span></span>               | <span data-ttu-id="f39cc-158">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f39cc-158">Value (type)</span></span> | <span data-ttu-id="f39cc-159">Popis</span><span class="sxs-lookup"><span data-stu-id="f39cc-159">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f39cc-160">Owin. ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="f39cc-160">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="f39cc-161">Volitelné</span><span class="sxs-lookup"><span data-stu-id="f39cc-161">Optional</span></span> |
| <span data-ttu-id="f39cc-162">Owin. ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="f39cc-162">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="f39cc-163">Volitelné</span><span class="sxs-lookup"><span data-stu-id="f39cc-163">Optional</span></span> |
| <span data-ttu-id="f39cc-164">Owin. ResponseHeaders hostitele</span><span class="sxs-lookup"><span data-stu-id="f39cc-164">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="f39cc-165">Owin. ResponseBody</span><span class="sxs-lookup"><span data-stu-id="f39cc-165">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="f39cc-166">Jiná data (OWIN v 1.0.0)</span><span class="sxs-lookup"><span data-stu-id="f39cc-166">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="f39cc-167">Klíč</span><span class="sxs-lookup"><span data-stu-id="f39cc-167">Key</span></span>               | <span data-ttu-id="f39cc-168">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f39cc-168">Value (type)</span></span> | <span data-ttu-id="f39cc-169">Popis</span><span class="sxs-lookup"><span data-stu-id="f39cc-169">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f39cc-170">Owin. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="f39cc-170">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="f39cc-171">Owin. Znění</span><span class="sxs-lookup"><span data-stu-id="f39cc-171">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="f39cc-172">Společné klíče</span><span class="sxs-lookup"><span data-stu-id="f39cc-172">Common keys</span></span>

| <span data-ttu-id="f39cc-173">Klíč</span><span class="sxs-lookup"><span data-stu-id="f39cc-173">Key</span></span>               | <span data-ttu-id="f39cc-174">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f39cc-174">Value (type)</span></span> | <span data-ttu-id="f39cc-175">Popis</span><span class="sxs-lookup"><span data-stu-id="f39cc-175">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f39cc-176">zabezpečení. ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="f39cc-176">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="f39cc-177">zabezpečení. LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="f39cc-177">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="f39cc-178">WebServer. RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="f39cc-178">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="f39cc-179">WebServer. RemotePort</span><span class="sxs-lookup"><span data-stu-id="f39cc-179">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="f39cc-180">WebServer. LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="f39cc-180">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="f39cc-181">WebServer. LocalPort</span><span class="sxs-lookup"><span data-stu-id="f39cc-181">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="f39cc-182">WebServer. Místní</span><span class="sxs-lookup"><span data-stu-id="f39cc-182">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="f39cc-183">WebServer. OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="f39cc-183">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="f39cc-184">SendFiles v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="f39cc-184">SendFiles v0.3.0</span></span>

| <span data-ttu-id="f39cc-185">Klíč</span><span class="sxs-lookup"><span data-stu-id="f39cc-185">Key</span></span>               | <span data-ttu-id="f39cc-186">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f39cc-186">Value (type)</span></span> | <span data-ttu-id="f39cc-187">Popis</span><span class="sxs-lookup"><span data-stu-id="f39cc-187">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f39cc-188">sendfile. SendAsync</span><span class="sxs-lookup"><span data-stu-id="f39cc-188">sendfile.SendAsync</span></span> | <span data-ttu-id="f39cc-189">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f39cc-189">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="f39cc-190">Za požadavek</span><span class="sxs-lookup"><span data-stu-id="f39cc-190">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="f39cc-191">Neprůhledný v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="f39cc-191">Opaque v0.3.0</span></span>

| <span data-ttu-id="f39cc-192">Klíč</span><span class="sxs-lookup"><span data-stu-id="f39cc-192">Key</span></span>               | <span data-ttu-id="f39cc-193">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f39cc-193">Value (type)</span></span> | <span data-ttu-id="f39cc-194">Popis</span><span class="sxs-lookup"><span data-stu-id="f39cc-194">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f39cc-195">krytím. Znění</span><span class="sxs-lookup"><span data-stu-id="f39cc-195">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="f39cc-196">krytím. Přejít</span><span class="sxs-lookup"><span data-stu-id="f39cc-196">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="f39cc-197">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f39cc-197">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="f39cc-198">krytím. Stream</span><span class="sxs-lookup"><span data-stu-id="f39cc-198">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="f39cc-199">krytím. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="f39cc-199">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="f39cc-200">WebSocket v 0.3.0</span><span class="sxs-lookup"><span data-stu-id="f39cc-200">WebSocket v0.3.0</span></span>

| <span data-ttu-id="f39cc-201">Klíč</span><span class="sxs-lookup"><span data-stu-id="f39cc-201">Key</span></span>               | <span data-ttu-id="f39cc-202">Hodnota (typ)</span><span class="sxs-lookup"><span data-stu-id="f39cc-202">Value (type)</span></span> | <span data-ttu-id="f39cc-203">Popis</span><span class="sxs-lookup"><span data-stu-id="f39cc-203">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="f39cc-204">protokolu WebSocket. Znění</span><span class="sxs-lookup"><span data-stu-id="f39cc-204">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="f39cc-205">protokolu WebSocket. Vyjádřit</span><span class="sxs-lookup"><span data-stu-id="f39cc-205">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="f39cc-206">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f39cc-206">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="f39cc-207">protokolu WebSocket. AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="f39cc-207">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="f39cc-208">Bez specifikace</span><span class="sxs-lookup"><span data-stu-id="f39cc-208">Non-spec</span></span> |
| <span data-ttu-id="f39cc-209">protokolu WebSocket. Dílčí protokol</span><span class="sxs-lookup"><span data-stu-id="f39cc-209">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="f39cc-210">Viz [část RFC6455 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) krok 5,5</span><span class="sxs-lookup"><span data-stu-id="f39cc-210">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="f39cc-211">protokolu WebSocket. SendAsync</span><span class="sxs-lookup"><span data-stu-id="f39cc-211">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="f39cc-212">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f39cc-212">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="f39cc-213">protokolu WebSocket. Metody ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="f39cc-213">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="f39cc-214">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f39cc-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="f39cc-215">protokolu WebSocket. CloseAsync</span><span class="sxs-lookup"><span data-stu-id="f39cc-215">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="f39cc-216">Viz [Signatura delegáta](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="f39cc-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="f39cc-217">protokolu WebSocket. CallCancelled</span><span class="sxs-lookup"><span data-stu-id="f39cc-217">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="f39cc-218">protokolu WebSocket. ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="f39cc-218">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="f39cc-219">Volitelné</span><span class="sxs-lookup"><span data-stu-id="f39cc-219">Optional</span></span> |
| <span data-ttu-id="f39cc-220">protokolu WebSocket. ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="f39cc-220">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="f39cc-221">Volitelné</span><span class="sxs-lookup"><span data-stu-id="f39cc-221">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="f39cc-222">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f39cc-222">Additional resources</span></span>

* [<span data-ttu-id="f39cc-223">Middleware</span><span class="sxs-lookup"><span data-stu-id="f39cc-223">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="f39cc-224">Servery</span><span class="sxs-lookup"><span data-stu-id="f39cc-224">Servers</span></span>](xref:fundamentals/servers/index)

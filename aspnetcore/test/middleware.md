---
title: Test ASP.NET Core middlewaru
author: tratcher
description: Naučte se testovat ASP.NET Core middlewaru pomocí TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: f4ed16b136da37c093a72a8866301a188a8518a2
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406482"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="515a3-103">Test ASP.NET Core middlewaru</span><span class="sxs-lookup"><span data-stu-id="515a3-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="515a3-104">Autor – [Chris Rossův](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="515a3-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="515a3-105">Middleware lze testovat v izolaci pomocí <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="515a3-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="515a3-106">Umožňuje:</span><span class="sxs-lookup"><span data-stu-id="515a3-106">It allows you to:</span></span>

* <span data-ttu-id="515a3-107">Vytvořte instanci kanálu aplikace obsahující pouze součásti, které je třeba otestovat.</span><span class="sxs-lookup"><span data-stu-id="515a3-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="515a3-108">Odešlete vlastní žádosti o ověření chování middlewaru.</span><span class="sxs-lookup"><span data-stu-id="515a3-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="515a3-109">Výhody:</span><span class="sxs-lookup"><span data-stu-id="515a3-109">Advantages:</span></span>

* <span data-ttu-id="515a3-110">Požadavky se odesílají v paměti a neprovádí se jejich serializace přes síť.</span><span class="sxs-lookup"><span data-stu-id="515a3-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="515a3-111">Tím se vyhnete dalším obavám, jako je Správa portů a certifikáty HTTPS.</span><span class="sxs-lookup"><span data-stu-id="515a3-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="515a3-112">Výjimky v middlewaru můžou přesměrovat přímo zpět na volající test.</span><span class="sxs-lookup"><span data-stu-id="515a3-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="515a3-113">Je možné přizpůsobit datové struktury serveru, například <xref:Microsoft.AspNetCore.Http.HttpContext> přímo v testu.</span><span class="sxs-lookup"><span data-stu-id="515a3-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="515a3-114">Nastavení TestServer</span><span class="sxs-lookup"><span data-stu-id="515a3-114">Set up the TestServer</span></span>

<span data-ttu-id="515a3-115">V testovacím projektu vytvořte test:</span><span class="sxs-lookup"><span data-stu-id="515a3-115">In the test project, create a test:</span></span>

* <span data-ttu-id="515a3-116">Sestavte a spusťte hostitele, který používá <xref:Microsoft.AspNetCore.TestHost.TestServer> .</span><span class="sxs-lookup"><span data-stu-id="515a3-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="515a3-117">Přidejte všechny požadované služby, které middleware používá.</span><span class="sxs-lookup"><span data-stu-id="515a3-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="515a3-118">Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) :</span><span class="sxs-lookup"><span data-stu-id="515a3-118">Add the [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet package to the project:</span></span>
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* <span data-ttu-id="515a3-119">Nakonfigurujte kanál zpracování, aby používal middleware pro test.</span><span class="sxs-lookup"><span data-stu-id="515a3-119">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="515a3-120">Odesílání žádostí pomocí HttpClient</span><span class="sxs-lookup"><span data-stu-id="515a3-120">Send requests with HttpClient</span></span>
<span data-ttu-id="515a3-121">Odeslat žádost pomocí <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="515a3-121">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="515a3-122">Vyhodnotit výsledek.</span><span class="sxs-lookup"><span data-stu-id="515a3-122">Assert the result.</span></span> <span data-ttu-id="515a3-123">Nejprve udělejte kontrolní výraz opakem očekávaného výsledku.</span><span class="sxs-lookup"><span data-stu-id="515a3-123">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="515a3-124">Počáteční spuštění s falešně pozitivním kontrolním výrazem potvrdí, že test neproběhne úspěšně v případě, že middleware probíhá správně.</span><span class="sxs-lookup"><span data-stu-id="515a3-124">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="515a3-125">Spusťte test a potvrďte, že test se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="515a3-125">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="515a3-126">V následujícím příkladu by middleware měl vrátit stavový kód 404 (*Nenalezeno*) při požadavku na kořenový koncový bod.</span><span class="sxs-lookup"><span data-stu-id="515a3-126">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="515a3-127">Proveďte první testovací běh s `Assert.NotEqual( ... );` , což by mělo selhat:</span><span class="sxs-lookup"><span data-stu-id="515a3-127">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="515a3-128">Změnou kontrolního výrazu otestujete middleware za normálních provozních podmínek.</span><span class="sxs-lookup"><span data-stu-id="515a3-128">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="515a3-129">Konečný test používá `Assert.Equal( ... );` .</span><span class="sxs-lookup"><span data-stu-id="515a3-129">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="515a3-130">Spusťte test znovu a potvrďte, že předává.</span><span class="sxs-lookup"><span data-stu-id="515a3-130">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="515a3-131">Odeslat požadavky pomocí HttpContext</span><span class="sxs-lookup"><span data-stu-id="515a3-131">Send requests with HttpContext</span></span>

<span data-ttu-id="515a3-132">Testovací aplikace může také odeslat žádost pomocí [SendAsync (Action \<HttpContext> , CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="515a3-132">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="515a3-133">V následujícím příkladu je provedeno několik kontrol, pokud `https://example.com/A/Path/?and=query` je zpracováván middleware:</span><span class="sxs-lookup"><span data-stu-id="515a3-133">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="515a3-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>povoluje přímou konfiguraci <xref:Microsoft.AspNetCore.Http.HttpContext> objektu místo použití <xref:System.Net.Http.HttpClient> abstrakcí.</span><span class="sxs-lookup"><span data-stu-id="515a3-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="515a3-135">Slouží <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> k manipulaci s strukturami, které jsou k dispozici pouze na serveru, například [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) nebo [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="515a3-135">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="515a3-136">Stejně jako v předchozím příkladu, který byl testován pro odpověď *404 – nenalezen* , zkontrolujte u každého `Assert` příkazu v předchozím testu opak.</span><span class="sxs-lookup"><span data-stu-id="515a3-136">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="515a3-137">Kontrola potvrdí, že se test správně nezdařil, když middleware pracuje normálně.</span><span class="sxs-lookup"><span data-stu-id="515a3-137">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="515a3-138">Po potvrzení, že falešně pozitivní test funguje, nastavte konečné `Assert` příkazy pro očekávané podmínky a hodnoty testu.</span><span class="sxs-lookup"><span data-stu-id="515a3-138">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="515a3-139">Spusťte ho znovu a potvrďte, že test proběhl úspěšně.</span><span class="sxs-lookup"><span data-stu-id="515a3-139">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="515a3-140">Omezení TestServer</span><span class="sxs-lookup"><span data-stu-id="515a3-140">TestServer limitations</span></span>

<span data-ttu-id="515a3-141">TestServer:</span><span class="sxs-lookup"><span data-stu-id="515a3-141">TestServer:</span></span>

* <span data-ttu-id="515a3-142">Byl vytvořen k replikaci chování serveru pro testování middlewaru.</span><span class="sxs-lookup"><span data-stu-id="515a3-142">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="515a3-143">***Se*** nepokouší replikovat všechna <xref:System.Net.Http.HttpClient> chování.</span><span class="sxs-lookup"><span data-stu-id="515a3-143">Does ***not*** try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
* <span data-ttu-id="515a3-144">Pokusí se dát klientovi přístup k co největšímu množství kontroly nad serverem a s co nejblíže k, co se na serveru děje.</span><span class="sxs-lookup"><span data-stu-id="515a3-144">Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="515a3-145">Například může vyvolat výjimky, které nejsou obvykle vyvolány nástrojem, `HttpClient` aby mohl přímo komunikovat stav serveru.</span><span class="sxs-lookup"><span data-stu-id="515a3-145">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="515a3-146">Nenastavuje některé hlavičky specifické pro přenos ve výchozím nastavení, protože nejsou obvykle relevantní pro middleware.</span><span class="sxs-lookup"><span data-stu-id="515a3-146">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="515a3-147">Další informace naleznete v následujícím oddílu.</span><span class="sxs-lookup"><span data-stu-id="515a3-147">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="515a3-148">Hlavičky Content-Length a transfer-Encoding</span><span class="sxs-lookup"><span data-stu-id="515a3-148">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="515a3-149">TestServer ***nenastavuje požadavek*** související s přenosem nebo hlavičky odpovědí, jako je [Délka obsahu](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) nebo [přenosové kódování](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span><span class="sxs-lookup"><span data-stu-id="515a3-149">TestServer does ***not*** set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="515a3-150">Aplikace by se měly v závislosti na těchto hlavičkách vyhnout, protože jejich použití se liší podle klienta, scénáře a protokolu.</span><span class="sxs-lookup"><span data-stu-id="515a3-150">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="515a3-151">Pokud `Content-Length` a `Transfer-Encoding` jsou nezbytné k otestování konkrétního scénáře, lze je zadat v testu při vytváření <xref:System.Net.Http.HttpRequestMessage> nebo <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="515a3-151">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="515a3-152">Další informace najdete v následujících problémech GitHubu:</span><span class="sxs-lookup"><span data-stu-id="515a3-152">For more information, see the following GitHub issues:</span></span>

* [<span data-ttu-id="515a3-153">dotnet/aspnetcore # 21677</span><span class="sxs-lookup"><span data-stu-id="515a3-153">dotnet/aspnetcore#21677</span></span>](https://github.com/dotnet/aspnetcore/issues/21677)
* [<span data-ttu-id="515a3-154">dotnet/aspnetcore # 18463</span><span class="sxs-lookup"><span data-stu-id="515a3-154">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="515a3-155">dotnet/aspnetcore # 13273</span><span class="sxs-lookup"><span data-stu-id="515a3-155">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)

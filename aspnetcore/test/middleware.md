---
title: Test ASP.NET Core middlewaru
author: tratcher
description: Naučte se testovat ASP.NET Core middlewaru pomocí TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876435"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="2e02b-103">Test ASP.NET Core middlewaru</span><span class="sxs-lookup"><span data-stu-id="2e02b-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="2e02b-104">Autor – [Chris Rossův](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="2e02b-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="2e02b-105">Middleware lze testovat v izolaci <xref:Microsoft.AspNetCore.TestHost.TestServer>pomocí.</span><span class="sxs-lookup"><span data-stu-id="2e02b-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="2e02b-106">Umožňuje:</span><span class="sxs-lookup"><span data-stu-id="2e02b-106">It allows you to:</span></span>

* <span data-ttu-id="2e02b-107">Vytvořte instanci kanálu aplikace obsahující pouze součásti, které je třeba otestovat.</span><span class="sxs-lookup"><span data-stu-id="2e02b-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="2e02b-108">Odešlete vlastní žádosti o ověření chování middlewaru.</span><span class="sxs-lookup"><span data-stu-id="2e02b-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="2e02b-109">Výhody:</span><span class="sxs-lookup"><span data-stu-id="2e02b-109">Advantages:</span></span>

* <span data-ttu-id="2e02b-110">Požadavky se odesílají v paměti a neprovádí se jejich serializace přes síť.</span><span class="sxs-lookup"><span data-stu-id="2e02b-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="2e02b-111">Tím se vyhnete dalším obavám, jako je Správa portů a certifikáty HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2e02b-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="2e02b-112">Výjimky v middlewaru můžou přesměrovat přímo zpět na volající test.</span><span class="sxs-lookup"><span data-stu-id="2e02b-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="2e02b-113">Je možné přizpůsobit datové struktury serveru, například <xref:Microsoft.AspNetCore.Http.HttpContext>přímo v testu.</span><span class="sxs-lookup"><span data-stu-id="2e02b-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="2e02b-114">Nastavení TestServer</span><span class="sxs-lookup"><span data-stu-id="2e02b-114">Set up the TestServer</span></span>

<span data-ttu-id="2e02b-115">V testovacím projektu vytvořte test:</span><span class="sxs-lookup"><span data-stu-id="2e02b-115">In the test project, create a test:</span></span>

* <span data-ttu-id="2e02b-116">Sestavte a spusťte hostitele, <xref:Microsoft.AspNetCore.TestHost.TestServer>který používá.</span><span class="sxs-lookup"><span data-stu-id="2e02b-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="2e02b-117">Přidejte všechny požadované služby, které middleware používá.</span><span class="sxs-lookup"><span data-stu-id="2e02b-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="2e02b-118">Nakonfigurujte kanál zpracování, aby používal middleware pro test.</span><span class="sxs-lookup"><span data-stu-id="2e02b-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="2e02b-119">Odesílání žádostí pomocí HttpClient</span><span class="sxs-lookup"><span data-stu-id="2e02b-119">Send requests with HttpClient</span></span>
<span data-ttu-id="2e02b-120">Odeslat žádost pomocí <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="2e02b-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="2e02b-121">Vyhodnotit výsledek.</span><span class="sxs-lookup"><span data-stu-id="2e02b-121">Assert the result.</span></span> <span data-ttu-id="2e02b-122">Nejprve udělejte kontrolní výraz opakem očekávaného výsledku.</span><span class="sxs-lookup"><span data-stu-id="2e02b-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="2e02b-123">Počáteční spuštění s falešně pozitivním kontrolním výrazem potvrdí, že test neproběhne úspěšně v případě, že middleware probíhá správně.</span><span class="sxs-lookup"><span data-stu-id="2e02b-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="2e02b-124">Spusťte test a potvrďte, že test se nezdařil.</span><span class="sxs-lookup"><span data-stu-id="2e02b-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="2e02b-125">V následujícím příkladu by middleware měl vrátit stavový kód 404 (*Nenalezeno*) při požadavku na kořenový koncový bod.</span><span class="sxs-lookup"><span data-stu-id="2e02b-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="2e02b-126">Proveďte první testovací běh s `Assert.NotEqual( ... );`, což by mělo selhat:</span><span class="sxs-lookup"><span data-stu-id="2e02b-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="2e02b-127">Změnou kontrolního výrazu otestujete middleware za normálních provozních podmínek.</span><span class="sxs-lookup"><span data-stu-id="2e02b-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="2e02b-128">Konečný test používá `Assert.Equal( ... );`.</span><span class="sxs-lookup"><span data-stu-id="2e02b-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="2e02b-129">Spusťte test znovu a potvrďte, že předává.</span><span class="sxs-lookup"><span data-stu-id="2e02b-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="2e02b-130">Odeslat požadavky pomocí HttpContext</span><span class="sxs-lookup"><span data-stu-id="2e02b-130">Send requests with HttpContext</span></span>

<span data-ttu-id="2e02b-131">Testovací aplikace může také odeslat žádost pomocí [SendAsync (Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="2e02b-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="2e02b-132">V následujícím příkladu je provedeno několik kontrol, pokud `https://example.com/A/Path/?and=query` je zpracováván middleware:</span><span class="sxs-lookup"><span data-stu-id="2e02b-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

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

<span data-ttu-id="2e02b-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>povoluje přímou konfiguraci <xref:Microsoft.AspNetCore.Http.HttpContext> objektu místo použití <xref:System.Net.Http.HttpClient> abstrakcí.</span><span class="sxs-lookup"><span data-stu-id="2e02b-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="2e02b-134">Slouží <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> k manipulaci s strukturami, které jsou k dispozici pouze na serveru, například [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) nebo [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="2e02b-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="2e02b-135">Stejně jako v předchozím příkladu, který byl testován pro odpověď *404 – nenalezen* , zkontrolujte u každého `Assert` příkazu v předchozím testu opak.</span><span class="sxs-lookup"><span data-stu-id="2e02b-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="2e02b-136">Kontrola potvrdí, že se test správně nezdařil, když middleware pracuje normálně.</span><span class="sxs-lookup"><span data-stu-id="2e02b-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="2e02b-137">Po potvrzení, že falešně pozitivní test funguje, nastavte konečné `Assert` příkazy pro očekávané podmínky a hodnoty testu.</span><span class="sxs-lookup"><span data-stu-id="2e02b-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="2e02b-138">Spusťte ho znovu a potvrďte, že test proběhl úspěšně.</span><span class="sxs-lookup"><span data-stu-id="2e02b-138">Run it again to confirm that the test passes.</span></span>

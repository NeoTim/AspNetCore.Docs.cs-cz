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
# <a name="test-aspnet-core-middleware"></a>Test ASP.NET Core middlewaru

Autor – [Chris Rossův](https://github.com/Tratcher)

Middleware lze testovat v izolaci <xref:Microsoft.AspNetCore.TestHost.TestServer>pomocí. Umožňuje:

* Vytvořte instanci kanálu aplikace obsahující pouze součásti, které je třeba otestovat.
* Odešlete vlastní žádosti o ověření chování middlewaru.

Výhody:

* Požadavky se odesílají v paměti a neprovádí se jejich serializace přes síť.
* Tím se vyhnete dalším obavám, jako je Správa portů a certifikáty HTTPS.
* Výjimky v middlewaru můžou přesměrovat přímo zpět na volající test.
* Je možné přizpůsobit datové struktury serveru, například <xref:Microsoft.AspNetCore.Http.HttpContext>přímo v testu.

## <a name="set-up-the-testserver"></a>Nastavení TestServer

V testovacím projektu vytvořte test:

* Sestavte a spusťte hostitele, <xref:Microsoft.AspNetCore.TestHost.TestServer>který používá.
* Přidejte všechny požadované služby, které middleware používá.
* Nakonfigurujte kanál zpracování, aby používal middleware pro test.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>Odesílání žádostí pomocí HttpClient
Odeslat žádost pomocí <xref:System.Net.Http.HttpClient>:

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Vyhodnotit výsledek. Nejprve udělejte kontrolní výraz opakem očekávaného výsledku. Počáteční spuštění s falešně pozitivním kontrolním výrazem potvrdí, že test neproběhne úspěšně v případě, že middleware probíhá správně. Spusťte test a potvrďte, že test se nezdařil.

V následujícím příkladu by middleware měl vrátit stavový kód 404 (*Nenalezeno*) při požadavku na kořenový koncový bod. Proveďte první testovací běh s `Assert.NotEqual( ... );`, což by mělo selhat:

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Změnou kontrolního výrazu otestujete middleware za normálních provozních podmínek. Konečný test používá `Assert.Equal( ... );`. Spusťte test znovu a potvrďte, že předává.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>Odeslat požadavky pomocí HttpContext

Testovací aplikace může také odeslat žádost pomocí [SendAsync (Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A). V následujícím příkladu je provedeno několik kontrol, pokud `https://example.com/A/Path/?and=query` je zpracováván middleware:

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

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>povoluje přímou konfiguraci <xref:Microsoft.AspNetCore.Http.HttpContext> objektu místo použití <xref:System.Net.Http.HttpClient> abstrakcí. Slouží <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> k manipulaci s strukturami, které jsou k dispozici pouze na serveru, například [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) nebo [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).

Stejně jako v předchozím příkladu, který byl testován pro odpověď *404 – nenalezen* , zkontrolujte u každého `Assert` příkazu v předchozím testu opak. Kontrola potvrdí, že se test správně nezdařil, když middleware pracuje normálně. Po potvrzení, že falešně pozitivní test funguje, nastavte konečné `Assert` příkazy pro očekávané podmínky a hodnoty testu. Spusťte ho znovu a potvrďte, že test proběhl úspěšně.

---
title: Test ASP.NET Core middlewaru
author: tratcher
description: Naučte se testovat ASP.NET Core middlewaru pomocí TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 235010c95cdd0c7ce1368b4abd91e75d81ae094b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021897"
---
# <a name="test-aspnet-core-middleware"></a>Test ASP.NET Core middlewaru

Autor – [Chris Rossův](https://github.com/Tratcher)

Middleware lze testovat v izolaci pomocí <xref:Microsoft.AspNetCore.TestHost.TestServer> . Umožňuje:

* Vytvořte instanci kanálu aplikace obsahující pouze součásti, které je třeba otestovat.
* Odešlete vlastní žádosti o ověření chování middlewaru.

Výhody:

* Požadavky se odesílají v paměti a neprovádí se jejich serializace přes síť.
* Tím se vyhnete dalším obavám, jako je Správa portů a certifikáty HTTPS.
* Výjimky v middlewaru můžou přesměrovat přímo zpět na volající test.
* Je možné přizpůsobit datové struktury serveru, například <xref:Microsoft.AspNetCore.Http.HttpContext> přímo v testu.

## <a name="set-up-the-testserver"></a>Nastavení TestServer

V testovacím projektu vytvořte test:

* Sestavte a spusťte hostitele, který používá <xref:Microsoft.AspNetCore.TestHost.TestServer> .
* Přidejte všechny požadované služby, které middleware používá.
* Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) :
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* Nakonfigurujte kanál zpracování, aby používal middleware pro test.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>Odesílání žádostí pomocí HttpClient
Odeslat žádost pomocí <xref:System.Net.Http.HttpClient> :

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Vyhodnotit výsledek. Nejprve udělejte kontrolní výraz opakem očekávaného výsledku. Počáteční spuštění s falešně pozitivním kontrolním výrazem potvrdí, že test neproběhne úspěšně v případě, že middleware probíhá správně. Spusťte test a potvrďte, že test se nezdařil.

V následujícím příkladu by middleware měl vrátit stavový kód 404 (*Nenalezeno*) při požadavku na kořenový koncový bod. Proveďte první testovací běh s `Assert.NotEqual( ... );` , což by mělo selhat:

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Změnou kontrolního výrazu otestujete middleware za normálních provozních podmínek. Konečný test používá `Assert.Equal( ... );` . Spusťte test znovu a potvrďte, že předává.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>Odeslat požadavky pomocí HttpContext

Testovací aplikace může také odeslat žádost pomocí [SendAsync (Action \<HttpContext> , CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A). V následujícím příkladu je provedeno několik kontrol, pokud `https://example.com/A/Path/?and=query` je zpracováván middleware:

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

## <a name="testserver-limitations"></a>Omezení TestServer

TestServer:

* Byl vytvořen k replikaci chování serveru pro testování middlewaru.
* ***Se*** nepokouší replikovat všechna <xref:System.Net.Http.HttpClient> chování.
* Pokusí se dát klientovi přístup k co největšímu množství kontroly nad serverem a s co nejblíže k, co se na serveru děje. Například může vyvolat výjimky, které nejsou obvykle vyvolány nástrojem, `HttpClient` aby mohl přímo komunikovat stav serveru.
* Nenastavuje některé hlavičky specifické pro přenos ve výchozím nastavení, protože nejsou obvykle relevantní pro middleware. Další informace naleznete v následujícím oddílu.

### <a name="content-length-and-transfer-encoding-headers"></a>Hlavičky Content-Length a transfer-Encoding

TestServer ***nenastavuje požadavek*** související s přenosem nebo hlavičky odpovědí, jako je [Délka obsahu](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) nebo [přenosové kódování](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding). Aplikace by se měly v závislosti na těchto hlavičkách vyhnout, protože jejich použití se liší podle klienta, scénáře a protokolu. Pokud `Content-Length` a `Transfer-Encoding` jsou nezbytné k otestování konkrétního scénáře, lze je zadat v testu při vytváření <xref:System.Net.Http.HttpRequestMessage> nebo <xref:Microsoft.AspNetCore.Http.HttpContext> . Další informace najdete v následujících problémech GitHubu:

* [dotnet/aspnetcore # 21677](https://github.com/dotnet/aspnetcore/issues/21677)
* [dotnet/aspnetcore # 18463](https://github.com/dotnet/aspnetcore/issues/18463)
* [dotnet/aspnetcore # 13273](https://github.com/dotnet/aspnetcore/issues/13273)

---
title: Ověřování a autorizace v gRPC pro ASP.NET Core
author: jamesnk
description: Naučte se používat ověřování a autorizaci v gRPC pro ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/07/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 49024295e4db7976924397bb24567d92d6298562
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308815"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Ověřování a autorizace v gRPC pro ASP.NET Core

Od [James Newton – král](https://twitter.com/jamesnk)

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(stažení)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Ověřování uživatelů volajících služby gRPC

gRPC se dá použít s [ověřováním ASP.NET Core](xref:security/authentication/identity) k přidružení uživatele ke každému volání.

Následuje příklad `Startup.Configure` , který používá gRPC a ASP.NET Core ověřování:

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        routes.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> Pořadí, ve kterém zaregistrujete ASP.NET Core middlewaru ověřování. Vždy volejte `UseAuthentication` `UseAuthorization` apřed`UseEndpoints`a před. `UseRouting`

Po nastavení ověřování se k uživateli dá v metodách služby gRPC přístup pomocí `ServerCallContext`.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Ověřování nosných tokenů

Klient může pro ověřování poskytnout přístupový token. Server token ověří a použije ho k identifikaci uživatele.

Na serveru je ověřování pomocí tokenu nosiče nakonfigurované pomocí [middleware nosiče JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

V klientovi .NET gRPC je možné token odeslat s voláními jako hlavičku:

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

### <a name="client-certificate-authentication"></a>Ověřování certifikátu klienta

Klient může případně poskytnout klientský certifikát pro ověřování. [Ověřování certifikátu](https://tools.ietf.org/html/rfc5246#section-7.4.4) se provádí na úrovni protokolu TLS dlouho předtím, než se někdy získá ASP.NET Core. Když požadavek vstoupí do ASP.NET Core, [balíček pro ověřování certifikátu klienta](xref:security/authentication/certauth) vám umožní tento certifikát přeložit na `ClaimsPrincipal`.

> [!NOTE]
> Hostitel musí být nakonfigurovaný tak, aby přijímal klientské certifikáty. Informace o přijímání klientských certifikátů v Kestrel, IIS a Azure najdete v tématu [Konfigurace hostitele pro vyžadování certifikátů](xref:security/authentication/certauth#configure-your-host-to-require-certificates) .

V klientovi .NET gRPC se certifikát klienta přidá do `HttpClientHandler` , který pak slouží k vytvoření klienta gRPC:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC client
    var httpClient = new HttpClient(handler);
    httpClient.BaseAddress = new Uri(baseAddress);

    return GrpcClient.Create<Ticketer.TicketerClient>(httpClient);
}
```

### <a name="other-authentication-mechanisms"></a>Jiné mechanismy ověřování

Kromě nosných tokenů a ověřování klientských certifikátů je potřeba, aby všechny ASP.NET Core podporované mechanismy ověřování, jako je OAuth, OpenID a Negotiate, pracovaly s gRPC. Další informace o konfiguraci ověřování na straně serveru najdete na stránce [ASP.NET Core ověřování](xref:security/authentication/identity) .

Konfigurace na straně klienta bude záviset na mechanismu ověřování, které používáte. Předchozí tokeny nosiče a příklady ověřování klientského certifikátu ukazují několik způsobů, jak může být klient gRPC nakonfigurovaný tak, aby odesílal metadata ověřování pomocí volání gRPC:

* GRPC klienti silného typu `HttpClient` používají interně. Ověřování lze nakonfigurovat na [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)nebo přidáním vlastních [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) instancí do `HttpClient`.
* Každé volání gRPC má nepovinný `CallOptions` argument. Vlastní záhlaví lze odeslat pomocí kolekce záhlaví možnosti.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorizace uživatelů přístup k službám a metodám služeb

Ve výchozím nastavení mohou být všechny metody ve službě volány neověřenými uživateli. Chcete-li vyžadovat ověření, použijte pro službu atribut [[autorizovat]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Pomocí argumentů konstruktoru a vlastností `[Authorize]` atributu můžete omezit přístup jenom na uživatele, kteří odpovídají na konkrétní [zásady autorizace](xref:security/authorization/policies). Pokud máte například vlastní zásadu `MyAuthorizationPolicy`autorizace, ujistěte se, že ke službě budou mít přístup jenom uživatelé, kteří mají k této zásadě přístup pomocí následujícího kódu:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Jednotlivé metody služby mohou mít `[Authorize]` také použit atribut. Pokud aktuální uživatel neodpovídá zásadám použitým **pro metodu** i třídu, je volajícímu vrácena chyba:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>Další zdroje

* [Ověřování nosných tokenů v ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Konfigurace ověřování klientského certifikátu v ASP.NET Core](xref:security/authentication/certauth)

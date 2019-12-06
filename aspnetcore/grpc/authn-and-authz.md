---
title: Ověřování a autorizace v gRPC pro ASP.NET Core
author: jamesnk
description: Naučte se používat ověřování a autorizaci v gRPC pro ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 258b34113f3c3d9ef2031a43295ea5806b1e22ff
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880694"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Ověřování a autorizace v gRPC pro ASP.NET Core

Od [James Newton – král](https://twitter.com/jamesnk)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Ověřování uživatelů volajících služby gRPC

gRPC se dá použít s [ověřováním ASP.NET Core](xref:security/authentication/identity) k přidružení uživatele ke každému volání.

Následuje příklad `Startup.Configure`, který používá ověřování gRPC a ASP.NET Core:

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
> Pořadí, ve kterém zaregistrujete ASP.NET Core middlewaru ověřování. Po `UseRouting` a před `UseEndpoints`vždy volejte `UseAuthentication` a `UseAuthorization`.

Mechanismus ověřování, který vaše aplikace používá během volání, je nutné nakonfigurovat. Konfigurace ověřování je přidaná v `Startup.ConfigureServices` a bude se lišit v závislosti na mechanismu ověřování, který vaše aplikace používá. Příklady zabezpečení aplikací ASP.NET Core najdete v tématu [ukázky ověřování](xref:security/authentication/samples).

Po nastavení ověřování bude k uživateli přístup v metodách služby gRPC prostřednictvím `ServerCallContext`.

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

Konfigurace `ChannelCredentials` na kanálu je alternativní způsob, jak odeslat token službě prostřednictvím volání gRPC. Přihlašovací údaje se spustí pokaždé, když se vytvoří volání gRPC, což zabrání nutnosti psát kód na více místech, aby bylo možné token předat sami.

Přihlašovací údaje v následujícím příkladu nakonfiguruje kanál tak, aby odesílal token při každém volání gRPC:

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>Ověření certifikátu klienta

Klient může případně poskytnout klientský certifikát pro ověřování. [Ověřování certifikátu](https://tools.ietf.org/html/rfc5246#section-7.4.4) se provádí na úrovni protokolu TLS dlouho předtím, než se někdy získá ASP.NET Core. Když požadavek vstoupí do ASP.NET Core, [balíček pro ověřování certifikátu klienta](xref:security/authentication/certauth) vám umožní tento certifikát přeložit na `ClaimsPrincipal`.

> [!NOTE]
> Hostitel musí být nakonfigurovaný tak, aby přijímal klientské certifikáty. Informace o přijímání klientských certifikátů v Kestrel, IIS a Azure najdete v tématu [Konfigurace hostitele pro vyžadování certifikátů](xref:security/authentication/certauth#configure-your-host-to-require-certificates) .

V klientovi .NET gRPC se certifikát klienta přidá do `HttpClientHandler`, který se pak používá k vytvoření klienta gRPC:

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>Jiné mechanismy ověřování

Mnoho ASP.NET Core podporovaných mechanismů ověřování funguje s gRPC:

* Azure Active Directory
* Certifikát klienta
* IdentityServer
* Token JWT
* OAuth 2.0
* OpenID Connect
* WS-Federation

Další informace o konfiguraci ověřování na serveru najdete v tématu [ASP.NET Core Authentication](xref:security/authentication/identity).

Konfigurace klienta gRPC na používání ověřování bude záviset na mechanismu ověřování, který používáte. Příklady předchozího nosiče a klientského certifikátu ukazují několik způsobů, jak může být klient gRPC nakonfigurovaný tak, aby odesílal metadata ověřování pomocí volání gRPC:

* Klienti se silným typem gRPC používají `HttpClient` interně. Ověřování lze nakonfigurovat na [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)nebo přidáním vlastních instancí [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) do `HttpClient`.
* Každé volání gRPC má volitelný argument `CallOptions`. Vlastní záhlaví lze odeslat pomocí kolekce záhlaví možnosti.

> [!NOTE]
> Ověřování systému Windows (NTLM/Kerberos/Negotiate) nelze použít s gRPC. gRPC vyžaduje HTTP/2 a HTTP/2 nepodporuje ověřování systému Windows.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorizace uživatelů přístup k službám a metodám služeb

Ve výchozím nastavení mohou být všechny metody ve službě volány neověřenými uživateli. Chcete-li vyžadovat ověření, použijte atribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) pro službu:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Pomocí argumentů konstruktoru a vlastností atributu `[Authorize]` můžete omezit přístup jenom na uživatele, kteří splňují konkrétní [zásady autorizace](xref:security/authorization/policies). Pokud máte například vlastní zásadu autorizace nazvanou `MyAuthorizationPolicy`, zajistěte, aby ke službě měli přístup jenom uživatelé, kteří mají k této zásadě přístup, pomocí následujícího kódu:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Jednotlivé metody služby mohou mít také použit atribut `[Authorize]`. Pokud aktuální uživatel neodpovídá zásadám použitým **pro metodu i třídu** , je volajícímu vrácena chyba:

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

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Ověřování nosných tokenů v ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Konfigurace ověřování klientského certifikátu v ASP.NET Core](xref:security/authentication/certauth)

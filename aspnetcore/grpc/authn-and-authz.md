---
title: Ověřování a autorizace v gRPC pro ASP.NET Core
author: jamesnk
description: Přečtěte si, jak používat ověřování a autorizaci v gRPC pro ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: c0312b186bbb35e3b802984484b7213016d8bf04
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78964432"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Ověřování a autorizace v gRPC pro ASP.NET Core

Podle [James Newton-King](https://twitter.com/jamesnk)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Ověření uživatelů volajících službu gRPC

gRPC lze použít s [ASP.NET core ověřování](xref:security/authentication/identity) maže přikaždém volání uživatele.

Následuje příklad, který `Startup.Configure` používá gRPC a ASP.NET základní ověřování:

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> Pořadí, ve kterém zaregistrujete ASP.NET základní ověřování middleware záležitosti. Vždy `UseAuthentication` volejte `UseAuthorization` `UseRouting` a `UseEndpoints`po a před .

Ověřovací mechanismus, který vaše aplikace používá během volání, musí být nakonfigurován. Konfigurace ověřování se `Startup.ConfigureServices` přidá a bude se lišit v závislosti na mechanismu ověřování, který vaše aplikace používá. Příklady zabezpečení aplikací ASP.NET Core najdete v [tématu Ukázky ověřování](xref:security/authentication/samples).

Po nastavení ověřování je uživatel přístupný v metodách služby gRPC prostřednictvím `ServerCallContext`rozhraní .

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Ověřování nože tokenu

Klient může poskytnout přístupový token pro ověřování. Server ověří token a použije jej k identifikaci uživatele.

Na serveru je ověřování nosnicových tokenů konfigurováno pomocí [middlewaru JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

V klientovi .NET gRPC lze token odeslat s voláním jako záhlaví:

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

Konfigurace `ChannelCredentials` na kanálu je alternativní způsob, jak odeslat token do služby s voláním gRPC. Pověření je spuštěna pokaždé, když je provedeno volání gRPC, což se vyhýbá nutnosti psát kód na více místech předat token sami.

Pověření v následujícím příkladu konfiguruje kanál pro odeslání tokenu s každým voláním gRPC:

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

### <a name="client-certificate-authentication"></a>Ověření klientského certifikátu

Klient by mohl alternativně poskytnout klientský certifikát pro ověřování. [Ověřování certifikátů](https://tools.ietf.org/html/rfc5246#section-7.4.4) probíhá na úrovni TLS, dlouho předtím, než se vůbec dostane k ASP.NET Core. Když požadavek zadá ASP.NET core, [balíček ověřování klientského certifikátu](xref:security/authentication/certauth) umožňuje přeložit certifikát na `ClaimsPrincipal`.

> [!NOTE]
> Hostitel musí být nakonfigurován tak, aby přijímal klientské certifikáty. Viz [konfigurace hostitele tak, aby vyžadoval certifikáty](xref:security/authentication/certauth#configure-your-host-to-require-certificates) pro informace o přijímání klientských certifikátů v Kestrel, IIS a Azure.

V klientovi .NET gRPC je klientský certifikát přidán k `HttpClientHandler` tomu, který se pak použije k vytvoření klienta gRPC:

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

Mnoho ASP.NET core podporované mechanismy ověřování pracují s gRPC:

* Azure Active Directory
* Klientský certifikát
* IdentityServer
* JWT Token
* OAuth 2.0
* OpenID Connect
* WS-Federation

Další informace o konfiguraci ověřování na serveru naleznete v [tématu ASP.NET Ověřování jádra](xref:security/authentication/identity).

Konfigurace klienta gRPC pro použití ověřování bude záviset na mechanismu ověřování, který používáte. Předchozí příklady nože a klientského certifikátu ukazují několik způsobů, jak lze klienta gRPC nakonfigurovat tak, aby odesílá metadata ověřování pomocí volání gRPC:

* Klienti gRPC silného `HttpClient` typu používají interně. Ověřování lze nakonfigurovat na [httpclienthandleru](/dotnet/api/system.net.http.httpclienthandler)nebo přidáním vlastních instancí [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) do . `HttpClient`
* Každé volání gRPC `CallOptions` má volitelný argument. Vlastní záhlaví lze odeslat pomocí kolekce záhlaví možnosti.

> [!NOTE]
> Ověřování systému Windows (NTLM/Kerberos/Negotiate) nelze použít s gRPC. gRPC vyžaduje protokol HTTP/2 a protokol HTTP/2 nepodporuje ověřování systému Windows.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorizace uživatelů k přístupu ke službám a metodám služeb

Ve výchozím nastavení mohou být všechny metody ve službě volány neověřenými uživateli. Chcete-li vyžadovat [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ověření, použijte atribut pro službu:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Argumenty konstruktoru a vlastnosti `[Authorize]` atributu můžete použít k omezení přístupu pouze na uživatele odpovídající určitým [zásadám autorizace](xref:security/authorization/policies). Pokud máte například vlastní zásadu `MyAuthorizationPolicy`autorizace s názvem , ujistěte se, že ke službě mají přístup pouze uživatelé odpovídající této zásadě pomocí následujícího kódu:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Jednotlivé metody služby `[Authorize]` mohou mít atribut také použit. Pokud aktuální uživatel neodpovídá zásady použité pro metodu **a** třídy, je volajícímu vrácena chyba:

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

* [Ověřování nosné tokeny v ASP.NET jádru](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Konfigurace ověřování klientského certifikátu v ASP.NET jádra](xref:security/authentication/certauth)

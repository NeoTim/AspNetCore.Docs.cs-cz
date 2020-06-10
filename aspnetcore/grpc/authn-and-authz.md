---
title: Ověřování a autorizace v gRPC pro ASP.NET Core
author: jamesnk
description: Naučte se používat ověřování a autorizaci v gRPC pro ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/authn-and-authz
ms.openlocfilehash: f9d2e73f57d69e1eb5039019dc9e64193cf67820
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105789"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="84e27-103">Ověřování a autorizace v gRPC pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84e27-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="84e27-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="84e27-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="84e27-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="84e27-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="84e27-106">Ověřování uživatelů volajících služby gRPC</span><span class="sxs-lookup"><span data-stu-id="84e27-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="84e27-107">gRPC se dá použít s [ověřováním ASP.NET Core](xref:security/authentication/identity) k přidružení uživatele ke každému volání.</span><span class="sxs-lookup"><span data-stu-id="84e27-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="84e27-108">Následuje příklad, `Startup.Configure` který používá gRPC a ASP.NET Core ověřování:</span><span class="sxs-lookup"><span data-stu-id="84e27-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="84e27-109">Pořadí, ve kterém zaregistrujete ASP.NET Core middlewaru ověřování.</span><span class="sxs-lookup"><span data-stu-id="84e27-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="84e27-110">Vždy volejte `UseAuthentication` a `UseAuthorization` `UseRouting` před a před `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="84e27-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="84e27-111">Mechanismus ověřování, který vaše aplikace používá během volání, je nutné nakonfigurovat.</span><span class="sxs-lookup"><span data-stu-id="84e27-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="84e27-112">Konfigurace ověřování je přidaná v `Startup.ConfigureServices` a bude se lišit v závislosti na mechanismu ověřování, který vaše aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="84e27-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="84e27-113">Příklady zabezpečení aplikací ASP.NET Core najdete v tématu [ukázky ověřování](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="84e27-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="84e27-114">Po nastavení ověřování se k uživateli dá v metodách služby gRPC přístup pomocí `ServerCallContext` .</span><span class="sxs-lookup"><span data-stu-id="84e27-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="84e27-115">Ověřování nosných tokenů</span><span class="sxs-lookup"><span data-stu-id="84e27-115">Bearer token authentication</span></span>

<span data-ttu-id="84e27-116">Klient může pro ověřování poskytnout přístupový token.</span><span class="sxs-lookup"><span data-stu-id="84e27-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="84e27-117">Server token ověří a použije ho k identifikaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="84e27-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="84e27-118">Na serveru je ověřování pomocí tokenu nosiče nakonfigurované pomocí [middleware nosiče JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="84e27-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="84e27-119">V klientovi .NET gRPC je možné token odeslat s voláními jako hlavičku:</span><span class="sxs-lookup"><span data-stu-id="84e27-119">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

<span data-ttu-id="84e27-120">Konfigurace `ChannelCredentials` na kanálu je alternativní způsob, jak odeslat token službě pomocí volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="84e27-120">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="84e27-121">Přihlašovací údaje se spustí pokaždé, když se vytvoří volání gRPC, což zabrání nutnosti psát kód na více místech, aby bylo možné token předat sami.</span><span class="sxs-lookup"><span data-stu-id="84e27-121">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="84e27-122">Přihlašovací údaje v následujícím příkladu nakonfiguruje kanál tak, aby odesílal token při každém volání gRPC:</span><span class="sxs-lookup"><span data-stu-id="84e27-122">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="84e27-123">Ověřování certifikátu klienta</span><span class="sxs-lookup"><span data-stu-id="84e27-123">Client certificate authentication</span></span>

<span data-ttu-id="84e27-124">Klient může případně poskytnout klientský certifikát pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="84e27-124">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="84e27-125">[Ověřování certifikátu](https://tools.ietf.org/html/rfc5246#section-7.4.4) se provádí na úrovni protokolu TLS dlouho předtím, než se někdy získá ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="84e27-125">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="84e27-126">Když požadavek vstoupí do ASP.NET Core, [balíček pro ověřování certifikátu klienta](xref:security/authentication/certauth) vám umožní tento certifikát přeložit na `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="84e27-126">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="84e27-127">Hostitel musí být nakonfigurovaný tak, aby přijímal klientské certifikáty.</span><span class="sxs-lookup"><span data-stu-id="84e27-127">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="84e27-128">Informace o přijímání klientských certifikátů v Kestrel, IIS a Azure najdete v tématu [Konfigurace hostitele pro vyžadování certifikátů](xref:security/authentication/certauth#configure-your-host-to-require-certificates) .</span><span class="sxs-lookup"><span data-stu-id="84e27-128">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="84e27-129">V klientovi .NET gRPC se certifikát klienta přidá do `HttpClientHandler` , který pak slouží k vytvoření klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="84e27-129">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="84e27-130">Jiné mechanismy ověřování</span><span class="sxs-lookup"><span data-stu-id="84e27-130">Other authentication mechanisms</span></span>

<span data-ttu-id="84e27-131">Mnoho ASP.NET Core podporovaných mechanismů ověřování funguje s gRPC:</span><span class="sxs-lookup"><span data-stu-id="84e27-131">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="84e27-132">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="84e27-132">Azure Active Directory</span></span>
* <span data-ttu-id="84e27-133">Certifikát klienta</span><span class="sxs-lookup"><span data-stu-id="84e27-133">Client Certificate</span></span>
* <span data-ttu-id="84e27-134">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="84e27-134">IdentityServer</span></span>
* <span data-ttu-id="84e27-135">Token JWT</span><span class="sxs-lookup"><span data-stu-id="84e27-135">JWT Token</span></span>
* <span data-ttu-id="84e27-136">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="84e27-136">OAuth 2.0</span></span>
* <span data-ttu-id="84e27-137">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="84e27-137">OpenID Connect</span></span>
* <span data-ttu-id="84e27-138">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="84e27-138">WS-Federation</span></span>

<span data-ttu-id="84e27-139">Další informace o konfiguraci ověřování na serveru najdete v tématu [ASP.NET Core Authentication](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="84e27-139">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="84e27-140">Konfigurace klienta gRPC na používání ověřování bude záviset na mechanismu ověřování, který používáte.</span><span class="sxs-lookup"><span data-stu-id="84e27-140">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="84e27-141">Příklady předchozího nosiče a klientského certifikátu ukazují několik způsobů, jak může být klient gRPC nakonfigurovaný tak, aby odesílal metadata ověřování pomocí volání gRPC:</span><span class="sxs-lookup"><span data-stu-id="84e27-141">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="84e27-142">GRPC klienti silného typu používají `HttpClient` interně.</span><span class="sxs-lookup"><span data-stu-id="84e27-142">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="84e27-143">Ověřování lze nakonfigurovat na [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler)nebo přidáním vlastních instancí [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) do `HttpClient` .</span><span class="sxs-lookup"><span data-stu-id="84e27-143">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="84e27-144">Každé volání gRPC má nepovinný `CallOptions` argument.</span><span class="sxs-lookup"><span data-stu-id="84e27-144">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="84e27-145">Vlastní záhlaví lze odeslat pomocí kolekce záhlaví možnosti.</span><span class="sxs-lookup"><span data-stu-id="84e27-145">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="84e27-146">Ověřování systému Windows (NTLM/Kerberos/Negotiate) nelze použít s gRPC.</span><span class="sxs-lookup"><span data-stu-id="84e27-146">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="84e27-147">gRPC vyžaduje HTTP/2 a HTTP/2 nepodporuje ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="84e27-147">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="84e27-148">Autorizace uživatelů přístup k službám a metodám služeb</span><span class="sxs-lookup"><span data-stu-id="84e27-148">Authorize users to access services and service methods</span></span>

<span data-ttu-id="84e27-149">Ve výchozím nastavení mohou být všechny metody ve službě volány neověřenými uživateli.</span><span class="sxs-lookup"><span data-stu-id="84e27-149">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="84e27-150">Chcete-li vyžadovat ověření, použijte [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atribut pro službu:</span><span class="sxs-lookup"><span data-stu-id="84e27-150">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="84e27-151">Pomocí argumentů konstruktoru a vlastností `[Authorize]` atributu můžete omezit přístup jenom na uživatele, kteří odpovídají na konkrétní [zásady autorizace](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="84e27-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="84e27-152">Pokud máte například vlastní zásadu autorizace `MyAuthorizationPolicy` , ujistěte se, že ke službě budou mít přístup jenom uživatelé, kteří mají k této zásadě přístup pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="84e27-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="84e27-153">Jednotlivé metody služby mohou mít `[Authorize]` také použit atribut.</span><span class="sxs-lookup"><span data-stu-id="84e27-153">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="84e27-154">Pokud aktuální uživatel neodpovídá zásadám použitým **pro metodu i třídu** , je volajícímu vrácena chyba:</span><span class="sxs-lookup"><span data-stu-id="84e27-154">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="84e27-155">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="84e27-155">Additional resources</span></span>

* [<span data-ttu-id="84e27-156">Ověřování nosných tokenů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84e27-156">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="84e27-157">Konfigurace ověřování klientského certifikátu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84e27-157">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)

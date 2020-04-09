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
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="7b91a-103">Ověřování a autorizace v gRPC pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b91a-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="7b91a-104">Podle [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="7b91a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="7b91a-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="7b91a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="7b91a-106">Ověření uživatelů volajících službu gRPC</span><span class="sxs-lookup"><span data-stu-id="7b91a-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="7b91a-107">gRPC lze použít s [ASP.NET core ověřování](xref:security/authentication/identity) maže přikaždém volání uživatele.</span><span class="sxs-lookup"><span data-stu-id="7b91a-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="7b91a-108">Následuje příklad, který `Startup.Configure` používá gRPC a ASP.NET základní ověřování:</span><span class="sxs-lookup"><span data-stu-id="7b91a-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="7b91a-109">Pořadí, ve kterém zaregistrujete ASP.NET základní ověřování middleware záležitosti.</span><span class="sxs-lookup"><span data-stu-id="7b91a-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="7b91a-110">Vždy `UseAuthentication` volejte `UseAuthorization` `UseRouting` a `UseEndpoints`po a před .</span><span class="sxs-lookup"><span data-stu-id="7b91a-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="7b91a-111">Ověřovací mechanismus, který vaše aplikace používá během volání, musí být nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="7b91a-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="7b91a-112">Konfigurace ověřování se `Startup.ConfigureServices` přidá a bude se lišit v závislosti na mechanismu ověřování, který vaše aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="7b91a-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="7b91a-113">Příklady zabezpečení aplikací ASP.NET Core najdete v [tématu Ukázky ověřování](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="7b91a-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="7b91a-114">Po nastavení ověřování je uživatel přístupný v metodách služby gRPC prostřednictvím `ServerCallContext`rozhraní .</span><span class="sxs-lookup"><span data-stu-id="7b91a-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="7b91a-115">Ověřování nože tokenu</span><span class="sxs-lookup"><span data-stu-id="7b91a-115">Bearer token authentication</span></span>

<span data-ttu-id="7b91a-116">Klient může poskytnout přístupový token pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="7b91a-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="7b91a-117">Server ověří token a použije jej k identifikaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="7b91a-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="7b91a-118">Na serveru je ověřování nosnicových tokenů konfigurováno pomocí [middlewaru JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="7b91a-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="7b91a-119">V klientovi .NET gRPC lze token odeslat s voláním jako záhlaví:</span><span class="sxs-lookup"><span data-stu-id="7b91a-119">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

<span data-ttu-id="7b91a-120">Konfigurace `ChannelCredentials` na kanálu je alternativní způsob, jak odeslat token do služby s voláním gRPC.</span><span class="sxs-lookup"><span data-stu-id="7b91a-120">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="7b91a-121">Pověření je spuštěna pokaždé, když je provedeno volání gRPC, což se vyhýbá nutnosti psát kód na více místech předat token sami.</span><span class="sxs-lookup"><span data-stu-id="7b91a-121">The credential is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span>

<span data-ttu-id="7b91a-122">Pověření v následujícím příkladu konfiguruje kanál pro odeslání tokenu s každým voláním gRPC:</span><span class="sxs-lookup"><span data-stu-id="7b91a-122">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="7b91a-123">Ověření klientského certifikátu</span><span class="sxs-lookup"><span data-stu-id="7b91a-123">Client certificate authentication</span></span>

<span data-ttu-id="7b91a-124">Klient by mohl alternativně poskytnout klientský certifikát pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="7b91a-124">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="7b91a-125">[Ověřování certifikátů](https://tools.ietf.org/html/rfc5246#section-7.4.4) probíhá na úrovni TLS, dlouho předtím, než se vůbec dostane k ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7b91a-125">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="7b91a-126">Když požadavek zadá ASP.NET core, [balíček ověřování klientského certifikátu](xref:security/authentication/certauth) umožňuje přeložit certifikát na `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="7b91a-126">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="7b91a-127">Hostitel musí být nakonfigurován tak, aby přijímal klientské certifikáty.</span><span class="sxs-lookup"><span data-stu-id="7b91a-127">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="7b91a-128">Viz [konfigurace hostitele tak, aby vyžadoval certifikáty](xref:security/authentication/certauth#configure-your-host-to-require-certificates) pro informace o přijímání klientských certifikátů v Kestrel, IIS a Azure.</span><span class="sxs-lookup"><span data-stu-id="7b91a-128">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="7b91a-129">V klientovi .NET gRPC je klientský certifikát přidán k `HttpClientHandler` tomu, který se pak použije k vytvoření klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="7b91a-129">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

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

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="7b91a-130">Jiné mechanismy ověřování</span><span class="sxs-lookup"><span data-stu-id="7b91a-130">Other authentication mechanisms</span></span>

<span data-ttu-id="7b91a-131">Mnoho ASP.NET core podporované mechanismy ověřování pracují s gRPC:</span><span class="sxs-lookup"><span data-stu-id="7b91a-131">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="7b91a-132">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="7b91a-132">Azure Active Directory</span></span>
* <span data-ttu-id="7b91a-133">Klientský certifikát</span><span class="sxs-lookup"><span data-stu-id="7b91a-133">Client Certificate</span></span>
* <span data-ttu-id="7b91a-134">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="7b91a-134">IdentityServer</span></span>
* <span data-ttu-id="7b91a-135">JWT Token</span><span class="sxs-lookup"><span data-stu-id="7b91a-135">JWT Token</span></span>
* <span data-ttu-id="7b91a-136">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="7b91a-136">OAuth 2.0</span></span>
* <span data-ttu-id="7b91a-137">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="7b91a-137">OpenID Connect</span></span>
* <span data-ttu-id="7b91a-138">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="7b91a-138">WS-Federation</span></span>

<span data-ttu-id="7b91a-139">Další informace o konfiguraci ověřování na serveru naleznete v [tématu ASP.NET Ověřování jádra](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="7b91a-139">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="7b91a-140">Konfigurace klienta gRPC pro použití ověřování bude záviset na mechanismu ověřování, který používáte.</span><span class="sxs-lookup"><span data-stu-id="7b91a-140">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="7b91a-141">Předchozí příklady nože a klientského certifikátu ukazují několik způsobů, jak lze klienta gRPC nakonfigurovat tak, aby odesílá metadata ověřování pomocí volání gRPC:</span><span class="sxs-lookup"><span data-stu-id="7b91a-141">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="7b91a-142">Klienti gRPC silného `HttpClient` typu používají interně.</span><span class="sxs-lookup"><span data-stu-id="7b91a-142">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="7b91a-143">Ověřování lze nakonfigurovat na [httpclienthandleru](/dotnet/api/system.net.http.httpclienthandler)nebo přidáním vlastních instancí [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) do . `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="7b91a-143">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="7b91a-144">Každé volání gRPC `CallOptions` má volitelný argument.</span><span class="sxs-lookup"><span data-stu-id="7b91a-144">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="7b91a-145">Vlastní záhlaví lze odeslat pomocí kolekce záhlaví možnosti.</span><span class="sxs-lookup"><span data-stu-id="7b91a-145">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="7b91a-146">Ověřování systému Windows (NTLM/Kerberos/Negotiate) nelze použít s gRPC.</span><span class="sxs-lookup"><span data-stu-id="7b91a-146">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="7b91a-147">gRPC vyžaduje protokol HTTP/2 a protokol HTTP/2 nepodporuje ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="7b91a-147">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="7b91a-148">Autorizace uživatelů k přístupu ke službám a metodám služeb</span><span class="sxs-lookup"><span data-stu-id="7b91a-148">Authorize users to access services and service methods</span></span>

<span data-ttu-id="7b91a-149">Ve výchozím nastavení mohou být všechny metody ve službě volány neověřenými uživateli.</span><span class="sxs-lookup"><span data-stu-id="7b91a-149">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="7b91a-150">Chcete-li vyžadovat [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ověření, použijte atribut pro službu:</span><span class="sxs-lookup"><span data-stu-id="7b91a-150">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="7b91a-151">Argumenty konstruktoru a vlastnosti `[Authorize]` atributu můžete použít k omezení přístupu pouze na uživatele odpovídající určitým [zásadám autorizace](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="7b91a-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="7b91a-152">Pokud máte například vlastní zásadu `MyAuthorizationPolicy`autorizace s názvem , ujistěte se, že ke službě mají přístup pouze uživatelé odpovídající této zásadě pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="7b91a-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="7b91a-153">Jednotlivé metody služby `[Authorize]` mohou mít atribut také použit.</span><span class="sxs-lookup"><span data-stu-id="7b91a-153">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="7b91a-154">Pokud aktuální uživatel neodpovídá zásady použité pro metodu **a** třídy, je volajícímu vrácena chyba:</span><span class="sxs-lookup"><span data-stu-id="7b91a-154">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="7b91a-155">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="7b91a-155">Additional resources</span></span>

* [<span data-ttu-id="7b91a-156">Ověřování nosné tokeny v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="7b91a-156">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="7b91a-157">Konfigurace ověřování klientského certifikátu v ASP.NET jádra</span><span class="sxs-lookup"><span data-stu-id="7b91a-157">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)

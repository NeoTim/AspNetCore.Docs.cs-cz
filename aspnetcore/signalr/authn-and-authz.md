---
title: Ověřování a autorizace v nástroji ASP.NET Core Signal
author: bradygaster
description: Naučte se používat ověřování a autorizaci v nástroji ASP.NET Core Signal.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 07/15/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: e7e7a9fd537ba89b64c15594652a290357a00038
ms.sourcegitcommit: f30b18442ed12831c7e86b0db249183ccd749f59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68412543"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="d2169-103">Ověřování a autorizace v nástroji ASP.NET Core Signal</span><span class="sxs-lookup"><span data-stu-id="d2169-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="d2169-104">Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="d2169-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="d2169-105">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(stažení)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d2169-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="d2169-106">Ověřování uživatelů připojujících se k centru signálů</span><span class="sxs-lookup"><span data-stu-id="d2169-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="d2169-107">K přidružení uživatele k jednotlivým připojením se dá použít signalizace s [ověřováním ASP.NET Core](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="d2169-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="d2169-108">V centru se k datům ověřování dá dostat z [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="d2169-108">In a hub, authentication data can be accessed from the [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="d2169-109">Ověřování umožňuje rozbočovači volat metody všech připojení přidružených k uživateli (Další informace najdete v tématu [Správa uživatelů a skupin v nástroji Signal](xref:signalr/groups) ).</span><span class="sxs-lookup"><span data-stu-id="d2169-109">Authentication allows the hub to call methods on all connections associated with a user (See [Manage users and groups in SignalR](xref:signalr/groups) for more information).</span></span> <span data-ttu-id="d2169-110">K jednomu uživateli může být přidruženo více připojení.</span><span class="sxs-lookup"><span data-stu-id="d2169-110">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="d2169-111">Následuje příklad `Startup.Configure` , který používá signalizaci a ASP.NET Core ověřování:</span><span class="sxs-lookup"><span data-stu-id="d2169-111">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();
    
    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="d2169-112">Pořadí, ve kterém zaregistrujete signál a ASP.NET Core v nich jsou v oblasti middleware ověřování.</span><span class="sxs-lookup"><span data-stu-id="d2169-112">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="d2169-113">Vždy zavolejte `UseAuthentication` předtím `UseSignalR` , aby `HttpContext`byl signál na.</span><span class="sxs-lookup"><span data-stu-id="d2169-113">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

### <a name="cookie-authentication"></a><span data-ttu-id="d2169-114">Ověřování souborem cookie</span><span class="sxs-lookup"><span data-stu-id="d2169-114">Cookie authentication</span></span>

<span data-ttu-id="d2169-115">V aplikaci založené na prohlížeči umožňuje ověřování pomocí souborů cookie, aby vaše stávající uživatelská pověření automaticky pokračovala v připojení k signalizaci.</span><span class="sxs-lookup"><span data-stu-id="d2169-115">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="d2169-116">Při použití klienta prohlížeče není nutná žádná další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d2169-116">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="d2169-117">Pokud je uživatel přihlášený ke svojí aplikaci, připojení k tomuto ověření automaticky zdědí připojení k tomuto signálu.</span><span class="sxs-lookup"><span data-stu-id="d2169-117">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="d2169-118">Soubory cookie jsou způsoby, jak odesílat přístupové tokeny, ale klienti bez prohlížeče je mohou odeslat.</span><span class="sxs-lookup"><span data-stu-id="d2169-118">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="d2169-119">Při použití [klienta .NET](xref:signalr/dotnet-client)lze `Cookies` vlastnost nakonfigurovat ve `.WithUrl` volání, aby bylo možné zadat soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="d2169-119">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call in order to provide a cookie.</span></span> <span data-ttu-id="d2169-120">Použití ověřování souborem cookie z klienta .NET ale vyžaduje, aby aplikace poskytovala rozhraní API pro výměnu ověřovacích dat pro soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="d2169-120">However, using cookie authentication from the .NET Client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="d2169-121">Ověřování nosných tokenů</span><span class="sxs-lookup"><span data-stu-id="d2169-121">Bearer token authentication</span></span>

<span data-ttu-id="d2169-122">Klient může místo použití souboru cookie zadat přístupový token.</span><span class="sxs-lookup"><span data-stu-id="d2169-122">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="d2169-123">Server token ověří a použije ho k identifikaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="d2169-123">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="d2169-124">Toto ověření se provádí jenom v případě, že se naváže připojení.</span><span class="sxs-lookup"><span data-stu-id="d2169-124">This validation is done only when the connection is established.</span></span> <span data-ttu-id="d2169-125">Během života připojení se server automaticky znovu neověřuje, aby zkontroloval odvolání tokenu.</span><span class="sxs-lookup"><span data-stu-id="d2169-125">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="d2169-126">Na serveru je ověřování pomocí tokenu nosiče nakonfigurované pomocí [middleware nosiče JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="d2169-126">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="d2169-127">V klientovi JavaScriptu lze token zadat pomocí možnosti [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .</span><span class="sxs-lookup"><span data-stu-id="d2169-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

<span data-ttu-id="d2169-128">V klientovi .NET existuje podobná vlastnost [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) , kterou lze použít ke konfiguraci tokenu:</span><span class="sxs-lookup"><span data-stu-id="d2169-128">In the .NET client, there is a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="d2169-129">Funkce přístupového tokenu, kterou zadáte, je volána před **všemi** požadavky HTTP provedenými signálem.</span><span class="sxs-lookup"><span data-stu-id="d2169-129">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="d2169-130">Pokud potřebujete obnovit token, abyste zachovali aktivní připojení (protože může vypršet platnost připojení), udělejte to v rámci této funkce a vraťte aktualizovaný token.</span><span class="sxs-lookup"><span data-stu-id="d2169-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="d2169-131">V případě standardních webových rozhraní API se tokeny nosiče odesílají v hlavičce protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="d2169-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="d2169-132">Návěstí ale v prohlížečích při použití některých přenosů nedokáže tato záhlaví nastavovat.</span><span class="sxs-lookup"><span data-stu-id="d2169-132">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="d2169-133">Při použití protokolu WebSockets a událostí odesílaných serverem se token přenáší jako parametr řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="d2169-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="d2169-134">Aby bylo možné tuto podporu na serveru podporovat, je nutná další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="d2169-134">In order to support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="d2169-135">Soubory cookie vs. nosných tokenů</span><span class="sxs-lookup"><span data-stu-id="d2169-135">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="d2169-136">Vzhledem k tomu, že soubory cookie jsou specifické pro prohlížeče, jejich posílání z jiných druhů klientů přináší složitost v porovnání s posíláním nosných tokenů.</span><span class="sxs-lookup"><span data-stu-id="d2169-136">Because cookies are specific to browsers, sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="d2169-137">Z tohoto důvodu se nedoporučuje ověřování souborů cookie, pokud aplikace potřebuje jenom ověřovat uživatele z klienta prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2169-137">For this reason, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="d2169-138">Ověřování nosných tokenů je doporučený postup při použití jiných klientů, než je klient prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d2169-138">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="d2169-139">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="d2169-139">Windows authentication</span></span>

<span data-ttu-id="d2169-140">Pokud je ve vaší aplikaci nakonfigurované [ověřování systému Windows](xref:security/authentication/windowsauth) , může signál tuto identitu použít k zabezpečení rozbočovačů.</span><span class="sxs-lookup"><span data-stu-id="d2169-140">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="d2169-141">Aby bylo možné odesílat zprávy jednotlivým uživatelům, je třeba přidat vlastního poskytovatele ID uživatele.</span><span class="sxs-lookup"><span data-stu-id="d2169-141">However, in order to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="d2169-142">Důvodem je to, že systém ověřování systému Windows neposkytuje deklaraci identity "identifikátor názvu", kterou Signal používá k určení uživatelského jména.</span><span class="sxs-lookup"><span data-stu-id="d2169-142">This is because the Windows authentication system doesn't provide the "Name Identifier" claim that SignalR uses to determine the user name.</span></span>

<span data-ttu-id="d2169-143">Přidejte novou třídu, která implementuje `IUserIdProvider` a načte jednu z deklarací identity od uživatele, která se má použít jako identifikátor.</span><span class="sxs-lookup"><span data-stu-id="d2169-143">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="d2169-144">Pokud například chcete použít deklaraci identity (která je uživatelské jméno systému Windows ve formuláři `[Domain]\[Username]`), vytvořte následující třídu:</span><span class="sxs-lookup"><span data-stu-id="d2169-144">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="d2169-145">Místo toho můžete použít libovolnou hodnotu `User` z (například identifikátor SID systému Windows atd.). `ClaimTypes.Name`</span><span class="sxs-lookup"><span data-stu-id="d2169-145">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="d2169-146">Hodnota, kterou zvolíte, musí být jedinečná mezi všemi uživateli v systému.</span><span class="sxs-lookup"><span data-stu-id="d2169-146">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="d2169-147">Jinak může zpráva určená pro jednoho uživatele skončit jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="d2169-147">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="d2169-148">Zaregistrujte tuto komponentu `Startup.ConfigureServices` v metodě.</span><span class="sxs-lookup"><span data-stu-id="d2169-148">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="d2169-149">V klientovi .NET musí být povoleno ověřování systému Windows nastavením vlastnosti [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :</span><span class="sxs-lookup"><span data-stu-id="d2169-149">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="d2169-150">Ověřování systému Windows je podporováno pouze klientem prohlížeče při použití aplikace Microsoft Internet Explorer nebo Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="d2169-150">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="d2169-151">Přizpůsobení manipulace identity pomocí deklarací identity</span><span class="sxs-lookup"><span data-stu-id="d2169-151">Use claims to customize identity handling</span></span>

<span data-ttu-id="d2169-152">Aplikace, která ověřuje uživatele, může odvodit ID uživatelů signálu z deklarací identity uživatelů.</span><span class="sxs-lookup"><span data-stu-id="d2169-152">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="d2169-153">Chcete-li určit, jak má Signal vytvořit ID `IUserIdProvider` uživatelů, implementujte a zaregistrujte implementaci.</span><span class="sxs-lookup"><span data-stu-id="d2169-153">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="d2169-154">Vzorový kód ukazuje, jak byste měli pomocí deklarací identity vybrat e-mailovou adresu uživatele jako identifikační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="d2169-154">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="d2169-155">Hodnota, kterou zvolíte, musí být jedinečná mezi všemi uživateli v systému.</span><span class="sxs-lookup"><span data-stu-id="d2169-155">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="d2169-156">Jinak může zpráva určená pro jednoho uživatele skončit jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="d2169-156">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="d2169-157">Registrace účtu přidá deklaraci identity s typem `ClaimsTypes.Email` do databáze identity ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="d2169-157">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="d2169-158">Zaregistrujte tuto součást `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="d2169-158">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="d2169-159">Autorizace uživatelů pro přístup k centrům a metodám centra</span><span class="sxs-lookup"><span data-stu-id="d2169-159">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="d2169-160">Ve výchozím nastavení mohou být všechny metody v centru volány neověřeným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="d2169-160">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="d2169-161">Aby bylo možné vyžadovat ověření, použijte atribut [autorizovat](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na hub:</span><span class="sxs-lookup"><span data-stu-id="d2169-161">In order to require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="d2169-162">Pomocí argumentů konstruktoru a vlastností `[Authorize]` atributu můžete omezit přístup jenom na uživatele, kteří odpovídají na konkrétní [zásady autorizace](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="d2169-162">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="d2169-163">Pokud máte například vlastní zásadu `MyAuthorizationPolicy` autorizace, můžete zajistit, že k centru budou mít přístup jenom uživatelé, kteří mají odpovídající zásady, a to pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="d2169-163">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="d2169-164">Jednotlivé metody rozbočovače mohou mít `[Authorize]` také použit atribut.</span><span class="sxs-lookup"><span data-stu-id="d2169-164">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="d2169-165">Pokud aktuální uživatel neodpovídá zásadám použitým pro metodu, je volajícímu vrácena chyba:</span><span class="sxs-lookup"><span data-stu-id="d2169-165">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="d2169-166">Přizpůsobení autorizace metody centra pomocí obslužných rutin autorizace</span><span class="sxs-lookup"><span data-stu-id="d2169-166">Use authorization handlers to customize hub method authorization</span></span>

<span data-ttu-id="d2169-167">Signál poskytuje vlastní prostředek pro obslužné rutiny autorizace, pokud metoda rozbočovače vyžaduje autorizaci.</span><span class="sxs-lookup"><span data-stu-id="d2169-167">SignalR provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="d2169-168">Prostředek je instancí `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="d2169-168">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="d2169-169">`HubInvocationContext` Zahrnuje,názevvyvolanémetodycentra`HubCallerContext`a argumenty metody hub.</span><span class="sxs-lookup"><span data-stu-id="d2169-169">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="d2169-170">Vezměte v úvahu příklad chatovací místnosti umožňující přihlášení více organizací prostřednictvím Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="d2169-170">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="d2169-171">Kdokoli s účet Microsoft se může přihlásit k chatu, ale jenom členové vlastnící organizace by měli být schopni zakázat uživatelům nebo zobrazit historie chatu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="d2169-171">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="d2169-172">Kromě toho můžeme chtít omezit určité funkce od určitých uživatelů.</span><span class="sxs-lookup"><span data-stu-id="d2169-172">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="d2169-173">Používání aktualizovaných funkcí v ASP.NET Core 3,0 je zcela možné.</span><span class="sxs-lookup"><span data-stu-id="d2169-173">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="d2169-174">Všimněte si, `DomainRestrictedRequirement` jak funguje jako vlastní `IAuthorizationRequirement`.</span><span class="sxs-lookup"><span data-stu-id="d2169-174">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="d2169-175">Teď, `HubInvocationContext` když se předává parametr prostředků, interní logika může zkontrolovat kontext, ve kterém se centrum volá, a rozhodnout, že uživatel může provádět jednotlivé metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="d2169-175">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="d2169-176">V `Startup.ConfigureServices`přidejte novou zásadu a jako parametr vytvořte `DomainRestricted` vlastní `DomainRestrictedRequirement` požadavek.</span><span class="sxs-lookup"><span data-stu-id="d2169-176">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="d2169-177">V předchozím příkladu `DomainRestrictedRequirement` je `IAuthorizationRequirement` třída a její vlastní `AuthorizationHandler` pro tento požadavek.</span><span class="sxs-lookup"><span data-stu-id="d2169-177">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="d2169-178">Je přijatelné rozdělit tyto dvě komponenty na samostatné třídy, aby se oddělily obavy.</span><span class="sxs-lookup"><span data-stu-id="d2169-178">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="d2169-179">Výhodou příkladu přístupu není nutné `AuthorizationHandler` vkládat při spuštění, protože požadavek a obslužná rutina jsou stejné.</span><span class="sxs-lookup"><span data-stu-id="d2169-179">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d2169-180">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d2169-180">Additional resources</span></span>

* [<span data-ttu-id="d2169-181">Ověřování nosných tokenů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2169-181">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="d2169-182">Ověřování na základě prostředků</span><span class="sxs-lookup"><span data-stu-id="d2169-182">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)

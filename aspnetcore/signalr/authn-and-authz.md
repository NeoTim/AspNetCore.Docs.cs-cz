---
title: Ověřování a autorizace v knihovně SignalR technologie ASP.NET Core
author: bradygaster
description: Další informace o použití ověřování a autorizace v knihovně SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/09/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: e8f9dc48be780fb91bdec6ea4d579f5e4f16197b
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516944"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="9d18b-103">Ověřování a autorizace v knihovně SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9d18b-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="9d18b-104">Podle [Andrew Stanton sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="9d18b-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="9d18b-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="9d18b-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="9d18b-106">Ověření uživatelé připojení k rozbočovači SignalR</span><span class="sxs-lookup"><span data-stu-id="9d18b-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="9d18b-107">SignalR je možné s [ověřování ASP.NET Core](xref:security/authentication/identity) Chcete-li přidružit uživatele ke každé připojení.</span><span class="sxs-lookup"><span data-stu-id="9d18b-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="9d18b-108">V rozbočovači, ověřovacích dat je přístupný z [ `HubConnectionContext.User` ](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) vlastnost.</span><span class="sxs-lookup"><span data-stu-id="9d18b-108">In a hub, authentication data can be accessed from the [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="9d18b-109">Ověřování umožňuje IOT hub a volat metody pro všechna připojení, které jsou spojeny s konkrétním uživatelem (viz [spravovat uživatele a skupiny v knihovně SignalR](xref:signalr/groups) Další informace).</span><span class="sxs-lookup"><span data-stu-id="9d18b-109">Authentication allows the hub to call methods on all connections associated with a user (See [Manage users and groups in SignalR](xref:signalr/groups) for more information).</span></span> <span data-ttu-id="9d18b-110">Několik připojení může být spojen s jedním uživatelem.</span><span class="sxs-lookup"><span data-stu-id="9d18b-110">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="9d18b-111">Následuje příklad `Startup.Configure` využívající SignalR a ASP.NET Core ověřování:</span><span class="sxs-lookup"><span data-stu-id="9d18b-111">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="9d18b-112">Záleží na pořadí, ve kterém zaregistrujete ověřovací middleware SignalR a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9d18b-112">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="9d18b-113">Vždy volat `UseAuthentication` před `UseSignalR` tak, aby SignalR má uživatel na `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="9d18b-113">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

### <a name="cookie-authentication"></a><span data-ttu-id="9d18b-114">Ověřování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="9d18b-114">Cookie authentication</span></span>

<span data-ttu-id="9d18b-115">V aplikaci založené na prohlížeči umožňuje ověřování souborů cookie své stávající přihlašovací údaje k automaticky směrovat do připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="9d18b-115">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="9d18b-116">Při používání prohlížeče klienta, je potřeba žádná další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9d18b-116">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="9d18b-117">Pokud je uživatel přihlášen do vaší aplikace, připojení SignalR automaticky zdědí toto ověřování.</span><span class="sxs-lookup"><span data-stu-id="9d18b-117">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="9d18b-118">Soubory cookie jsou specifické pro prohlížeč tak, aby odesílal přístupové tokeny, ale můžete jim poslat neprohlížečových klientů.</span><span class="sxs-lookup"><span data-stu-id="9d18b-118">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="9d18b-119">Při použití [klienta .NET](xref:signalr/dotnet-client), `Cookies` vlastnost lze nastavit v `.WithUrl` volání s cílem poskytnout soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="9d18b-119">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call in order to provide a cookie.</span></span> <span data-ttu-id="9d18b-120">Ověřování souborem cookie z klienta .NET však vyžaduje aplikaci poskytují rozhraní API pro výměnu ověřovacích dat pro soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="9d18b-120">However, using cookie authentication from the .NET Client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="9d18b-121">Ověřování tokenu nosiče</span><span class="sxs-lookup"><span data-stu-id="9d18b-121">Bearer token authentication</span></span>

<span data-ttu-id="9d18b-122">Klient může poskytnout přístupový token namísto použití do souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="9d18b-122">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="9d18b-123">Server ověří token a použije ho k identifikaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="9d18b-123">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="9d18b-124">Toto ověření se provádí pouze v případě, že připojení existuje.</span><span class="sxs-lookup"><span data-stu-id="9d18b-124">This validation is done only when the connection is established.</span></span> <span data-ttu-id="9d18b-125">Během existence připojení serveru nebude znovu ověřit automaticky vyhledat token zrušení.</span><span class="sxs-lookup"><span data-stu-id="9d18b-125">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="9d18b-126">Na serveru, je nakonfigurován pomocí ověřování tokenu nosiče [middleware nosiče JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="9d18b-126">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="9d18b-127">V klientovi JavaScript token, který se dá zadat pomocí [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) možnost.</span><span class="sxs-lookup"><span data-stu-id="9d18b-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

<span data-ttu-id="9d18b-128">V klientovi .NET je podobná [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) vlastnost, která je možné nakonfigurovat token:</span><span class="sxs-lookup"><span data-stu-id="9d18b-128">In the .NET client, there is a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="9d18b-129">Funkce tokenu přístupu zadáte je volána před provedením **každý** HTTP žádost SignalR.</span><span class="sxs-lookup"><span data-stu-id="9d18b-129">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="9d18b-130">Pokud potřebujete k obnovení tokenu, aby bylo možné udržovat aktivní připojení (protože to může vypršet jejich platnost během připojení), tak učinit z v rámci této funkce a vrátí aktualizovaný token.</span><span class="sxs-lookup"><span data-stu-id="9d18b-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="9d18b-131">Standardní webové rozhraní API se odešlou nosné tokeny v hlavičce HTTP.</span><span class="sxs-lookup"><span data-stu-id="9d18b-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="9d18b-132">SignalR je však nelze nastavit tyto hlavičky v prohlížečích, při použití některých přenosů.</span><span class="sxs-lookup"><span data-stu-id="9d18b-132">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="9d18b-133">Při použití Server-Sent události a protokoly Websocket, se přenášejí token jako parametr řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="9d18b-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="9d18b-134">Aby bylo možné na serveru z toho důvodu je vyžadována další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="9d18b-134">In order to support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="9d18b-135">Soubory cookie vs. nosné tokeny</span><span class="sxs-lookup"><span data-stu-id="9d18b-135">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="9d18b-136">Protože soubory cookie jsou specifické pro prohlížeče, odesílání z jiných typů klientů zvyšuje složitost ve srovnání s odesíláním nosné tokeny.</span><span class="sxs-lookup"><span data-stu-id="9d18b-136">Because cookies are specific to browsers, sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="9d18b-137">Z tohoto důvodu není ověřování souborů cookie nedoporučuje, pokud aplikace potřebuje pouze k ověření uživatelů z prohlížeče klienta.</span><span class="sxs-lookup"><span data-stu-id="9d18b-137">For this reason, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="9d18b-138">Ověřování tokenu nosiče je doporučený postup při používání klientů kromě prohlížeče klienta.</span><span class="sxs-lookup"><span data-stu-id="9d18b-138">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="9d18b-139">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="9d18b-139">Windows authentication</span></span>

<span data-ttu-id="9d18b-140">Pokud [ověřování Windows](xref:security/authentication/windowsauth) je nakonfigurovaná funkce SignalR ve vaší aplikaci, můžete použít tuto identitu zabezpečit rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="9d18b-140">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="9d18b-141">Však aby bylo možné odesílat zprávy pro jednotlivé uživatele, je nutné přidat vlastní uživatelské ID zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="9d18b-141">However, in order to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="9d18b-142">Je to proto, že ověřování systému Windows neposkytuje "Identifikátor názvu" deklarace identity, která používá funkci SignalR k určení uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="9d18b-142">This is because the Windows authentication system doesn't provide the "Name Identifier" claim that SignalR uses to determine the user name.</span></span>

<span data-ttu-id="9d18b-143">Přidejte novou třídu, která implementuje `IUserIdProvider` a načítat je jedna z deklarací z uživatele, který chcete použít jako identifikátor.</span><span class="sxs-lookup"><span data-stu-id="9d18b-143">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="9d18b-144">Chcete-li například použít deklarace identity "Name" (což je uživatelské jméno Windows ve formuláři `[Domain]\[Username]`), vytvořte následující třídy:</span><span class="sxs-lookup"><span data-stu-id="9d18b-144">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="9d18b-145">Spíše než `ClaimTypes.Name`, můžete použít libovolnou hodnotu od `User` (jako je například identifikátoru Windows SID atd.).</span><span class="sxs-lookup"><span data-stu-id="9d18b-145">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="9d18b-146">Hodnota, kterou zvolíte, musí být jedinečný mezi všemi uživateli ve vašem systému.</span><span class="sxs-lookup"><span data-stu-id="9d18b-146">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="9d18b-147">Zpráva určená pro jednoho uživatele, jinak můžou být nakonec přejdete na jiného uživatele.</span><span class="sxs-lookup"><span data-stu-id="9d18b-147">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="9d18b-148">Zaregistrujte tuto součást ve vaší `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="9d18b-148">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="9d18b-149">V rozhraní .NET pro klienta, musí být povoleno ověřování Windows tak, že nastavíte [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) vlastnost:</span><span class="sxs-lookup"><span data-stu-id="9d18b-149">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="9d18b-150">Ověřování Windows je podporována pouze podle klientského prohlížeče, pokud používáte Microsoft Internet Explorer nebo Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="9d18b-150">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="9d18b-151">Použití deklarací identity k přizpůsobení identity zpracování</span><span class="sxs-lookup"><span data-stu-id="9d18b-151">Use claims to customize identity handling</span></span>

<span data-ttu-id="9d18b-152">Aplikace, která ověřuje uživatele lze odvodit z deklarací identity uživatelů ID uživatelů SignalR.</span><span class="sxs-lookup"><span data-stu-id="9d18b-152">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="9d18b-153">Chcete-li určit způsob, jak vytvořit funkci SignalR ID uživatelů, implementovat `IUserIdProvider` a zaregistrujte implementace.</span><span class="sxs-lookup"><span data-stu-id="9d18b-153">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="9d18b-154">Vzorový kód ukazuje, jak by používat deklarace identity vyberte uživatele e-mailovou adresu jako identifikační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="9d18b-154">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="9d18b-155">Hodnota, kterou zvolíte, musí být jedinečný mezi všemi uživateli ve vašem systému.</span><span class="sxs-lookup"><span data-stu-id="9d18b-155">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="9d18b-156">Zpráva určená pro jednoho uživatele, jinak můžou být nakonec přejdete na jiného uživatele.</span><span class="sxs-lookup"><span data-stu-id="9d18b-156">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="9d18b-157">Registrace účtu přidá deklaraci identity s typem `ClaimsTypes.Email` k databázi technologie ASP.NET identity.</span><span class="sxs-lookup"><span data-stu-id="9d18b-157">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="9d18b-158">Zaregistrujte tuto součást ve vaší `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9d18b-158">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="9d18b-159">Povolit uživatelům přístup rozbočovače a metody</span><span class="sxs-lookup"><span data-stu-id="9d18b-159">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="9d18b-160">Ve výchozím nastavení může být volán všechny metody v rozbočovači neověřený uživatel.</span><span class="sxs-lookup"><span data-stu-id="9d18b-160">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="9d18b-161">Abyste mohli vyžadovat ověřování, použijte [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) atribut k rozbočovači:</span><span class="sxs-lookup"><span data-stu-id="9d18b-161">In order to require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="9d18b-162">Můžete použít argumenty konstruktoru a vlastnosti `[Authorize]` atribut omezují přístup pouze uživatelům odpovídající konkrétní [zásad autorizace](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="9d18b-162">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="9d18b-163">Například, pokud máte vlastní zásady autorizace volá `MyAuthorizationPolicy` můžete zajistit, že přístup pouze uživatelé odpovídající této zásadě centra pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="9d18b-163">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

<span data-ttu-id="9d18b-164">Může mít metod rozbočovače na jednotlivé `[Authorize]` i atribut.</span><span class="sxs-lookup"><span data-stu-id="9d18b-164">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="9d18b-165">Pokud má aktuální uživatel neodpovídá zásady použité na metodu, volající vrátí chybu:</span><span class="sxs-lookup"><span data-stu-id="9d18b-165">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub: Hub
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

## <a name="additional-resources"></a><span data-ttu-id="9d18b-166">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9d18b-166">Additional resources</span></span>

* [<span data-ttu-id="9d18b-167">Ověřování tokenu nosiče v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9d18b-167">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)

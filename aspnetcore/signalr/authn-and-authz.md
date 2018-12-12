---
title: Ověřování a autorizace v knihovně SignalR technologie ASP.NET Core
author: tdykstra
description: Další informace o použití ověřování a autorizace v knihovně SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 06/29/2018
uid: signalr/authn-and-authz
ms.openlocfilehash: 6624b2b4361eb0a5bb6e45108878ee6fe5ecb443
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284410"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="c852f-103">Ověřování a autorizace v knihovně SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c852f-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="c852f-104">Podle [Andrew Stanton sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="c852f-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="c852f-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c852f-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="c852f-106">Ověření uživatelé připojení k rozbočovači SignalR</span><span class="sxs-lookup"><span data-stu-id="c852f-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="c852f-107">SignalR je možné s [ověřování ASP.NET Core](xref:security/authentication/identity) Chcete-li přidružit uživatele ke každé připojení.</span><span class="sxs-lookup"><span data-stu-id="c852f-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="c852f-108">V rozbočovači, ověřovacích dat je přístupný z [ `HubConnectionContext.User` ](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) vlastnost.</span><span class="sxs-lookup"><span data-stu-id="c852f-108">In a hub, authentication data can be accessed from the [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="c852f-109">Ověřování umožňuje IOT hub a volat metody pro všechna připojení, které jsou spojeny s konkrétním uživatelem (viz [spravovat uživatele a skupiny v knihovně SignalR](xref:signalr/groups) Další informace).</span><span class="sxs-lookup"><span data-stu-id="c852f-109">Authentication allows the hub to call methods on all connections associated with a user (See [Manage users and groups in SignalR](xref:signalr/groups) for more information).</span></span> <span data-ttu-id="c852f-110">Několik připojení může být spojen s jedním uživatelem.</span><span class="sxs-lookup"><span data-stu-id="c852f-110">Multiple connections may be associated with a single user.</span></span>

### <a name="cookie-authentication"></a><span data-ttu-id="c852f-111">Ověřování souborů cookie</span><span class="sxs-lookup"><span data-stu-id="c852f-111">Cookie authentication</span></span>

<span data-ttu-id="c852f-112">V aplikaci založené na prohlížeči umožňuje ověřování souborů cookie své stávající přihlašovací údaje k automaticky směrovat do připojení SignalR.</span><span class="sxs-lookup"><span data-stu-id="c852f-112">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="c852f-113">Při používání prohlížeče klienta, je potřeba žádná další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="c852f-113">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="c852f-114">Pokud je uživatel přihlášen do vaší aplikace, připojení SignalR automaticky zdědí toto ověřování.</span><span class="sxs-lookup"><span data-stu-id="c852f-114">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="c852f-115">Soubory cookie jsou specifické pro prohlížeč tak, aby odesílal přístupové tokeny, ale můžete jim poslat neprohlížečových klientů.</span><span class="sxs-lookup"><span data-stu-id="c852f-115">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="c852f-116">Při použití [klienta .NET](xref:signalr/dotnet-client), `Cookies` vlastnost lze nastavit v `.WithUrl` volání s cílem poskytnout soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="c852f-116">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call in order to provide a cookie.</span></span> <span data-ttu-id="c852f-117">Ověřování souborem cookie z klienta .NET však vyžaduje aplikaci poskytují rozhraní API pro výměnu ověřovacích dat pro soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="c852f-117">However, using cookie authentication from the .NET Client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="c852f-118">Ověřování tokenu nosiče</span><span class="sxs-lookup"><span data-stu-id="c852f-118">Bearer token authentication</span></span>

<span data-ttu-id="c852f-119">Klient může poskytnout přístupový token namísto použití do souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="c852f-119">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="c852f-120">Server ověří token a použije ho k identifikaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="c852f-120">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="c852f-121">Toto ověření se provádí pouze v případě, že připojení existuje.</span><span class="sxs-lookup"><span data-stu-id="c852f-121">This validation is done only when the connection is established.</span></span> <span data-ttu-id="c852f-122">Během existence připojení serveru nebude znovu ověřit automaticky vyhledat token zrušení.</span><span class="sxs-lookup"><span data-stu-id="c852f-122">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="c852f-123">Na serveru, je nakonfigurován pomocí ověřování tokenu nosiče [middleware nosiče JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="c852f-123">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="c852f-124">V klientovi JavaScript token, který se dá zadat pomocí [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) možnost.</span><span class="sxs-lookup"><span data-stu-id="c852f-124">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

<span data-ttu-id="c852f-125">V klientovi .NET je podobná [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) vlastnost, která je možné nakonfigurovat token:</span><span class="sxs-lookup"><span data-stu-id="c852f-125">In the .NET client, there is a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="c852f-126">Funkce tokenu přístupu zadáte je volána před provedením **každý** HTTP žádost SignalR.</span><span class="sxs-lookup"><span data-stu-id="c852f-126">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="c852f-127">Pokud potřebujete k obnovení tokenu, aby bylo možné udržovat aktivní připojení (protože to může vypršet jejich platnost během připojení), tak učinit z v rámci této funkce a vrátí aktualizovaný token.</span><span class="sxs-lookup"><span data-stu-id="c852f-127">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="c852f-128">Standardní webové rozhraní API se odešlou nosné tokeny v hlavičce HTTP.</span><span class="sxs-lookup"><span data-stu-id="c852f-128">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="c852f-129">SignalR je však nelze nastavit tyto hlavičky v prohlížečích, při použití některých přenosů.</span><span class="sxs-lookup"><span data-stu-id="c852f-129">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="c852f-130">Při použití Server-Sent události a protokoly Websocket, se přenášejí token jako parametr řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="c852f-130">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="c852f-131">Aby bylo možné na serveru z toho důvodu je vyžadována další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="c852f-131">In order to support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="c852f-132">Soubory cookie vs. nosné tokeny</span><span class="sxs-lookup"><span data-stu-id="c852f-132">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="c852f-133">Protože soubory cookie jsou specifické pro prohlížeče, odesílání z jiných typů klientů zvyšuje složitost ve srovnání s odesíláním nosné tokeny.</span><span class="sxs-lookup"><span data-stu-id="c852f-133">Because cookies are specific to browsers, sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="c852f-134">Z tohoto důvodu není ověřování souborů cookie nedoporučuje, pokud aplikace potřebuje pouze k ověření uživatelů z prohlížeče klienta.</span><span class="sxs-lookup"><span data-stu-id="c852f-134">For this reason, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="c852f-135">Ověřování tokenu nosiče je doporučený postup při používání klientů kromě prohlížeče klienta.</span><span class="sxs-lookup"><span data-stu-id="c852f-135">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="c852f-136">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="c852f-136">Windows authentication</span></span>

<span data-ttu-id="c852f-137">Pokud [ověřování Windows](xref:security/authentication/windowsauth) je nakonfigurovaná funkce SignalR ve vaší aplikaci, můžete použít tuto identitu zabezpečit rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="c852f-137">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="c852f-138">Však aby bylo možné odesílat zprávy pro jednotlivé uživatele, je nutné přidat vlastní uživatelské ID zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="c852f-138">However, in order to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="c852f-139">Je to proto, že ověřování systému Windows neposkytuje "Identifikátor názvu" deklarace identity, která používá funkci SignalR k určení uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="c852f-139">This is because the Windows authentication system doesn't provide the "Name Identifier" claim that SignalR uses to determine the user name.</span></span>

<span data-ttu-id="c852f-140">Přidejte novou třídu, která implementuje `IUserIdProvider` a načítat je jedna z deklarací z uživatele, který chcete použít jako identifikátor.</span><span class="sxs-lookup"><span data-stu-id="c852f-140">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="c852f-141">Chcete-li například použít deklarace identity "Name" (což je uživatelské jméno Windows ve formuláři `[Domain]\[Username]`), vytvořte následující třídy:</span><span class="sxs-lookup"><span data-stu-id="c852f-141">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

```csharp
public class NameUserIdProvider : IUserIdProvider
{
    public string GetUserId(HubConnectionContext connection)
    {
        return connection.User?.FindFirst(ClaimTypes.Name)?.Value;
    }
}
```

<span data-ttu-id="c852f-142">Spíše než `ClaimTypes.Name`, můžete použít libovolnou hodnotu od `User` (jako je například identifikátoru Windows SID atd.).</span><span class="sxs-lookup"><span data-stu-id="c852f-142">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="c852f-143">Hodnota, kterou zvolíte, musí být jedinečný mezi všemi uživateli ve vašem systému.</span><span class="sxs-lookup"><span data-stu-id="c852f-143">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="c852f-144">Zpráva určená pro jednoho uživatele, jinak můžou být nakonec přejdete na jiného uživatele.</span><span class="sxs-lookup"><span data-stu-id="c852f-144">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="c852f-145">Zaregistrujte tuto součást v vaše `Startup.ConfigureServices` metoda **po** volání `.AddSignalR`</span><span class="sxs-lookup"><span data-stu-id="c852f-145">Register this component in your `Startup.ConfigureServices` method **after** the call to `.AddSignalR`</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="c852f-146">V rozhraní .NET pro klienta, musí být povoleno ověřování Windows tak, že nastavíte [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) vlastnost:</span><span class="sxs-lookup"><span data-stu-id="c852f-146">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="c852f-147">Ověřování Windows je podporována pouze podle klientského prohlížeče, pokud používáte Microsoft Internet Explorer nebo Microsoft Edge.</span><span class="sxs-lookup"><span data-stu-id="c852f-147">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="c852f-148">Povolit uživatelům přístup rozbočovače a metody</span><span class="sxs-lookup"><span data-stu-id="c852f-148">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="c852f-149">Ve výchozím nastavení může být volán všechny metody v rozbočovači neověřený uživatel.</span><span class="sxs-lookup"><span data-stu-id="c852f-149">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="c852f-150">Abyste mohli vyžadovat ověřování, použijte [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) atribut k rozbočovači:</span><span class="sxs-lookup"><span data-stu-id="c852f-150">In order to require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="c852f-151">Můžete použít argumenty konstruktoru a vlastnosti `[Authorize]` atribut omezují přístup pouze uživatelům odpovídající konkrétní [zásad autorizace](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="c852f-151">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="c852f-152">Například, pokud máte vlastní zásady autorizace volá `MyAuthorizationPolicy` můžete zajistit, že přístup pouze uživatelé odpovídající této zásadě centra pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="c852f-152">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

<span data-ttu-id="c852f-153">Může mít metod rozbočovače na jednotlivé `[Authorize]` i atribut.</span><span class="sxs-lookup"><span data-stu-id="c852f-153">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="c852f-154">Pokud má aktuální uživatel neodpovídá zásady použité na metodu, volající vrátí chybu:</span><span class="sxs-lookup"><span data-stu-id="c852f-154">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c852f-155">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c852f-155">Additional resources</span></span>

* [<span data-ttu-id="c852f-156">Ověřování tokenu nosiče v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c852f-156">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)

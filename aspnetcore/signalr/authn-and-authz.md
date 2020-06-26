---
title: Ověřování a autorizace v ASP.NET CoreSignalR
author: bradygaster
description: Naučte se používat ověřování a autorizaci v ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 794465ceb69f47ee3d5cc8c100b321cb958d9cfe
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407132"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="5c8f0-103">Ověřování a autorizace v ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="5c8f0-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="5c8f0-104">Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="5c8f0-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="5c8f0-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5c8f0-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="5c8f0-106">Ověřování uživatelů připojujících se k SignalR centru</span><span class="sxs-lookup"><span data-stu-id="5c8f0-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="5c8f0-107">dá se použít s [ověřováním ASP.NET Core](xref:security/authentication/identity) k přidružení uživatele k jednotlivým připojením.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="5c8f0-108">V centru můžete k datům ověřování přicházet z vlastnosti [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) .</span><span class="sxs-lookup"><span data-stu-id="5c8f0-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="5c8f0-109">Ověřování umožňuje centru volat metody u všech připojení přidružených k uživateli.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="5c8f0-110">Další informace najdete v tématu [Správa uživatelů a skupin v SignalR ](xref:signalr/groups)nástroji.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="5c8f0-111">K jednomu uživateli může být přidruženo více připojení.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="5c8f0-112">Následuje příklad, `Startup.Configure` který používá SignalR a ASP.NET Core ověřování:</span><span class="sxs-lookup"><span data-stu-id="5c8f0-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

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
> <span data-ttu-id="5c8f0-113">Pořadí, ve kterém zaregistrujete SignalR middleware a ASP.NET Core v oblasti ověřování.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="5c8f0-114">Vždy volejte `UseAuthentication` před tím `UseSignalR` , aby SignalR měl uživatel na `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="5c8f0-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="5c8f0-115">Ověřování souborem cookie</span><span class="sxs-lookup"><span data-stu-id="5c8f0-115">Cookie authentication</span></span>

<span data-ttu-id="5c8f0-116">V aplikaci založené na prohlížeči umožňuje ověřování pomocí souborů cookie, aby vaše stávající uživatelská pověření automaticky pokračovala v SignalR připojeních.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="5c8f0-117">Při použití klienta prohlížeče není nutná žádná další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="5c8f0-118">Pokud je uživatel přihlášený do vaší aplikace, SignalR připojení Toto ověřování automaticky zdědí.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="5c8f0-119">Soubory cookie jsou způsoby, jak odesílat přístupové tokeny, ale klienti bez prohlížeče je mohou odeslat.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="5c8f0-120">Při použití [klienta .NET](xref:signalr/dotnet-client) `Cookies` lze vlastnost nakonfigurovat ve `.WithUrl` volání za účelem poskytnutí souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="5c8f0-121">Použití ověřování souborem cookie z klienta .NET ale vyžaduje, aby aplikace poskytovala rozhraní API pro výměnu ověřovacích dat pro soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="5c8f0-122">Ověřování nosných tokenů</span><span class="sxs-lookup"><span data-stu-id="5c8f0-122">Bearer token authentication</span></span>

<span data-ttu-id="5c8f0-123">Klient může místo použití souboru cookie zadat přístupový token.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="5c8f0-124">Server token ověří a použije ho k identifikaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="5c8f0-125">Toto ověření se provádí jenom v případě, že se naváže připojení.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="5c8f0-126">Během života připojení se server automaticky znovu neověřuje, aby zkontroloval odvolání tokenu.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="5c8f0-127">Na serveru je ověřování pomocí tokenu nosiče nakonfigurované pomocí [middleware nosiče JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="5c8f0-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="5c8f0-128">V klientovi JavaScriptu lze token zadat pomocí možnosti [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .</span><span class="sxs-lookup"><span data-stu-id="5c8f0-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="5c8f0-129">V klientovi .NET existuje podobná vlastnost [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) , kterou lze použít ke konfiguraci tokenu:</span><span class="sxs-lookup"><span data-stu-id="5c8f0-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="5c8f0-130">Funkce přístupového tokenu, kterou poskytnete, je volána před **všemi** požadavky HTTP provedenými nástrojem SignalR .</span><span class="sxs-lookup"><span data-stu-id="5c8f0-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="5c8f0-131">Pokud potřebujete obnovit token, abyste zachovali aktivní připojení (protože může vypršet platnost připojení), udělejte to v rámci této funkce a vraťte aktualizovaný token.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="5c8f0-132">V případě standardních webových rozhraní API se tokeny nosiče odesílají v hlavičce protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="5c8f0-133">SignalRTato záhlaví ale v prohlížečích není možné nastavit při použití některých přenosů.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="5c8f0-134">Při použití protokolu WebSockets a událostí odesílaných serverem se token přenáší jako parametr řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="5c8f0-135">Pro podporu tohoto serveru je potřeba další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="5c8f0-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="5c8f0-136">Řetězec dotazu se používá v prohlížečích při připojování k objektům WebSockets a událostem odesílaným serverem kvůli omezením rozhraní API prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="5c8f0-137">Při použití protokolu HTTPS jsou hodnoty řetězce dotazu zabezpečené připojením TLS.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="5c8f0-138">Mnoho serverů ale protokoluje hodnoty řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-138">However, many servers log query string values.</span></span> <span data-ttu-id="5c8f0-139">Další informace najdete v tématu [požadavky na zabezpečení v SignalR ASP.NET Core ](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="5c8f0-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="5c8f0-140">pomocí hlaviček odesílá tokeny v prostředích, které je podporují (například klienti .NET a Java).</span><span class="sxs-lookup"><span data-stu-id="5c8f0-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="5c8f0-141">Soubory cookie vs. nosných tokenů</span><span class="sxs-lookup"><span data-stu-id="5c8f0-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="5c8f0-142">Soubory cookie jsou specifické pro prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="5c8f0-143">Posílání z jiných druhů klientů přináší v porovnání s posíláním nosných tokenů složitost.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="5c8f0-144">V důsledku toho se ověřování souborů cookie nedoporučuje, pokud aplikace potřebuje jenom ověřovat uživatele z klienta prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="5c8f0-145">Ověřování nosných tokenů je doporučený postup při použití jiných klientů, než je klient prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="5c8f0-146">Ověřování systému Windows</span><span class="sxs-lookup"><span data-stu-id="5c8f0-146">Windows authentication</span></span>

<span data-ttu-id="5c8f0-147">Pokud je ve vaší aplikaci nakonfigurované [ověřování systému Windows](xref:security/authentication/windowsauth) , SignalR může tuto identitu použít k zabezpečení rozbočovačů.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="5c8f0-148">Chcete-li však odesílat zprávy jednotlivým uživatelům, je nutné přidat vlastního poskytovatele ID uživatele.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="5c8f0-149">Systém ověřování systému Windows neposkytuje deklaraci identity "identifikátor názvu".</span><span class="sxs-lookup"><span data-stu-id="5c8f0-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="5c8f0-150">pomocí deklarace identity určí uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="5c8f0-151">Přidejte novou třídu, která implementuje `IUserIdProvider` a načte jednu z deklarací identity od uživatele, která se má použít jako identifikátor.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="5c8f0-152">Pokud například chcete použít deklaraci identity (která je uživatelské jméno systému Windows ve formuláři `[Domain]\[Username]` ), vytvořte následující třídu:</span><span class="sxs-lookup"><span data-stu-id="5c8f0-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="5c8f0-153">Místo `ClaimTypes.Name` toho můžete použít libovolnou hodnotu z rozhraní `User` (například identifikátor SID systému Windows atd.).</span><span class="sxs-lookup"><span data-stu-id="5c8f0-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="5c8f0-154">Hodnota, kterou zvolíte, musí být jedinečná mezi všemi uživateli v systému.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="5c8f0-155">Jinak může zpráva určená pro jednoho uživatele skončit jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="5c8f0-156">Zaregistrujte tuto komponentu v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="5c8f0-157">V klientovi .NET musí být povoleno ověřování systému Windows nastavením vlastnosti [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :</span><span class="sxs-lookup"><span data-stu-id="5c8f0-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="5c8f0-158">Ověřování systému Windows je podporováno v aplikacích Internet Explorer a Microsoft Edge, ale ne ve všech prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-158">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="5c8f0-159">Například v Chrome a Safari se nezdařil pokus o použití ověřování systému Windows a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-159">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="5c8f0-160">Když se ověřování systému Windows nepovede, klient se pokusí přejít na jiné přenosy, které by mohly fungovat.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-160">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="5c8f0-161">Přizpůsobení manipulace identity pomocí deklarací identity</span><span class="sxs-lookup"><span data-stu-id="5c8f0-161">Use claims to customize identity handling</span></span>

<span data-ttu-id="5c8f0-162">Aplikace, která ověřuje uživatele, může odvodit SignalR ID uživatelů od deklarací identity uživatelů.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-162">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="5c8f0-163">Chcete-li určit SignalR , jak vytvoří ID uživatele, implementujte `IUserIdProvider` a zaregistrujte implementaci.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-163">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="5c8f0-164">Vzorový kód ukazuje, jak byste měli pomocí deklarací identity vybrat e-mailovou adresu uživatele jako identifikační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-164">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="5c8f0-165">Hodnota, kterou zvolíte, musí být jedinečná mezi všemi uživateli v systému.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-165">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="5c8f0-166">Jinak může zpráva určená pro jednoho uživatele skončit jiným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-166">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="5c8f0-167">Registrace účtu přidá deklaraci identity s typem `ClaimsTypes.Email` do databáze identity ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-167">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="5c8f0-168">Zaregistrujte tuto součást v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5c8f0-168">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="5c8f0-169">Autorizace uživatelů pro přístup k centrům a metodám centra</span><span class="sxs-lookup"><span data-stu-id="5c8f0-169">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="5c8f0-170">Ve výchozím nastavení mohou být všechny metody v centru volány neověřeným uživatelem.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-170">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="5c8f0-171">Chcete-li vyžadovat ověření, použijte atribut [autorizovat](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na centrum:</span><span class="sxs-lookup"><span data-stu-id="5c8f0-171">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="5c8f0-172">Pomocí argumentů konstruktoru a vlastností `[Authorize]` atributu můžete omezit přístup jenom na uživatele, kteří odpovídají na konkrétní [zásady autorizace](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="5c8f0-172">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="5c8f0-173">Pokud máte například vlastní zásadu autorizace, `MyAuthorizationPolicy` můžete zajistit, že k centru budou mít přístup jenom uživatelé, kteří mají odpovídající zásady, a to pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5c8f0-173">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="5c8f0-174">Jednotlivé metody rozbočovače mohou mít `[Authorize]` také použit atribut.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-174">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="5c8f0-175">Pokud aktuální uživatel neodpovídá zásadám použitým pro metodu, je volajícímu vrácena chyba:</span><span class="sxs-lookup"><span data-stu-id="5c8f0-175">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="5c8f0-176">Přizpůsobení autorizace metody centra pomocí obslužných rutin autorizace</span><span class="sxs-lookup"><span data-stu-id="5c8f0-176">Use authorization handlers to customize hub method authorization</span></span>

SignalR<span data-ttu-id="5c8f0-177">poskytuje vlastní prostředek pro obslužné rutiny autorizace, pokud metoda rozbočovače vyžaduje autorizaci.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-177"> provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="5c8f0-178">Prostředek je instancí `HubInvocationContext` .</span><span class="sxs-lookup"><span data-stu-id="5c8f0-178">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="5c8f0-179">`HubInvocationContext`Zahrnuje `HubCallerContext` , název vyvolané metody centra a argumenty metody hub.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-179">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="5c8f0-180">Vezměte v úvahu příklad chatovací místnosti umožňující přihlášení více organizací prostřednictvím Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-180">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="5c8f0-181">Kdokoli s účet Microsoft se může přihlásit k chatu, ale jenom členové vlastnící organizace by měli být schopni zakázat uživatelům nebo zobrazit historie chatu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-181">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="5c8f0-182">Kromě toho můžeme chtít omezit určité funkce od určitých uživatelů.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-182">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="5c8f0-183">Používání aktualizovaných funkcí v ASP.NET Core 3,0 je zcela možné.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-183">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="5c8f0-184">Všimněte si, jak `DomainRestrictedRequirement` funguje jako vlastní `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="5c8f0-184">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="5c8f0-185">Teď, když se `HubInvocationContext` předává parametr prostředků, interní logika může zkontrolovat kontext, ve kterém se centrum volá, a rozhodnout, že uživatel může provádět jednotlivé metody rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-185">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

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

<span data-ttu-id="5c8f0-186">V `Startup.ConfigureServices` přidejte novou zásadu a `DomainRestrictedRequirement` jako parametr vytvořte vlastní požadavek `DomainRestricted` .</span><span class="sxs-lookup"><span data-stu-id="5c8f0-186">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

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

<span data-ttu-id="5c8f0-187">V předchozím příkladu `DomainRestrictedRequirement` je třída `IAuthorizationRequirement` a její vlastní `AuthorizationHandler` pro tento požadavek.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-187">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="5c8f0-188">Je přijatelné rozdělit tyto dvě komponenty na samostatné třídy, aby se oddělily obavy.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-188">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="5c8f0-189">Výhodou příkladu přístupu není nutné vkládat `AuthorizationHandler` při spuštění, protože požadavek a obslužná rutina jsou stejné.</span><span class="sxs-lookup"><span data-stu-id="5c8f0-189">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5c8f0-190">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5c8f0-190">Additional resources</span></span>

* [<span data-ttu-id="5c8f0-191">Ověřování nosných tokenů v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c8f0-191">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="5c8f0-192">Ověřování na základě prostředků</span><span class="sxs-lookup"><span data-stu-id="5c8f0-192">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)

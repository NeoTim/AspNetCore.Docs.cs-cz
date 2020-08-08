---
title: Ověřování a autorizace v ASP.NET CoreSignalR
author: bradygaster
description: Naučte se používat ověřování a autorizaci v ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
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
uid: signalr/authn-and-authz
ms.openlocfilehash: 1e022c510dda3e39dd02d607f1d9c493aecdeb5a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021559"
---
# <a name="authentication-and-authorization-in-aspnet-core-no-locsignalr"></a>Ověřování a autorizace v ASP.NET CoreSignalR

Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-no-locsignalr-hub"></a>Ověřování uživatelů připojujících se k SignalR centru

SignalRdá se použít s [ověřováním ASP.NET Core](xref:security/authentication/identity) k přidružení uživatele k jednotlivým připojením. V centru můžete k datům ověřování přicházet z vlastnosti [HubConnectionContext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) . Ověřování umožňuje centru volat metody u všech připojení přidružených k uživateli. Další informace najdete v tématu [Správa uživatelů a skupin v SignalR ](xref:signalr/groups)nástroji. K jednomu uživateli může být přidruženo více připojení.

Následuje příklad, `Startup.Configure` který používá SignalR a ASP.NET Core ověřování:

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
> Pořadí, ve kterém zaregistrujete SignalR middleware a ASP.NET Core v oblasti ověřování. Vždy volejte `UseAuthentication` před tím `UseSignalR` , aby SignalR měl uživatel na `HttpContext` .

::: moniker-end

### <a name="no-loccookie-authentication"></a>Cookiepřihlašovací

V aplikaci založené na prohlížeči cookie ověřování umožňuje, aby vaše stávající uživatelská pověření automaticky pokračovala v SignalR připojeních. Při použití klienta prohlížeče není nutná žádná další konfigurace. Pokud je uživatel přihlášený do vaší aplikace, SignalR připojení Toto ověřování automaticky zdědí.

Cookies jsou způsoby, jak odesílat přístupové tokeny, ale klienti bez prohlížeče můžou je odeslat. Při použití [klienta .NET](xref:signalr/dotnet-client) `Cookies` lze vlastnost nakonfigurovat při `.WithUrl` volání metody pro poskytnutí cookie . Použití cookie ověřování od klienta .NET ale vyžaduje, aby aplikace poskytovala rozhraní API pro výměnu ověřovacích dat pro cookie .

### <a name="bearer-token-authentication"></a>Ověřování nosných tokenů

Klient může místo použití použít přístupový token cookie . Server token ověří a použije ho k identifikaci uživatele. Toto ověření se provádí jenom v případě, že se naváže připojení. Během života připojení se server automaticky znovu neověřuje, aby zkontroloval odvolání tokenu.

Na serveru je ověřování pomocí tokenu nosiče nakonfigurované pomocí [middleware nosiče JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

V klientovi JavaScriptu lze token zadat pomocí možnosti [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

V klientovi .NET existuje podobná vlastnost [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) , kterou lze použít ke konfiguraci tokenu:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Funkce přístupového tokenu, kterou poskytnete, je volána před **všemi** požadavky HTTP provedenými nástrojem SignalR . Pokud potřebujete obnovit token, abyste zachovali aktivní připojení (protože může vypršet platnost připojení), udělejte to v rámci této funkce a vraťte aktualizovaný token.

V případě standardních webových rozhraní API se tokeny nosiče odesílají v hlavičce protokolu HTTP. SignalRTato záhlaví ale v prohlížečích není možné nastavit při použití některých přenosů. Při použití protokolu WebSockets a událostí odesílaných serverem se token přenáší jako parametr řetězce dotazu. Pro podporu tohoto serveru je potřeba další konfigurace:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> Řetězec dotazu se používá v prohlížečích při připojování k objektům WebSockets a událostem odesílaným serverem kvůli omezením rozhraní API prohlížeče. Při použití protokolu HTTPS jsou hodnoty řetězce dotazu zabezpečené připojením TLS. Mnoho serverů ale protokoluje hodnoty řetězce dotazu. Další informace najdete v tématu [požadavky na zabezpečení v SignalR ASP.NET Core ](xref:signalr/security). SignalRpomocí hlaviček odesílá tokeny v prostředích, které je podporují (například klienti .NET a Java).

### <a name="no-loccookies-vs-bearer-tokens"></a>Cookies vs. nosnými tokeny 

Cookies jsou specifické pro prohlížeče. Posílání z jiných druhů klientů přináší v porovnání s posíláním nosných tokenů složitost. V důsledku toho se cookie ověřování nedoporučuje, pokud aplikace potřebuje jenom ověřovat uživatele z klienta prohlížeče. Ověřování nosných tokenů je doporučený postup při použití jiných klientů, než je klient prohlížeče.

### <a name="windows-authentication"></a>Ověřování systému Windows

Pokud je ve vaší aplikaci nakonfigurované [ověřování systému Windows](xref:security/authentication/windowsauth) , SignalR může tuto identitu použít k zabezpečení rozbočovačů. Chcete-li však odesílat zprávy jednotlivým uživatelům, je nutné přidat vlastního poskytovatele ID uživatele. Systém ověřování systému Windows neposkytuje deklaraci identity "identifikátor názvu". SignalRpomocí deklarace identity určí uživatelské jméno.

Přidejte novou třídu, která implementuje `IUserIdProvider` a načte jednu z deklarací identity od uživatele, která se má použít jako identifikátor. Pokud například chcete použít deklaraci identity (která je uživatelské jméno systému Windows ve formuláři `[Domain]\[Username]` ), vytvořte následující třídu:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Místo `ClaimTypes.Name` toho můžete použít libovolnou hodnotu z rozhraní `User` (například identifikátor SID systému Windows atd.).

> [!NOTE]
> Hodnota, kterou zvolíte, musí být jedinečná mezi všemi uživateli v systému. Jinak může zpráva určená pro jednoho uživatele skončit jiným uživatelem.

Zaregistrujte tuto komponentu v `Startup.ConfigureServices` metodě.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

V klientovi .NET musí být povoleno ověřování systému Windows nastavením vlastnosti [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Ověřování systému Windows je podporováno v aplikacích Internet Explorer a Microsoft Edge, ale ne ve všech prohlížečích. Například v Chrome a Safari se nezdařil pokus o použití ověřování systému Windows a WebSockets. Když se ověřování systému Windows nepovede, klient se pokusí přejít na jiné přenosy, které by mohly fungovat.

### <a name="use-claims-to-customize-identity-handling"></a>Přizpůsobení manipulace identity pomocí deklarací identity

Aplikace, která ověřuje uživatele, může odvodit SignalR ID uživatelů od deklarací identity uživatelů. Chcete-li určit SignalR , jak vytvoří ID uživatele, implementujte `IUserIdProvider` a zaregistrujte implementaci.

Vzorový kód ukazuje, jak byste měli pomocí deklarací identity vybrat e-mailovou adresu uživatele jako identifikační vlastnost. 

> [!NOTE]
> Hodnota, kterou zvolíte, musí být jedinečná mezi všemi uživateli v systému. Jinak může zpráva určená pro jednoho uživatele skončit jiným uživatelem.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

Registrace účtu přidá deklaraci identity s typem `ClaimsTypes.Email` do databáze identity ASP.NET.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Zaregistrujte tuto součást v `Startup.ConfigureServices` .

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autorizace uživatelů pro přístup k centrům a metodám centra

Ve výchozím nastavení mohou být všechny metody v centru volány neověřeným uživatelem. Chcete-li vyžadovat ověření, použijte atribut [autorizovat](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na centrum:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Pomocí argumentů konstruktoru a vlastností `[Authorize]` atributu můžete omezit přístup jenom na uživatele, kteří odpovídají na konkrétní [zásady autorizace](xref:security/authorization/policies). Pokud máte například vlastní zásadu autorizace, `MyAuthorizationPolicy` můžete zajistit, že k centru budou mít přístup jenom uživatelé, kteří mají odpovídající zásady, a to pomocí následujícího kódu:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Jednotlivé metody rozbočovače mohou mít `[Authorize]` také použit atribut. Pokud aktuální uživatel neodpovídá zásadám použitým pro metodu, je volajícímu vrácena chyba:

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Přizpůsobení autorizace metody centra pomocí obslužných rutin autorizace

SignalRposkytuje vlastní prostředek pro obslužné rutiny autorizace, pokud metoda rozbočovače vyžaduje autorizaci. Prostředek je instancí `HubInvocationContext` . `HubInvocationContext`Zahrnuje `HubCallerContext` , název vyvolané metody centra a argumenty metody hub.

Vezměte v úvahu příklad chatovací místnosti umožňující přihlášení více organizací prostřednictvím Azure Active Directory. Kdokoli s účet Microsoft se může přihlásit k chatu, ale jenom členové vlastnící organizace by měli být schopni zakázat uživatelům nebo zobrazit historie chatu uživatelů. Kromě toho můžeme chtít omezit určité funkce od určitých uživatelů. Používání aktualizovaných funkcí v ASP.NET Core 3,0 je zcela možné. Všimněte si, jak `DomainRestrictedRequirement` funguje jako vlastní `IAuthorizationRequirement` . Teď, když se `HubInvocationContext` předává parametr prostředků, interní logika může zkontrolovat kontext, ve kterém se centrum volá, a rozhodnout, že uživatel může provádět jednotlivé metody rozbočovače.

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

V `Startup.ConfigureServices` přidejte novou zásadu a `DomainRestrictedRequirement` jako parametr vytvořte vlastní požadavek `DomainRestricted` .

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

V předchozím příkladu `DomainRestrictedRequirement` je třída `IAuthorizationRequirement` a její vlastní `AuthorizationHandler` pro tento požadavek. Je přijatelné rozdělit tyto dvě komponenty na samostatné třídy, aby se oddělily obavy. Výhodou příkladu přístupu není nutné vkládat `AuthorizationHandler` při spuštění, protože požadavek a obslužná rutina jsou stejné.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Ověřování nosných tokenů v ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Ověřování na základě prostředků](xref:security/authorization/resourcebased)

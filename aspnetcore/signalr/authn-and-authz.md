---
title: Ověřování a autorizace v nástroji ASP.NET Core Signal
author: bradygaster
description: Naučte se používat ověřování a autorizaci v nástroji ASP.NET Core Signal.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 07/15/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: da226f4e192be8e34a0b2cec1493a1353c995279
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746533"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>Ověřování a autorizace v nástroji ASP.NET Core Signal

Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(stažení)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>Ověřování uživatelů připojujících se k centru signálů

K přidružení uživatele k jednotlivým připojením se dá použít signalizace s [ověřováním ASP.NET Core](xref:security/authentication/identity) . V centru se k datům ověřování dá dostat z [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) vlastnosti. Ověřování umožňuje rozbočovači volat metody všech připojení přidružených k uživateli (Další informace najdete v tématu [Správa uživatelů a skupin v nástroji Signal](xref:signalr/groups) ). K jednomu uživateli může být přidruženo více připojení.

Následuje příklad `Startup.Configure` , který používá signalizaci a ASP.NET Core ověřování:

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
> Pořadí, ve kterém zaregistrujete signál a ASP.NET Core v nich jsou v oblasti middleware ověřování. Vždy zavolejte `UseAuthentication` předtím `UseSignalR` , aby `HttpContext`byl signál na.

::: moniker-end

### <a name="cookie-authentication"></a>Ověřování souborem cookie

V aplikaci založené na prohlížeči umožňuje ověřování pomocí souborů cookie, aby vaše stávající uživatelská pověření automaticky pokračovala v připojení k signalizaci. Při použití klienta prohlížeče není nutná žádná další konfigurace. Pokud je uživatel přihlášený ke svojí aplikaci, připojení k tomuto ověření automaticky zdědí připojení k tomuto signálu.

Soubory cookie jsou způsoby, jak odesílat přístupové tokeny, ale klienti bez prohlížeče je mohou odeslat. Při použití [klienta .NET](xref:signalr/dotnet-client)lze `Cookies` vlastnost nakonfigurovat ve `.WithUrl` volání, aby bylo možné zadat soubor cookie. Použití ověřování souborem cookie z klienta .NET ale vyžaduje, aby aplikace poskytovala rozhraní API pro výměnu ověřovacích dat pro soubor cookie.

### <a name="bearer-token-authentication"></a>Ověřování nosných tokenů

Klient může místo použití souboru cookie zadat přístupový token. Server token ověří a použije ho k identifikaci uživatele. Toto ověření se provádí jenom v případě, že se naváže připojení. Během života připojení se server automaticky znovu neověřuje, aby zkontroloval odvolání tokenu.

Na serveru je ověřování pomocí tokenu nosiče nakonfigurované pomocí [middleware nosiče JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

V klientovi JavaScriptu lze token zadat pomocí možnosti [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) .

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

V klientovi .NET existuje podobná vlastnost [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) , kterou lze použít ke konfiguraci tokenu:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Funkce přístupového tokenu, kterou zadáte, je volána před **všemi** požadavky HTTP provedenými signálem. Pokud potřebujete obnovit token, abyste zachovali aktivní připojení (protože může vypršet platnost připojení), udělejte to v rámci této funkce a vraťte aktualizovaný token.

V případě standardních webových rozhraní API se tokeny nosiče odesílají v hlavičce protokolu HTTP. Návěstí ale v prohlížečích při použití některých přenosů nedokáže tato záhlaví nastavovat. Při použití protokolu WebSockets a událostí odesílaných serverem se token přenáší jako parametr řetězce dotazu. Aby bylo možné tuto podporu na serveru podporovat, je nutná další konfigurace:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a>Soubory cookie vs. nosných tokenů 

Vzhledem k tomu, že soubory cookie jsou specifické pro prohlížeče, jejich posílání z jiných druhů klientů přináší složitost v porovnání s posíláním nosných tokenů. Z tohoto důvodu se nedoporučuje ověřování souborů cookie, pokud aplikace potřebuje jenom ověřovat uživatele z klienta prohlížeče. Ověřování nosných tokenů je doporučený postup při použití jiných klientů, než je klient prohlížeče.

### <a name="windows-authentication"></a>Ověřování systému Windows

Pokud je ve vaší aplikaci nakonfigurované [ověřování systému Windows](xref:security/authentication/windowsauth) , může signál tuto identitu použít k zabezpečení rozbočovačů. Aby bylo možné odesílat zprávy jednotlivým uživatelům, je třeba přidat vlastního poskytovatele ID uživatele. Důvodem je to, že systém ověřování systému Windows neposkytuje deklaraci identity "identifikátor názvu", kterou Signal používá k určení uživatelského jména.

Přidejte novou třídu, která implementuje `IUserIdProvider` a načte jednu z deklarací identity od uživatele, která se má použít jako identifikátor. Pokud například chcete použít deklaraci identity (která je uživatelské jméno systému Windows ve formuláři `[Domain]\[Username]`), vytvořte následující třídu:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Místo toho můžete použít libovolnou hodnotu `User` z (například identifikátor SID systému Windows atd.). `ClaimTypes.Name`

> [!NOTE]
> Hodnota, kterou zvolíte, musí být jedinečná mezi všemi uživateli v systému. Jinak může zpráva určená pro jednoho uživatele skončit jiným uživatelem.

Zaregistrujte tuto komponentu `Startup.ConfigureServices` v metodě.

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
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Ověřování systému Windows je podporováno pouze klientem prohlížeče při použití aplikace Microsoft Internet Explorer nebo Microsoft Edge.

### <a name="use-claims-to-customize-identity-handling"></a>Přizpůsobení manipulace identity pomocí deklarací identity

Aplikace, která ověřuje uživatele, může odvodit ID uživatelů signálu z deklarací identity uživatelů. Chcete-li určit, jak má Signal vytvořit ID `IUserIdProvider` uživatelů, implementujte a zaregistrujte implementaci.

Vzorový kód ukazuje, jak byste měli pomocí deklarací identity vybrat e-mailovou adresu uživatele jako identifikační vlastnost. 

> [!NOTE]
> Hodnota, kterou zvolíte, musí být jedinečná mezi všemi uživateli v systému. Jinak může zpráva určená pro jednoho uživatele skončit jiným uživatelem.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

Registrace účtu přidá deklaraci identity s typem `ClaimsTypes.Email` do databáze identity ASP.NET.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Zaregistrujte tuto součást `Startup.ConfigureServices`v.

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autorizace uživatelů pro přístup k centrům a metodám centra

Ve výchozím nastavení mohou být všechny metody v centru volány neověřeným uživatelem. Aby bylo možné vyžadovat ověření, použijte atribut [autorizovat](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) na hub:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Pomocí argumentů konstruktoru a vlastností `[Authorize]` atributu můžete omezit přístup jenom na uživatele, kteří odpovídají na konkrétní [zásady autorizace](xref:security/authorization/policies). Pokud máte například vlastní zásadu `MyAuthorizationPolicy` autorizace, můžete zajistit, že k centru budou mít přístup jenom uživatelé, kteří mají odpovídající zásady, a to pomocí následujícího kódu:

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

Signál poskytuje vlastní prostředek pro obslužné rutiny autorizace, pokud metoda rozbočovače vyžaduje autorizaci. Prostředek je instancí `HubInvocationContext`. `HubInvocationContext` Zahrnuje,názevvyvolanémetodycentra`HubCallerContext`a argumenty metody hub.

Vezměte v úvahu příklad chatovací místnosti umožňující přihlášení více organizací prostřednictvím Azure Active Directory. Kdokoli s účet Microsoft se může přihlásit k chatu, ale jenom členové vlastnící organizace by měli být schopni zakázat uživatelům nebo zobrazit historie chatu uživatelů. Kromě toho můžeme chtít omezit určité funkce od určitých uživatelů. Používání aktualizovaných funkcí v ASP.NET Core 3,0 je zcela možné. Všimněte si, `DomainRestrictedRequirement` jak funguje jako vlastní `IAuthorizationRequirement`. Teď, `HubInvocationContext` když se předává parametr prostředků, interní logika může zkontrolovat kontext, ve kterém se centrum volá, a rozhodnout, že uživatel může provádět jednotlivé metody rozbočovače.

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

V `Startup.ConfigureServices`přidejte novou zásadu a jako parametr vytvořte `DomainRestricted` vlastní `DomainRestrictedRequirement` požadavek.

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

V předchozím příkladu `DomainRestrictedRequirement` je `IAuthorizationRequirement` třída a její vlastní `AuthorizationHandler` pro tento požadavek. Je přijatelné rozdělit tyto dvě komponenty na samostatné třídy, aby se oddělily obavy. Výhodou příkladu přístupu není nutné `AuthorizationHandler` vkládat při spuštění, protože požadavek a obslužná rutina jsou stejné.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Ověřování nosných tokenů v ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Ověřování na základě prostředků](xref:security/authorization/resourcebased)

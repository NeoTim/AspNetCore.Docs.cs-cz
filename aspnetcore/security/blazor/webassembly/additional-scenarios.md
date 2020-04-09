---
title: ASP.NET Blazor další scénáře zabezpečení Core WebAssembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 516132379ae20bd31c0f3b3261bb09b3f5b218f2
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501130"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly další scénáře zabezpečení

Podle [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>Vyžádání dalších přístupových tokenů

Většina aplikací vyžaduje k interakci s chráněnými prostředky, které používají, jenom přístupový token. V některých případech může aplikace vyžadovat více než jeden token pro interakci se dvěma nebo více prostředky.

V následujícím příkladu jsou další obory rozhraní MICROSOFT GRAPH API Azure Active Directory (AAD) vyžadovány aplikací ke čtení uživatelských dat a odesílání pošty. Po přidání oprávnění rozhraní MICROSOFT Graph API na portálu Azure AAD se`Program.Main`nakonfigurují další obory v klientské aplikaci ( , *Program.cs*):

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

Metoda `IAccessTokenProvider.RequestToken` poskytuje přetížení, které umožňuje aplikaci zřídit token s danou sadou oborů, jak je vidět v následujícím příkladu:

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

`TryGetToken`Vrátí:

* `true``token` pro použití.
* `false`pokud token není načten.

## <a name="handle-token-request-errors"></a>Zpracování chyb požadavku tokenu

Když jednostránková aplikace (SPA) ověřuje uživatele pomocí Open ID Connect (OIDC), stav ověřování je udržován místně v rámci spa a u zprostředkovatele identity (IP) ve formě souboru cookie relace, který je nastaven v důsledku, že uživatel zadá svá pověření.

Tokeny, které IP vydává pro uživatele, jsou obvykle platné pro krátká časová období, přibližně jednu hodinu normálně, takže klientská aplikace musí pravidelně načítat nové tokeny. V opačném případě by byl uživatel po vypršení platnosti udělených tokenů odhlášen. Ve většině případů oidc klienti jsou schopni zřídit nové tokeny bez nutnosti uživatele znovu ověřit díky stavu ověřování nebo "relace", která je uložena v rámci IP adresy.

Existují některé případy, ve kterých klient nemůže získat token bez interakce s uživatelem, například když se z nějakého důvodu uživatel explicitně odhlásí z IP adresy. K tomuto scénáři `https://login.microsoftonline.com` dochází, pokud uživatel navštíví a odhlásí. V těchto scénářích aplikace neví okamžitě, že uživatel se odhlásil. Token, který má klient v držení již nemusí být platný. Klient také není schopen zřídit nový token bez interakce uživatele po vypršení platnosti aktuálního tokenu.

Tyto scénáře nejsou specifické pro ověřování založené na tokenech. Jsou součástí povahy sa. Spa pomocí souborů cookie také nedokáže volat server API, pokud je odebrán ověřovací soubor cookie.

Když aplikace provádí volání rozhraní API pro chráněné prostředky, musíte si být vědomi následujících:

* Chcete-li zřídit nový přístupový token pro volání rozhraní API, může být uživatel povinen znovu ověřit.
* I v případě, že klient má token, který se zdá být platný, volání na server může selhat, protože token byl odvolán uživatelem.

Když aplikace požádá o token, existují dva možné výsledky:

* Požadavek je úspěšný a aplikace má platný token.
* Požadavek se nezdaří a aplikace musí znovu ověřit uživatele získat nový token.

Pokud se požadavek na token nezdaří, musíte se rozhodnout, zda chcete před provedením přesměrování uložit libovolný aktuální stav. Existuje několik přístupů s rostoucí úrovní složitosti:

* Uložte aktuální stav stránky do úložiště relací. Během `OnInitializeAsync`kontroly, zda lze stav obnovit před pokračováním.
* Přidejte parametr řetězce dotazu a použijte jej jako způsob, jak signalizovat aplikaci, že potřebuje znovu hydratovat dříve uložený stav.
* Přidejte parametr řetězce dotazu s jedinečným identifikátorem pro ukládání dat v úložišti relace bez rizika kolizí s jinými položkami.

Následující příklad ukazuje, jak:

* Před přesměrováním na přihlašovací stránku zachovejte stav.
* Obnovte předchozí stav po ověření pomocí parametru řetězce dotazu.

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a>Uložení stavu aplikace před ověřovací operací

Během operace ověřování existují případy, kdy chcete uložit stav aplikace před přesměrováním prohlížeče na IP adresu. To může být případ, kdy používáte něco jako kontejner stavu a chcete obnovit stav po úspěšném ověření. Vlastní objekt stavu ověřování můžete použít k zachování stavu specifického pro aplikaci nebo odkaz na něj a obnovit tento stav po úspěšném dokončení operace ověřování.

`Authentication`komponenta (*Stránky/Authentication.razor*):

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a>Přizpůsobení tras aplikací

Ve výchozím `Microsoft.AspNetCore.Components.WebAssembly.Authentication` nastavení knihovna používá trasy uvedené v následující tabulce pro reprezentaci různých stavů ověřování.

| Trasa                            | Účel |
| -------------------------------- | ------- |
| `authentication/login`           | Spustí operaci přihlášení. |
| `authentication/login-callback`  | Zpracovává výsledek jakékoli operace přihlášení. |
| `authentication/login-failed`    | Zobrazí chybové zprávy, když se z nějakého důvodu nezdaří operace přihlášení. |
| `authentication/logout`          | Spustí operaci odhlašování. |
| `authentication/logout-callback` | Zpracovává výsledek operace odhlašování. |
| `authentication/logout-failed`   | Zobrazí chybové zprávy, když se z nějakého důvodu nezdaří operace odhlášení. |
| `authentication/logged-out`      | Označuje, že uživatel úspěšně odhlášení. |
| `authentication/profile`         | Spustí operaci pro úpravu profilu uživatele. |
| `authentication/register`        | Spustí operaci k registraci nového uživatele. |

Trasy zobrazené v předchozí tabulce lze `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`konfigurovat pomocí aplikace . Při nastavování možností pro poskytování vlastních tras zkontrolujte, zda má aplikace trasu, která zpracovává každou cestu.

V následujícím příkladu jsou všechny cesty `/security`předponou .

`Authentication`komponenta (*Stránky/Authentication.razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`*(Program.cs*):

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

Pokud požadavek vyžaduje zcela odlišné cesty, nastavte trasy, jak `RemoteAuthenticatorView` je popsáno výše a vykreslete s parametrem explicitní akce:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Pokud se tak rozhodnete, můžete ui rozdělit na různé stránky.

## <a name="customize-the-authentication-user-interface"></a>Přizpůsobení uživatelského rozhraní ověřování

`RemoteAuthenticatorView`obsahuje výchozí sadu částí ui pro každý stav ověřování. Každý stav lze přizpůsobit předáním `RenderFragment`vlastní . Chcete-li přizpůsobit zobrazený text během počátečního `RemoteAuthenticatorView` procesu přihlášení, můžete změnit následující.

`Authentication`komponenta (*Stránky/Authentication.razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

Má `RemoteAuthenticatorView` jeden fragment, který lze použít na trasu ověřování uvedené v následující tabulce.

| Trasa                            | Fragment                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

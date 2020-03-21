---
title: ASP.NET Core Blazor další scénáře zabezpečení pro WebAssembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: ccb512392341e3eea33f4ab45740b7900f7b63f9
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989464"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>Další scénáře zabezpečení ASP.NET Core Blazor pro WebAssembly

[Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="handle-token-request-errors"></a>Zpracování chyb požadavků na tokeny

Když jedna stránková aplikace (SPA) ověřuje uživatele pomocí funkce Open ID Connect (OIDC), je stav ověřování udržován místně v zabezpečeném uživatelském rozhraní (SPA) a ve zprostředkovateli identity (IP) ve formě souboru cookie relace, který je nastaven jako výsledek uživatele, který poskytuje své přihlašovací údaje.

Tokeny, které jsou pro uživatele vysílané, jsou obvykle platné po krátkou dobu přibližně jedna hodina, takže klientská aplikace musí pravidelně načítat nové tokeny. V opačném případě se uživatel odhlásí po vypršení platnosti udělených tokenů. Ve většině případů můžou klienti OIDC zřizovat nové tokeny, aniž by museli znovu ověřovat uživatele díky stavu ověřování nebo "relaci", která se udržuje v rámci IP adresy.

V některých případech může klient získat token bez zásahu uživatele, například pokud z nějakého důvodu se uživatel výslovně odhlásí z IP adresy. K tomuto scénáři dojde, pokud uživatel navštíví `https://login.microsoftonline.com` a odhlásí se. V těchto scénářích aplikace neví hned, že se uživatel odhlásil. Libovolný token, který může klient obsahovat, již nemusí být platný. Klient navíc nemůže zřídit nový token bez zásahu uživatele po vypršení platnosti tohoto tokenu.

Tyto scénáře nejsou specifické pro ověřování založené na tokenech. Jsou součástí charakteru jednostránkové. ZABEZPEČENÉ ověřování pomocí souborů cookie také nedokáže volat rozhraní API serveru, pokud je soubor cookie ověření odebrán.

Když aplikace provede volání rozhraní API k chráněným prostředkům, musíte mít na paměti následující informace:

* Pro zřízení nového přístupového tokenu pro volání rozhraní API může být uživatel požádán o ověření znovu.
* I v případě, že má klient token, který je pravděpodobně platný, volání serveru může selhat, protože byl token odvolán uživatelem.

Když aplikace požaduje token, existují dva možné výsledky:

* Požadavek je úspěšný a aplikace má platný token.
* Požadavek se nezdařil a aplikace musí znovu ověřit uživatele, aby získal nový token.

Pokud se žádost o token nezdařila, musíte se rozhodnout, zda chcete před provedením přesměrování Uložit aktuální stav. Existuje několik přístupů se zvýšenými úrovněmi složitosti:

* Uloží aktuální stav stránky do úložiště relace. Během `OnInitializeAsync`ověřte, zda je možné obnovit stav, než budete pokračovat.
* Přidejte parametr řetězce dotazu a použijte ho jako způsob, jak aplikaci signalizovat, že potřebuje znovu Hydrate dříve uložený stav.
* Přidejte parametr řetězce dotazu s jedinečným identifikátorem pro ukládání dat v úložišti relace bez rizikových kolizí s ostatními položkami.

Následující příklad ukazuje postup:

* Zachovat stav před přesměrováním na přihlašovací stránku
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

## <a name="save-app-state-before-an-authentication-operation"></a>Uložení stavu aplikace před operací ověřování

Během operace ověřování existují případy, kdy chcete uložit stav aplikace, než se prohlížeč přesměruje na IP adresu. To může být případ, kdy používáte něco jako kontejner stavu a chcete obnovit stav po úspěšném ověření. Vlastní objekt stavu ověřování můžete použít k zachování stavu specifického pro aplikaci nebo odkaz na něj a obnovení tohoto stavu po úspěšném dokončení operace ověřování.

součást `Authentication` (*stránky/ověřování. Razor*):

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

## <a name="request-additional-access-tokens"></a>Vyžádání dalších přístupových tokenů

Většina aplikací vyžaduje přístupový token pro interakci s chráněnými prostředky, které používají. V některých scénářích může aplikace vyžadovat více než jeden token, aby bylo možné pracovat se dvěma nebo více prostředky. Metoda `IAccessTokenProvider.RequestToken` poskytuje přetížení, které umožňuje aplikaci zřídit token s danou sadou oborů, jak je vidět v následujícím příkladu:

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });
```

## <a name="customize-app-routes"></a>Přizpůsobení směrování aplikací

Ve výchozím nastavení používá knihovna `Microsoft.AspNetCore.Components.WebAssembly.Authentication` trasy, které jsou uvedeny v následující tabulce, pro reprezentace různých stavů ověřování.

| Trasa                            | Účel |
| -------------------------------- | ------- |
| `authentication/login`           | Spustí operaci přihlášení. |
| `authentication/login-callback`  | Zpracovává výsledek jakékoli operace přihlášení. |
| `authentication/login-failed`    | Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace přihlášení. |
| `authentication/logout`          | Spustí operaci odhlášení. |
| `authentication/logout-callback` | Zpracovává výsledek operace odhlášení. |
| `authentication/logout-failed`   | Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace odhlášení. |
| `authentication/logged-out`      | Indikuje, že se uživatel úspěšně odhlásit. |
| `authentication/profile`         | Aktivuje operaci pro úpravu profilu uživatele. |
| `authentication/register`        | Aktivuje operaci pro registraci nového uživatele. |

Trasy zobrazené v předchozí tabulce lze konfigurovat prostřednictvím `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`. Při nastavování možností pro poskytování vlastních tras potvrďte, že aplikace má trasu, která zpracovává jednotlivé cesty.

V následujícím příkladu jsou všechny cesty s předponou `/security`.

součást `Authentication` (*stránky/ověřování. Razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (*program.cs*):

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

Pokud požadavek volá zcela jiné cesty, nastavte trasy popsané dříve a vykreslete `RemoteAuthenticatorView` s parametrem explicitní akce:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Pokud se rozhodnete tak učinit, můžete uživatelské rozhraní přerušit na jiné stránky.

## <a name="customize-the-authentication-user-interface"></a>Přizpůsobení uživatelského rozhraní ověřování

`RemoteAuthenticatorView` obsahuje výchozí sadu částí uživatelského rozhraní pro každý stav ověřování. Každý stav se dá přizpůsobit předáním vlastního `RenderFragment`. K přizpůsobení zobrazeného textu během procesu prvotního přihlášení může `RemoteAuthenticatorView` změnit následujícím způsobem.

součást `Authentication` (*stránky/ověřování. Razor*):

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

`RemoteAuthenticatorView` má jeden fragment, který je možné použít pro jednu trasu ověřování, jak je znázorněno v následující tabulce.

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

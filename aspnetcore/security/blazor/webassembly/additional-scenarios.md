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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="7f898-102">ASP.NET Core Blazor WebAssembly další scénáře zabezpečení</span><span class="sxs-lookup"><span data-stu-id="7f898-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="7f898-103">Podle [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="7f898-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="7f898-104">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="7f898-104">Request additional access tokens</span></span>

<span data-ttu-id="7f898-105">Většina aplikací vyžaduje k interakci s chráněnými prostředky, které používají, jenom přístupový token.</span><span class="sxs-lookup"><span data-stu-id="7f898-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="7f898-106">V některých případech může aplikace vyžadovat více než jeden token pro interakci se dvěma nebo více prostředky.</span><span class="sxs-lookup"><span data-stu-id="7f898-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="7f898-107">V následujícím příkladu jsou další obory rozhraní MICROSOFT GRAPH API Azure Active Directory (AAD) vyžadovány aplikací ke čtení uživatelských dat a odesílání pošty.</span><span class="sxs-lookup"><span data-stu-id="7f898-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="7f898-108">Po přidání oprávnění rozhraní MICROSOFT Graph API na portálu Azure AAD se`Program.Main`nakonfigurují další obory v klientské aplikaci ( , *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="7f898-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="7f898-109">Metoda `IAccessTokenProvider.RequestToken` poskytuje přetížení, které umožňuje aplikaci zřídit token s danou sadou oborů, jak je vidět v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="7f898-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="7f898-110">`TryGetToken`Vrátí:</span><span class="sxs-lookup"><span data-stu-id="7f898-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="7f898-111">`true``token` pro použití.</span><span class="sxs-lookup"><span data-stu-id="7f898-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="7f898-112">`false`pokud token není načten.</span><span class="sxs-lookup"><span data-stu-id="7f898-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="7f898-113">Zpracování chyb požadavku tokenu</span><span class="sxs-lookup"><span data-stu-id="7f898-113">Handle token request errors</span></span>

<span data-ttu-id="7f898-114">Když jednostránková aplikace (SPA) ověřuje uživatele pomocí Open ID Connect (OIDC), stav ověřování je udržován místně v rámci spa a u zprostředkovatele identity (IP) ve formě souboru cookie relace, který je nastaven v důsledku, že uživatel zadá svá pověření.</span><span class="sxs-lookup"><span data-stu-id="7f898-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="7f898-115">Tokeny, které IP vydává pro uživatele, jsou obvykle platné pro krátká časová období, přibližně jednu hodinu normálně, takže klientská aplikace musí pravidelně načítat nové tokeny.</span><span class="sxs-lookup"><span data-stu-id="7f898-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="7f898-116">V opačném případě by byl uživatel po vypršení platnosti udělených tokenů odhlášen.</span><span class="sxs-lookup"><span data-stu-id="7f898-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="7f898-117">Ve většině případů oidc klienti jsou schopni zřídit nové tokeny bez nutnosti uživatele znovu ověřit díky stavu ověřování nebo "relace", která je uložena v rámci IP adresy.</span><span class="sxs-lookup"><span data-stu-id="7f898-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="7f898-118">Existují některé případy, ve kterých klient nemůže získat token bez interakce s uživatelem, například když se z nějakého důvodu uživatel explicitně odhlásí z IP adresy.</span><span class="sxs-lookup"><span data-stu-id="7f898-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="7f898-119">K tomuto scénáři `https://login.microsoftonline.com` dochází, pokud uživatel navštíví a odhlásí. V těchto scénářích aplikace neví okamžitě, že uživatel se odhlásil. Token, který má klient v držení již nemusí být platný.</span><span class="sxs-lookup"><span data-stu-id="7f898-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="7f898-120">Klient také není schopen zřídit nový token bez interakce uživatele po vypršení platnosti aktuálního tokenu.</span><span class="sxs-lookup"><span data-stu-id="7f898-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="7f898-121">Tyto scénáře nejsou specifické pro ověřování založené na tokenech.</span><span class="sxs-lookup"><span data-stu-id="7f898-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="7f898-122">Jsou součástí povahy sa.</span><span class="sxs-lookup"><span data-stu-id="7f898-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="7f898-123">Spa pomocí souborů cookie také nedokáže volat server API, pokud je odebrán ověřovací soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="7f898-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="7f898-124">Když aplikace provádí volání rozhraní API pro chráněné prostředky, musíte si být vědomi následujících:</span><span class="sxs-lookup"><span data-stu-id="7f898-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="7f898-125">Chcete-li zřídit nový přístupový token pro volání rozhraní API, může být uživatel povinen znovu ověřit.</span><span class="sxs-lookup"><span data-stu-id="7f898-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="7f898-126">I v případě, že klient má token, který se zdá být platný, volání na server může selhat, protože token byl odvolán uživatelem.</span><span class="sxs-lookup"><span data-stu-id="7f898-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="7f898-127">Když aplikace požádá o token, existují dva možné výsledky:</span><span class="sxs-lookup"><span data-stu-id="7f898-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="7f898-128">Požadavek je úspěšný a aplikace má platný token.</span><span class="sxs-lookup"><span data-stu-id="7f898-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="7f898-129">Požadavek se nezdaří a aplikace musí znovu ověřit uživatele získat nový token.</span><span class="sxs-lookup"><span data-stu-id="7f898-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="7f898-130">Pokud se požadavek na token nezdaří, musíte se rozhodnout, zda chcete před provedením přesměrování uložit libovolný aktuální stav.</span><span class="sxs-lookup"><span data-stu-id="7f898-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="7f898-131">Existuje několik přístupů s rostoucí úrovní složitosti:</span><span class="sxs-lookup"><span data-stu-id="7f898-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="7f898-132">Uložte aktuální stav stránky do úložiště relací.</span><span class="sxs-lookup"><span data-stu-id="7f898-132">Store the current page state in session storage.</span></span> <span data-ttu-id="7f898-133">Během `OnInitializeAsync`kontroly, zda lze stav obnovit před pokračováním.</span><span class="sxs-lookup"><span data-stu-id="7f898-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="7f898-134">Přidejte parametr řetězce dotazu a použijte jej jako způsob, jak signalizovat aplikaci, že potřebuje znovu hydratovat dříve uložený stav.</span><span class="sxs-lookup"><span data-stu-id="7f898-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="7f898-135">Přidejte parametr řetězce dotazu s jedinečným identifikátorem pro ukládání dat v úložišti relace bez rizika kolizí s jinými položkami.</span><span class="sxs-lookup"><span data-stu-id="7f898-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="7f898-136">Následující příklad ukazuje, jak:</span><span class="sxs-lookup"><span data-stu-id="7f898-136">The following example shows how to:</span></span>

* <span data-ttu-id="7f898-137">Před přesměrováním na přihlašovací stránku zachovejte stav.</span><span class="sxs-lookup"><span data-stu-id="7f898-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="7f898-138">Obnovte předchozí stav po ověření pomocí parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="7f898-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="7f898-139">Uložení stavu aplikace před ověřovací operací</span><span class="sxs-lookup"><span data-stu-id="7f898-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="7f898-140">Během operace ověřování existují případy, kdy chcete uložit stav aplikace před přesměrováním prohlížeče na IP adresu.</span><span class="sxs-lookup"><span data-stu-id="7f898-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="7f898-141">To může být případ, kdy používáte něco jako kontejner stavu a chcete obnovit stav po úspěšném ověření.</span><span class="sxs-lookup"><span data-stu-id="7f898-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="7f898-142">Vlastní objekt stavu ověřování můžete použít k zachování stavu specifického pro aplikaci nebo odkaz na něj a obnovit tento stav po úspěšném dokončení operace ověřování.</span><span class="sxs-lookup"><span data-stu-id="7f898-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="7f898-143">`Authentication`komponenta (*Stránky/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="7f898-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="7f898-144">Přizpůsobení tras aplikací</span><span class="sxs-lookup"><span data-stu-id="7f898-144">Customize app routes</span></span>

<span data-ttu-id="7f898-145">Ve výchozím `Microsoft.AspNetCore.Components.WebAssembly.Authentication` nastavení knihovna používá trasy uvedené v následující tabulce pro reprezentaci různých stavů ověřování.</span><span class="sxs-lookup"><span data-stu-id="7f898-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="7f898-146">Trasa</span><span class="sxs-lookup"><span data-stu-id="7f898-146">Route</span></span>                            | <span data-ttu-id="7f898-147">Účel</span><span class="sxs-lookup"><span data-stu-id="7f898-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="7f898-148">Spustí operaci přihlášení.</span><span class="sxs-lookup"><span data-stu-id="7f898-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="7f898-149">Zpracovává výsledek jakékoli operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="7f898-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="7f898-150">Zobrazí chybové zprávy, když se z nějakého důvodu nezdaří operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="7f898-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="7f898-151">Spustí operaci odhlašování.</span><span class="sxs-lookup"><span data-stu-id="7f898-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="7f898-152">Zpracovává výsledek operace odhlašování.</span><span class="sxs-lookup"><span data-stu-id="7f898-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="7f898-153">Zobrazí chybové zprávy, když se z nějakého důvodu nezdaří operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="7f898-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="7f898-154">Označuje, že uživatel úspěšně odhlášení.</span><span class="sxs-lookup"><span data-stu-id="7f898-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="7f898-155">Spustí operaci pro úpravu profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="7f898-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="7f898-156">Spustí operaci k registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="7f898-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="7f898-157">Trasy zobrazené v předchozí tabulce lze `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`konfigurovat pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="7f898-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="7f898-158">Při nastavování možností pro poskytování vlastních tras zkontrolujte, zda má aplikace trasu, která zpracovává každou cestu.</span><span class="sxs-lookup"><span data-stu-id="7f898-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="7f898-159">V následujícím příkladu jsou všechny cesty `/security`předponou .</span><span class="sxs-lookup"><span data-stu-id="7f898-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="7f898-160">`Authentication`komponenta (*Stránky/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="7f898-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="7f898-161">`Program.Main`*(Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="7f898-161">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="7f898-162">Pokud požadavek vyžaduje zcela odlišné cesty, nastavte trasy, jak `RemoteAuthenticatorView` je popsáno výše a vykreslete s parametrem explicitní akce:</span><span class="sxs-lookup"><span data-stu-id="7f898-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="7f898-163">Pokud se tak rozhodnete, můžete ui rozdělit na různé stránky.</span><span class="sxs-lookup"><span data-stu-id="7f898-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="7f898-164">Přizpůsobení uživatelského rozhraní ověřování</span><span class="sxs-lookup"><span data-stu-id="7f898-164">Customize the authentication user interface</span></span>

<span data-ttu-id="7f898-165">`RemoteAuthenticatorView`obsahuje výchozí sadu částí ui pro každý stav ověřování.</span><span class="sxs-lookup"><span data-stu-id="7f898-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="7f898-166">Každý stav lze přizpůsobit předáním `RenderFragment`vlastní .</span><span class="sxs-lookup"><span data-stu-id="7f898-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="7f898-167">Chcete-li přizpůsobit zobrazený text během počátečního `RemoteAuthenticatorView` procesu přihlášení, můžete změnit následující.</span><span class="sxs-lookup"><span data-stu-id="7f898-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="7f898-168">`Authentication`komponenta (*Stránky/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="7f898-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="7f898-169">Má `RemoteAuthenticatorView` jeden fragment, který lze použít na trasu ověřování uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="7f898-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="7f898-170">Trasa</span><span class="sxs-lookup"><span data-stu-id="7f898-170">Route</span></span>                            | <span data-ttu-id="7f898-171">Fragment</span><span class="sxs-lookup"><span data-stu-id="7f898-171">Fragment</span></span>                |
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

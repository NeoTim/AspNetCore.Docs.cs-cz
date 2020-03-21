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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="373fe-102">Další scénáře zabezpečení ASP.NET Core Blazor pro WebAssembly</span><span class="sxs-lookup"><span data-stu-id="373fe-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="373fe-103">[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="373fe-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="handle-token-request-errors"></a><span data-ttu-id="373fe-104">Zpracování chyb požadavků na tokeny</span><span class="sxs-lookup"><span data-stu-id="373fe-104">Handle token request errors</span></span>

<span data-ttu-id="373fe-105">Když jedna stránková aplikace (SPA) ověřuje uživatele pomocí funkce Open ID Connect (OIDC), je stav ověřování udržován místně v zabezpečeném uživatelském rozhraní (SPA) a ve zprostředkovateli identity (IP) ve formě souboru cookie relace, který je nastaven jako výsledek uživatele, který poskytuje své přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="373fe-105">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="373fe-106">Tokeny, které jsou pro uživatele vysílané, jsou obvykle platné po krátkou dobu přibližně jedna hodina, takže klientská aplikace musí pravidelně načítat nové tokeny.</span><span class="sxs-lookup"><span data-stu-id="373fe-106">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="373fe-107">V opačném případě se uživatel odhlásí po vypršení platnosti udělených tokenů.</span><span class="sxs-lookup"><span data-stu-id="373fe-107">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="373fe-108">Ve většině případů můžou klienti OIDC zřizovat nové tokeny, aniž by museli znovu ověřovat uživatele díky stavu ověřování nebo "relaci", která se udržuje v rámci IP adresy.</span><span class="sxs-lookup"><span data-stu-id="373fe-108">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="373fe-109">V některých případech může klient získat token bez zásahu uživatele, například pokud z nějakého důvodu se uživatel výslovně odhlásí z IP adresy.</span><span class="sxs-lookup"><span data-stu-id="373fe-109">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="373fe-110">K tomuto scénáři dojde, pokud uživatel navštíví `https://login.microsoftonline.com` a odhlásí se. V těchto scénářích aplikace neví hned, že se uživatel odhlásil. Libovolný token, který může klient obsahovat, již nemusí být platný.</span><span class="sxs-lookup"><span data-stu-id="373fe-110">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="373fe-111">Klient navíc nemůže zřídit nový token bez zásahu uživatele po vypršení platnosti tohoto tokenu.</span><span class="sxs-lookup"><span data-stu-id="373fe-111">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="373fe-112">Tyto scénáře nejsou specifické pro ověřování založené na tokenech.</span><span class="sxs-lookup"><span data-stu-id="373fe-112">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="373fe-113">Jsou součástí charakteru jednostránkové.</span><span class="sxs-lookup"><span data-stu-id="373fe-113">They are part of the nature of SPAs.</span></span> <span data-ttu-id="373fe-114">ZABEZPEČENÉ ověřování pomocí souborů cookie také nedokáže volat rozhraní API serveru, pokud je soubor cookie ověření odebrán.</span><span class="sxs-lookup"><span data-stu-id="373fe-114">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="373fe-115">Když aplikace provede volání rozhraní API k chráněným prostředkům, musíte mít na paměti následující informace:</span><span class="sxs-lookup"><span data-stu-id="373fe-115">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="373fe-116">Pro zřízení nového přístupového tokenu pro volání rozhraní API může být uživatel požádán o ověření znovu.</span><span class="sxs-lookup"><span data-stu-id="373fe-116">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="373fe-117">I v případě, že má klient token, který je pravděpodobně platný, volání serveru může selhat, protože byl token odvolán uživatelem.</span><span class="sxs-lookup"><span data-stu-id="373fe-117">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="373fe-118">Když aplikace požaduje token, existují dva možné výsledky:</span><span class="sxs-lookup"><span data-stu-id="373fe-118">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="373fe-119">Požadavek je úspěšný a aplikace má platný token.</span><span class="sxs-lookup"><span data-stu-id="373fe-119">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="373fe-120">Požadavek se nezdařil a aplikace musí znovu ověřit uživatele, aby získal nový token.</span><span class="sxs-lookup"><span data-stu-id="373fe-120">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="373fe-121">Pokud se žádost o token nezdařila, musíte se rozhodnout, zda chcete před provedením přesměrování Uložit aktuální stav.</span><span class="sxs-lookup"><span data-stu-id="373fe-121">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="373fe-122">Existuje několik přístupů se zvýšenými úrovněmi složitosti:</span><span class="sxs-lookup"><span data-stu-id="373fe-122">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="373fe-123">Uloží aktuální stav stránky do úložiště relace.</span><span class="sxs-lookup"><span data-stu-id="373fe-123">Store the current page state in session storage.</span></span> <span data-ttu-id="373fe-124">Během `OnInitializeAsync`ověřte, zda je možné obnovit stav, než budete pokračovat.</span><span class="sxs-lookup"><span data-stu-id="373fe-124">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="373fe-125">Přidejte parametr řetězce dotazu a použijte ho jako způsob, jak aplikaci signalizovat, že potřebuje znovu Hydrate dříve uložený stav.</span><span class="sxs-lookup"><span data-stu-id="373fe-125">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="373fe-126">Přidejte parametr řetězce dotazu s jedinečným identifikátorem pro ukládání dat v úložišti relace bez rizikových kolizí s ostatními položkami.</span><span class="sxs-lookup"><span data-stu-id="373fe-126">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="373fe-127">Následující příklad ukazuje postup:</span><span class="sxs-lookup"><span data-stu-id="373fe-127">The following example shows how to:</span></span>

* <span data-ttu-id="373fe-128">Zachovat stav před přesměrováním na přihlašovací stránku</span><span class="sxs-lookup"><span data-stu-id="373fe-128">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="373fe-129">Obnovte předchozí stav po ověření pomocí parametru řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="373fe-129">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="373fe-130">Uložení stavu aplikace před operací ověřování</span><span class="sxs-lookup"><span data-stu-id="373fe-130">Save app state before an authentication operation</span></span>

<span data-ttu-id="373fe-131">Během operace ověřování existují případy, kdy chcete uložit stav aplikace, než se prohlížeč přesměruje na IP adresu.</span><span class="sxs-lookup"><span data-stu-id="373fe-131">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="373fe-132">To může být případ, kdy používáte něco jako kontejner stavu a chcete obnovit stav po úspěšném ověření.</span><span class="sxs-lookup"><span data-stu-id="373fe-132">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="373fe-133">Vlastní objekt stavu ověřování můžete použít k zachování stavu specifického pro aplikaci nebo odkaz na něj a obnovení tohoto stavu po úspěšném dokončení operace ověřování.</span><span class="sxs-lookup"><span data-stu-id="373fe-133">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="373fe-134">součást `Authentication` (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="373fe-134">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="request-additional-access-tokens"></a><span data-ttu-id="373fe-135">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="373fe-135">Request additional access tokens</span></span>

<span data-ttu-id="373fe-136">Většina aplikací vyžaduje přístupový token pro interakci s chráněnými prostředky, které používají.</span><span class="sxs-lookup"><span data-stu-id="373fe-136">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="373fe-137">V některých scénářích může aplikace vyžadovat více než jeden token, aby bylo možné pracovat se dvěma nebo více prostředky.</span><span class="sxs-lookup"><span data-stu-id="373fe-137">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span> <span data-ttu-id="373fe-138">Metoda `IAccessTokenProvider.RequestToken` poskytuje přetížení, které umožňuje aplikaci zřídit token s danou sadou oborů, jak je vidět v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="373fe-138">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });
```

## <a name="customize-app-routes"></a><span data-ttu-id="373fe-139">Přizpůsobení směrování aplikací</span><span class="sxs-lookup"><span data-stu-id="373fe-139">Customize app routes</span></span>

<span data-ttu-id="373fe-140">Ve výchozím nastavení používá knihovna `Microsoft.AspNetCore.Components.WebAssembly.Authentication` trasy, které jsou uvedeny v následující tabulce, pro reprezentace různých stavů ověřování.</span><span class="sxs-lookup"><span data-stu-id="373fe-140">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="373fe-141">Trasa</span><span class="sxs-lookup"><span data-stu-id="373fe-141">Route</span></span>                            | <span data-ttu-id="373fe-142">Účel</span><span class="sxs-lookup"><span data-stu-id="373fe-142">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="373fe-143">Spustí operaci přihlášení.</span><span class="sxs-lookup"><span data-stu-id="373fe-143">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="373fe-144">Zpracovává výsledek jakékoli operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="373fe-144">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="373fe-145">Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace přihlášení.</span><span class="sxs-lookup"><span data-stu-id="373fe-145">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="373fe-146">Spustí operaci odhlášení.</span><span class="sxs-lookup"><span data-stu-id="373fe-146">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="373fe-147">Zpracovává výsledek operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="373fe-147">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="373fe-148">Zobrazí chybové zprávy, když se z nějakého důvodu nepovede operace odhlášení.</span><span class="sxs-lookup"><span data-stu-id="373fe-148">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="373fe-149">Indikuje, že se uživatel úspěšně odhlásit.</span><span class="sxs-lookup"><span data-stu-id="373fe-149">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="373fe-150">Aktivuje operaci pro úpravu profilu uživatele.</span><span class="sxs-lookup"><span data-stu-id="373fe-150">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="373fe-151">Aktivuje operaci pro registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="373fe-151">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="373fe-152">Trasy zobrazené v předchozí tabulce lze konfigurovat prostřednictvím `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span><span class="sxs-lookup"><span data-stu-id="373fe-152">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="373fe-153">Při nastavování možností pro poskytování vlastních tras potvrďte, že aplikace má trasu, která zpracovává jednotlivé cesty.</span><span class="sxs-lookup"><span data-stu-id="373fe-153">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="373fe-154">V následujícím příkladu jsou všechny cesty s předponou `/security`.</span><span class="sxs-lookup"><span data-stu-id="373fe-154">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="373fe-155">součást `Authentication` (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="373fe-155">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="373fe-156">`Program.Main` (*program.cs*):</span><span class="sxs-lookup"><span data-stu-id="373fe-156">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="373fe-157">Pokud požadavek volá zcela jiné cesty, nastavte trasy popsané dříve a vykreslete `RemoteAuthenticatorView` s parametrem explicitní akce:</span><span class="sxs-lookup"><span data-stu-id="373fe-157">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="373fe-158">Pokud se rozhodnete tak učinit, můžete uživatelské rozhraní přerušit na jiné stránky.</span><span class="sxs-lookup"><span data-stu-id="373fe-158">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="373fe-159">Přizpůsobení uživatelského rozhraní ověřování</span><span class="sxs-lookup"><span data-stu-id="373fe-159">Customize the authentication user interface</span></span>

<span data-ttu-id="373fe-160">`RemoteAuthenticatorView` obsahuje výchozí sadu částí uživatelského rozhraní pro každý stav ověřování.</span><span class="sxs-lookup"><span data-stu-id="373fe-160">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="373fe-161">Každý stav se dá přizpůsobit předáním vlastního `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="373fe-161">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="373fe-162">K přizpůsobení zobrazeného textu během procesu prvotního přihlášení může `RemoteAuthenticatorView` změnit následujícím způsobem.</span><span class="sxs-lookup"><span data-stu-id="373fe-162">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="373fe-163">součást `Authentication` (*stránky/ověřování. Razor*):</span><span class="sxs-lookup"><span data-stu-id="373fe-163">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="373fe-164">`RemoteAuthenticatorView` má jeden fragment, který je možné použít pro jednu trasu ověřování, jak je znázorněno v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="373fe-164">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="373fe-165">Trasa</span><span class="sxs-lookup"><span data-stu-id="373fe-165">Route</span></span>                            | <span data-ttu-id="373fe-166">Fragment</span><span class="sxs-lookup"><span data-stu-id="373fe-166">Fragment</span></span>                |
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

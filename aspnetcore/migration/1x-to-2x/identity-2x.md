---
title: Migrace ověřování a Identity ASP.NET Core 2,0
author: scottaddie
description: Tento článek popisuje nejběžnější kroky pro migraci ASP.NET Core 1. x ověřování a Identity ASP.NET Core 2,0.
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: e828446716d88d92aeb587874421a5751dcb6de0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769498"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="f0ba1-103">Migrace ověřování a Identity ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="f0ba1-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="f0ba1-104">[Scottem Addie](https://github.com/scottaddie) a [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="f0ba1-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="f0ba1-105">ASP.NET Core 2,0 má nový model pro ověřování a [Identity](xref:security/authentication/identity) který zjednodušuje konfiguraci pomocí služeb.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="f0ba1-106">ASP.NET Core 1. x aplikací, které používají ověřování Identity , nebo je lze aktualizovat, aby používaly nový model, jak je uvedeno níže.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="f0ba1-107">Aktualizovat obory názvů</span><span class="sxs-lookup"><span data-stu-id="f0ba1-107">Update namespaces</span></span>

<span data-ttu-id="f0ba1-108">V 1. x byly třídy, `IdentityRole` jako `IdentityUser` jsou a, nalezeny `Microsoft.AspNetCore.Identity.EntityFrameworkCore` v oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="f0ba1-109">V 2,0 se <xref:Microsoft.AspNetCore.Identity> obor názvů stal novým domovem pro několik takových tříd.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="f0ba1-110">S výchozím Identity kódem obsahují `ApplicationUser` ovlivněné třídy a. `Startup`</span><span class="sxs-lookup"><span data-stu-id="f0ba1-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="f0ba1-111">Upravte své `using` příkazy tak, aby vyřešily ovlivněné odkazy.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="f0ba1-112">Middleware a služby ověřování</span><span class="sxs-lookup"><span data-stu-id="f0ba1-112">Authentication Middleware and services</span></span>

<span data-ttu-id="f0ba1-113">V projektech 1. x se ověřování konfiguruje prostřednictvím middlewaru.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="f0ba1-114">Metoda middleware je vyvolána pro každé schéma ověřování, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="f0ba1-115">Následující příklad 1. x konfiguruje ověřování na Facebooku pomocí Identity v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="f0ba1-116">V projektech 2,0 se ověřování konfiguruje přes služby.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="f0ba1-117">Každé schéma ověřování je registrováno v `ConfigureServices` metodě *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="f0ba1-118">`UseIdentity` Metoda je nahrazena řetězcem `UseAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="f0ba1-119">Následující příklad 2,0 konfiguruje ověřování na Facebooku pomocí Identity v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="f0ba1-120">`UseAuthentication` Metoda přidá jednu komponentu middleware pro ověřování, která zodpovídá za automatické ověřování a zpracování požadavků na vzdálené ověřování.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="f0ba1-121">Nahrazuje všechny jednotlivé komponenty middlewaru jedinou běžnou komponentou middlewaru.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="f0ba1-122">Níže jsou uvedené 2,0 pokyny k migraci pro každé hlavní schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="f0ba1-123">Ověřování na základě souborů cookie</span><span class="sxs-lookup"><span data-stu-id="f0ba1-123">Cookie-based authentication</span></span>

<span data-ttu-id="f0ba1-124">Vyberte jednu z následujících dvou možností a proveďte potřebné změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-124">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="f0ba1-125">Použít soubory cookie sIdentity</span><span class="sxs-lookup"><span data-stu-id="f0ba1-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="f0ba1-126">`UseIdentity` Nahraďte `UseAuthentication` v `Configure` metodě:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="f0ba1-127">Voláním `AddIdentity` metody v `ConfigureServices` metodě přidejte ověřovací služby souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="f0ba1-128">Volitelně můžete vyvolat metodu `ConfigureApplicationCookie` nebo `ConfigureExternalCookie` v `ConfigureServices` metodě pro vylepšení nastavení Identity souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="f0ba1-129">Používat soubory cookie bezIdentity</span><span class="sxs-lookup"><span data-stu-id="f0ba1-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="f0ba1-130">Nahraďte `UseCookieAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="f0ba1-131">Vyvolat metody `AddAuthentication` a `AddCookie` v `ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="f0ba1-132">Ověření nosiče JWT</span><span class="sxs-lookup"><span data-stu-id="f0ba1-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="f0ba1-133">V *Startup.cs*proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-133">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f0ba1-134">Nahraďte `UseJwtBearerAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f0ba1-135">Vyvolat `AddJwtBearer` metodu v `ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="f0ba1-136">Tento fragment kódu nepoužívá Identity, proto je třeba nastavit výchozí schéma předáním `JwtBearerDefaults.AuthenticationScheme` do `AddAuthentication` metody.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="f0ba1-137">Ověřování OpenID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="f0ba1-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="f0ba1-138">V *Startup.cs*proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-138">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="f0ba1-139">Nahraďte `UseOpenIdConnectAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f0ba1-140">Vyvolat `AddOpenIdConnect` metodu v `ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="f0ba1-141">Nahraďte `PostLogoutRedirectUri` vlastnost v `OpenIdConnectOptions` akci pomocí `SignedOutRedirectUri`:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="f0ba1-142">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="f0ba1-142">Facebook authentication</span></span>

<span data-ttu-id="f0ba1-143">V *Startup.cs*proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-143">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f0ba1-144">Nahraďte `UseFacebookAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f0ba1-145">Vyvolat `AddFacebook` metodu v `ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="f0ba1-146">Ověřování Googlu</span><span class="sxs-lookup"><span data-stu-id="f0ba1-146">Google authentication</span></span>

<span data-ttu-id="f0ba1-147">V *Startup.cs*proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-147">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f0ba1-148">Nahraďte `UseGoogleAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f0ba1-149">Vyvolat `AddGoogle` metodu v `ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="f0ba1-150">Ověřování pomocí účtu Microsoft</span><span class="sxs-lookup"><span data-stu-id="f0ba1-150">Microsoft Account authentication</span></span>

<span data-ttu-id="f0ba1-151">Další informace o ověřování účet Microsoft najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span><span class="sxs-lookup"><span data-stu-id="f0ba1-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="f0ba1-152">V *Startup.cs*proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-152">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f0ba1-153">Nahraďte `UseMicrosoftAccountAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f0ba1-154">Vyvolat `AddMicrosoftAccount` metodu v `ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="f0ba1-155">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="f0ba1-155">Twitter authentication</span></span>

<span data-ttu-id="f0ba1-156">V *Startup.cs*proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-156">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="f0ba1-157">Nahraďte `UseTwitterAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="f0ba1-158">Vyvolat `AddTwitter` metodu v `ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="f0ba1-159">Nastavení výchozích schémat ověřování</span><span class="sxs-lookup"><span data-stu-id="f0ba1-159">Setting default authentication schemes</span></span>

<span data-ttu-id="f0ba1-160">V 1. x byly vlastnosti `AutomaticAuthenticate` a `AutomaticChallenge` základní třídy [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) určeny k nastavení v jednom schématu ověřování.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="f0ba1-161">Tento způsob vykonání není dobrý.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="f0ba1-162">V 2,0 byly tyto dvě vlastnosti odebrány jako vlastnosti v jednotlivých `AuthenticationOptions` instancích.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="f0ba1-163">Lze je nakonfigurovat ve volání `AddAuthentication` metody v rámci `ConfigureServices` metody *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="f0ba1-164">V předchozím fragmentu kódu je výchozí schéma nastaveno na `CookieAuthenticationDefaults.AuthenticationScheme` ("soubory cookie").</span><span class="sxs-lookup"><span data-stu-id="f0ba1-164">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="f0ba1-165">Alternativně můžete pomocí přetížené verze `AddAuthentication` metody nastavit více než jednu vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="f0ba1-166">V následujícím příkladu přetížené metody je výchozí schéma nastaveno na `CookieAuthenticationDefaults.AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-166">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="f0ba1-167">Schéma ověřování může být případně zadáno v rámci svých individuálních `[Authorize]` atributů nebo zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="f0ba1-168">Definujte výchozí schéma v 2,0, pokud je splněna jedna z následujících podmínek:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="f0ba1-169">Chcete, aby byl uživatel automaticky přihlášen</span><span class="sxs-lookup"><span data-stu-id="f0ba1-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="f0ba1-170">Zásady ověřování použijete `[Authorize]` bez zadání schémat.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="f0ba1-171">Výjimkou z `AddIdentity` tohoto pravidla je metoda.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-171">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="f0ba1-172">Tato metoda přidá soubory cookie pro vás a nastaví výchozí ověřování a schémata pro dotaz na soubor cookie `IdentityConstants.ApplicationScheme`aplikace.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-172">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="f0ba1-173">Kromě toho nastaví výchozí schéma přihlášení na externí soubor cookie `IdentityConstants.ExternalScheme`.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-173">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="f0ba1-174">Použití ověřovacích rozšíření HttpContext</span><span class="sxs-lookup"><span data-stu-id="f0ba1-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="f0ba1-175">`IAuthenticationManager` Rozhraní je hlavní vstupní bod do systému ověřování 1. x.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="f0ba1-176">Byl nahrazen novou sadou `HttpContext` rozšiřujících metod v `Microsoft.AspNetCore.Authentication` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="f0ba1-177">Například projekty 1. x odkazují na `Authentication` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="f0ba1-178">V 2,0 projektech importujte `Microsoft.AspNetCore.Authentication` obor názvů a odstraňte odkazy na `Authentication` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="f0ba1-179">Ověřování systému Windows (HTTP. sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="f0ba1-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="f0ba1-180">K dispozici jsou dvě varianty ověřování systému Windows:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="f0ba1-181">Hostitel povoluje pouze ověřené uživatele.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-181">The host only allows authenticated users.</span></span> <span data-ttu-id="f0ba1-182">Tato variace není ovlivněna 2,0 změnami.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="f0ba1-183">Hostitel umožňuje anonymní i ověřené uživatele.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="f0ba1-184">Tato variace má vliv na změny 2,0.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="f0ba1-185">Například aplikace by měla umožňovat anonymní uživatele na vrstvě [IIS](xref:host-and-deploy/iis/index) nebo [http. sys](xref:fundamentals/servers/httpsys) , ale autorizuje uživatele na úrovni řadiče.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="f0ba1-186">V tomto scénáři nastavte výchozí schéma v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="f0ba1-187">Pro [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)nastavte výchozí schéma na `IISDefaults.AuthenticationScheme`:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="f0ba1-188">Pro [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)nastavte výchozí schéma na `HttpSysDefaults.AuthenticationScheme`:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="f0ba1-189">Nepovedlo se nastavit výchozí schéma, aby žádost o ověření (Challenge) nefungovala s následující výjimkou:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="f0ba1-190">`System.InvalidOperationException`: Není zadaný žádný authenticationScheme a nenašel se žádný DefaultChallengeScheme.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="f0ba1-191">Další informace naleznete v tématu <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="f0ba1-192">Instance IdentityCookieOptions</span><span class="sxs-lookup"><span data-stu-id="f0ba1-192">IdentityCookieOptions instances</span></span>

<span data-ttu-id="f0ba1-193">Vedlejší efekt 2,0 změn je místo instancí možností souborů cookie přepínač pomocí pojmenovaných možností.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-193">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="f0ba1-194">Možnost přizpůsobení názvů schémat Identity souborů cookie se odebere.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-194">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="f0ba1-195">Například projekty 1. x používají [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) k předání `IdentityCookieOptions` parametru do *AccountController.cs* a *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs*.</span></span> <span data-ttu-id="f0ba1-196">K externímu schématu ověřování souborů cookie se dostanete ze zadané instance:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-196">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="f0ba1-197">Výše uvedený převstřik konstruktoru se v projektech 2,0 stal zbytečným `_externalCookieScheme` a pole je možné odstranit:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="f0ba1-198">projekty 1. x používaly `_externalCookieScheme` pole následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-198">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="f0ba1-199">V projektech 2,0 nahraďte předchozí kód následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="f0ba1-200">`IdentityConstants.ExternalScheme` Konstantu lze použít přímo.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-200">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="f0ba1-201">Pomocí importu následujícího oboru `SignOutAsync` názvů vyřešte nově přidané volání:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="f0ba1-202">Přidat vlastnosti navigace IdentityUser POCO</span><span class="sxs-lookup"><span data-stu-id="f0ba1-202">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="f0ba1-203">Byla odebrána základní navigační vlastnost Entity Framework (EF) základního `IdentityUser` POCO (prostý starý objekt CLR).</span><span class="sxs-lookup"><span data-stu-id="f0ba1-203">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="f0ba1-204">Pokud váš projekt 1. x tyto vlastnosti používá, přidejte je ručně zpátky do projektu 2,0:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

<span data-ttu-id="f0ba1-205">Chcete-li zabránit duplicitním cizím klíčům při spuštění EF Core migrace, přidejte `IdentityDbContext` následující do `OnModelCreating` metody Class (po `base.OnModelCreating();` volání):</span><span class="sxs-lookup"><span data-stu-id="f0ba1-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="f0ba1-206">Nahradit GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="f0ba1-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="f0ba1-207">Synchronní metoda `GetExternalAuthenticationSchemes` byla odebrána namísto asynchronní verze.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="f0ba1-208">projekty 1. x mají následující kód v *Controllers/ManageController. cs*:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-208">1.x projects have the following code in *Controllers/ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="f0ba1-209">Tato metoda se zobrazí v *zobrazeních/účtech/přihlašovacích údajích. cshtml* je také:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="f0ba1-210">V projektech 2,0 použijte <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> metodu.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="f0ba1-211">Změna v *ManageController.cs* se podobá následujícímu kódu:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="f0ba1-212">V *Login. cshtml*se `AuthenticationScheme` vlastnost, ke které se `foreach` přistupovalo `Name`v smyčce, změní na:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-212">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="f0ba1-213">Změna vlastnosti ManageLoginsViewModel</span><span class="sxs-lookup"><span data-stu-id="f0ba1-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="f0ba1-214">`ManageLoginsViewModel` Objekt se používá v `ManageLogins` akci *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="f0ba1-215">V projektech 1. x je `OtherLogins` `IList<AuthenticationDescription>`návratový typ vlastnosti objektu.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="f0ba1-216">Tento návratový typ vyžaduje import `Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="f0ba1-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="f0ba1-217">V projektech 2,0 se návratový typ změní na `IList<AuthenticationScheme>`.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="f0ba1-218">Tento nový návratový typ vyžaduje nahrazení `Microsoft.AspNetCore.Http.Authentication` importu pomocí `Microsoft.AspNetCore.Authentication` importu.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="f0ba1-219">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f0ba1-219">Additional resources</span></span>

<span data-ttu-id="f0ba1-220">Další informace najdete v [diskuzi k problému ověření 2,0](https://github.com/aspnet/Security/issues/1338) na GitHubu.</span><span class="sxs-lookup"><span data-stu-id="f0ba1-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>

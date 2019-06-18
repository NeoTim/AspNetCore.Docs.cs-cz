---
title: Ověřování a identitu migrovat do ASP.NET Core 2.0
author: scottaddie
description: Tento článek popisuje nejběžnější postup pro migraci ASP.NET Core 1.x ověřování a identita pro ASP.NET Core 2.0.
ms.author: scaddie
ms.date: 06/13/2019
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 3e8bc75b87a85159c9668b52eea32bb7d700be6c
ms.sourcegitcommit: 516f166c5f7cec54edf3d9c71e6e2ba53fb3b0e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67196373"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="38312-103">Ověřování a identitu migrovat do ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="38312-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="38312-104">Podle [Scott Addie](https://github.com/scottaddie) a [ani Haovi společnosti](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="38312-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="38312-105">ASP.NET Core 2.0 je nový model pro ověřování a [Identity](xref:security/authentication/identity) , který zjednodušuje konfiguraci služby.</span><span class="sxs-lookup"><span data-stu-id="38312-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="38312-106">Aplikace ASP.NET Core 1.x, které používají ověřování nebo Identity lze aktualizovat pomocí nového modelu, jak je uvedeno níže.</span><span class="sxs-lookup"><span data-stu-id="38312-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="38312-107">Aktualizovat obory názvů</span><span class="sxs-lookup"><span data-stu-id="38312-107">Update namespaces</span></span>

<span data-ttu-id="38312-108">V 1.x, třídy, například `IdentityRole` a `IdentityUser` nebyly nalezeny v `Microsoft.AspNetCore.Identity.EntityFrameworkCore` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="38312-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="38312-109">Ve verzi 2.0 <xref:Microsoft.AspNetCore.Identity> obor názvů stal novým výchozím místem pro některé z těchto tříd.</span><span class="sxs-lookup"><span data-stu-id="38312-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="38312-110">S kódem Identity výchozí ovlivněné třídy zahrnují `ApplicationUser` a `Startup`.</span><span class="sxs-lookup"><span data-stu-id="38312-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="38312-111">Upravit vaše `using` příkazy ovlivněné odkazy.</span><span class="sxs-lookup"><span data-stu-id="38312-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="38312-112">Ověřovací Middleware a služby</span><span class="sxs-lookup"><span data-stu-id="38312-112">Authentication Middleware and services</span></span>

<span data-ttu-id="38312-113">V projektech 1.x je nakonfigurované ověřování prostřednictvím middlewaru.</span><span class="sxs-lookup"><span data-stu-id="38312-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="38312-114">Middleware metoda je volána pro každé schéma ověřování, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="38312-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="38312-115">V následujícím příkladu 1.x konfiguruje s identitou v ověřování přes síť Facebook *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38312-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="38312-116">V projektech, 2.0 je nakonfigurované ověřování prostřednictvím služby.</span><span class="sxs-lookup"><span data-stu-id="38312-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="38312-117">Každé schéma ověřování je registrován v `ConfigureServices` metoda *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="38312-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="38312-118">`UseIdentity` Metoda je nahrazen `UseAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="38312-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="38312-119">V následujícím příkladu 2.0 konfiguruje s identitou v ověřování přes síť Facebook *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38312-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="38312-120">`UseAuthentication` Metoda přidá komponenta jednoho ověřovacího middlewaru, který je zodpovědný za automatickou ověření a zpracování žádostí o vzdálené ověření.</span><span class="sxs-lookup"><span data-stu-id="38312-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="38312-121">Nahradí všechny komponenty middlewaru jednotlivých součástí jedné, běžné middlewaru.</span><span class="sxs-lookup"><span data-stu-id="38312-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="38312-122">Níže jsou uvedeny pokyny k migraci 2.0 pro každé schéma hlavní ověřování.</span><span class="sxs-lookup"><span data-stu-id="38312-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="38312-123">Ověřování na základě souborů cookie</span><span class="sxs-lookup"><span data-stu-id="38312-123">Cookie-based authentication</span></span>

<span data-ttu-id="38312-124">Vyberte jednu z následujících dvou možností a proveďte potřebné změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38312-124">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="38312-125">Použít soubory cookie s využitím Identity</span><span class="sxs-lookup"><span data-stu-id="38312-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="38312-126">Nahraďte `UseIdentity` s `UseAuthentication` v `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="38312-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="38312-127">Vyvolat `AddIdentity` metodu `ConfigureServices` metoda pro přidání služby ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="38312-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="38312-128">Volitelně můžete vyvolat `ConfigureApplicationCookie` nebo `ConfigureExternalCookie` metodu `ConfigureServices` metoda upravit nastavení souborů cookie Identity.</span><span class="sxs-lookup"><span data-stu-id="38312-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="38312-129">Použít soubory cookie bez systému Identity</span><span class="sxs-lookup"><span data-stu-id="38312-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="38312-130">Nahradit `UseCookieAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="38312-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="38312-131">Vyvolat `AddAuthentication` a `AddCookie` metody v `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="38312-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="38312-132">Ověřování nosného tokenu JWT</span><span class="sxs-lookup"><span data-stu-id="38312-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="38312-133">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38312-133">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="38312-134">Nahradit `UseJwtBearerAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="38312-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="38312-135">Vyvolat `AddJwtBearer` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="38312-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="38312-136">Tento fragment kódu nepoužívá identitu, abyste výchozí schéma by měl nastavit předáním `JwtBearerDefaults.AuthenticationScheme` k `AddAuthentication` metody.</span><span class="sxs-lookup"><span data-stu-id="38312-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="38312-137">Ověřování OpenID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="38312-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="38312-138">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38312-138">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="38312-139">Nahradit `UseOpenIdConnectAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="38312-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="38312-140">Vyvolat `AddOpenIdConnect` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="38312-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

- <span data-ttu-id="38312-141">Nahradit `PostLogoutRedirectUri` vlastnost `OpenIdConnectOptions` akce s `SignedOutRedirectUri`:</span><span class="sxs-lookup"><span data-stu-id="38312-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="38312-142">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="38312-142">Facebook authentication</span></span>

<span data-ttu-id="38312-143">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38312-143">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="38312-144">Nahradit `UseFacebookAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="38312-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="38312-145">Vyvolat `AddFacebook` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="38312-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="38312-146">Ověřování Googlu</span><span class="sxs-lookup"><span data-stu-id="38312-146">Google authentication</span></span>

<span data-ttu-id="38312-147">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38312-147">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="38312-148">Nahradit `UseGoogleAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="38312-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="38312-149">Vyvolat `AddGoogle` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="38312-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="38312-150">Ověřování pomocí Account Microsoft</span><span class="sxs-lookup"><span data-stu-id="38312-150">Microsoft Account authentication</span></span>

<span data-ttu-id="38312-151">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38312-151">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="38312-152">Nahradit `UseMicrosoftAccountAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="38312-152">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="38312-153">Vyvolat `AddMicrosoftAccount` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="38312-153">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="38312-154">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="38312-154">Twitter authentication</span></span>

<span data-ttu-id="38312-155">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38312-155">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="38312-156">Nahradit `UseTwitterAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="38312-156">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="38312-157">Vyvolat `AddTwitter` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="38312-157">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="38312-158">Nastavení výchozích schémat ověřování</span><span class="sxs-lookup"><span data-stu-id="38312-158">Setting default authentication schemes</span></span>

<span data-ttu-id="38312-159">V 1.x `AutomaticAuthenticate` a `AutomaticChallenge` vlastnosti [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) základní třídy byly v úmyslu nastavit na jedno schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="38312-159">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="38312-160">Neexistoval dobrý způsob, jak to chcete vynutit.</span><span class="sxs-lookup"><span data-stu-id="38312-160">There was no good way to enforce this.</span></span>

<span data-ttu-id="38312-161">Ve verzi 2.0, se odebraly tyto dvě vlastnosti jako vlastnosti na jednotlivých `AuthenticationOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="38312-161">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="38312-162">Se dají konfigurovat v `AddAuthentication` volání metody v rámci `ConfigureServices` metoda *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="38312-162">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="38312-163">V předchozím fragmentu kódu, výchozí schéma je nastavený na `CookieAuthenticationDefaults.AuthenticationScheme` (soubory cookie.").</span><span class="sxs-lookup"><span data-stu-id="38312-163">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="38312-164">Alternativně použijte přetížené verze `AddAuthentication` metoda nastavit více než jednu vlastnost.</span><span class="sxs-lookup"><span data-stu-id="38312-164">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="38312-165">V následujícím příkladu přetěžované metody, nebo výchozí schéma je nastaveno na `CookieAuthenticationDefaults.AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="38312-165">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="38312-166">Případně lze zadat schéma ověřování v rámci svůj individuální `[Authorize]` atributy nebo zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="38312-166">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="38312-167">Definujte výchozí schéma ve verzi 2.0, pokud platí jedna z následujících podmínek:</span><span class="sxs-lookup"><span data-stu-id="38312-167">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="38312-168">Chcete uživateli být automaticky přihlášeni</span><span class="sxs-lookup"><span data-stu-id="38312-168">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="38312-169">Můžete použít `[Authorize]` atribut nebo autorizační zásady bez zadání schémata</span><span class="sxs-lookup"><span data-stu-id="38312-169">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="38312-170">Výjimkou z tohoto pravidla je `AddIdentity` metody.</span><span class="sxs-lookup"><span data-stu-id="38312-170">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="38312-171">Tato metoda přidá soubory cookie pro vás a nastaví výchozí nastavení ověřování a ověřovací schémata pro soubor cookie aplikace `IdentityConstants.ApplicationScheme`.</span><span class="sxs-lookup"><span data-stu-id="38312-171">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="38312-172">Kromě toho nastaví výchozí přihlášení schéma na externí soubor cookie `IdentityConstants.ExternalScheme`.</span><span class="sxs-lookup"><span data-stu-id="38312-172">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="38312-173">Použití rozšíření ověřování HttpContext</span><span class="sxs-lookup"><span data-stu-id="38312-173">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="38312-174">`IAuthenticationManager` Rozhraní je hlavní vstupní bod do 1.x ověřovacího systému.</span><span class="sxs-lookup"><span data-stu-id="38312-174">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="38312-175">Nahradili jsme ho s novou sadu `HttpContext` rozšiřující metody v `Microsoft.AspNetCore.Authentication` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="38312-175">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="38312-176">Například 1.x projekty referenční dokumentace `Authentication` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="38312-176">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="38312-177">V projektech, 2.0, import `Microsoft.AspNetCore.Authentication` obor názvů a odstranit `Authentication` odkazy na vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="38312-177">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="38312-178">Windows Authentication (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="38312-178">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="38312-179">Existují dvě varianty ověřování Windows:</span><span class="sxs-lookup"><span data-stu-id="38312-179">There are two variations of Windows authentication:</span></span>
1. <span data-ttu-id="38312-180">Hostitel umožňuje pouze ověřeným uživatelům</span><span class="sxs-lookup"><span data-stu-id="38312-180">The host only allows authenticated users</span></span>
2. <span data-ttu-id="38312-181">Oba hostitele umožňuje anonymní a ověření uživatelé</span><span class="sxs-lookup"><span data-stu-id="38312-181">The host allows both anonymous and authenticated users</span></span>

<span data-ttu-id="38312-182">První výše popsané není ovlivněn 2.0 změny.</span><span class="sxs-lookup"><span data-stu-id="38312-182">The first variation described above is unaffected by the 2.0 changes.</span></span>

<span data-ttu-id="38312-183">Druhá je popsáno výše je ovlivněny změnami v 2.0.</span><span class="sxs-lookup"><span data-stu-id="38312-183">The second variation described above is affected by the 2.0 changes.</span></span> <span data-ttu-id="38312-184">Například je může být umožní anonymní uživatelé do vaší aplikace v IIS nebo [HTTP.sys](xref:fundamentals/servers/httpsys) vrstvy ale autorizací uživatele na úrovni Kontroleru.</span><span class="sxs-lookup"><span data-stu-id="38312-184">For example, you may be allowing anonymous users into your app at the IIS or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorizing users at the Controller level.</span></span> <span data-ttu-id="38312-185">V tomto scénáři, nastavte výchozí schéma na `IISDefaults.AuthenticationScheme` v `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="38312-185">In this scenario, set the default scheme to `IISDefaults.AuthenticationScheme` in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="38312-186">Nepodařilo se nastavit výchozí schéma brání vybízí funkčním požadavku authorize.</span><span class="sxs-lookup"><span data-stu-id="38312-186">Failure to set the default scheme prevents the authorize request to challenge from working.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="38312-187">IdentityCookieOptions instances</span><span class="sxs-lookup"><span data-stu-id="38312-187">IdentityCookieOptions instances</span></span>

<span data-ttu-id="38312-188">Vedlejším účinkem 2.0 změny se přepnout na používání s názvem možnosti namísto souboru cookie možnosti instance.</span><span class="sxs-lookup"><span data-stu-id="38312-188">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="38312-189">Odebere se možnost přizpůsobit si názvy schémat souboru cookie Identity.</span><span class="sxs-lookup"><span data-stu-id="38312-189">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="38312-190">Například 1.x projekty použití [konstruktor vkládání](xref:mvc/controllers/dependency-injection#constructor-injection) předat `IdentityCookieOptions` parametr do *AccountController.cs* a *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="38312-190">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs*.</span></span> <span data-ttu-id="38312-191">Schéma externí soubor cookie ověřování přistupuje z zadanou instanci:</span><span class="sxs-lookup"><span data-stu-id="38312-191">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="38312-192">Vkládání výše uvedené konstruktor stane zbytečné v projektech pro 2.0 a `_externalCookieScheme` je možné pole odstranit:</span><span class="sxs-lookup"><span data-stu-id="38312-192">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="38312-193">1.x projektů používaných `_externalCookieScheme` pole následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="38312-193">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="38312-194">V projektech, 2.0 nahraďte předchozí kód následujícím kódem.</span><span class="sxs-lookup"><span data-stu-id="38312-194">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="38312-195">`IdentityConstants.ExternalScheme` – Konstanta je možné přímo.</span><span class="sxs-lookup"><span data-stu-id="38312-195">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="38312-196">Vyřešit nově přidaný `SignOutAsync` volání importováním následující obor názvů:</span><span class="sxs-lookup"><span data-stu-id="38312-196">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="38312-197">Přidat IdentityUser objektů POCO navigační vlastnosti</span><span class="sxs-lookup"><span data-stu-id="38312-197">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="38312-198">Navigační vlastnosti Entity Framework (EF) Core základní třídy `IdentityUser` odebrané POCO (prostý staré CLR objekt).</span><span class="sxs-lookup"><span data-stu-id="38312-198">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="38312-199">Pokud váš projekt 1.x tyto vlastnosti, ručně přidáte do projektu 2.0:</span><span class="sxs-lookup"><span data-stu-id="38312-199">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="38312-200">Aby se zabránilo duplicitní cizí klíče při spuštění migrace EF Core, přidejte následující text do vaší `IdentityDbContext` třídy `OnModelCreating` – metoda (po `base.OnModelCreating();` volání):</span><span class="sxs-lookup"><span data-stu-id="38312-200">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="38312-201">Nahraďte GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="38312-201">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="38312-202">Synchronní metoda `GetExternalAuthenticationSchemes` byla odebrána a místo toho použití asynchronní verze.</span><span class="sxs-lookup"><span data-stu-id="38312-202">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="38312-203">projekty 1.x mají následující kód *Controllers/ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="38312-203">1.x projects have the following code in *Controllers/ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="38312-204">Tato metoda se zobrazí v *Views/Account/Login.cshtml* příliš:</span><span class="sxs-lookup"><span data-stu-id="38312-204">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="38312-205">V projektech, 2.0, použijte <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> metody.</span><span class="sxs-lookup"><span data-stu-id="38312-205">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="38312-206">Změnu v hodnotě *ManageController.cs* vypadá podobně jako následující kód:</span><span class="sxs-lookup"><span data-stu-id="38312-206">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="38312-207">V *Login.cshtml*, `AuthenticationScheme` přistupuje ve vlastnosti `foreach` smyčky se změní na `Name`:</span><span class="sxs-lookup"><span data-stu-id="38312-207">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="38312-208">Změna vlastnosti ManageLoginsViewModel</span><span class="sxs-lookup"><span data-stu-id="38312-208">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="38312-209">A `ManageLoginsViewModel` objekt se používá v `ManageLogins` akce *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="38312-209">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="38312-210">V 1.x projekty, objekt společnosti `OtherLogins` vlastnosti je návratový typ `IList<AuthenticationDescription>`.</span><span class="sxs-lookup"><span data-stu-id="38312-210">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="38312-211">Tento návratový typ vyžaduje importu `Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="38312-211">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="38312-212">V projektech, 2.0, návratový typ se změní na `IList<AuthenticationScheme>`.</span><span class="sxs-lookup"><span data-stu-id="38312-212">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="38312-213">Tento nový návratový typ vyžaduje nahrazení `Microsoft.AspNetCore.Http.Authentication` importovat `Microsoft.AspNetCore.Authentication` importovat.</span><span class="sxs-lookup"><span data-stu-id="38312-213">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="38312-214">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="38312-214">Additional resources</span></span>

<span data-ttu-id="38312-215">Další informace najdete v tématu [diskuse Auth 2.0](https://github.com/aspnet/Security/issues/1338) problém na Githubu.</span><span class="sxs-lookup"><span data-stu-id="38312-215">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>

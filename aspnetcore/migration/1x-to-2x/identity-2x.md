---
title: Ověřování a identitu migrovat do ASP.NET Core 2.0
author: scottaddie
description: Tento článek popisuje nejběžnější postup pro migraci ASP.NET Core 1.x ověřování a identita pro ASP.NET Core 2.0.
ms.author: scaddie
ms.date: 12/18/2018
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: d11d41c82236436096660a24df81a3df4da0fb8e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898731"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="2b51f-103">Ověřování a identitu migrovat do ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="2b51f-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="2b51f-104">Podle [Scott Addie](https://github.com/scottaddie) a [ani Haovi společnosti](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="2b51f-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="2b51f-105">ASP.NET Core 2.0 je nový model pro ověřování a [Identity](xref:security/authentication/identity) který zjednodušuje konfiguraci služby.</span><span class="sxs-lookup"><span data-stu-id="2b51f-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) which simplifies configuration by using services.</span></span> <span data-ttu-id="2b51f-106">Aplikace ASP.NET Core 1.x, které používají ověřování nebo Identity lze aktualizovat pomocí nového modelu, jak je uvedeno níže.</span><span class="sxs-lookup"><span data-stu-id="2b51f-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="2b51f-107">Ověřovací Middleware a služby</span><span class="sxs-lookup"><span data-stu-id="2b51f-107">Authentication Middleware and services</span></span>

<span data-ttu-id="2b51f-108">V projektech 1.x je nakonfigurované ověřování prostřednictvím middlewaru.</span><span class="sxs-lookup"><span data-stu-id="2b51f-108">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="2b51f-109">Middleware metoda je volána pro každé schéma ověřování, které chcete podporovat.</span><span class="sxs-lookup"><span data-stu-id="2b51f-109">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="2b51f-110">V následujícím příkladu 1.x konfiguruje s identitou v ověřování přes síť Facebook *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b51f-110">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="2b51f-111">V projektech, 2.0 je nakonfigurované ověřování prostřednictvím služby.</span><span class="sxs-lookup"><span data-stu-id="2b51f-111">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="2b51f-112">Každé schéma ověřování je registrován v `ConfigureServices` metoda *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="2b51f-112">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="2b51f-113">`UseIdentity` Metoda je nahrazen `UseAuthentication`.</span><span class="sxs-lookup"><span data-stu-id="2b51f-113">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="2b51f-114">V následujícím příkladu 2.0 konfiguruje s identitou v ověřování přes síť Facebook *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b51f-114">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="2b51f-115">`UseAuthentication` Metoda přidá komponenta jednoho ověřovacího middlewaru, který je zodpovědný za automatickou ověření a zpracování žádostí o vzdálené ověření.</span><span class="sxs-lookup"><span data-stu-id="2b51f-115">The `UseAuthentication` method adds a single authentication middleware component which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="2b51f-116">Nahradí všechny komponenty middlewaru jednotlivých součástí jedné, běžné middlewaru.</span><span class="sxs-lookup"><span data-stu-id="2b51f-116">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="2b51f-117">Níže jsou uvedeny pokyny k migraci 2.0 pro každé schéma hlavní ověřování.</span><span class="sxs-lookup"><span data-stu-id="2b51f-117">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="2b51f-118">Ověřování na základě souborů cookie</span><span class="sxs-lookup"><span data-stu-id="2b51f-118">Cookie-based authentication</span></span>

<span data-ttu-id="2b51f-119">Vyberte jednu z následujících dvou možností a proveďte potřebné změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b51f-119">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="2b51f-120">Použít soubory cookie s využitím Identity</span><span class="sxs-lookup"><span data-stu-id="2b51f-120">Use cookies with Identity</span></span>
    - <span data-ttu-id="2b51f-121">Nahraďte `UseIdentity` s `UseAuthentication` v `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="2b51f-121">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="2b51f-122">Vyvolat `AddIdentity` metodu `ConfigureServices` metoda pro přidání služby ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="2b51f-122">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="2b51f-123">Volitelně můžete vyvolat `ConfigureApplicationCookie` nebo `ConfigureExternalCookie` metodu `ConfigureServices` metoda upravit nastavení souborů cookie Identity.</span><span class="sxs-lookup"><span data-stu-id="2b51f-123">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="2b51f-124">Použít soubory cookie bez systému Identity</span><span class="sxs-lookup"><span data-stu-id="2b51f-124">Use cookies without Identity</span></span>
    - <span data-ttu-id="2b51f-125">Nahradit `UseCookieAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2b51f-125">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="2b51f-126">Vyvolat `AddAuthentication` a `AddCookie` metody v `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="2b51f-126">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="2b51f-127">Ověřování nosného tokenu JWT</span><span class="sxs-lookup"><span data-stu-id="2b51f-127">JWT Bearer Authentication</span></span>

<span data-ttu-id="2b51f-128">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b51f-128">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="2b51f-129">Nahradit `UseJwtBearerAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2b51f-129">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2b51f-130">Vyvolat `AddJwtBearer` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="2b51f-130">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="2b51f-131">Tento fragment kódu nepoužívá identitu, abyste výchozí schéma by měl nastavit předáním `JwtBearerDefaults.AuthenticationScheme` k `AddAuthentication` metody.</span><span class="sxs-lookup"><span data-stu-id="2b51f-131">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="2b51f-132">Ověřování OpenID Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="2b51f-132">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="2b51f-133">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b51f-133">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="2b51f-134">Nahradit `UseOpenIdConnectAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2b51f-134">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2b51f-135">Vyvolat `AddOpenIdConnect` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="2b51f-135">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

### <a name="facebook-authentication"></a><span data-ttu-id="2b51f-136">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="2b51f-136">Facebook authentication</span></span>

<span data-ttu-id="2b51f-137">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b51f-137">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="2b51f-138">Nahradit `UseFacebookAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2b51f-138">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2b51f-139">Vyvolat `AddFacebook` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="2b51f-139">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="2b51f-140">Ověřování Googlu</span><span class="sxs-lookup"><span data-stu-id="2b51f-140">Google authentication</span></span>

<span data-ttu-id="2b51f-141">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b51f-141">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="2b51f-142">Nahradit `UseGoogleAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2b51f-142">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2b51f-143">Vyvolat `AddGoogle` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="2b51f-143">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="2b51f-144">Ověřování pomocí Account Microsoft</span><span class="sxs-lookup"><span data-stu-id="2b51f-144">Microsoft Account authentication</span></span>

<span data-ttu-id="2b51f-145">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b51f-145">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="2b51f-146">Nahradit `UseMicrosoftAccountAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2b51f-146">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2b51f-147">Vyvolat `AddMicrosoftAccount` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="2b51f-147">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="2b51f-148">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="2b51f-148">Twitter authentication</span></span>

<span data-ttu-id="2b51f-149">Proveďte následující změny v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b51f-149">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="2b51f-150">Nahradit `UseTwitterAuthentication` volání metody `Configure` metodu s `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="2b51f-150">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="2b51f-151">Vyvolat `AddTwitter` metoda ve `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="2b51f-151">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="2b51f-152">Nastavení výchozích schémat ověřování</span><span class="sxs-lookup"><span data-stu-id="2b51f-152">Setting default authentication schemes</span></span>

<span data-ttu-id="2b51f-153">V 1.x `AutomaticAuthenticate` a `AutomaticChallenge` vlastnosti [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) základní třídy byly v úmyslu nastavit na jedno schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="2b51f-153">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="2b51f-154">Neexistoval dobrý způsob, jak to chcete vynutit.</span><span class="sxs-lookup"><span data-stu-id="2b51f-154">There was no good way to enforce this.</span></span>

<span data-ttu-id="2b51f-155">Ve verzi 2.0, se odebraly tyto dvě vlastnosti jako vlastnosti na jednotlivých `AuthenticationOptions` instance.</span><span class="sxs-lookup"><span data-stu-id="2b51f-155">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="2b51f-156">Se dají konfigurovat v `AddAuthentication` volání metody v rámci `ConfigureServices` metoda *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b51f-156">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="2b51f-157">V předchozím fragmentu kódu, výchozí schéma je nastavený na `CookieAuthenticationDefaults.AuthenticationScheme` (soubory cookie.").</span><span class="sxs-lookup"><span data-stu-id="2b51f-157">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="2b51f-158">Alternativně použijte přetížené verze `AddAuthentication` metoda nastavit více než jednu vlastnost.</span><span class="sxs-lookup"><span data-stu-id="2b51f-158">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="2b51f-159">V následujícím příkladu přetěžované metody, nebo výchozí schéma je nastaveno na `CookieAuthenticationDefaults.AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="2b51f-159">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="2b51f-160">Případně lze zadat schéma ověřování v rámci svůj individuální `[Authorize]` atributy nebo zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="2b51f-160">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="2b51f-161">Definujte výchozí schéma ve verzi 2.0, pokud platí jedna z následujících podmínek:</span><span class="sxs-lookup"><span data-stu-id="2b51f-161">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="2b51f-162">Chcete uživateli být automaticky přihlášeni</span><span class="sxs-lookup"><span data-stu-id="2b51f-162">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="2b51f-163">Můžete použít `[Authorize]` atribut nebo autorizační zásady bez zadání schémata</span><span class="sxs-lookup"><span data-stu-id="2b51f-163">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="2b51f-164">Výjimkou z tohoto pravidla je `AddIdentity` metody.</span><span class="sxs-lookup"><span data-stu-id="2b51f-164">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="2b51f-165">Tato metoda přidá soubory cookie pro vás a nastaví výchozí nastavení ověřování a ověřovací schémata pro soubor cookie aplikace `IdentityConstants.ApplicationScheme`.</span><span class="sxs-lookup"><span data-stu-id="2b51f-165">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="2b51f-166">Kromě toho nastaví výchozí přihlášení schéma na externí soubor cookie `IdentityConstants.ExternalScheme`.</span><span class="sxs-lookup"><span data-stu-id="2b51f-166">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="2b51f-167">Použití rozšíření ověřování HttpContext</span><span class="sxs-lookup"><span data-stu-id="2b51f-167">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="2b51f-168">`IAuthenticationManager` Rozhraní je hlavní vstupní bod do 1.x ověřovacího systému.</span><span class="sxs-lookup"><span data-stu-id="2b51f-168">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="2b51f-169">Nahradili jsme ho s novou sadu `HttpContext` rozšiřující metody v `Microsoft.AspNetCore.Authentication` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="2b51f-169">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="2b51f-170">Například 1.x projekty referenční dokumentace `Authentication` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="2b51f-170">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="2b51f-171">V projektech, 2.0, import `Microsoft.AspNetCore.Authentication` obor názvů a odstranit `Authentication` odkazy na vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2b51f-171">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="2b51f-172">Windows Authentication (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="2b51f-172">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="2b51f-173">Existují dvě varianty ověřování Windows:</span><span class="sxs-lookup"><span data-stu-id="2b51f-173">There are two variations of Windows authentication:</span></span>
1. <span data-ttu-id="2b51f-174">Hostitel umožňuje pouze ověřeným uživatelům</span><span class="sxs-lookup"><span data-stu-id="2b51f-174">The host only allows authenticated users</span></span>
2. <span data-ttu-id="2b51f-175">Oba hostitele umožňuje anonymní a ověření uživatelé</span><span class="sxs-lookup"><span data-stu-id="2b51f-175">The host allows both anonymous and authenticated users</span></span>

<span data-ttu-id="2b51f-176">První výše popsané není ovlivněn 2.0 změny.</span><span class="sxs-lookup"><span data-stu-id="2b51f-176">The first variation described above is unaffected by the 2.0 changes.</span></span>

<span data-ttu-id="2b51f-177">Druhá je popsáno výše je ovlivněny změnami v 2.0.</span><span class="sxs-lookup"><span data-stu-id="2b51f-177">The second variation described above is affected by the 2.0 changes.</span></span> <span data-ttu-id="2b51f-178">Jako příklad vám může být umožní anonymní uživatelé do vaší aplikace v IIS nebo [HTTP.sys](xref:fundamentals/servers/httpsys) vrstvy ale autorizací uživatele na úrovni Kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2b51f-178">As an example, you may be allowing anonymous users into your app at the IIS or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorizing users at the Controller level.</span></span> <span data-ttu-id="2b51f-179">V tomto scénáři, nastavte výchozí schéma na `IISDefaults.AuthenticationScheme` v `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="2b51f-179">In this scenario, set the default scheme to `IISDefaults.AuthenticationScheme` in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="2b51f-180">Nepodařilo se nastavit výchozí schéma odpovídajícím způsobem brání vybízí funkčním požadavku authorize.</span><span class="sxs-lookup"><span data-stu-id="2b51f-180">Failure to set the default scheme accordingly prevents the authorize request to challenge from working.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="2b51f-181">IdentityCookieOptions instances</span><span class="sxs-lookup"><span data-stu-id="2b51f-181">IdentityCookieOptions instances</span></span>

<span data-ttu-id="2b51f-182">Vedlejším účinkem 2.0 změny se přepnout na používání s názvem možnosti namísto souboru cookie možnosti instance.</span><span class="sxs-lookup"><span data-stu-id="2b51f-182">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="2b51f-183">Odebere se možnost přizpůsobit si názvy schémat souboru cookie Identity.</span><span class="sxs-lookup"><span data-stu-id="2b51f-183">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="2b51f-184">Například 1.x projekty použití [konstruktor vkládání](xref:mvc/controllers/dependency-injection#constructor-injection) předat `IdentityCookieOptions` parametr do *AccountController.cs*.</span><span class="sxs-lookup"><span data-stu-id="2b51f-184">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs*.</span></span> <span data-ttu-id="2b51f-185">Schéma externí soubor cookie ověřování přistupuje z zadanou instanci:</span><span class="sxs-lookup"><span data-stu-id="2b51f-185">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="2b51f-186">Vkládání výše uvedené konstruktor stane zbytečné v projektech pro 2.0 a `_externalCookieScheme` je možné pole odstranit:</span><span class="sxs-lookup"><span data-stu-id="2b51f-186">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="2b51f-187">`IdentityConstants.ExternalScheme` – Konstanta je možné přímo:</span><span class="sxs-lookup"><span data-stu-id="2b51f-187">The `IdentityConstants.ExternalScheme` constant can be used directly:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="2b51f-188">Přidat IdentityUser objektů POCO navigační vlastnosti</span><span class="sxs-lookup"><span data-stu-id="2b51f-188">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="2b51f-189">Navigační vlastnosti Entity Framework (EF) Core základní třídy `IdentityUser` odebrané POCO (prostý staré CLR objekt).</span><span class="sxs-lookup"><span data-stu-id="2b51f-189">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="2b51f-190">Pokud váš projekt 1.x tyto vlastnosti, ručně přidáte do projektu 2.0:</span><span class="sxs-lookup"><span data-stu-id="2b51f-190">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="2b51f-191">Aby se zabránilo duplicitní cizí klíče při spuštění migrace EF Core, přidejte následující text do vaší `IdentityDbContext` třídy `OnModelCreating` – metoda (po `base.OnModelCreating();` volání):</span><span class="sxs-lookup"><span data-stu-id="2b51f-191">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="2b51f-192">Nahraďte GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="2b51f-192">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="2b51f-193">Synchronní metoda `GetExternalAuthenticationSchemes` byla odebrána a místo toho použití asynchronní verze.</span><span class="sxs-lookup"><span data-stu-id="2b51f-193">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="2b51f-194">projekty 1.x mají následující kód *ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="2b51f-194">1.x projects have the following code in *ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="2b51f-195">Tato metoda se zobrazí v *Login.cshtml* příliš:</span><span class="sxs-lookup"><span data-stu-id="2b51f-195">This method appears in *Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

<span data-ttu-id="2b51f-196">V projektech, 2.0, použijte `GetExternalAuthenticationSchemesAsync` metody:</span><span class="sxs-lookup"><span data-stu-id="2b51f-196">In 2.0 projects, use the `GetExternalAuthenticationSchemesAsync` method:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="2b51f-197">V *Login.cshtml*, `AuthenticationScheme` přistupuje ve vlastnosti `foreach` smyčky se změní na `Name`:</span><span class="sxs-lookup"><span data-stu-id="2b51f-197">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="2b51f-198">Změna vlastnosti ManageLoginsViewModel</span><span class="sxs-lookup"><span data-stu-id="2b51f-198">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="2b51f-199">A `ManageLoginsViewModel` objekt se používá v `ManageLogins` akce *ManageController.cs*.</span><span class="sxs-lookup"><span data-stu-id="2b51f-199">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="2b51f-200">V 1.x projekty, objekt společnosti `OtherLogins` vlastnosti je návratový typ `IList<AuthenticationDescription>`.</span><span class="sxs-lookup"><span data-stu-id="2b51f-200">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="2b51f-201">Tento návratový typ vyžaduje importu `Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="2b51f-201">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="2b51f-202">V projektech, 2.0, návratový typ se změní na `IList<AuthenticationScheme>`.</span><span class="sxs-lookup"><span data-stu-id="2b51f-202">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="2b51f-203">Tento nový návratový typ vyžaduje nahrazení `Microsoft.AspNetCore.Http.Authentication` importovat `Microsoft.AspNetCore.Authentication` importovat.</span><span class="sxs-lookup"><span data-stu-id="2b51f-203">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="2b51f-204">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2b51f-204">Additional resources</span></span>

<span data-ttu-id="2b51f-205">Další podrobnosti a diskusi najdete v tématu [diskuse Auth 2.0](https://github.com/aspnet/Security/issues/1338) problém na Githubu.</span><span class="sxs-lookup"><span data-stu-id="2b51f-205">For additional details and discussion, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>

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
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>Ověřování a identitu migrovat do ASP.NET Core 2.0

Podle [Scott Addie](https://github.com/scottaddie) a [ani Haovi společnosti](https://github.com/HaoK)

ASP.NET Core 2.0 je nový model pro ověřování a [Identity](xref:security/authentication/identity) , který zjednodušuje konfiguraci služby. Aplikace ASP.NET Core 1.x, které používají ověřování nebo Identity lze aktualizovat pomocí nového modelu, jak je uvedeno níže.

## <a name="update-namespaces"></a>Aktualizovat obory názvů

V 1.x, třídy, například `IdentityRole` a `IdentityUser` nebyly nalezeny v `Microsoft.AspNetCore.Identity.EntityFrameworkCore` oboru názvů.

Ve verzi 2.0 <xref:Microsoft.AspNetCore.Identity> obor názvů stal novým výchozím místem pro některé z těchto tříd. S kódem Identity výchozí ovlivněné třídy zahrnují `ApplicationUser` a `Startup`. Upravit vaše `using` příkazy ovlivněné odkazy.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Ověřovací Middleware a služby

V projektech 1.x je nakonfigurované ověřování prostřednictvím middlewaru. Middleware metoda je volána pro každé schéma ověřování, které chcete podporovat.

V následujícím příkladu 1.x konfiguruje s identitou v ověřování přes síť Facebook *Startup.cs*:

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

V projektech, 2.0 je nakonfigurované ověřování prostřednictvím služby. Každé schéma ověřování je registrován v `ConfigureServices` metoda *Startup.cs*. `UseIdentity` Metoda je nahrazen `UseAuthentication`.

V následujícím příkladu 2.0 konfiguruje s identitou v ověřování přes síť Facebook *Startup.cs*:

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

`UseAuthentication` Metoda přidá komponenta jednoho ověřovacího middlewaru, který je zodpovědný za automatickou ověření a zpracování žádostí o vzdálené ověření. Nahradí všechny komponenty middlewaru jednotlivých součástí jedné, běžné middlewaru.

Níže jsou uvedeny pokyny k migraci 2.0 pro každé schéma hlavní ověřování.

### <a name="cookie-based-authentication"></a>Ověřování na základě souborů cookie

Vyberte jednu z následujících dvou možností a proveďte potřebné změny v *Startup.cs*:

1. Použít soubory cookie s využitím Identity
    - Nahraďte `UseIdentity` s `UseAuthentication` v `Configure` metody:

        ```csharp
        app.UseAuthentication();
        ```

    - Vyvolat `AddIdentity` metodu `ConfigureServices` metoda pro přidání služby ověřování souborů cookie.
    - Volitelně můžete vyvolat `ConfigureApplicationCookie` nebo `ConfigureExternalCookie` metodu `ConfigureServices` metoda upravit nastavení souborů cookie Identity.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Použít soubory cookie bez systému Identity
    - Nahradit `UseCookieAuthentication` volání metody `Configure` metodu s `UseAuthentication`:

        ```csharp
        app.UseAuthentication();
        ```

    - Vyvolat `AddAuthentication` a `AddCookie` metody v `ConfigureServices` metody:

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

### <a name="jwt-bearer-authentication"></a>Ověřování nosného tokenu JWT

Proveďte následující změny v *Startup.cs*:
- Nahradit `UseJwtBearerAuthentication` volání metody `Configure` metodu s `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddJwtBearer` metoda ve `ConfigureServices` metody:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Tento fragment kódu nepoužívá identitu, abyste výchozí schéma by měl nastavit předáním `JwtBearerDefaults.AuthenticationScheme` k `AddAuthentication` metody.

### <a name="openid-connect-oidc-authentication"></a>Ověřování OpenID Connect (OIDC)

Proveďte následující změny v *Startup.cs*:

- Nahradit `UseOpenIdConnectAuthentication` volání metody `Configure` metodu s `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddOpenIdConnect` metoda ve `ConfigureServices` metody:

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

- Nahradit `PostLogoutRedirectUri` vlastnost `OpenIdConnectOptions` akce s `SignedOutRedirectUri`:

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Ověřování Facebooku

Proveďte následující změny v *Startup.cs*:
- Nahradit `UseFacebookAuthentication` volání metody `Configure` metodu s `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddFacebook` metoda ve `ConfigureServices` metody:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Ověřování Googlu

Proveďte následující změny v *Startup.cs*:
- Nahradit `UseGoogleAuthentication` volání metody `Configure` metodu s `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddGoogle` metoda ve `ConfigureServices` metody:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Ověřování pomocí Account Microsoft

Proveďte následující změny v *Startup.cs*:
- Nahradit `UseMicrosoftAccountAuthentication` volání metody `Configure` metodu s `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddMicrosoftAccount` metoda ve `ConfigureServices` metody:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Ověřování Twitteru

Proveďte následující změny v *Startup.cs*:
- Nahradit `UseTwitterAuthentication` volání metody `Configure` metodu s `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddTwitter` metoda ve `ConfigureServices` metody:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Nastavení výchozích schémat ověřování

V 1.x `AutomaticAuthenticate` a `AutomaticChallenge` vlastnosti [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) základní třídy byly v úmyslu nastavit na jedno schéma ověřování. Neexistoval dobrý způsob, jak to chcete vynutit.

Ve verzi 2.0, se odebraly tyto dvě vlastnosti jako vlastnosti na jednotlivých `AuthenticationOptions` instance. Se dají konfigurovat v `AddAuthentication` volání metody v rámci `ConfigureServices` metoda *Startup.cs*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

V předchozím fragmentu kódu, výchozí schéma je nastavený na `CookieAuthenticationDefaults.AuthenticationScheme` (soubory cookie.").

Alternativně použijte přetížené verze `AddAuthentication` metoda nastavit více než jednu vlastnost. V následujícím příkladu přetěžované metody, nebo výchozí schéma je nastaveno na `CookieAuthenticationDefaults.AuthenticationScheme`. Případně lze zadat schéma ověřování v rámci svůj individuální `[Authorize]` atributy nebo zásad autorizace.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Definujte výchozí schéma ve verzi 2.0, pokud platí jedna z následujících podmínek:
- Chcete uživateli být automaticky přihlášeni
- Můžete použít `[Authorize]` atribut nebo autorizační zásady bez zadání schémata

Výjimkou z tohoto pravidla je `AddIdentity` metody. Tato metoda přidá soubory cookie pro vás a nastaví výchozí nastavení ověřování a ověřovací schémata pro soubor cookie aplikace `IdentityConstants.ApplicationScheme`. Kromě toho nastaví výchozí přihlášení schéma na externí soubor cookie `IdentityConstants.ExternalScheme`.

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Použití rozšíření ověřování HttpContext

`IAuthenticationManager` Rozhraní je hlavní vstupní bod do 1.x ověřovacího systému. Nahradili jsme ho s novou sadu `HttpContext` rozšiřující metody v `Microsoft.AspNetCore.Authentication` oboru názvů.

Například 1.x projekty referenční dokumentace `Authentication` vlastnost:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

V projektech, 2.0, import `Microsoft.AspNetCore.Authentication` obor názvů a odstranit `Authentication` odkazy na vlastnosti:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Windows Authentication (HTTP.sys / IISIntegration)

Existují dvě varianty ověřování Windows:
1. Hostitel umožňuje pouze ověřeným uživatelům
2. Oba hostitele umožňuje anonymní a ověření uživatelé

První výše popsané není ovlivněn 2.0 změny.

Druhá je popsáno výše je ovlivněny změnami v 2.0. Například je může být umožní anonymní uživatelé do vaší aplikace v IIS nebo [HTTP.sys](xref:fundamentals/servers/httpsys) vrstvy ale autorizací uživatele na úrovni Kontroleru. V tomto scénáři, nastavte výchozí schéma na `IISDefaults.AuthenticationScheme` v `Startup.ConfigureServices` metody:

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

Nepodařilo se nastavit výchozí schéma brání vybízí funkčním požadavku authorize.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>IdentityCookieOptions instances

Vedlejším účinkem 2.0 změny se přepnout na používání s názvem možnosti namísto souboru cookie možnosti instance. Odebere se možnost přizpůsobit si názvy schémat souboru cookie Identity.

Například 1.x projekty použití [konstruktor vkládání](xref:mvc/controllers/dependency-injection#constructor-injection) předat `IdentityCookieOptions` parametr do *AccountController.cs* a *ManageController.cs*. Schéma externí soubor cookie ověřování přistupuje z zadanou instanci:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

Vkládání výše uvedené konstruktor stane zbytečné v projektech pro 2.0 a `_externalCookieScheme` je možné pole odstranit:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

1.x projektů používaných `_externalCookieScheme` pole následujícím způsobem:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

V projektech, 2.0 nahraďte předchozí kód následujícím kódem. `IdentityConstants.ExternalScheme` – Konstanta je možné přímo.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Vyřešit nově přidaný `SignOutAsync` volání importováním následující obor názvů:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>Přidat IdentityUser objektů POCO navigační vlastnosti

Navigační vlastnosti Entity Framework (EF) Core základní třídy `IdentityUser` odebrané POCO (prostý staré CLR objekt). Pokud váš projekt 1.x tyto vlastnosti, ručně přidáte do projektu 2.0:

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

Aby se zabránilo duplicitní cizí klíče při spuštění migrace EF Core, přidejte následující text do vaší `IdentityDbContext` třídy `OnModelCreating` – metoda (po `base.OnModelCreating();` volání):

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

## <a name="replace-getexternalauthenticationschemes"></a>Nahraďte GetExternalAuthenticationSchemes

Synchronní metoda `GetExternalAuthenticationSchemes` byla odebrána a místo toho použití asynchronní verze. projekty 1.x mají následující kód *Controllers/ManageController.cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Tato metoda se zobrazí v *Views/Account/Login.cshtml* příliš:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

V projektech, 2.0, použijte <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> metody. Změnu v hodnotě *ManageController.cs* vypadá podobně jako následující kód:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

V *Login.cshtml*, `AuthenticationScheme` přistupuje ve vlastnosti `foreach` smyčky se změní na `Name`:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Změna vlastnosti ManageLoginsViewModel

A `ManageLoginsViewModel` objekt se používá v `ManageLogins` akce *ManageController.cs*. V 1.x projekty, objekt společnosti `OtherLogins` vlastnosti je návratový typ `IList<AuthenticationDescription>`. Tento návratový typ vyžaduje importu `Microsoft.AspNetCore.Http.Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

V projektech, 2.0, návratový typ se změní na `IList<AuthenticationScheme>`. Tento nový návratový typ vyžaduje nahrazení `Microsoft.AspNetCore.Http.Authentication` importovat `Microsoft.AspNetCore.Authentication` importovat.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Další zdroje

Další informace najdete v tématu [diskuse Auth 2.0](https://github.com/aspnet/Security/issues/1338) problém na Githubu.

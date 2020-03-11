---
title: Migrace ověřování a identity na ASP.NET Core 2,0
author: scottaddie
description: Tento článek popisuje nejběžnější kroky pro migraci ASP.NET Core 1. x ověřování a identity na ASP.NET Core 2,0.
ms.author: scaddie
ms.date: 06/21/2019
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: af905f1127d504839f66d9e0e1ca1dfc27e32772
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667606"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a>Migrace ověřování a identity na ASP.NET Core 2,0

[Scottem Addie](https://github.com/scottaddie) a [Hao Kung](https://github.com/HaoK)

ASP.NET Core 2,0 má nový model pro ověřování a [identitu](xref:security/authentication/identity) , který zjednodušuje konfiguraci pomocí služeb. ASP.NET Core 1. x aplikace, které používají ověřování nebo identitu, můžete aktualizovat tak, aby používaly nový model, jak je uvedeno níže.

## <a name="update-namespaces"></a>Aktualizovat obory názvů

V 1. x se v oboru názvů `Microsoft.AspNetCore.Identity.EntityFrameworkCore` našly třídy, jako jsou `IdentityRole` a `IdentityUser`.

V 2,0 se obor názvů <xref:Microsoft.AspNetCore.Identity> stal novým domovem pro několik takových tříd. S výchozím kódem identity obsahují ovlivněné třídy `ApplicationUser` a `Startup`. Chcete-li vyřešit ovlivněné odkazy, upravte příkazy `using`.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Middleware a služby ověřování

V projektech 1. x se ověřování konfiguruje prostřednictvím middlewaru. Metoda middleware je vyvolána pro každé schéma ověřování, které chcete podporovat.

Následující příklad 1. x konfiguruje ověřování na Facebooku pomocí identity v *Startup.cs*:

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

V projektech 2,0 se ověřování konfiguruje přes služby. Každé schéma ověřování je registrováno v metodě `ConfigureServices` *Startup.cs*. Metoda `UseIdentity` je nahrazena `UseAuthentication`.

Následující příklad 2,0 konfiguruje ověřování na Facebooku pomocí identity v *Startup.cs*:

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

Metoda `UseAuthentication` přidá jednu komponentu middleware pro ověřování, která zodpovídá za automatické ověřování a zpracování žádostí o vzdálené ověření. Nahrazuje všechny jednotlivé komponenty middlewaru jedinou běžnou komponentou middlewaru.

Níže jsou uvedené 2,0 pokyny k migraci pro každé hlavní schéma ověřování.

### <a name="cookie-based-authentication"></a>Ověřování na základě souborů cookie

Vyberte jednu z následujících dvou možností a proveďte potřebné změny v *Startup.cs*:

1. Použít soubory cookie s identitou
    - V `Configure` metodě nahraďte `UseIdentity` `UseAuthentication`:

        ```csharp
        app.UseAuthentication();
        ```

    - Chcete-li přidat ověřovací služby souborů cookie, volejte metodu `AddIdentity` v metodě `ConfigureServices`.
    - Volitelně můžete vyvolat metodu `ConfigureApplicationCookie` nebo `ConfigureExternalCookie` v metodě `ConfigureServices` pro vylepšení nastavení souborů cookie identity.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Použít soubory cookie bez identity
    - Nahraďte volání metody `UseCookieAuthentication` v metodě `Configure` `UseAuthentication`:

        ```csharp
        app.UseAuthentication();
        ```

    - V metodě `ConfigureServices` volejte metody `AddAuthentication` a `AddCookie`:

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

### <a name="jwt-bearer-authentication"></a>Ověření nosiče JWT

V *Startup.cs*proveďte následující změny:
- Nahraďte volání metody `UseJwtBearerAuthentication` v metodě `Configure` `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- V metodě `ConfigureServices` volejte metodu `AddJwtBearer`:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Tento fragment kódu nepoužívá identitu, proto by mělo být výchozí schéma nastaveno předáním `JwtBearerDefaults.AuthenticationScheme` k metodě `AddAuthentication`.

### <a name="openid-connect-oidc-authentication"></a>Ověřování OpenID Connect (OIDC)

V *Startup.cs*proveďte následující změny:

- Nahraďte volání metody `UseOpenIdConnectAuthentication` v metodě `Configure` `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- V metodě `ConfigureServices` volejte metodu `AddOpenIdConnect`:

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

- Nahraďte vlastnost `PostLogoutRedirectUri` v akci `OpenIdConnectOptions` `SignedOutRedirectUri`:

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Ověřování pomocí Facebooku

V *Startup.cs*proveďte následující změny:
- Nahraďte volání metody `UseFacebookAuthentication` v metodě `Configure` `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- V metodě `ConfigureServices` volejte metodu `AddFacebook`:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Ověřování pomocí Googlu

V *Startup.cs*proveďte následující změny:
- Nahraďte volání metody `UseGoogleAuthentication` v metodě `Configure` `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- V metodě `ConfigureServices` volejte metodu `AddGoogle`:

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a>Ověřování pomocí účtu Microsoft

Další informace o ověřování účet Microsoft najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/14455).

V *Startup.cs*proveďte následující změny:
- Nahraďte volání metody `UseMicrosoftAccountAuthentication` v metodě `Configure` `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- V metodě `ConfigureServices` volejte metodu `AddMicrosoftAccount`:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Ověřování pomocí Twitteru

V *Startup.cs*proveďte následující změny:
- Nahraďte volání metody `UseTwitterAuthentication` v metodě `Configure` `UseAuthentication`:

    ```csharp
    app.UseAuthentication();
    ```

- V metodě `ConfigureServices` volejte metodu `AddTwitter`:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Nastavení výchozích schémat ověřování

V 1. x byly vlastnosti `AutomaticAuthenticate` a `AutomaticChallenge` základní třídy [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) určeny k nastavení v jednom schématu ověřování. Tento způsob vykonání není dobrý.

V 2,0 byly tyto dvě vlastnosti odebrány jako vlastnosti u jednotlivých instancí `AuthenticationOptions`. Lze je nakonfigurovat ve volání metody `AddAuthentication` v rámci metody `ConfigureServices` *Startup.cs*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

V předchozím fragmentu kódu je výchozí schéma nastaveno na `CookieAuthenticationDefaults.AuthenticationScheme` ("cookies").

Případně můžete použít přetíženou verzi metody `AddAuthentication` k nastavení více než jedné vlastnosti. V následujícím příkladu přetížené metody je výchozí schéma nastaveno na `CookieAuthenticationDefaults.AuthenticationScheme`. Schéma ověřování může být případně zadáno v rámci svých individuálních atributů `[Authorize]` nebo zásad autorizace.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Definujte výchozí schéma v 2,0, pokud je splněna jedna z následujících podmínek:
- Chcete, aby byl uživatel automaticky přihlášen
- Použijete atribut `[Authorize]` nebo zásady autorizace bez zadání schémat.

Výjimkou z tohoto pravidla je metoda `AddIdentity`. Tato metoda přidá soubory cookie a nastaví výchozí ověřování a schémata pro ověřování souborů cookie aplikace `IdentityConstants.ApplicationScheme`. Kromě toho nastaví výchozí schéma přihlášení na externí soubor cookie `IdentityConstants.ExternalScheme`.

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Použití ověřovacích rozšíření HttpContext

Rozhraní `IAuthenticationManager` je hlavní vstupní bod do systému ověřování 1. x. Byl nahrazen novou sadou `HttpContext` rozšiřujících metod v oboru názvů `Microsoft.AspNetCore.Authentication`.

Například projekty 1. x odkazují na vlastnost `Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

V 2,0 projektech importujte `Microsoft.AspNetCore.Authentication` obor názvů a odstraňte odkazy na vlastnost `Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Ověřování systému Windows (HTTP. sys/IISIntegration)

K dispozici jsou dvě varianty ověřování systému Windows:

* Hostitel povoluje pouze ověřené uživatele. Tato variace není ovlivněna 2,0 změnami.
* Hostitel umožňuje anonymní i ověřené uživatele. Tato variace má vliv na změny 2,0. Například aplikace by měla umožňovat anonymní uživatele na vrstvě [IIS](xref:host-and-deploy/iis/index) nebo [http. sys](xref:fundamentals/servers/httpsys) , ale autorizuje uživatele na úrovni řadiče. V tomto scénáři nastavte výchozí schéma v metodě `Startup.ConfigureServices`.

  Pro [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)nastavte výchozí schéma na `IISDefaults.AuthenticationScheme`:

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  Pro [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)nastavte výchozí schéma na `HttpSysDefaults.AuthenticationScheme`:

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  Nepovedlo se nastavit výchozí schéma, aby žádost o ověření (Challenge) nefungovala s následující výjimkou:

  > `System.InvalidOperationException`: není zadaný žádný authenticationScheme a nenašel se žádný DefaultChallengeScheme.

Další informace naleznete v tématu <xref:security/authentication/windowsauth>.

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a>Instance IdentityCookieOptions

Vedlejší efekt 2,0 změn je místo instancí možností souborů cookie přepínač pomocí pojmenovaných možností. Možnost přizpůsobení názvů schémat souborů cookie identity se odeberou.

Například projekty 1. x používají [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) k předání parametru `IdentityCookieOptions` do *AccountController.cs* a *ManageController.cs*. K externímu schématu ověřování souborů cookie se dostanete ze zadané instance:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

Výše uvedený převstřik konstruktoru se v projektech 2,0 stal zbytečným a pole `_externalCookieScheme` lze odstranit:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

projekty 1. x používaly pole `_externalCookieScheme` následujícím způsobem:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

V projektech 2,0 nahraďte předchozí kód následujícím kódem. Konstantu `IdentityConstants.ExternalScheme` lze použít přímo.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

Pomocí importu následujícího oboru názvů vyřešte nově přidané `SignOutAsync` volání:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a>Přidat vlastnosti navigace IdentityUser POCO

Odebrali jsme základní navigační vlastnosti Entity Framework (EF) základního `IdentityUser` POCO (objekt CLR, který je ve starém formátu). Pokud váš projekt 1. x tyto vlastnosti používá, přidejte je ručně zpátky do projektu 2,0:

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

Chcete-li zabránit duplicitním cizím klíčům při spuštění EF Core migrace, přidejte následující do vaší `IdentityDbContext` třídy ' `OnModelCreating` metody (po volání `base.OnModelCreating();`):

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

## <a name="replace-getexternalauthenticationschemes"></a>Nahradit GetExternalAuthenticationSchemes

Synchronní metoda `GetExternalAuthenticationSchemes` byla odebrána namísto asynchronní verze. projekty 1. x mají následující kód v *Controllers/ManageController. cs*:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

Tato metoda se zobrazí v *zobrazeních/účtech/přihlašovacích údajích. cshtml* je také:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

V projektech 2,0 použijte metodu <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*>. Změna v *ManageController.cs* se podobá následujícímu kódu:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

V *Login. cshtml*se vlastnost `AuthenticationScheme`, ke které se přistupovalo v `foreach` smyčce, změní na `Name`:

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Změna vlastnosti ManageLoginsViewModel

Objekt `ManageLoginsViewModel` se používá v akci `ManageLogins` *ManageController.cs*. V projektech 1. x je návratový typ objektu `OtherLogins` vlastností `IList<AuthenticationDescription>`. Tento návratový typ vyžaduje import `Microsoft.AspNetCore.Http.Authentication`:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

V projektech 2,0 se návratový typ změní na `IList<AuthenticationScheme>`. Tento nový návratový typ vyžaduje nahrazení `Microsoft.AspNetCore.Http.Authentication` import pomocí `Microsoft.AspNetCore.Authentication` import.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Další zdroje

Další informace najdete v [diskuzi k problému ověření 2,0](https://github.com/aspnet/Security/issues/1338) na GitHubu.

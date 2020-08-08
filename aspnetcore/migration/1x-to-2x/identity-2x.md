---
title: Migrace ověřování a Identity ASP.NET Core 2,0
author: scottaddie
description: Tento článek popisuje nejběžnější kroky pro migraci ASP.NET Core 1. x ověřování a Identity ASP.NET Core 2,0.
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: 46f10df25235b532f188eda2a079aef71070cd6d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015287"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a>Migrace ověřování a Identity ASP.NET Core 2,0

[Scottem Addie](https://github.com/scottaddie) a [Hao Kung](https://github.com/HaoK)

ASP.NET Core 2,0 má nový model pro ověřování a [Identity](xref:security/authentication/identity) který zjednodušuje konfiguraci pomocí služeb. ASP.NET Core 1. x aplikací, které používají ověřování, nebo je Identity lze aktualizovat, aby používaly nový model, jak je uvedeno níže.

## <a name="update-namespaces"></a>Aktualizovat obory názvů

V 1. x byly třídy, jako jsou `IdentityRole` a, `IdentityUser` nalezeny v `Microsoft.AspNetCore.Identity.EntityFrameworkCore` oboru názvů.

V 2,0 se <xref:Microsoft.AspNetCore.Identity> obor názvů stal novým domovem pro několik takových tříd. S výchozím Identity kódem obsahují ovlivněné třídy `ApplicationUser` a `Startup` . Upravte své `using` příkazy tak, aby vyřešily ovlivněné odkazy.

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a>Middleware a služby ověřování

V projektech 1. x se ověřování konfiguruje prostřednictvím middlewaru. Metoda middleware je vyvolána pro každé schéma ověřování, které chcete podporovat.

Následující příklad 1. x konfiguruje ověřování na Facebooku pomocí Identity v *Startup.cs*:

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

V projektech 2,0 se ověřování konfiguruje přes služby. Každé schéma ověřování je registrováno v `ConfigureServices` metodě *Startup.cs*. `UseIdentity`Metoda je nahrazena řetězcem `UseAuthentication` .

Následující příklad 2,0 konfiguruje ověřování na Facebooku pomocí Identity v *Startup.cs*:

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

`UseAuthentication`Metoda přidá jednu komponentu middleware pro ověřování, která zodpovídá za automatické ověřování a zpracování požadavků na vzdálené ověřování. Nahrazuje všechny jednotlivé komponenty middlewaru jedinou běžnou komponentou middlewaru.

Níže jsou uvedené 2,0 pokyny k migraci pro každé hlavní schéma ověřování.

### <a name="no-loccookie-based-authentication"></a>Cookieověřování na základě

Vyberte jednu z následujících dvou možností a proveďte potřebné změny v *Startup.cs*:

1. Použít s cookie sIdentity
    - Nahraďte `UseIdentity` `UseAuthentication` v `Configure` metodě:

        ```csharp
        app.UseAuthentication();
        ```

    - `AddIdentity` `ConfigureServices` Chcete-li přidat ověřovací služby, volejte metodu v metodě cookie .
    - Volitelně můžete vyvolat `ConfigureApplicationCookie` metodu nebo `ConfigureExternalCookie` v `ConfigureServices` metodě pro vylepšení Identity cookie nastavení.

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. Použít cookie s bezIdentity
    - Nahraďte `UseCookieAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication` :

        ```csharp
        app.UseAuthentication();
        ```

    - Vyvolat `AddAuthentication` metody a `AddCookie` v `ConfigureServices` metodě:

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
- Nahraďte `UseJwtBearerAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddJwtBearer` metodu v `ConfigureServices` metodě:

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    Tento fragment kódu nepoužívá Identity , proto je třeba nastavit výchozí schéma předáním `JwtBearerDefaults.AuthenticationScheme` do `AddAuthentication` metody.

### <a name="openid-connect-oidc-authentication"></a>Ověřování OpenID Connect (OIDC)

V *Startup.cs*proveďte následující změny:

- Nahraďte `UseOpenIdConnectAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddOpenIdConnect` metodu v `ConfigureServices` metodě:

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

- Nahraďte `PostLogoutRedirectUri` vlastnost v `OpenIdConnectOptions` akci pomocí `SignedOutRedirectUri` :

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a>Ověřování Facebooku

V *Startup.cs*proveďte následující změny:
- Nahraďte `UseFacebookAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddFacebook` metodu v `ConfigureServices` metodě:

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a>Ověřování Googlu

V *Startup.cs*proveďte následující změny:
- Nahraďte `UseGoogleAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddGoogle` metodu v `ConfigureServices` metodě:

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
- Nahraďte `UseMicrosoftAccountAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddMicrosoftAccount` metodu v `ConfigureServices` metodě:

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a>Ověřování Twitteru

V *Startup.cs*proveďte následující změny:
- Nahraďte `UseTwitterAuthentication` volání metody v `Configure` metodě pomocí `UseAuthentication` :

    ```csharp
    app.UseAuthentication();
    ```

- Vyvolat `AddTwitter` metodu v `ConfigureServices` metodě:

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a>Nastavení výchozích schémat ověřování

V 1. x `AutomaticAuthenticate` `AutomaticChallenge` byly vlastnosti a základní třídy [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) určeny k nastavení v jednom schématu ověřování. Tento způsob vykonání není dobrý.

V 2,0 byly tyto dvě vlastnosti odebrány jako vlastnosti v jednotlivých `AuthenticationOptions` instancích. Lze je nakonfigurovat ve `AddAuthentication` volání metody v rámci `ConfigureServices` metody *Startup.cs*:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

V předchozím fragmentu kódu je výchozí schéma nastaveno na `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").

Alternativně můžete pomocí přetížené verze `AddAuthentication` metody nastavit více než jednu vlastnost. V následujícím příkladu přetížené metody je výchozí schéma nastaveno na `CookieAuthenticationDefaults.AuthenticationScheme` . Schéma ověřování může být případně zadáno v rámci svých individuálních `[Authorize]` atributů nebo zásad autorizace.

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

Definujte výchozí schéma v 2,0, pokud je splněna jedna z následujících podmínek:
- Chcete, aby byl uživatel automaticky přihlášen
- `[Authorize]`Zásady ověřování použijete bez zadání schémat.

Výjimkou z tohoto pravidla je `AddIdentity` metoda. Tato metoda přidá cookie za vás a nastaví výchozí schémata ověřování a ověření pro aplikaci cookie `IdentityConstants.ApplicationScheme` . Kromě toho nastaví výchozí schéma přihlášení na externí cookie `IdentityConstants.ExternalScheme` .

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a>Použití ověřovacích rozšíření HttpContext

`IAuthenticationManager`Rozhraní je hlavní vstupní bod do systému ověřování 1. x. Byl nahrazen novou sadou `HttpContext` rozšiřujících metod v `Microsoft.AspNetCore.Authentication` oboru názvů.

Například projekty 1. x odkazují na `Authentication` vlastnost:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

V 2,0 projektech importujte `Microsoft.AspNetCore.Authentication` obor názvů a odstraňte `Authentication` odkazy na vlastnosti:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a>Ověřování systému Windows (HTTP.sys/IISIntegration)

K dispozici jsou dvě varianty ověřování systému Windows:

* Hostitel povoluje pouze ověřené uživatele. Tato variace není ovlivněna 2,0 změnami.
* Hostitel umožňuje anonymní i ověřené uživatele. Tato variace má vliv na změny 2,0. Například aplikace by měla umožňovat anonymní uživatele na úrovni [služby IIS](xref:host-and-deploy/iis/index) nebo [HTTP.sys](xref:fundamentals/servers/httpsys) , ale autorizaci uživatelů na úrovni řadiče. V tomto scénáři nastavte výchozí schéma v `Startup.ConfigureServices` metodě.

  Pro [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)nastavte výchozí schéma na `IISDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  Pro [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)nastavte výchozí schéma na `HttpSysDefaults.AuthenticationScheme` :

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  Nepovedlo se nastavit výchozí schéma, aby žádost o ověření (Challenge) nefungovala s následující výjimkou:

  > `System.InvalidOperationException`: Není zadaný žádný authenticationScheme a nenašel se žádný DefaultChallengeScheme.

Další informace naleznete v tématu <xref:security/authentication/windowsauth>.

<a name="identity-cookie-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a>IdentityCookieInstance možností

Vedlejší efekt 2,0 změn je místo instancí možností přepínač pomocí pojmenovaných možností cookie . Možnost přizpůsobení Identity cookie názvů schémat se odebere.

Například projekty 1. x používají [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) k předání `IdentityCookieOptions` parametru do *AccountController.cs* a *ManageController.cs*. K externímu cookie schématu ověřování se dostanete ze zadané instance:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

Výše uvedený převstřik konstruktoru se v projektech 2,0 stal zbytečným a `_externalCookieScheme` pole je možné odstranit:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

projekty 1. x používaly `_externalCookieScheme` pole následujícím způsobem:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

V projektech 2,0 nahraďte předchozí kód následujícím kódem. `IdentityConstants.ExternalScheme`Konstantu lze použít přímo.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

`SignOutAsync`Pomocí importu následujícího oboru názvů vyřešte nově přidané volání:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a>Přidat Identity vlastnosti navigace POCO uživatele

Byla odebrána základní navigační vlastnost Entity Framework (EF) základního `IdentityUser` POCO (prostý starý objekt CLR). Pokud váš projekt 1. x tyto vlastnosti používá, přidejte je ručně zpátky do projektu 2,0:

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

Chcete-li zabránit duplicitním cizím klíčům při spuštění EF Core migrace, přidejte následující do `IdentityDbContext` `OnModelCreating` metody Class (po `base.OnModelCreating();` volání):

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

V projektech 2,0 použijte <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> metodu. Změna v *ManageController.cs* se podobá následujícímu kódu:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

V *Login. cshtml*se vlastnost, ke které se `AuthenticationScheme` přistupovalo v `foreach` smyčce, změní na `Name` :

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a>Změna vlastnosti ManageLoginsViewModel

`ManageLoginsViewModel`Objekt se používá v `ManageLogins` akci *ManageController.cs*. V projektech 1. x `OtherLogins` je návratový typ vlastnosti objektu `IList<AuthenticationDescription>` . Tento návratový typ vyžaduje import `Microsoft.AspNetCore.Http.Authentication` :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

V projektech 2,0 se návratový typ změní na `IList<AuthenticationScheme>` . Tento nový návratový typ vyžaduje nahrazení `Microsoft.AspNetCore.Http.Authentication` importu pomocí `Microsoft.AspNetCore.Authentication` importu.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a>Další zdroje

Další informace najdete v [diskuzi k problému ověření 2,0](https://github.com/aspnet/Security/issues/1338) na GitHubu.

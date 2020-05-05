---
title: Multi-Factor Authentication v ASP.NET Core
author: damienbod
description: Naučte se, jak nastavit vícefaktorové ověřování (MFA) v aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: rick-anderson
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/mfa
ms.openlocfilehash: e2f34a72515a700223ce83ce6ec8b55020599ab0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767418"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a>Multi-Factor Authentication v ASP.NET Core

Od [Damien Bowden](https://github.com/damienbod)

Multi-Factor Authentication (MFA) je proces, při kterém se uživatel během přihlašovací události požaduje pro další formy identifikace. Tato výzva by mohla být zadání kódu z cellphone, použití FIDO2 klíče nebo poskytnutí kontroly otiskem prstu. Pokud požadujete druhou formu ověřování, zabezpečení se zlepší. Tento přídavný faktor nemůže útočník snadno získat ani duplikovat.

Tento článek se zabývá následujícími oblastmi:

* Co je MFA a jaké jsou doporučené toky MFA
* Konfigurace MFA pro stránky správy pomocí ASP.NET CoreIdentity
* Odeslat požadavek na přihlášení MFA serveru OpenID Connect
* Vynutit, ASP.NET Core OpenID připojit klienta k vyžádání MFA

## <a name="mfa-2fa"></a>MFA, 2FA

MFA vyžaduje aspoň dva nebo více typů ověření identity, jako je třeba něco, co znáte, nebo biometrické ověřování pro uživatele, který chcete ověřit.

Dvojúrovňové ověřování (2FA) je jako podmnožina MFA, ale rozdíl mezi tím, že MFA může vyžadovat dva nebo více faktorů k prokázání identity.

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a>MFA TOTP (jednorázový algoritmus hesla na základě času)

Vícefaktorové ověřování pomocí TOTP je podporovanou implementací Identitypomocí ASP.NET Core. Tato možnost se dá použít společně s veškerou aplikací dodržujících ověřovacích dat, včetně:

* Aplikace Microsoft Authenticator
* Aplikace Google Authenticator

Podrobnosti implementace najdete na následujícím odkazu:

[Povolit generování kódu QR pro aplikace TOTP Authenticator v ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a>Vícefaktorové ověřování FIDO2 nebo nejenom hesla

FIDO2 je aktuálně:

* Nejbezpečnější způsob dosahování vícefaktorového ověřování.
* Jediný tok MFA, který chrání před útoky typu phishing.

V současné době ASP.NET Core nepodporuje přímé FIDO2. FIDO2 se dají použít pro MFA nebo pro toky nevyužívající hesla.

Azure Active Directory poskytuje podporu pro FIDO2 a bezheslem. Další informace najdete v tématu [Možnosti ověřování bez hesla pro Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).

### <a name="mfa-sms"></a>VÍCEFAKTOROVÉ OVĚŘOVÁNÍ SMS

VÍCEFAKTOROVÉ ověřování pomocí SMS zvyšuje zabezpečení v porovnání s ověřováním hesla (jedním faktorem). Použití serveru SMS jako druhého faktoru se však již nedoporučuje. Pro tento typ implementace existuje příliš mnoho známých vektorů útoku.

[Pokyny pro NIST](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-aspnet-core-identity"></a>Konfigurace MFA pro stránky správy pomocí ASP.NET CoreIdentity

Vícefaktorové ověřování může být pro uživatele vynucené k přístupu k citlivým Identity stránkám v aplikaci ASP.NET Core. To může být užitečné pro aplikace, kde pro různé identity existují různé úrovně přístupu. Uživatelé můžou například zobrazit profilová data pomocí přihlašovacího hesla, ale pro přístup ke stránkám pro správu musel správce použít MFA.

### <a name="extend-the-login-with-an-mfa-claim"></a>Prodloužení přihlašovacích údajů s deklarací MFA

Ukázkový kód je nastaven pomocí ASP.NET Core se Identity stránkami Razor a. `AddIdentity` Metoda se používá místo `AddDefaultIdentity` jedné, takže `IUserClaimsPrincipalFactory` implementaci lze použít k přidání deklarací identity do identity po úspěšném přihlášení.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

`AdditionalUserClaimsPrincipalFactory` Třída přidá `amr` deklaraci identity uživateli až po úspěšném přihlášení. Hodnota deklarace identity je čtena z databáze. Deklarace identity se tady přidá, protože uživatel by měl mít přístup jenom k vyššímu chráněnému zobrazení, pokud se identita přihlásila pomocí MFA. Pokud je zobrazení databáze čteno z databáze přímo namísto použití deklarace identity, je možné získat přístup k zobrazení bez MFA přímo po aktivaci MFA.

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

Vzhledem k Identity tomu, že došlo ke `Startup` změně nastavení služby ve třídě, Identity je nutné aktualizovat rozložení. Vygenerování Identity uživatelského rozhraní stránek do aplikace Definujte rozložení v souboru * Identity/Account/Manage/_Layout. cshtml* .

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

Přiřaďte také rozložení pro všechny stránky pro správu ze Identity stránek:

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a>Ověřit požadavek MFA na stránce pro správu

Stránka Správa Razor ověří, zda se uživatel přihlásil pomocí vícefaktorového ověřování. V `OnGet` metodě se identita používá pro přístup k deklaracím uživatelů. `amr` Deklarace identity je kontrolována na hodnotu `mfa`. Pokud v identitě chybí tato deklarace identity nebo `false`je, stránka se přesměruje na stránku povolit MFA. To je možné, protože uživatel se už přihlásil, ale bez MFA.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a>Logika uživatelského rozhraní pro přepnutí přihlašovacích údajů uživatele

Při spuštění se přidaly zásady autorizace. Tato zásada vyžaduje `amr` deklaraci identity s hodnotou `mfa`.

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

Tato zásada se pak dá v `_Layout` zobrazení použít k zobrazení nebo skrytí nabídky **správce** s upozorněním:

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

Pokud se identita přihlásila pomocí vícefaktorového ověřování (MFA), zobrazí se nabídka **správce** bez upozornění popisu tlačítka. Když se uživatel přihlásí bez MFA, **zobrazí se spolu** s popisem tlačítka, který uživatele informuje (vysvětlení upozornění).

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

Pokud se uživatel přihlásí bez MFA, zobrazí se upozornění:

![Ověřování MFA pro správce](mfa/_static/identitystandalonemfa_01.png)

Po kliknutí na odkaz pro **správu** se uživatel přesměruje na zobrazení pro povolení MFA:

![Správce aktivuje ověřování MFA.](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a>Odeslat požadavek na přihlášení MFA serveru OpenID Connect 

`acr_values` Parametr lze použít k předání `mfa` požadované hodnoty z klienta na server v žádosti o ověření.

> [!NOTE]
> Aby `acr_values` Tato funkce fungovala, musí být na serveru otevřeného ID Connect zpracován parametr.

### <a name="openid-connect-aspnet-core-client"></a>OpenID Connect ASP.NET Core klienta

Aplikace ASP.NET Core Razor Open ID Connect Client App používá `AddOpenIdConnect` metodu pro přihlášení k serveru s otevřeným ID Connect. `acr_values` Parametr je nastaven s `mfa` hodnotou a odeslán s požadavkem na ověření. `OpenIdConnectEvents` Slouží k přidání.

Doporučené `acr_values` hodnoty parametrů najdete v tématu [referenční hodnoty metod ověřování](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-identityserver-4-server-with-aspnet-core-identity"></a>Příklad OpenID připojení serveru IdentityServer 4 pomocí ASP.NET CoreIdentity

Na serveru OpenID Connect, který je implementován pomocí ASP.NET Core Identity s zobrazeními MVC, je vytvořeno nové zobrazení s názvem *ErrorEnable2FA. cshtml* . Zobrazení:

* Zobrazuje, jestli Identity pochází z aplikace, která vyžaduje MFA, ale uživatel je neaktivoval v Identity.
* Informuje uživatele a přidá odkaz pro aktivaci.

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

V `Login` metodě se implementace `IIdentityServerInteractionService` `_interaction` rozhraní používá pro přístup k parametrům Open ID Connect Request. K `acr_values` parametru je přistup pomocí `AcrValues` vlastnosti. Jak klient tuto `mfa` možnost odeslal, je možné ho zkontrolovat.

Pokud je vyžadováno MFA a uživatel v ASP.NET Core Identity má povolené MFA, přihlášení pokračuje. Pokud uživatel nemá povolené vícefaktorové ověřování, uživatel se přesměruje na vlastní zobrazení *ErrorEnable2FA. cshtml*. Pak ASP.NET Core Identity podepíše uživatele v.

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

`ExternalLoginCallback` Metoda funguje jako místní Identity přihlášení. `AcrValues` Vlastnost je kontrolována na `mfa` hodnotu. Pokud je `mfa` hodnota přítomna, ověřování MFA je vynuceno před dokončením přihlášení (například přesměrované `ErrorEnable2FA` na zobrazení).

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

Pokud je už uživatel přihlášený, klientská aplikace:

* Stále ověřuje `amr` deklaraci identity.
* Můžete nastavit MFA s odkazem na zobrazení ASP.NET Core Identity .

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a>Vynutit, ASP.NET Core OpenID připojit klienta k vyžádání MFA

Tento příklad ukazuje, jak aplikace Razor ASP.NET Core Page, která používá OpenID Connect k přihlášení, může vyžadovat, aby se uživatelé ověřili pomocí vícefaktorového ověřování.

Chcete-li ověřit požadavek MFA, `IAuthorizationRequirement` je vytvořen požadavek. Tato stránka se přidá na stránky pomocí zásady, která vyžaduje MFA.

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

`AuthorizationHandler` Je implementována, která bude používat `amr` deklaraci identity a kontroluje hodnotu `mfa`. `amr` Je vrácen v rámci `id_token` úspěšného ověření a může mít mnoho různých hodnot definovaných ve specifikaci [referenčních hodnot metod ověřování](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .

Vrácená hodnota závisí na tom, jak identita byla ověřena, a na základě implementace serveru Connect pro připojení.

`AuthorizationHandler` Použije `RequireMfa` požadavek a ověří `amr` deklaraci identity. Server OpenID Connect se dá implementovat pomocí IdentityServer4 s ASP.NET Core Identity. Když se uživatel přihlásí pomocí TOTP, `amr` deklarace identity se vrátí s hodnotou MFA. Pokud používáte jinou implementaci serveru OpenID Connect nebo jiný typ MFA, `amr` deklarace identity bude nebo může mít jinou hodnotu. Aby bylo možné tuto akci přijmout, je nutné kód rozšířit.

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

V `Startup.ConfigureServices` metodě se jako výchozí `AddOpenIdConnect` schéma výzvou používá metoda. Obslužná rutina autorizace, která se používá k ověření `amr` deklarace identity, je přidána do inverze řídicího kontejneru. Pak se vytvoří zásada, která `RequireMfa` požadavek přidá.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

Tato zásada se pak na Razor stránce použije podle potřeby. Zásady se dají globálně přidat i pro celou aplikaci.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

Pokud se uživatel ověřuje bez MFA, `amr` deklarace identity pravděpodobně bude mít `pwd` hodnotu. Požadavek nebude autorizovaný pro přístup k této stránce. Pomocí výchozích hodnot se uživatel přesměruje na stránku *účet/AccessDenied* . Toto chování lze změnit nebo můžete implementovat vlastní logiku. V tomto příkladu se přidá odkaz, aby platný uživatel mohl nastavit MFA pro svůj účet.

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

Nyní mají přístup k této stránce nebo webu pouze uživatelé, kteří ověřují pomocí VÍCEFAKTOROVÉHO ověřování. Pokud se používá jiné typy MFA nebo pokud je 2FA v `amr` pořádku, bude mít deklarace jiné hodnoty a musí se zpracovat správně. Jiné otevřené ID připojení servery také vrátí jiné hodnoty pro tuto deklaraci identity a nemusí následovat po specifikaci [referenčních hodnot metod ověřování](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .

Pokud se přihlašujete bez MFA (například pomocí hesla jenom heslo):

* `amr` Má `pwd` hodnotu:

    ![require_mfa_oidc_02. png](mfa/_static/require_mfa_oidc_02.png)

* Přístup byl odepřen:

    ![require_mfa_oidc_03. png](mfa/_static/require_mfa_oidc_03.png)

Další možností je přihlášení pomocí jednorázového Identityhesla pomocí:

![require_mfa_oidc_01. png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Povolit generování kódu QR pro aplikace TOTP Authenticator v ASP.NET Core](xref:security/authentication/identity-enable-qrcodes)
* [Možnosti ověřování neheslem pro Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless)
* [FIDO2 knihovna .NET pro ověření identity FIDO2/WebAuthn a kontrolní výraz pomocí .NET](https://github.com/abergs/fido2-net-lib)
* [Operace WebAuthn Super](https://github.com/herrjemand/awesome-webauthn)

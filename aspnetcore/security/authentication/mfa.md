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
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="b409d-103">Multi-Factor Authentication v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b409d-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="b409d-104">Od [Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="b409d-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

<span data-ttu-id="b409d-105">Multi-Factor Authentication (MFA) je proces, při kterém se uživatel během přihlašovací události požaduje pro další formy identifikace.</span><span class="sxs-lookup"><span data-stu-id="b409d-105">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="b409d-106">Tato výzva by mohla být zadání kódu z cellphone, použití FIDO2 klíče nebo poskytnutí kontroly otiskem prstu.</span><span class="sxs-lookup"><span data-stu-id="b409d-106">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="b409d-107">Pokud požadujete druhou formu ověřování, zabezpečení se zlepší.</span><span class="sxs-lookup"><span data-stu-id="b409d-107">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="b409d-108">Tento přídavný faktor nemůže útočník snadno získat ani duplikovat.</span><span class="sxs-lookup"><span data-stu-id="b409d-108">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="b409d-109">Tento článek se zabývá následujícími oblastmi:</span><span class="sxs-lookup"><span data-stu-id="b409d-109">This article covers the following areas:</span></span>

* <span data-ttu-id="b409d-110">Co je MFA a jaké jsou doporučené toky MFA</span><span class="sxs-lookup"><span data-stu-id="b409d-110">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="b409d-111">Konfigurace MFA pro stránky správy pomocí ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="b409d-111">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="b409d-112">Odeslat požadavek na přihlášení MFA serveru OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="b409d-112">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="b409d-113">Vynutit, ASP.NET Core OpenID připojit klienta k vyžádání MFA</span><span class="sxs-lookup"><span data-stu-id="b409d-113">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="b409d-114">MFA, 2FA</span><span class="sxs-lookup"><span data-stu-id="b409d-114">MFA, 2FA</span></span>

<span data-ttu-id="b409d-115">MFA vyžaduje aspoň dva nebo více typů ověření identity, jako je třeba něco, co znáte, nebo biometrické ověřování pro uživatele, který chcete ověřit.</span><span class="sxs-lookup"><span data-stu-id="b409d-115">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="b409d-116">Dvojúrovňové ověřování (2FA) je jako podmnožina MFA, ale rozdíl mezi tím, že MFA může vyžadovat dva nebo více faktorů k prokázání identity.</span><span class="sxs-lookup"><span data-stu-id="b409d-116">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="b409d-117">MFA TOTP (jednorázový algoritmus hesla na základě času)</span><span class="sxs-lookup"><span data-stu-id="b409d-117">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="b409d-118">Vícefaktorové ověřování pomocí TOTP je podporovanou implementací Identitypomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b409d-118">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="b409d-119">Tato možnost se dá použít společně s veškerou aplikací dodržujících ověřovacích dat, včetně:</span><span class="sxs-lookup"><span data-stu-id="b409d-119">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="b409d-120">Aplikace Microsoft Authenticator</span><span class="sxs-lookup"><span data-stu-id="b409d-120">Microsoft Authenticator App</span></span>
* <span data-ttu-id="b409d-121">Aplikace Google Authenticator</span><span class="sxs-lookup"><span data-stu-id="b409d-121">Google Authenticator App</span></span>

<span data-ttu-id="b409d-122">Podrobnosti implementace najdete na následujícím odkazu:</span><span class="sxs-lookup"><span data-stu-id="b409d-122">See the following link for implementation details:</span></span>

[<span data-ttu-id="b409d-123">Povolit generování kódu QR pro aplikace TOTP Authenticator v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b409d-123">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="b409d-124">Vícefaktorové ověřování FIDO2 nebo nejenom hesla</span><span class="sxs-lookup"><span data-stu-id="b409d-124">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="b409d-125">FIDO2 je aktuálně:</span><span class="sxs-lookup"><span data-stu-id="b409d-125">FIDO2 is currently:</span></span>

* <span data-ttu-id="b409d-126">Nejbezpečnější způsob dosahování vícefaktorového ověřování.</span><span class="sxs-lookup"><span data-stu-id="b409d-126">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="b409d-127">Jediný tok MFA, který chrání před útoky typu phishing.</span><span class="sxs-lookup"><span data-stu-id="b409d-127">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="b409d-128">V současné době ASP.NET Core nepodporuje přímé FIDO2.</span><span class="sxs-lookup"><span data-stu-id="b409d-128">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="b409d-129">FIDO2 se dají použít pro MFA nebo pro toky nevyužívající hesla.</span><span class="sxs-lookup"><span data-stu-id="b409d-129">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="b409d-130">Azure Active Directory poskytuje podporu pro FIDO2 a bezheslem.</span><span class="sxs-lookup"><span data-stu-id="b409d-130">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="b409d-131">Další informace najdete v tématu [Možnosti ověřování bez hesla pro Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span><span class="sxs-lookup"><span data-stu-id="b409d-131">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="b409d-132">VÍCEFAKTOROVÉ OVĚŘOVÁNÍ SMS</span><span class="sxs-lookup"><span data-stu-id="b409d-132">MFA SMS</span></span>

<span data-ttu-id="b409d-133">VÍCEFAKTOROVÉ ověřování pomocí SMS zvyšuje zabezpečení v porovnání s ověřováním hesla (jedním faktorem).</span><span class="sxs-lookup"><span data-stu-id="b409d-133">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="b409d-134">Použití serveru SMS jako druhého faktoru se však již nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="b409d-134">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="b409d-135">Pro tento typ implementace existuje příliš mnoho známých vektorů útoku.</span><span class="sxs-lookup"><span data-stu-id="b409d-135">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="b409d-136">Pokyny pro NIST</span><span class="sxs-lookup"><span data-stu-id="b409d-136">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-aspnet-core-identity"></a><span data-ttu-id="b409d-137">Konfigurace MFA pro stránky správy pomocí ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="b409d-137">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="b409d-138">Vícefaktorové ověřování může být pro uživatele vynucené k přístupu k citlivým Identity stránkám v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b409d-138">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="b409d-139">To může být užitečné pro aplikace, kde pro různé identity existují různé úrovně přístupu.</span><span class="sxs-lookup"><span data-stu-id="b409d-139">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="b409d-140">Uživatelé můžou například zobrazit profilová data pomocí přihlašovacího hesla, ale pro přístup ke stránkám pro správu musel správce použít MFA.</span><span class="sxs-lookup"><span data-stu-id="b409d-140">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="b409d-141">Prodloužení přihlašovacích údajů s deklarací MFA</span><span class="sxs-lookup"><span data-stu-id="b409d-141">Extend the login with an MFA claim</span></span>

<span data-ttu-id="b409d-142">Ukázkový kód je nastaven pomocí ASP.NET Core se Identity stránkami Razor a.</span><span class="sxs-lookup"><span data-stu-id="b409d-142">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="b409d-143">`AddIdentity` Metoda se používá místo `AddDefaultIdentity` jedné, takže `IUserClaimsPrincipalFactory` implementaci lze použít k přidání deklarací identity do identity po úspěšném přihlášení.</span><span class="sxs-lookup"><span data-stu-id="b409d-143">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

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

<span data-ttu-id="b409d-144">`AdditionalUserClaimsPrincipalFactory` Třída přidá `amr` deklaraci identity uživateli až po úspěšném přihlášení.</span><span class="sxs-lookup"><span data-stu-id="b409d-144">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="b409d-145">Hodnota deklarace identity je čtena z databáze.</span><span class="sxs-lookup"><span data-stu-id="b409d-145">The claim's value is read from the database.</span></span> <span data-ttu-id="b409d-146">Deklarace identity se tady přidá, protože uživatel by měl mít přístup jenom k vyššímu chráněnému zobrazení, pokud se identita přihlásila pomocí MFA.</span><span class="sxs-lookup"><span data-stu-id="b409d-146">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="b409d-147">Pokud je zobrazení databáze čteno z databáze přímo namísto použití deklarace identity, je možné získat přístup k zobrazení bez MFA přímo po aktivaci MFA.</span><span class="sxs-lookup"><span data-stu-id="b409d-147">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

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

<span data-ttu-id="b409d-148">Vzhledem k Identity tomu, že došlo ke `Startup` změně nastavení služby ve třídě, Identity je nutné aktualizovat rozložení.</span><span class="sxs-lookup"><span data-stu-id="b409d-148">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="b409d-149">Vygenerování Identity uživatelského rozhraní stránek do aplikace</span><span class="sxs-lookup"><span data-stu-id="b409d-149">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="b409d-150">Definujte rozložení v souboru \* Identity/Account/Manage/_Layout. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="b409d-150">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="b409d-151">Přiřaďte také rozložení pro všechny stránky pro správu ze Identity stránek:</span><span class="sxs-lookup"><span data-stu-id="b409d-151">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="b409d-152">Ověřit požadavek MFA na stránce pro správu</span><span class="sxs-lookup"><span data-stu-id="b409d-152">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="b409d-153">Stránka Správa Razor ověří, zda se uživatel přihlásil pomocí vícefaktorového ověřování.</span><span class="sxs-lookup"><span data-stu-id="b409d-153">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="b409d-154">V `OnGet` metodě se identita používá pro přístup k deklaracím uživatelů.</span><span class="sxs-lookup"><span data-stu-id="b409d-154">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="b409d-155">`amr` Deklarace identity je kontrolována na hodnotu `mfa`.</span><span class="sxs-lookup"><span data-stu-id="b409d-155">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="b409d-156">Pokud v identitě chybí tato deklarace identity nebo `false`je, stránka se přesměruje na stránku povolit MFA.</span><span class="sxs-lookup"><span data-stu-id="b409d-156">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="b409d-157">To je možné, protože uživatel se už přihlásil, ale bez MFA.</span><span class="sxs-lookup"><span data-stu-id="b409d-157">This is possible because the user has logged in already, but without MFA.</span></span>

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

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="b409d-158">Logika uživatelského rozhraní pro přepnutí přihlašovacích údajů uživatele</span><span class="sxs-lookup"><span data-stu-id="b409d-158">UI logic to toggle user login information</span></span>

<span data-ttu-id="b409d-159">Při spuštění se přidaly zásady autorizace.</span><span class="sxs-lookup"><span data-stu-id="b409d-159">An authorization policy was added at startup.</span></span> <span data-ttu-id="b409d-160">Tato zásada vyžaduje `amr` deklaraci identity s hodnotou `mfa`.</span><span class="sxs-lookup"><span data-stu-id="b409d-160">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="b409d-161">Tato zásada se pak dá v `_Layout` zobrazení použít k zobrazení nebo skrytí nabídky **správce** s upozorněním:</span><span class="sxs-lookup"><span data-stu-id="b409d-161">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="b409d-162">Pokud se identita přihlásila pomocí vícefaktorového ověřování (MFA), zobrazí se nabídka **správce** bez upozornění popisu tlačítka.</span><span class="sxs-lookup"><span data-stu-id="b409d-162">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="b409d-163">Když se uživatel přihlásí bez MFA, **zobrazí se spolu** s popisem tlačítka, který uživatele informuje (vysvětlení upozornění).</span><span class="sxs-lookup"><span data-stu-id="b409d-163">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

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

<span data-ttu-id="b409d-164">Pokud se uživatel přihlásí bez MFA, zobrazí se upozornění:</span><span class="sxs-lookup"><span data-stu-id="b409d-164">If the user logs in without MFA, the warning is displayed:</span></span>

![Ověřování MFA pro správce](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="b409d-166">Po kliknutí na odkaz pro **správu** se uživatel přesměruje na zobrazení pro povolení MFA:</span><span class="sxs-lookup"><span data-stu-id="b409d-166">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![Správce aktivuje ověřování MFA.](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="b409d-168">Odeslat požadavek na přihlášení MFA serveru OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="b409d-168">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="b409d-169">`acr_values` Parametr lze použít k předání `mfa` požadované hodnoty z klienta na server v žádosti o ověření.</span><span class="sxs-lookup"><span data-stu-id="b409d-169">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="b409d-170">Aby `acr_values` Tato funkce fungovala, musí být na serveru otevřeného ID Connect zpracován parametr.</span><span class="sxs-lookup"><span data-stu-id="b409d-170">The `acr_values` parameter needs to be handled on the Open ID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="b409d-171">OpenID Connect ASP.NET Core klienta</span><span class="sxs-lookup"><span data-stu-id="b409d-171">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="b409d-172">Aplikace ASP.NET Core Razor Open ID Connect Client App používá `AddOpenIdConnect` metodu pro přihlášení k serveru s otevřeným ID Connect.</span><span class="sxs-lookup"><span data-stu-id="b409d-172">The ASP.NET Core Razor Pages Open ID Connect client app uses the `AddOpenIdConnect` method to login to the Open ID Connect server.</span></span> <span data-ttu-id="b409d-173">`acr_values` Parametr je nastaven s `mfa` hodnotou a odeslán s požadavkem na ověření.</span><span class="sxs-lookup"><span data-stu-id="b409d-173">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="b409d-174">`OpenIdConnectEvents` Slouží k přidání.</span><span class="sxs-lookup"><span data-stu-id="b409d-174">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="b409d-175">Doporučené `acr_values` hodnoty parametrů najdete v tématu [referenční hodnoty metod ověřování](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span><span class="sxs-lookup"><span data-stu-id="b409d-175">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

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

### <a name="example-openid-connect-identityserver-4-server-with-aspnet-core-identity"></a><span data-ttu-id="b409d-176">Příklad OpenID připojení serveru IdentityServer 4 pomocí ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="b409d-176">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="b409d-177">Na serveru OpenID Connect, který je implementován pomocí ASP.NET Core Identity s zobrazeními MVC, je vytvořeno nové zobrazení s názvem *ErrorEnable2FA. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="b409d-177">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="b409d-178">Zobrazení:</span><span class="sxs-lookup"><span data-stu-id="b409d-178">The view:</span></span>

* <span data-ttu-id="b409d-179">Zobrazuje, jestli Identity pochází z aplikace, která vyžaduje MFA, ale uživatel je neaktivoval v Identity.</span><span class="sxs-lookup"><span data-stu-id="b409d-179">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="b409d-180">Informuje uživatele a přidá odkaz pro aktivaci.</span><span class="sxs-lookup"><span data-stu-id="b409d-180">Informs the user and adds a link to activate this.</span></span>

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

<span data-ttu-id="b409d-181">V `Login` metodě se implementace `IIdentityServerInteractionService` `_interaction` rozhraní používá pro přístup k parametrům Open ID Connect Request.</span><span class="sxs-lookup"><span data-stu-id="b409d-181">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the Open ID Connect request parameters.</span></span> <span data-ttu-id="b409d-182">K `acr_values` parametru je přistup pomocí `AcrValues` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b409d-182">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="b409d-183">Jak klient tuto `mfa` možnost odeslal, je možné ho zkontrolovat.</span><span class="sxs-lookup"><span data-stu-id="b409d-183">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="b409d-184">Pokud je vyžadováno MFA a uživatel v ASP.NET Core Identity má povolené MFA, přihlášení pokračuje.</span><span class="sxs-lookup"><span data-stu-id="b409d-184">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="b409d-185">Pokud uživatel nemá povolené vícefaktorové ověřování, uživatel se přesměruje na vlastní zobrazení *ErrorEnable2FA. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b409d-185">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml*.</span></span> <span data-ttu-id="b409d-186">Pak ASP.NET Core Identity podepíše uživatele v.</span><span class="sxs-lookup"><span data-stu-id="b409d-186">Then ASP.NET Core Identity signs the user in.</span></span>

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

<span data-ttu-id="b409d-187">`ExternalLoginCallback` Metoda funguje jako místní Identity přihlášení.</span><span class="sxs-lookup"><span data-stu-id="b409d-187">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="b409d-188">`AcrValues` Vlastnost je kontrolována na `mfa` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b409d-188">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="b409d-189">Pokud je `mfa` hodnota přítomna, ověřování MFA je vynuceno před dokončením přihlášení (například přesměrované `ErrorEnable2FA` na zobrazení).</span><span class="sxs-lookup"><span data-stu-id="b409d-189">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

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

<span data-ttu-id="b409d-190">Pokud je už uživatel přihlášený, klientská aplikace:</span><span class="sxs-lookup"><span data-stu-id="b409d-190">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="b409d-191">Stále ověřuje `amr` deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="b409d-191">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="b409d-192">Můžete nastavit MFA s odkazem na zobrazení ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="b409d-192">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="b409d-194">Vynutit, ASP.NET Core OpenID připojit klienta k vyžádání MFA</span><span class="sxs-lookup"><span data-stu-id="b409d-194">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="b409d-195">Tento příklad ukazuje, jak aplikace Razor ASP.NET Core Page, která používá OpenID Connect k přihlášení, může vyžadovat, aby se uživatelé ověřili pomocí vícefaktorového ověřování.</span><span class="sxs-lookup"><span data-stu-id="b409d-195">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="b409d-196">Chcete-li ověřit požadavek MFA, `IAuthorizationRequirement` je vytvořen požadavek.</span><span class="sxs-lookup"><span data-stu-id="b409d-196">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="b409d-197">Tato stránka se přidá na stránky pomocí zásady, která vyžaduje MFA.</span><span class="sxs-lookup"><span data-stu-id="b409d-197">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="b409d-198">`AuthorizationHandler` Je implementována, která bude používat `amr` deklaraci identity a kontroluje hodnotu `mfa`.</span><span class="sxs-lookup"><span data-stu-id="b409d-198">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="b409d-199">`amr` Je vrácen v rámci `id_token` úspěšného ověření a může mít mnoho různých hodnot definovaných ve specifikaci [referenčních hodnot metod ověřování](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="b409d-199">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="b409d-200">Vrácená hodnota závisí na tom, jak identita byla ověřena, a na základě implementace serveru Connect pro připojení.</span><span class="sxs-lookup"><span data-stu-id="b409d-200">The returned value depends on how the identity authenticated and on the Open ID Connect server implementation.</span></span>

<span data-ttu-id="b409d-201">`AuthorizationHandler` Použije `RequireMfa` požadavek a ověří `amr` deklaraci identity.</span><span class="sxs-lookup"><span data-stu-id="b409d-201">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="b409d-202">Server OpenID Connect se dá implementovat pomocí IdentityServer4 s ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="b409d-202">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="b409d-203">Když se uživatel přihlásí pomocí TOTP, `amr` deklarace identity se vrátí s hodnotou MFA.</span><span class="sxs-lookup"><span data-stu-id="b409d-203">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="b409d-204">Pokud používáte jinou implementaci serveru OpenID Connect nebo jiný typ MFA, `amr` deklarace identity bude nebo může mít jinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b409d-204">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="b409d-205">Aby bylo možné tuto akci přijmout, je nutné kód rozšířit.</span><span class="sxs-lookup"><span data-stu-id="b409d-205">The code must be extended to accept this as well.</span></span>

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

<span data-ttu-id="b409d-206">V `Startup.ConfigureServices` metodě se jako výchozí `AddOpenIdConnect` schéma výzvou používá metoda.</span><span class="sxs-lookup"><span data-stu-id="b409d-206">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="b409d-207">Obslužná rutina autorizace, která se používá k ověření `amr` deklarace identity, je přidána do inverze řídicího kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b409d-207">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="b409d-208">Pak se vytvoří zásada, která `RequireMfa` požadavek přidá.</span><span class="sxs-lookup"><span data-stu-id="b409d-208">A policy is then created which adds the `RequireMfa` requirement.</span></span>

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

<span data-ttu-id="b409d-209">Tato zásada se pak na Razor stránce použije podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="b409d-209">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="b409d-210">Zásady se dají globálně přidat i pro celou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b409d-210">The policy could be added globally for the entire app as well.</span></span>

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

<span data-ttu-id="b409d-211">Pokud se uživatel ověřuje bez MFA, `amr` deklarace identity pravděpodobně bude mít `pwd` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b409d-211">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="b409d-212">Požadavek nebude autorizovaný pro přístup k této stránce.</span><span class="sxs-lookup"><span data-stu-id="b409d-212">The request won't be authorized to access the page.</span></span> <span data-ttu-id="b409d-213">Pomocí výchozích hodnot se uživatel přesměruje na stránku *účet/AccessDenied* .</span><span class="sxs-lookup"><span data-stu-id="b409d-213">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="b409d-214">Toto chování lze změnit nebo můžete implementovat vlastní logiku.</span><span class="sxs-lookup"><span data-stu-id="b409d-214">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="b409d-215">V tomto příkladu se přidá odkaz, aby platný uživatel mohl nastavit MFA pro svůj účet.</span><span class="sxs-lookup"><span data-stu-id="b409d-215">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

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

<span data-ttu-id="b409d-216">Nyní mají přístup k této stránce nebo webu pouze uživatelé, kteří ověřují pomocí VÍCEFAKTOROVÉHO ověřování.</span><span class="sxs-lookup"><span data-stu-id="b409d-216">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="b409d-217">Pokud se používá jiné typy MFA nebo pokud je 2FA v `amr` pořádku, bude mít deklarace jiné hodnoty a musí se zpracovat správně.</span><span class="sxs-lookup"><span data-stu-id="b409d-217">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="b409d-218">Jiné otevřené ID připojení servery také vrátí jiné hodnoty pro tuto deklaraci identity a nemusí následovat po specifikaci [referenčních hodnot metod ověřování](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) .</span><span class="sxs-lookup"><span data-stu-id="b409d-218">Different Open ID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="b409d-219">Pokud se přihlašujete bez MFA (například pomocí hesla jenom heslo):</span><span class="sxs-lookup"><span data-stu-id="b409d-219">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="b409d-220">`amr` Má `pwd` hodnotu:</span><span class="sxs-lookup"><span data-stu-id="b409d-220">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02. png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="b409d-222">Přístup byl odepřen:</span><span class="sxs-lookup"><span data-stu-id="b409d-222">Access is denied:</span></span>

    ![require_mfa_oidc_03. png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="b409d-224">Další možností je přihlášení pomocí jednorázového Identityhesla pomocí:</span><span class="sxs-lookup"><span data-stu-id="b409d-224">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01. png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="b409d-226">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b409d-226">Additional resources</span></span>

* [<span data-ttu-id="b409d-227">Povolit generování kódu QR pro aplikace TOTP Authenticator v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b409d-227">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="b409d-228">Možnosti ověřování neheslem pro Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="b409d-228">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="b409d-229">FIDO2 knihovna .NET pro ověření identity FIDO2/WebAuthn a kontrolní výraz pomocí .NET</span><span class="sxs-lookup"><span data-stu-id="b409d-229">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="b409d-230">Operace WebAuthn Super</span><span class="sxs-lookup"><span data-stu-id="b409d-230">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)

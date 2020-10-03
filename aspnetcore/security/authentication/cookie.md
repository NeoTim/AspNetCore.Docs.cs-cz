---
title: Použít cookie ověřování bez ASP.NET Core Identity
author: rick-anderson
description: Naučte se používat cookie ověřování bez ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 211b6c7ec0bc7a48671e614427961cb332d06aa3
ms.sourcegitcommit: c0a15ab8549cb729731a0fdf1d7da0b7feaa11ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91671766"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="2694a-103">Použít cookie ověřování bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2694a-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="2694a-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2694a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2694a-105">ASP.NET Core Identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="2694a-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="2694a-106">Nicméně cookie poskytovatele ověřování založeného na typu se ASP.NET Core Identity dá použít.</span><span class="sxs-lookup"><span data-stu-id="2694a-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="2694a-107">Další informace naleznete v tématu <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="2694a-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="2694a-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2694a-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2694a-109">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="2694a-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="2694a-110">K přihlášení uživatele použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo.</span><span class="sxs-lookup"><span data-stu-id="2694a-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="2694a-111">Uživatel je ověřený v `AuthenticateUser` metodě v souboru *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="2694a-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="2694a-112">V reálném příkladu by byl uživatel ověřený proti databázi.</span><span class="sxs-lookup"><span data-stu-id="2694a-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="2694a-113">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="2694a-113">Configuration</span></span>

<span data-ttu-id="2694a-114">V `Startup.ConfigureServices` metodě vytvořte služby ověřování middlewaru pomocí <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod a:</span><span class="sxs-lookup"><span data-stu-id="2694a-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="2694a-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> předáno pro `AddAuthentication` Nastavení výchozího schématu ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2694a-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="2694a-116">`AuthenticationScheme` je užitečné v případě, že existuje více instancí cookie ověřování a vy chcete [autorizovat pomocí konkrétního schématu](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="2694a-116">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="2694a-117">Nastavení `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje Cookie pro schéma hodnotu "s".</span><span class="sxs-lookup"><span data-stu-id="2694a-117">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="2694a-118">Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="2694a-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="2694a-119">Schéma ověřování aplikace se liší od cookie schématu ověřování aplikace.</span><span class="sxs-lookup"><span data-stu-id="2694a-119">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="2694a-120">Pokud cookie není k dispozici schéma ověřování pro <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , používá `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="2694a-120">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="2694a-121">Vlastnost ověřování cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je standardně nastavená na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="2694a-121">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="2694a-122">Ověřování cookie s je povoleno, pokud návštěvník webu nesouhlasí s shromažďováním dat.</span><span class="sxs-lookup"><span data-stu-id="2694a-122">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="2694a-123">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="2694a-123">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="2694a-124">V `Startup.Configure` , zavolejte `UseAuthentication` a a `UseAuthorization` nastavte `HttpContext.User` vlastnost a spusťte middleware autorizace pro požadavky.</span><span class="sxs-lookup"><span data-stu-id="2694a-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="2694a-125">`UseAuthentication` `UseAuthorization` Před voláním volejte metody a `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="2694a-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="2694a-126"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Třída se používá ke konfiguraci možností poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="2694a-126">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="2694a-127">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="2694a-127">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="2694a-128">Cookie Middleware zásad</span><span class="sxs-lookup"><span data-stu-id="2694a-128">Cookie Policy Middleware</span></span>

<span data-ttu-id="2694a-129">[ Cookie Middleware zásad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje cookie Možnosti zásad.</span><span class="sxs-lookup"><span data-stu-id="2694a-129">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="2694a-130">Přidání middlewaru do kanálu zpracování aplikace je v pořádku &mdash; . týká se pouze podřízených komponent registrovaných v kanálu.</span><span class="sxs-lookup"><span data-stu-id="2694a-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="2694a-131">Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytované pro Cookie middleware zásad k řízení globálních charakteristik cookie zpracování a cookie připojení k obslužným rutinám zpracování, pokud cookie jsou připojeny nebo odstraněny.</span><span class="sxs-lookup"><span data-stu-id="2694a-131">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="2694a-132">Výchozí <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnota je `SameSiteMode.Lax` Povolit ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="2694a-132">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="2694a-133">Chcete-li striktně vyhovět zásadě stejného serveru `SameSiteMode.Strict` , nastavte `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="2694a-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="2694a-134">I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň cookie zabezpečení pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="2694a-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="2694a-135">CookieNastavení middlewaru zásad pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle matice níže.</span><span class="sxs-lookup"><span data-stu-id="2694a-135">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="2694a-136">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="2694a-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="2694a-137">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="2694a-137">Cookie.SameSite</span></span> | <span data-ttu-id="2694a-138">Výsledek Cookie . Nastavení SameSite</span><span class="sxs-lookup"><span data-stu-id="2694a-138">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="2694a-139">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2694a-139">SameSiteMode.None</span></span>     | <span data-ttu-id="2694a-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2694a-140">SameSiteMode.None</span></span><br><span data-ttu-id="2694a-141">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-142">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="2694a-143">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2694a-143">SameSiteMode.None</span></span><br><span data-ttu-id="2694a-144">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-145">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2694a-146">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="2694a-147">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2694a-147">SameSiteMode.None</span></span><br><span data-ttu-id="2694a-148">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-149">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="2694a-150">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-152">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2694a-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="2694a-154">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2694a-154">SameSiteMode.None</span></span><br><span data-ttu-id="2694a-155">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-156">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="2694a-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="2694a-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="2694a-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="2694a-160">Vytvoření ověřování cookie</span><span class="sxs-lookup"><span data-stu-id="2694a-160">Create an authentication cookie</span></span>

<span data-ttu-id="2694a-161">Chcete-li vytvořit cookie informace o uživatelích obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="2694a-161">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="2694a-162">Informace o uživateli jsou serializovány a uloženy v cookie .</span><span class="sxs-lookup"><span data-stu-id="2694a-162">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="2694a-163">Vytvořit <xref:System.Security.Claims.ClaimsIdentity> s libovolným vyžadovaným <xref:System.Security.Claims.Claim> s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="2694a-163">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="2694a-164">`SignInAsync` Vytvoří zašifrovaný objekt cookie a přidá ho k aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2694a-164">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="2694a-165">Pokud `AuthenticationScheme` není zadaný, použije se výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="2694a-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="2694a-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> se ve výchozím nastavení používá jenom pro několik specifických cest, například přihlašovací cesta a cesty odhlášení.</span><span class="sxs-lookup"><span data-stu-id="2694a-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="2694a-167">Další informace najdete v tématu [ Cookie AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="2694a-167">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="2694a-168">Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování.</span><span class="sxs-lookup"><span data-stu-id="2694a-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="2694a-169">Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.</span><span class="sxs-lookup"><span data-stu-id="2694a-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="2694a-170">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="2694a-170">Sign out</span></span>

<span data-ttu-id="2694a-171">Chcete-li odhlásit aktuálního uživatele a odstranit jeho cookie volání, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="2694a-171">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="2694a-172">Pokud `CookieAuthenticationDefaults.AuthenticationScheme` (nebo " Cookie s") se nepoužívá jako schéma (například "contoso Cookie "), zadejte schéma používané při konfiguraci zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="2694a-172">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="2694a-173">V opačném případě se použije výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="2694a-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="2694a-174">Když se prohlížeč zavře, automaticky odstraní relaci na základě cookie (netrvalé cookie s), ale cookie při zavření jednotlivé karty se nevymaže žádné.</span><span class="sxs-lookup"><span data-stu-id="2694a-174">When the browser closes it automatically deletes session based cookies (non-persistent cookies), but no cookies are cleared when an individual tab is closed.</span></span> <span data-ttu-id="2694a-175">Server není upozorněn na události zavření karty nebo prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2694a-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="2694a-176">Reakce na back-endové změny</span><span class="sxs-lookup"><span data-stu-id="2694a-176">React to back-end changes</span></span>

<span data-ttu-id="2694a-177">Po cookie vytvoření je cookie jediným zdrojem identity.</span><span class="sxs-lookup"><span data-stu-id="2694a-177">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="2694a-178">Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="2694a-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="2694a-179">cookieSystém ověřování aplikace nadále zpracovává požadavky na základě ověření cookie .</span><span class="sxs-lookup"><span data-stu-id="2694a-179">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="2694a-180">Uživatel zůstane přihlášený k aplikaci, pokud cookie je ověřování platné.</span><span class="sxs-lookup"><span data-stu-id="2694a-180">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="2694a-181"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Událost se dá použít k zachycení a přepsání ověřování cookie identity.</span><span class="sxs-lookup"><span data-stu-id="2694a-181">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="2694a-182">Ověřování cookie u každé žádosti snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2694a-182">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="2694a-183">Jeden z přístupů k cookie ověření je založen na sledování, kdy se uživatel změní.</span><span class="sxs-lookup"><span data-stu-id="2694a-183">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="2694a-184">Pokud se databáze od vydání uživatele nezměnila cookie , není nutné znovu ověřit uživatele, pokud cookie je jejich platnost stále platná.</span><span class="sxs-lookup"><span data-stu-id="2694a-184">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="2694a-185">V ukázkové aplikaci je databáze implementována v `IUserRepository` a ukládá `LastChanged` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2694a-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="2694a-186">Při aktualizaci uživatele v databázi `LastChanged` je hodnota nastavena na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="2694a-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="2694a-187">Chcete-li zrušit platnost cookie při změně databáze na základě `LastChanged` hodnoty, vytvořte objekt cookie s `LastChanged` deklarací identity obsahující aktuální `LastChanged` hodnotu z databáze:</span><span class="sxs-lookup"><span data-stu-id="2694a-187">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="2694a-188">Chcete-li implementovat přepsání pro `ValidatePrincipal` událost, napište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="2694a-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="2694a-189">Následuje příklad implementace `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="2694a-189">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="2694a-190">Zaregistrujte instanci události během cookie Registrace služby v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="2694a-190">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2694a-191">Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro `CustomCookieAuthenticationEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="2694a-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="2694a-192">Vezměte v úvahu situaci, kdy je jméno uživatele Aktualizováno &mdash; rozhodnutím, které nijak neovlivňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="2694a-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="2694a-193">Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte `context.ShouldRenew` vlastnost na `true` .</span><span class="sxs-lookup"><span data-stu-id="2694a-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2694a-194">Přístup, který je zde popsán, se spustí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="2694a-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="2694a-195">Ověřování ověřování cookie u všech uživatelů na všech žádostech může mít za následek velkou penalizaci výkonu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2694a-195">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="2694a-196">Trvalé cookie s</span><span class="sxs-lookup"><span data-stu-id="2694a-196">Persistent cookies</span></span>

<span data-ttu-id="2694a-197">Možná budete chtít, cookie aby trvala v rámci relací prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2694a-197">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="2694a-198">Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu.</span><span class="sxs-lookup"><span data-stu-id="2694a-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="2694a-199">Následující fragment kódu vytvoří identitu a odpovídající cookie , která je zachována v případě uzavření prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2694a-199">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="2694a-200">Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="2694a-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="2694a-201">Pokud cookie vyprší platnost při zavření prohlížeče, prohlížeč cookie po jeho restartování zruší.</span><span class="sxs-lookup"><span data-stu-id="2694a-201">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="2694a-202">Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="2694a-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="2694a-203">Absolutní cookie vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="2694a-203">Absolute cookie expiration</span></span>

<span data-ttu-id="2694a-204">Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="2694a-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="2694a-205">Aby bylo možné vytvořit trvalé cookie , `IsPersistent` musí být také nastaveno.</span><span class="sxs-lookup"><span data-stu-id="2694a-205">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="2694a-206">V opačném případě se cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje.</span><span class="sxs-lookup"><span data-stu-id="2694a-206">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="2694a-207">Když `ExpiresUtc` je nastaveno, přepíše hodnotu <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , pokud je nastavena.</span><span class="sxs-lookup"><span data-stu-id="2694a-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="2694a-208">Následující fragment kódu vytvoří identitu a odpovídajícím způsobem cookie , který trvá 20 minut.</span><span class="sxs-lookup"><span data-stu-id="2694a-208">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="2694a-209">Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="2694a-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2694a-210">ASP.NET Core Identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="2694a-210">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="2694a-211">Nicméně cookie poskytovatele ověřování založeného na typu se ASP.NET Core Identity dá použít.</span><span class="sxs-lookup"><span data-stu-id="2694a-211">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="2694a-212">Další informace naleznete v tématu <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="2694a-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="2694a-213">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2694a-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2694a-214">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="2694a-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="2694a-215">K přihlášení uživatele použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo.</span><span class="sxs-lookup"><span data-stu-id="2694a-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="2694a-216">Uživatel je ověřený v `AuthenticateUser` metodě v souboru *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="2694a-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="2694a-217">V reálném příkladu by byl uživatel ověřený proti databázi.</span><span class="sxs-lookup"><span data-stu-id="2694a-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="2694a-218">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="2694a-218">Configuration</span></span>

<span data-ttu-id="2694a-219">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro [Microsoft. AspNetCore. Authentication. Cookie balíček s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="2694a-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="2694a-220">V `Startup.ConfigureServices` metodě vytvořte službu ověřování middlewaru pomocí <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod a:</span><span class="sxs-lookup"><span data-stu-id="2694a-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="2694a-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> předáno pro `AddAuthentication` Nastavení výchozího schématu ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2694a-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="2694a-222">`AuthenticationScheme` je užitečné v případě, že existuje více instancí cookie ověřování a vy chcete [autorizovat pomocí konkrétního schématu](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="2694a-222">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="2694a-223">Nastavení `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje Cookie pro schéma hodnotu "s".</span><span class="sxs-lookup"><span data-stu-id="2694a-223">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="2694a-224">Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="2694a-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="2694a-225">Schéma ověřování aplikace se liší od cookie schématu ověřování aplikace.</span><span class="sxs-lookup"><span data-stu-id="2694a-225">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="2694a-226">Pokud cookie není k dispozici schéma ověřování pro <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , používá `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="2694a-226">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="2694a-227">Vlastnost ověřování cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je standardně nastavená na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="2694a-227">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="2694a-228">Ověřování cookie s je povoleno, pokud návštěvník webu nesouhlasí s shromažďováním dat.</span><span class="sxs-lookup"><span data-stu-id="2694a-228">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="2694a-229">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="2694a-229">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="2694a-230">V `Startup.Configure` metodě zavolejte `UseAuthentication` metodu pro vyvolání middleware ověřování, který nastaví `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="2694a-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="2694a-231">Zavolejte `UseAuthentication` metodu před voláním `UseMvcWithDefaultRoute` nebo `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="2694a-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="2694a-232"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Třída se používá ke konfiguraci možností poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="2694a-232">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="2694a-233">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="2694a-233">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="2694a-234">Cookie Middleware zásad</span><span class="sxs-lookup"><span data-stu-id="2694a-234">Cookie Policy Middleware</span></span>

<span data-ttu-id="2694a-235">[ Cookie Middleware zásad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje cookie Možnosti zásad.</span><span class="sxs-lookup"><span data-stu-id="2694a-235">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="2694a-236">Přidání middlewaru do kanálu zpracování aplikace je v pořádku &mdash; . týká se pouze podřízených komponent registrovaných v kanálu.</span><span class="sxs-lookup"><span data-stu-id="2694a-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="2694a-237">Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytované pro Cookie middleware zásad k řízení globálních charakteristik cookie zpracování a cookie připojení k obslužným rutinám zpracování, pokud cookie jsou připojeny nebo odstraněny.</span><span class="sxs-lookup"><span data-stu-id="2694a-237">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="2694a-238">Výchozí <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnota je `SameSiteMode.Lax` Povolit ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="2694a-238">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="2694a-239">Chcete-li striktně vyhovět zásadě stejného serveru `SameSiteMode.Strict` , nastavte `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="2694a-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="2694a-240">I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň cookie zabezpečení pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="2694a-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="2694a-241">CookieNastavení middlewaru zásad pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle matice níže.</span><span class="sxs-lookup"><span data-stu-id="2694a-241">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="2694a-242">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="2694a-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="2694a-243">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="2694a-243">Cookie.SameSite</span></span> | <span data-ttu-id="2694a-244">Výsledek Cookie . Nastavení SameSite</span><span class="sxs-lookup"><span data-stu-id="2694a-244">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="2694a-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2694a-245">SameSiteMode.None</span></span>     | <span data-ttu-id="2694a-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2694a-246">SameSiteMode.None</span></span><br><span data-ttu-id="2694a-247">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-248">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="2694a-249">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2694a-249">SameSiteMode.None</span></span><br><span data-ttu-id="2694a-250">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-251">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2694a-252">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="2694a-253">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2694a-253">SameSiteMode.None</span></span><br><span data-ttu-id="2694a-254">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-255">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="2694a-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-257">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2694a-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="2694a-260">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="2694a-260">SameSiteMode.None</span></span><br><span data-ttu-id="2694a-261">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="2694a-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="2694a-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="2694a-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="2694a-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="2694a-265">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="2694a-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="2694a-266">Vytvoření ověřování cookie</span><span class="sxs-lookup"><span data-stu-id="2694a-266">Create an authentication cookie</span></span>

<span data-ttu-id="2694a-267">Chcete-li vytvořit cookie informace o uživatelích obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="2694a-267">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="2694a-268">Informace o uživateli jsou serializovány a uloženy v cookie .</span><span class="sxs-lookup"><span data-stu-id="2694a-268">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="2694a-269">Vytvořit <xref:System.Security.Claims.ClaimsIdentity> s libovolným vyžadovaným <xref:System.Security.Claims.Claim> s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="2694a-269">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="2694a-270">`SignInAsync` Vytvoří zašifrovaný objekt cookie a přidá ho k aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2694a-270">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="2694a-271">Pokud `AuthenticationScheme` není zadaný, použije se výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="2694a-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="2694a-272">Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování.</span><span class="sxs-lookup"><span data-stu-id="2694a-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="2694a-273">Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.</span><span class="sxs-lookup"><span data-stu-id="2694a-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="2694a-274">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="2694a-274">Sign out</span></span>

<span data-ttu-id="2694a-275">Chcete-li odhlásit aktuálního uživatele a odstranit jeho cookie volání, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="2694a-275">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="2694a-276">Pokud `CookieAuthenticationDefaults.AuthenticationScheme` (nebo " Cookie s") se nepoužívá jako schéma (například "contoso Cookie "), zadejte schéma používané při konfiguraci zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="2694a-276">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="2694a-277">V opačném případě se použije výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="2694a-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="2694a-278">Reakce na back-endové změny</span><span class="sxs-lookup"><span data-stu-id="2694a-278">React to back-end changes</span></span>

<span data-ttu-id="2694a-279">Po cookie vytvoření je cookie jediným zdrojem identity.</span><span class="sxs-lookup"><span data-stu-id="2694a-279">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="2694a-280">Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="2694a-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="2694a-281">cookieSystém ověřování aplikace nadále zpracovává požadavky na základě ověření cookie .</span><span class="sxs-lookup"><span data-stu-id="2694a-281">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="2694a-282">Uživatel zůstane přihlášený k aplikaci, pokud cookie je ověřování platné.</span><span class="sxs-lookup"><span data-stu-id="2694a-282">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="2694a-283"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Událost se dá použít k zachycení a přepsání ověřování cookie identity.</span><span class="sxs-lookup"><span data-stu-id="2694a-283">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="2694a-284">Ověřování cookie u každé žádosti snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2694a-284">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="2694a-285">Jeden z přístupů k cookie ověření je založen na sledování, kdy se uživatel změní.</span><span class="sxs-lookup"><span data-stu-id="2694a-285">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="2694a-286">Pokud se databáze od vydání uživatele nezměnila cookie , není nutné znovu ověřit uživatele, pokud cookie je jejich platnost stále platná.</span><span class="sxs-lookup"><span data-stu-id="2694a-286">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="2694a-287">V ukázkové aplikaci je databáze implementována v `IUserRepository` a ukládá `LastChanged` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2694a-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="2694a-288">Při aktualizaci uživatele v databázi `LastChanged` je hodnota nastavena na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="2694a-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="2694a-289">Chcete-li zrušit platnost cookie při změně databáze na základě `LastChanged` hodnoty, vytvořte objekt cookie s `LastChanged` deklarací identity obsahující aktuální `LastChanged` hodnotu z databáze:</span><span class="sxs-lookup"><span data-stu-id="2694a-289">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="2694a-290">Chcete-li implementovat přepsání pro `ValidatePrincipal` událost, napište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="2694a-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="2694a-291">Následuje příklad implementace `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="2694a-291">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="2694a-292">Zaregistrujte instanci události během cookie Registrace služby v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="2694a-292">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2694a-293">Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro `CustomCookieAuthenticationEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="2694a-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="2694a-294">Vezměte v úvahu situaci, kdy je jméno uživatele Aktualizováno &mdash; rozhodnutím, které nijak neovlivňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="2694a-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="2694a-295">Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte `context.ShouldRenew` vlastnost na `true` .</span><span class="sxs-lookup"><span data-stu-id="2694a-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2694a-296">Přístup, který je zde popsán, se spustí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="2694a-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="2694a-297">Ověřování ověřování cookie u všech uživatelů na všech žádostech může mít za následek velkou penalizaci výkonu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2694a-297">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="2694a-298">Trvalé cookie s</span><span class="sxs-lookup"><span data-stu-id="2694a-298">Persistent cookies</span></span>

<span data-ttu-id="2694a-299">Možná budete chtít, cookie aby trvala v rámci relací prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2694a-299">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="2694a-300">Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu.</span><span class="sxs-lookup"><span data-stu-id="2694a-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="2694a-301">Následující fragment kódu vytvoří identitu a odpovídající cookie , která je zachována v případě uzavření prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2694a-301">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="2694a-302">Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="2694a-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="2694a-303">Pokud cookie vyprší platnost při zavření prohlížeče, prohlížeč cookie po jeho restartování zruší.</span><span class="sxs-lookup"><span data-stu-id="2694a-303">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="2694a-304">Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="2694a-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="2694a-305">Absolutní cookie vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="2694a-305">Absolute cookie expiration</span></span>

<span data-ttu-id="2694a-306">Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="2694a-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="2694a-307">Aby bylo možné vytvořit trvalé cookie , `IsPersistent` musí být také nastaveno.</span><span class="sxs-lookup"><span data-stu-id="2694a-307">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="2694a-308">V opačném případě se cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje.</span><span class="sxs-lookup"><span data-stu-id="2694a-308">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="2694a-309">Když `ExpiresUtc` je nastaveno, přepíše hodnotu <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , pokud je nastavena.</span><span class="sxs-lookup"><span data-stu-id="2694a-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="2694a-310">Následující fragment kódu vytvoří identitu a odpovídajícím způsobem cookie , který trvá 20 minut.</span><span class="sxs-lookup"><span data-stu-id="2694a-310">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="2694a-311">Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="2694a-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2694a-312">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="2694a-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="2694a-313">Kontroly rolí na základě zásad</span><span class="sxs-lookup"><span data-stu-id="2694a-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

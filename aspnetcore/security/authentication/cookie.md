---
title: Použít ověřování souborem cookie bez ASP.NET CoreIdentity
author: rick-anderson
description: Naučte se používat ověřování souborem cookie bez ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 401d03352b8c2c040202716bdddf484e3b778f52
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408822"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="6bd7a-103">Použít ověřování souborem cookie bez ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="6bd7a-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="6bd7a-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6bd7a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6bd7a-105">ASP.NET Core Identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="6bd7a-106">Nicméně nelze použít zprostředkovatele ověřování na základě souborů cookie bez ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="6bd7a-107">Další informace naleznete v tématu <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="6bd7a-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6bd7a-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6bd7a-109">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="6bd7a-110">K přihlášení uživatele použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="6bd7a-111">Uživatel je ověřený v `AuthenticateUser` metodě v souboru *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="6bd7a-112">V reálném příkladu by byl uživatel ověřený proti databázi.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="6bd7a-113">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="6bd7a-113">Configuration</span></span>

<span data-ttu-id="6bd7a-114">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro balíček [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="6bd7a-115">V `Startup.ConfigureServices` metodě vytvořte služby ověřování middlewaru pomocí <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod a:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="6bd7a-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>předáno pro `AddAuthentication` Nastavení výchozího schématu ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="6bd7a-117">`AuthenticationScheme`je užitečné v případě, že existuje více instancí ověřování souborem cookie a chcete [autorizovat s konkrétním schématem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="6bd7a-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="6bd7a-118">Nastavení `AuthenticationScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje pro schéma hodnotu "cookies".</span><span class="sxs-lookup"><span data-stu-id="6bd7a-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="6bd7a-119">Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="6bd7a-120">Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikace.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="6bd7a-121">Pokud není k dispozici schéma ověřování souborů cookie <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , použije se `CookieAuthenticationDefaults.AuthenticationScheme` ("soubory cookie").</span><span class="sxs-lookup"><span data-stu-id="6bd7a-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="6bd7a-122">Vlastnost ověřovacího souboru cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je standardně nastavená na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="6bd7a-123">Soubory cookie ověřování jsou povoleny, pokud návštěvník webu nesouhlasí s shromažďováním dat.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="6bd7a-124">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="6bd7a-125">V `Startup.Configure` , zavolejte `UseAuthentication` a a `UseAuthorization` nastavte `HttpContext.User` vlastnost a spusťte middleware autorizace pro požadavky.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="6bd7a-126">`UseAuthentication` `UseAuthorization` Před voláním volejte metody a `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="6bd7a-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="6bd7a-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Třída se používá ke konfiguraci možností poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="6bd7a-128">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="6bd7a-129">Middleware zásad souborů cookie</span><span class="sxs-lookup"><span data-stu-id="6bd7a-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="6bd7a-130">[Middleware zásad souborů cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje využít zásady souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="6bd7a-131">Přidání middlewaru do kanálu zpracování aplikace je v pořádku &mdash; . týká se pouze podřízených komponent registrovaných v kanálu.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="6bd7a-132">Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytovaného middlewaru zásad souborů cookie k řízení globálních charakteristik zpracování souborů cookie a připojení do obslužných rutin zpracování souborů cookie, když jsou soubory cookie připojeny nebo smazány.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="6bd7a-133">Výchozí <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnota je `SameSiteMode.Lax` Povolit ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="6bd7a-134">Chcete-li striktně vyhovět zásadě stejného serveru `SameSiteMode.Strict` , nastavte `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="6bd7a-135">I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souborů cookie pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="6bd7a-136">Nastavení middlewaru zásad souborů cookie pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení v závislosti na následující matici.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="6bd7a-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="6bd7a-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="6bd7a-138">Soubor cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="6bd7a-138">Cookie.SameSite</span></span> | <span data-ttu-id="6bd7a-139">Nastavení výsledného souboru cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="6bd7a-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="6bd7a-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="6bd7a-140">SameSiteMode.None</span></span>     | <span data-ttu-id="6bd7a-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="6bd7a-141">SameSiteMode.None</span></span><br><span data-ttu-id="6bd7a-142">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="6bd7a-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="6bd7a-144">SameSiteMode.None</span></span><br><span data-ttu-id="6bd7a-145">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="6bd7a-147">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="6bd7a-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="6bd7a-148">SameSiteMode.None</span></span><br><span data-ttu-id="6bd7a-149">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="6bd7a-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-152">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="6bd7a-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="6bd7a-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="6bd7a-155">SameSiteMode.None</span></span><br><span data-ttu-id="6bd7a-156">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="6bd7a-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="6bd7a-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="6bd7a-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="6bd7a-161">Vytvoření ověřovacího souboru cookie</span><span class="sxs-lookup"><span data-stu-id="6bd7a-161">Create an authentication cookie</span></span>

<span data-ttu-id="6bd7a-162">Chcete-li vytvořit soubor cookie obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="6bd7a-163">Informace o uživateli jsou serializovány a uloženy v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="6bd7a-164">Vytvořit <xref:System.Security.Claims.ClaimsIdentity> s libovolným vyžadovaným <xref:System.Security.Claims.Claim> s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="6bd7a-165">`SignInAsync`Vytvoří zašifrovaný soubor cookie a přidá ho k aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="6bd7a-166">Pokud `AuthenticationScheme` není zadaný, použije se výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="6bd7a-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>se ve výchozím nastavení používá jenom pro několik specifických cest, například přihlašovací cesta a cesty odhlášení.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="6bd7a-168">Další informace najdete v tématu [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="6bd7a-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="6bd7a-169">Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="6bd7a-170">Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="6bd7a-171">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="6bd7a-171">Sign out</span></span>

<span data-ttu-id="6bd7a-172">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="6bd7a-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="6bd7a-173">Pokud se `CookieAuthenticationDefaults.AuthenticationScheme` (nebo "soubory cookie") nepoužívají jako schéma (například "ContosoCookie"), zadejte schéma používané při konfiguraci poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="6bd7a-174">V opačném případě se použije výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="6bd7a-175">Reakce na back-endové změny</span><span class="sxs-lookup"><span data-stu-id="6bd7a-175">React to back-end changes</span></span>

<span data-ttu-id="6bd7a-176">Po vytvoření souboru cookie je soubor cookie jediným zdrojem identity.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="6bd7a-177">Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="6bd7a-178">Systém ověřování souborů cookie aplikace nadále zpracovává požadavky založené na souboru cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="6bd7a-179">Uživatel zůstane přihlášený k aplikaci, pokud je ověřovací soubor cookie platný.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="6bd7a-180"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Událost se dá použít k zachycení a přepsání ověřování identity souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="6bd7a-181">Ověření souboru cookie u všech požadavků snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="6bd7a-182">Jeden přístup k ověření souboru cookie je založen na sledování toho, kdy se databáze uživatele změní.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="6bd7a-183">Pokud se databáze od vydání souboru cookie uživatele nezměnila, není nutné znovu ověřit uživatele, pokud je jejich soubor cookie stále platný.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="6bd7a-184">V ukázkové aplikaci je databáze implementována v `IUserRepository` a ukládá `LastChanged` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="6bd7a-185">Při aktualizaci uživatele v databázi `LastChanged` je hodnota nastavena na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="6bd7a-186">Aby se soubor cookie neověřoval, když se databáze na základě této `LastChanged` hodnoty změní, vytvořte soubor cookie s `LastChanged` deklarací identity obsahující aktuální `LastChanged` hodnotu z databáze:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="6bd7a-187">Chcete-li implementovat přepsání pro `ValidatePrincipal` událost, napište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="6bd7a-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="6bd7a-188">Následuje příklad implementace `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="6bd7a-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="6bd7a-189">Zaregistrujte instanci události během registrace služby souborů cookie v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6bd7a-190">Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro `CustomCookieAuthenticationEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="6bd7a-191">Vezměte v úvahu situaci, kdy je jméno uživatele Aktualizováno &mdash; rozhodnutím, které nijak neovlivňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="6bd7a-192">Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte `context.ShouldRenew` vlastnost na `true` .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="6bd7a-193">Přístup, který je zde popsán, se spustí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="6bd7a-194">Ověřování souborů cookie pro ověřování pro všechny uživatele na všech žádostech může mít za následek velkou sankci pro výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="6bd7a-195">Trvalé soubory cookie</span><span class="sxs-lookup"><span data-stu-id="6bd7a-195">Persistent cookies</span></span>

<span data-ttu-id="6bd7a-196">Můžete chtít, aby soubor cookie trval mezi relacemi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="6bd7a-197">Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="6bd7a-198">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který se zachová v rámci zavření prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="6bd7a-199">Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="6bd7a-200">Pokud soubor cookie vyprší v době, kdy je prohlížeč zavřen, prohlížeč po restartování odstraní soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="6bd7a-201">Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="6bd7a-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="6bd7a-202">Vypršení platnosti absolutního souboru cookie</span><span class="sxs-lookup"><span data-stu-id="6bd7a-202">Absolute cookie expiration</span></span>

<span data-ttu-id="6bd7a-203">Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="6bd7a-204">Chcete-li vytvořit trvalý soubor cookie, `IsPersistent` je nutné také nastavit.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="6bd7a-205">V opačném případě se soubor cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="6bd7a-206">Když `ExpiresUtc` je nastaveno, přepíše hodnotu <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , pokud je nastavena.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="6bd7a-207">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který trvá 20 minut.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="6bd7a-208">Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-208">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="6bd7a-209">ASP.NET Core Identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="6bd7a-210">Můžete ale použít poskytovatele ověřování ověřování na základě souborů cookie bez ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="6bd7a-211">Další informace naleznete v tématu <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="6bd7a-212">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6bd7a-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6bd7a-213">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="6bd7a-214">K přihlášení uživatele použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="6bd7a-215">Uživatel je ověřený v `AuthenticateUser` metodě v souboru *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="6bd7a-216">V reálném příkladu by byl uživatel ověřený proti databázi.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="6bd7a-217">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="6bd7a-217">Configuration</span></span>

<span data-ttu-id="6bd7a-218">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro balíček [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="6bd7a-219">V `Startup.ConfigureServices` metodě vytvořte službu ověřování middlewaru pomocí <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod a:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="6bd7a-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>předáno pro `AddAuthentication` Nastavení výchozího schématu ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="6bd7a-221">`AuthenticationScheme`je užitečné v případě, že existuje více instancí ověřování souborem cookie a chcete [autorizovat s konkrétním schématem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="6bd7a-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="6bd7a-222">Nastavení `AuthenticationScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje pro schéma hodnotu "cookies".</span><span class="sxs-lookup"><span data-stu-id="6bd7a-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="6bd7a-223">Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="6bd7a-224">Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikace.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="6bd7a-225">Pokud není k dispozici schéma ověřování souborů cookie <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , použije se `CookieAuthenticationDefaults.AuthenticationScheme` ("soubory cookie").</span><span class="sxs-lookup"><span data-stu-id="6bd7a-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="6bd7a-226">Vlastnost ověřovacího souboru cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je standardně nastavená na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="6bd7a-227">Soubory cookie ověřování jsou povoleny, pokud návštěvník webu nesouhlasí s shromažďováním dat.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="6bd7a-228">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="6bd7a-229">V `Startup.Configure` metodě zavolejte `UseAuthentication` metodu pro vyvolání middleware ověřování, který nastaví `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="6bd7a-230">Zavolejte `UseAuthentication` metodu před voláním `UseMvcWithDefaultRoute` nebo `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="6bd7a-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="6bd7a-231"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Třída se používá ke konfiguraci možností poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="6bd7a-232">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="6bd7a-233">Middleware zásad souborů cookie</span><span class="sxs-lookup"><span data-stu-id="6bd7a-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="6bd7a-234">[Middleware zásad souborů cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje využít zásady souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="6bd7a-235">Přidání middlewaru do kanálu zpracování aplikace je v pořádku &mdash; . týká se pouze podřízených komponent registrovaných v kanálu.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="6bd7a-236">Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytovaného middlewaru zásad souborů cookie k řízení globálních charakteristik zpracování souborů cookie a připojení do obslužných rutin zpracování souborů cookie, když jsou soubory cookie připojeny nebo smazány.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="6bd7a-237">Výchozí <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnota je `SameSiteMode.Lax` Povolit ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="6bd7a-238">Chcete-li striktně vyhovět zásadě stejného serveru `SameSiteMode.Strict` , nastavte `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="6bd7a-239">I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souborů cookie pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="6bd7a-240">Nastavení middlewaru zásad souborů cookie pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení v závislosti na následující matici.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="6bd7a-241">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="6bd7a-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="6bd7a-242">Soubor cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="6bd7a-242">Cookie.SameSite</span></span> | <span data-ttu-id="6bd7a-243">Nastavení výsledného souboru cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="6bd7a-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="6bd7a-244">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="6bd7a-244">SameSiteMode.None</span></span>     | <span data-ttu-id="6bd7a-245">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="6bd7a-245">SameSiteMode.None</span></span><br><span data-ttu-id="6bd7a-246">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-247">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="6bd7a-248">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="6bd7a-248">SameSiteMode.None</span></span><br><span data-ttu-id="6bd7a-249">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-250">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="6bd7a-251">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="6bd7a-252">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="6bd7a-252">SameSiteMode.None</span></span><br><span data-ttu-id="6bd7a-253">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-254">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="6bd7a-255">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-256">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-257">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="6bd7a-258">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="6bd7a-259">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="6bd7a-259">SameSiteMode.None</span></span><br><span data-ttu-id="6bd7a-260">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="6bd7a-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="6bd7a-261">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="6bd7a-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="6bd7a-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="6bd7a-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="6bd7a-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="6bd7a-265">Vytvoření ověřovacího souboru cookie</span><span class="sxs-lookup"><span data-stu-id="6bd7a-265">Create an authentication cookie</span></span>

<span data-ttu-id="6bd7a-266">Chcete-li vytvořit soubor cookie obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="6bd7a-267">Informace o uživateli jsou serializovány a uloženy v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="6bd7a-268">Vytvořit <xref:System.Security.Claims.ClaimsIdentity> s libovolným vyžadovaným <xref:System.Security.Claims.Claim> s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="6bd7a-269">`SignInAsync`Vytvoří zašifrovaný soubor cookie a přidá ho k aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="6bd7a-270">Pokud `AuthenticationScheme` není zadaný, použije se výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="6bd7a-271">Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="6bd7a-272">Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="6bd7a-273">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="6bd7a-273">Sign out</span></span>

<span data-ttu-id="6bd7a-274">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="6bd7a-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="6bd7a-275">Pokud se `CookieAuthenticationDefaults.AuthenticationScheme` (nebo "soubory cookie") nepoužívají jako schéma (například "ContosoCookie"), zadejte schéma používané při konfiguraci poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="6bd7a-276">V opačném případě se použije výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="6bd7a-277">Reakce na back-endové změny</span><span class="sxs-lookup"><span data-stu-id="6bd7a-277">React to back-end changes</span></span>

<span data-ttu-id="6bd7a-278">Po vytvoření souboru cookie je soubor cookie jediným zdrojem identity.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="6bd7a-279">Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="6bd7a-280">Systém ověřování souborů cookie aplikace nadále zpracovává požadavky založené na souboru cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="6bd7a-281">Uživatel zůstane přihlášený k aplikaci, pokud je ověřovací soubor cookie platný.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="6bd7a-282"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Událost se dá použít k zachycení a přepsání ověřování identity souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="6bd7a-283">Ověření souboru cookie u všech požadavků snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="6bd7a-284">Jeden přístup k ověření souboru cookie je založen na sledování toho, kdy se databáze uživatele změní.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="6bd7a-285">Pokud se databáze od vydání souboru cookie uživatele nezměnila, není nutné znovu ověřit uživatele, pokud je jejich soubor cookie stále platný.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="6bd7a-286">V ukázkové aplikaci je databáze implementována v `IUserRepository` a ukládá `LastChanged` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="6bd7a-287">Při aktualizaci uživatele v databázi `LastChanged` je hodnota nastavena na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="6bd7a-288">Aby se soubor cookie neověřoval, když se databáze na základě této `LastChanged` hodnoty změní, vytvořte soubor cookie s `LastChanged` deklarací identity obsahující aktuální `LastChanged` hodnotu z databáze:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="6bd7a-289">Chcete-li implementovat přepsání pro `ValidatePrincipal` událost, napište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="6bd7a-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="6bd7a-290">Následuje příklad implementace `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="6bd7a-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="6bd7a-291">Zaregistrujte instanci události během registrace služby souborů cookie v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6bd7a-292">Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro `CustomCookieAuthenticationEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="6bd7a-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="6bd7a-293">Vezměte v úvahu situaci, kdy je jméno uživatele Aktualizováno &mdash; rozhodnutím, které nijak neovlivňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="6bd7a-294">Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte `context.ShouldRenew` vlastnost na `true` .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="6bd7a-295">Přístup, který je zde popsán, se spustí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="6bd7a-296">Ověřování souborů cookie pro ověřování pro všechny uživatele na všech žádostech může mít za následek velkou sankci pro výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="6bd7a-297">Trvalé soubory cookie</span><span class="sxs-lookup"><span data-stu-id="6bd7a-297">Persistent cookies</span></span>

<span data-ttu-id="6bd7a-298">Můžete chtít, aby soubor cookie trval mezi relacemi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="6bd7a-299">Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="6bd7a-300">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který se zachová v rámci zavření prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="6bd7a-301">Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="6bd7a-302">Pokud soubor cookie vyprší v době, kdy je prohlížeč zavřen, prohlížeč po restartování odstraní soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="6bd7a-303">Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="6bd7a-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="6bd7a-304">Vypršení platnosti absolutního souboru cookie</span><span class="sxs-lookup"><span data-stu-id="6bd7a-304">Absolute cookie expiration</span></span>

<span data-ttu-id="6bd7a-305">Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="6bd7a-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="6bd7a-306">Chcete-li vytvořit trvalý soubor cookie, `IsPersistent` je nutné také nastavit.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="6bd7a-307">V opačném případě se soubor cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="6bd7a-308">Když `ExpiresUtc` je nastaveno, přepíše hodnotu <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , pokud je nastavena.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="6bd7a-309">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který trvá 20 minut.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="6bd7a-310">Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="6bd7a-310">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="6bd7a-311">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6bd7a-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="6bd7a-312">Kontroly rolí na základě zásad</span><span class="sxs-lookup"><span data-stu-id="6bd7a-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

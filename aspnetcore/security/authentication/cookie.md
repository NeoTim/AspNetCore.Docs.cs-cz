---
title: Použít ověřování souborem cookie bez ASP.NET CoreIdentity
author: rick-anderson
description: Naučte se používat ověřování souborem cookie bez IdentityASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: c179b3657ad4cbda960c2afe685a63f3266a7402
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773841"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="4c3da-103">Použití ověřování souborem cookie bez ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="4c3da-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="4c3da-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4c3da-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4c3da-105">ASP.NET Core identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="4c3da-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="4c3da-106">Můžete ale použít poskytovatele ověřování na základě souborů cookie bez ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="4c3da-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="4c3da-107">Další informace naleznete v tématu <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="4c3da-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="4c3da-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4c3da-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4c3da-109">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c3da-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="4c3da-110">K přihlášení uživatele použijte `maria.rodriguez@contoso.com` **e-mailovou** adresu a jakékoli heslo.</span><span class="sxs-lookup"><span data-stu-id="4c3da-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="4c3da-111">Uživatel je ověřený v `AuthenticateUser` metodě v souboru *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="4c3da-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="4c3da-112">V reálném příkladu by byl uživatel ověřený proti databázi.</span><span class="sxs-lookup"><span data-stu-id="4c3da-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="4c3da-113">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="4c3da-113">Configuration</span></span>

<span data-ttu-id="4c3da-114">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro balíček [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="4c3da-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="4c3da-115">V `Startup.ConfigureServices` metodě vytvořte služby ověřování middlewaru pomocí metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a: <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*></span><span class="sxs-lookup"><span data-stu-id="4c3da-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="4c3da-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>předáno `AddAuthentication` pro nastavení výchozího schématu ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c3da-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="4c3da-117">`AuthenticationScheme`je užitečné v případě, že existuje více instancí ověřování souborem cookie a chcete [autorizovat s konkrétním schématem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="4c3da-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="4c3da-118">Nastavení `AuthenticationScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje pro schéma hodnotu "cookies".</span><span class="sxs-lookup"><span data-stu-id="4c3da-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="4c3da-119">Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="4c3da-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="4c3da-120">Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c3da-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="4c3da-121">Pokud není k <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>dispozici schéma ověřování souborů cookie, použije `CookieAuthenticationDefaults.AuthenticationScheme` se ("soubory cookie").</span><span class="sxs-lookup"><span data-stu-id="4c3da-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="4c3da-122"><xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> Vlastnost ověřovacího souboru cookie je standardně `true` nastavená na hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4c3da-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="4c3da-123">Soubory cookie ověřování jsou povoleny, pokud návštěvník webu nesouhlasí s shromažďováním dat.</span><span class="sxs-lookup"><span data-stu-id="4c3da-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="4c3da-124">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="4c3da-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="4c3da-125">V `Startup.Configure`, zavolejte `UseAuthentication` a `UseAuthorization` a nastavte `HttpContext.User` vlastnost a spusťte middleware autorizace pro požadavky.</span><span class="sxs-lookup"><span data-stu-id="4c3da-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="4c3da-126">Před `UseAuthentication` voláním `UseAuthorization` `UseEndpoints`volejte metody a:</span><span class="sxs-lookup"><span data-stu-id="4c3da-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="4c3da-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Třída se používá ke konfiguraci možností poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="4c3da-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="4c3da-128">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="4c3da-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="4c3da-129">Middleware zásad souborů cookie</span><span class="sxs-lookup"><span data-stu-id="4c3da-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="4c3da-130">[Middleware zásad souborů cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje využít zásady souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="4c3da-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="4c3da-131">Přidání middlewaru do kanálu zpracování aplikace je v pořádku&mdash;. týká se pouze podřízených komponent registrovaných v kanálu.</span><span class="sxs-lookup"><span data-stu-id="4c3da-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="4c3da-132">Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytovaného middlewaru zásad souborů cookie k řízení globálních charakteristik zpracování souborů cookie a připojení do obslužných rutin zpracování souborů cookie, když jsou soubory cookie připojeny nebo smazány.</span><span class="sxs-lookup"><span data-stu-id="4c3da-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="4c3da-133">Výchozí <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnota je `SameSiteMode.Lax` povolit ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="4c3da-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="4c3da-134">Chcete-li striktně vyhovět zásadě stejného serveru `SameSiteMode.Strict`, nastavte `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="4c3da-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="4c3da-135">I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souborů cookie pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="4c3da-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="4c3da-136">Nastavení middlewaru zásad souborů cookie `MinimumSameSitePolicy` pro může ovlivnit nastavení `Cookie.SameSite` v nastavení `CookieAuthenticationOptions` v závislosti na následující matici.</span><span class="sxs-lookup"><span data-stu-id="4c3da-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="4c3da-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="4c3da-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="4c3da-138">Soubor cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="4c3da-138">Cookie.SameSite</span></span> | <span data-ttu-id="4c3da-139">Nastavení výsledného souboru cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="4c3da-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="4c3da-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4c3da-140">SameSiteMode.None</span></span>     | <span data-ttu-id="4c3da-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4c3da-141">SameSiteMode.None</span></span><br><span data-ttu-id="4c3da-142">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="4c3da-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4c3da-144">SameSiteMode.None</span></span><br><span data-ttu-id="4c3da-145">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4c3da-147">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="4c3da-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4c3da-148">SameSiteMode.None</span></span><br><span data-ttu-id="4c3da-149">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="4c3da-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-152">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4c3da-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="4c3da-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4c3da-155">SameSiteMode.None</span></span><br><span data-ttu-id="4c3da-156">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="4c3da-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="4c3da-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="4c3da-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="4c3da-161">Vytvoření ověřovacího souboru cookie</span><span class="sxs-lookup"><span data-stu-id="4c3da-161">Create an authentication cookie</span></span>

<span data-ttu-id="4c3da-162">Chcete-li vytvořit soubor cookie obsahující informace o uživateli <xref:System.Security.Claims.ClaimsPrincipal>, vytvořte.</span><span class="sxs-lookup"><span data-stu-id="4c3da-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="4c3da-163">Informace o uživateli jsou serializovány a uloženy v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="4c3da-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="4c3da-164">Vytvořit <xref:System.Security.Claims.ClaimsIdentity> s libovolným vyžadovaným <xref:System.Security.Claims.Claim>s a <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> voláním pro přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="4c3da-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="4c3da-165">`SignInAsync`Vytvoří zašifrovaný soubor cookie a přidá ho k aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4c3da-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="4c3da-166">Pokud `AuthenticationScheme` není zadaný, použije se výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="4c3da-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="4c3da-167">Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování.</span><span class="sxs-lookup"><span data-stu-id="4c3da-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="4c3da-168">Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.</span><span class="sxs-lookup"><span data-stu-id="4c3da-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="4c3da-169">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="4c3da-169">Sign out</span></span>

<span data-ttu-id="4c3da-170">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="4c3da-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="4c3da-171">Pokud `CookieAuthenticationDefaults.AuthenticationScheme` se (nebo "soubory cookie") nepoužívají jako schéma (například "ContosoCookie"), zadejte schéma používané při konfiguraci poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="4c3da-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="4c3da-172">V opačném případě se použije výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="4c3da-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="4c3da-173">Reakce na back-endové změny</span><span class="sxs-lookup"><span data-stu-id="4c3da-173">React to back-end changes</span></span>

<span data-ttu-id="4c3da-174">Po vytvoření souboru cookie je soubor cookie jediným zdrojem identity.</span><span class="sxs-lookup"><span data-stu-id="4c3da-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="4c3da-175">Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="4c3da-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="4c3da-176">Systém ověřování souborů cookie aplikace nadále zpracovává požadavky založené na souboru cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="4c3da-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="4c3da-177">Uživatel zůstane přihlášený k aplikaci, pokud je ověřovací soubor cookie platný.</span><span class="sxs-lookup"><span data-stu-id="4c3da-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="4c3da-178"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> Událost se dá použít k zachycení a přepsání ověřování identity souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="4c3da-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="4c3da-179">Ověření souboru cookie u všech požadavků snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c3da-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="4c3da-180">Jeden přístup k ověření souboru cookie je založen na sledování toho, kdy se databáze uživatele změní.</span><span class="sxs-lookup"><span data-stu-id="4c3da-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="4c3da-181">Pokud se databáze od vydání souboru cookie uživatele nezměnila, není nutné znovu ověřit uživatele, pokud je jejich soubor cookie stále platný.</span><span class="sxs-lookup"><span data-stu-id="4c3da-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="4c3da-182">V ukázkové aplikaci je databáze implementována v `IUserRepository` a ukládá `LastChanged` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4c3da-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="4c3da-183">Při aktualizaci uživatele v databázi je `LastChanged` hodnota nastavena na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="4c3da-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="4c3da-184">Aby se soubor cookie neověřoval, když se databáze na základě této `LastChanged` hodnoty změní, vytvořte soubor cookie s `LastChanged` deklarací identity obsahující aktuální `LastChanged` hodnotu z databáze:</span><span class="sxs-lookup"><span data-stu-id="4c3da-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="4c3da-185">Chcete-li implementovat přepsání pro `ValidatePrincipal` událost, napište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="4c3da-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="4c3da-186">Následuje příklad implementace `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="4c3da-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="4c3da-187">Zaregistrujte instanci události během registrace služby souborů cookie `Startup.ConfigureServices` v metodě.</span><span class="sxs-lookup"><span data-stu-id="4c3da-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4c3da-188">Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro `CustomCookieAuthenticationEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="4c3da-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="4c3da-189">Vezměte v úvahu situaci, kdy je jméno uživatele Aktualizováno&mdash;rozhodnutím, které nijak neovlivňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="4c3da-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="4c3da-190">Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte `context.ShouldRenew` vlastnost na. `true`</span><span class="sxs-lookup"><span data-stu-id="4c3da-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="4c3da-191">Přístup, který je zde popsán, se spustí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="4c3da-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="4c3da-192">Ověřování souborů cookie pro ověřování pro všechny uživatele na všech žádostech může mít za následek velkou sankci pro výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c3da-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="4c3da-193">Trvalé soubory cookie</span><span class="sxs-lookup"><span data-stu-id="4c3da-193">Persistent cookies</span></span>

<span data-ttu-id="4c3da-194">Můžete chtít, aby soubor cookie trval mezi relacemi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4c3da-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="4c3da-195">Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu.</span><span class="sxs-lookup"><span data-stu-id="4c3da-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="4c3da-196">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který se zachová v rámci zavření prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4c3da-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="4c3da-197">Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="4c3da-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="4c3da-198">Pokud soubor cookie vyprší v době, kdy je prohlížeč zavřen, prohlížeč po restartování odstraní soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="4c3da-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="4c3da-199">Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="4c3da-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="4c3da-200">Vypršení platnosti absolutního souboru cookie</span><span class="sxs-lookup"><span data-stu-id="4c3da-200">Absolute cookie expiration</span></span>

<span data-ttu-id="4c3da-201">Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span><span class="sxs-lookup"><span data-stu-id="4c3da-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="4c3da-202">Chcete-li vytvořit trvalý soubor `IsPersistent` cookie, je nutné také nastavit.</span><span class="sxs-lookup"><span data-stu-id="4c3da-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="4c3da-203">V opačném případě se soubor cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje.</span><span class="sxs-lookup"><span data-stu-id="4c3da-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="4c3da-204">Když `ExpiresUtc` je nastaveno, přepíše hodnotu <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, pokud je nastavena.</span><span class="sxs-lookup"><span data-stu-id="4c3da-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="4c3da-205">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který trvá 20 minut.</span><span class="sxs-lookup"><span data-stu-id="4c3da-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="4c3da-206">Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="4c3da-206">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="4c3da-207">ASP.NET Core identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="4c3da-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="4c3da-208">Můžete ale použít poskytovatele ověřování ověřování na základě souborů cookie bez ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="4c3da-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="4c3da-209">Další informace naleznete v tématu <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="4c3da-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="4c3da-210">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4c3da-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4c3da-211">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c3da-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="4c3da-212">K přihlášení uživatele použijte `maria.rodriguez@contoso.com` **e-mailovou** adresu a jakékoli heslo.</span><span class="sxs-lookup"><span data-stu-id="4c3da-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="4c3da-213">Uživatel je ověřený v `AuthenticateUser` metodě v souboru *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="4c3da-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="4c3da-214">V reálném příkladu by byl uživatel ověřený proti databázi.</span><span class="sxs-lookup"><span data-stu-id="4c3da-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="4c3da-215">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="4c3da-215">Configuration</span></span>

<span data-ttu-id="4c3da-216">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro balíček [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="4c3da-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="4c3da-217">V `Startup.ConfigureServices` metodě vytvořte službu ověřování middlewaru pomocí metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a: <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*></span><span class="sxs-lookup"><span data-stu-id="4c3da-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="4c3da-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>předáno `AddAuthentication` pro nastavení výchozího schématu ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c3da-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="4c3da-219">`AuthenticationScheme`je užitečné v případě, že existuje více instancí ověřování souborem cookie a chcete [autorizovat s konkrétním schématem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="4c3da-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="4c3da-220">Nastavení `AuthenticationScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje pro schéma hodnotu "cookies".</span><span class="sxs-lookup"><span data-stu-id="4c3da-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="4c3da-221">Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="4c3da-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="4c3da-222">Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c3da-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="4c3da-223">Pokud není k <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>dispozici schéma ověřování souborů cookie, použije `CookieAuthenticationDefaults.AuthenticationScheme` se ("soubory cookie").</span><span class="sxs-lookup"><span data-stu-id="4c3da-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="4c3da-224"><xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> Vlastnost ověřovacího souboru cookie je standardně `true` nastavená na hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4c3da-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="4c3da-225">Soubory cookie ověřování jsou povoleny, pokud návštěvník webu nesouhlasí s shromažďováním dat.</span><span class="sxs-lookup"><span data-stu-id="4c3da-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="4c3da-226">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="4c3da-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="4c3da-227">V `Startup.Configure` metodě zavolejte `UseAuthentication` metodu pro vyvolání middleware ověřování, který nastaví `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4c3da-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="4c3da-228">Zavolejte `UseAuthentication` metodu před voláním `UseMvcWithDefaultRoute` nebo `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="4c3da-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="4c3da-229"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Třída se používá ke konfiguraci možností poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="4c3da-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="4c3da-230">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="4c3da-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="4c3da-231">Middleware zásad souborů cookie</span><span class="sxs-lookup"><span data-stu-id="4c3da-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="4c3da-232">[Middleware zásad souborů cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje využít zásady souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="4c3da-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="4c3da-233">Přidání middlewaru do kanálu zpracování aplikace je v pořádku&mdash;. týká se pouze podřízených komponent registrovaných v kanálu.</span><span class="sxs-lookup"><span data-stu-id="4c3da-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="4c3da-234">Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytovaného middlewaru zásad souborů cookie k řízení globálních charakteristik zpracování souborů cookie a připojení do obslužných rutin zpracování souborů cookie, když jsou soubory cookie připojeny nebo smazány.</span><span class="sxs-lookup"><span data-stu-id="4c3da-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="4c3da-235">Výchozí <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnota je `SameSiteMode.Lax` povolit ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="4c3da-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="4c3da-236">Chcete-li striktně vyhovět zásadě stejného serveru `SameSiteMode.Strict`, nastavte `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="4c3da-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="4c3da-237">I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souborů cookie pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="4c3da-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="4c3da-238">Nastavení middlewaru zásad souborů cookie `MinimumSameSitePolicy` pro může ovlivnit nastavení `Cookie.SameSite` v nastavení `CookieAuthenticationOptions` v závislosti na následující matici.</span><span class="sxs-lookup"><span data-stu-id="4c3da-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="4c3da-239">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="4c3da-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="4c3da-240">Soubor cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="4c3da-240">Cookie.SameSite</span></span> | <span data-ttu-id="4c3da-241">Nastavení výsledného souboru cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="4c3da-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="4c3da-242">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4c3da-242">SameSiteMode.None</span></span>     | <span data-ttu-id="4c3da-243">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4c3da-243">SameSiteMode.None</span></span><br><span data-ttu-id="4c3da-244">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-245">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="4c3da-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4c3da-246">SameSiteMode.None</span></span><br><span data-ttu-id="4c3da-247">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-248">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4c3da-249">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="4c3da-250">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4c3da-250">SameSiteMode.None</span></span><br><span data-ttu-id="4c3da-251">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-252">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="4c3da-253">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-254">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-255">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="4c3da-256">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="4c3da-257">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="4c3da-257">SameSiteMode.None</span></span><br><span data-ttu-id="4c3da-258">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="4c3da-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="4c3da-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="4c3da-260">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="4c3da-261">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="4c3da-262">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="4c3da-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="4c3da-263">Vytvoření ověřovacího souboru cookie</span><span class="sxs-lookup"><span data-stu-id="4c3da-263">Create an authentication cookie</span></span>

<span data-ttu-id="4c3da-264">Chcete-li vytvořit soubor cookie obsahující informace o uživateli <xref:System.Security.Claims.ClaimsPrincipal>, vytvořte.</span><span class="sxs-lookup"><span data-stu-id="4c3da-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="4c3da-265">Informace o uživateli jsou serializovány a uloženy v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="4c3da-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="4c3da-266">Vytvořit <xref:System.Security.Claims.ClaimsIdentity> s libovolným vyžadovaným <xref:System.Security.Claims.Claim>s a <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> voláním pro přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="4c3da-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="4c3da-267">`SignInAsync`Vytvoří zašifrovaný soubor cookie a přidá ho k aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4c3da-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="4c3da-268">Pokud `AuthenticationScheme` není zadaný, použije se výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="4c3da-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="4c3da-269">Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování.</span><span class="sxs-lookup"><span data-stu-id="4c3da-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="4c3da-270">Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.</span><span class="sxs-lookup"><span data-stu-id="4c3da-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="4c3da-271">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="4c3da-271">Sign out</span></span>

<span data-ttu-id="4c3da-272">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>, zavolejte:</span><span class="sxs-lookup"><span data-stu-id="4c3da-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="4c3da-273">Pokud `CookieAuthenticationDefaults.AuthenticationScheme` se (nebo "soubory cookie") nepoužívají jako schéma (například "ContosoCookie"), zadejte schéma používané při konfiguraci poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="4c3da-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="4c3da-274">V opačném případě se použije výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="4c3da-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="4c3da-275">Reakce na back-endové změny</span><span class="sxs-lookup"><span data-stu-id="4c3da-275">React to back-end changes</span></span>

<span data-ttu-id="4c3da-276">Po vytvoření souboru cookie je soubor cookie jediným zdrojem identity.</span><span class="sxs-lookup"><span data-stu-id="4c3da-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="4c3da-277">Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="4c3da-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="4c3da-278">Systém ověřování souborů cookie aplikace nadále zpracovává požadavky založené na souboru cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="4c3da-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="4c3da-279">Uživatel zůstane přihlášený k aplikaci, pokud je ověřovací soubor cookie platný.</span><span class="sxs-lookup"><span data-stu-id="4c3da-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="4c3da-280"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> Událost se dá použít k zachycení a přepsání ověřování identity souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="4c3da-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="4c3da-281">Ověření souboru cookie u všech požadavků snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4c3da-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="4c3da-282">Jeden přístup k ověření souboru cookie je založen na sledování toho, kdy se databáze uživatele změní.</span><span class="sxs-lookup"><span data-stu-id="4c3da-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="4c3da-283">Pokud se databáze od vydání souboru cookie uživatele nezměnila, není nutné znovu ověřit uživatele, pokud je jejich soubor cookie stále platný.</span><span class="sxs-lookup"><span data-stu-id="4c3da-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="4c3da-284">V ukázkové aplikaci je databáze implementována v `IUserRepository` a ukládá `LastChanged` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4c3da-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="4c3da-285">Při aktualizaci uživatele v databázi je `LastChanged` hodnota nastavena na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="4c3da-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="4c3da-286">Aby se soubor cookie neověřoval, když se databáze na základě této `LastChanged` hodnoty změní, vytvořte soubor cookie s `LastChanged` deklarací identity obsahující aktuální `LastChanged` hodnotu z databáze:</span><span class="sxs-lookup"><span data-stu-id="4c3da-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="4c3da-287">Chcete-li implementovat přepsání pro `ValidatePrincipal` událost, napište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="4c3da-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="4c3da-288">Následuje příklad implementace `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="4c3da-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="4c3da-289">Zaregistrujte instanci události během registrace služby souborů cookie `Startup.ConfigureServices` v metodě.</span><span class="sxs-lookup"><span data-stu-id="4c3da-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4c3da-290">Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro `CustomCookieAuthenticationEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="4c3da-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="4c3da-291">Vezměte v úvahu situaci, kdy je jméno uživatele Aktualizováno&mdash;rozhodnutím, které nijak neovlivňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="4c3da-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="4c3da-292">Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte `context.ShouldRenew` vlastnost na. `true`</span><span class="sxs-lookup"><span data-stu-id="4c3da-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="4c3da-293">Přístup, který je zde popsán, se spustí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="4c3da-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="4c3da-294">Ověřování souborů cookie pro ověřování pro všechny uživatele na všech žádostech může mít za následek velkou sankci pro výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c3da-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="4c3da-295">Trvalé soubory cookie</span><span class="sxs-lookup"><span data-stu-id="4c3da-295">Persistent cookies</span></span>

<span data-ttu-id="4c3da-296">Můžete chtít, aby soubor cookie trval mezi relacemi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4c3da-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="4c3da-297">Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu.</span><span class="sxs-lookup"><span data-stu-id="4c3da-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="4c3da-298">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který se zachová v rámci zavření prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4c3da-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="4c3da-299">Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="4c3da-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="4c3da-300">Pokud soubor cookie vyprší v době, kdy je prohlížeč zavřen, prohlížeč po restartování odstraní soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="4c3da-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="4c3da-301">Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="4c3da-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="4c3da-302">Vypršení platnosti absolutního souboru cookie</span><span class="sxs-lookup"><span data-stu-id="4c3da-302">Absolute cookie expiration</span></span>

<span data-ttu-id="4c3da-303">Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span><span class="sxs-lookup"><span data-stu-id="4c3da-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="4c3da-304">Chcete-li vytvořit trvalý soubor `IsPersistent` cookie, je nutné také nastavit.</span><span class="sxs-lookup"><span data-stu-id="4c3da-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="4c3da-305">V opačném případě se soubor cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje.</span><span class="sxs-lookup"><span data-stu-id="4c3da-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="4c3da-306">Když `ExpiresUtc` je nastaveno, přepíše hodnotu <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, pokud je nastavena.</span><span class="sxs-lookup"><span data-stu-id="4c3da-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="4c3da-307">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který trvá 20 minut.</span><span class="sxs-lookup"><span data-stu-id="4c3da-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="4c3da-308">Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="4c3da-308">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="4c3da-309">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4c3da-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="4c3da-310">Kontroly rolí na základě zásad</span><span class="sxs-lookup"><span data-stu-id="4c3da-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

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
ms.openlocfilehash: 24ba49828db08fdd67723c81ac0c8d9981ab3404
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945412"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="22802-103">Použít cookie ověřování bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="22802-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="22802-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="22802-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="22802-105">ASP.NET Core Identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="22802-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="22802-106">Nicméně cookie poskytovatele ověřování založeného na typu se ASP.NET Core Identity dá použít.</span><span class="sxs-lookup"><span data-stu-id="22802-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="22802-107">Další informace naleznete v tématu <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="22802-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="22802-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="22802-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="22802-109">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="22802-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="22802-110">K přihlášení uživatele použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo.</span><span class="sxs-lookup"><span data-stu-id="22802-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="22802-111">Uživatel je ověřený v `AuthenticateUser` metodě v souboru *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="22802-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="22802-112">V reálném příkladu by byl uživatel ověřený proti databázi.</span><span class="sxs-lookup"><span data-stu-id="22802-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="22802-113">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="22802-113">Configuration</span></span>

<span data-ttu-id="22802-114">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro [Microsoft. AspNetCore. Authentication. Cookie balíček s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="22802-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="22802-115">V `Startup.ConfigureServices` metodě vytvořte služby ověřování middlewaru pomocí <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod a:</span><span class="sxs-lookup"><span data-stu-id="22802-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="22802-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> předáno pro `AddAuthentication` Nastavení výchozího schématu ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="22802-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="22802-117">`AuthenticationScheme` je užitečné v případě, že existuje více instancí cookie ověřování a vy chcete [autorizovat pomocí konkrétního schématu](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="22802-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="22802-118">Nastavení `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje Cookie pro schéma hodnotu "s".</span><span class="sxs-lookup"><span data-stu-id="22802-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="22802-119">Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="22802-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="22802-120">Schéma ověřování aplikace se liší od cookie schématu ověřování aplikace.</span><span class="sxs-lookup"><span data-stu-id="22802-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="22802-121">Pokud cookie není k dispozici schéma ověřování pro <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , používá `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="22802-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="22802-122">Vlastnost ověřování cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je standardně nastavená na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="22802-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="22802-123">Ověřování cookie s je povoleno, pokud návštěvník webu nesouhlasí s shromažďováním dat.</span><span class="sxs-lookup"><span data-stu-id="22802-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="22802-124">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="22802-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="22802-125">V `Startup.Configure` , zavolejte `UseAuthentication` a a `UseAuthorization` nastavte `HttpContext.User` vlastnost a spusťte middleware autorizace pro požadavky.</span><span class="sxs-lookup"><span data-stu-id="22802-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="22802-126">`UseAuthentication` `UseAuthorization` Před voláním volejte metody a `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="22802-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="22802-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Třída se používá ke konfiguraci možností poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="22802-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="22802-128">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="22802-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="22802-129">Cookie Middleware zásad</span><span class="sxs-lookup"><span data-stu-id="22802-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="22802-130">[ Cookie Middleware zásad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje cookie Možnosti zásad.</span><span class="sxs-lookup"><span data-stu-id="22802-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="22802-131">Přidání middlewaru do kanálu zpracování aplikace je v pořádku &mdash; . týká se pouze podřízených komponent registrovaných v kanálu.</span><span class="sxs-lookup"><span data-stu-id="22802-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="22802-132">Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytované pro Cookie middleware zásad k řízení globálních charakteristik cookie zpracování a cookie připojení k obslužným rutinám zpracování, pokud cookie jsou připojeny nebo odstraněny.</span><span class="sxs-lookup"><span data-stu-id="22802-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="22802-133">Výchozí <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnota je `SameSiteMode.Lax` Povolit ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="22802-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="22802-134">Chcete-li striktně vyhovět zásadě stejného serveru `SameSiteMode.Strict` , nastavte `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="22802-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="22802-135">I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň cookie zabezpečení pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="22802-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="22802-136">CookieNastavení middlewaru zásad pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle matice níže.</span><span class="sxs-lookup"><span data-stu-id="22802-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="22802-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="22802-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="22802-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="22802-138">Cookie.SameSite</span></span> | <span data-ttu-id="22802-139">Výsledek Cookie . Nastavení SameSite</span><span class="sxs-lookup"><span data-stu-id="22802-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="22802-140">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="22802-140">SameSiteMode.None</span></span>     | <span data-ttu-id="22802-141">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="22802-141">SameSiteMode.None</span></span><br><span data-ttu-id="22802-142">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-143">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="22802-144">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="22802-144">SameSiteMode.None</span></span><br><span data-ttu-id="22802-145">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-146">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="22802-147">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="22802-148">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="22802-148">SameSiteMode.None</span></span><br><span data-ttu-id="22802-149">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-150">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="22802-151">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-152">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-153">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="22802-154">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="22802-155">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="22802-155">SameSiteMode.None</span></span><br><span data-ttu-id="22802-156">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-157">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="22802-158">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="22802-159">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="22802-160">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="22802-161">Vytvoření ověřování cookie</span><span class="sxs-lookup"><span data-stu-id="22802-161">Create an authentication cookie</span></span>

<span data-ttu-id="22802-162">Chcete-li vytvořit cookie informace o uživatelích obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="22802-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="22802-163">Informace o uživateli jsou serializovány a uloženy v cookie .</span><span class="sxs-lookup"><span data-stu-id="22802-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="22802-164">Vytvořit <xref:System.Security.Claims.ClaimsIdentity> s libovolným vyžadovaným <xref:System.Security.Claims.Claim> s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="22802-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="22802-165">`SignInAsync` Vytvoří zašifrovaný objekt cookie a přidá ho k aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="22802-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="22802-166">Pokud `AuthenticationScheme` není zadaný, použije se výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="22802-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="22802-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> se ve výchozím nastavení používá jenom pro několik specifických cest, například přihlašovací cesta a cesty odhlášení.</span><span class="sxs-lookup"><span data-stu-id="22802-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="22802-168">Další informace najdete v tématu [ Cookie AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="22802-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="22802-169">Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování.</span><span class="sxs-lookup"><span data-stu-id="22802-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="22802-170">Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.</span><span class="sxs-lookup"><span data-stu-id="22802-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="22802-171">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="22802-171">Sign out</span></span>

<span data-ttu-id="22802-172">Chcete-li odhlásit aktuálního uživatele a odstranit jeho cookie volání, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="22802-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="22802-173">Pokud `CookieAuthenticationDefaults.AuthenticationScheme` (nebo " Cookie s") se nepoužívá jako schéma (například "contoso Cookie "), zadejte schéma používané při konfiguraci zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="22802-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="22802-174">V opačném případě se použije výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="22802-174">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="22802-175">Když se prohlížeč zavře, automaticky odstraní relaci na základě cookie (netrvalé cookie s), ale cookie při zavření jednotlivé karty se nevymaže žádné.</span><span class="sxs-lookup"><span data-stu-id="22802-175">When the browser closes it automatically deletes session based cookies (non-persistent cookies), but no cookies are cleared when an individual tab is closed.</span></span> <span data-ttu-id="22802-176">Server není upozorněn na události zavření karty nebo prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="22802-176">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="22802-177">Reakce na back-endové změny</span><span class="sxs-lookup"><span data-stu-id="22802-177">React to back-end changes</span></span>

<span data-ttu-id="22802-178">Po cookie vytvoření je cookie jediným zdrojem identity.</span><span class="sxs-lookup"><span data-stu-id="22802-178">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="22802-179">Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="22802-179">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="22802-180">cookieSystém ověřování aplikace nadále zpracovává požadavky na základě ověření cookie .</span><span class="sxs-lookup"><span data-stu-id="22802-180">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="22802-181">Uživatel zůstane přihlášený k aplikaci, pokud cookie je ověřování platné.</span><span class="sxs-lookup"><span data-stu-id="22802-181">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="22802-182"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Událost se dá použít k zachycení a přepsání ověřování cookie identity.</span><span class="sxs-lookup"><span data-stu-id="22802-182">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="22802-183">Ověřování cookie u každé žádosti snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="22802-183">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="22802-184">Jeden z přístupů k cookie ověření je založen na sledování, kdy se uživatel změní.</span><span class="sxs-lookup"><span data-stu-id="22802-184">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="22802-185">Pokud se databáze od vydání uživatele nezměnila cookie , není nutné znovu ověřit uživatele, pokud cookie je jejich platnost stále platná.</span><span class="sxs-lookup"><span data-stu-id="22802-185">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="22802-186">V ukázkové aplikaci je databáze implementována v `IUserRepository` a ukládá `LastChanged` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="22802-186">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="22802-187">Při aktualizaci uživatele v databázi `LastChanged` je hodnota nastavena na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="22802-187">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="22802-188">Chcete-li zrušit platnost cookie při změně databáze na základě `LastChanged` hodnoty, vytvořte objekt cookie s `LastChanged` deklarací identity obsahující aktuální `LastChanged` hodnotu z databáze:</span><span class="sxs-lookup"><span data-stu-id="22802-188">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="22802-189">Chcete-li implementovat přepsání pro `ValidatePrincipal` událost, napište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="22802-189">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="22802-190">Následuje příklad implementace `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="22802-190">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="22802-191">Zaregistrujte instanci události během cookie Registrace služby v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="22802-191">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="22802-192">Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro `CustomCookieAuthenticationEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="22802-192">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="22802-193">Vezměte v úvahu situaci, kdy je jméno uživatele Aktualizováno &mdash; rozhodnutím, které nijak neovlivňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="22802-193">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="22802-194">Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte `context.ShouldRenew` vlastnost na `true` .</span><span class="sxs-lookup"><span data-stu-id="22802-194">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="22802-195">Přístup, který je zde popsán, se spustí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="22802-195">The approach described here is triggered on every request.</span></span> <span data-ttu-id="22802-196">Ověřování ověřování cookie u všech uživatelů na všech žádostech může mít za následek velkou penalizaci výkonu aplikace.</span><span class="sxs-lookup"><span data-stu-id="22802-196">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="22802-197">Trvalé cookie s</span><span class="sxs-lookup"><span data-stu-id="22802-197">Persistent cookies</span></span>

<span data-ttu-id="22802-198">Možná budete chtít, cookie aby trvala v rámci relací prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="22802-198">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="22802-199">Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu.</span><span class="sxs-lookup"><span data-stu-id="22802-199">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="22802-200">Následující fragment kódu vytvoří identitu a odpovídající cookie , která je zachována v případě uzavření prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="22802-200">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="22802-201">Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="22802-201">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="22802-202">Pokud cookie vyprší platnost při zavření prohlížeče, prohlížeč cookie po jeho restartování zruší.</span><span class="sxs-lookup"><span data-stu-id="22802-202">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="22802-203">Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="22802-203">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="22802-204">Absolutní cookie vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="22802-204">Absolute cookie expiration</span></span>

<span data-ttu-id="22802-205">Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="22802-205">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="22802-206">Aby bylo možné vytvořit trvalé cookie , `IsPersistent` musí být také nastaveno.</span><span class="sxs-lookup"><span data-stu-id="22802-206">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="22802-207">V opačném případě se cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje.</span><span class="sxs-lookup"><span data-stu-id="22802-207">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="22802-208">Když `ExpiresUtc` je nastaveno, přepíše hodnotu <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , pokud je nastavena.</span><span class="sxs-lookup"><span data-stu-id="22802-208">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="22802-209">Následující fragment kódu vytvoří identitu a odpovídajícím způsobem cookie , který trvá 20 minut.</span><span class="sxs-lookup"><span data-stu-id="22802-209">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="22802-210">Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="22802-210">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="22802-211">ASP.NET Core Identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="22802-211">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="22802-212">Nicméně cookie poskytovatele ověřování založeného na typu se ASP.NET Core Identity dá použít.</span><span class="sxs-lookup"><span data-stu-id="22802-212">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="22802-213">Další informace naleznete v tématu <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="22802-213">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="22802-214">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="22802-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="22802-215">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="22802-215">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="22802-216">K přihlášení uživatele použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo.</span><span class="sxs-lookup"><span data-stu-id="22802-216">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="22802-217">Uživatel je ověřený v `AuthenticateUser` metodě v souboru *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="22802-217">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="22802-218">V reálném příkladu by byl uživatel ověřený proti databázi.</span><span class="sxs-lookup"><span data-stu-id="22802-218">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="22802-219">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="22802-219">Configuration</span></span>

<span data-ttu-id="22802-220">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro [Microsoft. AspNetCore. Authentication. Cookie balíček s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="22802-220">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="22802-221">V `Startup.ConfigureServices` metodě vytvořte službu ověřování middlewaru pomocí <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod a:</span><span class="sxs-lookup"><span data-stu-id="22802-221">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="22802-222"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> předáno pro `AddAuthentication` Nastavení výchozího schématu ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="22802-222"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="22802-223">`AuthenticationScheme` je užitečné v případě, že existuje více instancí cookie ověřování a vy chcete [autorizovat pomocí konkrétního schématu](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="22802-223">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="22802-224">Nastavení `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje Cookie pro schéma hodnotu "s".</span><span class="sxs-lookup"><span data-stu-id="22802-224">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="22802-225">Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="22802-225">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="22802-226">Schéma ověřování aplikace se liší od cookie schématu ověřování aplikace.</span><span class="sxs-lookup"><span data-stu-id="22802-226">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="22802-227">Pokud cookie není k dispozici schéma ověřování pro <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , používá `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="22802-227">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="22802-228">Vlastnost ověřování cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je standardně nastavená na hodnotu `true` .</span><span class="sxs-lookup"><span data-stu-id="22802-228">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="22802-229">Ověřování cookie s je povoleno, pokud návštěvník webu nesouhlasí s shromažďováním dat.</span><span class="sxs-lookup"><span data-stu-id="22802-229">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="22802-230">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="22802-230">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="22802-231">V `Startup.Configure` metodě zavolejte `UseAuthentication` metodu pro vyvolání middleware ověřování, který nastaví `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="22802-231">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="22802-232">Zavolejte `UseAuthentication` metodu před voláním `UseMvcWithDefaultRoute` nebo `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="22802-232">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="22802-233"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Třída se používá ke konfiguraci možností poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="22802-233">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="22802-234">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="22802-234">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="22802-235">Cookie Middleware zásad</span><span class="sxs-lookup"><span data-stu-id="22802-235">Cookie Policy Middleware</span></span>

<span data-ttu-id="22802-236">[ Cookie Middleware zásad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje cookie Možnosti zásad.</span><span class="sxs-lookup"><span data-stu-id="22802-236">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="22802-237">Přidání middlewaru do kanálu zpracování aplikace je v pořádku &mdash; . týká se pouze podřízených komponent registrovaných v kanálu.</span><span class="sxs-lookup"><span data-stu-id="22802-237">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="22802-238">Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytované pro Cookie middleware zásad k řízení globálních charakteristik cookie zpracování a cookie připojení k obslužným rutinám zpracování, pokud cookie jsou připojeny nebo odstraněny.</span><span class="sxs-lookup"><span data-stu-id="22802-238">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="22802-239">Výchozí <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnota je `SameSiteMode.Lax` Povolit ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="22802-239">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="22802-240">Chcete-li striktně vyhovět zásadě stejného serveru `SameSiteMode.Strict` , nastavte `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="22802-240">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="22802-241">I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň cookie zabezpečení pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="22802-241">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="22802-242">CookieNastavení middlewaru zásad pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle matice níže.</span><span class="sxs-lookup"><span data-stu-id="22802-242">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="22802-243">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="22802-243">MinimumSameSitePolicy</span></span> | <span data-ttu-id="22802-244">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="22802-244">Cookie.SameSite</span></span> | <span data-ttu-id="22802-245">Výsledek Cookie . Nastavení SameSite</span><span class="sxs-lookup"><span data-stu-id="22802-245">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="22802-246">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="22802-246">SameSiteMode.None</span></span>     | <span data-ttu-id="22802-247">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="22802-247">SameSiteMode.None</span></span><br><span data-ttu-id="22802-248">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-248">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-249">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-249">SameSiteMode.Strict</span></span> | <span data-ttu-id="22802-250">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="22802-250">SameSiteMode.None</span></span><br><span data-ttu-id="22802-251">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-252">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-252">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="22802-253">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-253">SameSiteMode.Lax</span></span>      | <span data-ttu-id="22802-254">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="22802-254">SameSiteMode.None</span></span><br><span data-ttu-id="22802-255">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-256">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-256">SameSiteMode.Strict</span></span> | <span data-ttu-id="22802-257">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-258">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-259">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-259">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="22802-260">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-260">SameSiteMode.Strict</span></span>   | <span data-ttu-id="22802-261">SameSiteMode. None</span><span class="sxs-lookup"><span data-stu-id="22802-261">SameSiteMode.None</span></span><br><span data-ttu-id="22802-262">SameSiteMode. LAX</span><span class="sxs-lookup"><span data-stu-id="22802-262">SameSiteMode.Lax</span></span><br><span data-ttu-id="22802-263">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-263">SameSiteMode.Strict</span></span> | <span data-ttu-id="22802-264">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="22802-265">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-265">SameSiteMode.Strict</span></span><br><span data-ttu-id="22802-266">SameSiteMode. Strict</span><span class="sxs-lookup"><span data-stu-id="22802-266">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="22802-267">Vytvoření ověřování cookie</span><span class="sxs-lookup"><span data-stu-id="22802-267">Create an authentication cookie</span></span>

<span data-ttu-id="22802-268">Chcete-li vytvořit cookie informace o uživatelích obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="22802-268">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="22802-269">Informace o uživateli jsou serializovány a uloženy v cookie .</span><span class="sxs-lookup"><span data-stu-id="22802-269">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="22802-270">Vytvořit <xref:System.Security.Claims.ClaimsIdentity> s libovolným vyžadovaným <xref:System.Security.Claims.Claim> s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="22802-270">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="22802-271">`SignInAsync` Vytvoří zašifrovaný objekt cookie a přidá ho k aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="22802-271">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="22802-272">Pokud `AuthenticationScheme` není zadaný, použije se výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="22802-272">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="22802-273">Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování.</span><span class="sxs-lookup"><span data-stu-id="22802-273">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="22802-274">Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.</span><span class="sxs-lookup"><span data-stu-id="22802-274">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="22802-275">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="22802-275">Sign out</span></span>

<span data-ttu-id="22802-276">Chcete-li odhlásit aktuálního uživatele a odstranit jeho cookie volání, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="22802-276">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="22802-277">Pokud `CookieAuthenticationDefaults.AuthenticationScheme` (nebo " Cookie s") se nepoužívá jako schéma (například "contoso Cookie "), zadejte schéma používané při konfiguraci zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="22802-277">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="22802-278">V opačném případě se použije výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="22802-278">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="22802-279">Reakce na back-endové změny</span><span class="sxs-lookup"><span data-stu-id="22802-279">React to back-end changes</span></span>

<span data-ttu-id="22802-280">Po cookie vytvoření je cookie jediným zdrojem identity.</span><span class="sxs-lookup"><span data-stu-id="22802-280">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="22802-281">Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="22802-281">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="22802-282">cookieSystém ověřování aplikace nadále zpracovává požadavky na základě ověření cookie .</span><span class="sxs-lookup"><span data-stu-id="22802-282">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="22802-283">Uživatel zůstane přihlášený k aplikaci, pokud cookie je ověřování platné.</span><span class="sxs-lookup"><span data-stu-id="22802-283">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="22802-284"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Událost se dá použít k zachycení a přepsání ověřování cookie identity.</span><span class="sxs-lookup"><span data-stu-id="22802-284">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="22802-285">Ověřování cookie u každé žádosti snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="22802-285">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="22802-286">Jeden z přístupů k cookie ověření je založen na sledování, kdy se uživatel změní.</span><span class="sxs-lookup"><span data-stu-id="22802-286">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="22802-287">Pokud se databáze od vydání uživatele nezměnila cookie , není nutné znovu ověřit uživatele, pokud cookie je jejich platnost stále platná.</span><span class="sxs-lookup"><span data-stu-id="22802-287">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="22802-288">V ukázkové aplikaci je databáze implementována v `IUserRepository` a ukládá `LastChanged` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="22802-288">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="22802-289">Při aktualizaci uživatele v databázi `LastChanged` je hodnota nastavena na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="22802-289">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="22802-290">Chcete-li zrušit platnost cookie při změně databáze na základě `LastChanged` hodnoty, vytvořte objekt cookie s `LastChanged` deklarací identity obsahující aktuální `LastChanged` hodnotu z databáze:</span><span class="sxs-lookup"><span data-stu-id="22802-290">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="22802-291">Chcete-li implementovat přepsání pro `ValidatePrincipal` událost, napište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="22802-291">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="22802-292">Následuje příklad implementace `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="22802-292">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="22802-293">Zaregistrujte instanci události během cookie Registrace služby v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="22802-293">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="22802-294">Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro `CustomCookieAuthenticationEvents` třídu:</span><span class="sxs-lookup"><span data-stu-id="22802-294">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="22802-295">Vezměte v úvahu situaci, kdy je jméno uživatele Aktualizováno &mdash; rozhodnutím, které nijak neovlivňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="22802-295">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="22802-296">Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte `context.ShouldRenew` vlastnost na `true` .</span><span class="sxs-lookup"><span data-stu-id="22802-296">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="22802-297">Přístup, který je zde popsán, se spustí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="22802-297">The approach described here is triggered on every request.</span></span> <span data-ttu-id="22802-298">Ověřování ověřování cookie u všech uživatelů na všech žádostech může mít za následek velkou penalizaci výkonu aplikace.</span><span class="sxs-lookup"><span data-stu-id="22802-298">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="22802-299">Trvalé cookie s</span><span class="sxs-lookup"><span data-stu-id="22802-299">Persistent cookies</span></span>

<span data-ttu-id="22802-300">Možná budete chtít, cookie aby trvala v rámci relací prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="22802-300">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="22802-301">Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu.</span><span class="sxs-lookup"><span data-stu-id="22802-301">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="22802-302">Následující fragment kódu vytvoří identitu a odpovídající cookie , která je zachována v případě uzavření prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="22802-302">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="22802-303">Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="22802-303">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="22802-304">Pokud cookie vyprší platnost při zavření prohlížeče, prohlížeč cookie po jeho restartování zruší.</span><span class="sxs-lookup"><span data-stu-id="22802-304">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="22802-305">Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="22802-305">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="22802-306">Absolutní cookie vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="22802-306">Absolute cookie expiration</span></span>

<span data-ttu-id="22802-307">Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="22802-307">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="22802-308">Aby bylo možné vytvořit trvalé cookie , `IsPersistent` musí být také nastaveno.</span><span class="sxs-lookup"><span data-stu-id="22802-308">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="22802-309">V opačném případě se cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje.</span><span class="sxs-lookup"><span data-stu-id="22802-309">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="22802-310">Když `ExpiresUtc` je nastaveno, přepíše hodnotu <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , pokud je nastavena.</span><span class="sxs-lookup"><span data-stu-id="22802-310">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="22802-311">Následující fragment kódu vytvoří identitu a odpovídajícím způsobem cookie , který trvá 20 minut.</span><span class="sxs-lookup"><span data-stu-id="22802-311">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="22802-312">Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="22802-312">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="22802-313">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="22802-313">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="22802-314">Kontroly rolí na základě zásad</span><span class="sxs-lookup"><span data-stu-id="22802-314">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

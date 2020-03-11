---
title: Použití ověřování souborem cookie bez ASP.NET Core identity
author: rick-anderson
description: Naučte se používat ověřování souborem cookie bez ASP.NET Core identity.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
uid: security/authentication/cookie
ms.openlocfilehash: 64f881441a7a7f9a5529cb6ee5ce81142ccd69e6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663000"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="f230a-103">Použití ověřování souborem cookie bez ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="f230a-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="f230a-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f230a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f230a-105">ASP.NET Core identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="f230a-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="f230a-106">Můžete ale použít poskytovatele ověřování na základě souborů cookie bez ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="f230a-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="f230a-107">Další informace naleznete v tématu <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="f230a-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="f230a-108">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f230a-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f230a-109">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="f230a-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="f230a-110">Použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo k přihlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="f230a-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="f230a-111">Uživatel je ověřený v metodě `AuthenticateUser` v souboru *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="f230a-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="f230a-112">V reálném příkladu by byl uživatel ověřený proti databázi.</span><span class="sxs-lookup"><span data-stu-id="f230a-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="f230a-113">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="f230a-113">Configuration</span></span>

<span data-ttu-id="f230a-114">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro balíček [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="f230a-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="f230a-115">V metodě `Startup.ConfigureServices` vytvořte služby middlewaru ověřování pomocí metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>:</span><span class="sxs-lookup"><span data-stu-id="f230a-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="f230a-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> předaný do `AddAuthentication` nastaví výchozí schéma ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f230a-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="f230a-117">`AuthenticationScheme` je užitečné v případě, že existuje více instancí ověřování souborem cookie a chcete [autorizovat s konkrétním schématem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="f230a-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="f230a-118">Nastavení `AuthenticationScheme` [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje pro schéma hodnotu "cookies".</span><span class="sxs-lookup"><span data-stu-id="f230a-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="f230a-119">Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="f230a-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="f230a-120">Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikace.</span><span class="sxs-lookup"><span data-stu-id="f230a-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="f230a-121">Pokud není <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>schéma ověřování souborů cookie k dispozici, používá `CookieAuthenticationDefaults.AuthenticationScheme` ("soubory cookie").</span><span class="sxs-lookup"><span data-stu-id="f230a-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="f230a-122">Vlastnost <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ověřovacího souboru cookie je ve výchozím nastavení nastavena na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="f230a-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="f230a-123">Soubory cookie ověřování jsou povoleny, pokud návštěvník webu nesouhlasí s shromažďováním dat.</span><span class="sxs-lookup"><span data-stu-id="f230a-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="f230a-124">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="f230a-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="f230a-125">V `Startup.Configure`voláním `UseAuthentication` a `UseAuthorization` nastavte vlastnost `HttpContext.User` a spusťte middleware autorizace pro požadavky.</span><span class="sxs-lookup"><span data-stu-id="f230a-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="f230a-126">Před voláním `UseEndpoints`volejte `UseAuthentication` a `UseAuthorization` metody:</span><span class="sxs-lookup"><span data-stu-id="f230a-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="f230a-127">Třída <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> se používá ke konfiguraci možností poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="f230a-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="f230a-128">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v metodě `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f230a-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="f230a-129">Middleware zásad souborů cookie</span><span class="sxs-lookup"><span data-stu-id="f230a-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="f230a-130">[Middleware zásad souborů cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje využít zásady souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f230a-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="f230a-131">Přidání middlewaru do kanálu zpracování aplikace je v pořádku,&mdash;ovlivňuje pouze podřízené komponenty registrované v kanálu.</span><span class="sxs-lookup"><span data-stu-id="f230a-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="f230a-132">K řízení globálních charakteristik zpracování souborů cookie a připojení do obslužných rutin zpracování souborů cookie, když se připojí nebo odstraní soubory cookie, použijte <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytované middlewaru zásad souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f230a-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="f230a-133">Výchozí hodnota <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> je `SameSiteMode.Lax`, aby povolovala ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="f230a-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="f230a-134">Chcete-li striktně vymáhat zásady `SameSiteMode.Strict`stejné lokality, nastavte `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="f230a-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="f230a-135">I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souborů cookie pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="f230a-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="f230a-136">Nastavení middlewaru zásad souborů cookie pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle matice níže.</span><span class="sxs-lookup"><span data-stu-id="f230a-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="f230a-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="f230a-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="f230a-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="f230a-138">Cookie.SameSite</span></span> | <span data-ttu-id="f230a-139">Nastavení výsledného souboru cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="f230a-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="f230a-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f230a-140">SameSiteMode.None</span></span>     | <span data-ttu-id="f230a-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f230a-141">SameSiteMode.None</span></span><br><span data-ttu-id="f230a-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="f230a-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f230a-144">SameSiteMode.None</span></span><br><span data-ttu-id="f230a-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f230a-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="f230a-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f230a-148">SameSiteMode.None</span></span><br><span data-ttu-id="f230a-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="f230a-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f230a-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="f230a-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f230a-155">SameSiteMode.None</span></span><br><span data-ttu-id="f230a-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="f230a-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="f230a-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="f230a-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="f230a-161">Vytvoření ověřovacího souboru cookie</span><span class="sxs-lookup"><span data-stu-id="f230a-161">Create an authentication cookie</span></span>

<span data-ttu-id="f230a-162">Chcete-li vytvořit soubor cookie obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal>.</span><span class="sxs-lookup"><span data-stu-id="f230a-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="f230a-163">Informace o uživateli jsou serializovány a uloženy v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f230a-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="f230a-164">Vytvořte <xref:System.Security.Claims.ClaimsIdentity> s libovolným požadovaným <xref:System.Security.Claims.Claim>s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="f230a-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="f230a-165">`SignInAsync` vytvoří zašifrovaný soubor cookie a přidá ho k aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f230a-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="f230a-166">Pokud není zadaný `AuthenticationScheme`, použije se výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="f230a-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="f230a-167">Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování.</span><span class="sxs-lookup"><span data-stu-id="f230a-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="f230a-168">Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.</span><span class="sxs-lookup"><span data-stu-id="f230a-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="f230a-169">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="f230a-169">Sign out</span></span>

<span data-ttu-id="f230a-170">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="f230a-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="f230a-171">Pokud se jako schéma (například "ContosoCookie") nepoužívá `CookieAuthenticationDefaults.AuthenticationScheme` (nebo "soubory cookie"), zadejte schéma používané při konfiguraci poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="f230a-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="f230a-172">V opačném případě se použije výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="f230a-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="f230a-173">Reakce na back-endové změny</span><span class="sxs-lookup"><span data-stu-id="f230a-173">React to back-end changes</span></span>

<span data-ttu-id="f230a-174">Po vytvoření souboru cookie je soubor cookie jediným zdrojem identity.</span><span class="sxs-lookup"><span data-stu-id="f230a-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="f230a-175">Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="f230a-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="f230a-176">Systém ověřování souborů cookie aplikace nadále zpracovává požadavky založené na souboru cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="f230a-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="f230a-177">Uživatel zůstane přihlášený k aplikaci, pokud je ověřovací soubor cookie platný.</span><span class="sxs-lookup"><span data-stu-id="f230a-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="f230a-178">Událost <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> lze použít k zachycení a přepsání ověřování identity souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f230a-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="f230a-179">Ověření souboru cookie u všech požadavků snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f230a-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="f230a-180">Jeden přístup k ověření souboru cookie je založen na sledování toho, kdy se databáze uživatele změní.</span><span class="sxs-lookup"><span data-stu-id="f230a-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="f230a-181">Pokud se databáze od vydání souboru cookie uživatele nezměnila, není nutné znovu ověřit uživatele, pokud je jejich soubor cookie stále platný.</span><span class="sxs-lookup"><span data-stu-id="f230a-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="f230a-182">V ukázkové aplikaci je databáze implementovaná v `IUserRepository` a ukládá hodnotu `LastChanged`.</span><span class="sxs-lookup"><span data-stu-id="f230a-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="f230a-183">Když se uživatel v databázi aktualizuje, hodnota `LastChanged` je nastavená na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="f230a-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="f230a-184">Aby se soubor cookie neověřoval, když se databáze změní na základě `LastChanged` hodnoty, vytvořte soubor cookie s `LastChanged` deklarací identity obsahující aktuální hodnotu `LastChanged` z databáze:</span><span class="sxs-lookup"><span data-stu-id="f230a-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="f230a-185">Chcete-li implementovat přepsání pro událost `ValidatePrincipal`, zapište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="f230a-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="f230a-186">Následuje příklad implementace `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="f230a-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="f230a-187">Zaregistrujte instanci události během registrace služby souborů cookie v metodě `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f230a-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f230a-188">Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro třídu `CustomCookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="f230a-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="f230a-189">Vezměte v úvahu situaci, kdy se uživatelské jméno aktualizuje&mdash;rozhodnutí, které nijak neovlivňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="f230a-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="f230a-190">Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte vlastnost `context.ShouldRenew` na `true`.</span><span class="sxs-lookup"><span data-stu-id="f230a-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f230a-191">Přístup, který je zde popsán, se spustí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="f230a-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="f230a-192">Ověřování souborů cookie pro ověřování pro všechny uživatele na všech žádostech může mít za následek velkou sankci pro výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="f230a-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="f230a-193">Trvalé soubory cookie</span><span class="sxs-lookup"><span data-stu-id="f230a-193">Persistent cookies</span></span>

<span data-ttu-id="f230a-194">Můžete chtít, aby soubor cookie trval mezi relacemi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f230a-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="f230a-195">Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu.</span><span class="sxs-lookup"><span data-stu-id="f230a-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="f230a-196">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který se zachová v rámci zavření prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f230a-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="f230a-197">Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="f230a-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="f230a-198">Pokud soubor cookie vyprší v době, kdy je prohlížeč zavřen, prohlížeč po restartování odstraní soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="f230a-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="f230a-199">Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` v <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="f230a-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="f230a-200">Vypršení platnosti absolutního souboru cookie</span><span class="sxs-lookup"><span data-stu-id="f230a-200">Absolute cookie expiration</span></span>

<span data-ttu-id="f230a-201">Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span><span class="sxs-lookup"><span data-stu-id="f230a-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="f230a-202">Chcete-li vytvořit trvalý soubor cookie, musí být také nastavena `IsPersistent`.</span><span class="sxs-lookup"><span data-stu-id="f230a-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="f230a-203">V opačném případě se soubor cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje.</span><span class="sxs-lookup"><span data-stu-id="f230a-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="f230a-204">Pokud je nastavena `ExpiresUtc`, přepíše hodnotu <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, pokud je nastavena.</span><span class="sxs-lookup"><span data-stu-id="f230a-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="f230a-205">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který trvá 20 minut.</span><span class="sxs-lookup"><span data-stu-id="f230a-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="f230a-206">Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="f230a-206">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="f230a-207">ASP.NET Core identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="f230a-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="f230a-208">Můžete ale použít poskytovatele ověřování ověřování na základě souborů cookie bez ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="f230a-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="f230a-209">Další informace naleznete v tématu <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="f230a-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="f230a-210">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f230a-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f230a-211">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="f230a-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="f230a-212">Použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo k přihlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="f230a-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="f230a-213">Uživatel je ověřený v metodě `AuthenticateUser` v souboru *Pages/Account/Login. cshtml. cs* .</span><span class="sxs-lookup"><span data-stu-id="f230a-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="f230a-214">V reálném příkladu by byl uživatel ověřený proti databázi.</span><span class="sxs-lookup"><span data-stu-id="f230a-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="f230a-215">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="f230a-215">Configuration</span></span>

<span data-ttu-id="f230a-216">Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro balíček [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="f230a-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="f230a-217">V metodě `Startup.ConfigureServices` vytvořte službu middleware ověřování pomocí metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>:</span><span class="sxs-lookup"><span data-stu-id="f230a-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="f230a-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> předaný do `AddAuthentication` nastaví výchozí schéma ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f230a-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="f230a-219">`AuthenticationScheme` je užitečné v případě, že existuje více instancí ověřování souborem cookie a chcete [autorizovat s konkrétním schématem](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="f230a-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="f230a-220">Nastavení `AuthenticationScheme` [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje pro schéma hodnotu "cookies".</span><span class="sxs-lookup"><span data-stu-id="f230a-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="f230a-221">Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="f230a-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="f230a-222">Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikace.</span><span class="sxs-lookup"><span data-stu-id="f230a-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="f230a-223">Pokud není <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>schéma ověřování souborů cookie k dispozici, používá `CookieAuthenticationDefaults.AuthenticationScheme` ("soubory cookie").</span><span class="sxs-lookup"><span data-stu-id="f230a-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="f230a-224">Vlastnost <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ověřovacího souboru cookie je ve výchozím nastavení nastavena na hodnotu `true`.</span><span class="sxs-lookup"><span data-stu-id="f230a-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="f230a-225">Soubory cookie ověřování jsou povoleny, pokud návštěvník webu nesouhlasí s shromažďováním dat.</span><span class="sxs-lookup"><span data-stu-id="f230a-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="f230a-226">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="f230a-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="f230a-227">V metodě `Startup.Configure` voláním metody `UseAuthentication` volejte middleware ověřování, který nastaví vlastnost `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="f230a-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="f230a-228">Před voláním `UseMvcWithDefaultRoute` nebo `UseMvc`volejte metodu `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f230a-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="f230a-229">Třída <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> se používá ke konfiguraci možností poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="f230a-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="f230a-230">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v metodě `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f230a-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="f230a-231">Middleware zásad souborů cookie</span><span class="sxs-lookup"><span data-stu-id="f230a-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="f230a-232">[Middleware zásad souborů cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje využít zásady souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f230a-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="f230a-233">Přidání middlewaru do kanálu zpracování aplikace je v pořádku,&mdash;ovlivňuje pouze podřízené komponenty registrované v kanálu.</span><span class="sxs-lookup"><span data-stu-id="f230a-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="f230a-234">K řízení globálních charakteristik zpracování souborů cookie a připojení do obslužných rutin zpracování souborů cookie, když se připojí nebo odstraní soubory cookie, použijte <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytované middlewaru zásad souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f230a-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="f230a-235">Výchozí hodnota <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> je `SameSiteMode.Lax`, aby povolovala ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="f230a-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="f230a-236">Chcete-li striktně vymáhat zásady `SameSiteMode.Strict`stejné lokality, nastavte `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="f230a-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="f230a-237">I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souborů cookie pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="f230a-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="f230a-238">Nastavení middlewaru zásad souborů cookie pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle matice níže.</span><span class="sxs-lookup"><span data-stu-id="f230a-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="f230a-239">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="f230a-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="f230a-240">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="f230a-240">Cookie.SameSite</span></span> | <span data-ttu-id="f230a-241">Nastavení výsledného souboru cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="f230a-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="f230a-242">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f230a-242">SameSiteMode.None</span></span>     | <span data-ttu-id="f230a-243">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f230a-243">SameSiteMode.None</span></span><br><span data-ttu-id="f230a-244">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-245">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="f230a-246">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f230a-246">SameSiteMode.None</span></span><br><span data-ttu-id="f230a-247">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-248">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f230a-249">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="f230a-250">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f230a-250">SameSiteMode.None</span></span><br><span data-ttu-id="f230a-251">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-252">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="f230a-253">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-254">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-255">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f230a-256">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="f230a-257">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f230a-257">SameSiteMode.None</span></span><br><span data-ttu-id="f230a-258">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f230a-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="f230a-259">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="f230a-260">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="f230a-261">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="f230a-262">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f230a-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="f230a-263">Vytvoření ověřovacího souboru cookie</span><span class="sxs-lookup"><span data-stu-id="f230a-263">Create an authentication cookie</span></span>

<span data-ttu-id="f230a-264">Chcete-li vytvořit soubor cookie obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal>.</span><span class="sxs-lookup"><span data-stu-id="f230a-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="f230a-265">Informace o uživateli jsou serializovány a uloženy v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f230a-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="f230a-266">Vytvořte <xref:System.Security.Claims.ClaimsIdentity> s libovolným požadovaným <xref:System.Security.Claims.Claim>s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="f230a-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="f230a-267">`SignInAsync` vytvoří zašifrovaný soubor cookie a přidá ho k aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f230a-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="f230a-268">Pokud není zadaný `AuthenticationScheme`, použije se výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="f230a-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="f230a-269">Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování.</span><span class="sxs-lookup"><span data-stu-id="f230a-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="f230a-270">Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.</span><span class="sxs-lookup"><span data-stu-id="f230a-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="f230a-271">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="f230a-271">Sign out</span></span>

<span data-ttu-id="f230a-272">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="f230a-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="f230a-273">Pokud se jako schéma (například "ContosoCookie") nepoužívá `CookieAuthenticationDefaults.AuthenticationScheme` (nebo "soubory cookie"), zadejte schéma používané při konfiguraci poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="f230a-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="f230a-274">V opačném případě se použije výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="f230a-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="f230a-275">Reakce na back-endové změny</span><span class="sxs-lookup"><span data-stu-id="f230a-275">React to back-end changes</span></span>

<span data-ttu-id="f230a-276">Po vytvoření souboru cookie je soubor cookie jediným zdrojem identity.</span><span class="sxs-lookup"><span data-stu-id="f230a-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="f230a-277">Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="f230a-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="f230a-278">Systém ověřování souborů cookie aplikace nadále zpracovává požadavky založené na souboru cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="f230a-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="f230a-279">Uživatel zůstane přihlášený k aplikaci, pokud je ověřovací soubor cookie platný.</span><span class="sxs-lookup"><span data-stu-id="f230a-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="f230a-280">Událost <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> lze použít k zachycení a přepsání ověřování identity souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f230a-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="f230a-281">Ověření souboru cookie u všech požadavků snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f230a-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="f230a-282">Jeden přístup k ověření souboru cookie je založen na sledování toho, kdy se databáze uživatele změní.</span><span class="sxs-lookup"><span data-stu-id="f230a-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="f230a-283">Pokud se databáze od vydání souboru cookie uživatele nezměnila, není nutné znovu ověřit uživatele, pokud je jejich soubor cookie stále platný.</span><span class="sxs-lookup"><span data-stu-id="f230a-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="f230a-284">V ukázkové aplikaci je databáze implementovaná v `IUserRepository` a ukládá hodnotu `LastChanged`.</span><span class="sxs-lookup"><span data-stu-id="f230a-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="f230a-285">Když se uživatel v databázi aktualizuje, hodnota `LastChanged` je nastavená na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="f230a-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="f230a-286">Aby se soubor cookie neověřoval, když se databáze změní na základě `LastChanged` hodnoty, vytvořte soubor cookie s `LastChanged` deklarací identity obsahující aktuální hodnotu `LastChanged` z databáze:</span><span class="sxs-lookup"><span data-stu-id="f230a-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="f230a-287">Chcete-li implementovat přepsání pro událost `ValidatePrincipal`, zapište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="f230a-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="f230a-288">Následuje příklad implementace `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="f230a-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="f230a-289">Zaregistrujte instanci události během registrace služby souborů cookie v metodě `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f230a-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f230a-290">Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro třídu `CustomCookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="f230a-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="f230a-291">Vezměte v úvahu situaci, kdy se uživatelské jméno aktualizuje&mdash;rozhodnutí, které nijak neovlivňuje zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="f230a-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="f230a-292">Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte vlastnost `context.ShouldRenew` na `true`.</span><span class="sxs-lookup"><span data-stu-id="f230a-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f230a-293">Přístup, který je zde popsán, se spustí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="f230a-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="f230a-294">Ověřování souborů cookie pro ověřování pro všechny uživatele na všech žádostech může mít za následek velkou sankci pro výkon aplikace.</span><span class="sxs-lookup"><span data-stu-id="f230a-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="f230a-295">Trvalé soubory cookie</span><span class="sxs-lookup"><span data-stu-id="f230a-295">Persistent cookies</span></span>

<span data-ttu-id="f230a-296">Můžete chtít, aby soubor cookie trval mezi relacemi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f230a-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="f230a-297">Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu.</span><span class="sxs-lookup"><span data-stu-id="f230a-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="f230a-298">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který se zachová v rámci zavření prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f230a-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="f230a-299">Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="f230a-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="f230a-300">Pokud soubor cookie vyprší v době, kdy je prohlížeč zavřen, prohlížeč po restartování odstraní soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="f230a-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="f230a-301">Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` v <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="f230a-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="f230a-302">Vypršení platnosti absolutního souboru cookie</span><span class="sxs-lookup"><span data-stu-id="f230a-302">Absolute cookie expiration</span></span>

<span data-ttu-id="f230a-303">Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span><span class="sxs-lookup"><span data-stu-id="f230a-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="f230a-304">Chcete-li vytvořit trvalý soubor cookie, musí být také nastavena `IsPersistent`.</span><span class="sxs-lookup"><span data-stu-id="f230a-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="f230a-305">V opačném případě se soubor cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje.</span><span class="sxs-lookup"><span data-stu-id="f230a-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="f230a-306">Pokud je nastavena `ExpiresUtc`, přepíše hodnotu <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, pokud je nastavena.</span><span class="sxs-lookup"><span data-stu-id="f230a-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="f230a-307">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který trvá 20 minut.</span><span class="sxs-lookup"><span data-stu-id="f230a-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="f230a-308">Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.</span><span class="sxs-lookup"><span data-stu-id="f230a-308">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="f230a-309">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f230a-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="f230a-310">Kontroly rolí na základě zásad</span><span class="sxs-lookup"><span data-stu-id="f230a-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

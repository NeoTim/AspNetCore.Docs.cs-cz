---
title: Ověřování souborem cookie bez ASP.NET Core Identity
author: rick-anderson
description: Další informace o použití ověřování souborem cookie bez ASP.NET Core Identity.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/07/2019
uid: security/authentication/cookie
ms.openlocfilehash: bbba2e77f806e1ed30bb734763cdbaedc1471d62
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622749"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="d6adf-103">Ověřování souborem cookie bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="d6adf-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="d6adf-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d6adf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d6adf-105">ASP.NET Core Identity je ověřování dokončeno, plně vybavené poskytovatel pro vytváření a správa přihlášení.</span><span class="sxs-lookup"><span data-stu-id="d6adf-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="d6adf-106">Nicméně je možné zprostředkovatele ověřování ověřování na základě souboru cookie bez ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="d6adf-106">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="d6adf-107">Další informace naleznete v tématu <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="d6adf-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="d6adf-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d6adf-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d6adf-109">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetické uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6adf-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="d6adf-110">Použití **e-mailu** uživatelské jméno `maria.rodriguez@contoso.com` a jakékoli heslo k přihlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="d6adf-110">Use the **Email** user name `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="d6adf-111">Ověření uživatele v `AuthenticateUser` metodu *Pages/Account/Login.cshtml.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="d6adf-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="d6adf-112">V reálný příklad uživatel by ověřovány proti databázi.</span><span class="sxs-lookup"><span data-stu-id="d6adf-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="d6adf-113">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="d6adf-113">Configuration</span></span>

<span data-ttu-id="d6adf-114">Pokud se aplikace nepoužívá [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), vytvořit odkaz na balíček v souboru projektu [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="d6adf-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="d6adf-115">V `Startup.ConfigureServices` metody vytvoření Middleware ověřování služby s <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metody:</span><span class="sxs-lookup"><span data-stu-id="d6adf-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="d6adf-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> Předaný `AddAuthentication` nastaví výchozí schéma ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6adf-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="d6adf-117">`AuthenticationScheme` je užitečné, pokud existuje více instancí ověřování souborů cookie a vy chcete [autorizovat s konkrétní schéma](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="d6adf-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="d6adf-118">Nastavení `AuthenticationScheme` k [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje hodnotu "Soubory cookie" pro schéma.</span><span class="sxs-lookup"><span data-stu-id="d6adf-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="d6adf-119">Můžete zadat libovolnou hodnotu řetězce, která odlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="d6adf-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="d6adf-120">Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6adf-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="d6adf-121">Pokud není k dispozici schéma ověřování souborů cookie do <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, používá `CookieAuthenticationDefaults.AuthenticationScheme` (soubory cookie.").</span><span class="sxs-lookup"><span data-stu-id="d6adf-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="d6adf-122">Soubor cookie ověřování <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je nastavena na `true` ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="d6adf-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="d6adf-123">Když návštěvník nevyjádřil souhlas shromažďování dat jsou povoleny soubory cookie pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="d6adf-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="d6adf-124">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="d6adf-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="d6adf-125">V `Startup.Configure` metody, volání `UseAuthentication` metoda k vyvolání ověřovací Middleware, který nastaví `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="d6adf-125">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="d6adf-126">Volání `UseAuthentication` před voláním metody `UseMvcWithDefaultRoute` nebo `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="d6adf-126">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="d6adf-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Třída se používá ke konfiguraci možností zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="d6adf-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="d6adf-128">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="d6adf-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="d6adf-129">Zásady middlewaru souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="d6adf-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="d6adf-130">[Zásady middlewaru souboru cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje možnosti zásad souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="d6adf-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="d6adf-131">Přidání middleware do kanálu zpracování aplikace je v pořadí citlivé&mdash;ovlivní pouze podřízené komponenty zaregistrovaný v kanálu.</span><span class="sxs-lookup"><span data-stu-id="d6adf-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="d6adf-132">Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytnuté zásady middlewaru souboru Cookie. k řízení globální vlastnosti zpracování souboru cookie a hook do obslužné rutiny zpracování souboru cookie, když jsou soubory cookie připojí nebo odstraní.</span><span class="sxs-lookup"><span data-stu-id="d6adf-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="d6adf-133">Výchozí hodnota <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnotu `SameSiteMode.Lax` tak, aby povolovala ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="d6adf-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="d6adf-134">Přísně vynutit zásady stejný web `SameSiteMode.Strict`, nastavte `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="d6adf-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="d6adf-135">I když toto nastavení přeruší OAuth2 nebo jiná schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souboru cookie pro ostatní typy aplikací, které se nemusíte spoléhat na zpracování žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="d6adf-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="d6adf-136">Nastavení zásad middlewaru souboru Cookie pro `MinimumSameSitePolicy` může mít vliv na nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle níže uvedených matice.</span><span class="sxs-lookup"><span data-stu-id="d6adf-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="d6adf-137">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="d6adf-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="d6adf-138">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="d6adf-138">Cookie.SameSite</span></span> | <span data-ttu-id="d6adf-139">Výsledná nastavení Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="d6adf-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="d6adf-140">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="d6adf-140">SameSiteMode.None</span></span>     | <span data-ttu-id="d6adf-141">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="d6adf-141">SameSiteMode.None</span></span><br><span data-ttu-id="d6adf-142">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d6adf-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="d6adf-143">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d6adf-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="d6adf-144">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="d6adf-144">SameSiteMode.None</span></span><br><span data-ttu-id="d6adf-145">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d6adf-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="d6adf-146">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d6adf-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="d6adf-147">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d6adf-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="d6adf-148">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="d6adf-148">SameSiteMode.None</span></span><br><span data-ttu-id="d6adf-149">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d6adf-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="d6adf-150">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d6adf-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="d6adf-151">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d6adf-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="d6adf-152">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d6adf-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="d6adf-153">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d6adf-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="d6adf-154">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d6adf-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="d6adf-155">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="d6adf-155">SameSiteMode.None</span></span><br><span data-ttu-id="d6adf-156">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="d6adf-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="d6adf-157">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d6adf-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="d6adf-158">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d6adf-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="d6adf-159">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d6adf-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="d6adf-160">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="d6adf-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="d6adf-161">Vytvoření souboru cookie pro ověřování</span><span class="sxs-lookup"><span data-stu-id="d6adf-161">Create an authentication cookie</span></span>

<span data-ttu-id="d6adf-162">Pokud chcete vytvořit soubor cookie, která uchovává informace o uživateli, vytvořit <xref:System.Security.Claims.ClaimsPrincipal>.</span><span class="sxs-lookup"><span data-stu-id="d6adf-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="d6adf-163">Informace o uživateli je serializován a uložená v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="d6adf-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="d6adf-164">Vytvoření <xref:System.Security.Claims.ClaimsIdentity> s jakékoli požadované <xref:System.Security.Claims.Claim>s a volání <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> k přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="d6adf-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="d6adf-165">`SignInAsync` Vytvoří do zašifrovaného souboru cookie a přidá jej do aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="d6adf-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="d6adf-166">Pokud `AuthenticationScheme` není zadán, výchozí schéma se používá.</span><span class="sxs-lookup"><span data-stu-id="d6adf-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="d6adf-167">ASP.NET Core [ochranu dat](xref:security/data-protection/using-data-protection) pro šifrování je použit systém.</span><span class="sxs-lookup"><span data-stu-id="d6adf-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="d6adf-168">Pro aplikaci hostovanou ve více počítačích, zatížení vyrovnávání mezi aplikacemi, nebo pomocí webové farmy, [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview) používat stejné aktualizační kanál klíč a identifikátor aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6adf-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="d6adf-169">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="d6adf-169">Sign out</span></span>

<span data-ttu-id="d6adf-170">Chcete-li odhlásit aktuálního uživatele a odstranit jejich souborů cookie, zavolejte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="d6adf-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="d6adf-171">Pokud `CookieAuthenticationDefaults.AuthenticationScheme` (nebo "Soubory cookie") se nepoužívá jako schéma (například "ContosoCookie"), zadejte schéma používané při konfiguraci zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="d6adf-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="d6adf-172">V opačném případě se používá výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="d6adf-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="d6adf-173">Reagovat na změny back-end</span><span class="sxs-lookup"><span data-stu-id="d6adf-173">React to back-end changes</span></span>

<span data-ttu-id="d6adf-174">Po vytvoření souboru cookie je soubor cookie jediný zdroj identity.</span><span class="sxs-lookup"><span data-stu-id="d6adf-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="d6adf-175">Pokud uživatelský účet zakázaná v back endových systémů:</span><span class="sxs-lookup"><span data-stu-id="d6adf-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="d6adf-176">Aplikace soubor cookie ověřování systém bude dál zpracovávat žádosti na základě souboru cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="d6adf-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="d6adf-177">Uživatel zůstane přihlášený do aplikace za předpokladu, je platný soubor cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="d6adf-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="d6adf-178"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> Události je možné zachytit a přepsat ověření souboru cookie identity.</span><span class="sxs-lookup"><span data-stu-id="d6adf-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="d6adf-179">Ověřování souborů cookie u každého požadavku snižuje riziko odvolané uživatele, kteří používají aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d6adf-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="d6adf-180">Jedním z přístupů k ověření souboru cookie je založená na udržování přehledu o změně uživatelskou databázi.</span><span class="sxs-lookup"><span data-stu-id="d6adf-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="d6adf-181">Pokud databáze nebylo změněno od uživatele soubor cookie vydala, není nutné opakované ověření uživatele, pokud jejich souborů cookie je stále platný.</span><span class="sxs-lookup"><span data-stu-id="d6adf-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="d6adf-182">V ukázkové aplikaci, databázi je implementována v `IUserRepository` a uloží `LastChanged` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d6adf-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="d6adf-183">Při aktualizaci uživatele v databázi, `LastChanged` je hodnota nastavena na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="d6adf-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="d6adf-184">Pokud chcete platnost souboru cookie, pokud změny databáze v závislosti na `LastChanged` hodnoty, vytvořte soubor cookie s `LastChanged` deklarace obsahující aktuální `LastChanged` hodnota z databáze:</span><span class="sxs-lookup"><span data-stu-id="d6adf-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="d6adf-185">K implementaci přepsání `ValidatePrincipal` události, napište metodu s následující signaturou ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span><span class="sxs-lookup"><span data-stu-id="d6adf-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="d6adf-186">Tady je příklad implementace `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="d6adf-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="d6adf-187">Zaregistrovat instanci události během registrace služby souborů cookie v `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="d6adf-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d6adf-188">Zadejte [obor registrace služby](xref:fundamentals/dependency-injection#service-lifetimes) pro vaše `CustomCookieAuthenticationEvents` třídy:</span><span class="sxs-lookup"><span data-stu-id="d6adf-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="d6adf-189">Představte si situaci, ve kterém se aktualizuje uživatelské jméno&mdash;rozhodnutí, která nemá vliv na zabezpečení žádným způsobem.</span><span class="sxs-lookup"><span data-stu-id="d6adf-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="d6adf-190">Pokud chcete aktualizovat nedestruktivně hlavní název uživatele, zavolejte `context.ReplacePrincipal` a nastavit `context.ShouldRenew` vlastnost `true`.</span><span class="sxs-lookup"><span data-stu-id="d6adf-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d6adf-191">Tento přístup je zde popsáno, se aktivuje u každého požadavku.</span><span class="sxs-lookup"><span data-stu-id="d6adf-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="d6adf-192">Ověřuje se ověřovací soubory cookie pro všechny uživatele v každé žádosti může způsobit snížení výkonu velké aplikace.</span><span class="sxs-lookup"><span data-stu-id="d6adf-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="d6adf-193">Trvalé soubory cookie</span><span class="sxs-lookup"><span data-stu-id="d6adf-193">Persistent cookies</span></span>

<span data-ttu-id="d6adf-194">Můžete chtít soubor cookie zachovat v rámci relací prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="d6adf-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="d6adf-195">Tato trvalost má být povoleno pouze explicitní uživatelské souhlas s "Zapamatovat" zaškrtávací políčko na přihlašovací nebo mechanismus podobný.</span><span class="sxs-lookup"><span data-stu-id="d6adf-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="d6adf-196">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který odolává prostřednictvím prohlížeče UZÁVĚRECH.</span><span class="sxs-lookup"><span data-stu-id="d6adf-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="d6adf-197">Klouzavé vypršení platnosti nastavení jste dříve nakonfigurovali jsou zachované.</span><span class="sxs-lookup"><span data-stu-id="d6adf-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="d6adf-198">Pokud se při zavření prohlížeče vyprší platnost souboru cookie, vymaže prohlížeči soubor cookie po jeho restartování.</span><span class="sxs-lookup"><span data-stu-id="d6adf-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="d6adf-199">Nastavte <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> k `true` v <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="d6adf-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="d6adf-200">Soubor cookie absolutní vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="d6adf-200">Absolute cookie expiration</span></span>

<span data-ttu-id="d6adf-201">Čas absolutní vypršení platnosti můžete nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span><span class="sxs-lookup"><span data-stu-id="d6adf-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="d6adf-202">K vytvoření trvalého souboru cookie, `IsPersistent` musí být také nastavena.</span><span class="sxs-lookup"><span data-stu-id="d6adf-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="d6adf-203">V opačném případě soubor cookie se vytvoří s životností založeného na relacích a může vyprší před nebo po lístek ověřování, který ji obsahuje.</span><span class="sxs-lookup"><span data-stu-id="d6adf-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="d6adf-204">Když `ExpiresUtc` je nastaven, přepíše hodnotu <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> možnost <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, pokud nastavení.</span><span class="sxs-lookup"><span data-stu-id="d6adf-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="d6adf-205">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který má platnost po dobu 20 minut.</span><span class="sxs-lookup"><span data-stu-id="d6adf-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="d6adf-206">To ignoruje klouzavé vypršení platnosti nastavení předtím nakonfigurovali.</span><span class="sxs-lookup"><span data-stu-id="d6adf-206">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="d6adf-207">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d6adf-207">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="d6adf-208">Role na základě zásad kontroly</span><span class="sxs-lookup"><span data-stu-id="d6adf-208">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

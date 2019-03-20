---
title: Ověřování souborem cookie bez ASP.NET Core Identity
author: rick-anderson
description: Vysvětlení, používání ověřování souborem cookie bez ASP.NET Core Identity
ms.author: riande
ms.date: 02/25/2019
uid: security/authentication/cookie
ms.openlocfilehash: c6cba588abb003ee677d8f1753b73a1ced1414b0
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209372"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="f2ebe-103">Ověřování souborem cookie bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="f2ebe-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="f2ebe-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f2ebe-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f2ebe-105">Jak už víte, v předchozích tématech ověřování [ASP.NET Core Identity](xref:security/authentication/identity) je ověřování dokončeno, plně vybavené zprostředkovatele pro vytváření a správa přihlášení.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-105">As you've seen in the earlier authentication topics, [ASP.NET Core Identity](xref:security/authentication/identity) is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="f2ebe-106">Můžete ale použít vlastní logiku ověřování vlastní soubor cookie s ověřováním pomocí čas od času.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-106">However, you may want to use your own custom authentication logic with cookie-based authentication at times.</span></span> <span data-ttu-id="f2ebe-107">Ověřování na základě souborů cookie slouží jako zprostředkovatel ověřování samostatné bez ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-107">You can use cookie-based authentication as a standalone authentication provider without ASP.NET Core Identity.</span></span>

<span data-ttu-id="f2ebe-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f2ebe-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f2ebe-109">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetické uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="f2ebe-110">Použití uživatelského jména e-mailu "maria.rodriguez@contoso.com" a jakékoli heslo k přihlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-110">Use the Email username "maria.rodriguez@contoso.com" and any password to sign in the user.</span></span> <span data-ttu-id="f2ebe-111">Ověření uživatele v `AuthenticateUser` metodu *Pages/Account/Login.cshtml.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="f2ebe-112">V reálný příklad uživatel by ověřovány proti databázi.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-112">In a real-world example, the user would be authenticated against a database.</span></span>

<span data-ttu-id="f2ebe-113">Informace o migraci na základě souboru cookie ověřování ASP.NET Core 1.x do 2.0, naleznete v tématu [migrovat ověřování a identita na téma ASP.NET Core 2.0 (ověřování na základě souborů Cookie)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-113">For information on migrating cookie-based authentication from ASP.NET Core 1.x to 2.0, see [Migrate Authentication and Identity to ASP.NET Core 2.0 topic (Cookie-based Authentication)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).</span></span>

<span data-ttu-id="f2ebe-114">Použití ASP.NET Core Identity, najdete v článku [Úvod do Identity](xref:security/authentication/identity) tématu.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-114">To use ASP.NET Core Identity, see the [Introduction to Identity](xref:security/authentication/identity) topic.</span></span>

## <a name="configuration"></a><span data-ttu-id="f2ebe-115">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="f2ebe-115">Configuration</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f2ebe-116">Pokud se aplikace nepoužívá [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), vytvořit odkaz na balíček v souboru projektu [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) balíčku (verze 2.1.0 nebo později).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-116">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package (version 2.1.0 or later).</span></span>

<span data-ttu-id="f2ebe-117">V `ConfigureServices` metody vytvoření Middleware ověřování služby s `AddAuthentication` a `AddCookie` metody:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-117">In the `ConfigureServices` method, create the Authentication Middleware service with the `AddAuthentication` and `AddCookie` methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="f2ebe-118">`AuthenticationScheme` Předaný `AddAuthentication` nastaví výchozí schéma ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-118">`AuthenticationScheme` passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="f2ebe-119">`AuthenticationScheme` je užitečné, pokud existuje více instancí ověřování souborů cookie a vy chcete [autorizovat s konkrétní schéma](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-119">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="f2ebe-120">Nastavení `AuthenticationScheme` k `CookieAuthenticationDefaults.AuthenticationScheme` poskytuje hodnotu "Soubory cookie" pro schéma.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-120">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="f2ebe-121">Můžete zadat libovolnou hodnotu řetězce, která odlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-121">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="f2ebe-122">Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-122">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="f2ebe-123">Pokud není k dispozici schéma ověřování souborů cookie do <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, používá [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (soubory cookie.").</span><span class="sxs-lookup"><span data-stu-id="f2ebe-123">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies").</span></span>

<span data-ttu-id="f2ebe-124">Soubor cookie ověřování <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je nastavena na `true` ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-124">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="f2ebe-125">Když návštěvník nevyjádřil souhlas shromažďování dat jsou povoleny soubory cookie pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-125">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="f2ebe-126">Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-126">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="f2ebe-127">V `Configure` metody, použijte `UseAuthentication` metoda k vyvolání ověřovací Middleware, který nastaví `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-127">In the `Configure` method, use the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="f2ebe-128">Volání `UseAuthentication` před voláním metody `UseMvcWithDefaultRoute` nebo `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-128">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="f2ebe-129">**Možnosti AddCookie**</span><span class="sxs-lookup"><span data-stu-id="f2ebe-129">**AddCookie Options**</span></span>

<span data-ttu-id="f2ebe-130">[CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) třída se používá ke konfiguraci možností zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-130">The [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="f2ebe-131">Možnost</span><span class="sxs-lookup"><span data-stu-id="f2ebe-131">Option</span></span> | <span data-ttu-id="f2ebe-132">Popis</span><span class="sxs-lookup"><span data-stu-id="f2ebe-132">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="f2ebe-133">AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="f2ebe-133">AccessDeniedPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-134">Určuje cestu k poskytování s 302 Found (adresa URL přesměrování) aktivovaného `HttpContext.ForbidAsync`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-134">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ForbidAsync`.</span></span> <span data-ttu-id="f2ebe-135">Výchozí hodnota je `/Account/AccessDenied`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-135">The default value is `/Account/AccessDenied`.</span></span> |
| [<span data-ttu-id="f2ebe-136">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="f2ebe-136">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-137">Vystavitel, který má použít pro [vystavitele](/dotnet/api/system.security.claims.claim.issuer) vlastnost na všechny deklarace vytvořené službou ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-137">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication service.</span></span> |
| [<span data-ttu-id="f2ebe-138">Cookie.Domain</span><span class="sxs-lookup"><span data-stu-id="f2ebe-138">Cookie.Domain</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-139">Název domény, kde je soubor cookie obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-139">The domain name where the cookie is served.</span></span> <span data-ttu-id="f2ebe-140">Ve výchozím nastavení toto je název hostitele žádosti.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-140">By default, this is the host name of the request.</span></span> <span data-ttu-id="f2ebe-141">Prohlížeč odesílá pouze soubor cookie v požadavcích na odpovídající název hostitele.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-141">The browser only sends the cookie in requests to a matching host name.</span></span> <span data-ttu-id="f2ebe-142">Můžete chtít upravit tak, aby soubory cookie, které jsou k dispozici pro všechny hostitele ve vaší doméně.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-142">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="f2ebe-143">Například nastavení domény souboru cookie na `.contoso.com` ji zpřístupní k `contoso.com`, `www.contoso.com`, a `staging.www.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-143">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="f2ebe-144">Cookie.HttpOnly</span><span class="sxs-lookup"><span data-stu-id="f2ebe-144">Cookie.HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-145">Příznak označující, pokud by měl být soubor cookie přístupný jenom pro servery.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-145">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="f2ebe-146">Změna této hodnoty na `false` povoluje skripty na straně klienta pro přístup k souboru cookie a může otevřít aplikaci tak, aby krádež souborů cookie by měl mít vaše aplikace [skriptování napříč weby (XSS)](xref:security/cross-site-scripting) ohrožení zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-146">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="f2ebe-147">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-147">The default value is `true`.</span></span> |
| [<span data-ttu-id="f2ebe-148">Cookie.Name</span><span class="sxs-lookup"><span data-stu-id="f2ebe-148">Cookie.Name</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-149">Nastaví název souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-149">Sets the name of the cookie.</span></span> |
| [<span data-ttu-id="f2ebe-150">Cookie.Path</span><span class="sxs-lookup"><span data-stu-id="f2ebe-150">Cookie.Path</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-151">Použít k izolaci aplikací spuštěných na stejný název hostitele.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-151">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="f2ebe-152">Pokud máte aplikaci spuštěnou na `/app1` a chcete omezit souborů cookie do této aplikace, nastavte `CookiePath` vlastnost `/app1`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-152">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="f2ebe-153">Díky tomu souboru cookie, který je k dispozici pouze u požadavků na `/app1` a všechny aplikace pod ním.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-153">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="f2ebe-154">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="f2ebe-154">Cookie.SameSite</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-155">Určuje, zda v prohlížeči by měl povolit soubory cookie bude připojený na stejný web požadavky jenom (`SameSiteMode.Strict`) nebo pomocí bezpečných metod HTTP a požadavky na stejný web požadavky mezi weby (`SameSiteMode.Lax`).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-155">Indicates whether the browser should allow the cookie to be attached to same-site requests only (`SameSiteMode.Strict`) or cross-site requests using safe HTTP methods and same-site requests (`SameSiteMode.Lax`).</span></span> <span data-ttu-id="f2ebe-156">Pokud je nastavena na `SameSiteMode.None`, není-li nastavena hodnota hlavičky souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-156">When set to `SameSiteMode.None`, the cookie header value isn't set.</span></span> <span data-ttu-id="f2ebe-157">Všimněte si, že [middlewaru souboru Cookie. zásady](#cookie-policy-middleware) mohou přepsat hodnotu, kterou zadáte.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-157">Note that [Cookie Policy Middleware](#cookie-policy-middleware) might overwrite the value that you provide.</span></span> <span data-ttu-id="f2ebe-158">Pro podporu ověřování OAuth, výchozí hodnota je `SameSiteMode.Lax`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-158">To support OAuth authentication, the default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="f2ebe-159">Další informace najdete v tématu [nefunkční kvůli zásadám SameSite souboru cookie ověřování OAuth](https://github.com/aspnet/Security/issues/1231).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-159">For more information, see [OAuth authentication broken due to SameSite cookie policy](https://github.com/aspnet/Security/issues/1231).</span></span> |
| [<span data-ttu-id="f2ebe-160">Cookie.SecurePolicy</span><span class="sxs-lookup"><span data-stu-id="f2ebe-160">Cookie.SecurePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-161">Příznak označující, pokud by měl být soubor cookie vytvořený omezené na protokol HTTPS (`CookieSecurePolicy.Always`), HTTP nebo HTTPS (`CookieSecurePolicy.None`), nebo stejný protokol jako požadavek (`CookieSecurePolicy.SameAsRequest`).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-161">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="f2ebe-162">Výchozí hodnota je `CookieSecurePolicy.SameAsRequest`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-162">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="f2ebe-163">DataProtectionProvider</span><span class="sxs-lookup"><span data-stu-id="f2ebe-163">DataProtectionProvider</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-164">Nastaví `DataProtectionProvider` , který slouží k vytvoření výchozích `TicketDataFormat`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-164">Sets the `DataProtectionProvider` that's used to create the default `TicketDataFormat`.</span></span> <span data-ttu-id="f2ebe-165">Pokud `TicketDataFormat` je vlastnost nastavena, `DataProtectionProvider` není použita možnost.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-165">If the `TicketDataFormat` property is set, the `DataProtectionProvider` option isn't used.</span></span> <span data-ttu-id="f2ebe-166">Pokud se nezadá, výchozí zprostředkovatel ochrany dat aplikace se používá.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-166">If not provided, the app's default data protection provider is used.</span></span> |
| [<span data-ttu-id="f2ebe-167">Události</span><span class="sxs-lookup"><span data-stu-id="f2ebe-167">Events</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-168">Obslužná rutina volá metody zprostředkovatele, který poskytují kontrolu aplikace v určitých bodech, zpracování.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-168">The handler calls methods on the provider that give the app control at certain processing points.</span></span> <span data-ttu-id="f2ebe-169">Pokud `Events` nejsou zadaná, je použita výchozí instance, který nemá žádný účinek, pokud jsou volány metody.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-169">If `Events` aren't provided, a default instance is supplied that does nothing when the methods are called.</span></span> |
| [<span data-ttu-id="f2ebe-170">EventsType</span><span class="sxs-lookup"><span data-stu-id="f2ebe-170">EventsType</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-171">Použít jako typ služby k získání `Events` instanci místo vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-171">Used as the service type to get the `Events` instance instead of the property.</span></span> |
| [<span data-ttu-id="f2ebe-172">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="f2ebe-172">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-173">`TimeSpan` Po kterém vyprší platnost lístku ověřování uložený v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-173">The `TimeSpan` after which the authentication ticket stored inside the cookie expires.</span></span> <span data-ttu-id="f2ebe-174">`ExpireTimeSpan` je přidán do aktuální čas při vytváření dobu vypršení platnosti-the-ticket.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-174">`ExpireTimeSpan` is added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="f2ebe-175">`ExpiredTimeSpan` Hodnota vždy přejde do šifrovaného AuthTicket ověřit server.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-175">The `ExpiredTimeSpan` value always goes into the encrypted AuthTicket verified by the server.</span></span> <span data-ttu-id="f2ebe-176">Může taky přejít do [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) záhlaví, ale pouze v případě `IsPersistent` nastavena.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-176">It may also go into the [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) header, but only if `IsPersistent` is set.</span></span> <span data-ttu-id="f2ebe-177">Nastavit `IsPersistent` k `true`, nakonfigurujte [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) předán `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-177">To set `IsPersistent` to `true`, configure the [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) passed to `SignInAsync`.</span></span> <span data-ttu-id="f2ebe-178">Výchozí hodnota `ExpireTimeSpan` je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-178">The default value of `ExpireTimeSpan` is 14 days.</span></span> |
| [<span data-ttu-id="f2ebe-179">LoginPath</span><span class="sxs-lookup"><span data-stu-id="f2ebe-179">LoginPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-180">Určuje cestu k poskytování s 302 Found (adresa URL přesměrování) aktivovaného `HttpContext.ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-180">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ChallengeAsync`.</span></span> <span data-ttu-id="f2ebe-181">Aktuální adresa URL, které vygenerovalo 401 je přidán do `LoginPath` jako parametru řetězce dotazu s názvem podle `ReturnUrlParameter`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-181">The current URL that generated the 401 is added to the `LoginPath` as a query string parameter named by the `ReturnUrlParameter`.</span></span> <span data-ttu-id="f2ebe-182">Jakmile požadavek na `LoginPath` udělí novou identitu přihlášení, `ReturnUrlParameter` hodnota slouží k přesměrování prohlížeče zpět na adresu URL, která způsobila původní kód neautorizovaného stavu.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-182">Once a request to the `LoginPath` grants a new sign-in identity, the `ReturnUrlParameter` value is used to redirect the browser back to the URL that caused the original unauthorized status code.</span></span> <span data-ttu-id="f2ebe-183">Výchozí hodnota je `/Account/Login`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-183">The default value is `/Account/Login`.</span></span> |
| [<span data-ttu-id="f2ebe-184">LogoutPath</span><span class="sxs-lookup"><span data-stu-id="f2ebe-184">LogoutPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-185">Pokud `LogoutPath` je k dispozici pro obslužnou rutinu, pak přesměruje požadavek na tuto cestu podle hodnoty `ReturnUrlParameter`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-185">If the `LogoutPath` is provided to the handler, then a request to that path redirects based on the value of the `ReturnUrlParameter`.</span></span> <span data-ttu-id="f2ebe-186">Výchozí hodnota je `/Account/Logout`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-186">The default value is `/Account/Logout`.</span></span> |
| [<span data-ttu-id="f2ebe-187">ReturnUrlParameter</span><span class="sxs-lookup"><span data-stu-id="f2ebe-187">ReturnUrlParameter</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-188">Určuje název parametru řetězce dotazu, který se připojí obslužnou rutinou pro odpovědi 302 Found (adresa URL přesměrování).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-188">Determines the name of the query string parameter that's appended by the handler for a 302 Found (URL redirect) response.</span></span> <span data-ttu-id="f2ebe-189">`ReturnUrlParameter` se používá, když dorazí požadavek na `LoginPath` nebo `LogoutPath` do prohlížeče po provedení akce přihlašovací nebo odhlašovací vraťte k původní adresu URL.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-189">`ReturnUrlParameter` is used when a request arrives on the `LoginPath` or `LogoutPath` to return the browser to the original URL after the login or logout action is performed.</span></span> <span data-ttu-id="f2ebe-190">Výchozí hodnota je `ReturnUrl`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-190">The default value is `ReturnUrl`.</span></span> |
| [<span data-ttu-id="f2ebe-191">SessionStore</span><span class="sxs-lookup"><span data-stu-id="f2ebe-191">SessionStore</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-192">Volitelný kontejner používá k ukládání identit napříč požadavky.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-192">An optional container used to store identity across requests.</span></span> <span data-ttu-id="f2ebe-193">Když se použije, jenom identifikátor relace je odeslat klientovi.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-193">When used, only a session identifier is sent to the client.</span></span> <span data-ttu-id="f2ebe-194">`SessionStore` je možné zmírnit potenciální potíže s velkými identitami.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-194">`SessionStore` can be used to mitigate potential problems with large identities.</span></span> |
| [<span data-ttu-id="f2ebe-195">parametr slidingExpiration</span><span class="sxs-lookup"><span data-stu-id="f2ebe-195">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-196">Příznak označující, pokud se nový soubor cookie s čas vypršení platnosti aktualizace by měla být vydána dynamicky.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-196">A flag indicating if a new cookie with an updated expiration time should be issued dynamically.</span></span> <span data-ttu-id="f2ebe-197">To může nastat na všechny požadavky, pokud aktuální období vypršení platnosti souboru cookie je více než 50 % vypršela platnost.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-197">This can happen on any request where the current cookie expiration period is more than 50% expired.</span></span> <span data-ttu-id="f2ebe-198">Nové datum vypršení platnosti bude přesunut dopředu na aktuální datum plus `ExpireTimespan`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-198">The new expiration date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="f2ebe-199">[Čas vypršení platnosti souboru cookie absolutní](xref:security/authentication/cookie#absolute-cookie-expiration) můžete nastavit pomocí `AuthenticationProperties` třídy při volání metody `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-199">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="f2ebe-200">Čas absolutní vypršení platnosti můžete zlepšit zabezpečení vaší aplikace tím, že omezíte množství času, který je platný soubor cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-200">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="f2ebe-201">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-201">The default value is `true`.</span></span> |
| [<span data-ttu-id="f2ebe-202">TicketDataFormat</span><span class="sxs-lookup"><span data-stu-id="f2ebe-202">TicketDataFormat</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-203">`TicketDataFormat` Se používá k ochraně a zrušení této ochrany identity a dalších vlastností, které jsou uloženy v hodnotě souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-203">The `TicketDataFormat` is used to protect and unprotect the identity and other properties that are stored in the cookie value.</span></span> <span data-ttu-id="f2ebe-204">Pokud se nezadá, `TicketDataFormat` je vytvořený pomocí [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-204">If not provided, a `TicketDataFormat` is created using the [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0).</span></span> |
| [<span data-ttu-id="f2ebe-205">Ověření</span><span class="sxs-lookup"><span data-stu-id="f2ebe-205">Validate</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | <span data-ttu-id="f2ebe-206">Metoda, která kontroluje, že možnosti jsou platné.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-206">Method that checks that the options are valid.</span></span> |

<span data-ttu-id="f2ebe-207">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-207">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="f2ebe-208">Soubor cookie používá ASP.NET Core 1.x [middleware](xref:fundamentals/middleware/index) hlavní název uživatele, který serializuje do zašifrovaného souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-208">ASP.NET Core 1.x uses cookie [middleware](xref:fundamentals/middleware/index) that serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="f2ebe-209">Na následné žádosti soubor cookie se ověří a znovu vytvořit a přiřadit k objektu zabezpečení `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-209">On subsequent requests, the cookie is validated, and the principal is recreated and assigned to the `HttpContext.User` property.</span></span>

<span data-ttu-id="f2ebe-210">Nainstalujte [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) balíčku NuGet ve vašem projektu.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-210">Install the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) NuGet package in your project.</span></span> <span data-ttu-id="f2ebe-211">Tento balíček obsahuje middlewaru souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-211">This package contains the cookie middleware.</span></span>

<span data-ttu-id="f2ebe-212">Použití `UseCookieAuthentication` metoda ve `Configure` metoda ve vaší *Startup.cs* souboru před `UseMvc` nebo `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-212">Use the `UseCookieAuthentication` method in the `Configure` method in your *Startup.cs* file before `UseMvc` or `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AccessDeniedPath = "/Account/Forbidden/",
    AuthenticationScheme = CookieAuthenticationDefaults.AuthenticationScheme,
    AutomaticAuthenticate = true,
    AutomaticChallenge = true,
    LoginPath = "/Account/Unauthorized/"
});
```

<span data-ttu-id="f2ebe-213">**Možnosti CookieAuthenticationOptions**</span><span class="sxs-lookup"><span data-stu-id="f2ebe-213">**CookieAuthenticationOptions Options**</span></span>

<span data-ttu-id="f2ebe-214">[CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) třída se používá ke konfiguraci možností zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-214">The [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="f2ebe-215">Možnost</span><span class="sxs-lookup"><span data-stu-id="f2ebe-215">Option</span></span> | <span data-ttu-id="f2ebe-216">Popis</span><span class="sxs-lookup"><span data-stu-id="f2ebe-216">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="f2ebe-217">AuthenticationScheme</span><span class="sxs-lookup"><span data-stu-id="f2ebe-217">AuthenticationScheme</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | <span data-ttu-id="f2ebe-218">Nastaví schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-218">Sets the authentication scheme.</span></span> <span data-ttu-id="f2ebe-219">`AuthenticationScheme` je užitečné, pokud existuje více instancí ověřování a vy chcete [autorizovat s konkrétní schéma](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-219">`AuthenticationScheme` is useful when there are multiple instances of authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="f2ebe-220">Nastavení `AuthenticationScheme` k `CookieAuthenticationDefaults.AuthenticationScheme` poskytuje hodnotu "Soubory cookie" pro schéma.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-220">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="f2ebe-221">Můžete zadat libovolnou hodnotu řetězce, která odlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-221">You can supply any string value that distinguishes the scheme.</span></span> |
| [<span data-ttu-id="f2ebe-222">AutomaticAuthenticate</span><span class="sxs-lookup"><span data-stu-id="f2ebe-222">AutomaticAuthenticate</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | <span data-ttu-id="f2ebe-223">Nastaví hodnotu označující, že ověřování souborů cookie by měl spustit u každého požadavku a pokusí se ověřit a proveďte rekonstrukci serializovaný objekt zabezpečení, vytvořená.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-223">Sets a value to indicate that the cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span> |
| [<span data-ttu-id="f2ebe-224">AutomaticChallenge</span><span class="sxs-lookup"><span data-stu-id="f2ebe-224">AutomaticChallenge</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | <span data-ttu-id="f2ebe-225">Při hodnotě true se zpracovává middleware ověřování automatické výzvy.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-225">If true, the authentication middleware handles automatic challenges.</span></span> <span data-ttu-id="f2ebe-226">Pokud false, middleware ověřování pouze změní odpovědi, když explicitně `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-226">If false, the authentication middleware only alters responses when explicitly indicated by the `AuthenticationScheme`.</span></span> |
| [<span data-ttu-id="f2ebe-227">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="f2ebe-227">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | <span data-ttu-id="f2ebe-228">Vystavitel, který má použít pro [vystavitele](/dotnet/api/system.security.claims.claim.issuer) vlastnost na všechny deklarace vytvořené middleware ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-228">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication middleware.</span></span> |
| [<span data-ttu-id="f2ebe-229">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="f2ebe-229">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | <span data-ttu-id="f2ebe-230">Název domény, kde je soubor cookie obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-230">The domain name where the cookie is served.</span></span> <span data-ttu-id="f2ebe-231">Ve výchozím nastavení toto je název hostitele žádosti.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-231">By default, this is the host name of the request.</span></span> <span data-ttu-id="f2ebe-232">Prohlížeč slouží pouze souboru cookie, který má odpovídající název hostitele.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-232">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="f2ebe-233">Můžete chtít upravit tak, aby soubory cookie, které jsou k dispozici pro všechny hostitele ve vaší doméně.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-233">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="f2ebe-234">Například nastavení domény souboru cookie na `.contoso.com` ji zpřístupní k `contoso.com`, `www.contoso.com`, a `staging.www.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-234">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="f2ebe-235">CookieHttpOnly</span><span class="sxs-lookup"><span data-stu-id="f2ebe-235">CookieHttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | <span data-ttu-id="f2ebe-236">Příznak označující, pokud by měl být soubor cookie přístupný jenom pro servery.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-236">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="f2ebe-237">Změna této hodnoty na `false` povoluje skripty na straně klienta pro přístup k souboru cookie a může otevřít aplikaci tak, aby krádež souborů cookie by měl mít vaše aplikace [skriptování napříč weby (XSS)](xref:security/cross-site-scripting) ohrožení zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-237">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="f2ebe-238">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-238">The default value is `true`.</span></span> |
| [<span data-ttu-id="f2ebe-239">CookiePath</span><span class="sxs-lookup"><span data-stu-id="f2ebe-239">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | <span data-ttu-id="f2ebe-240">Použít k izolaci aplikací spuštěných na stejný název hostitele.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-240">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="f2ebe-241">Pokud máte aplikaci spuštěnou na `/app1` a chcete omezit souborů cookie do této aplikace, nastavte `CookiePath` vlastnost `/app1`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-241">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="f2ebe-242">Díky tomu souboru cookie, který je k dispozici pouze u požadavků na `/app1` a všechny aplikace pod ním.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-242">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="f2ebe-243">CookieSecure</span><span class="sxs-lookup"><span data-stu-id="f2ebe-243">CookieSecure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | <span data-ttu-id="f2ebe-244">Příznak označující, pokud by měl být soubor cookie vytvořený omezené na protokol HTTPS (`CookieSecurePolicy.Always`), HTTP nebo HTTPS (`CookieSecurePolicy.None`), nebo stejný protokol jako požadavek (`CookieSecurePolicy.SameAsRequest`).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-244">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="f2ebe-245">Výchozí hodnota je `CookieSecurePolicy.SameAsRequest`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-245">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="f2ebe-246">Popis</span><span class="sxs-lookup"><span data-stu-id="f2ebe-246">Description</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | <span data-ttu-id="f2ebe-247">Další informace o typu ověřování, který je k dispozici pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-247">Additional information about the authentication type which is made available to the app.</span></span> |
| [<span data-ttu-id="f2ebe-248">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="f2ebe-248">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | <span data-ttu-id="f2ebe-249">`TimeSpan` Po kterém vyprší platnost lístku ověřování.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-249">The `TimeSpan` after which the authentication ticket expires.</span></span> <span data-ttu-id="f2ebe-250">Přidá se do aktuálního času vytvoření dobu vypršení platnosti-the-ticket.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-250">It's added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="f2ebe-251">Použití `ExpireTimeSpan`, je nutné nastavit `IsPersistent` k `true` v `AuthenticationProperties` předán `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-251">To use `ExpireTimeSpan`, you must set `IsPersistent` to `true` in the `AuthenticationProperties` passed to `SignInAsync`.</span></span> <span data-ttu-id="f2ebe-252">Výchozí hodnota je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-252">The default value is 14 days.</span></span> |
| [<span data-ttu-id="f2ebe-253">parametr slidingExpiration</span><span class="sxs-lookup"><span data-stu-id="f2ebe-253">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | <span data-ttu-id="f2ebe-254">Příznak označující, zda datum vypršení platnosti souboru cookie resetuje při více než polovina `ExpireTimeSpan` uplynutí intervalu.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-254">A flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="f2ebe-255">Nový čas vypršení platnosti bude přesunut dopředu na aktuální datum plus `ExpireTimespan`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-255">The new expiration time is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="f2ebe-256">[Čas vypršení platnosti souboru cookie absolutní](xref:security/authentication/cookie#absolute-cookie-expiration) můžete nastavit pomocí `AuthenticationProperties` třídy při volání metody `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-256">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="f2ebe-257">Čas absolutní vypršení platnosti můžete zlepšit zabezpečení vaší aplikace tím, že omezíte množství času, který je platný soubor cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-257">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="f2ebe-258">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-258">The default value is `true`.</span></span> |

<span data-ttu-id="f2ebe-259">Nastavte `CookieAuthenticationOptions` pro Middleware ověřování souborů Cookie v `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-259">Set `CookieAuthenticationOptions` for the Cookie Authentication Middleware in the `Configure` method:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

::: moniker-end

## <a name="cookie-policy-middleware"></a><span data-ttu-id="f2ebe-260">Zásady middlewaru souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-260">Cookie Policy Middleware</span></span>

<span data-ttu-id="f2ebe-261">[Zásady middlewaru souboru cookie](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) umožňuje možnosti zásad souborů cookie v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-261">[Cookie Policy Middleware](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) enables cookie policy capabilities in an app.</span></span> <span data-ttu-id="f2ebe-262">Přidání middleware do kanálu zpracování aplikace je v pořadí citlivé; ovlivňuje pouze komponenty zaregistrovaný v kanálu po něm.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-262">Adding the middleware to the app processing pipeline is order sensitive; it only affects components registered after it in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 <span data-ttu-id="f2ebe-263">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) poskytnuté middlewaru souboru Cookie. zásady umožňují určit globální vlastnosti zpracování souboru cookie a hook do obslužné rutiny zpracování souboru cookie, kdy jsou soubory cookie připojí nebo odstraní.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-263">The [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) provided to the Cookie Policy Middleware allow you to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

| <span data-ttu-id="f2ebe-264">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="f2ebe-264">Property</span></span> | <span data-ttu-id="f2ebe-265">Popis</span><span class="sxs-lookup"><span data-stu-id="f2ebe-265">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="f2ebe-266">HttpOnly</span><span class="sxs-lookup"><span data-stu-id="f2ebe-266">HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | <span data-ttu-id="f2ebe-267">Ovlivňuje, jestli HttpOnly musí být soubory cookie, který je příznak označující, pokud by měl být soubor cookie přístupný jenom pro servery.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-267">Affects whether cookies must be HttpOnly, which is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="f2ebe-268">Výchozí hodnota je `HttpOnlyPolicy.None`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-268">The default value is `HttpOnlyPolicy.None`.</span></span> |
| [<span data-ttu-id="f2ebe-269">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="f2ebe-269">MinimumSameSitePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | <span data-ttu-id="f2ebe-270">Má vliv na stejný web atribut souboru cookie (viz níže).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-270">Affects the cookie's same-site attribute (see below).</span></span> <span data-ttu-id="f2ebe-271">Výchozí hodnota je `SameSiteMode.Lax`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-271">The default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="f2ebe-272">Tato možnost je dostupná pro ASP.NET Core 2.0 +.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-272">This option is available for ASP.NET Core 2.0+.</span></span> |
| [<span data-ttu-id="f2ebe-273">OnAppendCookie</span><span class="sxs-lookup"><span data-stu-id="f2ebe-273">OnAppendCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | <span data-ttu-id="f2ebe-274">Volá se, když je přidán do souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-274">Called when a cookie is appended.</span></span> |
| [<span data-ttu-id="f2ebe-275">OnDeleteCookie</span><span class="sxs-lookup"><span data-stu-id="f2ebe-275">OnDeleteCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | <span data-ttu-id="f2ebe-276">Volá se, když se odstraní soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-276">Called when a cookie is deleted.</span></span> |
| [<span data-ttu-id="f2ebe-277">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="f2ebe-277">Secure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | <span data-ttu-id="f2ebe-278">Ovlivňuje, jestli soubory cookie musí být zabezpečené.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-278">Affects whether cookies must be Secure.</span></span> <span data-ttu-id="f2ebe-279">Výchozí hodnota je `CookieSecurePolicy.None`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-279">The default value is `CookieSecurePolicy.None`.</span></span> |

<span data-ttu-id="f2ebe-280">**MinimumSameSitePolicy** (ASP.NET Core 2.0 + jenom)</span><span class="sxs-lookup"><span data-stu-id="f2ebe-280">**MinimumSameSitePolicy** (ASP.NET Core 2.0+ only)</span></span>

<span data-ttu-id="f2ebe-281">Výchozí hodnota `MinimumSameSitePolicy` hodnotu `SameSiteMode.Lax` tak, aby povolovala ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-281">The default `MinimumSameSitePolicy` value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="f2ebe-282">Přísně vynutit zásady stejný web `SameSiteMode.Strict`, nastavte `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-282">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="f2ebe-283">I když toto nastavení přeruší OAuth2 nebo jiná schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souboru cookie pro ostatní typy aplikací, které se nemusíte spoléhat na zpracování žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-283">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="f2ebe-284">Nastavení zásad middlewaru souboru Cookie pro `MinimumSameSitePolicy` může mít vliv na vaše nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle níže uvedených matice.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-284">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect your setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="f2ebe-285">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="f2ebe-285">MinimumSameSitePolicy</span></span> | <span data-ttu-id="f2ebe-286">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="f2ebe-286">Cookie.SameSite</span></span> | <span data-ttu-id="f2ebe-287">Výsledná nastavení Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="f2ebe-287">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="f2ebe-288">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f2ebe-288">SameSiteMode.None</span></span>     | <span data-ttu-id="f2ebe-289">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f2ebe-289">SameSiteMode.None</span></span><br><span data-ttu-id="f2ebe-290">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f2ebe-290">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2ebe-291">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f2ebe-291">SameSiteMode.Strict</span></span> | <span data-ttu-id="f2ebe-292">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f2ebe-292">SameSiteMode.None</span></span><br><span data-ttu-id="f2ebe-293">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f2ebe-293">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2ebe-294">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f2ebe-294">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f2ebe-295">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f2ebe-295">SameSiteMode.Lax</span></span>      | <span data-ttu-id="f2ebe-296">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f2ebe-296">SameSiteMode.None</span></span><br><span data-ttu-id="f2ebe-297">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f2ebe-297">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2ebe-298">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f2ebe-298">SameSiteMode.Strict</span></span> | <span data-ttu-id="f2ebe-299">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f2ebe-299">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2ebe-300">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f2ebe-300">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2ebe-301">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f2ebe-301">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="f2ebe-302">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f2ebe-302">SameSiteMode.Strict</span></span>   | <span data-ttu-id="f2ebe-303">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="f2ebe-303">SameSiteMode.None</span></span><br><span data-ttu-id="f2ebe-304">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="f2ebe-304">SameSiteMode.Lax</span></span><br><span data-ttu-id="f2ebe-305">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f2ebe-305">SameSiteMode.Strict</span></span> | <span data-ttu-id="f2ebe-306">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f2ebe-306">SameSiteMode.Strict</span></span><br><span data-ttu-id="f2ebe-307">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f2ebe-307">SameSiteMode.Strict</span></span><br><span data-ttu-id="f2ebe-308">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="f2ebe-308">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="f2ebe-309">Vytvoření souboru cookie pro ověřování</span><span class="sxs-lookup"><span data-stu-id="f2ebe-309">Create an authentication cookie</span></span>

<span data-ttu-id="f2ebe-310">Pokud chcete vytvořit soubor cookie, která uchovává informace o uživateli, je nutné vytvořit [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-310">To create a cookie holding user information, you must construct a [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal).</span></span> <span data-ttu-id="f2ebe-311">Informace o uživateli je serializován a uložená v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-311">The user information is serialized and stored in the cookie.</span></span> 

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f2ebe-312">Vytvoření [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) s jakékoli požadované [deklarace identity](/dotnet/api/system.security.claims.claim)s a volání [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) k přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-312">Create a [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) with any required [Claim](/dotnet/api/system.security.claims.claim)s and call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="f2ebe-313">Volání [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) k přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-313">Call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) to sign in the user:</span></span>

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker-end

<span data-ttu-id="f2ebe-314">`SignInAsync` Vytvoří do zašifrovaného souboru cookie a přidá jej do aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-314">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="f2ebe-315">Pokud nezadáte `AuthenticationScheme`, se používá výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-315">If you don't specify an `AuthenticationScheme`, the default scheme is used.</span></span>

<span data-ttu-id="f2ebe-316">Pod pokličkou, je šifrování, které používá ASP.NET Core [ochranu dat](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) systému.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-316">Under the covers, the encryption used is ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) system.</span></span> <span data-ttu-id="f2ebe-317">Pokud se hostují aplikaci na více počítačů, Vyrovnávání zatížení napříč aplikacemi nebo pomocí webové farmy, je nutné [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview) používat stejné aktualizační kanál klíč a identifikátor aplikace.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-317">If you're hosting app on multiple machines, load balancing across apps, or using a web farm, then you must [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="f2ebe-318">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="f2ebe-318">Sign out</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f2ebe-319">Chcete-li odhlásit aktuálního uživatele a odstranit jejich souborů cookie, zavolejte [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span><span class="sxs-lookup"><span data-stu-id="f2ebe-319">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="f2ebe-320">Chcete-li odhlásit aktuálního uživatele a odstranit jejich souborů cookie, zavolejte [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span><span class="sxs-lookup"><span data-stu-id="f2ebe-320">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span></span>

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

::: moniker-end

<span data-ttu-id="f2ebe-321">Pokud nepoužíváte `CookieAuthenticationDefaults.AuthenticationScheme` (nebo "Soubory cookie") jako schéma (například "ContosoCookie"), zadejte schéma, které jste použili při konfiguraci zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-321">If you aren't using `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") as the scheme (for example, "ContosoCookie"), supply the scheme you used when configuring the authentication provider.</span></span> <span data-ttu-id="f2ebe-322">V opačném případě se používá výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-322">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="f2ebe-323">Reagovat na změny back-end</span><span class="sxs-lookup"><span data-stu-id="f2ebe-323">React to back-end changes</span></span>

<span data-ttu-id="f2ebe-324">Po vytvoření souboru cookie, bude jediný zdroj identity.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-324">Once a cookie is created, it becomes the single source of identity.</span></span> <span data-ttu-id="f2ebe-325">I v případě, že zakázání uživatele v back endových systémů, systém ověřování souborů cookie je vůbec nezná to a uživatel zůstane přihlášený jako jejich soubor cookie je neplatný.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-325">Even if you disable a user in your back-end systems, the cookie authentication system has no knowledge of this, and a user stays logged in as long as their cookie is valid.</span></span>

<span data-ttu-id="f2ebe-326">[ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) událost v ASP.NET Core 2.x nebo [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) metoda v ASP.NET Core 1.x lze zachytit a přepsat ověření souboru cookie identity.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-326">The [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) event in ASP.NET Core 2.x or the [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) method in ASP.NET Core 1.x can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="f2ebe-327">Tento přístup snižuje riziko odvolané uživatele, kteří používají aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-327">This approach mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="f2ebe-328">Jedním z přístupů k ověření souboru cookie je založená na udržování přehledu o při změně uživatelskou databázi.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-328">One approach to cookie validation is based on keeping track of when the user database has been changed.</span></span> <span data-ttu-id="f2ebe-329">Pokud databáze nebylo změněno od uživatele soubor cookie vydala, není nutné opakované ověření uživatele, pokud jejich souborů cookie je stále platný.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-329">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="f2ebe-330">Databáze, které je implementované v tomto scénáři implementovat `IUserRepository` v tomto příkladu ukládá `LastChanged` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-330">To implement this scenario, the database, which is implemented in `IUserRepository` for this example, stores a `LastChanged` value.</span></span> <span data-ttu-id="f2ebe-331">Když se aktualizuje každý uživatel v databázi, `LastChanged` je hodnota nastavena na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-331">When any user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="f2ebe-332">Pokud chcete platnost souboru cookie, pokud změny databáze v závislosti na `LastChanged` hodnoty, vytvořte soubor cookie s `LastChanged` deklarace obsahující aktuální `LastChanged` hodnota z databáze:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-332">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="f2ebe-333">K implementaci přepsání `ValidatePrincipal` události, napište metodu s následující signaturou ve třídě, který je odvozen z [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span><span class="sxs-lookup"><span data-stu-id="f2ebe-333">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that you derive from [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="f2ebe-334">Příklad bude vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-334">An example looks like the following:</span></span>

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

<span data-ttu-id="f2ebe-335">Zaregistrovat instanci události během registrace služby souborů cookie v `ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-335">Register the events instance during cookie service registration in the `ConfigureServices` method.</span></span> <span data-ttu-id="f2ebe-336">Registraci vymezené služby pro vaše `CustomCookieAuthenticationEvents` třídy:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-336">Provide a scoped service registration for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="f2ebe-337">K implementaci přepsání `ValidateAsync` události, napište metodu s následující signaturou:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-337">To implement an override for the `ValidateAsync` event, write a method with the following signature:</span></span>

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

<span data-ttu-id="f2ebe-338">ASP.NET Core Identity implementuje tato kontrola v rámci jeho [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync).</span><span class="sxs-lookup"><span data-stu-id="f2ebe-338">ASP.NET Core Identity implements this check as part of its [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync).</span></span> <span data-ttu-id="f2ebe-339">Příklad bude vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-339">An example looks like the following:</span></span>

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = 
            context.HttpContext.RequestServices
                .GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="f2ebe-340">Zaregistrujte události během konfigurace ověřování souborů cookie v `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="f2ebe-340">Register the event during cookie authentication configuration in the `Configure` method:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    Events = new CookieAuthenticationEvents
    {
        OnValidatePrincipal = LastChangedValidator.ValidateAsync
    }
});
```

::: moniker-end

<span data-ttu-id="f2ebe-341">Představte si situaci, ve kterém se aktualizuje uživatelské jméno &mdash; rozhodnutí, která nemá vliv na zabezpečení žádným způsobem.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-341">Consider a situation in which the user's name is updated &mdash; a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="f2ebe-342">Pokud chcete aktualizovat nedestruktivně hlavní název uživatele, zavolejte `context.ReplacePrincipal` a nastavit `context.ShouldRenew` vlastnost `true`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-342">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f2ebe-343">Tento přístup je zde popsáno, se aktivuje u každého požadavku.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-343">The approach described here is triggered on every request.</span></span> <span data-ttu-id="f2ebe-344">To může způsobit snížení výkonu velké aplikace.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-344">This can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="f2ebe-345">Trvalé soubory cookie</span><span class="sxs-lookup"><span data-stu-id="f2ebe-345">Persistent cookies</span></span>

<span data-ttu-id="f2ebe-346">Můžete chtít soubor cookie zachovat v rámci relací prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-346">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="f2ebe-347">Tato trvalost má být povoleno pouze explicitní uživatelské souhlas s "Zapamatovat" zaškrtávací políčko na přihlašovací nebo mechanismus podobný.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-347">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on login or a similar mechanism.</span></span> 

<span data-ttu-id="f2ebe-348">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který odolává prostřednictvím prohlížeče UZÁVĚRECH.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-348">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="f2ebe-349">Klouzavé vypršení platnosti nastavení jste dříve nakonfigurovali jsou zachované.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-349">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="f2ebe-350">Pokud se při zavření prohlížeče vyprší platnost souboru cookie, vymaže prohlížeči soubor cookie po jeho restartování.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-350">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="f2ebe-351">[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) třídy se nachází v `Microsoft.AspNetCore.Authentication` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-351">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) class resides in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

<span data-ttu-id="f2ebe-352">[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) třídy se nachází v `Microsoft.AspNetCore.Http.Authentication` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-352">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) class resides in the `Microsoft.AspNetCore.Http.Authentication` namespace.</span></span>

::: moniker-end

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="f2ebe-353">Soubor cookie absolutní vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="f2ebe-353">Absolute cookie expiration</span></span>

<span data-ttu-id="f2ebe-354">Můžete nastavit dobu vypršení platnosti absolutní s `ExpiresUtc`.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-354">You can set an absolute expiration time with `ExpiresUtc`.</span></span> <span data-ttu-id="f2ebe-355">K vytvoření trvalého souboru cookie, je nutné nastavit také `IsPersistent`; v opačném případě soubor cookie se vytvoří s životností založeného na relacích a může vypršet buď před nebo po lístek ověřování, který obsahuje.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-355">To create a persistent cookie, you must also set `IsPersistent`; otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="f2ebe-356">Když `ExpiresUtc` je nastavena na `SignInAsync`, přepíše hodnotu `ExpireTimeSpan` možnost `CookieAuthenticationOptions`, pokud nastavení.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-356">When `ExpiresUtc` is set on `SignInAsync`, it overrides the value of the `ExpireTimeSpan` option of `CookieAuthenticationOptions`, if set.</span></span>

<span data-ttu-id="f2ebe-357">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který má platnost po dobu 20 minut.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-357">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="f2ebe-358">To ignoruje klouzavé vypršení platnosti nastavení předtím nakonfigurovali.</span><span class="sxs-lookup"><span data-stu-id="f2ebe-358">This ignores any sliding expiration settings previously configured.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
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

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f2ebe-359">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f2ebe-359">Additional resources</span></span>

* [<span data-ttu-id="f2ebe-360">Ověřování 2.0 změny / migrace oznámení</span><span class="sxs-lookup"><span data-stu-id="f2ebe-360">Auth 2.0 Changes / Migration Announcement</span></span>](https://github.com/aspnet/Announcements/issues/262)
* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="f2ebe-361">Role na základě zásad kontroly</span><span class="sxs-lookup"><span data-stu-id="f2ebe-361">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

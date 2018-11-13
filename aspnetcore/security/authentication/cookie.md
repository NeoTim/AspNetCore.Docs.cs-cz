---
title: Ověřování souborem cookie bez ASP.NET Core Identity
author: rick-anderson
description: Vysvětlení, používání ověřování souborem cookie bez ASP.NET Core Identity
ms.author: riande
ms.date: 10/11/2017
uid: security/authentication/cookie
ms.openlocfilehash: f55b36cf3fc3b60e9d592348625f58ebaba90da7
ms.sourcegitcommit: 408921a932448f66cb46fd53c307a864f5323fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51570110"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="2d586-103">Ověřování souborem cookie bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2d586-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="2d586-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2d586-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2d586-105">Jak už víte, v předchozích tématech ověřování [ASP.NET Core Identity](xref:security/authentication/identity) je ověřování dokončeno, plně vybavené zprostředkovatele pro vytváření a správa přihlášení.</span><span class="sxs-lookup"><span data-stu-id="2d586-105">As you've seen in the earlier authentication topics, [ASP.NET Core Identity](xref:security/authentication/identity) is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="2d586-106">Můžete ale použít vlastní logiku ověřování vlastní soubor cookie s ověřováním pomocí čas od času.</span><span class="sxs-lookup"><span data-stu-id="2d586-106">However, you may want to use your own custom authentication logic with cookie-based authentication at times.</span></span> <span data-ttu-id="2d586-107">Ověřování na základě souborů cookie slouží jako zprostředkovatel ověřování samostatné bez ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="2d586-107">You can use cookie-based authentication as a standalone authentication provider without ASP.NET Core Identity.</span></span>

<span data-ttu-id="2d586-108">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2d586-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2d586-109">Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetické uživatele Marie Rodriguez pevně zakódované do aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d586-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="2d586-110">Použití uživatelského jména e-mailu "maria.rodriguez@contoso.com" a jakékoli heslo k přihlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="2d586-110">Use the Email username "maria.rodriguez@contoso.com" and any password to sign in the user.</span></span> <span data-ttu-id="2d586-111">Ověření uživatele v `AuthenticateUser` metodu *Pages/Account/Login.cshtml.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="2d586-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="2d586-112">V reálný příklad uživatel by ověřovány proti databázi.</span><span class="sxs-lookup"><span data-stu-id="2d586-112">In a real-world example, the user would be authenticated against a database.</span></span>

<span data-ttu-id="2d586-113">Informace o migraci na základě souboru cookie ověřování ASP.NET Core 1.x do 2.0, naleznete v tématu [migrovat ověřování a identita na téma ASP.NET Core 2.0 (ověřování na základě souborů Cookie)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).</span><span class="sxs-lookup"><span data-stu-id="2d586-113">For information on migrating cookie-based authentication from ASP.NET Core 1.x to 2.0, see [Migrate Authentication and Identity to ASP.NET Core 2.0 topic (Cookie-based Authentication)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).</span></span>

<span data-ttu-id="2d586-114">Použití ASP.NET Core Identity, najdete v článku [Úvod do Identity](xref:security/authentication/identity) tématu.</span><span class="sxs-lookup"><span data-stu-id="2d586-114">To use ASP.NET Core Identity, see the [Introduction to Identity](xref:security/authentication/identity) topic.</span></span>

## <a name="configuration"></a><span data-ttu-id="2d586-115">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="2d586-115">Configuration</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="2d586-116">Pokud se aplikace nepoužívá [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), vytvořit odkaz na balíček v souboru projektu [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) balíčku (verze 2.1.0 nebo později).</span><span class="sxs-lookup"><span data-stu-id="2d586-116">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package (version 2.1.0 or later).</span></span>

<span data-ttu-id="2d586-117">V `ConfigureServices` metody vytvoření Middleware ověřování služby s `AddAuthentication` a `AddCookie` metody:</span><span class="sxs-lookup"><span data-stu-id="2d586-117">In the `ConfigureServices` method, create the Authentication Middleware service with the `AddAuthentication` and `AddCookie` methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="2d586-118">`AuthenticationScheme` Předaný `AddAuthentication` nastaví výchozí schéma ověřování pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2d586-118">`AuthenticationScheme` passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="2d586-119">`AuthenticationScheme` je užitečné, pokud existuje více instancí ověřování souborů cookie a vy chcete [autorizovat s konkrétní schéma](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="2d586-119">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="2d586-120">Nastavení `AuthenticationScheme` k `CookieAuthenticationDefaults.AuthenticationScheme` poskytuje hodnotu "Soubory cookie" pro schéma.</span><span class="sxs-lookup"><span data-stu-id="2d586-120">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="2d586-121">Můžete zadat libovolnou hodnotu řetězce, která odlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="2d586-121">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="2d586-122">Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2d586-122">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="2d586-123">Pokud není k dispozici schéma ověřování souborů cookie do <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, používá [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (soubory cookie.").</span><span class="sxs-lookup"><span data-stu-id="2d586-123">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies").</span></span>

<span data-ttu-id="2d586-124">V `Configure` metody, použijte `UseAuthentication` metoda k vyvolání ověřovací Middleware, který nastaví `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="2d586-124">In the `Configure` method, use the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="2d586-125">Volání `UseAuthentication` před voláním metody `UseMvcWithDefaultRoute` nebo `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="2d586-125">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="2d586-126">**Možnosti AddCookie**</span><span class="sxs-lookup"><span data-stu-id="2d586-126">**AddCookie Options**</span></span>

<span data-ttu-id="2d586-127">[CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) třída se používá ke konfiguraci možností zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="2d586-127">The [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="2d586-128">Možnost</span><span class="sxs-lookup"><span data-stu-id="2d586-128">Option</span></span> | <span data-ttu-id="2d586-129">Popis</span><span class="sxs-lookup"><span data-stu-id="2d586-129">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="2d586-130">AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="2d586-130">AccessDeniedPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | <span data-ttu-id="2d586-131">Určuje cestu k poskytování s 302 Found (adresa URL přesměrování) aktivovaného `HttpContext.ForbidAsync`.</span><span class="sxs-lookup"><span data-stu-id="2d586-131">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ForbidAsync`.</span></span> <span data-ttu-id="2d586-132">Výchozí hodnota je `/Account/AccessDenied`.</span><span class="sxs-lookup"><span data-stu-id="2d586-132">The default value is `/Account/AccessDenied`.</span></span> |
| [<span data-ttu-id="2d586-133">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="2d586-133">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | <span data-ttu-id="2d586-134">Vystavitel, který má použít pro [vystavitele](/dotnet/api/system.security.claims.claim.issuer) vlastnost na všechny deklarace vytvořené službou ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-134">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication service.</span></span> |
| [<span data-ttu-id="2d586-135">Cookie.Domain</span><span class="sxs-lookup"><span data-stu-id="2d586-135">Cookie.Domain</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | <span data-ttu-id="2d586-136">Název domény, kde je soubor cookie obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="2d586-136">The domain name where the cookie is served.</span></span> <span data-ttu-id="2d586-137">Ve výchozím nastavení toto je název hostitele žádosti.</span><span class="sxs-lookup"><span data-stu-id="2d586-137">By default, this is the host name of the request.</span></span> <span data-ttu-id="2d586-138">Prohlížeč odesílá pouze soubor cookie v požadavcích na odpovídající název hostitele.</span><span class="sxs-lookup"><span data-stu-id="2d586-138">The browser only sends the cookie in requests to a matching host name.</span></span> <span data-ttu-id="2d586-139">Můžete chtít upravit tak, aby soubory cookie, které jsou k dispozici pro všechny hostitele ve vaší doméně.</span><span class="sxs-lookup"><span data-stu-id="2d586-139">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="2d586-140">Například nastavení domény souboru cookie na `.contoso.com` ji zpřístupní k `contoso.com`, `www.contoso.com`, a `staging.www.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="2d586-140">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="2d586-141">Cookie.Expiration</span><span class="sxs-lookup"><span data-stu-id="2d586-141">Cookie.Expiration</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.expiration?view=aspnetcore-2.0) | <span data-ttu-id="2d586-142">Získá nebo nastaví životnost do souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-142">Gets or sets the lifespan of a cookie.</span></span> <span data-ttu-id="2d586-143">V současné době to možnost operace a bude zastaralé v ASP.NET Core 2.1 +.</span><span class="sxs-lookup"><span data-stu-id="2d586-143">Currently, this option no-ops and will become obsolete in ASP.NET Core 2.1+.</span></span> <span data-ttu-id="2d586-144">Použití `ExpireTimeSpan` možnost nastavit vypršení platnosti souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-144">Use the `ExpireTimeSpan` option to set cookie expiration.</span></span> <span data-ttu-id="2d586-145">Další informace najdete v tématu [vysvětlení chování CookieAuthenticationOptions.Cookie.Expiration](https://github.com/aspnet/Security/issues/1293).</span><span class="sxs-lookup"><span data-stu-id="2d586-145">For more information, see [Clarify behavior of CookieAuthenticationOptions.Cookie.Expiration](https://github.com/aspnet/Security/issues/1293).</span></span> |
| [<span data-ttu-id="2d586-146">Cookie.HttpOnly</span><span class="sxs-lookup"><span data-stu-id="2d586-146">Cookie.HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | <span data-ttu-id="2d586-147">Příznak označující, pokud by měl být soubor cookie přístupný jenom pro servery.</span><span class="sxs-lookup"><span data-stu-id="2d586-147">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="2d586-148">Změna této hodnoty na `false` povoluje skripty na straně klienta pro přístup k souboru cookie a může otevřít aplikaci tak, aby krádež souborů cookie by měl mít vaše aplikace [skriptování napříč weby (XSS)](xref:security/cross-site-scripting) ohrožení zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="2d586-148">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="2d586-149">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="2d586-149">The default value is `true`.</span></span> |
| [<span data-ttu-id="2d586-150">Cookie.Name</span><span class="sxs-lookup"><span data-stu-id="2d586-150">Cookie.Name</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | <span data-ttu-id="2d586-151">Nastaví název souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-151">Sets the name of the cookie.</span></span> |
| [<span data-ttu-id="2d586-152">Cookie.Path</span><span class="sxs-lookup"><span data-stu-id="2d586-152">Cookie.Path</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | <span data-ttu-id="2d586-153">Použít k izolaci aplikací spuštěných na stejný název hostitele.</span><span class="sxs-lookup"><span data-stu-id="2d586-153">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="2d586-154">Pokud máte aplikaci spuštěnou na `/app1` a chcete omezit souborů cookie do této aplikace, nastavte `CookiePath` vlastnost `/app1`.</span><span class="sxs-lookup"><span data-stu-id="2d586-154">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="2d586-155">Díky tomu souboru cookie, který je k dispozici pouze u požadavků na `/app1` a všechny aplikace pod ním.</span><span class="sxs-lookup"><span data-stu-id="2d586-155">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="2d586-156">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="2d586-156">Cookie.SameSite</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | <span data-ttu-id="2d586-157">Určuje, zda v prohlížeči by měl povolit soubory cookie bude připojený na stejný web požadavky jenom (`SameSiteMode.Strict`) nebo pomocí bezpečných metod HTTP a požadavky na stejný web požadavky mezi weby (`SameSiteMode.Lax`).</span><span class="sxs-lookup"><span data-stu-id="2d586-157">Indicates whether the browser should allow the cookie to be attached to same-site requests only (`SameSiteMode.Strict`) or cross-site requests using safe HTTP methods and same-site requests (`SameSiteMode.Lax`).</span></span> <span data-ttu-id="2d586-158">Pokud je nastavena na `SameSiteMode.None`, není-li nastavena hodnota hlavičky souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-158">When set to `SameSiteMode.None`, the cookie header value isn't set.</span></span> <span data-ttu-id="2d586-159">Všimněte si, že [middlewaru souboru Cookie. zásady](#cookie-policy-middleware) mohou přepsat hodnotu, kterou zadáte.</span><span class="sxs-lookup"><span data-stu-id="2d586-159">Note that [Cookie Policy Middleware](#cookie-policy-middleware) might overwrite the value that you provide.</span></span> <span data-ttu-id="2d586-160">Pro podporu ověřování OAuth, výchozí hodnota je `SameSiteMode.Lax`.</span><span class="sxs-lookup"><span data-stu-id="2d586-160">To support OAuth authentication, the default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="2d586-161">Další informace najdete v tématu [nefunkční kvůli zásadám SameSite souboru cookie ověřování OAuth](https://github.com/aspnet/Security/issues/1231).</span><span class="sxs-lookup"><span data-stu-id="2d586-161">For more information, see [OAuth authentication broken due to SameSite cookie policy](https://github.com/aspnet/Security/issues/1231).</span></span> |
| [<span data-ttu-id="2d586-162">Cookie.SecurePolicy</span><span class="sxs-lookup"><span data-stu-id="2d586-162">Cookie.SecurePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | <span data-ttu-id="2d586-163">Příznak označující, pokud by měl být soubor cookie vytvořený omezené na protokol HTTPS (`CookieSecurePolicy.Always`), HTTP nebo HTTPS (`CookieSecurePolicy.None`), nebo stejný protokol jako požadavek (`CookieSecurePolicy.SameAsRequest`).</span><span class="sxs-lookup"><span data-stu-id="2d586-163">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="2d586-164">Výchozí hodnota je `CookieSecurePolicy.SameAsRequest`.</span><span class="sxs-lookup"><span data-stu-id="2d586-164">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="2d586-165">DataProtectionProvider</span><span class="sxs-lookup"><span data-stu-id="2d586-165">DataProtectionProvider</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | <span data-ttu-id="2d586-166">Nastaví `DataProtectionProvider` , který slouží k vytvoření výchozích `TicketDataFormat`.</span><span class="sxs-lookup"><span data-stu-id="2d586-166">Sets the `DataProtectionProvider` that's used to create the default `TicketDataFormat`.</span></span> <span data-ttu-id="2d586-167">Pokud `TicketDataFormat` je vlastnost nastavena, `DataProtectionProvider` není použita možnost.</span><span class="sxs-lookup"><span data-stu-id="2d586-167">If the `TicketDataFormat` property is set, the `DataProtectionProvider` option isn't used.</span></span> <span data-ttu-id="2d586-168">Pokud se nezadá, výchozí zprostředkovatel ochrany dat aplikace se používá.</span><span class="sxs-lookup"><span data-stu-id="2d586-168">If not provided, the app's default data protection provider is used.</span></span> |
| [<span data-ttu-id="2d586-169">Události</span><span class="sxs-lookup"><span data-stu-id="2d586-169">Events</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | <span data-ttu-id="2d586-170">Obslužná rutina volá metody zprostředkovatele, který poskytují kontrolu aplikace v určitých bodech, zpracování.</span><span class="sxs-lookup"><span data-stu-id="2d586-170">The handler calls methods on the provider that give the app control at certain processing points.</span></span> <span data-ttu-id="2d586-171">Pokud `Events` nejsou zadaná, je použita výchozí instance, který nemá žádný účinek, pokud jsou volány metody.</span><span class="sxs-lookup"><span data-stu-id="2d586-171">If `Events` aren't provided, a default instance is supplied that does nothing when the methods are called.</span></span> |
| [<span data-ttu-id="2d586-172">EventsType</span><span class="sxs-lookup"><span data-stu-id="2d586-172">EventsType</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | <span data-ttu-id="2d586-173">Použít jako typ služby k získání `Events` instanci místo vlastnost.</span><span class="sxs-lookup"><span data-stu-id="2d586-173">Used as the service type to get the `Events` instance instead of the property.</span></span> |
| [<span data-ttu-id="2d586-174">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="2d586-174">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | <span data-ttu-id="2d586-175">`TimeSpan` Po kterém vyprší platnost lístku ověřování uložený v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-175">The `TimeSpan` after which the authentication ticket stored inside the cookie expires.</span></span> <span data-ttu-id="2d586-176">`ExpireTimeSpan` je přidán do aktuální čas při vytváření dobu vypršení platnosti-the-ticket.</span><span class="sxs-lookup"><span data-stu-id="2d586-176">`ExpireTimeSpan` is added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="2d586-177">`ExpiredTimeSpan` Hodnota vždy přejde do šifrovaného AuthTicket ověřit server.</span><span class="sxs-lookup"><span data-stu-id="2d586-177">The `ExpiredTimeSpan` value always goes into the encrypted AuthTicket verified by the server.</span></span> <span data-ttu-id="2d586-178">Může taky přejít do [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) záhlaví, ale pouze v případě `IsPersistent` nastavena.</span><span class="sxs-lookup"><span data-stu-id="2d586-178">It may also go into the [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) header, but only if `IsPersistent` is set.</span></span> <span data-ttu-id="2d586-179">Nastavit `IsPersistent` k `true`, nakonfigurujte [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) předán `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="2d586-179">To set `IsPersistent` to `true`, configure the [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) passed to `SignInAsync`.</span></span> <span data-ttu-id="2d586-180">Výchozí hodnota `ExpireTimeSpan` je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="2d586-180">The default value of `ExpireTimeSpan` is 14 days.</span></span> |
| [<span data-ttu-id="2d586-181">LoginPath</span><span class="sxs-lookup"><span data-stu-id="2d586-181">LoginPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | <span data-ttu-id="2d586-182">Určuje cestu k poskytování s 302 Found (adresa URL přesměrování) aktivovaného `HttpContext.ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="2d586-182">Provides the path to supply with a 302 Found (URL redirect) when triggered by `HttpContext.ChallengeAsync`.</span></span> <span data-ttu-id="2d586-183">Aktuální adresa URL, které vygenerovalo 401 je přidán do `LoginPath` jako parametru řetězce dotazu s názvem podle `ReturnUrlParameter`.</span><span class="sxs-lookup"><span data-stu-id="2d586-183">The current URL that generated the 401 is added to the `LoginPath` as a query string parameter named by the `ReturnUrlParameter`.</span></span> <span data-ttu-id="2d586-184">Jakmile požadavek na `LoginPath` udělí novou identitu přihlášení, `ReturnUrlParameter` hodnota slouží k přesměrování prohlížeče zpět na adresu URL, která způsobila původní kód neautorizovaného stavu.</span><span class="sxs-lookup"><span data-stu-id="2d586-184">Once a request to the `LoginPath` grants a new sign-in identity, the `ReturnUrlParameter` value is used to redirect the browser back to the URL that caused the original unauthorized status code.</span></span> <span data-ttu-id="2d586-185">Výchozí hodnota je `/Account/Login`.</span><span class="sxs-lookup"><span data-stu-id="2d586-185">The default value is `/Account/Login`.</span></span> |
| [<span data-ttu-id="2d586-186">LogoutPath</span><span class="sxs-lookup"><span data-stu-id="2d586-186">LogoutPath</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | <span data-ttu-id="2d586-187">Pokud `LogoutPath` je k dispozici pro obslužnou rutinu, pak přesměruje požadavek na tuto cestu podle hodnoty `ReturnUrlParameter`.</span><span class="sxs-lookup"><span data-stu-id="2d586-187">If the `LogoutPath` is provided to the handler, then a request to that path redirects based on the value of the `ReturnUrlParameter`.</span></span> <span data-ttu-id="2d586-188">Výchozí hodnota je `/Account/Logout`.</span><span class="sxs-lookup"><span data-stu-id="2d586-188">The default value is `/Account/Logout`.</span></span> |
| [<span data-ttu-id="2d586-189">ReturnUrlParameter</span><span class="sxs-lookup"><span data-stu-id="2d586-189">ReturnUrlParameter</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | <span data-ttu-id="2d586-190">Určuje název parametru řetězce dotazu, který se připojí obslužnou rutinou pro odpovědi 302 Found (adresa URL přesměrování).</span><span class="sxs-lookup"><span data-stu-id="2d586-190">Determines the name of the query string parameter that's appended by the handler for a 302 Found (URL redirect) response.</span></span> <span data-ttu-id="2d586-191">`ReturnUrlParameter` se používá, když dorazí požadavek na `LoginPath` nebo `LogoutPath` do prohlížeče po provedení akce přihlašovací nebo odhlašovací vraťte k původní adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2d586-191">`ReturnUrlParameter` is used when a request arrives on the `LoginPath` or `LogoutPath` to return the browser to the original URL after the login or logout action is performed.</span></span> <span data-ttu-id="2d586-192">Výchozí hodnota je `ReturnUrl`.</span><span class="sxs-lookup"><span data-stu-id="2d586-192">The default value is `ReturnUrl`.</span></span> |
| [<span data-ttu-id="2d586-193">SessionStore</span><span class="sxs-lookup"><span data-stu-id="2d586-193">SessionStore</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | <span data-ttu-id="2d586-194">Volitelný kontejner používá k ukládání identit napříč požadavky.</span><span class="sxs-lookup"><span data-stu-id="2d586-194">An optional container used to store identity across requests.</span></span> <span data-ttu-id="2d586-195">Když se použije, jenom identifikátor relace je odeslat klientovi.</span><span class="sxs-lookup"><span data-stu-id="2d586-195">When used, only a session identifier is sent to the client.</span></span> <span data-ttu-id="2d586-196">`SessionStore` je možné zmírnit potenciální potíže s velkými identitami.</span><span class="sxs-lookup"><span data-stu-id="2d586-196">`SessionStore` can be used to mitigate potential problems with large identities.</span></span> |
| [<span data-ttu-id="2d586-197">parametr slidingExpiration</span><span class="sxs-lookup"><span data-stu-id="2d586-197">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | <span data-ttu-id="2d586-198">Příznak označující, pokud se nový soubor cookie s čas vypršení platnosti aktualizace by měla být vydána dynamicky.</span><span class="sxs-lookup"><span data-stu-id="2d586-198">A flag indicating if a new cookie with an updated expiration time should be issued dynamically.</span></span> <span data-ttu-id="2d586-199">To může nastat na všechny požadavky, pokud aktuální období vypršení platnosti souboru cookie je více než 50 % vypršela platnost.</span><span class="sxs-lookup"><span data-stu-id="2d586-199">This can happen on any request where the current cookie expiration period is more than 50% expired.</span></span> <span data-ttu-id="2d586-200">Nové datum vypršení platnosti bude přesunut dopředu na aktuální datum plus `ExpireTimespan`.</span><span class="sxs-lookup"><span data-stu-id="2d586-200">The new expiration date is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="2d586-201">[Čas vypršení platnosti souboru cookie absolutní](xref:security/authentication/cookie#absolute-cookie-expiration) můžete nastavit pomocí `AuthenticationProperties` třídy při volání metody `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="2d586-201">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="2d586-202">Čas absolutní vypršení platnosti můžete zlepšit zabezpečení vaší aplikace tím, že omezíte množství času, který je platný soubor cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="2d586-202">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="2d586-203">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="2d586-203">The default value is `true`.</span></span> |
| [<span data-ttu-id="2d586-204">TicketDataFormat</span><span class="sxs-lookup"><span data-stu-id="2d586-204">TicketDataFormat</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | <span data-ttu-id="2d586-205">`TicketDataFormat` Se používá k ochraně a zrušení této ochrany identity a dalších vlastností, které jsou uloženy v hodnotě souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-205">The `TicketDataFormat` is used to protect and unprotect the identity and other properties that are stored in the cookie value.</span></span> <span data-ttu-id="2d586-206">Pokud se nezadá, `TicketDataFormat` je vytvořený pomocí [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="2d586-206">If not provided, a `TicketDataFormat` is created using the [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0).</span></span> |
| [<span data-ttu-id="2d586-207">Ověření</span><span class="sxs-lookup"><span data-stu-id="2d586-207">Validate</span></span>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | <span data-ttu-id="2d586-208">Metoda, která kontroluje, že možnosti jsou platné.</span><span class="sxs-lookup"><span data-stu-id="2d586-208">Method that checks that the options are valid.</span></span> |

<span data-ttu-id="2d586-209">Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="2d586-209">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="2d586-210">Soubor cookie používá ASP.NET Core 1.x [middleware](xref:fundamentals/middleware/index) hlavní název uživatele, který serializuje do zašifrovaného souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-210">ASP.NET Core 1.x uses cookie [middleware](xref:fundamentals/middleware/index) that serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="2d586-211">Na následné žádosti soubor cookie se ověří a znovu vytvořit a přiřadit k objektu zabezpečení `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="2d586-211">On subsequent requests, the cookie is validated, and the principal is recreated and assigned to the `HttpContext.User` property.</span></span>

<span data-ttu-id="2d586-212">Nainstalujte [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) balíčku NuGet ve vašem projektu.</span><span class="sxs-lookup"><span data-stu-id="2d586-212">Install the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) NuGet package in your project.</span></span> <span data-ttu-id="2d586-213">Tento balíček obsahuje middlewaru souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-213">This package contains the cookie middleware.</span></span>

<span data-ttu-id="2d586-214">Použití `UseCookieAuthentication` metoda ve `Configure` metoda ve vaší *Startup.cs* souboru před `UseMvc` nebo `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="2d586-214">Use the `UseCookieAuthentication` method in the `Configure` method in your *Startup.cs* file before `UseMvc` or `UseMvcWithDefaultRoute`:</span></span>

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

<span data-ttu-id="2d586-215">**Možnosti CookieAuthenticationOptions**</span><span class="sxs-lookup"><span data-stu-id="2d586-215">**CookieAuthenticationOptions Options**</span></span>

<span data-ttu-id="2d586-216">[CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) třída se používá ke konfiguraci možností zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="2d586-216">The [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) class is used to configure the authentication provider options.</span></span>

| <span data-ttu-id="2d586-217">Možnost</span><span class="sxs-lookup"><span data-stu-id="2d586-217">Option</span></span> | <span data-ttu-id="2d586-218">Popis</span><span class="sxs-lookup"><span data-stu-id="2d586-218">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="2d586-219">AuthenticationScheme</span><span class="sxs-lookup"><span data-stu-id="2d586-219">AuthenticationScheme</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | <span data-ttu-id="2d586-220">Nastaví schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="2d586-220">Sets the authentication scheme.</span></span> <span data-ttu-id="2d586-221">`AuthenticationScheme` je užitečné, pokud existuje více instancí ověřování a vy chcete [autorizovat s konkrétní schéma](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="2d586-221">`AuthenticationScheme` is useful when there are multiple instances of authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="2d586-222">Nastavení `AuthenticationScheme` k `CookieAuthenticationDefaults.AuthenticationScheme` poskytuje hodnotu "Soubory cookie" pro schéma.</span><span class="sxs-lookup"><span data-stu-id="2d586-222">Setting the `AuthenticationScheme` to `CookieAuthenticationDefaults.AuthenticationScheme` provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="2d586-223">Můžete zadat libovolnou hodnotu řetězce, která odlišuje schéma.</span><span class="sxs-lookup"><span data-stu-id="2d586-223">You can supply any string value that distinguishes the scheme.</span></span> |
| [<span data-ttu-id="2d586-224">AutomaticAuthenticate</span><span class="sxs-lookup"><span data-stu-id="2d586-224">AutomaticAuthenticate</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | <span data-ttu-id="2d586-225">Nastaví hodnotu označující, že ověřování souborů cookie by měl spustit u každého požadavku a pokusí se ověřit a proveďte rekonstrukci serializovaný objekt zabezpečení, vytvořená.</span><span class="sxs-lookup"><span data-stu-id="2d586-225">Sets a value to indicate that the cookie authentication should run on every request and attempt to validate and reconstruct any serialized principal it created.</span></span> |
| [<span data-ttu-id="2d586-226">AutomaticChallenge</span><span class="sxs-lookup"><span data-stu-id="2d586-226">AutomaticChallenge</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | <span data-ttu-id="2d586-227">Při hodnotě true se zpracovává middleware ověřování automatické výzvy.</span><span class="sxs-lookup"><span data-stu-id="2d586-227">If true, the authentication middleware handles automatic challenges.</span></span> <span data-ttu-id="2d586-228">Pokud false, middleware ověřování pouze změní odpovědi, když explicitně `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="2d586-228">If false, the authentication middleware only alters responses when explicitly indicated by the `AuthenticationScheme`.</span></span> |
| [<span data-ttu-id="2d586-229">ClaimsIssuer</span><span class="sxs-lookup"><span data-stu-id="2d586-229">ClaimsIssuer</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | <span data-ttu-id="2d586-230">Vystavitel, který má použít pro [vystavitele](/dotnet/api/system.security.claims.claim.issuer) vlastnost na všechny deklarace vytvořené middleware ověřování souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-230">The issuer to use for the [Issuer](/dotnet/api/system.security.claims.claim.issuer) property on any claims created by the cookie authentication middleware.</span></span> |
| [<span data-ttu-id="2d586-231">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="2d586-231">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | <span data-ttu-id="2d586-232">Název domény, kde je soubor cookie obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="2d586-232">The domain name where the cookie is served.</span></span> <span data-ttu-id="2d586-233">Ve výchozím nastavení toto je název hostitele žádosti.</span><span class="sxs-lookup"><span data-stu-id="2d586-233">By default, this is the host name of the request.</span></span> <span data-ttu-id="2d586-234">Prohlížeč slouží pouze souboru cookie, který má odpovídající název hostitele.</span><span class="sxs-lookup"><span data-stu-id="2d586-234">The browser only serves the cookie to a matching host name.</span></span> <span data-ttu-id="2d586-235">Můžete chtít upravit tak, aby soubory cookie, které jsou k dispozici pro všechny hostitele ve vaší doméně.</span><span class="sxs-lookup"><span data-stu-id="2d586-235">You may wish to adjust this to have cookies available to any host in your domain.</span></span> <span data-ttu-id="2d586-236">Například nastavení domény souboru cookie na `.contoso.com` ji zpřístupní k `contoso.com`, `www.contoso.com`, a `staging.www.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="2d586-236">For example, setting the cookie domain to `.contoso.com` makes it available to `contoso.com`, `www.contoso.com`, and `staging.www.contoso.com`.</span></span> |
| [<span data-ttu-id="2d586-237">CookieHttpOnly</span><span class="sxs-lookup"><span data-stu-id="2d586-237">CookieHttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | <span data-ttu-id="2d586-238">Příznak označující, pokud by měl být soubor cookie přístupný jenom pro servery.</span><span class="sxs-lookup"><span data-stu-id="2d586-238">A flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="2d586-239">Změna této hodnoty na `false` povoluje skripty na straně klienta pro přístup k souboru cookie a může otevřít aplikaci tak, aby krádež souborů cookie by měl mít vaše aplikace [skriptování napříč weby (XSS)](xref:security/cross-site-scripting) ohrožení zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="2d586-239">Changing this value to `false` permits client-side scripts to access the cookie and may open your app to cookie theft should your app have a [Cross-site scripting (XSS)](xref:security/cross-site-scripting) vulnerability.</span></span> <span data-ttu-id="2d586-240">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="2d586-240">The default value is `true`.</span></span> |
| [<span data-ttu-id="2d586-241">CookiePath</span><span class="sxs-lookup"><span data-stu-id="2d586-241">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | <span data-ttu-id="2d586-242">Použít k izolaci aplikací spuštěných na stejný název hostitele.</span><span class="sxs-lookup"><span data-stu-id="2d586-242">Used to isolate apps running on the same host name.</span></span> <span data-ttu-id="2d586-243">Pokud máte aplikaci spuštěnou na `/app1` a chcete omezit souborů cookie do této aplikace, nastavte `CookiePath` vlastnost `/app1`.</span><span class="sxs-lookup"><span data-stu-id="2d586-243">If you have an app running at `/app1` and want to restrict cookies to that app, set the `CookiePath` property to `/app1`.</span></span> <span data-ttu-id="2d586-244">Díky tomu souboru cookie, který je k dispozici pouze u požadavků na `/app1` a všechny aplikace pod ním.</span><span class="sxs-lookup"><span data-stu-id="2d586-244">By doing so, the cookie is only available on requests to `/app1` and any app underneath it.</span></span> |
| [<span data-ttu-id="2d586-245">CookieSecure</span><span class="sxs-lookup"><span data-stu-id="2d586-245">CookieSecure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | <span data-ttu-id="2d586-246">Příznak označující, pokud by měl být soubor cookie vytvořený omezené na protokol HTTPS (`CookieSecurePolicy.Always`), HTTP nebo HTTPS (`CookieSecurePolicy.None`), nebo stejný protokol jako požadavek (`CookieSecurePolicy.SameAsRequest`).</span><span class="sxs-lookup"><span data-stu-id="2d586-246">A flag indicating if the cookie created should be limited to HTTPS (`CookieSecurePolicy.Always`), HTTP or HTTPS (`CookieSecurePolicy.None`), or the same protocol as the request (`CookieSecurePolicy.SameAsRequest`).</span></span> <span data-ttu-id="2d586-247">Výchozí hodnota je `CookieSecurePolicy.SameAsRequest`.</span><span class="sxs-lookup"><span data-stu-id="2d586-247">The default value is `CookieSecurePolicy.SameAsRequest`.</span></span> |
| [<span data-ttu-id="2d586-248">Popis</span><span class="sxs-lookup"><span data-stu-id="2d586-248">Description</span></span>](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | <span data-ttu-id="2d586-249">Další informace o typu ověřování, který je k dispozici pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2d586-249">Additional information about the authentication type which is made available to the app.</span></span> |
| [<span data-ttu-id="2d586-250">ExpireTimeSpan</span><span class="sxs-lookup"><span data-stu-id="2d586-250">ExpireTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | <span data-ttu-id="2d586-251">`TimeSpan` Po kterém vyprší platnost lístku ověřování.</span><span class="sxs-lookup"><span data-stu-id="2d586-251">The `TimeSpan` after which the authentication ticket expires.</span></span> <span data-ttu-id="2d586-252">Přidá se do aktuálního času vytvoření dobu vypršení platnosti-the-ticket.</span><span class="sxs-lookup"><span data-stu-id="2d586-252">It's added to the current time to create the expiration time for the ticket.</span></span> <span data-ttu-id="2d586-253">Použití `ExpireTimeSpan`, je nutné nastavit `IsPersistent` k `true` v `AuthenticationProperties` předán `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="2d586-253">To use `ExpireTimeSpan`, you must set `IsPersistent` to `true` in the `AuthenticationProperties` passed to `SignInAsync`.</span></span> <span data-ttu-id="2d586-254">Výchozí hodnota je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="2d586-254">The default value is 14 days.</span></span> |
| [<span data-ttu-id="2d586-255">parametr slidingExpiration</span><span class="sxs-lookup"><span data-stu-id="2d586-255">SlidingExpiration</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | <span data-ttu-id="2d586-256">Příznak označující, zda datum vypršení platnosti souboru cookie resetuje při více než polovina `ExpireTimeSpan` uplynutí intervalu.</span><span class="sxs-lookup"><span data-stu-id="2d586-256">A flag indicating whether the cookie expiration date resets when more than half of the `ExpireTimeSpan` interval has passed.</span></span> <span data-ttu-id="2d586-257">Nový čas exipiration bude přesunut dopředu na aktuální datum plus `ExpireTimespan`.</span><span class="sxs-lookup"><span data-stu-id="2d586-257">The new exipiration time is moved forward to be the current date plus the `ExpireTimespan`.</span></span> <span data-ttu-id="2d586-258">[Čas vypršení platnosti souboru cookie absolutní](xref:security/authentication/cookie#absolute-cookie-expiration) můžete nastavit pomocí `AuthenticationProperties` třídy při volání metody `SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="2d586-258">An [absolute cookie expiration time](xref:security/authentication/cookie#absolute-cookie-expiration) can be set by using the `AuthenticationProperties` class when calling `SignInAsync`.</span></span> <span data-ttu-id="2d586-259">Čas absolutní vypršení platnosti můžete zlepšit zabezpečení vaší aplikace tím, že omezíte množství času, který je platný soubor cookie ověřování.</span><span class="sxs-lookup"><span data-stu-id="2d586-259">An absolute expiration time can improve the security of your app by limiting the amount of time that the authentication cookie is valid.</span></span> <span data-ttu-id="2d586-260">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="2d586-260">The default value is `true`.</span></span> |

<span data-ttu-id="2d586-261">Nastavte `CookieAuthenticationOptions` pro Middleware ověřování souborů Cookie v `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="2d586-261">Set `CookieAuthenticationOptions` for the Cookie Authentication Middleware in the `Configure` method:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

::: moniker-end

## <a name="cookie-policy-middleware"></a><span data-ttu-id="2d586-262">Zásady middlewaru souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-262">Cookie Policy Middleware</span></span>

<span data-ttu-id="2d586-263">[Zásady middlewaru souboru cookie](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) umožňuje možnosti zásad souborů cookie v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2d586-263">[Cookie Policy Middleware](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) enables cookie policy capabilities in an app.</span></span> <span data-ttu-id="2d586-264">Přidání middleware do kanálu zpracování aplikace je v pořadí citlivé; ovlivňuje pouze komponenty zaregistrovaný v kanálu po něm.</span><span class="sxs-lookup"><span data-stu-id="2d586-264">Adding the middleware to the app processing pipeline is order sensitive; it only affects components registered after it in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 <span data-ttu-id="2d586-265">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) poskytnuté middlewaru souboru Cookie. zásady umožňují určit globální vlastnosti zpracování souboru cookie a hook do obslužné rutiny zpracování souboru cookie, kdy jsou soubory cookie připojí nebo odstraní.</span><span class="sxs-lookup"><span data-stu-id="2d586-265">The [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) provided to the Cookie Policy Middleware allow you to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

| <span data-ttu-id="2d586-266">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="2d586-266">Property</span></span> | <span data-ttu-id="2d586-267">Popis</span><span class="sxs-lookup"><span data-stu-id="2d586-267">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="2d586-268">HttpOnly</span><span class="sxs-lookup"><span data-stu-id="2d586-268">HttpOnly</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | <span data-ttu-id="2d586-269">Ovlivňuje, jestli HttpOnly musí být soubory cookie, který je příznak označující, pokud by měl být soubor cookie přístupný jenom pro servery.</span><span class="sxs-lookup"><span data-stu-id="2d586-269">Affects whether cookies must be HttpOnly, which is a flag indicating if the cookie should be accessible only to servers.</span></span> <span data-ttu-id="2d586-270">Výchozí hodnota je `HttpOnlyPolicy.None`.</span><span class="sxs-lookup"><span data-stu-id="2d586-270">The default value is `HttpOnlyPolicy.None`.</span></span> |
| [<span data-ttu-id="2d586-271">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="2d586-271">MinimumSameSitePolicy</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | <span data-ttu-id="2d586-272">Má vliv na stejný web atribut souboru cookie (viz níže).</span><span class="sxs-lookup"><span data-stu-id="2d586-272">Affects the cookie's same-site attribute (see below).</span></span> <span data-ttu-id="2d586-273">Výchozí hodnota je `SameSiteMode.Lax`.</span><span class="sxs-lookup"><span data-stu-id="2d586-273">The default value is `SameSiteMode.Lax`.</span></span> <span data-ttu-id="2d586-274">Tato možnost je dostupná pro ASP.NET Core 2.0 +.</span><span class="sxs-lookup"><span data-stu-id="2d586-274">This option is available for ASP.NET Core 2.0+.</span></span> |
| [<span data-ttu-id="2d586-275">OnAppendCookie</span><span class="sxs-lookup"><span data-stu-id="2d586-275">OnAppendCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | <span data-ttu-id="2d586-276">Volá se, když je přidán do souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-276">Called when a cookie is appended.</span></span> |
| [<span data-ttu-id="2d586-277">OnDeleteCookie</span><span class="sxs-lookup"><span data-stu-id="2d586-277">OnDeleteCookie</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | <span data-ttu-id="2d586-278">Volá se, když se odstraní soubor cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-278">Called when a cookie is deleted.</span></span> |
| [<span data-ttu-id="2d586-279">zabezpečení</span><span class="sxs-lookup"><span data-stu-id="2d586-279">Secure</span></span>](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | <span data-ttu-id="2d586-280">Ovlivňuje, jestli soubory cookie musí být zabezpečené.</span><span class="sxs-lookup"><span data-stu-id="2d586-280">Affects whether cookies must be Secure.</span></span> <span data-ttu-id="2d586-281">Výchozí hodnota je `CookieSecurePolicy.None`.</span><span class="sxs-lookup"><span data-stu-id="2d586-281">The default value is `CookieSecurePolicy.None`.</span></span> |

<span data-ttu-id="2d586-282">**MinimumSameSitePolicy** (ASP.NET Core 2.0 + jenom)</span><span class="sxs-lookup"><span data-stu-id="2d586-282">**MinimumSameSitePolicy** (ASP.NET Core 2.0+ only)</span></span>

<span data-ttu-id="2d586-283">Výchozí hodnota `MinimumSameSitePolicy` hodnotu `SameSiteMode.Lax` tak, aby povolovala ověřování OAuth2.</span><span class="sxs-lookup"><span data-stu-id="2d586-283">The default `MinimumSameSitePolicy` value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="2d586-284">Přísně vynutit zásady stejný web `SameSiteMode.Strict`, nastavte `MinimumSameSitePolicy`.</span><span class="sxs-lookup"><span data-stu-id="2d586-284">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="2d586-285">I když toto nastavení přeruší OAuth2 nebo jiná schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souboru cookie pro ostatní typy aplikací, které se nemusíte spoléhat na zpracování žádosti nepůvodního zdroje.</span><span class="sxs-lookup"><span data-stu-id="2d586-285">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="2d586-286">Nastavení zásad middlewaru souboru Cookie pro `MinimumSameSitePolicy` může mít vliv na vaše nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle níže uvedených matice.</span><span class="sxs-lookup"><span data-stu-id="2d586-286">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect your setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="2d586-287">MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="2d586-287">MinimumSameSitePolicy</span></span> | <span data-ttu-id="2d586-288">Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="2d586-288">Cookie.SameSite</span></span> | <span data-ttu-id="2d586-289">Výsledná nastavení Cookie.SameSite</span><span class="sxs-lookup"><span data-stu-id="2d586-289">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="2d586-290">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="2d586-290">SameSiteMode.None</span></span>     | <span data-ttu-id="2d586-291">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="2d586-291">SameSiteMode.None</span></span><br><span data-ttu-id="2d586-292">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="2d586-292">SameSiteMode.Lax</span></span><br><span data-ttu-id="2d586-293">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="2d586-293">SameSiteMode.Strict</span></span> | <span data-ttu-id="2d586-294">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="2d586-294">SameSiteMode.None</span></span><br><span data-ttu-id="2d586-295">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="2d586-295">SameSiteMode.Lax</span></span><br><span data-ttu-id="2d586-296">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="2d586-296">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2d586-297">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="2d586-297">SameSiteMode.Lax</span></span>      | <span data-ttu-id="2d586-298">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="2d586-298">SameSiteMode.None</span></span><br><span data-ttu-id="2d586-299">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="2d586-299">SameSiteMode.Lax</span></span><br><span data-ttu-id="2d586-300">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="2d586-300">SameSiteMode.Strict</span></span> | <span data-ttu-id="2d586-301">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="2d586-301">SameSiteMode.Lax</span></span><br><span data-ttu-id="2d586-302">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="2d586-302">SameSiteMode.Lax</span></span><br><span data-ttu-id="2d586-303">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="2d586-303">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2d586-304">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="2d586-304">SameSiteMode.Strict</span></span>   | <span data-ttu-id="2d586-305">SameSiteMode.None</span><span class="sxs-lookup"><span data-stu-id="2d586-305">SameSiteMode.None</span></span><br><span data-ttu-id="2d586-306">SameSiteMode.Lax</span><span class="sxs-lookup"><span data-stu-id="2d586-306">SameSiteMode.Lax</span></span><br><span data-ttu-id="2d586-307">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="2d586-307">SameSiteMode.Strict</span></span> | <span data-ttu-id="2d586-308">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="2d586-308">SameSiteMode.Strict</span></span><br><span data-ttu-id="2d586-309">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="2d586-309">SameSiteMode.Strict</span></span><br><span data-ttu-id="2d586-310">SameSiteMode.Strict</span><span class="sxs-lookup"><span data-stu-id="2d586-310">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="2d586-311">Vytvoření souboru cookie pro ověřování</span><span class="sxs-lookup"><span data-stu-id="2d586-311">Create an authentication cookie</span></span>

<span data-ttu-id="2d586-312">Pokud chcete vytvořit soubor cookie, která uchovává informace o uživateli, je nutné vytvořit [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal).</span><span class="sxs-lookup"><span data-stu-id="2d586-312">To create a cookie holding user information, you must construct a [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal).</span></span> <span data-ttu-id="2d586-313">Informace o uživateli je serializován a uložená v souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="2d586-313">The user information is serialized and stored in the cookie.</span></span> 

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="2d586-314">Vytvoření [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) s jakékoli požadované [deklarace identity](/dotnet/api/system.security.claims.claim)s a volání [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) k přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="2d586-314">Create a [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) with any required [Claim](/dotnet/api/system.security.claims.claim)s and call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="2d586-315">Volání [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) k přihlášení uživatele:</span><span class="sxs-lookup"><span data-stu-id="2d586-315">Call [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) to sign in the user:</span></span>

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker-end

<span data-ttu-id="2d586-316">`SignInAsync` Vytvoří do zašifrovaného souboru cookie a přidá jej do aktuální odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2d586-316">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="2d586-317">Pokud nezadáte `AuthenticationScheme`, se používá výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="2d586-317">If you don't specify an `AuthenticationScheme`, the default scheme is used.</span></span>

<span data-ttu-id="2d586-318">Pod pokličkou, je šifrování, které používá ASP.NET Core [ochranu dat](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) systému.</span><span class="sxs-lookup"><span data-stu-id="2d586-318">Under the covers, the encryption used is ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) system.</span></span> <span data-ttu-id="2d586-319">Pokud se hostují aplikaci na více počítačů, Vyrovnávání zatížení napříč aplikacemi nebo pomocí webové farmy, je nutné [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview) používat stejné aktualizační kanál klíč a identifikátor aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d586-319">If you're hosting app on multiple machines, load balancing across apps, or using a web farm, then you must [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="2d586-320">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="2d586-320">Sign out</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="2d586-321">Chcete-li odhlásit aktuálního uživatele a odstranit jejich souborů cookie, zavolejte [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span><span class="sxs-lookup"><span data-stu-id="2d586-321">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="2d586-322">Chcete-li odhlásit aktuálního uživatele a odstranit jejich souborů cookie, zavolejte [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span><span class="sxs-lookup"><span data-stu-id="2d586-322">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):</span></span>

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

::: moniker-end

<span data-ttu-id="2d586-323">Pokud nepoužíváte `CookieAuthenticationDefaults.AuthenticationScheme` (nebo "Soubory cookie") jako schéma (například "ContosoCookie"), zadejte schéma, které jste použili při konfiguraci zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="2d586-323">If you aren't using `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") as the scheme (for example, "ContosoCookie"), supply the scheme you used when configuring the authentication provider.</span></span> <span data-ttu-id="2d586-324">V opačném případě se používá výchozí schéma.</span><span class="sxs-lookup"><span data-stu-id="2d586-324">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="2d586-325">Reagovat na změny back-end</span><span class="sxs-lookup"><span data-stu-id="2d586-325">React to back-end changes</span></span>

<span data-ttu-id="2d586-326">Po vytvoření souboru cookie, bude jediný zdroj identity.</span><span class="sxs-lookup"><span data-stu-id="2d586-326">Once a cookie is created, it becomes the single source of identity.</span></span> <span data-ttu-id="2d586-327">I v případě, že zakázání uživatele v back endových systémů, systém ověřování souborů cookie je vůbec nezná to a uživatel zůstane přihlášený jako jejich soubor cookie je neplatný.</span><span class="sxs-lookup"><span data-stu-id="2d586-327">Even if you disable a user in your back-end systems, the cookie authentication system has no knowledge of this, and a user stays logged in as long as their cookie is valid.</span></span>

<span data-ttu-id="2d586-328">[ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) událost v ASP.NET Core 2.x nebo [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) metoda v ASP.NET Core 1.x lze zachytit a přepsat ověření souboru cookie identity.</span><span class="sxs-lookup"><span data-stu-id="2d586-328">The [ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) event in ASP.NET Core 2.x or the [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) method in ASP.NET Core 1.x can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="2d586-329">Tento přístup snižuje riziko odvolané uživatele, kteří používají aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2d586-329">This approach mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="2d586-330">Jedním z přístupů k ověření souboru cookie je založená na udržování přehledu o při změně uživatelskou databázi.</span><span class="sxs-lookup"><span data-stu-id="2d586-330">One approach to cookie validation is based on keeping track of when the user database has been changed.</span></span> <span data-ttu-id="2d586-331">Pokud databáze nebylo změněno od uživatele soubor cookie vydala, není nutné opakované ověření uživatele, pokud jejich souborů cookie je stále platný.</span><span class="sxs-lookup"><span data-stu-id="2d586-331">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="2d586-332">Databáze, které je implementované v tomto scénáři implementovat `IUserRepository` v tomto příkladu ukládá `LastChanged` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2d586-332">To implement this scenario, the database, which is implemented in `IUserRepository` for this example, stores a `LastChanged` value.</span></span> <span data-ttu-id="2d586-333">Když se aktualizuje každý uživatel v databázi, `LastChanged` je hodnota nastavena na aktuální čas.</span><span class="sxs-lookup"><span data-stu-id="2d586-333">When any user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="2d586-334">Pokud chcete platnost souboru cookie, pokud změny databáze v závislosti na `LastChanged` hodnoty, vytvořte soubor cookie s `LastChanged` deklarace obsahující aktuální `LastChanged` hodnota z databáze:</span><span class="sxs-lookup"><span data-stu-id="2d586-334">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="2d586-335">K implementaci přepsání `ValidatePrincipal` události, napište metodu s následující signaturou ve třídě, který je odvozen z [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span><span class="sxs-lookup"><span data-stu-id="2d586-335">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that you derive from [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="2d586-336">Příklad bude vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="2d586-336">An example looks like the following:</span></span>

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

<span data-ttu-id="2d586-337">Zaregistrovat instanci události během registrace služby souborů cookie v `ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="2d586-337">Register the events instance during cookie service registration in the `ConfigureServices` method.</span></span> <span data-ttu-id="2d586-338">Registraci vymezené služby pro vaše `CustomCookieAuthenticationEvents` třídy:</span><span class="sxs-lookup"><span data-stu-id="2d586-338">Provide a scoped service registration for your `CustomCookieAuthenticationEvents` class:</span></span>

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

<span data-ttu-id="2d586-339">K implementaci přepsání `ValidateAsync` události, napište metodu s následující signaturou:</span><span class="sxs-lookup"><span data-stu-id="2d586-339">To implement an override for the `ValidateAsync` event, write a method with the following signature:</span></span>

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

<span data-ttu-id="2d586-340">ASP.NET Core Identity implementuje tato kontrola v rámci jeho [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync).</span><span class="sxs-lookup"><span data-stu-id="2d586-340">ASP.NET Core Identity implements this check as part of its [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync).</span></span> <span data-ttu-id="2d586-341">Příklad bude vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="2d586-341">An example looks like the following:</span></span>

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

<span data-ttu-id="2d586-342">Zaregistrujte události během konfigurace ověřování souborů cookie v `Configure` metody:</span><span class="sxs-lookup"><span data-stu-id="2d586-342">Register the event during cookie authentication configuration in the `Configure` method:</span></span>

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

<span data-ttu-id="2d586-343">Představte si situaci, ve kterém se aktualizuje uživatelské jméno &mdash; rozhodnutí, která nemá vliv na zabezpečení žádným způsobem.</span><span class="sxs-lookup"><span data-stu-id="2d586-343">Consider a situation in which the user's name is updated &mdash; a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="2d586-344">Pokud chcete aktualizovat nedestruktivně hlavní název uživatele, zavolejte `context.ReplacePrincipal` a nastavit `context.ShouldRenew` vlastnost `true`.</span><span class="sxs-lookup"><span data-stu-id="2d586-344">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2d586-345">Tento přístup je zde popsáno, se aktivuje u každého požadavku.</span><span class="sxs-lookup"><span data-stu-id="2d586-345">The approach described here is triggered on every request.</span></span> <span data-ttu-id="2d586-346">To může způsobit snížení výkonu velké aplikace.</span><span class="sxs-lookup"><span data-stu-id="2d586-346">This can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="2d586-347">Trvalé soubory cookie</span><span class="sxs-lookup"><span data-stu-id="2d586-347">Persistent cookies</span></span>

<span data-ttu-id="2d586-348">Můžete chtít soubor cookie zachovat v rámci relací prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2d586-348">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="2d586-349">Tato trvalost má být povoleno pouze explicitní uživatelské souhlas s "Zapamatovat" zaškrtávací políčko na přihlašovací nebo mechanismus podobný.</span><span class="sxs-lookup"><span data-stu-id="2d586-349">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on login or a similar mechanism.</span></span> 

<span data-ttu-id="2d586-350">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který odolává prostřednictvím prohlížeče UZÁVĚRECH.</span><span class="sxs-lookup"><span data-stu-id="2d586-350">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="2d586-351">Klouzavé vypršení platnosti nastavení jste dříve nakonfigurovali jsou zachované.</span><span class="sxs-lookup"><span data-stu-id="2d586-351">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="2d586-352">Pokud se při zavření prohlížeče vyprší platnost souboru cookie, vymaže prohlížeči soubor cookie po jeho restartování.</span><span class="sxs-lookup"><span data-stu-id="2d586-352">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

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

<span data-ttu-id="2d586-353">[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) třídy se nachází v `Microsoft.AspNetCore.Authentication` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="2d586-353">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) class resides in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

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

<span data-ttu-id="2d586-354">[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) třídy se nachází v `Microsoft.AspNetCore.Http.Authentication` oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="2d586-354">The [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) class resides in the `Microsoft.AspNetCore.Http.Authentication` namespace.</span></span>

::: moniker-end

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="2d586-355">Soubor cookie absolutní vypršení platnosti</span><span class="sxs-lookup"><span data-stu-id="2d586-355">Absolute cookie expiration</span></span>

<span data-ttu-id="2d586-356">Můžete nastavit dobu vypršení platnosti absolutní s `ExpiresUtc`.</span><span class="sxs-lookup"><span data-stu-id="2d586-356">You can set an absolute expiration time with `ExpiresUtc`.</span></span> <span data-ttu-id="2d586-357">Musíte taky nastavit `IsPersistent`; v opačném případě `ExpiresUtc` je ignorována a vytvoří se soubor cookie jedné relace.</span><span class="sxs-lookup"><span data-stu-id="2d586-357">You must also set `IsPersistent`; otherwise, `ExpiresUtc` is ignored and a single-session cookie is created.</span></span> <span data-ttu-id="2d586-358">Když `ExpiresUtc` je nastavena na `SignInAsync`, přepíše hodnotu `ExpireTimeSpan` možnost `CookieAuthenticationOptions`, pokud nastavení.</span><span class="sxs-lookup"><span data-stu-id="2d586-358">When `ExpiresUtc` is set on `SignInAsync`, it overrides the value of the `ExpireTimeSpan` option of `CookieAuthenticationOptions`, if set.</span></span>

<span data-ttu-id="2d586-359">Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který má platnost po dobu 20 minut.</span><span class="sxs-lookup"><span data-stu-id="2d586-359">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="2d586-360">To ignoruje klouzavé vypršení platnosti nastavení předtím nakonfigurovali.</span><span class="sxs-lookup"><span data-stu-id="2d586-360">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="2d586-361">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2d586-361">Additional resources</span></span>

* [<span data-ttu-id="2d586-362">Ověřování 2.0 změny / migrace oznámení</span><span class="sxs-lookup"><span data-stu-id="2d586-362">Auth 2.0 Changes / Migration Announcement</span></span>](https://github.com/aspnet/Announcements/issues/262)
* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="2d586-363">Role na základě zásad kontroly</span><span class="sxs-lookup"><span data-stu-id="2d586-363">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

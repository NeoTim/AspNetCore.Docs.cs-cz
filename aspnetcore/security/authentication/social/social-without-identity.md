---
title: Ověřování přes Facebook, Google a externí poskytovatel bez ASP.NET CoreIdentity
author: rick-anderson
description: Vysvětlení použití Facebooku, Google, Twitteru atd. ověřování uživatelů účtu bez ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: ed908526604b04f9aebb93935aa3ad4719621526
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406040"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="5a52a-103">Použití ověřování poskytovatele přihlašování přes sociální sítě bez ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="5a52a-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="5a52a-104">Od [Kirka Larkin](https://twitter.com/serpent5) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5a52a-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5a52a-105"><xref:security/authentication/social/index>Popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="5a52a-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="5a52a-106">Přístup popsaný v tomto tématu zahrnuje ASP.NET Core Identity jako poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="5a52a-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="5a52a-107">Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="5a52a-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="5a52a-108">To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core Identity , ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.</span><span class="sxs-lookup"><span data-stu-id="5a52a-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="5a52a-109">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="5a52a-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="5a52a-110">Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google.</span><span class="sxs-lookup"><span data-stu-id="5a52a-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="5a52a-111">Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="5a52a-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="5a52a-112">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="5a52a-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="5a52a-113">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="5a52a-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="5a52a-114">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="5a52a-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="5a52a-115">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="5a52a-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="5a52a-116">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="5a52a-116">Configuration</span></span>

<span data-ttu-id="5a52a-117">V `ConfigureServices` metodě nakonfigurujte ověřovací schémata aplikace pomocí <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod, a <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :</span><span class="sxs-lookup"><span data-stu-id="5a52a-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="5a52a-118">Volání pro <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> .</span><span class="sxs-lookup"><span data-stu-id="5a52a-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="5a52a-119">`DefaultScheme`Je výchozí schéma používané následujícími `HttpContext` metodami rozšíření ověřování:</span><span class="sxs-lookup"><span data-stu-id="5a52a-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="5a52a-120">Nastavení aplikace `DefaultScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="5a52a-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="5a52a-121">Nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="5a52a-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="5a52a-122">`DefaultChallengeScheme`Přepisuje `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="5a52a-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="5a52a-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Další vlastnosti, které se při nastavení přepisují, najdete v tématu `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="5a52a-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="5a52a-124">V `Startup.Configure` , zavolejte `UseAuthentication` a `UseAuthorization` mezi voláním `UseRouting` a `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="5a52a-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="5a52a-125">Tím se nastaví `HttpContext.User` vlastnost a spustí middleware autorizace pro požadavky:</span><span class="sxs-lookup"><span data-stu-id="5a52a-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="5a52a-126">Další informace o schématech ověřování najdete v tématu věnovaném [principům ověřování](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="5a52a-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="5a52a-127">Další informace o ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="5a52a-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="5a52a-128">Použít autorizaci</span><span class="sxs-lookup"><span data-stu-id="5a52a-128">Apply authorization</span></span>

<span data-ttu-id="5a52a-129">Otestujte konfiguraci ověřování aplikace použitím `AuthorizeAttribute` atributu na kontroler, akci nebo stránku.</span><span class="sxs-lookup"><span data-stu-id="5a52a-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="5a52a-130">Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="5a52a-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="5a52a-131">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="5a52a-131">Sign out</span></span>

<span data-ttu-id="5a52a-132">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="5a52a-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="5a52a-133">Následující kód přidá `Logout` obslužnou rutinu stránky na stránku *indexu* :</span><span class="sxs-lookup"><span data-stu-id="5a52a-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="5a52a-134">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="5a52a-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="5a52a-135">Aplikace `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` je použita jako návratová.</span><span class="sxs-lookup"><span data-stu-id="5a52a-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5a52a-136">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5a52a-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5a52a-137"><xref:security/authentication/social/index>Popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="5a52a-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="5a52a-138">Přístup popsaný v tomto tématu zahrnuje ASP.NET Core Identity jako poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="5a52a-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="5a52a-139">Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="5a52a-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="5a52a-140">To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core Identity , ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.</span><span class="sxs-lookup"><span data-stu-id="5a52a-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="5a52a-141">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="5a52a-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="5a52a-142">Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google.</span><span class="sxs-lookup"><span data-stu-id="5a52a-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="5a52a-143">Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="5a52a-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="5a52a-144">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="5a52a-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="5a52a-145">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="5a52a-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="5a52a-146">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="5a52a-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="5a52a-147">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="5a52a-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="5a52a-148">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="5a52a-148">Configuration</span></span>

<span data-ttu-id="5a52a-149">V `ConfigureServices` metodě nakonfigurujte ověřovací schémata aplikace pomocí `AddAuthentication` `AddCookie` metod, a `AddGoogle` :</span><span class="sxs-lookup"><span data-stu-id="5a52a-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="5a52a-150">Volání [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) nastaví [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a52a-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="5a52a-151">`DefaultScheme`Je výchozí schéma používané následujícími `HttpContext` metodami rozšíření ověřování:</span><span class="sxs-lookup"><span data-stu-id="5a52a-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="5a52a-152">Nastavení aplikace `DefaultScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="5a52a-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="5a52a-153">Nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="5a52a-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="5a52a-154">`DefaultChallengeScheme`Přepisuje `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="5a52a-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="5a52a-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Další vlastnosti, které se při nastavení přepisují, najdete v tématu `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="5a52a-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="5a52a-156">V `Configure` metodě zavolejte `UseAuthentication` metodu pro vyvolání middleware ověřování, který nastaví `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5a52a-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="5a52a-157">Zavolejte `UseAuthentication` metodu před voláním `UseMvcWithDefaultRoute` nebo `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="5a52a-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="5a52a-158">Další informace o schématech ověřování najdete v tématu věnovaném [principům ověřování](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="5a52a-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="5a52a-159">Další informace o ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="5a52a-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="5a52a-160">Použít autorizaci</span><span class="sxs-lookup"><span data-stu-id="5a52a-160">Apply authorization</span></span>

<span data-ttu-id="5a52a-161">Otestujte konfiguraci ověřování aplikace použitím `AuthorizeAttribute` atributu na kontroler, akci nebo stránku.</span><span class="sxs-lookup"><span data-stu-id="5a52a-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="5a52a-162">Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="5a52a-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="5a52a-163">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="5a52a-163">Sign out</span></span>

<span data-ttu-id="5a52a-164">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="5a52a-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="5a52a-165">Následující kód přidá `Logout` obslužnou rutinu stránky na stránku *indexu* :</span><span class="sxs-lookup"><span data-stu-id="5a52a-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="5a52a-166">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="5a52a-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="5a52a-167">Aplikace `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` je použita jako návratová.</span><span class="sxs-lookup"><span data-stu-id="5a52a-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5a52a-168">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5a52a-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end

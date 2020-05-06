---
title: Ověřování přes Facebook, Google a externí poskytovatel bez ASP.NET CoreIdentity
author: rick-anderson
description: Vysvětlení použití Facebooku, Google, Twitteru atd. ověřování uživatelů účtu bez ASP.NET Core Identity.
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cc44eb83947540ca9a5a04ffad4fdb8522fab26a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775736"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="4e5bf-103">Použití ověřování poskytovatele přihlašování přes sociální sítě bez ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="4e5bf-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="4e5bf-104">Od [Kirka Larkin](https://twitter.com/serpent5) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4e5bf-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4e5bf-105"><xref:security/authentication/social/index>Popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="4e5bf-106">Přístup popsaný v tomto tématu zahrnuje ASP.NET Core Identity jako poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="4e5bf-107">Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="4e5bf-108">To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core Identity, ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="4e5bf-109">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="4e5bf-110">Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="4e5bf-111">Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="4e5bf-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="4e5bf-112">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="4e5bf-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="4e5bf-113">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="4e5bf-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="4e5bf-114">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="4e5bf-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="4e5bf-115">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="4e5bf-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="4e5bf-116">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="4e5bf-116">Configuration</span></span>

<span data-ttu-id="4e5bf-117">V `ConfigureServices` metodě nakonfigurujte ověřovací schémata aplikace pomocí metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>a: <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*></span><span class="sxs-lookup"><span data-stu-id="4e5bf-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="4e5bf-118">Volání pro <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="4e5bf-119">`DefaultScheme` Je výchozí schéma používané následujícími `HttpContext` metodami rozšíření ověřování:</span><span class="sxs-lookup"><span data-stu-id="4e5bf-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="4e5bf-120">Nastavení aplikace `DefaultScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="4e5bf-121">Nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="4e5bf-122">`DefaultChallengeScheme`Přepisuje `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="4e5bf-123">Další <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> vlastnosti, které se při `DefaultScheme` nastavení přepisují, najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="4e5bf-124">V `Startup.Configure`, zavolejte `UseAuthentication` a `UseAuthorization` mezi `UseRouting` voláním `UseEndpoints`a.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="4e5bf-125">Tím se `HttpContext.User` nastaví vlastnost a spustí middleware autorizace pro požadavky:</span><span class="sxs-lookup"><span data-stu-id="4e5bf-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="4e5bf-126">Další informace o schématech ověřování najdete v tématu věnovaném [principům ověřování](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="4e5bf-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="4e5bf-127">Další informace o ověřování souborů cookie najdete v <xref:security/authentication/cookie>tématu.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="4e5bf-128">Použít autorizaci</span><span class="sxs-lookup"><span data-stu-id="4e5bf-128">Apply authorization</span></span>

<span data-ttu-id="4e5bf-129">Otestujte konfiguraci ověřování aplikace použitím `AuthorizeAttribute` atributu na kontroler, akci nebo stránku.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="4e5bf-130">Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="4e5bf-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="4e5bf-131">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="4e5bf-131">Sign out</span></span>

<span data-ttu-id="4e5bf-132">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="4e5bf-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="4e5bf-133">Následující kód přidá obslužnou rutinu `Logout` stránky na stránku *indexu* :</span><span class="sxs-lookup"><span data-stu-id="4e5bf-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="4e5bf-134">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="4e5bf-135">Aplikace `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` je použita jako návratová.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e5bf-136">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4e5bf-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e5bf-137"><xref:security/authentication/social/index>Popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="4e5bf-138">Přístup popsaný v tomto tématu zahrnuje ASP.NET Core Identity jako poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="4e5bf-139">Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="4e5bf-140">To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core Identity, ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="4e5bf-141">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="4e5bf-142">Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="4e5bf-143">Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="4e5bf-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="4e5bf-144">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="4e5bf-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="4e5bf-145">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="4e5bf-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="4e5bf-146">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="4e5bf-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="4e5bf-147">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="4e5bf-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="4e5bf-148">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="4e5bf-148">Configuration</span></span>

<span data-ttu-id="4e5bf-149">V `ConfigureServices` metodě nakonfigurujte ověřovací schémata aplikace pomocí metod `AddAuthentication`, `AddCookie`a: `AddGoogle`</span><span class="sxs-lookup"><span data-stu-id="4e5bf-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="4e5bf-150">Volání [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) nastaví [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)aplikace.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="4e5bf-151">`DefaultScheme` Je výchozí schéma používané následujícími `HttpContext` metodami rozšíření ověřování:</span><span class="sxs-lookup"><span data-stu-id="4e5bf-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="4e5bf-152">Nastavení aplikace `DefaultScheme` na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="4e5bf-153">Nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="4e5bf-154">`DefaultChallengeScheme`Přepisuje `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="4e5bf-155">Další <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> vlastnosti, které se při `DefaultScheme` nastavení přepisují, najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="4e5bf-156">V `Configure` metodě zavolejte `UseAuthentication` metodu pro vyvolání middleware ověřování, který nastaví `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="4e5bf-157">Zavolejte `UseAuthentication` metodu před voláním `UseMvcWithDefaultRoute` nebo `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="4e5bf-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="4e5bf-158">Další informace o schématech ověřování najdete v tématu věnovaném [principům ověřování](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="4e5bf-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="4e5bf-159">Další informace o ověřování souborů cookie najdete v <xref:security/authentication/cookie>tématu.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="4e5bf-160">Použít autorizaci</span><span class="sxs-lookup"><span data-stu-id="4e5bf-160">Apply authorization</span></span>

<span data-ttu-id="4e5bf-161">Otestujte konfiguraci ověřování aplikace použitím `AuthorizeAttribute` atributu na kontroler, akci nebo stránku.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="4e5bf-162">Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="4e5bf-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="4e5bf-163">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="4e5bf-163">Sign out</span></span>

<span data-ttu-id="4e5bf-164">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="4e5bf-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="4e5bf-165">Následující kód přidá obslužnou rutinu `Logout` stránky na stránku *indexu* :</span><span class="sxs-lookup"><span data-stu-id="4e5bf-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="4e5bf-166">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="4e5bf-167">Aplikace `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` je použita jako návratová.</span><span class="sxs-lookup"><span data-stu-id="4e5bf-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e5bf-168">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4e5bf-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end

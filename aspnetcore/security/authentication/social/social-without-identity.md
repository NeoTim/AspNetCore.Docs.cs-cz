---
title: Ověřování přes Facebook, Google a externí poskytovatel bez ASP.NET Core identity
author: rick-anderson
description: Vysvětlení použití Facebooku, Google, Twitteru atd. ověřování uživatelů účtu bez ASP.NET Core identity.
ms.author: riande
ms.date: 12/10/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 612964ec9ed4975cdc81780dda3bac6cce96037f
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75359055"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="83bf3-103">Použití ověřování poskytovatele přihlašování přes sociální sítě bez ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="83bf3-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83bf3-104"><xref:security/authentication/social/index> popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="83bf3-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="83bf3-105">Přístup popsaný v tomto tématu zahrnuje ASP.NET Core identity jako poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="83bf3-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="83bf3-106">Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="83bf3-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="83bf3-107">To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core identity, ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.</span><span class="sxs-lookup"><span data-stu-id="83bf3-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="83bf3-108">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="83bf3-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="83bf3-109">Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google.</span><span class="sxs-lookup"><span data-stu-id="83bf3-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="83bf3-110">Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="83bf3-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="83bf3-111">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="83bf3-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="83bf3-112">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="83bf3-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="83bf3-113">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="83bf3-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="83bf3-114">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="83bf3-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="83bf3-115">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="83bf3-115">Configuration</span></span>

<span data-ttu-id="83bf3-116">V metodě `ConfigureServices` nakonfigurujte ověřovací schémata aplikace pomocí metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>a <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>:</span><span class="sxs-lookup"><span data-stu-id="83bf3-116">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="83bf3-117">Volání <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> nastaví <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>aplikace.</span><span class="sxs-lookup"><span data-stu-id="83bf3-117">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="83bf3-118">`DefaultScheme` je výchozí schéma používané následujícími metodami rozšíření ověřování `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="83bf3-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="83bf3-119">Nastavení `DefaultScheme` aplikace na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="83bf3-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="83bf3-120">Nastavení <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> aplikace na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="83bf3-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="83bf3-121">`DefaultChallengeScheme` Přepisuje `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="83bf3-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="83bf3-122">Další vlastnosti, které přepisují `DefaultScheme` při nastavení, najdete v tématu <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>.</span><span class="sxs-lookup"><span data-stu-id="83bf3-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="83bf3-123">V `Startup.Configure`volejte `UseAuthentication` a `UseAuthorization` mezi voláním `UseRouting` a `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="83bf3-123">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="83bf3-124">Tím se nastaví vlastnost `HttpContext.User` a spustí middleware autorizace pro požadavky:</span><span class="sxs-lookup"><span data-stu-id="83bf3-124">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="83bf3-125">Další informace o schématech ověřování najdete v tématu věnovaném [principům ověřování](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="83bf3-125">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="83bf3-126">Další informace o ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="83bf3-126">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="83bf3-127">Použít autorizaci</span><span class="sxs-lookup"><span data-stu-id="83bf3-127">Apply authorization</span></span>

<span data-ttu-id="83bf3-128">Otestujte konfiguraci ověřování aplikace použitím atributu `AuthorizeAttribute` na kontroler, akci nebo stránku.</span><span class="sxs-lookup"><span data-stu-id="83bf3-128">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="83bf3-129">Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="83bf3-129">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="83bf3-130">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="83bf3-130">Sign out</span></span>

<span data-ttu-id="83bf3-131">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="83bf3-131">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="83bf3-132">Následující kód přidá obslužnou rutinu stránky `Logout` do stránky *indexu* :</span><span class="sxs-lookup"><span data-stu-id="83bf3-132">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="83bf3-133">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="83bf3-133">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="83bf3-134">`DefaultScheme` aplikace `CookieAuthenticationDefaults.AuthenticationScheme` se používá jako vrácení.</span><span class="sxs-lookup"><span data-stu-id="83bf3-134">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83bf3-135">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="83bf3-135">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83bf3-136"><xref:security/authentication/social/index> popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="83bf3-136"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="83bf3-137">Přístup popsaný v tomto tématu zahrnuje ASP.NET Core identity jako poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="83bf3-137">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="83bf3-138">Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="83bf3-138">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="83bf3-139">To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core identity, ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.</span><span class="sxs-lookup"><span data-stu-id="83bf3-139">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="83bf3-140">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="83bf3-140">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="83bf3-141">Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google.</span><span class="sxs-lookup"><span data-stu-id="83bf3-141">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="83bf3-142">Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="83bf3-142">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="83bf3-143">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="83bf3-143">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="83bf3-144">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="83bf3-144">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="83bf3-145">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="83bf3-145">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="83bf3-146">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="83bf3-146">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="83bf3-147">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="83bf3-147">Configuration</span></span>

<span data-ttu-id="83bf3-148">V metodě `ConfigureServices` nakonfigurujte ověřovací schémata aplikace pomocí metod `AddAuthentication`, `AddCookie`a `AddGoogle`:</span><span class="sxs-lookup"><span data-stu-id="83bf3-148">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="83bf3-149">Volání [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) nastaví [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)aplikace.</span><span class="sxs-lookup"><span data-stu-id="83bf3-149">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="83bf3-150">`DefaultScheme` je výchozí schéma používané následujícími metodami rozšíření ověřování `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="83bf3-150">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="83bf3-151">Nastavení `DefaultScheme` aplikace na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="83bf3-151">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="83bf3-152">Nastavení <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> aplikace na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="83bf3-152">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="83bf3-153">`DefaultChallengeScheme` Přepisuje `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="83bf3-153">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="83bf3-154">Další vlastnosti, které přepisují `DefaultScheme` při nastavení, najdete v tématu <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>.</span><span class="sxs-lookup"><span data-stu-id="83bf3-154">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="83bf3-155">V metodě `Configure` voláním metody `UseAuthentication` volejte middleware ověřování, který nastaví vlastnost `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="83bf3-155">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="83bf3-156">Před voláním `UseMvcWithDefaultRoute` nebo `UseMvc`volejte metodu `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="83bf3-156">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="83bf3-157">Další informace o schématech ověřování najdete v tématu věnovaném [principům ověřování](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="83bf3-157">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="83bf3-158">Další informace o ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="83bf3-158">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="83bf3-159">Použít autorizaci</span><span class="sxs-lookup"><span data-stu-id="83bf3-159">Apply authorization</span></span>

<span data-ttu-id="83bf3-160">Otestujte konfiguraci ověřování aplikace použitím atributu `AuthorizeAttribute` na kontroler, akci nebo stránku.</span><span class="sxs-lookup"><span data-stu-id="83bf3-160">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="83bf3-161">Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="83bf3-161">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="83bf3-162">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="83bf3-162">Sign out</span></span>

<span data-ttu-id="83bf3-163">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="83bf3-163">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="83bf3-164">Následující kód přidá obslužnou rutinu stránky `Logout` do stránky *indexu* :</span><span class="sxs-lookup"><span data-stu-id="83bf3-164">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="83bf3-165">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="83bf3-165">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="83bf3-166">`DefaultScheme` aplikace `CookieAuthenticationDefaults.AuthenticationScheme` se používá jako vrácení.</span><span class="sxs-lookup"><span data-stu-id="83bf3-166">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83bf3-167">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="83bf3-167">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end

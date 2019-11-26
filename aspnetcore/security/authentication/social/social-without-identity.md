---
title: Ověřování přes Facebook, Google a externí poskytovatel bez ASP.NET Core identity
author: rick-anderson
description: Vysvětlení použití Facebooku, Google, Twitteru atd. ověřování uživatelů účtu bez ASP.NET Core identity.
ms.author: riande
ms.date: 11/19/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 680ea091dcc5ed7f94879b5d277e8be7e5abeb7b
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289119"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="16d47-103">Použití ověřování poskytovatele přihlašování přes sociální sítě bez ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="16d47-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="16d47-104"><xref:security/authentication/social/index> popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="16d47-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="16d47-105">Přístup popsaný v tomto tématu zahrnuje ASP.NET Core identity jako poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="16d47-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="16d47-106">Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="16d47-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="16d47-107">To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core identity, ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.</span><span class="sxs-lookup"><span data-stu-id="16d47-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="16d47-108">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="16d47-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="16d47-109">Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google.</span><span class="sxs-lookup"><span data-stu-id="16d47-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="16d47-110">Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="16d47-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="16d47-111">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="16d47-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="16d47-112">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="16d47-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="16d47-113">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="16d47-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="16d47-114">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="16d47-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="16d47-115">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="16d47-115">Configuration</span></span>

<span data-ttu-id="16d47-116">V metodě `ConfigureServices` nakonfigurujte ověřovací schémata aplikace pomocí metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>a <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>:</span><span class="sxs-lookup"><span data-stu-id="16d47-116">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="16d47-117">Volání <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> nastaví <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>aplikace.</span><span class="sxs-lookup"><span data-stu-id="16d47-117">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="16d47-118">`DefaultScheme` je výchozí schéma používané následujícími metodami rozšíření ověřování `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="16d47-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="16d47-119">Nastavení `DefaultScheme` aplikace na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="16d47-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="16d47-120">Nastavení <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> aplikace na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="16d47-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="16d47-121">`DefaultChallengeScheme` Přepisuje `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="16d47-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="16d47-122">Další vlastnosti, které přepisují `DefaultScheme` při nastavení, najdete v tématu <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>.</span><span class="sxs-lookup"><span data-stu-id="16d47-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="16d47-123">V `Startup.Configure`volejte `UseAuthentication` a `UseAuthorization` mezi voláním `UseRouting` a `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="16d47-123">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="16d47-124">Tím se nastaví vlastnost `HttpContext.User` a spustí middleware autorizace pro požadavky:</span><span class="sxs-lookup"><span data-stu-id="16d47-124">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="16d47-125">Další informace o schématech ověřování a ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="16d47-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="16d47-126">Použít autorizaci</span><span class="sxs-lookup"><span data-stu-id="16d47-126">Apply authorization</span></span>

<span data-ttu-id="16d47-127">Otestujte konfiguraci ověřování aplikace použitím atributu `AuthorizeAttribute` na kontroler, akci nebo stránku.</span><span class="sxs-lookup"><span data-stu-id="16d47-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="16d47-128">Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="16d47-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="16d47-129">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="16d47-129">Sign out</span></span>

<span data-ttu-id="16d47-130">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="16d47-130">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="16d47-131">Následující kód přidá obslužnou rutinu stránky `Logout` do stránky *indexu* :</span><span class="sxs-lookup"><span data-stu-id="16d47-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="16d47-132">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="16d47-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="16d47-133">`DefaultScheme` aplikace `CookieAuthenticationDefaults.AuthenticationScheme` se používá jako vrácení.</span><span class="sxs-lookup"><span data-stu-id="16d47-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16d47-134">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="16d47-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="16d47-135"><xref:security/authentication/social/index> popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="16d47-135"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="16d47-136">Přístup popsaný v tomto tématu zahrnuje ASP.NET Core identity jako poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="16d47-136">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="16d47-137">Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="16d47-137">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="16d47-138">To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core identity, ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.</span><span class="sxs-lookup"><span data-stu-id="16d47-138">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="16d47-139">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="16d47-139">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="16d47-140">Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google.</span><span class="sxs-lookup"><span data-stu-id="16d47-140">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="16d47-141">Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="16d47-141">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="16d47-142">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="16d47-142">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="16d47-143">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="16d47-143">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="16d47-144">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="16d47-144">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="16d47-145">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="16d47-145">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="16d47-146">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="16d47-146">Configuration</span></span>

<span data-ttu-id="16d47-147">V metodě `ConfigureServices` nakonfigurujte ověřovací schémata aplikace pomocí metod `AddAuthentication`, `AddCookie`a `AddGoogle`:</span><span class="sxs-lookup"><span data-stu-id="16d47-147">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="16d47-148">Volání [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) nastaví [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)aplikace.</span><span class="sxs-lookup"><span data-stu-id="16d47-148">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="16d47-149">`DefaultScheme` je výchozí schéma používané následujícími metodami rozšíření ověřování `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="16d47-149">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="16d47-150">Nastavení `DefaultScheme` aplikace na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="16d47-150">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="16d47-151">Nastavení <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> aplikace na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="16d47-151">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="16d47-152">`DefaultChallengeScheme` Přepisuje `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="16d47-152">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="16d47-153">Další vlastnosti, které přepisují `DefaultScheme` při nastavení, najdete v tématu <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>.</span><span class="sxs-lookup"><span data-stu-id="16d47-153">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="16d47-154">V metodě `Configure` voláním metody `UseAuthentication` volejte middleware ověřování, který nastaví vlastnost `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="16d47-154">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="16d47-155">Před voláním `UseMvcWithDefaultRoute` nebo `UseMvc`volejte metodu `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="16d47-155">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="16d47-156">Další informace o schématech ověřování a ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="16d47-156">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="16d47-157">Použít autorizaci</span><span class="sxs-lookup"><span data-stu-id="16d47-157">Apply authorization</span></span>

<span data-ttu-id="16d47-158">Otestujte konfiguraci ověřování aplikace použitím atributu `AuthorizeAttribute` na kontroler, akci nebo stránku.</span><span class="sxs-lookup"><span data-stu-id="16d47-158">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="16d47-159">Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="16d47-159">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="16d47-160">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="16d47-160">Sign out</span></span>

<span data-ttu-id="16d47-161">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="16d47-161">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="16d47-162">Následující kód přidá obslužnou rutinu stránky `Logout` do stránky *indexu* :</span><span class="sxs-lookup"><span data-stu-id="16d47-162">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="16d47-163">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="16d47-163">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="16d47-164">`DefaultScheme` aplikace `CookieAuthenticationDefaults.AuthenticationScheme` se používá jako vrácení.</span><span class="sxs-lookup"><span data-stu-id="16d47-164">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16d47-165">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="16d47-165">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end

---
title: Ověřování přes Facebook, Google a externí poskytovatel bez ASP.NET Core identity
author: rick-anderson
description: Vysvětlení použití Facebooku, Google, Twitteru atd. ověřování uživatelů účtu bez ASP.NET Core identity.
ms.author: riande
ms.date: 09/25/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 54dd93a13b2f7ed09c2c305f529d5f4610567184
ms.sourcegitcommit: 6d26ab647ede4f8e57465e29b03be5cb130fc872
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71999897"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="f6bc5-103">Použití ověřování poskytovatele přihlašování přes sociální sítě bez ASP.NET Core identity</span><span class="sxs-lookup"><span data-stu-id="f6bc5-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f6bc5-104"><xref:security/authentication/social/index> popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="f6bc5-105">Přístup popsaný v tomto tématu zahrnuje ASP.NET Core identity jako poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="f6bc5-106">Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="f6bc5-107">To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core identity, ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="f6bc5-108">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="f6bc5-109">Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="f6bc5-110">Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="f6bc5-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="f6bc5-111">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="f6bc5-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f6bc5-112">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="f6bc5-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f6bc5-113">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="f6bc5-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f6bc5-114">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="f6bc5-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="f6bc5-115">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="f6bc5-115">Configuration</span></span>

<span data-ttu-id="f6bc5-116">V metodě `ConfigureServices` nakonfigurujte ověřovací schémata aplikace pomocí metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> a <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>:</span><span class="sxs-lookup"><span data-stu-id="f6bc5-116">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/3.0sample/Startup.cs?name=snippet1)]

<span data-ttu-id="f6bc5-117">Volání <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> nastaví <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> aplikace.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-117">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="f6bc5-118">@No__t-0 je výchozí schéma používané následujícími metodami rozšíření ověřování `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="f6bc5-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="f6bc5-119">Nastavení @no__t aplikace-0 na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="f6bc5-120">Nastavení @no__tu aplikace-0 na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="f6bc5-121">@no__t – 0 Přepisuje `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="f6bc5-122">Další vlastnosti, které při nastavení přepisují `DefaultScheme`, naleznete v části <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="f6bc5-123">V `Startup.Configure` zavolejte `UseAuthentication` a `UseAuthorization` a nastavte vlastnost `HttpContext.User` a spusťte middleware autorizace pro požadavky.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-123">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="f6bc5-124">Před voláním `UseEndpoints` zavolejte metody `UseAuthentication` a `UseAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="f6bc5-124">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](social-without-identity/3.0sample/Startup.cs?name=snippet2)]

<span data-ttu-id="f6bc5-125">Další informace o schématech ověřování a ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="f6bc5-126">Použít autorizaci</span><span class="sxs-lookup"><span data-stu-id="f6bc5-126">Apply authorization</span></span>

<span data-ttu-id="f6bc5-127">Otestujte konfiguraci ověřování aplikace, a to použitím atributu `AuthorizeAttribute` na kontroler, akci nebo stránku.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="f6bc5-128">Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="f6bc5-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/3.0sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="f6bc5-129">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="f6bc5-129">Sign out</span></span>

<span data-ttu-id="f6bc5-130">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="f6bc5-130">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="f6bc5-131">Následující kód přidá obslužnou rutinu stránky `Logout` na stránku *indexu* :</span><span class="sxs-lookup"><span data-stu-id="f6bc5-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/3.0sample/Pages/Index.cshtml.cs?name=snippet&highlight=14-18)]

<span data-ttu-id="f6bc5-132">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="f6bc5-133">Aplikace `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` se používá jako vrácení zpět.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6bc5-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f6bc5-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f6bc5-135"><xref:security/authentication/social/index> popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-135"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="f6bc5-136">Přístup popsaný v tomto tématu zahrnuje ASP.NET Core identity jako poskytovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-136">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="f6bc5-137">Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-137">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="f6bc5-138">To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core identity, ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-138">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="f6bc5-139">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-139">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="f6bc5-140">Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-140">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="f6bc5-141">Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="f6bc5-141">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="f6bc5-142">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="f6bc5-142">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="f6bc5-143">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="f6bc5-143">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="f6bc5-144">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="f6bc5-144">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="f6bc5-145">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="f6bc5-145">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="f6bc5-146">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="f6bc5-146">Configuration</span></span>

<span data-ttu-id="f6bc5-147">V metodě `ConfigureServices` nakonfigurujte ověřovací schémata aplikace pomocí metod `AddAuthentication`, `AddCookie` a `AddGoogle`:</span><span class="sxs-lookup"><span data-stu-id="f6bc5-147">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="f6bc5-148">Volání [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) nastaví [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)aplikace.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-148">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="f6bc5-149">@No__t-0 je výchozí schéma používané následujícími metodami rozšíření ověřování `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="f6bc5-149">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="f6bc5-150">Nastavení @no__t aplikace-0 na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-150">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="f6bc5-151">Nastavení @no__tu aplikace-0 na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-151">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="f6bc5-152">@no__t – 0 Přepisuje `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-152">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="f6bc5-153">Další vlastnosti, které při nastavení přepisují `DefaultScheme`, naleznete v části <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-153">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="f6bc5-154">V metodě `Configure` volejte metodu `UseAuthentication` pro vyvolání middleware ověřování, který nastaví vlastnost `HttpContext.User`.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-154">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="f6bc5-155">Před voláním `UseMvcWithDefaultRoute` nebo `UseMvc` volejte metodu `UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f6bc5-155">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="f6bc5-156">Další informace o schématech ověřování a ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-156">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="f6bc5-157">Použít autorizaci</span><span class="sxs-lookup"><span data-stu-id="f6bc5-157">Apply authorization</span></span>

<span data-ttu-id="f6bc5-158">Otestujte konfiguraci ověřování aplikace, a to použitím atributu `AuthorizeAttribute` na kontroler, akci nebo stránku.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-158">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="f6bc5-159">Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="f6bc5-159">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="f6bc5-160">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="f6bc5-160">Sign out</span></span>

<span data-ttu-id="f6bc5-161">Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="f6bc5-161">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="f6bc5-162">Následující kód přidá obslužnou rutinu stránky `Logout` na stránku *indexu* :</span><span class="sxs-lookup"><span data-stu-id="f6bc5-162">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

<span data-ttu-id="f6bc5-163">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-163">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="f6bc5-164">Aplikace `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` se používá jako vrácení zpět.</span><span class="sxs-lookup"><span data-stu-id="f6bc5-164">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f6bc5-165">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f6bc5-165">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end

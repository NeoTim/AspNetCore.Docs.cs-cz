---
title: Facebook, Google a externí zprostředkovatel ověřování bez ASP.NET Core Identity
author: rick-anderson
description: Vysvětlení, používání Facebook, Google, Twitter, ověřování uživatelů atd. účet bez ASP.NET Core Identity.
ms.author: riande
ms.date: 07/04/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: 1e7124e8b07c0faf2d005ec3ef55c0414a697d64
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561571"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="2fbcc-103">Ověřování pomocí zprostředkovatele sociální přihlásit bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2fbcc-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="2fbcc-104"><xref:security/authentication/social/index> Popisuje, jak povolit uživatelům přihlášení pomocí přihlašovacích údajů z externího zprostředkovatele ověřování OAuth 2.0.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="2fbcc-105">Přístup je popsáno v tomto tématu zahrnuje ASP.NET Core Identity jako zprostředkovatel ověřování.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="2fbcc-106">Tato ukázka předvádí, jak používat externí zprostředkovatel ověřování **bez** ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="2fbcc-107">To je užitečné pro aplikace, které nevyžadují všechny funkce technologie ASP.NET Core Identity, ale stále potřebuje k fungování integraci se zprostředkovatelem důvěryhodné externího ověřování.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="2fbcc-108">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) za účelem ověřování totožnosti uživatelů.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="2fbcc-109">Pomocí služby Google posune ověřování mnoho složitých úkolů při správě procesu přihlášení ke Googlu.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="2fbcc-110">Integrovat různých externího zprostředkovatele ověřování, naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="2fbcc-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="2fbcc-111">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="2fbcc-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="2fbcc-112">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="2fbcc-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="2fbcc-113">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="2fbcc-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="2fbcc-114">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="2fbcc-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="2fbcc-115">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="2fbcc-115">Configuration</span></span>

<span data-ttu-id="2fbcc-116">V `ConfigureServices` metody konfigurace schémat ověřování aplikaci s `AddAuthentication`, `AddCookie` a `AddGoogle` metody:</span><span class="sxs-lookup"><span data-stu-id="2fbcc-116">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie` and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="2fbcc-117">Volání [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) nastaví aplikace [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span><span class="sxs-lookup"><span data-stu-id="2fbcc-117">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="2fbcc-118">`DefaultScheme` Je výchozí schéma používá následující `HttpContext` rozšiřující metody ověřování:</span><span class="sxs-lookup"><span data-stu-id="2fbcc-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="2fbcc-119">Nastavení aplikace `DefaultScheme` k [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (soubory cookie.") nakonfiguruje aplikaci používat soubory cookie jako výchozí schéma pro tyto metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="2fbcc-120">Nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> k [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="2fbcc-121">`DefaultChallengeScheme` přepíše `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="2fbcc-122">Zobrazit <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> pro další vlastnosti, které přepíší `DefaultScheme` při nastavení.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="2fbcc-123">V `Configure` metody, volání `UseAuthentication` metoda k vyvolání ověřovací Middleware, který nastaví `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-123">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="2fbcc-124">Volání `UseAuthentication` před voláním metody `UseMvcWithDefaultRoute` nebo `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="2fbcc-124">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="2fbcc-125">Další informace o schémat ověřování a ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="applying-authorization"></a><span data-ttu-id="2fbcc-126">Použití autorizace</span><span class="sxs-lookup"><span data-stu-id="2fbcc-126">Applying authorization</span></span>

<span data-ttu-id="2fbcc-127">Otestujte konfiguraci ověřování aplikace s použitím `AuthorizeAttribute` atribut pro kontroler, akci nebo stránky.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="2fbcc-128">Následující kód omezuje přístup *ochrany osobních údajů* stránky pro uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="2fbcc-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="2fbcc-129">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="2fbcc-129">Sign out</span></span>

<span data-ttu-id="2fbcc-130">Chcete-li odhlásit aktuálního uživatele a odstranit jejich souborů cookie, zavolejte [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="2fbcc-130">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0).</span></span> <span data-ttu-id="2fbcc-131">Přidá následující kód `Logout` obslužná rutina stránky *Index* stránky:</span><span class="sxs-lookup"><span data-stu-id="2fbcc-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

<span data-ttu-id="2fbcc-132">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="2fbcc-133">Aplikace `DefaultScheme` z `CookieAuthenticationDefaults.AuthenticationScheme` slouží jako propad zpět.</span><span class="sxs-lookup"><span data-stu-id="2fbcc-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fbcc-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2fbcc-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

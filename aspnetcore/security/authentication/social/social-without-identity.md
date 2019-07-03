---
title: Facebook, Google a externí zprostředkovatel ověřování bez ASP.NET Core Identity
author: rick-anderson
description: Vysvětlení, používání Facebook, Google, Twitter, ověřování uživatelů atd. účet bez ASP.NET Core Identity.
ms.author: riande
ms.date: 07/04/2019
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: e67da513fef1ce453110c465b08e9c7965e71df5
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67557665"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="be686-103">Ověřování pomocí zprostředkovatele sociální přihlásit bez ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="be686-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="be686-104"><xref:security/authentication/social/index> Popisuje, jak povolit uživatelům přihlášení pomocí přihlašovacích údajů z externího zprostředkovatele ověřování OAuth 2.0.</span><span class="sxs-lookup"><span data-stu-id="be686-104"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="be686-105">Přístup je popsáno v tomto tématu zahrnuje ASP.NET Core Identity jako zprostředkovatel ověřování.</span><span class="sxs-lookup"><span data-stu-id="be686-105">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="be686-106">Tato ukázka předvádí, jak používat externí zprostředkovatel ověřování **bez** ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="be686-106">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="be686-107">To je užitečné pro aplikace, které nevyžadují všechny funkce technologie ASP.NET Core Identity, ale stále potřebuje k fungování integraci se zprostředkovatelem důvěryhodné externího ověřování.</span><span class="sxs-lookup"><span data-stu-id="be686-107">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="be686-108">Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) za účelem ověřování totožnosti uživatelů.</span><span class="sxs-lookup"><span data-stu-id="be686-108">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="be686-109">Pomocí služby Google posune ověřování mnoho složitých úkolů při správě procesu přihlášení ke Googlu.</span><span class="sxs-lookup"><span data-stu-id="be686-109">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="be686-110">Integrovat různých externího zprostředkovatele ověřování, naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="be686-110">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="be686-111">Ověřování Facebooku</span><span class="sxs-lookup"><span data-stu-id="be686-111">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="be686-112">Ověřování Microsoftu</span><span class="sxs-lookup"><span data-stu-id="be686-112">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="be686-113">Ověřování Twitteru</span><span class="sxs-lookup"><span data-stu-id="be686-113">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="be686-114">Další zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="be686-114">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="be686-115">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="be686-115">Configuration</span></span>

<span data-ttu-id="be686-116">V `ConfigureServices` metody konfigurace schémat ověřování aplikaci s `AddAuthentication`, `AddCookie` a `AddGoogle` metody:</span><span class="sxs-lookup"><span data-stu-id="be686-116">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie` and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="be686-117">Volání [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) nastaví aplikace [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span><span class="sxs-lookup"><span data-stu-id="be686-117">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="be686-118">`DefaultScheme` Je výchozí schéma používá následující `HttpContext` rozšiřující metody ověřování:</span><span class="sxs-lookup"><span data-stu-id="be686-118">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="be686-119">Nastavení aplikace `DefaultScheme` k [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (soubory cookie.") nakonfiguruje aplikaci používat soubory cookie jako výchozí schéma pro tyto metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="be686-119">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="be686-120">Nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> k [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="be686-120">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="be686-121">`DefaultChallengeScheme` přepíše `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="be686-121">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="be686-122">Zobrazit <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> pro další vlastnosti, které přepíší `DefaultScheme` při nastavení.</span><span class="sxs-lookup"><span data-stu-id="be686-122">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="be686-123">V `Configure` metody, volání `UseAuthentication` metoda k vyvolání ověřovací Middleware, který nastaví `HttpContext.User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="be686-123">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="be686-124">Volání `UseAuthentication` před voláním metody `UseMvcWithDefaultRoute` nebo `UseMvc`:</span><span class="sxs-lookup"><span data-stu-id="be686-124">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="be686-125">Další informace o schémat ověřování a ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie>.</span><span class="sxs-lookup"><span data-stu-id="be686-125">To learn more about authentication schemes and cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="applying-basic-authorization"></a><span data-ttu-id="be686-126">Použití základního ověřování</span><span class="sxs-lookup"><span data-stu-id="be686-126">Applying basic authorization</span></span>

<span data-ttu-id="be686-127">Otestujte konfiguraci ověřování aplikace s použitím `AuthorizeAttribute` atribut pro kontroler, akci nebo stránky.</span><span class="sxs-lookup"><span data-stu-id="be686-127">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="be686-128">Následující kód omezuje přístup *ochrany osobních údajů* stránky pro uživatele, kteří byli ověřeni:</span><span class="sxs-lookup"><span data-stu-id="be686-128">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="be686-129">Odhlásit se</span><span class="sxs-lookup"><span data-stu-id="be686-129">Sign out</span></span>

<span data-ttu-id="be686-130">Chcete-li odhlásit aktuálního uživatele a odstranit jejich souborů cookie, zavolejte [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="be686-130">To sign out the current user and delete their cookie, call [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0).</span></span> <span data-ttu-id="be686-131">Přidá následující kód `Logout` obslužná rutina stránky *Index* stránky:</span><span class="sxs-lookup"><span data-stu-id="be686-131">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

<span data-ttu-id="be686-132">Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování.</span><span class="sxs-lookup"><span data-stu-id="be686-132">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="be686-133">Aplikace `DefaultScheme` z `CookieAuthenticationDefaults.AuthenticationScheme` slouží jako propad zpět.</span><span class="sxs-lookup"><span data-stu-id="be686-133">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be686-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="be686-134">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

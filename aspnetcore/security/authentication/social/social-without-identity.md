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
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Ověřování pomocí zprostředkovatele sociální přihlásit bez ASP.NET Core Identity

<xref:security/authentication/social/index> Popisuje, jak povolit uživatelům přihlášení pomocí přihlašovacích údajů z externího zprostředkovatele ověřování OAuth 2.0. Přístup je popsáno v tomto tématu zahrnuje ASP.NET Core Identity jako zprostředkovatel ověřování.

Tato ukázka předvádí, jak používat externí zprostředkovatel ověřování **bez** ASP.NET Core Identity. To je užitečné pro aplikace, které nevyžadují všechny funkce technologie ASP.NET Core Identity, ale stále potřebuje k fungování integraci se zprostředkovatelem důvěryhodné externího ověřování.

Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) za účelem ověřování totožnosti uživatelů. Pomocí služby Google posune ověřování mnoho složitých úkolů při správě procesu přihlášení ke Googlu. Integrovat různých externího zprostředkovatele ověřování, naleznete v následujících tématech:

* [Ověřování Facebooku](xref:security/authentication/facebook-logins)
* [Ověřování Microsoftu](xref:security/authentication/microsoft-logins)
* [Ověřování Twitteru](xref:security/authentication/twitter-logins)
* [Další zprostředkovatelé](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguraci

V `ConfigureServices` metody konfigurace schémat ověřování aplikaci s `AddAuthentication`, `AddCookie` a `AddGoogle` metody:

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

Volání [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) nastaví aplikace [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme). `DefaultScheme` Je výchozí schéma používá následující `HttpContext` rozšiřující metody ověřování:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Nastavení aplikace `DefaultScheme` k [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (soubory cookie.") nakonfiguruje aplikaci používat soubory cookie jako výchozí schéma pro tyto metody rozšíření. Nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> k [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`. `DefaultChallengeScheme` přepíše `DefaultScheme`. Zobrazit <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> pro další vlastnosti, které přepíší `DefaultScheme` při nastavení.

V `Configure` metody, volání `UseAuthentication` metoda k vyvolání ověřovací Middleware, který nastaví `HttpContext.User` vlastnost. Volání `UseAuthentication` před voláním metody `UseMvcWithDefaultRoute` nebo `UseMvc`:

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

Další informace o schémat ověřování a ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie>.

## <a name="applying-authorization"></a>Použití autorizace

Otestujte konfiguraci ověřování aplikace s použitím `AuthorizeAttribute` atribut pro kontroler, akci nebo stránky. Následující kód omezuje přístup *ochrany osobních údajů* stránky pro uživatele, kteří byli ověřeni:

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Odhlásit se

Chcete-li odhlásit aktuálního uživatele a odstranit jejich souborů cookie, zavolejte [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0). Přidá následující kód `Logout` obslužná rutina stránky *Index* stránky:

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování. Aplikace `DefaultScheme` z `CookieAuthenticationDefaults.AuthenticationScheme` slouží jako propad zpět.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

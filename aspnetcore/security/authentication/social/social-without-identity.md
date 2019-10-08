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
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Použití ověřování poskytovatele přihlašování přes sociální sítě bez ASP.NET Core identity

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index> popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování. Přístup popsaný v tomto tématu zahrnuje ASP.NET Core identity jako poskytovatele ověřování.

Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core identity. To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core identity, ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.

Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů. Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google. Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:

* [Ověřování Facebooku](xref:security/authentication/facebook-logins)
* [Ověřování Microsoftu](xref:security/authentication/microsoft-logins)
* [Ověřování Twitteru](xref:security/authentication/twitter-logins)
* [Další zprostředkovatelé](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguraci

V metodě `ConfigureServices` nakonfigurujte ověřovací schémata aplikace pomocí metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> a <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*>:

[!code-csharp[](social-without-identity/3.0sample/Startup.cs?name=snippet1)]

Volání <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> nastaví <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> aplikace. @No__t-0 je výchozí schéma používané následujícími metodami rozšíření ověřování `HttpContext`:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Nastavení @no__t aplikace-0 na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie. Nastavení @no__tu aplikace-0 na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`. @no__t – 0 Přepisuje `DefaultScheme`. Další vlastnosti, které při nastavení přepisují `DefaultScheme`, naleznete v části <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>.

V `Startup.Configure` zavolejte `UseAuthentication` a `UseAuthorization` a nastavte vlastnost `HttpContext.User` a spusťte middleware autorizace pro požadavky. Před voláním `UseEndpoints` zavolejte metody `UseAuthentication` a `UseAuthorization`:

[!code-csharp[](social-without-identity/3.0sample/Startup.cs?name=snippet2)]

Další informace o schématech ověřování a ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie>.

## <a name="apply-authorization"></a>Použít autorizaci

Otestujte konfiguraci ověřování aplikace, a to použitím atributu `AuthorizeAttribute` na kontroler, akci nebo stránku. Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:

[!code-csharp[](social-without-identity/3.0sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Odhlásit se

Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Následující kód přidá obslužnou rutinu stránky `Logout` na stránku *indexu* :

[!code-csharp[](social-without-identity/3.0sample/Pages/Index.cshtml.cs?name=snippet&highlight=14-18)]

Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování. Aplikace `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` se používá jako vrácení zpět.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index> popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování. Přístup popsaný v tomto tématu zahrnuje ASP.NET Core identity jako poskytovatele ověřování.

Tato ukázka předvádí, jak použít externího zprostředkovatele ověřování **bez** ASP.NET Core identity. To je užitečné pro aplikace, které nevyžadují všechny funkce ASP.NET Core identity, ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.

Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů. Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google. Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:

* [Ověřování Facebooku](xref:security/authentication/facebook-logins)
* [Ověřování Microsoftu](xref:security/authentication/microsoft-logins)
* [Ověřování Twitteru](xref:security/authentication/twitter-logins)
* [Další zprostředkovatelé](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguraci

V metodě `ConfigureServices` nakonfigurujte ověřovací schémata aplikace pomocí metod `AddAuthentication`, `AddCookie` a `AddGoogle`:

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet1)]

Volání [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) nastaví [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)aplikace. @No__t-0 je výchozí schéma používané následujícími metodami rozšíření ověřování `HttpContext`:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Nastavení @no__t aplikace-0 na [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies") nakonfiguruje aplikaci tak, aby jako výchozí schéma pro tyto metody rozšíření používala soubory cookie. Nastavení @no__tu aplikace-0 na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync`. @no__t – 0 Přepisuje `DefaultScheme`. Další vlastnosti, které při nastavení přepisují `DefaultScheme`, naleznete v části <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>.

V metodě `Configure` volejte metodu `UseAuthentication` pro vyvolání middleware ověřování, který nastaví vlastnost `HttpContext.User`. Před voláním `UseMvcWithDefaultRoute` nebo `UseMvc` volejte metodu `UseAuthentication`:

[!code-csharp[](social-without-identity/sample/Startup.cs?name=snippet2)]

Další informace o schématech ověřování a ověřování souborů cookie najdete v tématu <xref:security/authentication/cookie>.

## <a name="apply-authorization"></a>Použít autorizaci

Otestujte konfiguraci ověřování aplikace, a to použitím atributu `AuthorizeAttribute` na kontroler, akci nebo stránku. Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:

[!code-csharp[](social-without-identity/sample/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Odhlásit se

Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Následující kód přidá obslužnou rutinu stránky `Logout` na stránku *indexu* :

[!code-csharp[](social-without-identity/sample/Pages/Index.cshtml.cs?name=snippet&highlight=7-11)]

Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování. Aplikace `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` se používá jako vrácení zpět.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end

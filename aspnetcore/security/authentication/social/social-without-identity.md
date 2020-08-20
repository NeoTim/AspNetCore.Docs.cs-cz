---
title: Ověřování pro Facebook, Google a externí poskytovatele bez ASP.NET Core Identity
author: rick-anderson
description: Vysvětlení použití Facebooku, Google, Twitteru atd. ověřování uživatelů účtu bez ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: a91a2f2fb7873e5a672c624e9cf863ae720c8005
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634225"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a>Použít ověřování poskytovatele přihlašování přes sociální sítě bez ASP.NET Core Identity

Od [Kirka Larkin](https://twitter.com/serpent5) a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index> Popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování. Přístup, který je popsaný v tomto tématu, zahrnuje ASP.NET Core Identity jako poskytovatele ověřování.

Tato ukázka předvádí, jak použít externího poskytovatele ověřování **bez** ASP.NET Core Identity . To je užitečné pro aplikace, které nevyžadují všechny funkce nástroje ASP.NET Core Identity , ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.

Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů. Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google. Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:

* [Ověřování Facebooku](xref:security/authentication/facebook-logins)
* [Ověřování Microsoftu](xref:security/authentication/microsoft-logins)
* [Ověřování Twitteru](xref:security/authentication/twitter-logins)
* [Další zprostředkovatelé](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfigurace

V `ConfigureServices` metodě nakonfigurujte ověřovací schémata aplikace pomocí <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod, a <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

Volání pro <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> . `DefaultScheme`Je výchozí schéma používané následujícími `HttpContext` metodami rozšíření ověřování:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Nastavení aplikace `DefaultScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") nakonfiguruje aplikaci tak, aby používala Cookie jako výchozí schéma pro tyto metody rozšíření. Nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync` . `DefaultChallengeScheme` Přepisuje `DefaultScheme` . <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Další vlastnosti, které se při nastavení přepisují, najdete v tématu `DefaultScheme` .

V `Startup.Configure` , zavolejte `UseAuthentication` a `UseAuthorization` mezi voláním `UseRouting` a `UseEndpoints` . Tím se nastaví `HttpContext.User` vlastnost a spustí middleware autorizace pro požadavky:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Další informace o schématech ověřování najdete v tématu věnovaném [principům ověřování](xref:security/authentication/index#authentication-concepts). Další informace o cookie ověřování najdete v tématu <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Použít autorizaci

Otestujte konfiguraci ověřování aplikace použitím `AuthorizeAttribute` atributu na kontroler, akci nebo stránku. Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Odhlásit se

Chcete-li odhlásit aktuálního uživatele a odstranit jeho cookie volání, zavolejte na [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Následující kód přidá `Logout` obslužnou rutinu stránky na stránku *indexu* :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování. Aplikace `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` je použita jako návratová.

## <a name="additional-resources"></a>Další zdroje informací

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index> Popisuje, jak povolit uživatelům přihlášení pomocí OAuth 2,0 s přihlašovacími údaji od externích zprostředkovatelů ověřování. Přístup, který je popsaný v tomto tématu, zahrnuje ASP.NET Core Identity jako poskytovatele ověřování.

Tato ukázka předvádí, jak použít externího poskytovatele ověřování **bez** ASP.NET Core Identity . To je užitečné pro aplikace, které nevyžadují všechny funkce nástroje ASP.NET Core Identity , ale stále vyžadují integraci s důvěryhodným externím poskytovatelem ověřování.

Tato ukázka používá [ověřování Google](xref:security/authentication/google-logins) pro ověřování uživatelů. Pomocí ověřování Google se posunou mnohé ze složitých procesů při správě procesu přihlašování do Google. Informace o integraci s jiným externím poskytovatelem ověřování naleznete v následujících tématech:

* [Ověřování Facebooku](xref:security/authentication/facebook-logins)
* [Ověřování Microsoftu](xref:security/authentication/microsoft-logins)
* [Ověřování Twitteru](xref:security/authentication/twitter-logins)
* [Další zprostředkovatelé](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfigurace

V `ConfigureServices` metodě nakonfigurujte ověřovací schémata aplikace pomocí `AddAuthentication` `AddCookie` metod, a `AddGoogle` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

Volání [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) nastaví [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)aplikace. `DefaultScheme`Je výchozí schéma používané následujícími `HttpContext` metodami rozšíření ověřování:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Nastavení aplikace `DefaultScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") nakonfiguruje aplikaci tak, aby používala Cookie jako výchozí schéma pro tyto metody rozšíření. Nastavení aplikace <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> na [GoogleDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") nakonfiguruje aplikaci tak, aby používala Google jako výchozí schéma pro volání `ChallengeAsync` . `DefaultChallengeScheme` Přepisuje `DefaultScheme` . <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Další vlastnosti, které se při nastavení přepisují, najdete v tématu `DefaultScheme` .

V `Configure` metodě zavolejte `UseAuthentication` metodu pro vyvolání middleware ověřování, který nastaví `HttpContext.User` vlastnost. Zavolejte `UseAuthentication` metodu před voláním `UseMvcWithDefaultRoute` nebo `UseMvc` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Další informace o schématech ověřování najdete v tématu věnovaném [principům ověřování](xref:security/authentication/index#authentication-concepts). Další informace o cookie ověřování najdete v tématu <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Použít autorizaci

Otestujte konfiguraci ověřování aplikace použitím `AuthorizeAttribute` atributu na kontroler, akci nebo stránku. Následující kód omezuje přístup na stránku *ochrany osobních údajů* na uživatele, kteří byli ověřeni:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Odhlásit se

Chcete-li odhlásit aktuálního uživatele a odstranit jeho cookie volání, zavolejte na [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Následující kód přidá `Logout` obslužnou rutinu stránky na stránku *indexu* :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Všimněte si, že volání `SignOutAsync` neurčuje schéma ověřování. Aplikace `DefaultScheme` `CookieAuthenticationDefaults.AuthenticationScheme` je použita jako návratová.

## <a name="additional-resources"></a>Další materiály

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end

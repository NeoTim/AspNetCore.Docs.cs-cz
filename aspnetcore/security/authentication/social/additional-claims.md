---
title: Zachování další deklarace identity a tokeny od externích poskytovatelů v ASP.NET Core
author: guardrex
description: Zjistěte, jak vytvořit další deklarace identity a tokeny od externích poskytovatelů.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 49c323fab64bd4ea52dd1d8cf2e43a79d4d0d0dc
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327349"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Zachování další deklarace identity a tokeny od externích poskytovatelů v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Aplikace ASP.NET Core můžete vytvořit další deklarace identity a tokenů z externího zprostředkovatele ověřování, jako je Facebook, Google, Microsoft a Twitter. Každý poskytovatel odhalí různé informace o uživatelích na své platformě, ale vzor pro příjem a transformaci dat uživatele do dalších deklarací identity je stejný.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

Rozhodněte, které externího zprostředkovatele ověřování pro podporu v aplikaci. Pro každého zprostředkovatele aplikace registrovaly a získaly ID klienta a tajný kód klienta. Další informace naleznete v tématu <xref:security/authentication/social/index>. [Ukázkovou aplikaci](#sample-app-instructions) používá [zprostředkovatele ověřování Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Nastavte ID klienta a tajný kód klienta

Zprostředkovatele ověřování OAuth vytváří vztah důvěryhodnosti s aplikací pomocí ID klienta a tajný kód klienta. ID klienta a hodnoty tajných kódů klienta jsou vytvořené pro aplikaci zprostředkovatele externího ověřování při registraci aplikace s tímto poskytovatelem. Každý externího poskytovatele, který používá aplikace musí mít nakonfigurovanou nezávisle na sobě ID klienta a tajný kód klienta poskytovatele. Další informace najdete v tématech zprostředkovatele externího ověřování, které platí pro váš scénář:

* [Ověřování Facebooku](xref:security/authentication/facebook-logins)
* [Ověřování Googlu](xref:security/authentication/google-logins)
* [Ověřování Microsoftu](xref:security/authentication/microsoft-logins)
* [Ověřování Twitteru](xref:security/authentication/twitter-logins)
* [Další zprostředkovatelé ověřování](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Ukázková aplikace nakonfiguruje zprostředkovatele ověřování Google s ID klienta a tajný kód klienta, které jsou k dispozici společností Google:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,6)]

## <a name="establish-the-authentication-scope"></a>Vytvořit rozsah ověřování

Zadejte seznam oprávnění k načtení z poskytovatele tak, že zadáte <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>. V následující tabulce se zobrazí obory ověřování pro běžné externího poskytovatele.

| Poskytovatel  | Rozsah                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/plus.login`                     |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

Ukázková aplikace přidá ke službě Google `plus.login` oboru požadavku Google + přihlášení oprávnění:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=7)]

## <a name="map-user-data-keys-and-create-claims"></a>Mapování uživatelských dat klíčů a vytvářet deklarace identity

V možnostech zprostředkovatele, zadejte <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> pro každý klíč externího poskytovatele JSON uživatelské údaje pro identitu aplikace ke čtení na přihlášení. Další informace o typy deklarací identity najdete v tématu <xref:System.Security.Claims.ClaimTypes>.

Vytvoří ukázkovou aplikaci <xref:System.Security.Claims.ClaimTypes.Gender> deklarace identity z `gender` klíče v Google uživatelská data:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=8)]

V <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) je přihlášený k aplikaci s <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>. Během procesu, přihlašování <xref:Microsoft.AspNetCore.Identity.UserManager%601> můžete ukládat `ApplicationUser` deklarací identity pro uživatele nejsou k dispozici data <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.

V ukázkové aplikaci `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) vytváří <xref:System.Security.Claims.ClaimTypes.Gender> deklarací identity pro podepsané v `ApplicationUser`:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=30-31)]

## <a name="save-the-access-token"></a>Uložit přístupový token

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definuje, jestli tokeny přístupu a aktualizace by měla být uložena v <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po úspěšném ověření. `SaveTokens` je nastavena na `false` ve výchozím nastavení ke zmenšení velikosti konečné ověřovacího souboru cookie.

Ukázková aplikace nastaví hodnotu `SaveTokens` k `true` v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=9)]

Když `OnPostConfirmationAsync` spustí, ukládat přístupový token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) z externího poskytovatele v `ApplicationUser`společnosti `AuthenticationProperties`.

Ukázková aplikace uloží přístupového tokenu v:

* `OnPostConfirmationAsync` &ndash; Spustí pro nové registrace uživatele.
* `OnGetCallbackAsync` &ndash; Spustí, když se dříve zaregistrovaný uživatel přihlásí do aplikace.

*Account/ExternalLogin.cshtml.cs*:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=34-35)]

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnGetCallbackAsync&highlight=31-32)]

## <a name="how-to-add-additional-custom-tokens"></a>Jak přidat další vlastní tokeny

K předvedení jak přidat vlastní token, který je uložený jako součást `SaveTokens`, přidá ukázkovou aplikaci <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> s aktuálním <xref:System.DateTime> pro [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) z `TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/AdditionalClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=10-21)]

## <a name="sample-app-instructions"></a>Ukázku pokynů aplikace

Ukázková aplikace předvádí, jak:

* Získat pohlaví uživatele z Googlu a ukládat pohlaví deklaraci identity s hodnotou.
* Přístupový token Google Store v uživatele `AuthenticationProperties`.

Použití ukázkové aplikace:

1. Aplikace registrovaly a získaly platné ID klienta a tajný kód klienta pro ověřování Google. Další informace naleznete v tématu <xref:security/authentication/google-logins>.
1. Zadejte ID klienta a tajný kód klienta aplikace v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> z `Startup.ConfigureServices`.
1. Spusťte aplikaci a požadavku na stránku Moje deklarací identity. Když uživatel není přihlášen, aplikace přesměruje do Googlu. Přihlásit se přes Google. Google přesměruje uživatele zpět do aplikace (`/Home/MyClaims`). Ověření uživatele a načtení stránky Moje deklarací identity. Není k dispozici v rámci deklarace pohlaví **deklarace identity uživatelů** s hodnotou získanou z Google. Přístupový token se zobrazí v **vlastnosti ověřování**.

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    b36a7b09-9135-4810-b7a5-78697ff23e99
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    username@gmail.com
AspNet.Identity.SecurityStamp
    29G2TB881ATCUQFJSRFG1S0QJ0OOAWVT
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/gender
    female
http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod
    Google

Authentication Properties

.Token.access_token
    bv42.Dgw...GQMv9ArLPs
.Token.token_type
    Bearer
.Token.expires_at
    2018-08-27T19:08:00.0000000+00:00
.Token.TicketCreated
    8/27/2018 6:08:00 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.issued
    Mon, 27 Aug 2018 18:08:05 GMT
.expires
    Mon, 10 Sep 2018 18:08:05 GMT
```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

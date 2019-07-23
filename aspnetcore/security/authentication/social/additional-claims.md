---
title: Zachování další deklarace identity a tokeny od externích poskytovatelů v ASP.NET Core
author: guardrex
description: Zjistěte, jak vytvořit další deklarace identity a tokeny od externích poskytovatelů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: security/authentication/social/additional-claims
ms.openlocfilehash: e18287e5a4171b3f7a6daa122111448b8447c1da
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874848"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Zachování další deklarace identity a tokeny od externích poskytovatelů v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Aplikace ASP.NET Core můžete vytvořit další deklarace identity a tokenů z externího zprostředkovatele ověřování, jako je Facebook, Google, Microsoft a Twitter. Každý poskytovatel odhalí různé informace o uživatelích na své platformě, ale vzor pro příjem a transformaci dat uživatele do dalších deklarací identity je stejný.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

Rozhodněte, které externího zprostředkovatele ověřování pro podporu v aplikaci. Pro každého zprostředkovatele aplikace registrovaly a získaly ID klienta a tajný kód klienta. Další informace naleznete v tématu <xref:security/authentication/social/index>. Tato ukázková aplikace používá [zprostředkovatele ověřování Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Nastavte ID klienta a tajný kód klienta

Zprostředkovatele ověřování OAuth vytváří vztah důvěryhodnosti s aplikací pomocí ID klienta a tajný kód klienta. ID klienta a hodnoty tajných kódů klienta jsou vytvořené pro aplikaci zprostředkovatele externího ověřování při registraci aplikace s tímto poskytovatelem. Každý externího poskytovatele, který používá aplikace musí mít nakonfigurovanou nezávisle na sobě ID klienta a tajný kód klienta poskytovatele. Další informace najdete v tématech zprostředkovatele externího ověřování, které platí pro váš scénář:

* [Ověřování Facebooku](xref:security/authentication/facebook-logins)
* [Ověřování Googlu](xref:security/authentication/google-logins)
* [Ověřování Microsoftu](xref:security/authentication/microsoft-logins)
* [Ověřování Twitteru](xref:security/authentication/twitter-logins)
* [Další zprostředkovatelé ověřování](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Ukázková aplikace nakonfiguruje zprostředkovatele ověřování Google s ID klienta a tajný kód klienta, které jsou k dispozici společností Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Vytvořit rozsah ověřování

Zadejte seznam oprávnění k načtení z poskytovatele tak, že zadáte <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>. V následující tabulce se zobrazí obory ověřování pro běžné externího poskytovatele.

| Poskytovatel  | Scope                                                            |
| --------- | ---------------------------------------------------------------- |
| Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |

Ukázkové aplikace, Google `userinfo.profile` oboru se automaticky přidá rozhraním, když <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> je volán na <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>. Pokud aplikace vyžaduje další obory, je přidáte do požadované možnosti. V následujícím příkladu se ke službě Google `https://www.googleapis.com/auth/user.birthday.read` oboru se přidá získat datum narození uživatele:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapování uživatelských dat klíčů a vytvářet deklarace identity

V možnostech zprostředkovatele, zadejte <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> nebo <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pro každý klíč/podklíč externího poskytovatele JSON uživatelské údaje pro identitu aplikace ke čtení na přihlášení. Další informace o typy deklarací identity najdete v tématu <xref:System.Security.Claims.ClaimTypes>.

Ukázková aplikace vytvoří národní prostředí (`urn:google:locale`) a obrázku (`urn:google:picture`) deklarace identity od `locale` a `picture` klíče v Google uživatelská data:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

V <xref:Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync*>, <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) je přihlášený k aplikaci s <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>. Během procesu, přihlašování <xref:Microsoft.AspNetCore.Identity.UserManager%601> můžete ukládat `ApplicationUser` deklarací identity pro uživatele nejsou k dispozici data <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.

V ukázkové aplikaci `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) vytvoří národní prostředí (`urn:google:locale`) a obrázku (`urn:google:picture`) deklarací identity pro podepsané v `ApplicationUser`, včetně deklarací identity pro <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Ve výchozím nastavení jsou deklarace identity uživatele uložené v souboru cookie pro ověřování. Pokud soubor cookie ověřování je moc velká, může to způsobit aplikace se nezdařila, protože:

* Prohlížeč zjistí, že hlavička cookie je příliš dlouhý.
* Celková velikost požadavku je moc velká.

Pokud velký objem dat uživatele se vyžaduje pro zpracování požadavků uživatele:

* Omezte počet a velikost deklarace identity uživatelů pro zpracování pouze co aplikace vyžaduje požadavku.
* Použití vlastní <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> pro Middleware ověřování souborů Cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> k ukládání identit napříč požadavky. Zachovat velké množství informací identity na serveru při odesílání pouze identifikátor klíče malé relace do klienta.

## <a name="save-the-access-token"></a>Uložit přístupový token

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definuje, jestli tokeny přístupu a aktualizace by měla být uložena v <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po úspěšném ověření. `SaveTokens` je nastavena na `false` ve výchozím nastavení ke zmenšení velikosti konečné ověřovacího souboru cookie.

Ukázková aplikace nastaví hodnotu `SaveTokens` k `true` v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Když `OnPostConfirmationAsync` spustí, ukládat přístupový token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) z externího poskytovatele v `ApplicationUser`společnosti `AuthenticationProperties`.

Ukázková aplikace uloží přístupového tokenu v `OnPostConfirmationAsync` (nové registrace uživatele) a `OnGetCallbackAsync` (dříve registrovaného uživatele) v *Account/ExternalLogin.cshtml.cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Jak přidat další vlastní tokeny

K předvedení jak přidat vlastní token, který je uložený jako součást `SaveTokens`, přidá ukázkovou aplikaci <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> s aktuálním <xref:System.DateTime> pro [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) z `TicketCreated`:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-28)]

## <a name="creating-and-adding-claims"></a>Vytvoření a přidání deklarace identity

Rozhraní poskytuje běžné akce a rozšiřující metody pro vytvoření a přidání deklarace identity do kolekce. Další informace najdete v tématu <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.

Uživatelé mohou definovat vlastní akce odvozením z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> a implementaci abstraktní <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metody.

Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Odebrání akce deklarace identity a deklarací identity

[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) odebere všechny deklarace identity akce daném <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekce. [ClaimActionCollectionMapExtensions.DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) odstraní deklarace identity z daný <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z identity. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> se používá především s [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) odebrat vygeneruje protokol deklarací identity.

## <a name="sample-app-output"></a>Ukázkový výstup aplikace

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="additional-resources"></a>Další zdroje

* [ASPNET/AspNetCore engineering SocialSample aplikace](https://github.com/aspnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample) &ndash; propojené ukázkové aplikace je na [úložiště GitHub aspnet/AspNetCore](https://github.com/aspnet/AspNetCore) `master` engineering větve. `master` Větev obsahuje kód aktivně vyvíjí pro další verzi ASP.NET Core. Pokud chcete zobrazit verzi ukázkovou aplikaci pro vydanou verzi ASP.NET Core, použijte **větev** rozevírací seznam a vyberte větev vydané verze (například `release/2.2`).

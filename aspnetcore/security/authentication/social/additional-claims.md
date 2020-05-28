---
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a>Trvalé další deklarace identity a tokeny od externích zprostředkovatelů v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Aplikace ASP.NET Core může navázat další deklarace identity a tokeny od externích zprostředkovatelů ověřování, jako je Facebook, Google, Microsoft a Twitter. Každý zprostředkovatel odhalí různé informace o uživatelích na své platformě, ale vzor pro příjem a transformaci uživatelských dat do dalších deklarací identity je stejný.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

Rozhodněte, které externí zprostředkovatelé ověřování budou v aplikaci podporovat. Pro každého poskytovatele Zaregistrujte aplikaci a Získejte ID klienta a tajný klíč klienta. Další informace naleznete v tématu <xref:security/authentication/social/index>. Ukázková aplikace používá [poskytovatele ověřování Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Nastavení ID klienta a tajného klíče klienta

Zprostředkovatel ověřování OAuth vytvoří vztah důvěryhodnosti s aplikací pomocí ID klienta a tajného klíče klienta. ID klienta a hodnoty tajného klíče klienta jsou vytvořeny pro aplikaci externím zprostředkovatelem ověřování při registraci aplikace u poskytovatele. Každý externí poskytovatel, který aplikace používá, musí být nakonfigurován nezávisle s ID klienta a tajného kódu klienta. Další informace najdete v tématech věnovaném externímu poskytovateli ověřování, které se vztahují k vašemu scénáři:

* [Ověřování Facebooku](xref:security/authentication/facebook-logins)
* [Ověřování Googlu](xref:security/authentication/google-logins)
* [Ověřování Microsoftu](xref:security/authentication/microsoft-logins)
* [Ověřování Twitteru](xref:security/authentication/twitter-logins)
* [Další zprostředkovatelé ověřování](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Ukázková aplikace nakonfiguruje poskytovatele ověřování Google pomocí ID klienta a tajného kódu klienta poskytovaného Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Vytvoření oboru ověřování

Zadejte seznam oprávnění, která se mají načíst ze zprostředkovatele, zadáním <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> . V následující tabulce jsou uvedeny obory ověřování pro běžné externí zprostředkovatele.

| Poskytovatel  | Rozsah                                                            |
| ---
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

----- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |

V ukázkové aplikaci `userinfo.profile` je obor Google automaticky přidán rozhraním, když <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> je volána na portálu <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . Pokud aplikace vyžaduje další obory, přidejte je do možností. V následujícím příkladu `https://www.googleapis.com/auth/user.birthday.read` je přidaný obor Google, aby bylo možné načíst narozeniny uživatele:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapování klíčů uživatelských dat a vytváření deklarací

V možnostech poskytovatele zadejte <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> nebo <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pro každý klíč nebo podklíč v datech uživatele JSON externího poskytovatele, aby se identita aplikace četla při přihlášení. Další informace o typech deklarací identity naleznete v tématu <xref:System.Security.Claims.ClaimTypes> .

Ukázková aplikace vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) z `locale` `picture` klíčů a v datech uživatelů Google:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

V je aplikace `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) přihlášena do aplikace pomocí <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . Během procesu přihlašování <xref:Microsoft.AspNetCore.Identity.UserManager%601> může aplikace ukládat `ApplicationUser` deklarace identity pro uživatelská data, která jsou k dispozici v <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

V ukázkové aplikaci `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) se vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) pro přihlášené `ApplicationUser` , včetně deklarace identity pro <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Ve výchozím nastavení jsou deklarace identity uživatele uloženy v ověřovacím souboru cookie. Pokud je soubor cookie ověřování příliš velký, může dojít k selhání aplikace z těchto důvodů:

* Prohlížeč zjistí, že hlavička souboru cookie je příliš dlouhá.
* Celková velikost požadavku je příliš velká.

Pokud se pro zpracování uživatelských požadavků vyžaduje velké množství uživatelských dat:

* Omezte počet a velikost deklarací identity uživatelů pro zpracování žádostí jenom na to, co aplikace vyžaduje.
* <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> K ukládání identit napříč požadavky použijte vlastníky pro middleware ověřování souborů cookie. Zachovat velké množství informací o identitě na serveru, zatímco do klienta odesílá jenom malý klíč identifikátoru relace.

## <a name="save-the-access-token"></a>Uložení přístupového tokenu

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definuje, jestli se mají <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po úspěšné autorizaci ukládat tokeny přístupu a aktualizace. `SaveTokens`je nastavené na výchozí hodnotu, `false` aby se snížila velikost konečného ověřovacího souboru cookie.

Ukázková aplikace nastaví hodnotu `SaveTokens` na `true` v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Když `OnPostConfirmationAsync` se spustí, uložte přístupový token ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) od externího poskytovatele v `ApplicationUser` `AuthenticationProperties` .

Ukázková aplikace uloží přístupový token do `OnPostConfirmationAsync` (registrace nového uživatele) a `OnGetCallbackAsync` (dřív registrovaný uživatel) v *účtu/ExternalLogin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Postup přidání dalších vlastních tokenů

Aby bylo možné předvést, jak přidat vlastní token, který je uložen v rámci `SaveTokens` , ukázková aplikace přidá a <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> aktuální <xref:System.DateTime> pro [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Vytváření a přidávání deklarací identity

Rozhraní poskytuje běžné akce a metody rozšíření pro vytváření a přidávání deklarací identity do kolekce. Další informace naleznete v tématech <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .

Uživatelé mohou definovat vlastní akce odvozením z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> a implementací abstraktní <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metody.

Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Odebrání akcí a deklarací identity

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) odebere všechny akce deklarací identity pro daný <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekce. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) odstraní deklaraci identity udělené <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z identity. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se primárně používá s [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) k odebrání deklarací generovaných protokolem.

## <a name="sample-app-output"></a>Výstup ukázkové aplikace

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Aplikace ASP.NET Core může navázat další deklarace identity a tokeny od externích zprostředkovatelů ověřování, jako je Facebook, Google, Microsoft a Twitter. Každý zprostředkovatel odhalí různé informace o uživatelích na své platformě, ale vzor pro příjem a transformaci uživatelských dat do dalších deklarací identity je stejný.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

Rozhodněte, které externí zprostředkovatelé ověřování budou v aplikaci podporovat. Pro každého poskytovatele Zaregistrujte aplikaci a Získejte ID klienta a tajný klíč klienta. Další informace naleznete v tématu <xref:security/authentication/social/index>. Ukázková aplikace používá [poskytovatele ověřování Google](xref:security/authentication/google-logins).

## <a name="set-the-client-id-and-client-secret"></a>Nastavení ID klienta a tajného klíče klienta

Zprostředkovatel ověřování OAuth vytvoří vztah důvěryhodnosti s aplikací pomocí ID klienta a tajného klíče klienta. ID klienta a hodnoty tajného klíče klienta jsou vytvořeny pro aplikaci externím zprostředkovatelem ověřování při registraci aplikace u poskytovatele. Každý externí poskytovatel, který aplikace používá, musí být nakonfigurován nezávisle s ID klienta a tajného kódu klienta. Další informace najdete v tématech věnovaném externímu poskytovateli ověřování, které se vztahují k vašemu scénáři:

* [Ověřování Facebooku](xref:security/authentication/facebook-logins)
* [Ověřování Googlu](xref:security/authentication/google-logins)
* [Ověřování Microsoftu](xref:security/authentication/microsoft-logins)
* [Ověřování Twitteru](xref:security/authentication/twitter-logins)
* [Další zprostředkovatelé ověřování](xref:security/authentication/otherlogins)
* [OpenIdConnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

Ukázková aplikace nakonfiguruje poskytovatele ověřování Google pomocí ID klienta a tajného kódu klienta poskytovaného Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a>Vytvoření oboru ověřování

Zadejte seznam oprávnění, která se mají načíst ze zprostředkovatele, zadáním <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> . V následující tabulce jsou uvedeny obory ověřování pro běžné externí zprostředkovatele.

| Poskytovatel  | Rozsah                                                            |
| ---
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

----- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |

V ukázkové aplikaci `userinfo.profile` je obor Google automaticky přidán rozhraním, když <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> je volána na portálu <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> . Pokud aplikace vyžaduje další obory, přidejte je do možností. V následujícím příkladu `https://www.googleapis.com/auth/user.birthday.read` je přidaný obor Google, aby bylo možné načíst narozeniny uživatele:

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a>Mapování klíčů uživatelských dat a vytváření deklarací

V možnostech poskytovatele zadejte <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> nebo <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> pro každý klíč nebo podklíč v datech uživatele JSON externího poskytovatele, aby se identita aplikace četla při přihlášení. Další informace o typech deklarací identity naleznete v tématu <xref:System.Security.Claims.ClaimTypes> .

Ukázková aplikace vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) z `locale` `picture` klíčů a v datech uživatelů Google:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

V je aplikace `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) přihlášena do aplikace pomocí <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> . Během procesu přihlašování <xref:Microsoft.AspNetCore.Identity.UserManager%601> může aplikace ukládat `ApplicationUser` deklarace identity pro uživatelská data, která jsou k dispozici v <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .

V ukázkové aplikaci `OnPostConfirmationAsync` (*account/ExternalLogin. cshtml. cs*) se vytváří deklarace národního prostředí ( `urn:google:locale` ) a obrázku ( `urn:google:picture` ) pro přihlášené `ApplicationUser` , včetně deklarace identity pro <xref:System.Security.Claims.ClaimTypes.GivenName> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

Ve výchozím nastavení jsou deklarace identity uživatele uloženy v ověřovacím souboru cookie. Pokud je soubor cookie ověřování příliš velký, může dojít k selhání aplikace z těchto důvodů:

* Prohlížeč zjistí, že hlavička souboru cookie je příliš dlouhá.
* Celková velikost požadavku je příliš velká.

Pokud se pro zpracování uživatelských požadavků vyžaduje velké množství uživatelských dat:

* Omezte počet a velikost deklarací identity uživatelů pro zpracování žádostí jenom na to, co aplikace vyžaduje.
* <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> K ukládání identit napříč požadavky použijte vlastníky pro middleware ověřování souborů cookie. Zachovat velké množství informací o identitě na serveru, zatímco do klienta odesílá jenom malý klíč identifikátoru relace.

## <a name="save-the-access-token"></a>Uložení přístupového tokenu

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>definuje, jestli se mají <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> po úspěšné autorizaci ukládat tokeny přístupu a aktualizace. `SaveTokens`je nastavené na výchozí hodnotu, `false` aby se snížila velikost konečného ověřovacího souboru cookie.

Ukázková aplikace nastaví hodnotu `SaveTokens` na `true` v <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

Když `OnPostConfirmationAsync` se spustí, uložte přístupový token ([ExternalLoginInfo. AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) od externího poskytovatele v `ApplicationUser` `AuthenticationProperties` .

Ukázková aplikace uloží přístupový token do `OnPostConfirmationAsync` (registrace nového uživatele) a `OnGetCallbackAsync` (dřív registrovaný uživatel) v *účtu/ExternalLogin. cshtml. cs*:

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a>Postup přidání dalších vlastních tokenů

Aby bylo možné předvést, jak přidat vlastní token, který je uložen v rámci `SaveTokens` , ukázková aplikace přidá a <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> aktuální <xref:System.DateTime> pro [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) `TicketCreated` :

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a>Vytváření a přidávání deklarací identity

Rozhraní poskytuje běžné akce a metody rozšíření pro vytváření a přidávání deklarací identity do kolekce. Další informace naleznete v tématech <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions> .

Uživatelé mohou definovat vlastní akce odvozením z <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> a implementací abstraktní <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> metody.

Další informace naleznete v tématu <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.

## <a name="removal-of-claim-actions-and-claims"></a>Odebrání akcí a deklarací identity

[ClaimActionCollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) odebere všechny akce deklarací identity pro daný <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z kolekce. [ClaimActionCollectionMapExtensions. DeleteClaim (ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) odstraní deklaraci identity udělené <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> z identity. <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>se primárně používá s [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) k odebrání deklarací generovaných protokolem.

## <a name="sample-app-output"></a>Výstup ukázkové aplikace

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

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [dotnet/AspNetCore Engineering SocialSample App](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): propojená ukázková aplikace je v technické větvi [úložiště dotnet/AspNetCore GitHub](https://github.com/dotnet/AspNetCore) `master` . `master`Větev obsahuje kód v rámci aktivního vývoje pro další vydání ASP.NET Core. Pokud chcete zobrazit verzi ukázkové aplikace pro vydanou verzi ASP.NET Core, použijte rozevírací seznam **větev** a vyberte větev vydané verze (například `release/{X.Y}` ).

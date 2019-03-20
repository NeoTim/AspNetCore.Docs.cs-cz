---
title: Ověřování souborem cookie bez ASP.NET Core Identity
author: rick-anderson
description: Vysvětlení, používání ověřování souborem cookie bez ASP.NET Core Identity
ms.author: riande
ms.date: 02/25/2019
uid: security/authentication/cookie
ms.openlocfilehash: c6cba588abb003ee677d8f1753b73a1ced1414b0
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209372"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>Ověřování souborem cookie bez ASP.NET Core Identity

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Luke Latham](https://github.com/guardrex)

Jak už víte, v předchozích tématech ověřování [ASP.NET Core Identity](xref:security/authentication/identity) je ověřování dokončeno, plně vybavené zprostředkovatele pro vytváření a správa přihlášení. Můžete ale použít vlastní logiku ověřování vlastní soubor cookie s ověřováním pomocí čas od času. Ověřování na základě souborů cookie slouží jako zprostředkovatel ověřování samostatné bez ASP.NET Core Identity.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([stažení](xref:index#how-to-download-a-sample))

Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetické uživatele Marie Rodriguez pevně zakódované do aplikace. Použití uživatelského jména e-mailu "maria.rodriguez@contoso.com" a jakékoli heslo k přihlášení uživatele. Ověření uživatele v `AuthenticateUser` metodu *Pages/Account/Login.cshtml.cs* souboru. V reálný příklad uživatel by ověřovány proti databázi.

Informace o migraci na základě souboru cookie ověřování ASP.NET Core 1.x do 2.0, naleznete v tématu [migrovat ověřování a identita na téma ASP.NET Core 2.0 (ověřování na základě souborů Cookie)](xref:migration/1x-to-2x/identity-2x#cookie-based-authentication).

Použití ASP.NET Core Identity, najdete v článku [Úvod do Identity](xref:security/authentication/identity) tématu.

## <a name="configuration"></a>Konfigurace

::: moniker range=">= aspnetcore-2.0"

Pokud se aplikace nepoužívá [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), vytvořit odkaz na balíček v souboru projektu [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) balíčku (verze 2.1.0 nebo později).

V `ConfigureServices` metody vytvoření Middleware ověřování služby s `AddAuthentication` a `AddCookie` metody:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

`AuthenticationScheme` Předaný `AddAuthentication` nastaví výchozí schéma ověřování pro aplikaci. `AuthenticationScheme` je užitečné, pokud existuje více instancí ověřování souborů cookie a vy chcete [autorizovat s konkrétní schéma](xref:security/authorization/limitingidentitybyscheme). Nastavení `AuthenticationScheme` k `CookieAuthenticationDefaults.AuthenticationScheme` poskytuje hodnotu "Soubory cookie" pro schéma. Můžete zadat libovolnou hodnotu řetězce, která odlišuje schéma.

Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikaci. Pokud není k dispozici schéma ověřování souborů cookie do <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, používá [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (soubory cookie.").

Soubor cookie ověřování <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je nastavena na `true` ve výchozím nastavení. Když návštěvník nevyjádřil souhlas shromažďování dat jsou povoleny soubory cookie pro ověřování. Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.

V `Configure` metody, použijte `UseAuthentication` metoda k vyvolání ověřovací Middleware, který nastaví `HttpContext.User` vlastnost. Volání `UseAuthentication` před voláním metody `UseMvcWithDefaultRoute` nebo `UseMvc`:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

**Možnosti AddCookie**

[CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions?view=aspnetcore-2.0) třída se používá ke konfiguraci možností zprostředkovatele ověřování.

| Možnost | Popis |
| ------ | ----------- |
| [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath?view=aspnetcore-2.0) | Určuje cestu k poskytování s 302 Found (adresa URL přesměrování) aktivovaného `HttpContext.ForbidAsync`. Výchozí hodnota je `/Account/AccessDenied`. |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.claimsissuer?view=aspnetcore-2.0) | Vystavitel, který má použít pro [vystavitele](/dotnet/api/system.security.claims.claim.issuer) vlastnost na všechny deklarace vytvořené službou ověřování souborů cookie. |
| [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain?view=aspnetcore-2.0) | Název domény, kde je soubor cookie obsluhuje. Ve výchozím nastavení toto je název hostitele žádosti. Prohlížeč odesílá pouze soubor cookie v požadavcích na odpovídající název hostitele. Můžete chtít upravit tak, aby soubory cookie, které jsou k dispozici pro všechny hostitele ve vaší doméně. Například nastavení domény souboru cookie na `.contoso.com` ji zpřístupní k `contoso.com`, `www.contoso.com`, a `staging.www.contoso.com`. |
| [Cookie.HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly?view=aspnetcore-2.0) | Příznak označující, pokud by měl být soubor cookie přístupný jenom pro servery. Změna této hodnoty na `false` povoluje skripty na straně klienta pro přístup k souboru cookie a může otevřít aplikaci tak, aby krádež souborů cookie by měl mít vaše aplikace [skriptování napříč weby (XSS)](xref:security/cross-site-scripting) ohrožení zabezpečení. Výchozí hodnota je `true`. |
| [Cookie.Name](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name?view=aspnetcore-2.0) | Nastaví název souboru cookie. |
| [Cookie.Path](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path?view=aspnetcore-2.0) | Použít k izolaci aplikací spuštěných na stejný název hostitele. Pokud máte aplikaci spuštěnou na `/app1` a chcete omezit souborů cookie do této aplikace, nastavte `CookiePath` vlastnost `/app1`. Díky tomu souboru cookie, který je k dispozici pouze u požadavků na `/app1` a všechny aplikace pod ním. |
| [Cookie.SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite?view=aspnetcore-2.0) | Určuje, zda v prohlížeči by měl povolit soubory cookie bude připojený na stejný web požadavky jenom (`SameSiteMode.Strict`) nebo pomocí bezpečných metod HTTP a požadavky na stejný web požadavky mezi weby (`SameSiteMode.Lax`). Pokud je nastavena na `SameSiteMode.None`, není-li nastavena hodnota hlavičky souborů cookie. Všimněte si, že [middlewaru souboru Cookie. zásady](#cookie-policy-middleware) mohou přepsat hodnotu, kterou zadáte. Pro podporu ověřování OAuth, výchozí hodnota je `SameSiteMode.Lax`. Další informace najdete v tématu [nefunkční kvůli zásadám SameSite souboru cookie ověřování OAuth](https://github.com/aspnet/Security/issues/1231). |
| [Cookie.SecurePolicy](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.securepolicy?view=aspnetcore-2.0) | Příznak označující, pokud by měl být soubor cookie vytvořený omezené na protokol HTTPS (`CookieSecurePolicy.Always`), HTTP nebo HTTPS (`CookieSecurePolicy.None`), nebo stejný protokol jako požadavek (`CookieSecurePolicy.SameAsRequest`). Výchozí hodnota je `CookieSecurePolicy.SameAsRequest`. |
| [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0) | Nastaví `DataProtectionProvider` , který slouží k vytvoření výchozích `TicketDataFormat`. Pokud `TicketDataFormat` je vlastnost nastavena, `DataProtectionProvider` není použita možnost. Pokud se nezadá, výchozí zprostředkovatel ochrany dat aplikace se používá. |
| [Události](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.events?view=aspnetcore-2.0) | Obslužná rutina volá metody zprostředkovatele, který poskytují kontrolu aplikace v určitých bodech, zpracování. Pokud `Events` nejsou zadaná, je použita výchozí instance, který nemá žádný účinek, pokud jsou volány metody. |
| [EventsType](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.eventstype?view=aspnetcore-2.0) | Použít jako typ služby k získání `Events` instanci místo vlastnost. |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan?view=aspnetcore-2.0) | `TimeSpan` Po kterém vyprší platnost lístku ověřování uložený v souboru cookie. `ExpireTimeSpan` je přidán do aktuální čas při vytváření dobu vypršení platnosti-the-ticket. `ExpiredTimeSpan` Hodnota vždy přejde do šifrovaného AuthTicket ověřit server. Může taky přejít do [Set-Cookie](https://tools.ietf.org/html/rfc6265#section-4.1) záhlaví, ale pouze v případě `IsPersistent` nastavena. Nastavit `IsPersistent` k `true`, nakonfigurujte [AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties) předán `SignInAsync`. Výchozí hodnota `ExpireTimeSpan` je 14 dní. |
| [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath?view=aspnetcore-2.0) | Určuje cestu k poskytování s 302 Found (adresa URL přesměrování) aktivovaného `HttpContext.ChallengeAsync`. Aktuální adresa URL, které vygenerovalo 401 je přidán do `LoginPath` jako parametru řetězce dotazu s názvem podle `ReturnUrlParameter`. Jakmile požadavek na `LoginPath` udělí novou identitu přihlášení, `ReturnUrlParameter` hodnota slouží k přesměrování prohlížeče zpět na adresu URL, která způsobila původní kód neautorizovaného stavu. Výchozí hodnota je `/Account/Login`. |
| [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath?view=aspnetcore-2.0) | Pokud `LogoutPath` je k dispozici pro obslužnou rutinu, pak přesměruje požadavek na tuto cestu podle hodnoty `ReturnUrlParameter`. Výchozí hodnota je `/Account/Logout`. |
| [ReturnUrlParameter](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.returnurlparameter?view=aspnetcore-2.0) | Určuje název parametru řetězce dotazu, který se připojí obslužnou rutinou pro odpovědi 302 Found (adresa URL přesměrování). `ReturnUrlParameter` se používá, když dorazí požadavek na `LoginPath` nebo `LogoutPath` do prohlížeče po provedení akce přihlašovací nebo odhlašovací vraťte k původní adresu URL. Výchozí hodnota je `ReturnUrl`. |
| [SessionStore](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.sessionstore?view=aspnetcore-2.0) | Volitelný kontejner používá k ukládání identit napříč požadavky. Když se použije, jenom identifikátor relace je odeslat klientovi. `SessionStore` je možné zmírnit potenciální potíže s velkými identitami. |
| [parametr slidingExpiration](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-2.0) | Příznak označující, pokud se nový soubor cookie s čas vypršení platnosti aktualizace by měla být vydána dynamicky. To může nastat na všechny požadavky, pokud aktuální období vypršení platnosti souboru cookie je více než 50 % vypršela platnost. Nové datum vypršení platnosti bude přesunut dopředu na aktuální datum plus `ExpireTimespan`. [Čas vypršení platnosti souboru cookie absolutní](xref:security/authentication/cookie#absolute-cookie-expiration) můžete nastavit pomocí `AuthenticationProperties` třídy při volání metody `SignInAsync`. Čas absolutní vypršení platnosti můžete zlepšit zabezpečení vaší aplikace tím, že omezíte množství času, který je platný soubor cookie ověřování. Výchozí hodnota je `true`. |
| [TicketDataFormat](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.ticketdataformat?view=aspnetcore-2.0) | `TicketDataFormat` Se používá k ochraně a zrušení této ochrany identity a dalších vlastností, které jsou uloženy v hodnotě souboru cookie. Pokud se nezadá, `TicketDataFormat` je vytvořený pomocí [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.dataprotectionprovider?view=aspnetcore-2.0). |
| [Ověření](/dotnet/api/microsoft.aspnetcore.authentication.authenticationschemeoptions.validate?view=aspnetcore-2.0) | Metoda, která kontroluje, že možnosti jsou platné. |

Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `ConfigureServices` metody:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Soubor cookie používá ASP.NET Core 1.x [middleware](xref:fundamentals/middleware/index) hlavní název uživatele, který serializuje do zašifrovaného souboru cookie. Na následné žádosti soubor cookie se ověří a znovu vytvořit a přiřadit k objektu zabezpečení `HttpContext.User` vlastnost.

Nainstalujte [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) balíčku NuGet ve vašem projektu. Tento balíček obsahuje middlewaru souboru cookie.

Použití `UseCookieAuthentication` metoda ve `Configure` metoda ve vaší *Startup.cs* souboru před `UseMvc` nebo `UseMvcWithDefaultRoute`:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions()
{
    AccessDeniedPath = "/Account/Forbidden/",
    AuthenticationScheme = CookieAuthenticationDefaults.AuthenticationScheme,
    AutomaticAuthenticate = true,
    AutomaticChallenge = true,
    LoginPath = "/Account/Unauthorized/"
});
```

**Možnosti CookieAuthenticationOptions**

[CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions?view=aspnetcore-1.1) třída se používá ke konfiguraci možností zprostředkovatele ověřování.

| Možnost | Popis |
| ------ | ----------- |
| [AuthenticationScheme](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.authenticationscheme?view=aspnetcore-1.1) | Nastaví schéma ověřování. `AuthenticationScheme` je užitečné, pokud existuje více instancí ověřování a vy chcete [autorizovat s konkrétní schéma](xref:security/authorization/limitingidentitybyscheme). Nastavení `AuthenticationScheme` k `CookieAuthenticationDefaults.AuthenticationScheme` poskytuje hodnotu "Soubory cookie" pro schéma. Můžete zadat libovolnou hodnotu řetězce, která odlišuje schéma. |
| [AutomaticAuthenticate](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticauthenticate?view=aspnetcore-1.1) | Nastaví hodnotu označující, že ověřování souborů cookie by měl spustit u každého požadavku a pokusí se ověřit a proveďte rekonstrukci serializovaný objekt zabezpečení, vytvořená. |
| [AutomaticChallenge](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.automaticchallenge?view=aspnetcore-1.1) | Při hodnotě true se zpracovává middleware ověřování automatické výzvy. Pokud false, middleware ověřování pouze změní odpovědi, když explicitně `AuthenticationScheme`. |
| [ClaimsIssuer](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.claimsissuer?view=aspnetcore-1.1) | Vystavitel, který má použít pro [vystavitele](/dotnet/api/system.security.claims.claim.issuer) vlastnost na všechny deklarace vytvořené middleware ověřování souborů cookie. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiedomain?view=aspnetcore-1.1) | Název domény, kde je soubor cookie obsluhuje. Ve výchozím nastavení toto je název hostitele žádosti. Prohlížeč slouží pouze souboru cookie, který má odpovídající název hostitele. Můžete chtít upravit tak, aby soubory cookie, které jsou k dispozici pro všechny hostitele ve vaší doméně. Například nastavení domény souboru cookie na `.contoso.com` ji zpřístupní k `contoso.com`, `www.contoso.com`, a `staging.www.contoso.com`. |
| [CookieHttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiehttponly?view=aspnetcore-1.1) | Příznak označující, pokud by měl být soubor cookie přístupný jenom pro servery. Změna této hodnoty na `false` povoluje skripty na straně klienta pro přístup k souboru cookie a může otevřít aplikaci tak, aby krádež souborů cookie by měl mít vaše aplikace [skriptování napříč weby (XSS)](xref:security/cross-site-scripting) ohrožení zabezpečení. Výchozí hodnota je `true`. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiepath?view=aspnetcore-1.1) | Použít k izolaci aplikací spuštěných na stejný název hostitele. Pokud máte aplikaci spuštěnou na `/app1` a chcete omezit souborů cookie do této aplikace, nastavte `CookiePath` vlastnost `/app1`. Díky tomu souboru cookie, který je k dispozici pouze u požadavků na `/app1` a všechny aplikace pod ním. |
| [CookieSecure](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.cookiesecure?view=aspnetcore-1.1) | Příznak označující, pokud by měl být soubor cookie vytvořený omezené na protokol HTTPS (`CookieSecurePolicy.Always`), HTTP nebo HTTPS (`CookieSecurePolicy.None`), nebo stejný protokol jako požadavek (`CookieSecurePolicy.SameAsRequest`). Výchozí hodnota je `CookieSecurePolicy.SameAsRequest`. |
| [Popis](/dotnet/api/microsoft.aspnetcore.builder.authenticationoptions.description?view=aspnetcore-1.1) | Další informace o typu ověřování, který je k dispozici pro aplikaci. |
| [ExpireTimeSpan](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.expiretimespan?view=aspnetcore-1.1) | `TimeSpan` Po kterém vyprší platnost lístku ověřování. Přidá se do aktuálního času vytvoření dobu vypršení platnosti-the-ticket. Použití `ExpireTimeSpan`, je nutné nastavit `IsPersistent` k `true` v `AuthenticationProperties` předán `SignInAsync`. Výchozí hodnota je 14 dní. |
| [parametr slidingExpiration](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions.slidingexpiration?view=aspnetcore-1.1) | Příznak označující, zda datum vypršení platnosti souboru cookie resetuje při více než polovina `ExpireTimeSpan` uplynutí intervalu. Nový čas vypršení platnosti bude přesunut dopředu na aktuální datum plus `ExpireTimespan`. [Čas vypršení platnosti souboru cookie absolutní](xref:security/authentication/cookie#absolute-cookie-expiration) můžete nastavit pomocí `AuthenticationProperties` třídy při volání metody `SignInAsync`. Čas absolutní vypršení platnosti můžete zlepšit zabezpečení vaší aplikace tím, že omezíte množství času, který je platný soubor cookie ověřování. Výchozí hodnota je `true`. |

Nastavte `CookieAuthenticationOptions` pro Middleware ověřování souborů Cookie v `Configure` metody:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    ...
});
```

::: moniker-end

## <a name="cookie-policy-middleware"></a>Zásady middlewaru souboru cookie.

[Zásady middlewaru souboru cookie](/dotnet/api/microsoft.aspnetcore.cookiepolicy.cookiepolicymiddleware) umožňuje možnosti zásad souborů cookie v aplikaci. Přidání middleware do kanálu zpracování aplikace je v pořadí citlivé; ovlivňuje pouze komponenty zaregistrovaný v kanálu po něm.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

 [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) poskytnuté middlewaru souboru Cookie. zásady umožňují určit globální vlastnosti zpracování souboru cookie a hook do obslužné rutiny zpracování souboru cookie, kdy jsou soubory cookie připojí nebo odstraní.

| Vlastnost | Popis |
| -------- | ----------- |
| [HttpOnly](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.httponly) | Ovlivňuje, jestli HttpOnly musí být soubory cookie, který je příznak označující, pokud by měl být soubor cookie přístupný jenom pro servery. Výchozí hodnota je `HttpOnlyPolicy.None`. |
| [MinimumSameSitePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.minimumsamesitepolicy) | Má vliv na stejný web atribut souboru cookie (viz níže). Výchozí hodnota je `SameSiteMode.Lax`. Tato možnost je dostupná pro ASP.NET Core 2.0 +. |
| [OnAppendCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.onappendcookie) | Volá se, když je přidán do souboru cookie. |
| [OnDeleteCookie](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.ondeletecookie) | Volá se, když se odstraní soubor cookie. |
| [Zabezpečení](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.secure) | Ovlivňuje, jestli soubory cookie musí být zabezpečené. Výchozí hodnota je `CookieSecurePolicy.None`. |

**MinimumSameSitePolicy** (ASP.NET Core 2.0 + jenom)

Výchozí hodnota `MinimumSameSitePolicy` hodnotu `SameSiteMode.Lax` tak, aby povolovala ověřování OAuth2. Přísně vynutit zásady stejný web `SameSiteMode.Strict`, nastavte `MinimumSameSitePolicy`. I když toto nastavení přeruší OAuth2 nebo jiná schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souboru cookie pro ostatní typy aplikací, které se nemusíte spoléhat na zpracování žádosti nepůvodního zdroje.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Nastavení zásad middlewaru souboru Cookie pro `MinimumSameSitePolicy` může mít vliv na vaše nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle níže uvedených matice.

| MinimumSameSitePolicy | Cookie.SameSite | Výsledná nastavení Cookie.SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Vytvoření souboru cookie pro ověřování

Pokud chcete vytvořit soubor cookie, která uchovává informace o uživateli, je nutné vytvořit [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal). Informace o uživateli je serializován a uložená v souboru cookie. 

::: moniker range=">= aspnetcore-2.0"

Vytvoření [ClaimsIdentity](/dotnet/api/system.security.claims.claimsidentity) s jakékoli požadované [deklarace identity](/dotnet/api/system.security.claims.claim)s a volání [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signinasync?view=aspnetcore-2.0) k přihlášení uživatele:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Volání [SignInAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signinasync?view=aspnetcore-1.1) k přihlášení uživatele:

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker-end

`SignInAsync` Vytvoří do zašifrovaného souboru cookie a přidá jej do aktuální odpovědi. Pokud nezadáte `AuthenticationScheme`, se používá výchozí schéma.

Pod pokličkou, je šifrování, které používá ASP.NET Core [ochranu dat](xref:security/data-protection/using-data-protection#security-data-protection-getting-started) systému. Pokud se hostují aplikaci na více počítačů, Vyrovnávání zatížení napříč aplikacemi nebo pomocí webové farmy, je nutné [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview) používat stejné aktualizační kanál klíč a identifikátor aplikace.

## <a name="sign-out"></a>Odhlásit se

::: moniker range=">= aspnetcore-2.0"

Chcete-li odhlásit aktuálního uživatele a odstranit jejich souborů cookie, zavolejte [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhttpcontextextensions.signoutasync?view=aspnetcore-2.0):

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Chcete-li odhlásit aktuálního uživatele a odstranit jejich souborů cookie, zavolejte [SignOutAsync](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1.signoutasync?view=aspnetcore-1.1):

```csharp
await HttpContext.Authentication.SignOutAsync(
    CookieAuthenticationDefaults.AuthenticationScheme);
```

::: moniker-end

Pokud nepoužíváte `CookieAuthenticationDefaults.AuthenticationScheme` (nebo "Soubory cookie") jako schéma (například "ContosoCookie"), zadejte schéma, které jste použili při konfiguraci zprostředkovatele ověřování. V opačném případě se používá výchozí schéma.

## <a name="react-to-back-end-changes"></a>Reagovat na změny back-end

Po vytvoření souboru cookie, bude jediný zdroj identity. I v případě, že zakázání uživatele v back endových systémů, systém ověřování souborů cookie je vůbec nezná to a uživatel zůstane přihlášený jako jejich soubor cookie je neplatný.

[ValidatePrincipal](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents.validateprincipal) událost v ASP.NET Core 2.x nebo [ValidateAsync](/dotnet/api/microsoft.aspnetcore.identity.isecuritystampvalidator.validateasync?view=aspnetcore-1.1) metoda v ASP.NET Core 1.x lze zachytit a přepsat ověření souboru cookie identity. Tento přístup snižuje riziko odvolané uživatele, kteří používají aplikaci.

Jedním z přístupů k ověření souboru cookie je založená na udržování přehledu o při změně uživatelskou databázi. Pokud databáze nebylo změněno od uživatele soubor cookie vydala, není nutné opakované ověření uživatele, pokud jejich souborů cookie je stále platný. Databáze, které je implementované v tomto scénáři implementovat `IUserRepository` v tomto příkladu ukládá `LastChanged` hodnotu. Když se aktualizuje každý uživatel v databázi, `LastChanged` je hodnota nastavena na aktuální čas.

Pokud chcete platnost souboru cookie, pokud změny databáze v závislosti na `LastChanged` hodnoty, vytvořte soubor cookie s `LastChanged` deklarace obsahující aktuální `LastChanged` hodnota z databáze:

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

::: moniker range=">= aspnetcore-2.0"

K implementaci přepsání `ValidatePrincipal` události, napište metodu s následující signaturou ve třídě, který je odvozen z [CookieAuthenticationEvents](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationevents):

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Příklad bude vypadat nějak takto:

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Zaregistrovat instanci události během registrace služby souborů cookie v `ConfigureServices` metody. Registraci vymezené služby pro vaše `CustomCookieAuthenticationEvents` třídy:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

K implementaci přepsání `ValidateAsync` události, napište metodu s následující signaturou:

```csharp
ValidateAsync(CookieValidatePrincipalContext)
```

ASP.NET Core Identity implementuje tato kontrola v rámci jeho [SecurityStampValidator](/dotnet/api/microsoft.aspnetcore.identity.securitystampvalidator-1.validateasync). Příklad bude vypadat nějak takto:

```csharp
public static class LastChangedValidator
{
    public static async Task ValidateAsync(CookieValidatePrincipalContext context)
    {
        // Pull database from registered DI services.
        var userRepository = 
            context.HttpContext.RequestServices
                .GetRequiredService<IUserRepository>();
        var userPrincipal = context.Principal;

        // Look for the last changed claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

Zaregistrujte události během konfigurace ověřování souborů cookie v `Configure` metody:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    Events = new CookieAuthenticationEvents
    {
        OnValidatePrincipal = LastChangedValidator.ValidateAsync
    }
});
```

::: moniker-end

Představte si situaci, ve kterém se aktualizuje uživatelské jméno &mdash; rozhodnutí, která nemá vliv na zabezpečení žádným způsobem. Pokud chcete aktualizovat nedestruktivně hlavní název uživatele, zavolejte `context.ReplacePrincipal` a nastavit `context.ShouldRenew` vlastnost `true`.

> [!WARNING]
> Tento přístup je zde popsáno, se aktivuje u každého požadavku. To může způsobit snížení výkonu velké aplikace.

## <a name="persistent-cookies"></a>Trvalé soubory cookie

Můžete chtít soubor cookie zachovat v rámci relací prohlížeče. Tato trvalost má být povoleno pouze explicitní uživatelské souhlas s "Zapamatovat" zaškrtávací políčko na přihlašovací nebo mechanismus podobný. 

Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který odolává prostřednictvím prohlížeče UZÁVĚRECH. Klouzavé vypršení platnosti nastavení jste dříve nakonfigurovali jsou zachované. Pokud se při zavření prohlížeče vyprší platnost souboru cookie, vymaže prohlížeči soubor cookie po jeho restartování.

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.authentication.authenticationproperties?view=aspnetcore-2.0) třídy se nachází v `Microsoft.AspNetCore.Authentication` oboru názvů.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

[AuthenticationProperties](/dotnet/api/microsoft.aspnetcore.http.authentication.authenticationproperties?view=aspnetcore-1.1) třídy se nachází v `Microsoft.AspNetCore.Http.Authentication` oboru názvů.

::: moniker-end

## <a name="absolute-cookie-expiration"></a>Soubor cookie absolutní vypršení platnosti

Můžete nastavit dobu vypršení platnosti absolutní s `ExpiresUtc`. K vytvoření trvalého souboru cookie, je nutné nastavit také `IsPersistent`; v opačném případě soubor cookie se vytvoří s životností založeného na relacích a může vypršet buď před nebo po lístek ověřování, který obsahuje. Když `ExpiresUtc` je nastavena na `SignInAsync`, přepíše hodnotu `ExpireTimeSpan` možnost `CookieAuthenticationOptions`, pokud nastavení.

Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který má platnost po dobu 20 minut. To ignoruje klouzavé vypršení platnosti nastavení předtím nakonfigurovali.

::: moniker range=">= aspnetcore-2.0"

```csharp
await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
await HttpContext.Authentication.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Ověřování 2.0 změny / migrace oznámení](https://github.com/aspnet/Announcements/issues/262)
* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Role na základě zásad kontroly](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

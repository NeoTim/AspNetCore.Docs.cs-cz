---
title: Ověřování souborem cookie bez ASP.NET Core Identity
author: rick-anderson
description: Další informace o použití ověřování souborem cookie bez ASP.NET Core Identity.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/07/2019
uid: security/authentication/cookie
ms.openlocfilehash: bbba2e77f806e1ed30bb734763cdbaedc1471d62
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622749"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>Ověřování souborem cookie bez ASP.NET Core Identity

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Luke Latham](https://github.com/guardrex)

ASP.NET Core Identity je ověřování dokončeno, plně vybavené poskytovatel pro vytváření a správa přihlášení. Nicméně je možné zprostředkovatele ověřování ověřování na základě souboru cookie bez ASP.NET Core Identity. Další informace naleznete v tématu <xref:security/authentication/identity>.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([stažení](xref:index#how-to-download-a-sample))

Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetické uživatele Marie Rodriguez pevně zakódované do aplikace. Použití **e-mailu** uživatelské jméno `maria.rodriguez@contoso.com` a jakékoli heslo k přihlášení uživatele. Ověření uživatele v `AuthenticateUser` metodu *Pages/Account/Login.cshtml.cs* souboru. V reálný příklad uživatel by ověřovány proti databázi.

## <a name="configuration"></a>Konfiguraci

Pokud se aplikace nepoužívá [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), vytvořit odkaz na balíček v souboru projektu [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) balíčku.

V `Startup.ConfigureServices` metody vytvoření Middleware ověřování služby s <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metody:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> Předaný `AddAuthentication` nastaví výchozí schéma ověřování pro aplikaci. `AuthenticationScheme` je užitečné, pokud existuje více instancí ověřování souborů cookie a vy chcete [autorizovat s konkrétní schéma](xref:security/authorization/limitingidentitybyscheme). Nastavení `AuthenticationScheme` k [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje hodnotu "Soubory cookie" pro schéma. Můžete zadat libovolnou hodnotu řetězce, která odlišuje schéma.

Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikaci. Pokud není k dispozici schéma ověřování souborů cookie do <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, používá `CookieAuthenticationDefaults.AuthenticationScheme` (soubory cookie.").

Soubor cookie ověřování <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je nastavena na `true` ve výchozím nastavení. Když návštěvník nevyjádřil souhlas shromažďování dat jsou povoleny soubory cookie pro ověřování. Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.

V `Startup.Configure` metody, volání `UseAuthentication` metoda k vyvolání ověřovací Middleware, který nastaví `HttpContext.User` vlastnost. Volání `UseAuthentication` před voláním metody `UseMvcWithDefaultRoute` nebo `UseMvc`:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Třída se používá ke konfiguraci možností zprostředkovatele ověřování.

Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metody:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Zásady middlewaru souboru cookie.

[Zásady middlewaru souboru cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje možnosti zásad souboru cookie. Přidání middleware do kanálu zpracování aplikace je v pořadí citlivé&mdash;ovlivní pouze podřízené komponenty zaregistrovaný v kanálu.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytnuté zásady middlewaru souboru Cookie. k řízení globální vlastnosti zpracování souboru cookie a hook do obslužné rutiny zpracování souboru cookie, když jsou soubory cookie připojí nebo odstraní.

Výchozí hodnota <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnotu `SameSiteMode.Lax` tak, aby povolovala ověřování OAuth2. Přísně vynutit zásady stejný web `SameSiteMode.Strict`, nastavte `MinimumSameSitePolicy`. I když toto nastavení přeruší OAuth2 nebo jiná schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souboru cookie pro ostatní typy aplikací, které se nemusíte spoléhat na zpracování žádosti nepůvodního zdroje.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Nastavení zásad middlewaru souboru Cookie pro `MinimumSameSitePolicy` může mít vliv na nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle níže uvedených matice.

| MinimumSameSitePolicy | Cookie.SameSite | Výsledná nastavení Cookie.SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Vytvoření souboru cookie pro ověřování

Pokud chcete vytvořit soubor cookie, která uchovává informace o uživateli, vytvořit <xref:System.Security.Claims.ClaimsPrincipal>. Informace o uživateli je serializován a uložená v souboru cookie. 

Vytvoření <xref:System.Security.Claims.ClaimsIdentity> s jakékoli požadované <xref:System.Security.Claims.Claim>s a volání <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> k přihlášení uživatele:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` Vytvoří do zašifrovaného souboru cookie a přidá jej do aktuální odpovědi. Pokud `AuthenticationScheme` není zadán, výchozí schéma se používá.

ASP.NET Core [ochranu dat](xref:security/data-protection/using-data-protection) pro šifrování je použit systém. Pro aplikaci hostovanou ve více počítačích, zatížení vyrovnávání mezi aplikacemi, nebo pomocí webové farmy, [Konfigurace ochrany dat](xref:security/data-protection/configuration/overview) používat stejné aktualizační kanál klíč a identifikátor aplikace.

## <a name="sign-out"></a>Odhlásit se

Chcete-li odhlásit aktuálního uživatele a odstranit jejich souborů cookie, zavolejte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Pokud `CookieAuthenticationDefaults.AuthenticationScheme` (nebo "Soubory cookie") se nepoužívá jako schéma (například "ContosoCookie"), zadejte schéma používané při konfiguraci zprostředkovatele ověřování. V opačném případě se používá výchozí schéma.

## <a name="react-to-back-end-changes"></a>Reagovat na změny back-end

Po vytvoření souboru cookie je soubor cookie jediný zdroj identity. Pokud uživatelský účet zakázaná v back endových systémů:

* Aplikace soubor cookie ověřování systém bude dál zpracovávat žádosti na základě souboru cookie ověřování.
* Uživatel zůstane přihlášený do aplikace za předpokladu, je platný soubor cookie ověřování.

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> Události je možné zachytit a přepsat ověření souboru cookie identity. Ověřování souborů cookie u každého požadavku snižuje riziko odvolané uživatele, kteří používají aplikaci.

Jedním z přístupů k ověření souboru cookie je založená na udržování přehledu o změně uživatelskou databázi. Pokud databáze nebylo změněno od uživatele soubor cookie vydala, není nutné opakované ověření uživatele, pokud jejich souborů cookie je stále platný. V ukázkové aplikaci, databázi je implementována v `IUserRepository` a uloží `LastChanged` hodnotu. Při aktualizaci uživatele v databázi, `LastChanged` je hodnota nastavena na aktuální čas.

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

K implementaci přepsání `ValidatePrincipal` události, napište metodu s následující signaturou ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Tady je příklad implementace `CookieAuthenticationEvents`:

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

Zaregistrovat instanci události během registrace služby souborů cookie v `Startup.ConfigureServices` metody. Zadejte [obor registrace služby](xref:fundamentals/dependency-injection#service-lifetimes) pro vaše `CustomCookieAuthenticationEvents` třídy:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Představte si situaci, ve kterém se aktualizuje uživatelské jméno&mdash;rozhodnutí, která nemá vliv na zabezpečení žádným způsobem. Pokud chcete aktualizovat nedestruktivně hlavní název uživatele, zavolejte `context.ReplacePrincipal` a nastavit `context.ShouldRenew` vlastnost `true`.

> [!WARNING]
> Tento přístup je zde popsáno, se aktivuje u každého požadavku. Ověřuje se ověřovací soubory cookie pro všechny uživatele v každé žádosti může způsobit snížení výkonu velké aplikace.

## <a name="persistent-cookies"></a>Trvalé soubory cookie

Můžete chtít soubor cookie zachovat v rámci relací prohlížeče. Tato trvalost má být povoleno pouze explicitní uživatelské souhlas s "Zapamatovat" zaškrtávací políčko na přihlašovací nebo mechanismus podobný. 

Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který odolává prostřednictvím prohlížeče UZÁVĚRECH. Klouzavé vypršení platnosti nastavení jste dříve nakonfigurovali jsou zachované. Pokud se při zavření prohlížeče vyprší platnost souboru cookie, vymaže prohlížeči soubor cookie po jeho restartování.

Nastavte <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> k `true` v <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-cookie-expiration"></a>Soubor cookie absolutní vypršení platnosti

Čas absolutní vypršení platnosti můžete nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>. K vytvoření trvalého souboru cookie, `IsPersistent` musí být také nastavena. V opačném případě soubor cookie se vytvoří s životností založeného na relacích a může vyprší před nebo po lístek ověřování, který ji obsahuje. Když `ExpiresUtc` je nastaven, přepíše hodnotu <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> možnost <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, pokud nastavení.

Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který má platnost po dobu 20 minut. To ignoruje klouzavé vypršení platnosti nastavení předtím nakonfigurovali.

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Role na základě zásad kontroly](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

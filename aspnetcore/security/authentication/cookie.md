---
title: Použití ověřování souborem cookie bez ASP.NET Core identity
author: rick-anderson
description: Naučte se používat ověřování souborem cookie bez ASP.NET Core identity.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
uid: security/authentication/cookie
ms.openlocfilehash: b7c8b2cccb27dd6818330b17439675e41bfef013
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219204"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a>Použití ověřování souborem cookie bez ASP.NET Core identity

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů. Můžete ale použít poskytovatele ověřování na základě souborů cookie bez ASP.NET Core identity. Další informace naleznete v tématu <xref:security/authentication/identity>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace. Použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo k přihlášení uživatele. Uživatel je ověřený v metodě `AuthenticateUser` v souboru *Pages/Account/Login. cshtml. cs* . V reálném příkladu by byl uživatel ověřený proti databázi.

## <a name="configuration"></a>Konfigurace

Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro balíček [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

V metodě `Startup.ConfigureServices` vytvořte služby middlewaru ověřování pomocí metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>:

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> předaný do `AddAuthentication` nastaví výchozí schéma ověřování pro aplikaci. `AuthenticationScheme` je užitečné v případě, že existuje více instancí ověřování souborem cookie a chcete [autorizovat s konkrétním schématem](xref:security/authorization/limitingidentitybyscheme). Nastavení `AuthenticationScheme` [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje pro schéma hodnotu "cookies". Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.

Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikace. Pokud není <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>schéma ověřování souborů cookie k dispozici, používá `CookieAuthenticationDefaults.AuthenticationScheme` ("soubory cookie").

Vlastnost <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ověřovacího souboru cookie je ve výchozím nastavení nastavena na hodnotu `true`. Soubory cookie ověřování jsou povoleny, pokud návštěvník webu nesouhlasí s shromažďováním dat. Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.

V `Startup.Configure`voláním `UseAuthentication` a `UseAuthorization` nastavte vlastnost `HttpContext.User` a spusťte middleware autorizace pro požadavky. Před voláním `UseEndpoints`volejte `UseAuthentication` a `UseAuthorization` metody:

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

Třída <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> se používá ke konfiguraci možností poskytovatele ověřování.

Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v metodě `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Middleware zásad souborů cookie

[Middleware zásad souborů cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje využít zásady souborů cookie. Přidání middlewaru do kanálu zpracování aplikace je v pořádku,&mdash;ovlivňuje pouze podřízené komponenty registrované v kanálu.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

K řízení globálních charakteristik zpracování souborů cookie a připojení do obslužných rutin zpracování souborů cookie, když se připojí nebo odstraní soubory cookie, použijte <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytované middlewaru zásad souborů cookie.

Výchozí hodnota <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> je `SameSiteMode.Lax`, aby povolovala ověřování OAuth2. Chcete-li striktně vymáhat zásady `SameSiteMode.Strict`stejné lokality, nastavte `MinimumSameSitePolicy`. I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souborů cookie pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Nastavení middlewaru zásad souborů cookie pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle matice níže.

| MinimumSameSitePolicy | Cookie.SameSite | Nastavení výsledného souboru cookie. SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Vytvoření ověřovacího souboru cookie

Chcete-li vytvořit soubor cookie obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal>. Informace o uživateli jsou serializovány a uloženy v souboru cookie. 

Vytvořte <xref:System.Security.Claims.ClaimsIdentity> s libovolným požadovaným <xref:System.Security.Claims.Claim>s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

`SignInAsync` vytvoří zašifrovaný soubor cookie a přidá ho k aktuální odpovědi. Pokud není zadaný `AuthenticationScheme`, použije se výchozí schéma.

Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování. Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.

## <a name="sign-out"></a>Odhlásit se

Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Pokud se jako schéma (například "ContosoCookie") nepoužívá `CookieAuthenticationDefaults.AuthenticationScheme` (nebo "soubory cookie"), zadejte schéma používané při konfiguraci poskytovatele ověřování. V opačném případě se použije výchozí schéma.

## <a name="react-to-back-end-changes"></a>Reakce na back-endové změny

Po vytvoření souboru cookie je soubor cookie jediným zdrojem identity. Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:

* Systém ověřování souborů cookie aplikace nadále zpracovává požadavky založené na souboru cookie ověřování.
* Uživatel zůstane přihlášený k aplikaci, pokud je ověřovací soubor cookie platný.

Událost <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> lze použít k zachycení a přepsání ověřování identity souborů cookie. Ověření souboru cookie u všech požadavků snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.

Jeden přístup k ověření souboru cookie je založen na sledování toho, kdy se databáze uživatele změní. Pokud se databáze od vydání souboru cookie uživatele nezměnila, není nutné znovu ověřit uživatele, pokud je jejich soubor cookie stále platný. V ukázkové aplikaci je databáze implementovaná v `IUserRepository` a ukládá hodnotu `LastChanged`. Když se uživatel v databázi aktualizuje, hodnota `LastChanged` je nastavená na aktuální čas.

Aby se soubor cookie neověřoval, když se databáze změní na základě `LastChanged` hodnoty, vytvořte soubor cookie s `LastChanged` deklarací identity obsahující aktuální hodnotu `LastChanged` z databáze:

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

Chcete-li implementovat přepsání pro událost `ValidatePrincipal`, zapište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Následuje příklad implementace `CookieAuthenticationEvents`:

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

Zaregistrujte instanci události během registrace služby souborů cookie v metodě `Startup.ConfigureServices`. Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro třídu `CustomCookieAuthenticationEvents`:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Vezměte v úvahu situaci, kdy se uživatelské jméno aktualizuje&mdash;rozhodnutí, které nijak neovlivňuje zabezpečení. Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte vlastnost `context.ShouldRenew` na `true`.

> [!WARNING]
> Přístup, který je zde popsán, se spustí při každém požadavku. Ověřování souborů cookie pro ověřování pro všechny uživatele na všech žádostech může mít za následek velkou sankci pro výkon aplikace.

## <a name="persistent-cookies"></a>Trvalé soubory cookie

Můžete chtít, aby soubor cookie trval mezi relacemi prohlížeče. Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu. 

Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který se zachová v rámci zavření prohlížeče. Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná. Pokud soubor cookie vyprší v době, kdy je prohlížeč zavřen, prohlížeč po restartování odstraní soubor cookie.

Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` v <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:

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

## <a name="absolute-cookie-expiration"></a>Vypršení platnosti absolutního souboru cookie

Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>. Chcete-li vytvořit trvalý soubor cookie, musí být také nastavena `IsPersistent`. V opačném případě se soubor cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje. Pokud je nastavena `ExpiresUtc`, přepíše hodnotu <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, pokud je nastavena.

Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který trvá 20 minut. Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů. Můžete ale použít poskytovatele ověřování ověřování na základě souborů cookie bez ASP.NET Core identity. Další informace naleznete v tématu <xref:security/authentication/identity>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace. Použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo k přihlášení uživatele. Uživatel je ověřený v metodě `AuthenticateUser` v souboru *Pages/Account/Login. cshtml. cs* . V reálném příkladu by byl uživatel ověřený proti databázi.

## <a name="configuration"></a>Konfigurace

Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro balíček [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

V metodě `Startup.ConfigureServices` vytvořte službu middleware ověřování pomocí metod <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> a <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> předaný do `AddAuthentication` nastaví výchozí schéma ověřování pro aplikaci. `AuthenticationScheme` je užitečné v případě, že existuje více instancí ověřování souborem cookie a chcete [autorizovat s konkrétním schématem](xref:security/authorization/limitingidentitybyscheme). Nastavení `AuthenticationScheme` [CookieAuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje pro schéma hodnotu "cookies". Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.

Schéma ověřování aplikace se liší od schématu ověřování souborů cookie aplikace. Pokud není <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>schéma ověřování souborů cookie k dispozici, používá `CookieAuthenticationDefaults.AuthenticationScheme` ("soubory cookie").

Vlastnost <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ověřovacího souboru cookie je ve výchozím nastavení nastavena na hodnotu `true`. Soubory cookie ověřování jsou povoleny, pokud návštěvník webu nesouhlasí s shromažďováním dat. Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.

V metodě `Startup.Configure` voláním metody `UseAuthentication` volejte middleware ověřování, který nastaví vlastnost `HttpContext.User`. Před voláním `UseMvcWithDefaultRoute` nebo `UseMvc`volejte metodu `UseAuthentication`:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

Třída <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> se používá ke konfiguraci možností poskytovatele ověřování.

Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v metodě `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a>Middleware zásad souborů cookie

[Middleware zásad souborů cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje využít zásady souborů cookie. Přidání middlewaru do kanálu zpracování aplikace je v pořádku,&mdash;ovlivňuje pouze podřízené komponenty registrované v kanálu.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

K řízení globálních charakteristik zpracování souborů cookie a připojení do obslužných rutin zpracování souborů cookie, když se připojí nebo odstraní soubory cookie, použijte <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytované middlewaru zásad souborů cookie.

Výchozí hodnota <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> je `SameSiteMode.Lax`, aby povolovala ověřování OAuth2. Chcete-li striktně vymáhat zásady `SameSiteMode.Strict`stejné lokality, nastavte `MinimumSameSitePolicy`. I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň zabezpečení souborů cookie pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Nastavení middlewaru zásad souborů cookie pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle matice níže.

| MinimumSameSitePolicy | Cookie.SameSite | Nastavení výsledného souboru cookie. SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode.None     | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Lax      | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Lax<br>SameSiteMode.Lax<br>SameSiteMode.Strict |
| SameSiteMode.Strict   | SameSiteMode.None<br>SameSiteMode.Lax<br>SameSiteMode.Strict | SameSiteMode.Strict<br>SameSiteMode.Strict<br>SameSiteMode.Strict |

## <a name="create-an-authentication-cookie"></a>Vytvoření ověřovacího souboru cookie

Chcete-li vytvořit soubor cookie obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal>. Informace o uživateli jsou serializovány a uloženy v souboru cookie. 

Vytvořte <xref:System.Security.Claims.ClaimsIdentity> s libovolným požadovaným <xref:System.Security.Claims.Claim>s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` vytvoří zašifrovaný soubor cookie a přidá ho k aktuální odpovědi. Pokud není zadaný `AuthenticationScheme`, použije se výchozí schéma.

Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování. Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.

## <a name="sign-out"></a>Odhlásit se

Chcete-li odhlásit aktuálního uživatele a odstranit svůj soubor cookie, zavolejte <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Pokud se jako schéma (například "ContosoCookie") nepoužívá `CookieAuthenticationDefaults.AuthenticationScheme` (nebo "soubory cookie"), zadejte schéma používané při konfiguraci poskytovatele ověřování. V opačném případě se použije výchozí schéma.

## <a name="react-to-back-end-changes"></a>Reakce na back-endové změny

Po vytvoření souboru cookie je soubor cookie jediným zdrojem identity. Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:

* Systém ověřování souborů cookie aplikace nadále zpracovává požadavky založené na souboru cookie ověřování.
* Uživatel zůstane přihlášený k aplikaci, pokud je ověřovací soubor cookie platný.

Událost <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> lze použít k zachycení a přepsání ověřování identity souborů cookie. Ověření souboru cookie u všech požadavků snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.

Jeden přístup k ověření souboru cookie je založen na sledování toho, kdy se databáze uživatele změní. Pokud se databáze od vydání souboru cookie uživatele nezměnila, není nutné znovu ověřit uživatele, pokud je jejich soubor cookie stále platný. V ukázkové aplikaci je databáze implementovaná v `IUserRepository` a ukládá hodnotu `LastChanged`. Když se uživatel v databázi aktualizuje, hodnota `LastChanged` je nastavená na aktuální čas.

Aby se soubor cookie neověřoval, když se databáze změní na základě `LastChanged` hodnoty, vytvořte soubor cookie s `LastChanged` deklarací identity obsahující aktuální hodnotu `LastChanged` z databáze:

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

Chcete-li implementovat přepsání pro událost `ValidatePrincipal`, zapište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Následuje příklad implementace `CookieAuthenticationEvents`:

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

Zaregistrujte instanci události během registrace služby souborů cookie v metodě `Startup.ConfigureServices`. Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro třídu `CustomCookieAuthenticationEvents`:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Vezměte v úvahu situaci, kdy se uživatelské jméno aktualizuje&mdash;rozhodnutí, které nijak neovlivňuje zabezpečení. Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte vlastnost `context.ShouldRenew` na `true`.

> [!WARNING]
> Přístup, který je zde popsán, se spustí při každém požadavku. Ověřování souborů cookie pro ověřování pro všechny uživatele na všech žádostech může mít za následek velkou sankci pro výkon aplikace.

## <a name="persistent-cookies"></a>Trvalé soubory cookie

Můžete chtít, aby soubor cookie trval mezi relacemi prohlížeče. Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu. 

Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který se zachová v rámci zavření prohlížeče. Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná. Pokud soubor cookie vyprší v době, kdy je prohlížeč zavřen, prohlížeč po restartování odstraní soubor cookie.

Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` v <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:

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

## <a name="absolute-cookie-expiration"></a>Vypršení platnosti absolutního souboru cookie

Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>. Chcete-li vytvořit trvalý soubor cookie, musí být také nastavena `IsPersistent`. V opačném případě se soubor cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje. Pokud je nastavena `ExpiresUtc`, přepíše hodnotu <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, pokud je nastavena.

Následující fragment kódu vytvoří identitu a odpovídající soubor cookie, který trvá 20 minut. Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.

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

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Kontroly rolí na základě zásad](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

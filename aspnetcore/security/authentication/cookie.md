---
title: Použít cookie ověřování bez ASP.NET Core Identity
author: rick-anderson
description: Naučte se používat cookie ověřování bez ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: security/authentication/cookie
ms.openlocfilehash: 211b6c7ec0bc7a48671e614427961cb332d06aa3
ms.sourcegitcommit: c0a15ab8549cb729731a0fdf1d7da0b7feaa11ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91671766"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a>Použít cookie ověřování bez ASP.NET Core Identity

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů. Nicméně cookie poskytovatele ověřování založeného na typu se ASP.NET Core Identity dá použít. Další informace naleznete v tématu <xref:security/authentication/identity>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace. K přihlášení uživatele použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo. Uživatel je ověřený v `AuthenticateUser` metodě v souboru *Pages/Account/Login. cshtml. cs* . V reálném příkladu by byl uživatel ověřený proti databázi.

## <a name="configuration"></a>Konfigurace

V `Startup.ConfigureServices` metodě vytvořte služby ověřování middlewaru pomocí <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod a:

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> předáno pro `AddAuthentication` Nastavení výchozího schématu ověřování pro aplikaci. `AuthenticationScheme` je užitečné v případě, že existuje více instancí cookie ověřování a vy chcete [autorizovat pomocí konkrétního schématu](xref:security/authorization/limitingidentitybyscheme). Nastavení `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje Cookie pro schéma hodnotu "s". Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.

Schéma ověřování aplikace se liší od cookie schématu ověřování aplikace. Pokud cookie není k dispozici schéma ověřování pro <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , používá `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").

Vlastnost ověřování cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je standardně nastavená na hodnotu `true` . Ověřování cookie s je povoleno, pokud návštěvník webu nesouhlasí s shromažďováním dat. Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.

V `Startup.Configure` , zavolejte `UseAuthentication` a a `UseAuthorization` nastavte `HttpContext.User` vlastnost a spusťte middleware autorizace pro požadavky. `UseAuthentication` `UseAuthorization` Před voláním volejte metody a `UseEndpoints` :

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Třída se používá ke konfiguraci možností poskytovatele ověřování.

Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metodě:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>Cookie Middleware zásad

[ Cookie Middleware zásad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje cookie Možnosti zásad. Přidání middlewaru do kanálu zpracování aplikace je v pořádku &mdash; . týká se pouze podřízených komponent registrovaných v kanálu.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytované pro Cookie middleware zásad k řízení globálních charakteristik cookie zpracování a cookie připojení k obslužným rutinám zpracování, pokud cookie jsou připojeny nebo odstraněny.

Výchozí <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnota je `SameSiteMode.Lax` Povolit ověřování OAuth2. Chcete-li striktně vyhovět zásadě stejného serveru `SameSiteMode.Strict` , nastavte `MinimumSameSitePolicy` . I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň cookie zabezpečení pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

CookieNastavení middlewaru zásad pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle matice níže.

| MinimumSameSitePolicy | Cookie. SameSite | Výsledek Cookie . Nastavení SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode. None     | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. LAX      | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. LAX<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. Strict   | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. Strict<br>SameSiteMode. Strict<br>SameSiteMode. Strict |

## <a name="create-an-authentication-no-loccookie"></a>Vytvoření ověřování cookie

Chcete-li vytvořit cookie informace o uživatelích obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal> . Informace o uživateli jsou serializovány a uloženy v cookie . 

Vytvořit <xref:System.Security.Claims.ClaimsIdentity> s libovolným vyžadovaným <xref:System.Security.Claims.Claim> s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

`SignInAsync` Vytvoří zašifrovaný objekt cookie a přidá ho k aktuální odpovědi. Pokud `AuthenticationScheme` není zadaný, použije se výchozí schéma.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> se ve výchozím nastavení používá jenom pro několik specifických cest, například přihlašovací cesta a cesty odhlášení. Další informace najdete v tématu [ Cookie AuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).

Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování. Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.

## <a name="sign-out"></a>Odhlásit se

Chcete-li odhlásit aktuálního uživatele a odstranit jeho cookie volání, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> postupujte takto:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Pokud `CookieAuthenticationDefaults.AuthenticationScheme` (nebo " Cookie s") se nepoužívá jako schéma (například "contoso Cookie "), zadejte schéma používané při konfiguraci zprostředkovatele ověřování. V opačném případě se použije výchozí schéma.

Když se prohlížeč zavře, automaticky odstraní relaci na základě cookie (netrvalé cookie s), ale cookie při zavření jednotlivé karty se nevymaže žádné. Server není upozorněn na události zavření karty nebo prohlížeče.

## <a name="react-to-back-end-changes"></a>Reakce na back-endové změny

Po cookie vytvoření je cookie jediným zdrojem identity. Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:

* cookieSystém ověřování aplikace nadále zpracovává požadavky na základě ověření cookie .
* Uživatel zůstane přihlášený k aplikaci, pokud cookie je ověřování platné.

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Událost se dá použít k zachycení a přepsání ověřování cookie identity. Ověřování cookie u každé žádosti snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.

Jeden z přístupů k cookie ověření je založen na sledování, kdy se uživatel změní. Pokud se databáze od vydání uživatele nezměnila cookie , není nutné znovu ověřit uživatele, pokud cookie je jejich platnost stále platná. V ukázkové aplikaci je databáze implementována v `IUserRepository` a ukládá `LastChanged` hodnotu. Při aktualizaci uživatele v databázi `LastChanged` je hodnota nastavena na aktuální čas.

Chcete-li zrušit platnost cookie při změně databáze na základě `LastChanged` hodnoty, vytvořte objekt cookie s `LastChanged` deklarací identity obsahující aktuální `LastChanged` hodnotu z databáze:

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

Chcete-li implementovat přepsání pro `ValidatePrincipal` událost, napište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Následuje příklad implementace `CookieAuthenticationEvents` :

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

Zaregistrujte instanci události během cookie Registrace služby v `Startup.ConfigureServices` metodě. Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro `CustomCookieAuthenticationEvents` třídu:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Vezměte v úvahu situaci, kdy je jméno uživatele Aktualizováno &mdash; rozhodnutím, které nijak neovlivňuje zabezpečení. Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte `context.ShouldRenew` vlastnost na `true` .

> [!WARNING]
> Přístup, který je zde popsán, se spustí při každém požadavku. Ověřování ověřování cookie u všech uživatelů na všech žádostech může mít za následek velkou penalizaci výkonu aplikace.

## <a name="persistent-no-loccookies"></a>Trvalé cookie s

Možná budete chtít, cookie aby trvala v rámci relací prohlížeče. Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu. 

Následující fragment kódu vytvoří identitu a odpovídající cookie , která je zachována v případě uzavření prohlížeče. Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná. Pokud cookie vyprší platnost při zavření prohlížeče, prohlížeč cookie po jeho restartování zruší.

Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :

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

## <a name="absolute-no-loccookie-expiration"></a>Absolutní cookie vypršení platnosti

Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> . Aby bylo možné vytvořit trvalé cookie , `IsPersistent` musí být také nastaveno. V opačném případě se cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje. Když `ExpiresUtc` je nastaveno, přepíše hodnotu <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , pokud je nastavena.

Následující fragment kódu vytvoří identitu a odpovídajícím způsobem cookie , který trvá 20 minut. Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.

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

ASP.NET Core Identity je úplný, plnohodnotný zprostředkovatel ověřování pro vytváření a správu přihlašovacích údajů. Nicméně cookie poskytovatele ověřování založeného na typu se ASP.NET Core Identity dá použít. Další informace naleznete v tématu <xref:security/authentication/identity>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Pro demonstrační účely v ukázkové aplikaci je uživatelský účet pro hypotetického uživatele Marie Rodriguez pevně zakódované do aplikace. K přihlášení uživatele použijte **e-mailovou** adresu `maria.rodriguez@contoso.com` a jakékoli heslo. Uživatel je ověřený v `AuthenticateUser` metodě v souboru *Pages/Account/Login. cshtml. cs* . V reálném příkladu by byl uživatel ověřený proti databázi.

## <a name="configuration"></a>Konfigurace

Pokud aplikace nepoužívá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček v souboru projektu pro [Microsoft. AspNetCore. Authentication. Cookie balíček s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .

V `Startup.ConfigureServices` metodě vytvořte službu ověřování middlewaru pomocí <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> metod a:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> předáno pro `AddAuthentication` Nastavení výchozího schématu ověřování pro aplikaci. `AuthenticationScheme` je užitečné v případě, že existuje více instancí cookie ověřování a vy chcete [autorizovat pomocí konkrétního schématu](xref:security/authorization/limitingidentitybyscheme). Nastavení `AuthenticationScheme` na [ Cookie AuthenticationDefaults. AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) poskytuje Cookie pro schéma hodnotu "s". Můžete dodat libovolnou řetězcovou hodnotu, která rozlišuje schéma.

Schéma ověřování aplikace se liší od cookie schématu ověřování aplikace. Pokud cookie není k dispozici schéma ověřování pro <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , používá `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").

Vlastnost ověřování cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> je standardně nastavená na hodnotu `true` . Ověřování cookie s je povoleno, pokud návštěvník webu nesouhlasí s shromažďováním dat. Další informace naleznete v tématu <xref:security/gdpr#essential-cookies>.

V `Startup.Configure` metodě zavolejte `UseAuthentication` metodu pro vyvolání middleware ověřování, který nastaví `HttpContext.User` vlastnost. Zavolejte `UseAuthentication` metodu před voláním `UseMvcWithDefaultRoute` nebo `UseMvc` :

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Třída se používá ke konfiguraci možností poskytovatele ověřování.

Nastavte `CookieAuthenticationOptions` v konfiguraci služby pro ověřování v `Startup.ConfigureServices` metodě:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>Cookie Middleware zásad

[ Cookie Middleware zásad](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) umožňuje cookie Možnosti zásad. Přidání middlewaru do kanálu zpracování aplikace je v pořádku &mdash; . týká se pouze podřízených komponent registrovaných v kanálu.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Použití <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> poskytované pro Cookie middleware zásad k řízení globálních charakteristik cookie zpracování a cookie připojení k obslužným rutinám zpracování, pokud cookie jsou připojeny nebo odstraněny.

Výchozí <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> hodnota je `SameSiteMode.Lax` Povolit ověřování OAuth2. Chcete-li striktně vyhovět zásadě stejného serveru `SameSiteMode.Strict` , nastavte `MinimumSameSitePolicy` . I když toto nastavení přeruší OAuth2 a další schémata ověřování mezi zdroji, zvyšuje úroveň cookie zabezpečení pro jiné typy aplikací, které nespoléhají na zpracování žádostí mezi zdroji.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

CookieNastavení middlewaru zásad pro `MinimumSameSitePolicy` může ovlivnit nastavení `Cookie.SameSite` v `CookieAuthenticationOptions` nastavení podle matice níže.

| MinimumSameSitePolicy | Cookie. SameSite | Výsledek Cookie . Nastavení SameSite |
| --------------------- | --------------- | --------------------------------- |
| SameSiteMode. None     | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. LAX      | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. LAX<br>SameSiteMode. LAX<br>SameSiteMode. Strict |
| SameSiteMode. Strict   | SameSiteMode. None<br>SameSiteMode. LAX<br>SameSiteMode. Strict | SameSiteMode. Strict<br>SameSiteMode. Strict<br>SameSiteMode. Strict |

## <a name="create-an-authentication-no-loccookie"></a>Vytvoření ověřování cookie

Chcete-li vytvořit cookie informace o uživatelích obsahující informace o uživateli, vytvořte <xref:System.Security.Claims.ClaimsPrincipal> . Informace o uživateli jsou serializovány a uloženy v cookie . 

Vytvořit <xref:System.Security.Claims.ClaimsIdentity> s libovolným vyžadovaným <xref:System.Security.Claims.Claim> s a voláním <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> pro přihlášení uživatele:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` Vytvoří zašifrovaný objekt cookie a přidá ho k aktuální odpovědi. Pokud `AuthenticationScheme` není zadaný, použije se výchozí schéma.

Systém [ochrany dat](xref:security/data-protection/using-data-protection) ASP.NET Core slouží k šifrování. Pro aplikaci hostovanou na více počítačích, Vyrovnávání zatížení napříč aplikacemi nebo při použití webové farmy [nakonfigurujte ochranu dat](xref:security/data-protection/configuration/overview) tak, aby používala stejný identifikátor Key Ring a App.

## <a name="sign-out"></a>Odhlásit se

Chcete-li odhlásit aktuálního uživatele a odstranit jeho cookie volání, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> postupujte takto:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Pokud `CookieAuthenticationDefaults.AuthenticationScheme` (nebo " Cookie s") se nepoužívá jako schéma (například "contoso Cookie "), zadejte schéma používané při konfiguraci zprostředkovatele ověřování. V opačném případě se použije výchozí schéma.

## <a name="react-to-back-end-changes"></a>Reakce na back-endové změny

Po cookie vytvoření je cookie jediným zdrojem identity. Pokud je uživatelský účet v systémech back-end zakázaný, postupujte takto:

* cookieSystém ověřování aplikace nadále zpracovává požadavky na základě ověření cookie .
* Uživatel zůstane přihlášený k aplikaci, pokud cookie je ověřování platné.

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Událost se dá použít k zachycení a přepsání ověřování cookie identity. Ověřování cookie u každé žádosti snižuje riziko odvolaných uživatelů, kteří přistupují k aplikaci.

Jeden z přístupů k cookie ověření je založen na sledování, kdy se uživatel změní. Pokud se databáze od vydání uživatele nezměnila cookie , není nutné znovu ověřit uživatele, pokud cookie je jejich platnost stále platná. V ukázkové aplikaci je databáze implementována v `IUserRepository` a ukládá `LastChanged` hodnotu. Při aktualizaci uživatele v databázi `LastChanged` je hodnota nastavena na aktuální čas.

Chcete-li zrušit platnost cookie při změně databáze na základě `LastChanged` hodnoty, vytvořte objekt cookie s `LastChanged` deklarací identity obsahující aktuální `LastChanged` hodnotu z databáze:

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

Chcete-li implementovat přepsání pro `ValidatePrincipal` událost, napište metodu s následujícím podpisem ve třídě, která je odvozena z <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Následuje příklad implementace `CookieAuthenticationEvents` :

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

Zaregistrujte instanci události během cookie Registrace služby v `Startup.ConfigureServices` metodě. Poskytněte [registraci oboru služby](xref:fundamentals/dependency-injection#service-lifetimes) pro `CustomCookieAuthenticationEvents` třídu:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Vezměte v úvahu situaci, kdy je jméno uživatele Aktualizováno &mdash; rozhodnutím, které nijak neovlivňuje zabezpečení. Pokud chcete nedestruktivní aktualizaci objektu zabezpečení uživatele, zavolejte `context.ReplacePrincipal` a nastavte `context.ShouldRenew` vlastnost na `true` .

> [!WARNING]
> Přístup, který je zde popsán, se spustí při každém požadavku. Ověřování ověřování cookie u všech uživatelů na všech žádostech může mít za následek velkou penalizaci výkonu aplikace.

## <a name="persistent-no-loccookies"></a>Trvalé cookie s

Možná budete chtít, cookie aby trvala v rámci relací prohlížeče. Tato stálost by měla být povolena pouze s explicitním souhlasem uživatele s zaškrtávacím políčkem "zapamatovat mě" při přihlašování nebo podobném mechanismu. 

Následující fragment kódu vytvoří identitu a odpovídající cookie , která je zachována v případě uzavření prohlížeče. Budou dodržena všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná. Pokud cookie vyprší platnost při zavření prohlížeče, prohlížeč cookie po jeho restartování zruší.

Nastavit <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> na `true` <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :

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

## <a name="absolute-no-loccookie-expiration"></a>Absolutní cookie vypršení platnosti

Absolutní čas vypršení platnosti lze nastavit pomocí <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> . Aby bylo možné vytvořit trvalé cookie , `IsPersistent` musí být také nastaveno. V opačném případě se cookie vytvoří s životností založenou na relaci a může vypršet buď před, nebo za ověřovacím lístkem, který obsahuje. Když `ExpiresUtc` je nastaveno, přepíše hodnotu <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> možnosti <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , pokud je nastavena.

Následující fragment kódu vytvoří identitu a odpovídajícím způsobem cookie , který trvá 20 minut. Tato možnost ignoruje všechna nastavení klouzavého vypršení platnosti dříve nakonfigurovaná.

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

## <a name="additional-resources"></a>Další zdroje informací

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Kontroly rolí na základě zásad](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>

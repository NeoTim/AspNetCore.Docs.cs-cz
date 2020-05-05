---
title: Vlastní zprostředkovatelé autorizačních zásad v ASP.NET Core
author: mjrousos
description: Naučte se používat vlastní IAuthorizationPolicyProvider v aplikaci ASP.NET Core k dynamickému generování zásad autorizace.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 1db78e5b2cea964471e4eea090f713f6af5f4740
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777537"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Vlastní zprostředkovatelé zásad autorizace využívající IAuthorizationPolicyProvider v ASP.NET Core 

O [Jan Rousos](https://github.com/mjrousos)

Při použití [autorizace založené na zásadách](xref:security/authorization/policies)se obvykle zaregistrují zásady voláním `AuthorizationOptions.AddPolicy` jako součást konfigurace autorizační služby. V některých scénářích to nemusí být možné (nebo vhodné) k registraci všech zásad autorizace tímto způsobem. V těchto případech můžete k řízení způsobu, jakým jsou dodány zásady autorizace, použít vlastní `IAuthorizationPolicyProvider` .

Příklady scénářů, které mohou být užitečné pro vlastní [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) , zahrnují:

* Použití externí služby k zajištění vyhodnocení zásad.
* Použití velkého rozsahu zásad (například pro různá čísla místností nebo stáří), takže nedává smysl přidat jednotlivé zásady autorizace `AuthorizationOptions.AddPolicy` volání.
* Vytváření zásad za běhu založené na informacích v externím zdroji dat (jako je databáze) nebo k dynamickému určování požadavků autorizace prostřednictvím jiného mechanismu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) z [úložiště GitHub AspNetCore](https://github.com/dotnet/AspNetCore) Stáhněte si soubor ZIP pro úložiště dotnet/AspNetCore. Rozbalte soubor. Přejděte do složky projektu *Src/Security/Samples/CustomPolicyProvider* .

## <a name="customize-policy-retrieval"></a>Přizpůsobení načtení zásad

Aplikace ASP.NET Core používají implementaci `IAuthorizationPolicyProvider` rozhraní k načtení zásad autorizace. Ve výchozím nastavení je [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) zaregistrován a používán. `DefaultAuthorizationPolicyProvider`Vrátí zásady ze `AuthorizationOptions` zadaného `IServiceCollection.AddAuthorization` volání.

Toto chování si přizpůsobte tak `IAuthorizationPolicyProvider` , že zaregistrujete jinou implementaci do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace. 

`IAuthorizationPolicyProvider` Rozhraní obsahuje tři rozhraní API:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) vrací zásadu autorizace pro daný název.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) vrátí výchozí zásady autorizace (zásady použité pro `[Authorize]` atributy bez zadaných zásad). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) vrátí záložní zásadu autorizace (zásady používané middlewarem autorizace, když není zadaná žádná zásada). 

Implementací těchto rozhraní API můžete přizpůsobit způsob poskytování zásad autorizace.

## <a name="parameterized-authorize-attribute-example"></a>Příklad parametrizovaného autorizačního atributu

Jeden scénář, `IAuthorizationPolicyProvider` kde je užitečný, je `[Authorize]` povolit vlastní atributy, jejichž požadavky závisí na parametru. Například v dokumentaci pro [autorizaci na základě zásad](xref:security/authorization/policies) se jako ukázka použila zásada na základě stáří ("AtLeast21"). Pokud by měly být k dispozici různé akce kontroleru v aplikaci pro uživatele *různého* stáří, může být užitečné mít mnoho různých zásad založených na věku. Místo registrace všech různých zásad založených na věku, v `AuthorizationOptions`nichž bude aplikace potřebovat, můžete zásady vygenerovat dynamicky s vlastními. `IAuthorizationPolicyProvider` Aby bylo možné zásady používat snadněji, můžete poznámky provádět pomocí vlastního autorizačního atributu, jako `[MinimumAgeAuthorize(20)]`je.

## <a name="custom-authorization-attributes"></a>Vlastní autorizační atributy

Zásady autorizace se identifikují podle jejich názvů. Vlastní `MinimumAgeAuthorizeAttribute` popsaná dříve musí mapovat argumenty na řetězec, který lze použít k načtení odpovídajících zásad autorizace. Můžete to provést odvozením z `AuthorizeAttribute` a nastavením `Age` vlastnosti pro zabalení `AuthorizeAttribute.Policy` vlastnosti.

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

Tento typ atributu má `Policy` řetězec na základě pevně kódované předpony (`"MinimumAge"`) a celé číslo předané prostřednictvím konstruktoru.

Můžete ji použít na akce stejným způsobem jako ostatní `Authorize` atributy s tím rozdílem, že jako parametr přebírá celé číslo.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Vlastní IAuthorizationPolicyProvider

Vlastníci `MinimumAgeAuthorizeAttribute` usnadňují vyžádání autorizačních zásad pro případné minimální požadované stáří. Další problém, který se má vyřešit, je ověření, jestli jsou zásady autorizace dostupné pro všechny různé věkové služby. To je místo, `IAuthorizationPolicyProvider` kde je to užitečné.

Při použití `MinimumAgeAuthorizationAttribute`se názvy zásad autorizace řídí vzorem `"MinimumAge" + Age`, takže vlastníci `IAuthorizationPolicyProvider` by měli vytvořit zásady autorizace podle těchto pokynů:

* Analyzuje se věk z názvu zásady.
* Použití `AuthorizationPolicyBuilder` k vytvoření nového`AuthorizationPolicy`
* V tomto a následujících příkladech se předpokládá, že uživatel je ověřený prostřednictvím souboru cookie. `AuthorizationPolicyBuilder` Měl by být vytvořen buď pomocí alespoň jednoho názvu schématu autorizace, nebo vždy úspěšně. V opačném případě nejsou k dispozici žádné informace o tom, jak poskytnout uživateli výzvu a bude vyvolána výjimka.
* Přidávají se požadavky na zásady na základě stáří pomocí `AuthorizationPolicyBuilder.AddRequirements`. V jiných scénářích můžete použít `RequireClaim`místo toho `RequireRole`, nebo `RequireUserName` .

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>Několik poskytovatelů zásad autorizace

Při použití vlastních `IAuthorizationPolicyProvider` implementací Pamatujte na to, že ASP.NET Core používá pouze jednu instanci `IAuthorizationPolicyProvider`. Pokud vlastní zprostředkovatel nedokáže poskytovat zásady autorizace pro všechny názvy zásad, které se použijí, měl by se odložit k poskytovateli zálohování. 

Představte si třeba aplikaci, která potřebuje vlastní věkové zásady a jednodušší načítání zásad na základě rolí. Taková aplikace by mohla použít vlastního zprostředkovatele autorizačních zásad, který:

* Pokusí se analyzovat názvy zásad. 
* Zavolá do jiného poskytovatele zásad (například `DefaultAuthorizationPolicyProvider`), pokud název zásady neobsahuje stáří.

Ukázkovou `IAuthorizationPolicyProvider` implementaci uvedenou výše je možné aktualizovat tak, aby `DefaultAuthorizationPolicyProvider` používala vytvoření poskytovatele zásad zálohování ve svém konstruktoru (aby se použilo pro případ, že se název zásady neshoduje s očekávaným vzorem "minimálního" + stáří).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Pak lze `GetPolicyAsync` metodu aktualizovat tak, aby používala `BackupPolicyProvider` místo vrácení hodnoty null:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Výchozí zásady

Kromě poskytování pojmenovaných autorizačních zásad je nutné `IAuthorizationPolicyProvider` implementovat `GetDefaultPolicyAsync` vlastní zásady autorizace pro `[Authorize]` atributy bez zadaného názvu zásady.

V mnoha případech tento atribut autorizace vyžaduje jenom ověřeného uživatele, takže můžete vytvořit potřebné zásady s voláním `RequireAuthenticatedUser`:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Stejně jako u všech aspektů vlastní `IAuthorizationPolicyProvider`můžete to podle potřeby přizpůsobit. V některých případech může být žádoucí načíst výchozí zásadu z nouzového `IAuthorizationPolicyProvider`režimu.

## <a name="fallback-policy"></a>Záložní zásady

Vlastní `IAuthorizationPolicyProvider` nastavení může volitelně implementovat `GetFallbackPolicyAsync` zásadu, která se používá při [kombinování zásad](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) a kdy nejsou zadané žádné zásady. Pokud `GetFallbackPolicyAsync` vrátí zásadu, která nemá hodnotu null, je vrácená zásada používána middlewarem autorizace, pokud nejsou pro požadavek zadány žádné zásady.

Pokud se nevyžadují žádné záložní zásady, může zprostředkovatel vrátit `null` nebo odložit záložnímu zprostředkovateli:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Použití vlastního IAuthorizationPolicyProvider

Pokud chcete použít vlastní zásady z `IAuthorizationPolicyProvider`, musíte:

* Zaregistrujte `AuthorizationHandler` příslušné typy se vkládáním závislostí (popsané v tématu [autorizace na základě zásad](xref:security/authorization/policies#authorization-handlers)), stejně jako u všech scénářů autorizace na základě zásad.
* Zaregistrujte `IAuthorizationPolicyProvider` vlastní typ v kolekci služby pro vkládání závislostí aplikace ( `Startup.ConfigureServices`v), abyste nahradili výchozího poskytovatele zásad.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Kompletní vlastní `IAuthorizationPolicyProvider` ukázka je k dispozici v [úložišti GitHub/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).

---
title: Vlastní zprostředkovatelé autorizačních zásad v ASP.NET Core
author: mjrousos
description: Naučte se používat vlastní IAuthorizationPolicyProvider v aplikaci ASP.NET Core k dynamickému generování zásad autorizace.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 9f0a0cd5337f7f8d2fc8a4b6902a63b98f6bd702
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828981"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Vlastní zprostředkovatelé zásad autorizace využívající IAuthorizationPolicyProvider v ASP.NET Core 

O [Jan Rousos](https://github.com/mjrousos)

Při použití [ověřování na základě zásad](xref:security/authorization/policies)jsou zásady zaregistrované voláním `AuthorizationOptions.AddPolicy` jako součást konfigurace autorizační služby. V některých scénářích to nemusí být možné (nebo vhodné) k registraci všech zásad autorizace tímto způsobem. V těchto případech můžete k řízení způsobu, jakým jsou dodány zásady autorizace, použít vlastní `IAuthorizationPolicyProvider`.

Příklady scénářů, které mohou být užitečné pro vlastní [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) , zahrnují:

* Použití externí služby k zajištění vyhodnocení zásad.
* Použití velkého rozsahu zásad (například pro různá čísla místností nebo stáří), takže nemá smysl přidat jednotlivé zásady autorizace pomocí `AuthorizationOptions.AddPolicy` volání.
* Vytváření zásad za běhu založené na informacích v externím zdroji dat (jako je databáze) nebo k dynamickému určování požadavků autorizace prostřednictvím jiného mechanismu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) z [úložiště GitHub AspNetCore](https://github.com/dotnet/AspNetCore) Stáhněte si soubor ZIP pro úložiště dotnet/AspNetCore. Rozbalte soubor. Přejděte do složky projektu *Src/Security/Samples/CustomPolicyProvider* .

## <a name="customize-policy-retrieval"></a>Přizpůsobení načtení zásad

ASP.NET Core aplikace používají implementaci rozhraní `IAuthorizationPolicyProvider` k načtení zásad autorizace. Ve výchozím nastavení je [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) zaregistrován a používán. `DefaultAuthorizationPolicyProvider` vrátí zásady z `AuthorizationOptions` poskytnuté ve volání `IServiceCollection.AddAuthorization`.

Toto chování si přizpůsobte tak, že zaregistrujete jinou implementaci `IAuthorizationPolicyProvider` v kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace. 

Rozhraní `IAuthorizationPolicyProvider` obsahuje tři rozhraní API:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) vrací zásadu autorizace pro daný název.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) vrátí výchozí zásady autorizace (zásady používané pro `[Authorize]` atributů bez zadaných zásad). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) vrátí záložní zásadu autorizace (zásady používané middlewarem autorizace, když není zadaná žádná zásada). 

Implementací těchto rozhraní API můžete přizpůsobit způsob poskytování zásad autorizace.

## <a name="parameterized-authorize-attribute-example"></a>Příklad parametrizovaného autorizačního atributu

Jedním z scénářů, kde `IAuthorizationPolicyProvider` je užitečné, je povolení vlastních atributů `[Authorize]`, jejichž požadavky závisí na parametru. Například v dokumentaci pro [autorizaci na základě zásad](xref:security/authorization/policies) se jako ukázka použila zásada na základě stáří ("AtLeast21"). Pokud by měly být k dispozici různé akce kontroleru v aplikaci pro uživatele *různého* stáří, může být užitečné mít mnoho různých zásad založených na věku. Místo registrace všech různých zásad založených na věku, které bude aplikace potřebovat v `AuthorizationOptions`, můžete zásady vygenerovat dynamicky pomocí vlastního `IAuthorizationPolicyProvider`. Aby bylo možné zásady používat snadněji, můžete poznámky provádět pomocí vlastního autorizačního atributu, jako je `[MinimumAgeAuthorize(20)]`.

## <a name="custom-authorization-attributes"></a>Vlastní autorizační atributy

Zásady autorizace se identifikují podle jejich názvů. Vlastní `MinimumAgeAuthorizeAttribute` popsané dříve musí mapovat argumenty na řetězec, který lze použít k načtení odpovídajících zásad autorizace. Můžete to provést odvozením z `AuthorizeAttribute` a nastavením vlastnosti `Age` zabalit vlastnost `AuthorizeAttribute.Policy`.

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

Můžete ji použít na akce stejným způsobem jako ostatní atributy `Authorize` s tím rozdílem, že jako parametr přebírá celé číslo.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Vlastní IAuthorizationPolicyProvider

Vlastní `MinimumAgeAuthorizeAttribute` usnadňuje vyžádání autorizačních zásad pro případné minimální požadované stáří. Další problém, který se má vyřešit, je ověření, jestli jsou zásady autorizace dostupné pro všechny různé věkové služby. Zde je místo, kde je `IAuthorizationPolicyProvider` užitečné.

Při použití `MinimumAgeAuthorizationAttribute`se názvy zásad autorizace dostanou `"MinimumAge" + Age`vzor, takže vlastní `IAuthorizationPolicyProvider` vygeneruje zásady autorizace podle těchto pokynů:

* Analyzuje se věk z názvu zásady.
* Vytvoření nového `AuthorizationPolicy` pomocí `AuthorizationPolicyBuilder`
* V tomto a následujících příkladech se předpokládá, že uživatel je ověřený prostřednictvím souboru cookie. `AuthorizationPolicyBuilder` by měl být vytvořen buď s alespoň jedním názvem schématu autorizace, nebo vždy úspěšně. V opačném případě nejsou k dispozici žádné informace o tom, jak poskytnout uživateli výzvu a bude vyvolána výjimka.
* Přidávají se požadavky na zásady na základě stáří pomocí `AuthorizationPolicyBuilder.AddRequirements`. V jiných scénářích můžete místo toho použít `RequireClaim`, `RequireRole`nebo `RequireUserName`.

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

Při použití vlastních implementací `IAuthorizationPolicyProvider` mějte na paměti, že ASP.NET Core používá pouze jednu instanci `IAuthorizationPolicyProvider`. Pokud vlastní zprostředkovatel nedokáže poskytovat zásady autorizace pro všechny názvy zásad, které se použijí, měl by se odložit k poskytovateli zálohování. 

Představte si třeba aplikaci, která potřebuje vlastní věkové zásady a jednodušší načítání zásad na základě rolí. Taková aplikace by mohla použít vlastního zprostředkovatele autorizačních zásad, který:

* Pokusí se analyzovat názvy zásad. 
* Zavolá do jiného poskytovatele zásad (například `DefaultAuthorizationPolicyProvider`), pokud název zásady neobsahuje stáří.

Ukázková `IAuthorizationPolicyProvider` výše uvedená výše se dá aktualizovat tak, aby používala `DefaultAuthorizationPolicyProvider` vytvořením poskytovatele zásad zálohování ve svém konstruktoru (aby se použila v případě, že se název zásady neshoduje s očekávaným vzorem "minimálního počtu" a stáří).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Pak lze metodu `GetPolicyAsync` aktualizovat tak, aby používala `BackupPolicyProvider` namísto vrácení hodnoty null:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Výchozí zásady

Kromě poskytování pojmenovaných autorizačních zásad musí vlastní `IAuthorizationPolicyProvider` implementovat `GetDefaultPolicyAsync`, aby poskytoval zásady autorizace pro `[Authorize]` atributů bez zadaného názvu zásady.

V mnoha případech tento atribut autorizace vyžaduje jenom ověřeného uživatele, takže můžete vytvořit potřebné zásady s voláním `RequireAuthenticatedUser`:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Stejně jako u všech aspektů vlastního `IAuthorizationPolicyProvider`můžete to podle potřeby přizpůsobit. V některých případech může být žádoucí načíst výchozí zásadu z záložní `IAuthorizationPolicyProvider`.

## <a name="fallback-policy"></a>Záložní zásady

Vlastní `IAuthorizationPolicyProvider` může volitelně implementovat `GetFallbackPolicyAsync` a poskytnout tak zásadu, která se používá při [kombinování zásad](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) a kdy nejsou zadané žádné zásady. Pokud `GetFallbackPolicyAsync` vrátí zásadu, která nemá hodnotu null, je vrácená zásada používána middlewarem autorizace, když nejsou pro požadavek zadány žádné zásady.

Pokud se nevyžadují žádné záložní zásady, může zprostředkovatel vrátit `null` nebo se odložit k záložnímu zprostředkovateli:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Použití vlastního IAuthorizationPolicyProvider

Pokud chcete použít vlastní zásady z `IAuthorizationPolicyProvider`, musíte:

* Zaregistrujte odpovídající typy `AuthorizationHandler` se vkládáním závislostí (popsané v tématu [autorizace na základě zásad](xref:security/authorization/policies#authorization-handlers)), stejně jako u všech scénářů autorizace na základě zásad.
* Zaregistrujte vlastní typ `IAuthorizationPolicyProvider` v kolekci služby pro vkládání závislostí aplikace (v `Startup.ConfigureServices`), aby se výchozí zprostředkovatel zásad nahradil.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Kompletní ukázka vlastní `IAuthorizationPolicyProvider` je k dispozici v [úložišti GitHub ASPNET/AuthSamples](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).

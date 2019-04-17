---
title: Poskytovatelé zásad autorizace v ASP.NET Core
author: mjrousos
description: Další informace o použití vlastních IAuthorizationPolicyProvider v aplikaci ASP.NET Core pro dynamické generování zásad autorizace.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: e17372bb0ec9091c385a70b1e907eaa3cff24003
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614406"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Vlastní zprostředkovatelé zásad autorizace pomocí IAuthorizationPolicyProvider v ASP.NET Core 

Podle [Mike Rousos](https://github.com/mjrousos)

Obvykle při použití [autorizace na základě zásad](xref:security/authorization/policies), zásady jsou registrované pomocí volání `AuthorizationOptions.AddPolicy` jako součást konfigurace služby ověření. V některých případech nemusí být možné (nebo žádoucí) k registraci všech zásad autorizace tímto způsobem. V takových případech můžete použít vlastní `IAuthorizationPolicyProvider` řídit, jak jsou dodávány zásad autorizace.

Uvedené příklady situací, kdy vlastní [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) může být užitečná, patří:

* K poskytování hodnocení zásad pomocí externí služby.
* Pomocí velkého rozsahu zásad (pro jiné místnosti čísla nebo ve věku, například), proto nemá smysl pro přidání jednotlivých zásad jednotlivé autorizace pomocí `AuthorizationOptions.AddPolicy` volání.
* Vytváření zásad za běhu na základě informací v externího zdroje dat (třeba databáze) nebo určení požadavků na povolení dynamicky prostřednictvím jiného mechanismu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) z [úložiště AspNetCore GitHub](https://github.com/aspnet/AspNetCore). Stáhněte soubor ZIP aspnet/AspNetCore úložiště. Rozbalte tento soubor. Přejděte *src/Security/samples/CustomPolicyProvider* složky projektu.

## <a name="customize-policy-retrieval"></a>Přizpůsobení načtení zásad

Aplikace ASP.NET Core pomocí implementace `IAuthorizationPolicyProvider` rozhraní k načtení zásad autorizace. Ve výchozím nastavení [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) je zaregistrované a použít. `DefaultAuthorizationPolicyProvider` Vrátí zásad `AuthorizationOptions` součástí `IServiceCollection.AddAuthorization` volání.

Toto chování můžete přizpůsobit tak, že zaregistrujete jiný `IAuthorizationPolicyProvider` implementace aplikace [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru. 

`IAuthorizationPolicyProvider` Rozhraní obsahuje dvě rozhraní API:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) vrátí zásad autorizace pro daný název.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) vrátí výchozí zásady autorizace (zásady používané pro `[Authorize]` atributy bez zásad zadaný). 

Implementací těchto dvou rozhraní API můžete přizpůsobit, jak jsou k dispozici zásad autorizace.

## <a name="parameterized-authorize-attribute-example"></a>Parametrizované autorizovat příkladu atribut

Jeden scénář kde `IAuthorizationPolicyProvider` je užitečné, je povolení vlastní `[Authorize]` atributy, jehož požadavky na závisí na parametru. Například v [autorizace na základě zásad](xref:security/authorization/policies) dokumentaci, založené na stáří ("AtLeast21") zásady byl použit jako ukázku. Pokud jiný kontroler akce v aplikaci by měla být k dispozici uživatelům *různých* ve věku, může být užitečné používat mnoho různých zásad na základě věku. Místo registrace všechny různé věkové zásady, které aplikace se musí v `AuthorizationOptions`, můžete vygenerovat zásad dynamicky pomocí vlastní `IAuthorizationPolicyProvider`. Chcete-li pomocí zásad jednodušší, může opatřit poznámkami akce s atributem vlastní autorizace jako `[MinimumAgeAuthorize(20)]`.

## <a name="custom-authorization-attributes"></a>Vlastní atributy autorizace

Zásady autorizace se identifikují podle svých názvů. Vlastní `MinimumAgeAuthorizeAttribute` popsané dříve je potřeba namapovat argumenty do řetězce, který slouží k načtení odpovídajících zásad autorizace. Uděláte to tak odvozený od `AuthorizeAttribute` a provádění `Age` wrap vlastnost `AuthorizeAttribute.Policy` vlastnost.

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

Tento typ atribut má `Policy` řetězce na základě pevně zakódované předpony (`"MinimumAge"`) a celé číslo předané prostřednictvím konstruktoru.

Můžete provést u akce stejným způsobem jako ostatní `Authorize` atributy s tím rozdílem, že přijímá jako parametr celé číslo.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Vlastní IAuthorizationPolicyProvider

Vlastní `MinimumAgeAuthorizeAttribute` usnadňuje žádosti o zásady autorizace pro žádné minimální stáří požadovaného. Další problém vyřešit, je zajistit, že zásady ověřování jsou k dispozici pro všechny tyto různé věkové kategorie. Tady `IAuthorizationPolicyProvider` je užitečné.

Při použití `MinimumAgeAuthorizationAttribute`, názvy zásad autorizace se řídí vzorem `"MinimumAge" + Age`, takže vlastní `IAuthorizationPolicyProvider` by měl generovat zásad autorizace podle:

* Analýza věk z název zásady.
* Pomocí `AuthorizationPolicyBuilder` vytvořit nový `AuthorizationPolicy`
* Přidávání požadavků zásad založené na věk pomocí `AuthorizationPolicyBuilder.AddRequirements`. V jiných scénářích může používat `RequireClaim`, `RequireRole`, nebo `RequireUserName` místo.

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
            var policy = new AuthorizationPolicyBuilder();
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>Více poskytovatelů zásad autorizace

Při použití vlastní `IAuthorizationPolicyProvider` implementace, mějte na paměti, který ASP.NET Core využívá jenom jednu instanci `IAuthorizationPolicyProvider`. Pokud vlastního zprostředkovatele není schopna poskytnout zásady autorizace pro všechny názvy zásad, které se použijí, ji by měl vrátit zpět k poskytovatelem zálohování. 

Zvažte například aplikaci, která vyžaduje zásady vlastní stáří a tradičnější načtení zásad na základě rolí. Takové aplikace může používat poskytovatele zásad autorizace, které:

* Pokusí se analyzovat názvy zásad. 
* Volání do jiné zásady zprostředkovatele (jako je `DefaultAuthorizationPolicyProvider`), pokud neobsahuje název zásady stáří.

V příkladu `IAuthorizationPolicyProvider` implementace uvedené výše můžete aktualizovat na používání `DefaultAuthorizationPolicyProvider` tak, že vytvoříte záložní zásad poskytovatele 've svém konstruktoru (který se má použít v případě, že název zásad neodpovídá očekávanému způsobu "MinimumAge" + vek).

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Pak, bude `GetPolicyAsync` metodu je možné aktualizovat používat `FallbackPolicyProvider` místo hodnoty null:

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Výchozí zásady

Kromě toho, že zásady s názvem autorizace, vlastní `IAuthorizationPolicyProvider` musí implementovat `GetDefaultPolicyAsync` poskytnout zásady autorizace pro `[Authorize]` atributy bez zadaný název zásad.

V mnoha případech se tento atribut autorizace vyžaduje pouze ověřeného uživatele, abyste měli nezbytné zásad voláním `RequireAuthenticatedUser`:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build());
```

Stejně jako u všech aspektů vlastní `IAuthorizationPolicyProvider`, si ho můžete přizpůsobit, podle potřeby. V některých případech může být žádoucí se načíst zásady ze záložní `IAuthorizationPolicyProvider`.

## <a name="required-policy"></a>Požadované zásady

Vlastní `IAuthorizationPolicyProvider` musí implementovat `GetRequiredPolicyAsync` na, volitelně zadejte zásadu, která je vždy vyžadován. Pokud `GetRequiredPolicyAsync` vrátí nenulovou zásadu, tyto zásady se kombinovat v jakémkoli jiném (s názvem, nebo výchozí hodnoty) zásady, které jsou požadovány.

V případě potřeby žádné požadované zásady poskytovateli pouze vrátí hodnotu null nebo odložit pro použití náhradní lokality zprostředkovatele:

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Použít vlastní IAuthorizationPolicyProvider

Použití vlastních zásad ze `IAuthorizationPolicyProvider`, musíte:

* Zaregistrujte příslušný `AuthorizationHandler` typy s injektáž závislostí (popsané v [autorizace na základě zásad](xref:security/authorization/policies#authorization-handlers)), stejně jako u všech scénářích ověřování na základě zásad.
* Zaregistrovat vlastní `IAuthorizationPolicyProvider` typ v kolekci služby injektáž závislostí aplikace (v `Startup.ConfigureServices`) Chcete-li nahradit výchozí poskytovatel zásad.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Kompletní vlastní `IAuthorizationPolicyProvider` ukázka je k dispozici v [úložiště GitHub aspnet/AuthSamples](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).

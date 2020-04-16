---
title: Vlastní poskytovatelé autorizačních zásad v ASP.NET jádru
author: mjrousos
description: Naučte se používat vlastní IAuthorizationPolicyProvider v aplikaci ASP.NET Core dynamicky generovat zásady autorizace.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2a8b189cc9f17529a962a1f9642c7bb199d5781b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440919"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Vlastní zprostředkovatelé autorizačních zásad používající iAuthorizationPolicyProvider v ASP.NET jádru 

Podle [Mike Rousos](https://github.com/mjrousos)

Obvykle při použití [autorizace založené na zásadách](xref:security/authorization/policies)jsou zásady registrovány voláním `AuthorizationOptions.AddPolicy` jako součást konfigurace autorizační služby. V některých případech nemusí být možné (nebo žádoucí) zaregistrovat všechny zásady autorizace tímto způsobem. V těchto případech můžete použít `IAuthorizationPolicyProvider` vlastní řídit, jak jsou poskytovány zásady autorizace.

Příklady scénářů, kde může být užitečný vlastní [IAuthorizationPolicyProvider:](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)

* Použití externí služby k zajištění vyhodnocení zásad.
* Použití široké škály zásad (například pro různá čísla nebo věk) proto nemá smysl přidávat `AuthorizationOptions.AddPolicy` jednotlivé zásady autorizace pomocí hovoru.
* Vytváření zásad za běhu na základě informací v externím zdroji dat (například databáze) nebo dynamického určení požadavků na autorizaci prostřednictvím jiného mechanismu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) z [úložiště AspNetCore GitHub](https://github.com/dotnet/AspNetCore). Stáhněte si soubor ZIP úložiště dotnet/AspNetCore. Rozbalte soubor. Přejděte do složky projektu *src/Security/samples/CustomPolicyProvider.*

## <a name="customize-policy-retrieval"></a>Přizpůsobení načítání zásad

ASP.NET Základní aplikace používají `IAuthorizationPolicyProvider` implementaci rozhraní k načtení zásad autorizace. Ve výchozím nastavení je [StandardAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registrován a používán. `DefaultAuthorizationPolicyProvider`vrátí zásady `AuthorizationOptions` z uvedených ve `IServiceCollection.AddAuthorization` výzvě.

Přizpůsobte toto chování `IAuthorizationPolicyProvider` registrací jiné implementace v kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace. 

Rozhraní `IAuthorizationPolicyProvider` obsahuje tři rozhraní API:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) vrátí zásady autorizace pro daný název.
* [Funkce GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) vrátí výchozí zásadu `[Authorize]` autorizace (zásadu používanou pro atributy bez zadané zásady). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) vrátí záložní zásady autorizace (zásady používané autorizace Middleware, pokud není zadána žádná zásada). 

Implementací těchto api můžete přizpůsobit způsob, jakým jsou k dispozici zásady autorizace.

## <a name="parameterized-authorize-attribute-example"></a>Parametrizovaný příklad atributu authorize

Jeden scénář, kde `IAuthorizationPolicyProvider` je `[Authorize]` užitečné povolení vlastní atributy, jejichž požadavky závisí na parametr. Například v dokumentaci [autorizace založené na zásadách](xref:security/authorization/policies) byla jako ukázka použita zásada založená na věku ("AtLeast21"). Pokud by měly být uživatelům *různého* věku k dispozici různé akce kontroloru, může být užitečné mít mnoho různých zásad založených na věku. Namísto registrace všech různých zásad založených na věku, `AuthorizationOptions`které bude aplikace potřebovat v `IAuthorizationPolicyProvider`aplikaci , můžete zásady generovat dynamicky pomocí vlastního . Chcete-li usnadnit používání zásad, můžete akce oznamovat vlastními atributy autorizace, jako je `[MinimumAgeAuthorize(20)]`.

## <a name="custom-authorization-attributes"></a>Atributy vlastní autorizace

Zásady autorizace jsou označeny jejich názvy. Vlastní `MinimumAgeAuthorizeAttribute` popsané dříve potřebuje mapovat argumenty do řetězce, který lze použít k načtení odpovídající zásady autorizace. Můžete to udělat tak, že `AuthorizeAttribute` odvodíte a přimějete `Age` vlastnost zabalit `AuthorizeAttribute.Policy` vlastnost.

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

Tento typ atributu `Policy` má řetězec založený na pevně`"MinimumAge"`zakódované předponě ( ) a celé číslo předané prostřednictvím konstruktoru.

Můžete ji použít na akce stejným `Authorize` způsobem jako ostatní atributy s tím rozdílem, že trvá celé číslo jako parametr.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Vlastní iAuthorizationPolicyProvider

Vlastní `MinimumAgeAuthorizeAttribute` usnadňuje požádat o autorizaci zásady pro minimální požadovaný věk. Dalším problémem, který je třeba vyřešit, je zajistit, aby byly k dispozici zásady autorizace pro všechny tyto různé věkové kategorie. To je `IAuthorizationPolicyProvider` místo, kde je užitečné.

Při `MinimumAgeAuthorizationAttribute`použití budou názvy zásad `"MinimumAge" + Age`autorizace následovat `IAuthorizationPolicyProvider` podle vzoru , takže vlastní by měl generovat zásady autorizace:

* Analýza stáří z názvu zásady.
* Použití `AuthorizationPolicyBuilder` k vytvoření nového`AuthorizationPolicy`
* V tomto a následujících příkladech se předpokládá, že uživatel je ověřen prostřednictvím souboru cookie. By `AuthorizationPolicyBuilder` měl být buď vytvořen s alespoň jeden název autorizačního schématu nebo vždy úspěšné. V opačném případě nejsou k dispozici žádné informace o tom, jak poskytnout výzvu uživateli a bude vyvolána výjimka.
* Přidání požadavků na zásady na `AuthorizationPolicyBuilder.AddRequirements`základě věku s . V jiných scénářích `RequireClaim`můžete `RequireRole`použít `RequireUserName` , nebo místo.

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

## <a name="multiple-authorization-policy-providers"></a>Více poskytovatelů autorizačních zásad

Při použití `IAuthorizationPolicyProvider` vlastníimplementace, mějte na paměti, že `IAuthorizationPolicyProvider`ASP.NET Core používá pouze jednu instanci . Pokud vlastní zprostředkovatel není schopen poskytnout zásady autorizace pro všechny názvy zásad, které budou použity, by měl odložit na zprostředkovatele zálohování. 

Zvažte například aplikaci, která potřebuje vlastní zásady věku a tradiční načítání zásad založených na rolích. Taková aplikace může používat vlastního poskytovatele autorizačních zásad, který:

* Pokusí se analyzovat názvy zásad. 
* Volá do jiného poskytovatele `DefaultAuthorizationPolicyProvider`zásad (například), pokud název zásady neobsahuje věk.

Příklad `IAuthorizationPolicyProvider` implementace uvedené výše lze aktualizovat `DefaultAuthorizationPolicyProvider` použít vytvořením zprostředkovatele zásad zálohování v jeho konstruktoru (který se použije v případě, že název zásady neodpovídá jeho očekávaný vzor "MinimumAge" + stáří).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Potom `GetPolicyAsync` metoda může být aktualizován `BackupPolicyProvider` a použít místo vrácení null:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Výchozí zásada

Kromě poskytování pojmenovaných zásad autorizace je `GetDefaultPolicyAsync` třeba provést vlastní `[Authorize]` `IAuthorizationPolicyProvider` implementaci, aby bylo zajištěno zásady autorizace pro atributy bez zadaného názvu zásady.

V mnoha případech tento atribut autorizace vyžaduje pouze ověřeného uživatele, takže `RequireAuthenticatedUser`můžete provést nezbytné zásady s voláním na :

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Stejně jako u všech `IAuthorizationPolicyProvider`aspektů vlastní , můžete přizpůsobit to, podle potřeby. V některých případech může být žádoucí načíst výchozí `IAuthorizationPolicyProvider`zásady z záložní .

## <a name="fallback-policy"></a>Záložní zásady

Vlastní `IAuthorizationPolicyProvider` můžete volitelně `GetFallbackPolicyAsync` implementovat poskytnout zásady, které se používají při [kombinování zásad](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) a když nejsou zadány žádné zásady. Pokud `GetFallbackPolicyAsync` vrátí zásadu bez null, vrácené zásady se používá autorizace Middleware, pokud nejsou určeny žádné zásady pro požadavek.

Pokud není vyžadována žádná záložní zásada, zprostředkovatel může vrátit `null` nebo odložit záložnízprostředkovatel:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Použití vlastního zprostředkovatele IAuthorizationPolicyProvider

Chcete-li používat `IAuthorizationPolicyProvider`vlastní zásady z aplikace , musíte:

* Zaregistrujte `AuthorizationHandler` příslušné typy s vkládání závislostí (popsané v [autorizaci založené na zásadách](xref:security/authorization/policies#authorization-handlers)), stejně jako u všech scénářů autorizace založených na zásadách.
* Zaregistrujte `IAuthorizationPolicyProvider` vlastní typ v kolekci služeb vkládání `Startup.ConfigureServices`závislostí aplikace (v ) a nahraďte výchozího zprostředkovatele zásad.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Kompletní vlastní `IAuthorizationPolicyProvider` ukázka je k dispozici v [úložišti GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).

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
ms.openlocfilehash: c104de2903867b3257e5bde5b55dfcb256499746
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755791"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="8c6d8-103">Vlastní zprostředkovatelé zásad autorizace využívající IAuthorizationPolicyProvider v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c6d8-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="8c6d8-104">O [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="8c6d8-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="8c6d8-105">Při použití [autorizace založené na zásadách](xref:security/authorization/policies)se obvykle zaregistrují zásady voláním `AuthorizationOptions.AddPolicy` jako součást konfigurace autorizační služby.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="8c6d8-106">V některých scénářích to nemusí být možné (nebo vhodné) k registraci všech zásad autorizace tímto způsobem.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="8c6d8-107">V těchto případech můžete k řízení způsobu, jakým jsou dodány zásady autorizace, [použít vlastní `IAuthorizationPolicyProvider` ](#ci) .</span><span class="sxs-lookup"><span data-stu-id="8c6d8-107">In those cases, you can [use a custom `IAuthorizationPolicyProvider`](#ci) to control how authorization policies are supplied.</span></span>

<span data-ttu-id="8c6d8-108">Příklady scénářů, které mohou být užitečné pro vlastní [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) , zahrnují:</span><span class="sxs-lookup"><span data-stu-id="8c6d8-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="8c6d8-109">Použití externí služby k zajištění vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="8c6d8-110">Použití velkého rozsahu zásad (například pro různá čísla místností nebo stáří), takže nedává smysl přidat jednotlivé zásady autorizace `AuthorizationOptions.AddPolicy` volání.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="8c6d8-111">Vytváření zásad za běhu založené na informacích v externím zdroji dat (jako je databáze) nebo k dynamickému určování požadavků autorizace prostřednictvím jiného mechanismu.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="8c6d8-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) z [úložiště GitHub AspNetCore](https://github.com/dotnet/AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="8c6d8-112">[View or download sample code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="8c6d8-113">Stáhněte si soubor ZIP pro úložiště dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="8c6d8-114">Rozbalte soubor.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-114">Unzip the file.</span></span> <span data-ttu-id="8c6d8-115">Přejděte do složky projektu *Src/Security/Samples/CustomPolicyProvider* .</span><span class="sxs-lookup"><span data-stu-id="8c6d8-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="8c6d8-116">Přizpůsobení načtení zásad</span><span class="sxs-lookup"><span data-stu-id="8c6d8-116">Customize policy retrieval</span></span>

<span data-ttu-id="8c6d8-117">Aplikace ASP.NET Core používají implementaci `IAuthorizationPolicyProvider` rozhraní k načtení zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="8c6d8-118">Ve výchozím nastavení je [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) zaregistrován a používán.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="8c6d8-119">`DefaultAuthorizationPolicyProvider`Vrátí zásady ze `AuthorizationOptions` zadaného `IServiceCollection.AddAuthorization` volání.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="8c6d8-120">Toto chování si přizpůsobte tak, že zaregistrujete jinou `IAuthorizationPolicyProvider` implementaci do kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="8c6d8-121">`IAuthorizationPolicyProvider`Rozhraní obsahuje tři rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="8c6d8-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="8c6d8-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) vrací zásadu autorizace pro daný název.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="8c6d8-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) vrátí výchozí zásady autorizace (zásady použité pro `[Authorize]` atributy bez zadaných zásad).</span><span class="sxs-lookup"><span data-stu-id="8c6d8-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="8c6d8-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) vrátí záložní zásadu autorizace (zásady používané middlewarem autorizace, když není zadaná žádná zásada).</span><span class="sxs-lookup"><span data-stu-id="8c6d8-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="8c6d8-125">Implementací těchto rozhraní API můžete přizpůsobit způsob poskytování zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="8c6d8-126">Příklad parametrizovaného autorizačního atributu</span><span class="sxs-lookup"><span data-stu-id="8c6d8-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="8c6d8-127">Jeden scénář, kde `IAuthorizationPolicyProvider` je užitečný, je povolit vlastní `[Authorize]` atributy, jejichž požadavky závisí na parametru.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="8c6d8-128">Například v dokumentaci pro [autorizaci na základě zásad](xref:security/authorization/policies) se jako ukázka použila zásada na základě stáří ("AtLeast21").</span><span class="sxs-lookup"><span data-stu-id="8c6d8-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="8c6d8-129">Pokud by měly být k dispozici různé akce kontroleru v aplikaci pro uživatele *různého* stáří, může být užitečné mít mnoho různých zásad založených na věku.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="8c6d8-130">Místo registrace všech různých zásad založených na věku, v nichž bude aplikace potřebovat `AuthorizationOptions` , můžete zásady vygenerovat dynamicky s vlastními `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="8c6d8-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="8c6d8-131">Aby bylo možné zásady používat snadněji, můžete poznámky provádět pomocí vlastního autorizačního atributu, jako je `[MinimumAgeAuthorize(20)]` .</span><span class="sxs-lookup"><span data-stu-id="8c6d8-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="8c6d8-132">Vlastní autorizační atributy</span><span class="sxs-lookup"><span data-stu-id="8c6d8-132">Custom Authorization attributes</span></span>

<span data-ttu-id="8c6d8-133">Zásady autorizace se identifikují podle jejich názvů.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="8c6d8-134">Vlastní `MinimumAgeAuthorizeAttribute` popsaná dříve musí mapovat argumenty na řetězec, který lze použít k načtení odpovídajících zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="8c6d8-135">Můžete to provést odvozením z a nastavením `AuthorizeAttribute` `Age` vlastnosti pro zabalení `AuthorizeAttribute.Policy` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="8c6d8-136">Tento typ atributu má `Policy` řetězec na základě pevně kódované předpony ( `"MinimumAge"` ) a celé číslo předané prostřednictvím konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="8c6d8-137">Můžete ji použít na akce stejným způsobem jako ostatní `Authorize` atributy s tím rozdílem, že jako parametr přebírá celé číslo.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="8c6d8-138">Vlastní IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="8c6d8-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="8c6d8-139">Vlastníci usnadňují `MinimumAgeAuthorizeAttribute` vyžádání autorizačních zásad pro případné minimální požadované stáří.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="8c6d8-140">Další problém, který se má vyřešit, je ověření, jestli jsou zásady autorizace dostupné pro všechny různé věkové služby.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="8c6d8-141">To je místo, kde `IAuthorizationPolicyProvider` je to užitečné.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="8c6d8-142">Při použití se `MinimumAgeAuthorizationAttribute` názvy zásad autorizace řídí vzorem `"MinimumAge" + Age` , takže vlastníci `IAuthorizationPolicyProvider` by měli vytvořit zásady autorizace podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="8c6d8-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="8c6d8-143">Analyzuje se věk z názvu zásady.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="8c6d8-144">Použití `AuthorizationPolicyBuilder` k vytvoření nového`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="8c6d8-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="8c6d8-145">V tomto a následujících příkladech se předpokládá, že uživatel je ověřený prostřednictvím souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="8c6d8-146">`AuthorizationPolicyBuilder`Měl by být vytvořen buď pomocí alespoň jednoho názvu schématu autorizace, nebo vždy úspěšně.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="8c6d8-147">V opačném případě nejsou k dispozici žádné informace o tom, jak poskytnout uživateli výzvu a bude vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="8c6d8-148">Přidávají se požadavky na zásady na základě stáří pomocí `AuthorizationPolicyBuilder.AddRequirements` .</span><span class="sxs-lookup"><span data-stu-id="8c6d8-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="8c6d8-149">V jiných scénářích můžete použít `RequireClaim` `RequireRole` místo toho, nebo `RequireUserName` .</span><span class="sxs-lookup"><span data-stu-id="8c6d8-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="8c6d8-150">Několik poskytovatelů zásad autorizace</span><span class="sxs-lookup"><span data-stu-id="8c6d8-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="8c6d8-151">Při použití vlastních `IAuthorizationPolicyProvider` implementací Pamatujte na to, že ASP.NET Core používá pouze jednu instanci `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="8c6d8-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="8c6d8-152">Pokud vlastní zprostředkovatel nedokáže poskytovat zásady autorizace pro všechny názvy zásad, které se použijí, měl by se odložit k poskytovateli zálohování.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="8c6d8-153">Představte si třeba aplikaci, která potřebuje vlastní věkové zásady a jednodušší načítání zásad na základě rolí.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="8c6d8-154">Taková aplikace by mohla použít vlastního zprostředkovatele autorizačních zásad, který:</span><span class="sxs-lookup"><span data-stu-id="8c6d8-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="8c6d8-155">Pokusí se analyzovat názvy zásad.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="8c6d8-156">Zavolá do jiného poskytovatele zásad (například `DefaultAuthorizationPolicyProvider` ), pokud název zásady neobsahuje stáří.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="8c6d8-157">Ukázkovou `IAuthorizationPolicyProvider` implementaci uvedenou výše je možné aktualizovat tak, aby používala `DefaultAuthorizationPolicyProvider` Vytvoření poskytovatele zásad zálohování ve svém konstruktoru (aby se použilo pro případ, že se název zásady neshoduje s očekávaným vzorem "minimálního" + stáří).</span><span class="sxs-lookup"><span data-stu-id="8c6d8-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="8c6d8-158">Pak `GetPolicyAsync` lze metodu aktualizovat tak, aby používala `BackupPolicyProvider` místo vrácení hodnoty null:</span><span class="sxs-lookup"><span data-stu-id="8c6d8-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="8c6d8-159">Výchozí zásady</span><span class="sxs-lookup"><span data-stu-id="8c6d8-159">Default policy</span></span>

<span data-ttu-id="8c6d8-160">Kromě poskytování pojmenovaných autorizačních zásad je `IAuthorizationPolicyProvider` nutné implementovat vlastní `GetDefaultPolicyAsync` zásady autorizace pro `[Authorize]` atributy bez zadaného názvu zásady.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="8c6d8-161">V mnoha případech tento atribut autorizace vyžaduje jenom ověřeného uživatele, takže můžete vytvořit potřebné zásady s voláním `RequireAuthenticatedUser` :</span><span class="sxs-lookup"><span data-stu-id="8c6d8-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="8c6d8-162">Stejně jako u všech aspektů vlastní `IAuthorizationPolicyProvider` můžete to podle potřeby přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="8c6d8-163">V některých případech může být žádoucí načíst výchozí zásadu z nouzového režimu `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="8c6d8-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="8c6d8-164">Záložní zásady</span><span class="sxs-lookup"><span data-stu-id="8c6d8-164">Fallback policy</span></span>

<span data-ttu-id="8c6d8-165">Vlastní `IAuthorizationPolicyProvider` nastavení může volitelně implementovat `GetFallbackPolicyAsync` zásadu, která se používá při [kombinování zásad](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) a kdy nejsou zadané žádné zásady.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="8c6d8-166">Pokud `GetFallbackPolicyAsync` vrátí zásadu, která nemá hodnotu null, je vrácená zásada používána middlewarem autorizace, pokud nejsou pro požadavek zadány žádné zásady.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="8c6d8-167">Pokud se nevyžadují žádné záložní zásady, může zprostředkovatel vrátit `null` nebo odložit záložnímu zprostředkovateli:</span><span class="sxs-lookup"><span data-stu-id="8c6d8-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="8c6d8-168">Použití vlastního IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="8c6d8-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="8c6d8-169">Pokud chcete použít vlastní zásady z `IAuthorizationPolicyProvider` , ***musíte***:</span><span class="sxs-lookup"><span data-stu-id="8c6d8-169">To use custom policies from an `IAuthorizationPolicyProvider`, you ***must***:</span></span>

* <span data-ttu-id="8c6d8-170">Zaregistrujte příslušné `AuthorizationHandler` typy se vkládáním závislostí (popsané v tématu [autorizace na základě zásad](xref:security/authorization/policies#authorization-handlers)), stejně jako u všech scénářů autorizace na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="8c6d8-171">Zaregistrujte vlastní `IAuthorizationPolicyProvider` typ v kolekci služby pro vkládání závislostí aplikace v nástroji `Startup.ConfigureServices` a nahraďte výchozího poskytovatele zásad.</span><span class="sxs-lookup"><span data-stu-id="8c6d8-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection in `Startup.ConfigureServices` to replace the default policy provider.</span></span>

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

<span data-ttu-id="8c6d8-172">Kompletní vlastní `IAuthorizationPolicyProvider` Ukázka je k dispozici v [úložišti GitHub/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="8c6d8-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span></span>

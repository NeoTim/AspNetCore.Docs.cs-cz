---
title: Poskytovatelé zásad autorizace v ASP.NET Core
author: mjrousos
description: Další informace o použití vlastních IAuthorizationPolicyProvider v aplikaci ASP.NET Core pro dynamické generování zásad autorizace.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: e17372bb0ec9091c385a70b1e907eaa3cff24003
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902502"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="e5eb2-103">Vlastní zprostředkovatelé zásad autorizace pomocí IAuthorizationPolicyProvider v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e5eb2-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="e5eb2-104">Podle [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="e5eb2-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="e5eb2-105">Obvykle při použití [autorizace na základě zásad](xref:security/authorization/policies), zásady jsou registrované pomocí volání `AuthorizationOptions.AddPolicy` jako součást konfigurace služby ověření.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="e5eb2-106">V některých případech nemusí být možné (nebo žádoucí) k registraci všech zásad autorizace tímto způsobem.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="e5eb2-107">V takových případech můžete použít vlastní `IAuthorizationPolicyProvider` řídit, jak jsou dodávány zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="e5eb2-108">Uvedené příklady situací, kdy vlastní [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) může být užitečná, patří:</span><span class="sxs-lookup"><span data-stu-id="e5eb2-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="e5eb2-109">K poskytování hodnocení zásad pomocí externí služby.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="e5eb2-110">Pomocí velkého rozsahu zásad (pro jiné místnosti čísla nebo ve věku, například), proto nemá smysl pro přidání jednotlivých zásad jednotlivé autorizace pomocí `AuthorizationOptions.AddPolicy` volání.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn’t make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="e5eb2-111">Vytváření zásad za běhu na základě informací v externího zdroje dat (třeba databáze) nebo určení požadavků na povolení dynamicky prostřednictvím jiného mechanismu.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="e5eb2-112">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) z [úložiště AspNetCore GitHub](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="e5eb2-112">[View or download sample code](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="e5eb2-113">Stáhněte soubor ZIP aspnet/AspNetCore úložiště.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-113">Download the aspnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="e5eb2-114">Rozbalte tento soubor.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-114">Unzip the file.</span></span> <span data-ttu-id="e5eb2-115">Přejděte *src/Security/samples/CustomPolicyProvider* složky projektu.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="e5eb2-116">Přizpůsobení načtení zásad</span><span class="sxs-lookup"><span data-stu-id="e5eb2-116">Customize policy retrieval</span></span>

<span data-ttu-id="e5eb2-117">Aplikace ASP.NET Core pomocí implementace `IAuthorizationPolicyProvider` rozhraní k načtení zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="e5eb2-118">Ve výchozím nastavení [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) je zaregistrované a použít.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="e5eb2-119">`DefaultAuthorizationPolicyProvider` Vrátí zásad `AuthorizationOptions` součástí `IServiceCollection.AddAuthorization` volání.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="e5eb2-120">Toto chování můžete přizpůsobit tak, že zaregistrujete jiný `IAuthorizationPolicyProvider` implementace aplikace [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-120">You can customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app’s [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="e5eb2-121">`IAuthorizationPolicyProvider` Rozhraní obsahuje dvě rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="e5eb2-121">The `IAuthorizationPolicyProvider` interface contains two APIs:</span></span>

* <span data-ttu-id="e5eb2-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) vrátí zásad autorizace pro daný název.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="e5eb2-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) vrátí výchozí zásady autorizace (zásady používané pro `[Authorize]` atributy bez zásad zadaný).</span><span class="sxs-lookup"><span data-stu-id="e5eb2-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 

<span data-ttu-id="e5eb2-124">Implementací těchto dvou rozhraní API můžete přizpůsobit, jak jsou k dispozici zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-124">By implementing these two APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="e5eb2-125">Parametrizované autorizovat příkladu atribut</span><span class="sxs-lookup"><span data-stu-id="e5eb2-125">Parameterized authorize attribute example</span></span>

<span data-ttu-id="e5eb2-126">Jeden scénář kde `IAuthorizationPolicyProvider` je užitečné, je povolení vlastní `[Authorize]` atributy, jehož požadavky na závisí na parametru.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-126">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="e5eb2-127">Například v [autorizace na základě zásad](xref:security/authorization/policies) dokumentaci, založené na stáří ("AtLeast21") zásady byl použit jako ukázku.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-127">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="e5eb2-128">Pokud jiný kontroler akce v aplikaci by měla být k dispozici uživatelům *různých* ve věku, může být užitečné používat mnoho různých zásad na základě věku.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-128">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="e5eb2-129">Místo registrace všechny různé věkové zásady, které aplikace se musí v `AuthorizationOptions`, můžete vygenerovat zásad dynamicky pomocí vlastní `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-129">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="e5eb2-130">Chcete-li pomocí zásad jednodušší, může opatřit poznámkami akce s atributem vlastní autorizace jako `[MinimumAgeAuthorize(20)]`.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-130">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="e5eb2-131">Vlastní atributy autorizace</span><span class="sxs-lookup"><span data-stu-id="e5eb2-131">Custom Authorization attributes</span></span>

<span data-ttu-id="e5eb2-132">Zásady autorizace se identifikují podle svých názvů.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-132">Authorization policies are identified by their names.</span></span> <span data-ttu-id="e5eb2-133">Vlastní `MinimumAgeAuthorizeAttribute` popsané dříve je potřeba namapovat argumenty do řetězce, který slouží k načtení odpovídajících zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-133">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="e5eb2-134">Uděláte to tak odvozený od `AuthorizeAttribute` a provádění `Age` wrap vlastnost `AuthorizeAttribute.Policy` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-134">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="e5eb2-135">Tento typ atribut má `Policy` řetězce na základě pevně zakódované předpony (`"MinimumAge"`) a celé číslo předané prostřednictvím konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-135">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="e5eb2-136">Můžete provést u akce stejným způsobem jako ostatní `Authorize` atributy s tím rozdílem, že přijímá jako parametr celé číslo.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-136">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="e5eb2-137">Vlastní IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="e5eb2-137">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="e5eb2-138">Vlastní `MinimumAgeAuthorizeAttribute` usnadňuje žádosti o zásady autorizace pro žádné minimální stáří požadovaného.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-138">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="e5eb2-139">Další problém vyřešit, je zajistit, že zásady ověřování jsou k dispozici pro všechny tyto různé věkové kategorie.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-139">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="e5eb2-140">Tady `IAuthorizationPolicyProvider` je užitečné.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-140">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="e5eb2-141">Při použití `MinimumAgeAuthorizationAttribute`, názvy zásad autorizace se řídí vzorem `"MinimumAge" + Age`, takže vlastní `IAuthorizationPolicyProvider` by měl generovat zásad autorizace podle:</span><span class="sxs-lookup"><span data-stu-id="e5eb2-141">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="e5eb2-142">Analýza věk z název zásady.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-142">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="e5eb2-143">Pomocí `AuthorizationPolicyBuilder` vytvořit nový `AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="e5eb2-143">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="e5eb2-144">Přidávání požadavků zásad založené na věk pomocí `AuthorizationPolicyBuilder.AddRequirements`.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-144">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="e5eb2-145">V jiných scénářích může používat `RequireClaim`, `RequireRole`, nebo `RequireUserName` místo.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-145">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="e5eb2-146">Více poskytovatelů zásad autorizace</span><span class="sxs-lookup"><span data-stu-id="e5eb2-146">Multiple authorization policy providers</span></span>

<span data-ttu-id="e5eb2-147">Při použití vlastní `IAuthorizationPolicyProvider` implementace, mějte na paměti, který ASP.NET Core využívá jenom jednu instanci `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-147">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="e5eb2-148">Pokud vlastního zprostředkovatele není schopna poskytnout zásady autorizace pro všechny názvy zásad, které se použijí, ji by měl vrátit zpět k poskytovatelem zálohování.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-148">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should fall back to a backup provider.</span></span> 

<span data-ttu-id="e5eb2-149">Zvažte například aplikaci, která vyžaduje zásady vlastní stáří a tradičnější načtení zásad na základě rolí.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-149">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="e5eb2-150">Takové aplikace může používat poskytovatele zásad autorizace, které:</span><span class="sxs-lookup"><span data-stu-id="e5eb2-150">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="e5eb2-151">Pokusí se analyzovat názvy zásad.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-151">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="e5eb2-152">Volání do jiné zásady zprostředkovatele (jako je `DefaultAuthorizationPolicyProvider`), pokud neobsahuje název zásady stáří.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-152">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="e5eb2-153">V příkladu `IAuthorizationPolicyProvider` implementace uvedené výše můžete aktualizovat na používání `DefaultAuthorizationPolicyProvider` tak, že vytvoříte záložní zásad poskytovatele 've svém konstruktoru (který se má použít v případě, že název zásad neodpovídá očekávanému způsobu "MinimumAge" + vek).</span><span class="sxs-lookup"><span data-stu-id="e5eb2-153">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a fallback policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="e5eb2-154">Pak, bude `GetPolicyAsync` metodu je možné aktualizovat používat `FallbackPolicyProvider` místo hodnoty null:</span><span class="sxs-lookup"><span data-stu-id="e5eb2-154">Then, the `GetPolicyAsync` method can be updated to use the `FallbackPolicyProvider` instead of returning null:</span></span>

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="e5eb2-155">Výchozí zásady</span><span class="sxs-lookup"><span data-stu-id="e5eb2-155">Default policy</span></span>

<span data-ttu-id="e5eb2-156">Kromě toho, že zásady s názvem autorizace, vlastní `IAuthorizationPolicyProvider` musí implementovat `GetDefaultPolicyAsync` poskytnout zásady autorizace pro `[Authorize]` atributy bez zadaný název zásad.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-156">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="e5eb2-157">V mnoha případech se tento atribut autorizace vyžaduje pouze ověřeného uživatele, abyste měli nezbytné zásad voláním `RequireAuthenticatedUser`:</span><span class="sxs-lookup"><span data-stu-id="e5eb2-157">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build());
```

<span data-ttu-id="e5eb2-158">Stejně jako u všech aspektů vlastní `IAuthorizationPolicyProvider`, si ho můžete přizpůsobit, podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-158">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="e5eb2-159">V některých případech může být žádoucí se načíst zásady ze záložní `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-159">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="required-policy"></a><span data-ttu-id="e5eb2-160">Požadované zásady</span><span class="sxs-lookup"><span data-stu-id="e5eb2-160">Required policy</span></span>

<span data-ttu-id="e5eb2-161">Vlastní `IAuthorizationPolicyProvider` musí implementovat `GetRequiredPolicyAsync` na, volitelně zadejte zásadu, která je vždy vyžadován.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-161">A custom `IAuthorizationPolicyProvider` needs to implement `GetRequiredPolicyAsync` to, optionally, provide a policy that is always required.</span></span> <span data-ttu-id="e5eb2-162">Pokud `GetRequiredPolicyAsync` vrátí nenulovou zásadu, tyto zásady se kombinovat v jakémkoli jiném (s názvem, nebo výchozí hodnoty) zásady, které jsou požadovány.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-162">If `GetRequiredPolicyAsync` returns a non-null policy, that policy will be combined with any other (named or default) policy that is requested.</span></span>

<span data-ttu-id="e5eb2-163">V případě potřeby žádné požadované zásady poskytovateli pouze vrátí hodnotu null nebo odložit pro použití náhradní lokality zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="e5eb2-163">If no required policy is needed, the provider can just return null or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="e5eb2-164">Použít vlastní IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="e5eb2-164">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="e5eb2-165">Použití vlastních zásad ze `IAuthorizationPolicyProvider`, musíte:</span><span class="sxs-lookup"><span data-stu-id="e5eb2-165">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="e5eb2-166">Zaregistrujte příslušný `AuthorizationHandler` typy s injektáž závislostí (popsané v [autorizace na základě zásad](xref:security/authorization/policies#authorization-handlers)), stejně jako u všech scénářích ověřování na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-166">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="e5eb2-167">Zaregistrovat vlastní `IAuthorizationPolicyProvider` typ v kolekci služby injektáž závislostí aplikace (v `Startup.ConfigureServices`) Chcete-li nahradit výchozí poskytovatel zásad.</span><span class="sxs-lookup"><span data-stu-id="e5eb2-167">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="e5eb2-168">Kompletní vlastní `IAuthorizationPolicyProvider` ukázka je k dispozici v [úložiště GitHub aspnet/AuthSamples](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="e5eb2-168">A complete custom `IAuthorizationPolicyProvider` sample is available in the [aspnet/AuthSamples GitHub repository](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span></span>

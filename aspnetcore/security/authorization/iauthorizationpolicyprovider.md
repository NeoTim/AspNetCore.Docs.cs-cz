---
title: Vlastní zprostředkovatelé autorizačních zásad v ASP.NET Core
author: mjrousos
description: Naučte se používat vlastní IAuthorizationPolicyProvider v aplikaci ASP.NET Core k dynamickému generování zásad autorizace.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 9f0a0cd5337f7f8d2fc8a4b6902a63b98f6bd702
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659129"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="da5b6-103">Vlastní zprostředkovatelé zásad autorizace využívající IAuthorizationPolicyProvider v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da5b6-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="da5b6-104">O [Jan Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="da5b6-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="da5b6-105">Při použití [ověřování na základě zásad](xref:security/authorization/policies)jsou zásady zaregistrované voláním `AuthorizationOptions.AddPolicy` jako součást konfigurace autorizační služby.</span><span class="sxs-lookup"><span data-stu-id="da5b6-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="da5b6-106">V některých scénářích to nemusí být možné (nebo vhodné) k registraci všech zásad autorizace tímto způsobem.</span><span class="sxs-lookup"><span data-stu-id="da5b6-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="da5b6-107">V těchto případech můžete k řízení způsobu, jakým jsou dodány zásady autorizace, použít vlastní `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="da5b6-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="da5b6-108">Příklady scénářů, které mohou být užitečné pro vlastní [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) , zahrnují:</span><span class="sxs-lookup"><span data-stu-id="da5b6-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="da5b6-109">Použití externí služby k zajištění vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="da5b6-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="da5b6-110">Použití velkého rozsahu zásad (například pro různá čísla místností nebo stáří), takže nemá smysl přidat jednotlivé zásady autorizace pomocí `AuthorizationOptions.AddPolicy` volání.</span><span class="sxs-lookup"><span data-stu-id="da5b6-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="da5b6-111">Vytváření zásad za běhu založené na informacích v externím zdroji dat (jako je databáze) nebo k dynamickému určování požadavků autorizace prostřednictvím jiného mechanismu.</span><span class="sxs-lookup"><span data-stu-id="da5b6-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="da5b6-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) z [úložiště GitHub AspNetCore](https://github.com/dotnet/AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="da5b6-112">[View or download sample code](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="da5b6-113">Stáhněte si soubor ZIP pro úložiště dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="da5b6-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="da5b6-114">Rozbalte soubor.</span><span class="sxs-lookup"><span data-stu-id="da5b6-114">Unzip the file.</span></span> <span data-ttu-id="da5b6-115">Přejděte do složky projektu *Src/Security/Samples/CustomPolicyProvider* .</span><span class="sxs-lookup"><span data-stu-id="da5b6-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="da5b6-116">Přizpůsobení načtení zásad</span><span class="sxs-lookup"><span data-stu-id="da5b6-116">Customize policy retrieval</span></span>

<span data-ttu-id="da5b6-117">ASP.NET Core aplikace používají implementaci rozhraní `IAuthorizationPolicyProvider` k načtení zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="da5b6-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="da5b6-118">Ve výchozím nastavení je [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) zaregistrován a používán.</span><span class="sxs-lookup"><span data-stu-id="da5b6-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="da5b6-119">`DefaultAuthorizationPolicyProvider` vrátí zásady z `AuthorizationOptions` poskytnuté ve volání `IServiceCollection.AddAuthorization`.</span><span class="sxs-lookup"><span data-stu-id="da5b6-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="da5b6-120">Toto chování si přizpůsobte tak, že zaregistrujete jinou implementaci `IAuthorizationPolicyProvider` v kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="da5b6-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="da5b6-121">Rozhraní `IAuthorizationPolicyProvider` obsahuje tři rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="da5b6-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="da5b6-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) vrací zásadu autorizace pro daný název.</span><span class="sxs-lookup"><span data-stu-id="da5b6-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="da5b6-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) vrátí výchozí zásady autorizace (zásady používané pro `[Authorize]` atributů bez zadaných zásad).</span><span class="sxs-lookup"><span data-stu-id="da5b6-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="da5b6-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) vrátí záložní zásadu autorizace (zásady používané middlewarem autorizace, když není zadaná žádná zásada).</span><span class="sxs-lookup"><span data-stu-id="da5b6-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="da5b6-125">Implementací těchto rozhraní API můžete přizpůsobit způsob poskytování zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="da5b6-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="da5b6-126">Příklad parametrizovaného autorizačního atributu</span><span class="sxs-lookup"><span data-stu-id="da5b6-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="da5b6-127">Jedním z scénářů, kde `IAuthorizationPolicyProvider` je užitečné, je povolení vlastních atributů `[Authorize]`, jejichž požadavky závisí na parametru.</span><span class="sxs-lookup"><span data-stu-id="da5b6-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="da5b6-128">Například v dokumentaci pro [autorizaci na základě zásad](xref:security/authorization/policies) se jako ukázka použila zásada na základě stáří ("AtLeast21").</span><span class="sxs-lookup"><span data-stu-id="da5b6-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="da5b6-129">Pokud by měly být k dispozici různé akce kontroleru v aplikaci pro uživatele *různého* stáří, může být užitečné mít mnoho různých zásad založených na věku.</span><span class="sxs-lookup"><span data-stu-id="da5b6-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="da5b6-130">Místo registrace všech různých zásad založených na věku, které bude aplikace potřebovat v `AuthorizationOptions`, můžete zásady vygenerovat dynamicky pomocí vlastního `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="da5b6-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="da5b6-131">Aby bylo možné zásady používat snadněji, můžete poznámky provádět pomocí vlastního autorizačního atributu, jako je `[MinimumAgeAuthorize(20)]`.</span><span class="sxs-lookup"><span data-stu-id="da5b6-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="da5b6-132">Vlastní autorizační atributy</span><span class="sxs-lookup"><span data-stu-id="da5b6-132">Custom Authorization attributes</span></span>

<span data-ttu-id="da5b6-133">Zásady autorizace se identifikují podle jejich názvů.</span><span class="sxs-lookup"><span data-stu-id="da5b6-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="da5b6-134">Vlastní `MinimumAgeAuthorizeAttribute` popsané dříve musí mapovat argumenty na řetězec, který lze použít k načtení odpovídajících zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="da5b6-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="da5b6-135">Můžete to provést odvozením z `AuthorizeAttribute` a nastavením vlastnosti `Age` zabalit vlastnost `AuthorizeAttribute.Policy`.</span><span class="sxs-lookup"><span data-stu-id="da5b6-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="da5b6-136">Tento typ atributu má `Policy` řetězec na základě pevně kódované předpony (`"MinimumAge"`) a celé číslo předané prostřednictvím konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="da5b6-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="da5b6-137">Můžete ji použít na akce stejným způsobem jako ostatní atributy `Authorize` s tím rozdílem, že jako parametr přebírá celé číslo.</span><span class="sxs-lookup"><span data-stu-id="da5b6-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="da5b6-138">Vlastní IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="da5b6-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="da5b6-139">Vlastní `MinimumAgeAuthorizeAttribute` usnadňuje vyžádání autorizačních zásad pro případné minimální požadované stáří.</span><span class="sxs-lookup"><span data-stu-id="da5b6-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="da5b6-140">Další problém, který se má vyřešit, je ověření, jestli jsou zásady autorizace dostupné pro všechny různé věkové služby.</span><span class="sxs-lookup"><span data-stu-id="da5b6-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="da5b6-141">Zde je místo, kde je `IAuthorizationPolicyProvider` užitečné.</span><span class="sxs-lookup"><span data-stu-id="da5b6-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="da5b6-142">Při použití `MinimumAgeAuthorizationAttribute`se názvy zásad autorizace dostanou `"MinimumAge" + Age`vzor, takže vlastní `IAuthorizationPolicyProvider` vygeneruje zásady autorizace podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="da5b6-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="da5b6-143">Analyzuje se věk z názvu zásady.</span><span class="sxs-lookup"><span data-stu-id="da5b6-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="da5b6-144">Vytvoření nového `AuthorizationPolicy` pomocí `AuthorizationPolicyBuilder`</span><span class="sxs-lookup"><span data-stu-id="da5b6-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="da5b6-145">V tomto a následujících příkladech se předpokládá, že uživatel je ověřený prostřednictvím souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="da5b6-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="da5b6-146">`AuthorizationPolicyBuilder` by měl být vytvořen buď s alespoň jedním názvem schématu autorizace, nebo vždy úspěšně.</span><span class="sxs-lookup"><span data-stu-id="da5b6-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="da5b6-147">V opačném případě nejsou k dispozici žádné informace o tom, jak poskytnout uživateli výzvu a bude vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="da5b6-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="da5b6-148">Přidávají se požadavky na zásady na základě stáří pomocí `AuthorizationPolicyBuilder.AddRequirements`.</span><span class="sxs-lookup"><span data-stu-id="da5b6-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="da5b6-149">V jiných scénářích můžete místo toho použít `RequireClaim`, `RequireRole`nebo `RequireUserName`.</span><span class="sxs-lookup"><span data-stu-id="da5b6-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="da5b6-150">Několik poskytovatelů zásad autorizace</span><span class="sxs-lookup"><span data-stu-id="da5b6-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="da5b6-151">Při použití vlastních implementací `IAuthorizationPolicyProvider` mějte na paměti, že ASP.NET Core používá pouze jednu instanci `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="da5b6-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="da5b6-152">Pokud vlastní zprostředkovatel nedokáže poskytovat zásady autorizace pro všechny názvy zásad, které se použijí, měl by se odložit k poskytovateli zálohování.</span><span class="sxs-lookup"><span data-stu-id="da5b6-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="da5b6-153">Představte si třeba aplikaci, která potřebuje vlastní věkové zásady a jednodušší načítání zásad na základě rolí.</span><span class="sxs-lookup"><span data-stu-id="da5b6-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="da5b6-154">Taková aplikace by mohla použít vlastního zprostředkovatele autorizačních zásad, který:</span><span class="sxs-lookup"><span data-stu-id="da5b6-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="da5b6-155">Pokusí se analyzovat názvy zásad.</span><span class="sxs-lookup"><span data-stu-id="da5b6-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="da5b6-156">Zavolá do jiného poskytovatele zásad (například `DefaultAuthorizationPolicyProvider`), pokud název zásady neobsahuje stáří.</span><span class="sxs-lookup"><span data-stu-id="da5b6-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="da5b6-157">Ukázková `IAuthorizationPolicyProvider` výše uvedená výše se dá aktualizovat tak, aby používala `DefaultAuthorizationPolicyProvider` vytvořením poskytovatele zásad zálohování ve svém konstruktoru (aby se použila v případě, že se název zásady neshoduje s očekávaným vzorem "minimálního počtu" a stáří).</span><span class="sxs-lookup"><span data-stu-id="da5b6-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="da5b6-158">Pak lze metodu `GetPolicyAsync` aktualizovat tak, aby používala `BackupPolicyProvider` namísto vrácení hodnoty null:</span><span class="sxs-lookup"><span data-stu-id="da5b6-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="da5b6-159">Výchozí zásady</span><span class="sxs-lookup"><span data-stu-id="da5b6-159">Default policy</span></span>

<span data-ttu-id="da5b6-160">Kromě poskytování pojmenovaných autorizačních zásad musí vlastní `IAuthorizationPolicyProvider` implementovat `GetDefaultPolicyAsync`, aby poskytoval zásady autorizace pro `[Authorize]` atributů bez zadaného názvu zásady.</span><span class="sxs-lookup"><span data-stu-id="da5b6-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="da5b6-161">V mnoha případech tento atribut autorizace vyžaduje jenom ověřeného uživatele, takže můžete vytvořit potřebné zásady s voláním `RequireAuthenticatedUser`:</span><span class="sxs-lookup"><span data-stu-id="da5b6-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="da5b6-162">Stejně jako u všech aspektů vlastního `IAuthorizationPolicyProvider`můžete to podle potřeby přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="da5b6-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="da5b6-163">V některých případech může být žádoucí načíst výchozí zásadu z záložní `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="da5b6-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="da5b6-164">Záložní zásady</span><span class="sxs-lookup"><span data-stu-id="da5b6-164">Fallback policy</span></span>

<span data-ttu-id="da5b6-165">Vlastní `IAuthorizationPolicyProvider` může volitelně implementovat `GetFallbackPolicyAsync` a poskytnout tak zásadu, která se používá při [kombinování zásad](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) a kdy nejsou zadané žádné zásady.</span><span class="sxs-lookup"><span data-stu-id="da5b6-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="da5b6-166">Pokud `GetFallbackPolicyAsync` vrátí zásadu, která nemá hodnotu null, je vrácená zásada používána middlewarem autorizace, když nejsou pro požadavek zadány žádné zásady.</span><span class="sxs-lookup"><span data-stu-id="da5b6-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="da5b6-167">Pokud se nevyžadují žádné záložní zásady, může zprostředkovatel vrátit `null` nebo se odložit k záložnímu zprostředkovateli:</span><span class="sxs-lookup"><span data-stu-id="da5b6-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="da5b6-168">Použití vlastního IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="da5b6-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="da5b6-169">Pokud chcete použít vlastní zásady z `IAuthorizationPolicyProvider`, musíte:</span><span class="sxs-lookup"><span data-stu-id="da5b6-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="da5b6-170">Zaregistrujte odpovídající typy `AuthorizationHandler` se vkládáním závislostí (popsané v tématu [autorizace na základě zásad](xref:security/authorization/policies#authorization-handlers)), stejně jako u všech scénářů autorizace na základě zásad.</span><span class="sxs-lookup"><span data-stu-id="da5b6-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="da5b6-171">Zaregistrujte vlastní typ `IAuthorizationPolicyProvider` v kolekci služby pro vkládání závislostí aplikace (v `Startup.ConfigureServices`), aby se výchozí zprostředkovatel zásad nahradil.</span><span class="sxs-lookup"><span data-stu-id="da5b6-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="da5b6-172">Kompletní ukázka vlastní `IAuthorizationPolicyProvider` je k dispozici v [úložišti GitHub ASPNET/AuthSamples](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="da5b6-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [aspnet/AuthSamples GitHub repository](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span></span>

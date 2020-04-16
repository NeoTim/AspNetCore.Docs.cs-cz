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
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="38105-103">Vlastní zprostředkovatelé autorizačních zásad používající iAuthorizationPolicyProvider v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="38105-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="38105-104">Podle [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="38105-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="38105-105">Obvykle při použití [autorizace založené na zásadách](xref:security/authorization/policies)jsou zásady registrovány voláním `AuthorizationOptions.AddPolicy` jako součást konfigurace autorizační služby.</span><span class="sxs-lookup"><span data-stu-id="38105-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="38105-106">V některých případech nemusí být možné (nebo žádoucí) zaregistrovat všechny zásady autorizace tímto způsobem.</span><span class="sxs-lookup"><span data-stu-id="38105-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="38105-107">V těchto případech můžete použít `IAuthorizationPolicyProvider` vlastní řídit, jak jsou poskytovány zásady autorizace.</span><span class="sxs-lookup"><span data-stu-id="38105-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="38105-108">Příklady scénářů, kde může být užitečný vlastní [IAuthorizationPolicyProvider:](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider)</span><span class="sxs-lookup"><span data-stu-id="38105-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="38105-109">Použití externí služby k zajištění vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="38105-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="38105-110">Použití široké škály zásad (například pro různá čísla nebo věk) proto nemá smysl přidávat `AuthorizationOptions.AddPolicy` jednotlivé zásady autorizace pomocí hovoru.</span><span class="sxs-lookup"><span data-stu-id="38105-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="38105-111">Vytváření zásad za běhu na základě informací v externím zdroji dat (například databáze) nebo dynamického určení požadavků na autorizaci prostřednictvím jiného mechanismu.</span><span class="sxs-lookup"><span data-stu-id="38105-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="38105-112">[Zobrazení nebo stažení ukázkového kódu](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) z [úložiště AspNetCore GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="38105-112">[View or download sample code](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="38105-113">Stáhněte si soubor ZIP úložiště dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="38105-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="38105-114">Rozbalte soubor.</span><span class="sxs-lookup"><span data-stu-id="38105-114">Unzip the file.</span></span> <span data-ttu-id="38105-115">Přejděte do složky projektu *src/Security/samples/CustomPolicyProvider.*</span><span class="sxs-lookup"><span data-stu-id="38105-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="38105-116">Přizpůsobení načítání zásad</span><span class="sxs-lookup"><span data-stu-id="38105-116">Customize policy retrieval</span></span>

<span data-ttu-id="38105-117">ASP.NET Základní aplikace používají `IAuthorizationPolicyProvider` implementaci rozhraní k načtení zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="38105-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="38105-118">Ve výchozím nastavení je [StandardAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registrován a používán.</span><span class="sxs-lookup"><span data-stu-id="38105-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="38105-119">`DefaultAuthorizationPolicyProvider`vrátí zásady `AuthorizationOptions` z uvedených ve `IServiceCollection.AddAuthorization` výzvě.</span><span class="sxs-lookup"><span data-stu-id="38105-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="38105-120">Přizpůsobte toto chování `IAuthorizationPolicyProvider` registrací jiné implementace v kontejneru [vkládání závislostí](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="38105-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="38105-121">Rozhraní `IAuthorizationPolicyProvider` obsahuje tři rozhraní API:</span><span class="sxs-lookup"><span data-stu-id="38105-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="38105-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) vrátí zásady autorizace pro daný název.</span><span class="sxs-lookup"><span data-stu-id="38105-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="38105-123">[Funkce GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) vrátí výchozí zásadu `[Authorize]` autorizace (zásadu používanou pro atributy bez zadané zásady).</span><span class="sxs-lookup"><span data-stu-id="38105-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="38105-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) vrátí záložní zásady autorizace (zásady používané autorizace Middleware, pokud není zadána žádná zásada).</span><span class="sxs-lookup"><span data-stu-id="38105-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="38105-125">Implementací těchto api můžete přizpůsobit způsob, jakým jsou k dispozici zásady autorizace.</span><span class="sxs-lookup"><span data-stu-id="38105-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="38105-126">Parametrizovaný příklad atributu authorize</span><span class="sxs-lookup"><span data-stu-id="38105-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="38105-127">Jeden scénář, kde `IAuthorizationPolicyProvider` je `[Authorize]` užitečné povolení vlastní atributy, jejichž požadavky závisí na parametr.</span><span class="sxs-lookup"><span data-stu-id="38105-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="38105-128">Například v dokumentaci [autorizace založené na zásadách](xref:security/authorization/policies) byla jako ukázka použita zásada založená na věku ("AtLeast21").</span><span class="sxs-lookup"><span data-stu-id="38105-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="38105-129">Pokud by měly být uživatelům *různého* věku k dispozici různé akce kontroloru, může být užitečné mít mnoho různých zásad založených na věku.</span><span class="sxs-lookup"><span data-stu-id="38105-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="38105-130">Namísto registrace všech různých zásad založených na věku, `AuthorizationOptions`které bude aplikace potřebovat v `IAuthorizationPolicyProvider`aplikaci , můžete zásady generovat dynamicky pomocí vlastního .</span><span class="sxs-lookup"><span data-stu-id="38105-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="38105-131">Chcete-li usnadnit používání zásad, můžete akce oznamovat vlastními atributy autorizace, jako je `[MinimumAgeAuthorize(20)]`.</span><span class="sxs-lookup"><span data-stu-id="38105-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="38105-132">Atributy vlastní autorizace</span><span class="sxs-lookup"><span data-stu-id="38105-132">Custom Authorization attributes</span></span>

<span data-ttu-id="38105-133">Zásady autorizace jsou označeny jejich názvy.</span><span class="sxs-lookup"><span data-stu-id="38105-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="38105-134">Vlastní `MinimumAgeAuthorizeAttribute` popsané dříve potřebuje mapovat argumenty do řetězce, který lze použít k načtení odpovídající zásady autorizace.</span><span class="sxs-lookup"><span data-stu-id="38105-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="38105-135">Můžete to udělat tak, že `AuthorizeAttribute` odvodíte a přimějete `Age` vlastnost zabalit `AuthorizeAttribute.Policy` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="38105-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="38105-136">Tento typ atributu `Policy` má řetězec založený na pevně`"MinimumAge"`zakódované předponě ( ) a celé číslo předané prostřednictvím konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="38105-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="38105-137">Můžete ji použít na akce stejným `Authorize` způsobem jako ostatní atributy s tím rozdílem, že trvá celé číslo jako parametr.</span><span class="sxs-lookup"><span data-stu-id="38105-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="38105-138">Vlastní iAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="38105-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="38105-139">Vlastní `MinimumAgeAuthorizeAttribute` usnadňuje požádat o autorizaci zásady pro minimální požadovaný věk.</span><span class="sxs-lookup"><span data-stu-id="38105-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="38105-140">Dalším problémem, který je třeba vyřešit, je zajistit, aby byly k dispozici zásady autorizace pro všechny tyto různé věkové kategorie.</span><span class="sxs-lookup"><span data-stu-id="38105-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="38105-141">To je `IAuthorizationPolicyProvider` místo, kde je užitečné.</span><span class="sxs-lookup"><span data-stu-id="38105-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="38105-142">Při `MinimumAgeAuthorizationAttribute`použití budou názvy zásad `"MinimumAge" + Age`autorizace následovat `IAuthorizationPolicyProvider` podle vzoru , takže vlastní by měl generovat zásady autorizace:</span><span class="sxs-lookup"><span data-stu-id="38105-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="38105-143">Analýza stáří z názvu zásady.</span><span class="sxs-lookup"><span data-stu-id="38105-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="38105-144">Použití `AuthorizationPolicyBuilder` k vytvoření nového`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="38105-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="38105-145">V tomto a následujících příkladech se předpokládá, že uživatel je ověřen prostřednictvím souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="38105-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="38105-146">By `AuthorizationPolicyBuilder` měl být buď vytvořen s alespoň jeden název autorizačního schématu nebo vždy úspěšné.</span><span class="sxs-lookup"><span data-stu-id="38105-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="38105-147">V opačném případě nejsou k dispozici žádné informace o tom, jak poskytnout výzvu uživateli a bude vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="38105-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="38105-148">Přidání požadavků na zásady na `AuthorizationPolicyBuilder.AddRequirements`základě věku s .</span><span class="sxs-lookup"><span data-stu-id="38105-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="38105-149">V jiných scénářích `RequireClaim`můžete `RequireRole`použít `RequireUserName` , nebo místo.</span><span class="sxs-lookup"><span data-stu-id="38105-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="38105-150">Více poskytovatelů autorizačních zásad</span><span class="sxs-lookup"><span data-stu-id="38105-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="38105-151">Při použití `IAuthorizationPolicyProvider` vlastníimplementace, mějte na paměti, že `IAuthorizationPolicyProvider`ASP.NET Core používá pouze jednu instanci .</span><span class="sxs-lookup"><span data-stu-id="38105-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="38105-152">Pokud vlastní zprostředkovatel není schopen poskytnout zásady autorizace pro všechny názvy zásad, které budou použity, by měl odložit na zprostředkovatele zálohování.</span><span class="sxs-lookup"><span data-stu-id="38105-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="38105-153">Zvažte například aplikaci, která potřebuje vlastní zásady věku a tradiční načítání zásad založených na rolích.</span><span class="sxs-lookup"><span data-stu-id="38105-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="38105-154">Taková aplikace může používat vlastního poskytovatele autorizačních zásad, který:</span><span class="sxs-lookup"><span data-stu-id="38105-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="38105-155">Pokusí se analyzovat názvy zásad.</span><span class="sxs-lookup"><span data-stu-id="38105-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="38105-156">Volá do jiného poskytovatele `DefaultAuthorizationPolicyProvider`zásad (například), pokud název zásady neobsahuje věk.</span><span class="sxs-lookup"><span data-stu-id="38105-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="38105-157">Příklad `IAuthorizationPolicyProvider` implementace uvedené výše lze aktualizovat `DefaultAuthorizationPolicyProvider` použít vytvořením zprostředkovatele zásad zálohování v jeho konstruktoru (který se použije v případě, že název zásady neodpovídá jeho očekávaný vzor "MinimumAge" + stáří).</span><span class="sxs-lookup"><span data-stu-id="38105-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="38105-158">Potom `GetPolicyAsync` metoda může být aktualizován `BackupPolicyProvider` a použít místo vrácení null:</span><span class="sxs-lookup"><span data-stu-id="38105-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="38105-159">Výchozí zásada</span><span class="sxs-lookup"><span data-stu-id="38105-159">Default policy</span></span>

<span data-ttu-id="38105-160">Kromě poskytování pojmenovaných zásad autorizace je `GetDefaultPolicyAsync` třeba provést vlastní `[Authorize]` `IAuthorizationPolicyProvider` implementaci, aby bylo zajištěno zásady autorizace pro atributy bez zadaného názvu zásady.</span><span class="sxs-lookup"><span data-stu-id="38105-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="38105-161">V mnoha případech tento atribut autorizace vyžaduje pouze ověřeného uživatele, takže `RequireAuthenticatedUser`můžete provést nezbytné zásady s voláním na :</span><span class="sxs-lookup"><span data-stu-id="38105-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="38105-162">Stejně jako u všech `IAuthorizationPolicyProvider`aspektů vlastní , můžete přizpůsobit to, podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="38105-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="38105-163">V některých případech může být žádoucí načíst výchozí `IAuthorizationPolicyProvider`zásady z záložní .</span><span class="sxs-lookup"><span data-stu-id="38105-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="38105-164">Záložní zásady</span><span class="sxs-lookup"><span data-stu-id="38105-164">Fallback policy</span></span>

<span data-ttu-id="38105-165">Vlastní `IAuthorizationPolicyProvider` můžete volitelně `GetFallbackPolicyAsync` implementovat poskytnout zásady, které se používají při [kombinování zásad](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) a když nejsou zadány žádné zásady.</span><span class="sxs-lookup"><span data-stu-id="38105-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="38105-166">Pokud `GetFallbackPolicyAsync` vrátí zásadu bez null, vrácené zásady se používá autorizace Middleware, pokud nejsou určeny žádné zásady pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="38105-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="38105-167">Pokud není vyžadována žádná záložní zásada, zprostředkovatel může vrátit `null` nebo odložit záložnízprostředkovatel:</span><span class="sxs-lookup"><span data-stu-id="38105-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="38105-168">Použití vlastního zprostředkovatele IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="38105-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="38105-169">Chcete-li používat `IAuthorizationPolicyProvider`vlastní zásady z aplikace , musíte:</span><span class="sxs-lookup"><span data-stu-id="38105-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="38105-170">Zaregistrujte `AuthorizationHandler` příslušné typy s vkládání závislostí (popsané v [autorizaci založené na zásadách](xref:security/authorization/policies#authorization-handlers)), stejně jako u všech scénářů autorizace založených na zásadách.</span><span class="sxs-lookup"><span data-stu-id="38105-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="38105-171">Zaregistrujte `IAuthorizationPolicyProvider` vlastní typ v kolekci služeb vkládání `Startup.ConfigureServices`závislostí aplikace (v ) a nahraďte výchozího zprostředkovatele zásad.</span><span class="sxs-lookup"><span data-stu-id="38105-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="38105-172">Kompletní vlastní `IAuthorizationPolicyProvider` ukázka je k dispozici v [úložišti GitHub dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="38105-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).</span></span>

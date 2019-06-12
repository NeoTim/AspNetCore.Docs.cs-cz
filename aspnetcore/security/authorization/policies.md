---
title: Autorizace na základě zásad v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit a použít rutiny zásad autorizace pro vynucují požadavky pro autorizaci v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: security/authorization/policies
ms.openlocfilehash: 67337c847ba71df3fe61250996ec944632ad5d57
ms.sourcegitcommit: 1bb3f3f1905b4e7d4ca1b314f2ce6ee5dd8be75f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2019
ms.locfileid: "66837345"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="42436-103">Autorizace na základě zásad v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42436-103">Policy-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="42436-104">Na pozadí [autorizace na základě rolí](xref:security/authorization/roles) a [autorizace na základě rolí](xref:security/authorization/claims) pomocí požadavku, obslužné rutiny požadavku a předem nakonfigurované zásady.</span><span class="sxs-lookup"><span data-stu-id="42436-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="42436-105">Tyto stavební bloky podporují výraz vyhodnocení autorizace v kódu.</span><span class="sxs-lookup"><span data-stu-id="42436-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="42436-106">Výsledkem je struktura bohatší, opakovaně použitelný, testovatelného autorizace.</span><span class="sxs-lookup"><span data-stu-id="42436-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="42436-107">Zásady autorizace se skládá z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="42436-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="42436-108">Je registrován jako součást konfigurace služby ověřování v `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="42436-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="42436-109">V předchozím příkladu se vytvoří zásadu "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="42436-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="42436-110">Má jeden požadavek&mdash;s minimálním stářím, který je poskytnut jako parametr s požadavkem.</span><span class="sxs-lookup"><span data-stu-id="42436-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="42436-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="42436-111">IAuthorizationService</span></span> 

<span data-ttu-id="42436-112">Primární služba, která určuje, zda je ověření úspěšné <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span><span class="sxs-lookup"><span data-stu-id="42436-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="42436-113">Předchozí kód zvýrazní dvou metod [služby IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="42436-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="42436-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> je služba značky se žádné metody a mechanismus pro sledování, jestli je ověřování úspěšné.</span><span class="sxs-lookup"><span data-stu-id="42436-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="42436-115">Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> zodpovídá za probíhá kontrola, zda jsou splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="42436-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="42436-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Třída je obslužná rutina používá k označení, zda byly splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="42436-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="42436-117">Následující kód ukazuje zjednodušený (i s poznámkami s komentáři) výchozí implementace autorizační službu:</span><span class="sxs-lookup"><span data-stu-id="42436-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="42436-118">Následující kód ukazuje typické `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="42436-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="42436-119">Použití <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something"]` pro autorizaci.</span><span class="sxs-lookup"><span data-stu-id="42436-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something"]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="42436-120">Přiřazení zásad pro kontrolery MVC</span><span class="sxs-lookup"><span data-stu-id="42436-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="42436-121">Pokud používáte pro stránky Razor, přečtěte si téma [přiřazení zásad pro stránky Razor](#applying-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="42436-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="42436-122">Zásady se použijí k řadičům pomocí `[Authorize]` atribut s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="42436-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="42436-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="42436-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="42436-124">Přiřazení zásad pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="42436-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="42436-125">Zásady se použijí pro stránky Razor pomocí `[Authorize]` atribut s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="42436-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="42436-126">Příklad:</span><span class="sxs-lookup"><span data-stu-id="42436-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="42436-127">Zásady můžete použít také pro stránky Razor pomocí [autorizace konvence](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="42436-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="42436-128">Požadavky</span><span class="sxs-lookup"><span data-stu-id="42436-128">Requirements</span></span>

<span data-ttu-id="42436-129">Požadavek na autorizaci je kolekce dat parametry, které zásady můžete použít k vyhodnocení aktuální hlavní název uživatele.</span><span class="sxs-lookup"><span data-stu-id="42436-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="42436-130">V zásadách "AtLeast21" požadavek je jediný parametr&mdash;minimálním stářím.</span><span class="sxs-lookup"><span data-stu-id="42436-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="42436-131">Implementuje požadavek [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je rozhraní prázdnou značku.</span><span class="sxs-lookup"><span data-stu-id="42436-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="42436-132">Parametrizované minimální stáří požadavek může být implementován takto:</span><span class="sxs-lookup"><span data-stu-id="42436-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="42436-133">Zásad autorizace obsahuje více požadavků na autorizaci, všechny požadavky musí předat v pořadí pro hodnocení zásad proběhla úspěšně.</span><span class="sxs-lookup"><span data-stu-id="42436-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="42436-134">Jinými slovy, více autorizace požadavky do jednoho autorizační zásady jsou považovány na **a** základ.</span><span class="sxs-lookup"><span data-stu-id="42436-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="42436-135">Požadavek není nutné mít data nebo vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="42436-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="42436-136">Povolení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="42436-136">Authorization handlers</span></span>

<span data-ttu-id="42436-137">Je zodpovědná za vyhodnocení vlastností požadavek na obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="42436-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="42436-138">Obslužná rutina autorizace vyhodnotí požadavky proti zadaný [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, zda je povolen přístup.</span><span class="sxs-lookup"><span data-stu-id="42436-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="42436-139">Může mít požadavek [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="42436-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="42436-140">Obslužná rutina může dědit [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je potřeba zpracovat.</span><span class="sxs-lookup"><span data-stu-id="42436-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="42436-141">Alternativně může implementovat obslužnou rutinu [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jeden typ požadavku.</span><span class="sxs-lookup"><span data-stu-id="42436-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="42436-142">Použijte obslužnou rutinu pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="42436-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="42436-143">Následuje příklad vztah 1: 1, ve kterém obslužnou rutinu minimální stáří využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="42436-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="42436-144">Předchozí kód určuje, zda má aktuální uživatel instančního objektu datum narození deklarace identity, který byl vydán známého a důvěryhodného vystavitele.</span><span class="sxs-lookup"><span data-stu-id="42436-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="42436-145">Ověření nelze provést, pokud chybí deklarace identity, v takovém případě se vrátí dokončeného úkolu.</span><span class="sxs-lookup"><span data-stu-id="42436-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="42436-146">Při deklaraci identity je k dispozici, se vypočítá věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="42436-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="42436-147">Pokud uživatel splňuje minimální stáří určené požadavku, autorizace se bude považovat za úspěšné.</span><span class="sxs-lookup"><span data-stu-id="42436-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="42436-148">Když je ověření úspěšné, `context.Succeed` vyvolání splnění požadavku jako svůj jediný parametr.</span><span class="sxs-lookup"><span data-stu-id="42436-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="42436-149">Použijte obslužnou rutinu pro několik požadavků</span><span class="sxs-lookup"><span data-stu-id="42436-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="42436-150">Následuje příklad vztah jeden mnoho, ve kterém obslužnou rutinu oprávnění dokáže zpracovat tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="42436-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="42436-151">Předchozí kód projde [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky není označena jako úspěšně dokončený.</span><span class="sxs-lookup"><span data-stu-id="42436-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="42436-152">Pro `ReadPermission` požadavek, uživatel musí být vlastníkem nebo sponzor přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="42436-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="42436-153">V případě třídy `EditPermission` nebo `DeletePermission` požadavek, nezíská musí být vlastníkem pro přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="42436-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="42436-154">Zápis obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="42436-154">Handler registration</span></span>

<span data-ttu-id="42436-155">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42436-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="42436-156">Příklad:</span><span class="sxs-lookup"><span data-stu-id="42436-156">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="42436-157">Předchozí kód registruje `MinimumAgeHandler` jako singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="42436-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="42436-158">Obslužné rutiny lze registrovat pomocí některé z integrovaného [služby životnosti](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="42436-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="42436-159">Co by měl vrátit obslužnou rutinu?</span><span class="sxs-lookup"><span data-stu-id="42436-159">What should a handler return?</span></span>

<span data-ttu-id="42436-160">Všimněte si, že `Handle` metodu [příklad obslužná rutina](#security-authorization-handler-example) nevrací žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="42436-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="42436-161">Jak se stavem úspěch nebo selhání uvedené?</span><span class="sxs-lookup"><span data-stu-id="42436-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="42436-162">Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)`, předá tento požadavek, který se úspěšně ověřilo.</span><span class="sxs-lookup"><span data-stu-id="42436-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="42436-163">Obslužná rutina nemusí řešit selhání obecně platí, jako ostatní obslužné rutiny pro stejný požadavek může být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="42436-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="42436-164">Chcete-li zaručit selhání, i v případě ostatních obslužných rutin požadavek úspěšný, zavolejte `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="42436-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="42436-165">Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail`, stále se nazývají všechny ostatní obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="42436-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="42436-166">To umožňuje požadavek na vytvoření vedlejší účinky, jako je například protokolování, které dojde i v případě, že má jinou obslužnou rutinu úspěšně ověřen nebo požadavku se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="42436-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="42436-167">Pokud je nastavena na `false`, [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) vlastnosti (k dispozici v ASP.NET Core 1.1 nebo novější) zkratům provádění obslužné rutiny při `context.Fail` je volána.</span><span class="sxs-lookup"><span data-stu-id="42436-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="42436-168">`InvokeHandlersAfterFailure` Výchozí hodnota je `true`, v takovém případě se nazývají všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="42436-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="42436-169">I když se ověřování nezdaří, jsou volány obslužné rutiny autorizace.</span><span class="sxs-lookup"><span data-stu-id="42436-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="42436-170">Proč byste měli více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="42436-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="42436-171">V případech, kde chcete vyhodnocení na **nebo** základ, implementovat více obslužných rutin pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="42436-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="42436-172">Microsoft má například dveří, které otevřít pouze pomocí klíčů karty.</span><span class="sxs-lookup"><span data-stu-id="42436-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="42436-173">Ponecháte-li karta s klíčem v domácnostech, recepční vytiskne dočasné štítku a otevře dveře za vás.</span><span class="sxs-lookup"><span data-stu-id="42436-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="42436-174">V tomto scénáři bude mít jeden požadavek *BuildingEntry*, ale více obslužných rutin, každý z nich zkoumání jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="42436-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="42436-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="42436-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="42436-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="42436-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="42436-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="42436-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="42436-178">Ujistěte se, že jsou oba obslužné rutiny [zaregistrovaný](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="42436-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="42436-179">Pokud je buď obslužná rutina úspěšné při zásady vyhodnotí `BuildingEntryRequirement`, hodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="42436-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="42436-180">Používat funkci func pro splnění zásad</span><span class="sxs-lookup"><span data-stu-id="42436-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="42436-181">Může nastat situace, ve které splňující zásady je jednoduchý vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="42436-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="42436-182">Je možné zadat `Func<AuthorizationHandlerContext, bool>` při konfiguraci zásad s `RequireAssertion` tvůrce zásad.</span><span class="sxs-lookup"><span data-stu-id="42436-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="42436-183">Například předchozí `BadgeEntryHandler` měl by být přepsán následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="42436-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="42436-184">Přístup k kontext požadavku MVC v obslužné rutině</span><span class="sxs-lookup"><span data-stu-id="42436-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="42436-185">`HandleRequirementAsync` Implementovat v obslužné rutině autorizační metoda má dva parametry: `AuthorizationHandlerContext` a `TRequirement` obsluhujete.</span><span class="sxs-lookup"><span data-stu-id="42436-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="42436-186">Architektur, jako je MVC nebo Jabbr jsou můžete přidat libovolný objekt `Resource` vlastnost `AuthorizationHandlerContext` k předávání dalších informací.</span><span class="sxs-lookup"><span data-stu-id="42436-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="42436-187">Například MVC předá instance [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) v `Resource` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="42436-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="42436-188">Tato vlastnost poskytuje přístup k `HttpContext`, `RouteData`a všechno, co je jinak poskytované MVC a stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="42436-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="42436-189">Použití `Resource` vlastnost je konkrétní rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="42436-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="42436-190">Pomocí informací `Resource` vlastnost omezuje vaše zásady autorizace pro konkrétní platformy.</span><span class="sxs-lookup"><span data-stu-id="42436-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="42436-191">Měli přetypovat `Resource` pomocí vlastnosti `is` – klíčové slovo a potom potvrdit přetypování proběhla úspěšně, aby se váš kód nebude k chybě s `InvalidCastException` při spuštění na ostatní platformy:</span><span class="sxs-lookup"><span data-stu-id="42436-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

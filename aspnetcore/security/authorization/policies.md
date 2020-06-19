---
title: Ověřování na základě zásad v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet a používat obslužné rutiny zásad autorizace pro vynucování autorizačních požadavků v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 533bddc9c4499dad99cfdb3089045ea10aed4548
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074162"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="e06c7-103">Ověřování na základě zásad v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e06c7-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e06c7-104">V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady.</span><span class="sxs-lookup"><span data-stu-id="e06c7-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="e06c7-105">Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu.</span><span class="sxs-lookup"><span data-stu-id="e06c7-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="e06c7-106">Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.</span><span class="sxs-lookup"><span data-stu-id="e06c7-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="e06c7-107">Zásady autorizace se skládají z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="e06c7-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="e06c7-108">Je zaregistrován jako součást konfigurace autorizační služby v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="e06c7-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="e06c7-109">V předchozím příkladu se vytvoří zásada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="e06c7-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="e06c7-110">Má jeden požadavek na &mdash; Minimální stáří, které je zadáno jako parametr požadavku.</span><span class="sxs-lookup"><span data-stu-id="e06c7-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="e06c7-111">Načetl služby IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="e06c7-111">IAuthorizationService</span></span> 

<span data-ttu-id="e06c7-112">Primární služba, která určuje, jestli je autorizace úspěšná, je <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="e06c7-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="e06c7-113">Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="e06c7-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="e06c7-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda autorizace je úspěšná.</span><span class="sxs-lookup"><span data-stu-id="e06c7-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="e06c7-115">Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> je zodpovědný za kontrolu, zda jsou splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="e06c7-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

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

<span data-ttu-id="e06c7-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>Třída je to, co obslužná rutina používá k označení, zda byly splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="e06c7-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="e06c7-117">Následující kód zobrazuje zjednodušenou (a poznámkovou poznámku) výchozí implementaci autorizační služby:</span><span class="sxs-lookup"><span data-stu-id="e06c7-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="e06c7-118">Následující kód ukazuje typický `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e06c7-118">The following code shows a typical `ConfigureServices`:</span></span>

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


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="e06c7-119">Použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]` k autorizaci.</span><span class="sxs-lookup"><span data-stu-id="e06c7-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="e06c7-120">Použití zásad u řadičů MVC</span><span class="sxs-lookup"><span data-stu-id="e06c7-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="e06c7-121">Pokud používáte Razor stránky, přečtěte si téma [použití zásad na Razor stránkách](#apply-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e06c7-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="e06c7-122">Zásady se aplikují na řadiče pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="e06c7-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e06c7-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e06c7-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="e06c7-124">Použít zásady na Razor stránky</span><span class="sxs-lookup"><span data-stu-id="e06c7-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="e06c7-125">Zásady se aplikují na Razor stránky pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="e06c7-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e06c7-126">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e06c7-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="e06c7-127">Zásady se dají na stránkách použít taky Razor pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="e06c7-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="e06c7-128">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e06c7-128">Requirements</span></span>

<span data-ttu-id="e06c7-129">Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="e06c7-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="e06c7-130">V našich zásadách "AtLeast21" je požadavek jedním parametrem &mdash; Minimální stáří.</span><span class="sxs-lookup"><span data-stu-id="e06c7-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="e06c7-131">Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky.</span><span class="sxs-lookup"><span data-stu-id="e06c7-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="e06c7-132">Parametr minimálního stáří by mohl být implementován následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="e06c7-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="e06c7-133">Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="e06c7-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="e06c7-134">Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .</span><span class="sxs-lookup"><span data-stu-id="e06c7-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="e06c7-135">Požadavek nemusí mít data ani vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e06c7-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="e06c7-136">Obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="e06c7-136">Authorization handlers</span></span>

<span data-ttu-id="e06c7-137">Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku.</span><span class="sxs-lookup"><span data-stu-id="e06c7-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="e06c7-138">Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.</span><span class="sxs-lookup"><span data-stu-id="e06c7-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="e06c7-139">Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="e06c7-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="e06c7-140">Obslužná rutina může [Zdědit \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je požadavek, který má být zpracován.</span><span class="sxs-lookup"><span data-stu-id="e06c7-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="e06c7-141">Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.</span><span class="sxs-lookup"><span data-stu-id="e06c7-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="e06c7-142">Použití obslužné rutiny pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="e06c7-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="e06c7-143">Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="e06c7-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="e06c7-144">Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem.</span><span class="sxs-lookup"><span data-stu-id="e06c7-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="e06c7-145">K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol.</span><span class="sxs-lookup"><span data-stu-id="e06c7-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="e06c7-146">Pokud je k dispozici deklarace identity, počítá se věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="e06c7-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="e06c7-147">Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou.</span><span class="sxs-lookup"><span data-stu-id="e06c7-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="e06c7-148">Po úspěšném ověření `context.Succeed` je vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.</span><span class="sxs-lookup"><span data-stu-id="e06c7-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="e06c7-149">Použití obslužné rutiny pro více požadavků</span><span class="sxs-lookup"><span data-stu-id="e06c7-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="e06c7-150">Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="e06c7-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="e06c7-151">Předchozí průchod kódu [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; vlastnost obsahující požadavky, které nejsou označeny jako úspěšné.</span><span class="sxs-lookup"><span data-stu-id="e06c7-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="e06c7-152">Pro `ReadPermission` přístup k požadovanému prostředku musí být uživatel buď vlastník, nebo sponzor.</span><span class="sxs-lookup"><span data-stu-id="e06c7-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="e06c7-153">V případě `EditPermission` `DeletePermission` požadavku nebo musí být vlastníkem přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="e06c7-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="e06c7-154">Registrace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="e06c7-154">Handler registration</span></span>

<span data-ttu-id="e06c7-155">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="e06c7-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="e06c7-156">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e06c7-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="e06c7-157">Předchozí kód se registruje `MinimumAgeHandler` jako typ singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="e06c7-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="e06c7-158">Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="e06c7-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="e06c7-159">Co by měl obslužná rutina vrátit?</span><span class="sxs-lookup"><span data-stu-id="e06c7-159">What should a handler return?</span></span>

<span data-ttu-id="e06c7-160">Všimněte si, že `Handle` metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e06c7-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="e06c7-161">Jak se uvádí stav buď úspěch, nebo neúspěch?</span><span class="sxs-lookup"><span data-stu-id="e06c7-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="e06c7-162">Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)` a předáním požadavku, který byl úspěšně ověřen.</span><span class="sxs-lookup"><span data-stu-id="e06c7-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="e06c7-163">Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="e06c7-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="e06c7-164">Chcete-li zaručit selhání i v případě úspěchu dalších obslužných rutin požadavků, zavolejte `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="e06c7-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="e06c7-165">Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail` , všechny ostatní obslužné rutiny jsou stále volány.</span><span class="sxs-lookup"><span data-stu-id="e06c7-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="e06c7-166">To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek.</span><span class="sxs-lookup"><span data-stu-id="e06c7-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="e06c7-167">Když je nastavena na `false` , vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy, kdy je volána spuštění obslužných rutin `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="e06c7-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="e06c7-168">`InvokeHandlersAfterFailure`Výchozí hodnota – v takovém `true` případě jsou volány všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="e06c7-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="e06c7-169">Obslužné rutiny autorizace jsou volány i v případě, že ověřování selhává.</span><span class="sxs-lookup"><span data-stu-id="e06c7-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="e06c7-170">Proč bych chtěl více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="e06c7-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="e06c7-171">V případech, kdy chcete, aby vyhodnocení bylo na **nebo** bázi, implementujte více obslužných rutin pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="e06c7-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="e06c7-172">Například Microsoft má dveře, které se otevírají jenom s kartami Key.</span><span class="sxs-lookup"><span data-stu-id="e06c7-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="e06c7-173">Pokud ponecháte kartu klíče na domovské stránce, recepční vytiskne dočasný štítek a otevře dvířka za vás.</span><span class="sxs-lookup"><span data-stu-id="e06c7-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="e06c7-174">V tomto scénáři byste měli jeden požadavek, *BuildingEntry*, ale několik obslužných rutin, každý z nich prozkoumá jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="e06c7-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="e06c7-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="e06c7-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="e06c7-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e06c7-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="e06c7-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e06c7-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="e06c7-178">Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="e06c7-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="e06c7-179">Pokud obslužná rutina uspěje, když zásada vyhodnotí `BuildingEntryRequirement` , vyhodnocení zásad je úspěšné.</span><span class="sxs-lookup"><span data-stu-id="e06c7-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="e06c7-180">Použití funkce ke splnění zásad</span><span class="sxs-lookup"><span data-stu-id="e06c7-180">Use a func to fulfill a policy</span></span>

<span data-ttu-id="e06c7-181">Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="e06c7-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="e06c7-182">Je možné dodat a `Func<AuthorizationHandlerContext, bool>` nakonfigurovat zásadu pomocí `RequireAssertion` Tvůrce zásad.</span><span class="sxs-lookup"><span data-stu-id="e06c7-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="e06c7-183">Například předchozí `BadgeEntryHandler` může být přepsána následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="e06c7-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="e06c7-184">Přístup k kontextu požadavku MVC v obslužných rutinách</span><span class="sxs-lookup"><span data-stu-id="e06c7-184">Access MVC request context in handlers</span></span>

<span data-ttu-id="e06c7-185">`HandleRequirementAsync`Metoda, kterou implementujete v obslužné rutině autorizace, má dva parametry: `AuthorizationHandlerContext` a, `TRequirement` kterou zpracováváte.</span><span class="sxs-lookup"><span data-stu-id="e06c7-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="e06c7-186">Rozhraní, jako je MVC, nebo SignalR jsou volná pro přidání libovolného objektu do `Resource` vlastnosti v `AuthorizationHandlerContext` pro předání dalších informací.</span><span class="sxs-lookup"><span data-stu-id="e06c7-186">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="e06c7-187">Při použití směrování koncových bodů se autorizaci obvykle zpracovává pomocí middleware autorizace.</span><span class="sxs-lookup"><span data-stu-id="e06c7-187">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="e06c7-188">V tomto případě `Resource` je vlastnost instancí třídy <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="e06c7-188">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="e06c7-189">Koncový bod se dá použít k testování základního prostředku, ke kterému se právě směrujete.</span><span class="sxs-lookup"><span data-stu-id="e06c7-189">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="e06c7-190">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e06c7-190">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="e06c7-191">Koncový bod neposkytuje přístup k aktuálnímu `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="e06c7-191">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="e06c7-192">Při použití směrování koncových bodů použijte `IHttpContextAcessor` pro přístup k `HttpContext` obslužné rutině autorizace.</span><span class="sxs-lookup"><span data-stu-id="e06c7-192">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="e06c7-193">Další informace naleznete v tématu [použití vlastnosti HttpContext z vlastních komponent](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="e06c7-193">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="e06c7-194">V případě tradičního směrování nebo když k autorizaci dojde jako součást autorizačního filtru MVC, hodnota `Resource` je <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span><span class="sxs-lookup"><span data-stu-id="e06c7-194">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="e06c7-195">Tato vlastnost poskytuje přístup k `HttpContext` , `RouteData` a vše ostatní poskytované MVC a Razor stránkami.</span><span class="sxs-lookup"><span data-stu-id="e06c7-195">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="e06c7-196">Použití `Resource` vlastnosti je specifické pro rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e06c7-196">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="e06c7-197">Použití informací v této `Resource` vlastnosti omezuje vaše zásady autorizace na konkrétní architektury.</span><span class="sxs-lookup"><span data-stu-id="e06c7-197">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="e06c7-198">Vlastnost byste měli přetypovat `Resource` pomocí `is` klíčového slova a pak potvrdit, že přetypování bylo úspěšné, aby `InvalidCastException` při spuštění v jiných rozhraních nedošlo k chybě kódu.</span><span class="sxs-lookup"><span data-stu-id="e06c7-198">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e06c7-199">V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady.</span><span class="sxs-lookup"><span data-stu-id="e06c7-199">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="e06c7-200">Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu.</span><span class="sxs-lookup"><span data-stu-id="e06c7-200">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="e06c7-201">Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.</span><span class="sxs-lookup"><span data-stu-id="e06c7-201">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="e06c7-202">Zásady autorizace se skládají z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="e06c7-202">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="e06c7-203">Je zaregistrován jako součást konfigurace autorizační služby v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="e06c7-203">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="e06c7-204">V předchozím příkladu se vytvoří zásada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="e06c7-204">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="e06c7-205">Má jeden požadavek na &mdash; Minimální stáří, které je zadáno jako parametr požadavku.</span><span class="sxs-lookup"><span data-stu-id="e06c7-205">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="e06c7-206">Načetl služby IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="e06c7-206">IAuthorizationService</span></span> 

<span data-ttu-id="e06c7-207">Primární služba, která určuje, jestli je autorizace úspěšná, je <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="e06c7-207">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="e06c7-208">Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="e06c7-208">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="e06c7-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda autorizace je úspěšná.</span><span class="sxs-lookup"><span data-stu-id="e06c7-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="e06c7-210">Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> je zodpovědný za kontrolu, zda jsou splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="e06c7-210">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

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

<span data-ttu-id="e06c7-211"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>Třída je to, co obslužná rutina používá k označení, zda byly splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="e06c7-211">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="e06c7-212">Následující kód zobrazuje zjednodušenou (a poznámkovou poznámku) výchozí implementaci autorizační služby:</span><span class="sxs-lookup"><span data-stu-id="e06c7-212">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="e06c7-213">Následující kód ukazuje typický `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e06c7-213">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="e06c7-214">Použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]` k autorizaci.</span><span class="sxs-lookup"><span data-stu-id="e06c7-214">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="e06c7-215">Použití zásad u řadičů MVC</span><span class="sxs-lookup"><span data-stu-id="e06c7-215">Apply policies to MVC controllers</span></span>

<span data-ttu-id="e06c7-216">Pokud používáte Razor stránky, přečtěte si téma [použití zásad na Razor stránkách](#apply-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="e06c7-216">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="e06c7-217">Zásady se aplikují na řadiče pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="e06c7-217">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e06c7-218">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e06c7-218">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="e06c7-219">Použít zásady na Razor stránky</span><span class="sxs-lookup"><span data-stu-id="e06c7-219">Apply policies to Razor Pages</span></span>

<span data-ttu-id="e06c7-220">Zásady se aplikují na Razor stránky pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="e06c7-220">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e06c7-221">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e06c7-221">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="e06c7-222">Zásady se dají na stránkách použít taky Razor pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="e06c7-222">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="e06c7-223">Požadavky</span><span class="sxs-lookup"><span data-stu-id="e06c7-223">Requirements</span></span>

<span data-ttu-id="e06c7-224">Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="e06c7-224">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="e06c7-225">V našich zásadách "AtLeast21" je požadavek jedním parametrem &mdash; Minimální stáří.</span><span class="sxs-lookup"><span data-stu-id="e06c7-225">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="e06c7-226">Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky.</span><span class="sxs-lookup"><span data-stu-id="e06c7-226">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="e06c7-227">Parametr minimálního stáří by mohl být implementován následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="e06c7-227">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="e06c7-228">Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="e06c7-228">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="e06c7-229">Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .</span><span class="sxs-lookup"><span data-stu-id="e06c7-229">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="e06c7-230">Požadavek nemusí mít data ani vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e06c7-230">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="e06c7-231">Obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="e06c7-231">Authorization handlers</span></span>

<span data-ttu-id="e06c7-232">Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku.</span><span class="sxs-lookup"><span data-stu-id="e06c7-232">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="e06c7-233">Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.</span><span class="sxs-lookup"><span data-stu-id="e06c7-233">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="e06c7-234">Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="e06c7-234">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="e06c7-235">Obslužná rutina může [Zdědit \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je požadavek, který má být zpracován.</span><span class="sxs-lookup"><span data-stu-id="e06c7-235">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="e06c7-236">Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.</span><span class="sxs-lookup"><span data-stu-id="e06c7-236">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="e06c7-237">Použití obslužné rutiny pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="e06c7-237">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="e06c7-238">Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="e06c7-238">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="e06c7-239">Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem.</span><span class="sxs-lookup"><span data-stu-id="e06c7-239">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="e06c7-240">K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol.</span><span class="sxs-lookup"><span data-stu-id="e06c7-240">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="e06c7-241">Pokud je k dispozici deklarace identity, počítá se věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="e06c7-241">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="e06c7-242">Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou.</span><span class="sxs-lookup"><span data-stu-id="e06c7-242">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="e06c7-243">Po úspěšném ověření `context.Succeed` je vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.</span><span class="sxs-lookup"><span data-stu-id="e06c7-243">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="e06c7-244">Použití obslužné rutiny pro více požadavků</span><span class="sxs-lookup"><span data-stu-id="e06c7-244">Use a handler for multiple requirements</span></span>

<span data-ttu-id="e06c7-245">Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="e06c7-245">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="e06c7-246">Předchozí průchod kódu [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; vlastnost obsahující požadavky, které nejsou označeny jako úspěšné.</span><span class="sxs-lookup"><span data-stu-id="e06c7-246">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="e06c7-247">Pro `ReadPermission` přístup k požadovanému prostředku musí být uživatel buď vlastník, nebo sponzor.</span><span class="sxs-lookup"><span data-stu-id="e06c7-247">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="e06c7-248">V případě `EditPermission` `DeletePermission` požadavku nebo musí být vlastníkem přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="e06c7-248">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="e06c7-249">Registrace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="e06c7-249">Handler registration</span></span>

<span data-ttu-id="e06c7-250">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="e06c7-250">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="e06c7-251">Příklad:</span><span class="sxs-lookup"><span data-stu-id="e06c7-251">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="e06c7-252">Předchozí kód se registruje `MinimumAgeHandler` jako typ singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="e06c7-252">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="e06c7-253">Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="e06c7-253">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="e06c7-254">Co by měl obslužná rutina vrátit?</span><span class="sxs-lookup"><span data-stu-id="e06c7-254">What should a handler return?</span></span>

<span data-ttu-id="e06c7-255">Všimněte si, že `Handle` metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="e06c7-255">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="e06c7-256">Jak se uvádí stav buď úspěch, nebo neúspěch?</span><span class="sxs-lookup"><span data-stu-id="e06c7-256">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="e06c7-257">Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)` a předáním požadavku, který byl úspěšně ověřen.</span><span class="sxs-lookup"><span data-stu-id="e06c7-257">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="e06c7-258">Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="e06c7-258">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="e06c7-259">Chcete-li zaručit selhání i v případě úspěchu dalších obslužných rutin požadavků, zavolejte `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="e06c7-259">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="e06c7-260">Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail` , všechny ostatní obslužné rutiny jsou stále volány.</span><span class="sxs-lookup"><span data-stu-id="e06c7-260">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="e06c7-261">To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek.</span><span class="sxs-lookup"><span data-stu-id="e06c7-261">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="e06c7-262">Když je nastavena na `false` , vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy, kdy je volána spuštění obslužných rutin `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="e06c7-262">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="e06c7-263">`InvokeHandlersAfterFailure`Výchozí hodnota – v takovém `true` případě jsou volány všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="e06c7-263">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="e06c7-264">Obslužné rutiny autorizace jsou volány i v případě, že ověřování selhává.</span><span class="sxs-lookup"><span data-stu-id="e06c7-264">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="e06c7-265">Proč bych chtěl více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="e06c7-265">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="e06c7-266">V případech, kdy chcete, aby vyhodnocení bylo na **nebo** bázi, implementujte více obslužných rutin pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="e06c7-266">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="e06c7-267">Například Microsoft má dveře, které se otevírají jenom s kartami Key.</span><span class="sxs-lookup"><span data-stu-id="e06c7-267">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="e06c7-268">Pokud ponecháte kartu klíče na domovské stránce, recepční vytiskne dočasný štítek a otevře dvířka za vás.</span><span class="sxs-lookup"><span data-stu-id="e06c7-268">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="e06c7-269">V tomto scénáři byste měli jeden požadavek, *BuildingEntry*, ale několik obslužných rutin, každý z nich prozkoumá jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="e06c7-269">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="e06c7-270">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="e06c7-270">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="e06c7-271">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e06c7-271">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="e06c7-272">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e06c7-272">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="e06c7-273">Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="e06c7-273">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="e06c7-274">Pokud obslužná rutina uspěje, když zásada vyhodnotí `BuildingEntryRequirement` , vyhodnocení zásad je úspěšné.</span><span class="sxs-lookup"><span data-stu-id="e06c7-274">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="e06c7-275">Použití funkce ke splnění zásad</span><span class="sxs-lookup"><span data-stu-id="e06c7-275">Use a func to fulfill a policy</span></span>

<span data-ttu-id="e06c7-276">Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="e06c7-276">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="e06c7-277">Je možné dodat a `Func<AuthorizationHandlerContext, bool>` nakonfigurovat zásadu pomocí `RequireAssertion` Tvůrce zásad.</span><span class="sxs-lookup"><span data-stu-id="e06c7-277">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="e06c7-278">Například předchozí `BadgeEntryHandler` může být přepsána následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="e06c7-278">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="e06c7-279">Přístup k kontextu požadavku MVC v obslužných rutinách</span><span class="sxs-lookup"><span data-stu-id="e06c7-279">Access MVC request context in handlers</span></span>

<span data-ttu-id="e06c7-280">`HandleRequirementAsync`Metoda, kterou implementujete v obslužné rutině autorizace, má dva parametry: `AuthorizationHandlerContext` a, `TRequirement` kterou zpracováváte.</span><span class="sxs-lookup"><span data-stu-id="e06c7-280">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="e06c7-281">Rozhraní, jako je MVC, nebo SignalR jsou volná pro přidání libovolného objektu do `Resource` vlastnosti v `AuthorizationHandlerContext` pro předání dalších informací.</span><span class="sxs-lookup"><span data-stu-id="e06c7-281">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="e06c7-282">MVC například projde instanci [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) ve `Resource` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e06c7-282">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="e06c7-283">Tato vlastnost poskytuje přístup k `HttpContext` , `RouteData` a vše ostatní poskytované MVC a Razor stránkami.</span><span class="sxs-lookup"><span data-stu-id="e06c7-283">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="e06c7-284">Použití `Resource` vlastnosti je specifické pro rozhraní.</span><span class="sxs-lookup"><span data-stu-id="e06c7-284">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="e06c7-285">Použití informací v této `Resource` vlastnosti omezuje vaše zásady autorizace na konkrétní architektury.</span><span class="sxs-lookup"><span data-stu-id="e06c7-285">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="e06c7-286">Vlastnost byste měli přetypovat `Resource` pomocí `is` klíčového slova a pak potvrdit, že přetypování bylo úspěšné, aby `InvalidCastException` při spuštění v jiných rozhraních nedošlo k chybě kódu.</span><span class="sxs-lookup"><span data-stu-id="e06c7-286">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end

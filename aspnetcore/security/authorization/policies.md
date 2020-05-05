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
ms.openlocfilehash: 3b6fcef91355bf22e5aa185652d9489a44998db0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777498"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="a7b7a-103">Ověřování na základě zásad v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7b7a-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a7b7a-104">V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="a7b7a-105">Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="a7b7a-106">Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="a7b7a-107">Zásady autorizace se skládají z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="a7b7a-108">Je zaregistrován jako součást konfigurace autorizační služby v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="a7b7a-109">V předchozím příkladu se vytvoří zásada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="a7b7a-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="a7b7a-110">Má jeden požadavek&mdash;na minimální stáří, které je zadáno jako parametr požadavku.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="a7b7a-111">Načetl služby IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="a7b7a-111">IAuthorizationService</span></span> 

<span data-ttu-id="a7b7a-112">Primární služba, která určuje, jestli je autorizace úspěšná <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>, je:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="a7b7a-113">Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="a7b7a-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="a7b7a-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda autorizace je úspěšná.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="a7b7a-115">Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> je zodpovědný za kontrolu, zda jsou splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="a7b7a-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Třída je to, co obslužná rutina používá k označení, zda byly splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="a7b7a-117">Následující kód zobrazuje zjednodušenou (a poznámkovou poznámku) výchozí implementaci autorizační služby:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="a7b7a-118">Následující kód ukazuje typický `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="a7b7a-119">Použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]` k autorizaci.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="a7b7a-120">Použití zásad u řadičů MVC</span><span class="sxs-lookup"><span data-stu-id="a7b7a-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="a7b7a-121">Pokud používáte Razor stránky, přečtěte si téma [použití zásad Razor na stránky](#applying-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="a7b7a-122">Zásady se aplikují na řadiče pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="a7b7a-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="a7b7a-124">Použití zásad na Razor stránky</span><span class="sxs-lookup"><span data-stu-id="a7b7a-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="a7b7a-125">Zásady se aplikují Razor na stránky pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="a7b7a-126">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="a7b7a-127">Zásady se dají na Razor stránkách použít taky pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="a7b7a-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="a7b7a-128">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a7b7a-128">Requirements</span></span>

<span data-ttu-id="a7b7a-129">Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="a7b7a-130">V našich zásadách "AtLeast21" je požadavek jedním parametrem&mdash;minimální stáří.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="a7b7a-131">Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="a7b7a-132">Parametr minimálního stáří by mohl být implementován následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="a7b7a-133">Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="a7b7a-134">Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .</span><span class="sxs-lookup"><span data-stu-id="a7b7a-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="a7b7a-135">Požadavek nemusí mít data ani vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="a7b7a-136">Obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="a7b7a-136">Authorization handlers</span></span>

<span data-ttu-id="a7b7a-137">Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="a7b7a-138">Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="a7b7a-139">Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="a7b7a-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="a7b7a-140">Obslužná rutina může [dědit\<AuthorizationHandler TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), `TRequirement` kde je požadavek, který má být zpracován.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="a7b7a-141">Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="a7b7a-142">Použití obslužné rutiny pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="a7b7a-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="a7b7a-143">Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="a7b7a-144">Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="a7b7a-145">K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="a7b7a-146">Pokud je k dispozici deklarace identity, počítá se věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="a7b7a-147">Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="a7b7a-148">Po úspěšném ověření `context.Succeed` je vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="a7b7a-149">Použití obslužné rutiny pro více požadavků</span><span class="sxs-lookup"><span data-stu-id="a7b7a-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="a7b7a-150">Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="a7b7a-151">Předchozí průchod kódu [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky, které nejsou označeny jako úspěšné.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="a7b7a-152">`ReadPermission` Pro přístup k požadovanému prostředku musí být uživatel buď vlastník, nebo sponzor.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="a7b7a-153">V případě požadavku `EditPermission` nebo `DeletePermission` musí být vlastníkem přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="a7b7a-154">Registrace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="a7b7a-154">Handler registration</span></span>

<span data-ttu-id="a7b7a-155">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="a7b7a-156">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="a7b7a-157">Předchozí kód se registruje `MinimumAgeHandler` jako typ singleton vyvoláním. `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`</span><span class="sxs-lookup"><span data-stu-id="a7b7a-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="a7b7a-158">Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="a7b7a-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="a7b7a-159">Co by měl obslužná rutina vrátit?</span><span class="sxs-lookup"><span data-stu-id="a7b7a-159">What should a handler return?</span></span>

<span data-ttu-id="a7b7a-160">Všimněte si, `Handle` že metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="a7b7a-161">Jak se uvádí stav buď úspěch, nebo neúspěch?</span><span class="sxs-lookup"><span data-stu-id="a7b7a-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="a7b7a-162">Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)`a předáním požadavku, který byl úspěšně ověřen.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="a7b7a-163">Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="a7b7a-164">Chcete-li zaručit selhání i v případě úspěchu dalších obslužných `context.Fail`rutin požadavků, zavolejte.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="a7b7a-165">Pokud obslužná rutina `context.Succeed` volá `context.Fail`nebo, všechny ostatní obslužné rutiny jsou stále volány.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="a7b7a-166">To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="a7b7a-167">Když je nastavena `false`na, vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy, kdy `context.Fail` je volána spuštění obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="a7b7a-168">`InvokeHandlersAfterFailure``true`výchozí hodnota – v takovém případě jsou volány všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="a7b7a-169">Obslužné rutiny autorizace jsou volány i v případě, že ověřování selhává.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="a7b7a-170">Proč bych chtěl více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="a7b7a-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="a7b7a-171">V případech, kdy chcete, aby vyhodnocení bylo na **nebo** bázi, implementujte více obslužných rutin pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="a7b7a-172">Například Microsoft má dveře, které se otevírají jenom s kartami Key.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="a7b7a-173">Pokud ponecháte kartu klíče na domovské stránce, recepční vytiskne dočasný štítek a otevře dvířka za vás.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="a7b7a-174">V tomto scénáři byste měli jeden požadavek, *BuildingEntry*, ale několik obslužných rutin, každý z nich prozkoumá jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="a7b7a-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="a7b7a-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="a7b7a-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="a7b7a-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="a7b7a-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="a7b7a-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="a7b7a-178">Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="a7b7a-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="a7b7a-179">Pokud obslužná rutina uspěje `BuildingEntryRequirement`, když zásada vyhodnotí, vyhodnocení zásad je úspěšné.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="a7b7a-180">Splnění zásad pomocí funkce</span><span class="sxs-lookup"><span data-stu-id="a7b7a-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="a7b7a-181">Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="a7b7a-182">Je možné dodat a `Func<AuthorizationHandlerContext, bool>` nakonfigurovat zásadu pomocí Tvůrce `RequireAssertion` zásad.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="a7b7a-183">Například předchozí `BadgeEntryHandler` může být přepsána následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="a7b7a-184">Přístup k kontextu požadavku MVC v obslužných rutinách</span><span class="sxs-lookup"><span data-stu-id="a7b7a-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="a7b7a-185">`HandleRequirementAsync` Metoda, kterou implementujete v obslužné rutině autorizace, má dva `AuthorizationHandlerContext` parametry: `TRequirement` a, kterou zpracováváte.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="a7b7a-186">Architektury, jako je MVC nebo Jabbr, jsou volné pro přidání libovolného objektu do `Resource` vlastnosti v, `AuthorizationHandlerContext` aby bylo možné předávat Další informace.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="a7b7a-187">MVC například projde instanci [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) ve `Resource` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="a7b7a-188">Tato vlastnost poskytuje přístup k `HttpContext`, `RouteData`a vše ostatní poskytované MVC a Razor stránkami.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="a7b7a-189">Použití `Resource` vlastnosti je specifické pro rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="a7b7a-190">Použití informací v této `Resource` vlastnosti omezuje vaše zásady autorizace na konkrétní architektury.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="a7b7a-191">`Resource` Vlastnost byste měli přetypovat pomocí `is` klíčového slova a pak potvrdit, že přetypování bylo úspěšné, aby při spuštění v jiných rozhraních nedošlo `InvalidCastException` k chybě kódu.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="a7b7a-192">V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="a7b7a-193">Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="a7b7a-194">Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="a7b7a-195">Zásady autorizace se skládají z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="a7b7a-196">Je zaregistrován jako součást konfigurace autorizační služby v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="a7b7a-197">V předchozím příkladu se vytvoří zásada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="a7b7a-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="a7b7a-198">Má jeden požadavek&mdash;na minimální stáří, které je zadáno jako parametr požadavku.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="a7b7a-199">Načetl služby IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="a7b7a-199">IAuthorizationService</span></span> 

<span data-ttu-id="a7b7a-200">Primární služba, která určuje, jestli je autorizace úspěšná <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>, je:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="a7b7a-201">Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="a7b7a-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="a7b7a-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda autorizace je úspěšná.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="a7b7a-203">Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> je zodpovědný za kontrolu, zda jsou splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="a7b7a-204"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Třída je to, co obslužná rutina používá k označení, zda byly splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="a7b7a-205">Následující kód zobrazuje zjednodušenou (a poznámkovou poznámku) výchozí implementaci autorizační služby:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="a7b7a-206">Následující kód ukazuje typický `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="a7b7a-207">Použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]` k autorizaci.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="a7b7a-208">Použití zásad u řadičů MVC</span><span class="sxs-lookup"><span data-stu-id="a7b7a-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="a7b7a-209">Pokud používáte Razor stránky, přečtěte si téma [použití zásad Razor na stránky](#applying-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="a7b7a-210">Zásady se aplikují na řadiče pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="a7b7a-211">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="a7b7a-212">Použití zásad na Razor stránky</span><span class="sxs-lookup"><span data-stu-id="a7b7a-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="a7b7a-213">Zásady se aplikují Razor na stránky pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="a7b7a-214">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="a7b7a-215">Zásady se dají na Razor stránkách použít taky pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="a7b7a-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="a7b7a-216">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a7b7a-216">Requirements</span></span>

<span data-ttu-id="a7b7a-217">Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="a7b7a-218">V našich zásadách "AtLeast21" je požadavek jedním parametrem&mdash;minimální stáří.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="a7b7a-219">Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="a7b7a-220">Parametr minimálního stáří by mohl být implementován následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="a7b7a-221">Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="a7b7a-222">Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .</span><span class="sxs-lookup"><span data-stu-id="a7b7a-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="a7b7a-223">Požadavek nemusí mít data ani vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="a7b7a-224">Obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="a7b7a-224">Authorization handlers</span></span>

<span data-ttu-id="a7b7a-225">Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="a7b7a-226">Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="a7b7a-227">Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="a7b7a-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="a7b7a-228">Obslužná rutina může [dědit\<AuthorizationHandler TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), `TRequirement` kde je požadavek, který má být zpracován.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="a7b7a-229">Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="a7b7a-230">Použití obslužné rutiny pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="a7b7a-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="a7b7a-231">Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="a7b7a-232">Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="a7b7a-233">K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="a7b7a-234">Pokud je k dispozici deklarace identity, počítá se věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="a7b7a-235">Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="a7b7a-236">Po úspěšném ověření `context.Succeed` je vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="a7b7a-237">Použití obslužné rutiny pro více požadavků</span><span class="sxs-lookup"><span data-stu-id="a7b7a-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="a7b7a-238">Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="a7b7a-239">Předchozí průchod kódu [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky, které nejsou označeny jako úspěšné.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="a7b7a-240">`ReadPermission` Pro přístup k požadovanému prostředku musí být uživatel buď vlastník, nebo sponzor.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="a7b7a-241">V případě požadavku `EditPermission` nebo `DeletePermission` musí být vlastníkem přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="a7b7a-242">Registrace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="a7b7a-242">Handler registration</span></span>

<span data-ttu-id="a7b7a-243">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="a7b7a-244">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="a7b7a-245">Předchozí kód se registruje `MinimumAgeHandler` jako typ singleton vyvoláním. `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`</span><span class="sxs-lookup"><span data-stu-id="a7b7a-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="a7b7a-246">Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="a7b7a-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="a7b7a-247">Co by měl obslužná rutina vrátit?</span><span class="sxs-lookup"><span data-stu-id="a7b7a-247">What should a handler return?</span></span>

<span data-ttu-id="a7b7a-248">Všimněte si, `Handle` že metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="a7b7a-249">Jak se uvádí stav buď úspěch, nebo neúspěch?</span><span class="sxs-lookup"><span data-stu-id="a7b7a-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="a7b7a-250">Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)`a předáním požadavku, který byl úspěšně ověřen.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="a7b7a-251">Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="a7b7a-252">Chcete-li zaručit selhání i v případě úspěchu dalších obslužných `context.Fail`rutin požadavků, zavolejte.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="a7b7a-253">Pokud obslužná rutina `context.Succeed` volá `context.Fail`nebo, všechny ostatní obslužné rutiny jsou stále volány.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="a7b7a-254">To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="a7b7a-255">Když je nastavena `false`na, vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy, kdy `context.Fail` je volána spuštění obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="a7b7a-256">`InvokeHandlersAfterFailure``true`výchozí hodnota – v takovém případě jsou volány všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="a7b7a-257">Obslužné rutiny autorizace jsou volány i v případě, že ověřování selhává.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="a7b7a-258">Proč bych chtěl více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="a7b7a-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="a7b7a-259">V případech, kdy chcete, aby vyhodnocení bylo na **nebo** bázi, implementujte více obslužných rutin pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="a7b7a-260">Například Microsoft má dveře, které se otevírají jenom s kartami Key.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="a7b7a-261">Pokud ponecháte kartu klíče na domovské stránce, recepční vytiskne dočasný štítek a otevře dvířka za vás.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="a7b7a-262">V tomto scénáři byste měli jeden požadavek, *BuildingEntry*, ale několik obslužných rutin, každý z nich prozkoumá jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="a7b7a-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="a7b7a-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="a7b7a-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="a7b7a-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="a7b7a-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="a7b7a-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="a7b7a-266">Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="a7b7a-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="a7b7a-267">Pokud obslužná rutina uspěje `BuildingEntryRequirement`, když zásada vyhodnotí, vyhodnocení zásad je úspěšné.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="a7b7a-268">Splnění zásad pomocí funkce</span><span class="sxs-lookup"><span data-stu-id="a7b7a-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="a7b7a-269">Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="a7b7a-270">Je možné dodat a `Func<AuthorizationHandlerContext, bool>` nakonfigurovat zásadu pomocí Tvůrce `RequireAssertion` zásad.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="a7b7a-271">Například předchozí `BadgeEntryHandler` může být přepsána následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="a7b7a-272">Přístup k kontextu požadavku MVC v obslužných rutinách</span><span class="sxs-lookup"><span data-stu-id="a7b7a-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="a7b7a-273">`HandleRequirementAsync` Metoda, kterou implementujete v obslužné rutině autorizace, má dva `AuthorizationHandlerContext` parametry: `TRequirement` a, kterou zpracováváte.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="a7b7a-274">Rozhraní, jako je MVC, SignalR nebo jsou volná pro přidání libovolného objektu `Resource` do vlastnosti v `AuthorizationHandlerContext` pro předání dalších informací.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="a7b7a-275">Při použití směrování koncových bodů se autorizaci obvykle zpracovává pomocí middleware autorizace.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="a7b7a-276">V tomto případě je `Resource` vlastnost instancí třídy. <xref:Microsoft.AspNetCore.Http.Endpoint></span><span class="sxs-lookup"><span data-stu-id="a7b7a-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="a7b7a-277">Koncový bod se dá použít k testování základního prostředku, na který se chystáte směrovat.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="a7b7a-278">Příklad:</span><span class="sxs-lookup"><span data-stu-id="a7b7a-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="a7b7a-279">V případě tradičního směrování nebo když k autorizaci dojde jako součást autorizačního filtru MVC, hodnota `Resource` je <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="a7b7a-280">Tato vlastnost poskytuje přístup k `HttpContext`, `RouteData`a vše ostatní poskytované MVC a Razor stránkami.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="a7b7a-281">Použití `Resource` vlastnosti je specifické pro rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="a7b7a-282">Použití informací v této `Resource` vlastnosti omezuje vaše zásady autorizace na konkrétní architektury.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="a7b7a-283">`Resource` Vlastnost byste měli přetypovat pomocí `is` klíčového slova a pak potvrdit, že přetypování bylo úspěšné, aby při spuštění v jiných rozhraních nedošlo `InvalidCastException` k chybě kódu.</span><span class="sxs-lookup"><span data-stu-id="a7b7a-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end

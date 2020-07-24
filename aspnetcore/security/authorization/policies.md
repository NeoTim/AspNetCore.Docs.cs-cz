---
title: Ověřování na základě zásad v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet a používat obslužné rutiny zásad autorizace pro vynucování autorizačních požadavků v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/policies
ms.openlocfilehash: 668c68bc328860ef17e1f2df09103fca07733ef7
ms.sourcegitcommit: 1b89fc58114a251926abadfd5c69c120f1ba12d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87160171"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="85061-103">Ověřování na základě zásad v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85061-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="85061-104">V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady.</span><span class="sxs-lookup"><span data-stu-id="85061-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="85061-105">Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu.</span><span class="sxs-lookup"><span data-stu-id="85061-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="85061-106">Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.</span><span class="sxs-lookup"><span data-stu-id="85061-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="85061-107">Zásady autorizace se skládají z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="85061-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="85061-108">Je zaregistrován jako součást konfigurace autorizační služby v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="85061-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="85061-109">V předchozím příkladu se vytvoří zásada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="85061-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="85061-110">Má jeden požadavek na &mdash; Minimální stáří, které je zadáno jako parametr požadavku.</span><span class="sxs-lookup"><span data-stu-id="85061-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="85061-111">Načetl služby IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="85061-111">IAuthorizationService</span></span> 

<span data-ttu-id="85061-112">Primární služba, která určuje, jestli je autorizace úspěšná, je <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="85061-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="85061-113">Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="85061-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="85061-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda autorizace je úspěšná.</span><span class="sxs-lookup"><span data-stu-id="85061-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="85061-115">Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> je zodpovědný za kontrolu, zda jsou splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="85061-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="85061-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>Třída je to, co obslužná rutina používá k označení, zda byly splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="85061-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="85061-117">Následující kód zobrazuje zjednodušenou (a poznámkovou poznámku) výchozí implementaci autorizační služby:</span><span class="sxs-lookup"><span data-stu-id="85061-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="85061-118">Následující kód ukazuje typický `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="85061-118">The following code shows a typical `ConfigureServices`:</span></span>

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
    services.Add:::no-loc(Razor):::Pages();
}
```

<span data-ttu-id="85061-119">Použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]` k autorizaci.</span><span class="sxs-lookup"><span data-stu-id="85061-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="85061-120">Použití zásad u řadičů MVC</span><span class="sxs-lookup"><span data-stu-id="85061-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="85061-121">Pokud používáte :::no-loc(Razor)::: stránky, přečtěte si téma [použití zásad na :::no-loc(Razor)::: stránkách](#apply-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="85061-121">If you're using :::no-loc(Razor)::: Pages, see [Apply policies to :::no-loc(Razor)::: Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="85061-122">Zásady se aplikují na řadiče pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="85061-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="85061-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="85061-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="85061-124">Použít zásady na :::no-loc(Razor)::: stránky</span><span class="sxs-lookup"><span data-stu-id="85061-124">Apply policies to :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="85061-125">Zásady se aplikují na :::no-loc(Razor)::: stránky pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="85061-125">Policies are applied to :::no-loc(Razor)::: Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="85061-126">Příklad:</span><span class="sxs-lookup"><span data-stu-id="85061-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="85061-127">Zásady nelze ***použít*** na :::no-loc(Razor)::: úrovni obslužné rutiny stránky, musejí být aplikovány na stránku.</span><span class="sxs-lookup"><span data-stu-id="85061-127">Policies can ***not*** be applied at the :::no-loc(Razor)::: Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="85061-128">Zásady lze použít na :::no-loc(Razor)::: stránky pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="85061-128">Policies can be applied to :::no-loc(Razor)::: Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="85061-129">Požadavky</span><span class="sxs-lookup"><span data-stu-id="85061-129">Requirements</span></span>

<span data-ttu-id="85061-130">Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="85061-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="85061-131">V našich zásadách "AtLeast21" je požadavek jedním parametrem &mdash; Minimální stáří.</span><span class="sxs-lookup"><span data-stu-id="85061-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="85061-132">Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky.</span><span class="sxs-lookup"><span data-stu-id="85061-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="85061-133">Parametr minimálního stáří by mohl být implementován následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="85061-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="85061-134">Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="85061-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="85061-135">Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .</span><span class="sxs-lookup"><span data-stu-id="85061-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="85061-136">Požadavek nemusí mít data ani vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="85061-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="85061-137">Obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="85061-137">Authorization handlers</span></span>

<span data-ttu-id="85061-138">Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku.</span><span class="sxs-lookup"><span data-stu-id="85061-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="85061-139">Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.</span><span class="sxs-lookup"><span data-stu-id="85061-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="85061-140">Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="85061-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="85061-141">Obslužná rutina může [Zdědit \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je požadavek, který má být zpracován.</span><span class="sxs-lookup"><span data-stu-id="85061-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="85061-142">Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.</span><span class="sxs-lookup"><span data-stu-id="85061-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="85061-143">Použití obslužné rutiny pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="85061-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="85061-144">Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="85061-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="85061-145">Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem.</span><span class="sxs-lookup"><span data-stu-id="85061-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="85061-146">K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol.</span><span class="sxs-lookup"><span data-stu-id="85061-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="85061-147">Pokud je k dispozici deklarace identity, počítá se věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="85061-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="85061-148">Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou.</span><span class="sxs-lookup"><span data-stu-id="85061-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="85061-149">Po úspěšném ověření `context.Succeed` je vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.</span><span class="sxs-lookup"><span data-stu-id="85061-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="85061-150">Použití obslužné rutiny pro více požadavků</span><span class="sxs-lookup"><span data-stu-id="85061-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="85061-151">Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="85061-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="85061-152">Předchozí průchod kódu [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; vlastnost obsahující požadavky, které nejsou označeny jako úspěšné.</span><span class="sxs-lookup"><span data-stu-id="85061-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="85061-153">Pro `ReadPermission` přístup k požadovanému prostředku musí být uživatel buď vlastník, nebo sponzor.</span><span class="sxs-lookup"><span data-stu-id="85061-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="85061-154">V případě `EditPermission` `DeletePermission` požadavku nebo musí být vlastníkem přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="85061-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="85061-155">Registrace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="85061-155">Handler registration</span></span>

<span data-ttu-id="85061-156">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="85061-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="85061-157">Příklad:</span><span class="sxs-lookup"><span data-stu-id="85061-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="85061-158">Předchozí kód se registruje `MinimumAgeHandler` jako typ singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="85061-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="85061-159">Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="85061-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="85061-160">Co by měl obslužná rutina vrátit?</span><span class="sxs-lookup"><span data-stu-id="85061-160">What should a handler return?</span></span>

<span data-ttu-id="85061-161">Všimněte si, že `Handle` metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="85061-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="85061-162">Jak se uvádí stav buď úspěch, nebo neúspěch?</span><span class="sxs-lookup"><span data-stu-id="85061-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="85061-163">Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)` a předáním požadavku, který byl úspěšně ověřen.</span><span class="sxs-lookup"><span data-stu-id="85061-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="85061-164">Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="85061-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="85061-165">Chcete-li zaručit selhání i v případě úspěchu dalších obslužných rutin požadavků, zavolejte `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="85061-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="85061-166">Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail` , všechny ostatní obslužné rutiny jsou stále volány.</span><span class="sxs-lookup"><span data-stu-id="85061-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="85061-167">To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek.</span><span class="sxs-lookup"><span data-stu-id="85061-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="85061-168">Když je nastavena na `false` , vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy, kdy je volána spuštění obslužných rutin `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="85061-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="85061-169">`InvokeHandlersAfterFailure`Výchozí hodnota – v takovém `true` případě jsou volány všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="85061-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="85061-170">Obslužné rutiny autorizace jsou volány i v případě, že ověřování selhává.</span><span class="sxs-lookup"><span data-stu-id="85061-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="85061-171">Proč bych chtěl více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="85061-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="85061-172">V případech, kdy chcete, aby vyhodnocení bylo na **nebo** bázi, implementujte více obslužných rutin pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="85061-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="85061-173">Například Microsoft má dveře, které se otevírají jenom s kartami Key.</span><span class="sxs-lookup"><span data-stu-id="85061-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="85061-174">Pokud ponecháte kartu klíče na domovské stránce, recepční vytiskne dočasný štítek a otevře dvířka za vás.</span><span class="sxs-lookup"><span data-stu-id="85061-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="85061-175">V tomto scénáři byste měli jeden požadavek, *BuildingEntry*, ale několik obslužných rutin, každý z nich prozkoumá jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="85061-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="85061-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="85061-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="85061-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="85061-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="85061-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="85061-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="85061-179">Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="85061-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="85061-180">Pokud obslužná rutina uspěje, když zásada vyhodnotí `BuildingEntryRequirement` , vyhodnocení zásad je úspěšné.</span><span class="sxs-lookup"><span data-stu-id="85061-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="85061-181">Použití funkce ke splnění zásad</span><span class="sxs-lookup"><span data-stu-id="85061-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="85061-182">Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="85061-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="85061-183">Je možné dodat a `Func<AuthorizationHandlerContext, bool>` nakonfigurovat zásadu pomocí `RequireAssertion` Tvůrce zásad.</span><span class="sxs-lookup"><span data-stu-id="85061-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="85061-184">Například předchozí `BadgeEntryHandler` může být přepsána následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="85061-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="85061-185">Přístup k kontextu požadavku MVC v obslužných rutinách</span><span class="sxs-lookup"><span data-stu-id="85061-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="85061-186">`HandleRequirementAsync`Metoda, kterou implementujete v obslužné rutině autorizace, má dva parametry: `AuthorizationHandlerContext` a, `TRequirement` kterou zpracováváte.</span><span class="sxs-lookup"><span data-stu-id="85061-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="85061-187">Rozhraní, jako je MVC, nebo :::no-loc(SignalR)::: jsou volná pro přidání libovolného objektu do `Resource` vlastnosti v `AuthorizationHandlerContext` pro předání dalších informací.</span><span class="sxs-lookup"><span data-stu-id="85061-187">Frameworks such as MVC or :::no-loc(SignalR)::: are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="85061-188">Při použití směrování koncových bodů se autorizaci obvykle zpracovává pomocí middleware autorizace.</span><span class="sxs-lookup"><span data-stu-id="85061-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="85061-189">V tomto případě `Resource` je vlastnost instancí třídy <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="85061-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="85061-190">Koncový bod se dá použít k testování základního prostředku, ke kterému se právě směrujete.</span><span class="sxs-lookup"><span data-stu-id="85061-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="85061-191">Příklad:</span><span class="sxs-lookup"><span data-stu-id="85061-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="85061-192">Koncový bod neposkytuje přístup k aktuálnímu `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="85061-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="85061-193">Při použití směrování koncových bodů použijte `IHttpContextAcessor` pro přístup k `HttpContext` obslužné rutině autorizace.</span><span class="sxs-lookup"><span data-stu-id="85061-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="85061-194">Další informace naleznete v tématu [použití vlastnosti HttpContext z vlastních komponent](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="85061-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="85061-195">V případě tradičního směrování nebo když k autorizaci dojde jako součást autorizačního filtru MVC, hodnota `Resource` je <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span><span class="sxs-lookup"><span data-stu-id="85061-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="85061-196">Tato vlastnost poskytuje přístup k `HttpContext` , `RouteData` a vše ostatní poskytované MVC a :::no-loc(Razor)::: stránkami.</span><span class="sxs-lookup"><span data-stu-id="85061-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="85061-197">Použití `Resource` vlastnosti je specifické pro rozhraní.</span><span class="sxs-lookup"><span data-stu-id="85061-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="85061-198">Použití informací v této `Resource` vlastnosti omezuje vaše zásady autorizace na konkrétní architektury.</span><span class="sxs-lookup"><span data-stu-id="85061-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="85061-199">Vlastnost byste měli přetypovat `Resource` pomocí `is` klíčového slova a pak potvrdit, že přetypování bylo úspěšné, aby `InvalidCastException` při spuštění v jiných rozhraních nedošlo k chybě kódu.</span><span class="sxs-lookup"><span data-stu-id="85061-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="85061-200">Globálně vyžadovat ověření všech uživatelů</span><span class="sxs-lookup"><span data-stu-id="85061-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="85061-201">V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady.</span><span class="sxs-lookup"><span data-stu-id="85061-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="85061-202">Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu.</span><span class="sxs-lookup"><span data-stu-id="85061-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="85061-203">Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.</span><span class="sxs-lookup"><span data-stu-id="85061-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="85061-204">Zásady autorizace se skládají z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="85061-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="85061-205">Je zaregistrován jako součást konfigurace autorizační služby v `Startup.ConfigureServices` metodě:</span><span class="sxs-lookup"><span data-stu-id="85061-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="85061-206">V předchozím příkladu se vytvoří zásada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="85061-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="85061-207">Má jeden požadavek na &mdash; Minimální stáří, které je zadáno jako parametr požadavku.</span><span class="sxs-lookup"><span data-stu-id="85061-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="85061-208">Načetl služby IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="85061-208">IAuthorizationService</span></span> 

<span data-ttu-id="85061-209">Primární služba, která určuje, jestli je autorizace úspěšná, je <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="85061-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="85061-210">Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="85061-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="85061-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda autorizace je úspěšná.</span><span class="sxs-lookup"><span data-stu-id="85061-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="85061-212">Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> je zodpovědný za kontrolu, zda jsou splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="85061-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="85061-213"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>Třída je to, co obslužná rutina používá k označení, zda byly splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="85061-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="85061-214">Následující kód zobrazuje zjednodušenou (a poznámkovou poznámku) výchozí implementaci autorizační služby:</span><span class="sxs-lookup"><span data-stu-id="85061-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="85061-215">Následující kód ukazuje typický `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="85061-215">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="85061-216">Použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]` k autorizaci.</span><span class="sxs-lookup"><span data-stu-id="85061-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="85061-217">Použití zásad u řadičů MVC</span><span class="sxs-lookup"><span data-stu-id="85061-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="85061-218">Pokud používáte :::no-loc(Razor)::: stránky, přečtěte si téma [použití zásad na :::no-loc(Razor)::: stránkách](#apply-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="85061-218">If you're using :::no-loc(Razor)::: Pages, see [Apply policies to :::no-loc(Razor)::: Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="85061-219">Zásady se aplikují na řadiče pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="85061-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="85061-220">Příklad:</span><span class="sxs-lookup"><span data-stu-id="85061-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="85061-221">Použít zásady na :::no-loc(Razor)::: stránky</span><span class="sxs-lookup"><span data-stu-id="85061-221">Apply policies to :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="85061-222">Zásady se aplikují na :::no-loc(Razor)::: stránky pomocí `[Authorize]` atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="85061-222">Policies are applied to :::no-loc(Razor)::: Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="85061-223">Příklad:</span><span class="sxs-lookup"><span data-stu-id="85061-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="85061-224">Zásady se dají na stránkách použít taky :::no-loc(Razor)::: pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="85061-224">Policies can also be applied to :::no-loc(Razor)::: Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="85061-225">Požadavky</span><span class="sxs-lookup"><span data-stu-id="85061-225">Requirements</span></span>

<span data-ttu-id="85061-226">Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="85061-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="85061-227">V našich zásadách "AtLeast21" je požadavek jedním parametrem &mdash; Minimální stáří.</span><span class="sxs-lookup"><span data-stu-id="85061-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="85061-228">Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky.</span><span class="sxs-lookup"><span data-stu-id="85061-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="85061-229">Parametr minimálního stáří by mohl být implementován následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="85061-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="85061-230">Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="85061-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="85061-231">Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .</span><span class="sxs-lookup"><span data-stu-id="85061-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="85061-232">Požadavek nemusí mít data ani vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="85061-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="85061-233">Obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="85061-233">Authorization handlers</span></span>

<span data-ttu-id="85061-234">Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku.</span><span class="sxs-lookup"><span data-stu-id="85061-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="85061-235">Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.</span><span class="sxs-lookup"><span data-stu-id="85061-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="85061-236">Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="85061-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="85061-237">Obslužná rutina může [Zdědit \<TRequirement> AuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je požadavek, který má být zpracován.</span><span class="sxs-lookup"><span data-stu-id="85061-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="85061-238">Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.</span><span class="sxs-lookup"><span data-stu-id="85061-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="85061-239">Použití obslužné rutiny pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="85061-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="85061-240">Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="85061-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="85061-241">Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem.</span><span class="sxs-lookup"><span data-stu-id="85061-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="85061-242">K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol.</span><span class="sxs-lookup"><span data-stu-id="85061-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="85061-243">Pokud je k dispozici deklarace identity, počítá se věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="85061-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="85061-244">Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou.</span><span class="sxs-lookup"><span data-stu-id="85061-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="85061-245">Po úspěšném ověření `context.Succeed` je vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.</span><span class="sxs-lookup"><span data-stu-id="85061-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="85061-246">Použití obslužné rutiny pro více požadavků</span><span class="sxs-lookup"><span data-stu-id="85061-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="85061-247">Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="85061-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="85061-248">Předchozí průchod kódu [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; vlastnost obsahující požadavky, které nejsou označeny jako úspěšné.</span><span class="sxs-lookup"><span data-stu-id="85061-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="85061-249">Pro `ReadPermission` přístup k požadovanému prostředku musí být uživatel buď vlastník, nebo sponzor.</span><span class="sxs-lookup"><span data-stu-id="85061-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="85061-250">V případě `EditPermission` `DeletePermission` požadavku nebo musí být vlastníkem přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="85061-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="85061-251">Registrace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="85061-251">Handler registration</span></span>

<span data-ttu-id="85061-252">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="85061-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="85061-253">Příklad:</span><span class="sxs-lookup"><span data-stu-id="85061-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="85061-254">Předchozí kód se registruje `MinimumAgeHandler` jako typ singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="85061-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="85061-255">Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="85061-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="85061-256">Co by měl obslužná rutina vrátit?</span><span class="sxs-lookup"><span data-stu-id="85061-256">What should a handler return?</span></span>

<span data-ttu-id="85061-257">Všimněte si, že `Handle` metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="85061-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="85061-258">Jak se uvádí stav buď úspěch, nebo neúspěch?</span><span class="sxs-lookup"><span data-stu-id="85061-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="85061-259">Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)` a předáním požadavku, který byl úspěšně ověřen.</span><span class="sxs-lookup"><span data-stu-id="85061-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="85061-260">Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="85061-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="85061-261">Chcete-li zaručit selhání i v případě úspěchu dalších obslužných rutin požadavků, zavolejte `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="85061-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="85061-262">Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail` , všechny ostatní obslužné rutiny jsou stále volány.</span><span class="sxs-lookup"><span data-stu-id="85061-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="85061-263">To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek.</span><span class="sxs-lookup"><span data-stu-id="85061-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="85061-264">Když je nastavena na `false` , vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy, kdy je volána spuštění obslužných rutin `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="85061-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="85061-265">`InvokeHandlersAfterFailure`Výchozí hodnota – v takovém `true` případě jsou volány všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="85061-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="85061-266">Obslužné rutiny autorizace jsou volány i v případě, že ověřování selhává.</span><span class="sxs-lookup"><span data-stu-id="85061-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="85061-267">Proč bych chtěl více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="85061-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="85061-268">V případech, kdy chcete, aby vyhodnocení bylo na **nebo** bázi, implementujte více obslužných rutin pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="85061-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="85061-269">Například Microsoft má dveře, které se otevírají jenom s kartami Key.</span><span class="sxs-lookup"><span data-stu-id="85061-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="85061-270">Pokud ponecháte kartu klíče na domovské stránce, recepční vytiskne dočasný štítek a otevře dvířka za vás.</span><span class="sxs-lookup"><span data-stu-id="85061-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="85061-271">V tomto scénáři byste měli jeden požadavek, *BuildingEntry*, ale několik obslužných rutin, každý z nich prozkoumá jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="85061-271">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="85061-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="85061-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="85061-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="85061-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="85061-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="85061-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="85061-275">Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="85061-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="85061-276">Pokud obslužná rutina uspěje, když zásada vyhodnotí `BuildingEntryRequirement` , vyhodnocení zásad je úspěšné.</span><span class="sxs-lookup"><span data-stu-id="85061-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="85061-277">Použití funkce ke splnění zásad</span><span class="sxs-lookup"><span data-stu-id="85061-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="85061-278">Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="85061-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="85061-279">Je možné dodat a `Func<AuthorizationHandlerContext, bool>` nakonfigurovat zásadu pomocí `RequireAssertion` Tvůrce zásad.</span><span class="sxs-lookup"><span data-stu-id="85061-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="85061-280">Například předchozí `BadgeEntryHandler` může být přepsána následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="85061-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="85061-281">Přístup k kontextu požadavku MVC v obslužných rutinách</span><span class="sxs-lookup"><span data-stu-id="85061-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="85061-282">`HandleRequirementAsync`Metoda, kterou implementujete v obslužné rutině autorizace, má dva parametry: `AuthorizationHandlerContext` a, `TRequirement` kterou zpracováváte.</span><span class="sxs-lookup"><span data-stu-id="85061-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="85061-283">Rozhraní, jako je MVC, nebo :::no-loc(SignalR)::: jsou volná pro přidání libovolného objektu do `Resource` vlastnosti v `AuthorizationHandlerContext` pro předání dalších informací.</span><span class="sxs-lookup"><span data-stu-id="85061-283">Frameworks such as MVC or :::no-loc(SignalR)::: are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="85061-284">MVC například projde instanci [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) ve `Resource` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="85061-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="85061-285">Tato vlastnost poskytuje přístup k `HttpContext` , `RouteData` a vše ostatní poskytované MVC a :::no-loc(Razor)::: stránkami.</span><span class="sxs-lookup"><span data-stu-id="85061-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="85061-286">Použití `Resource` vlastnosti je specifické pro rozhraní.</span><span class="sxs-lookup"><span data-stu-id="85061-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="85061-287">Použití informací v této `Resource` vlastnosti omezuje vaše zásady autorizace na konkrétní architektury.</span><span class="sxs-lookup"><span data-stu-id="85061-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="85061-288">Vlastnost byste měli přetypovat `Resource` pomocí `is` klíčového slova a pak potvrdit, že přetypování bylo úspěšné, aby `InvalidCastException` při spuštění v jiných rozhraních nedošlo k chybě kódu.</span><span class="sxs-lookup"><span data-stu-id="85061-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end

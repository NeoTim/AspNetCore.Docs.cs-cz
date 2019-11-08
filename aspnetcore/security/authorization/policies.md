---
title: Ověřování na základě zásad v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet a používat obslužné rutiny zásad autorizace pro vynucování autorizačních požadavků v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: security/authorization/policies
ms.openlocfilehash: e3929fb0f45d4ba28f46a6b42b653940de0badb0
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761036"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="b0775-103">Ověřování na základě zásad v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0775-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b0775-104">V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady.</span><span class="sxs-lookup"><span data-stu-id="b0775-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="b0775-105">Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu.</span><span class="sxs-lookup"><span data-stu-id="b0775-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="b0775-106">Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.</span><span class="sxs-lookup"><span data-stu-id="b0775-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="b0775-107">Zásady autorizace se skládají z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="b0775-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="b0775-108">Je zaregistrován jako součást konfigurace autorizační služby v metodě `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0775-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="b0775-109">V předchozím příkladu se vytvoří zásada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="b0775-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="b0775-110">Má jeden požadavek&mdash;minimální stáří, které je zadáno jako parametr pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="b0775-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="b0775-111">Načetl služby IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="b0775-111">IAuthorizationService</span></span> 

<span data-ttu-id="b0775-112">Primární služba, která určuje, jestli je autorizace úspěšná, <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span><span class="sxs-lookup"><span data-stu-id="b0775-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="b0775-113">Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="b0775-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="b0775-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> je služba značek bez metod a mechanismus pro sledování, zda byla autorizace úspěšná.</span><span class="sxs-lookup"><span data-stu-id="b0775-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="b0775-115">Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> zodpovídá za kontrolu, jestli jsou splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="b0775-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="b0775-116">Třída <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> je to, co obslužná rutina používá k označení, zda byly splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="b0775-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="b0775-117">Následující kód zobrazuje zjednodušenou (a poznámkovou poznámku) výchozí implementaci autorizační služby:</span><span class="sxs-lookup"><span data-stu-id="b0775-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="b0775-118">Následující kód ukazuje typický `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0775-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="b0775-119">Pro autorizaci použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]`.</span><span class="sxs-lookup"><span data-stu-id="b0775-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="b0775-120">Použití zásad u řadičů MVC</span><span class="sxs-lookup"><span data-stu-id="b0775-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="b0775-121">Pokud používáte Razor Pages, přečtěte si téma [použití zásad pro Razor Pages](#applying-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="b0775-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="b0775-122">Zásady se aplikují na řadiče pomocí atributu `[Authorize]` s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="b0775-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b0775-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b0775-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="b0775-124">Použití zásad pro Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b0775-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="b0775-125">Zásady se aplikují na Razor Pages pomocí atributu `[Authorize]` s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="b0775-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b0775-126">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b0775-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="b0775-127">Zásady je možné použít také pro Razor Pages pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="b0775-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="b0775-128">Požadavky</span><span class="sxs-lookup"><span data-stu-id="b0775-128">Requirements</span></span>

<span data-ttu-id="b0775-129">Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="b0775-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="b0775-130">V našich zásadách "AtLeast21" je požadavek jedním parametrem&mdash;minimální stáří.</span><span class="sxs-lookup"><span data-stu-id="b0775-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="b0775-131">Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky.</span><span class="sxs-lookup"><span data-stu-id="b0775-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="b0775-132">Parametr minimálního stáří by mohl být implementován následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="b0775-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="b0775-133">Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="b0775-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="b0775-134">Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .</span><span class="sxs-lookup"><span data-stu-id="b0775-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="b0775-135">Požadavek nemusí mít data ani vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b0775-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="b0775-136">Obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="b0775-136">Authorization handlers</span></span>

<span data-ttu-id="b0775-137">Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku.</span><span class="sxs-lookup"><span data-stu-id="b0775-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="b0775-138">Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.</span><span class="sxs-lookup"><span data-stu-id="b0775-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="b0775-139">Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="b0775-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="b0775-140">Obslužná rutina může dědit [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je požadavek, který má být zpracován.</span><span class="sxs-lookup"><span data-stu-id="b0775-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="b0775-141">Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.</span><span class="sxs-lookup"><span data-stu-id="b0775-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="b0775-142">Použití obslužné rutiny pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="b0775-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="b0775-143">Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="b0775-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="b0775-144">Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem.</span><span class="sxs-lookup"><span data-stu-id="b0775-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="b0775-145">K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol.</span><span class="sxs-lookup"><span data-stu-id="b0775-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="b0775-146">Pokud je k dispozici deklarace identity, počítá se věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="b0775-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="b0775-147">Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou.</span><span class="sxs-lookup"><span data-stu-id="b0775-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="b0775-148">Po úspěšném ověření je `context.Succeed` vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.</span><span class="sxs-lookup"><span data-stu-id="b0775-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="b0775-149">Použití obslužné rutiny pro více požadavků</span><span class="sxs-lookup"><span data-stu-id="b0775-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="b0775-150">Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="b0775-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="b0775-151">Předchozí kód projde [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky, které nejsou označeny jako úspěšné.</span><span class="sxs-lookup"><span data-stu-id="b0775-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="b0775-152">Pro `ReadPermission` požadavek musí být uživatel buď vlastníkem, nebo sponzorem pro přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="b0775-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="b0775-153">V případě `EditPermission` nebo `DeletePermission` požadavku musí být vlastníkem pro přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="b0775-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="b0775-154">Registrace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="b0775-154">Handler registration</span></span>

<span data-ttu-id="b0775-155">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b0775-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="b0775-156">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b0775-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="b0775-157">Předchozí kód registruje `MinimumAgeHandler` jako typ singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="b0775-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="b0775-158">Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="b0775-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="b0775-159">Co by měl obslužná rutina vrátit?</span><span class="sxs-lookup"><span data-stu-id="b0775-159">What should a handler return?</span></span>

<span data-ttu-id="b0775-160">Všimněte si, že metoda `Handle` v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b0775-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="b0775-161">Jak se uvádí stav buď úspěch, nebo neúspěch?</span><span class="sxs-lookup"><span data-stu-id="b0775-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="b0775-162">Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)`a předáním požadavku, který byl úspěšně ověřen.</span><span class="sxs-lookup"><span data-stu-id="b0775-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="b0775-163">Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="b0775-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="b0775-164">Chcete-li zaručit selhání i v případě úspěchu dalších obslužných rutin požadavků, zavolejte `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="b0775-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="b0775-165">Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail`, všechny ostatní obslužné rutiny jsou stále volány.</span><span class="sxs-lookup"><span data-stu-id="b0775-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="b0775-166">To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek.</span><span class="sxs-lookup"><span data-stu-id="b0775-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="b0775-167">Pokud je nastavena na `false`, vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy spuštění obslužných rutin při volání `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="b0775-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="b0775-168">Výchozí hodnota `InvokeHandlersAfterFailure` `true`. v takovém případě jsou volány všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="b0775-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="b0775-169">Obslužné rutiny autorizace jsou volány i v případě, že ověřování selhává.</span><span class="sxs-lookup"><span data-stu-id="b0775-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="b0775-170">Proč bych chtěl více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="b0775-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="b0775-171">V případech, kdy chcete, aby vyhodnocení bylo na **nebo** bázi, implementujte více obslužných rutin pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="b0775-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="b0775-172">Například Microsoft má dveře, které se otevírají jenom s kartami Key.</span><span class="sxs-lookup"><span data-stu-id="b0775-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="b0775-173">Pokud ponecháte kartu klíče na domovské stránce, recepční vytiskne dočasný štítek a otevře dvířka za vás.</span><span class="sxs-lookup"><span data-stu-id="b0775-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="b0775-174">V tomto scénáři byste měli jeden požadavek, *BuildingEntry*, ale několik obslužných rutin, každý z nich prozkoumá jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="b0775-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="b0775-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="b0775-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="b0775-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b0775-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="b0775-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b0775-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="b0775-178">Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="b0775-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="b0775-179">Pokud je obslužná rutina úspěšná, když zásada vyhodnotí `BuildingEntryRequirement`, vyhodnocení zásad je úspěšné.</span><span class="sxs-lookup"><span data-stu-id="b0775-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="b0775-180">Splnění zásad pomocí funkce</span><span class="sxs-lookup"><span data-stu-id="b0775-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="b0775-181">Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="b0775-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="b0775-182">Při konfiguraci zásady pomocí nástroje `RequireAssertion` Builder zásad je možné uvést `Func<AuthorizationHandlerContext, bool>`.</span><span class="sxs-lookup"><span data-stu-id="b0775-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="b0775-183">Předchozí `BadgeEntryHandler` například můžete přepsat následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="b0775-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="b0775-184">Přístup k kontextu požadavku MVC v obslužných rutinách</span><span class="sxs-lookup"><span data-stu-id="b0775-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="b0775-185">Metoda `HandleRequirementAsync`, kterou implementujete v obslužné rutině autorizace, má dva parametry: `AuthorizationHandlerContext` a `TRequirement`, které zpracováváte.</span><span class="sxs-lookup"><span data-stu-id="b0775-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="b0775-186">Rozhraní, jako je MVC nebo Jabbr, jsou zdarma, pokud chcete do vlastnosti `Resource` v `AuthorizationHandlerContext` přidat libovolný objekt pro předání dalších informací.</span><span class="sxs-lookup"><span data-stu-id="b0775-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="b0775-187">MVC například projde instanci [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) ve vlastnosti `Resource`.</span><span class="sxs-lookup"><span data-stu-id="b0775-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="b0775-188">Tato vlastnost poskytuje přístup k `HttpContext`, `RouteData`a všechno ostatní poskytované MVC a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="b0775-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="b0775-189">Použití vlastnosti `Resource` je specifické pro rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b0775-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="b0775-190">Použití informací z vlastnosti `Resource` omezí vaše zásady autorizace na konkrétní architektury.</span><span class="sxs-lookup"><span data-stu-id="b0775-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="b0775-191">Vlastnost `Resource` byste měli přetypovat pomocí klíčového slova `is` a pak potvrdit, že přetypování bylo úspěšné, aby při spuštění v jiných rozhraních nedošlo k chybě kódu `InvalidCastException`.</span><span class="sxs-lookup"><span data-stu-id="b0775-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="b0775-192">V rámci pokrývání [ověřování na základě rolí](xref:security/authorization/roles) a [autorizaci založené na deklaracích](xref:security/authorization/claims) se používají požadavky, obslužná rutina požadavků a předem nakonfigurované zásady.</span><span class="sxs-lookup"><span data-stu-id="b0775-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="b0775-193">Tyto stavební bloky podporují výraz ověřovacích vyhodnocení v kódu.</span><span class="sxs-lookup"><span data-stu-id="b0775-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="b0775-194">Výsledkem je bohatší, opakovaně použitelná testovatelné autorizační struktura.</span><span class="sxs-lookup"><span data-stu-id="b0775-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="b0775-195">Zásady autorizace se skládají z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="b0775-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="b0775-196">Je zaregistrován jako součást konfigurace autorizační služby v metodě `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0775-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="b0775-197">V předchozím příkladu se vytvoří zásada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="b0775-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="b0775-198">Má jeden požadavek&mdash;minimální stáří, které je zadáno jako parametr pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="b0775-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="b0775-199">Načetl služby IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="b0775-199">IAuthorizationService</span></span> 

<span data-ttu-id="b0775-200">Primární služba, která určuje, jestli je autorizace úspěšná, <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span><span class="sxs-lookup"><span data-stu-id="b0775-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="b0775-201">Předchozí kód zvýrazní dvě metody [načetl služby IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="b0775-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="b0775-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> je služba značek bez metod a mechanismus pro sledování, zda byla autorizace úspěšná.</span><span class="sxs-lookup"><span data-stu-id="b0775-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="b0775-203">Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> zodpovídá za kontrolu, jestli jsou splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="b0775-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="b0775-204">Třída <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> je to, co obslužná rutina používá k označení, zda byly splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="b0775-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="b0775-205">Následující kód zobrazuje zjednodušenou (a poznámkovou poznámku) výchozí implementaci autorizační služby:</span><span class="sxs-lookup"><span data-stu-id="b0775-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="b0775-206">Následující kód ukazuje typický `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b0775-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="b0775-207">Pro autorizaci použijte <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> nebo `[Authorize(Policy = "Something")]`.</span><span class="sxs-lookup"><span data-stu-id="b0775-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="b0775-208">Použití zásad u řadičů MVC</span><span class="sxs-lookup"><span data-stu-id="b0775-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="b0775-209">Pokud používáte Razor Pages, přečtěte si téma [použití zásad pro Razor Pages](#applying-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="b0775-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="b0775-210">Zásady se aplikují na řadiče pomocí atributu `[Authorize]` s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="b0775-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b0775-211">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b0775-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="b0775-212">Použití zásad pro Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b0775-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="b0775-213">Zásady se aplikují na Razor Pages pomocí atributu `[Authorize]` s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="b0775-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b0775-214">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b0775-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="b0775-215">Zásady je možné použít také pro Razor Pages pomocí [autorizační konvence](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="b0775-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="b0775-216">Požadavky</span><span class="sxs-lookup"><span data-stu-id="b0775-216">Requirements</span></span>

<span data-ttu-id="b0775-217">Požadavek na autorizaci je kolekce datových parametrů, které může zásada použít k vyhodnocení aktuálního objektu zabezpečení uživatele.</span><span class="sxs-lookup"><span data-stu-id="b0775-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="b0775-218">V našich zásadách "AtLeast21" je požadavek jedním parametrem&mdash;minimální stáří.</span><span class="sxs-lookup"><span data-stu-id="b0775-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="b0775-219">Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky.</span><span class="sxs-lookup"><span data-stu-id="b0775-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="b0775-220">Parametr minimálního stáří by mohl být implementován následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="b0775-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="b0775-221">Pokud zásady autorizace obsahují více autorizačních požadavků, musí všechny požadavky úspěšně projít, aby bylo vyhodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="b0775-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="b0775-222">Jinými slovy: víc autorizačních požadavků přidaných do jediné zásady autorizace se zpracovává na bázi **a** .</span><span class="sxs-lookup"><span data-stu-id="b0775-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="b0775-223">Požadavek nemusí mít data ani vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="b0775-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="b0775-224">Obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="b0775-224">Authorization handlers</span></span>

<span data-ttu-id="b0775-225">Obslužná rutina autorizace zodpovídá za hodnocení vlastností požadavku.</span><span class="sxs-lookup"><span data-stu-id="b0775-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="b0775-226">Obslužná rutina autorizace vyhodnocuje požadavky na poskytnutý [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, jestli je povolený přístup.</span><span class="sxs-lookup"><span data-stu-id="b0775-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="b0775-227">Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="b0775-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="b0775-228">Obslužná rutina může dědit [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je požadavek, který má být zpracován.</span><span class="sxs-lookup"><span data-stu-id="b0775-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="b0775-229">Alternativně může obslužná rutina implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jednoho typu požadavku.</span><span class="sxs-lookup"><span data-stu-id="b0775-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="b0775-230">Použití obslužné rutiny pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="b0775-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="b0775-231">Následuje příklad vztahu 1:1, ve kterém minimální věková obslužná rutina využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="b0775-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="b0775-232">Předchozí kód určuje, zda má aktuální objekt zabezpečení uživatele datum narození, který byl vydán známým a důvěryhodným vydavatelem.</span><span class="sxs-lookup"><span data-stu-id="b0775-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="b0775-233">K autorizaci nedojde v případě, že deklarace identity chybí. v takovém případě se vrátí dokončený úkol.</span><span class="sxs-lookup"><span data-stu-id="b0775-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="b0775-234">Pokud je k dispozici deklarace identity, počítá se věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="b0775-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="b0775-235">Pokud uživatel splňuje minimální stáří definované požadavkem, autorizace se považuje za úspěšnou.</span><span class="sxs-lookup"><span data-stu-id="b0775-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="b0775-236">Po úspěšném ověření je `context.Succeed` vyvolána s uspokojivým požadavkem jako jeho jediným parametrem.</span><span class="sxs-lookup"><span data-stu-id="b0775-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="b0775-237">Použití obslužné rutiny pro více požadavků</span><span class="sxs-lookup"><span data-stu-id="b0775-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="b0775-238">Následuje příklad vztahu 1: n, ve kterém obslužná rutina oprávnění dokáže zvládnout tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="b0775-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="b0775-239">Předchozí kód projde [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky, které nejsou označeny jako úspěšné.</span><span class="sxs-lookup"><span data-stu-id="b0775-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="b0775-240">Pro `ReadPermission` požadavek musí být uživatel buď vlastníkem, nebo sponzorem pro přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="b0775-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="b0775-241">V případě `EditPermission` nebo `DeletePermission` požadavku musí být vlastníkem pro přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="b0775-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="b0775-242">Registrace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="b0775-242">Handler registration</span></span>

<span data-ttu-id="b0775-243">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b0775-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="b0775-244">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b0775-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="b0775-245">Předchozí kód registruje `MinimumAgeHandler` jako typ singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="b0775-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="b0775-246">Obslužné rutiny mohou být registrovány pomocí kterékoli z předdefinovaných [životností služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="b0775-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="b0775-247">Co by měl obslužná rutina vrátit?</span><span class="sxs-lookup"><span data-stu-id="b0775-247">What should a handler return?</span></span>

<span data-ttu-id="b0775-248">Všimněte si, že metoda `Handle` v [příkladu obslužné rutiny](#security-authorization-handler-example) nevrací žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b0775-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="b0775-249">Jak se uvádí stav buď úspěch, nebo neúspěch?</span><span class="sxs-lookup"><span data-stu-id="b0775-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="b0775-250">Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)`a předáním požadavku, který byl úspěšně ověřen.</span><span class="sxs-lookup"><span data-stu-id="b0775-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="b0775-251">Obslužná rutina nemusí zpracovávat chyby obecně, protože jiné obslužné rutiny pro stejný požadavek mohou být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="b0775-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="b0775-252">Chcete-li zaručit selhání i v případě úspěchu dalších obslužných rutin požadavků, zavolejte `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="b0775-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="b0775-253">Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail`, všechny ostatní obslužné rutiny jsou stále volány.</span><span class="sxs-lookup"><span data-stu-id="b0775-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="b0775-254">To umožňuje požadavkům vytvořit vedlejší účinky, jako je protokolování, které probíhá, i když jiná obslužná rutina úspěšně ověřila nebo neprošel požadavek.</span><span class="sxs-lookup"><span data-stu-id="b0775-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="b0775-255">Pokud je nastavena na `false`, vlastnost [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (k dispozici v ASP.NET Core 1,1 a novější) krátkodobé okruhy spuštění obslužných rutin při volání `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="b0775-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="b0775-256">Výchozí hodnota `InvokeHandlersAfterFailure` `true`. v takovém případě jsou volány všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="b0775-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="b0775-257">Obslužné rutiny autorizace jsou volány i v případě, že ověřování selhává.</span><span class="sxs-lookup"><span data-stu-id="b0775-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="b0775-258">Proč bych chtěl více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="b0775-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="b0775-259">V případech, kdy chcete, aby vyhodnocení bylo na **nebo** bázi, implementujte více obslužných rutin pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="b0775-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="b0775-260">Například Microsoft má dveře, které se otevírají jenom s kartami Key.</span><span class="sxs-lookup"><span data-stu-id="b0775-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="b0775-261">Pokud ponecháte kartu klíče na domovské stránce, recepční vytiskne dočasný štítek a otevře dvířka za vás.</span><span class="sxs-lookup"><span data-stu-id="b0775-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="b0775-262">V tomto scénáři byste měli jeden požadavek, *BuildingEntry*, ale několik obslužných rutin, každý z nich prozkoumá jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="b0775-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="b0775-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="b0775-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="b0775-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b0775-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="b0775-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b0775-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="b0775-266">Zajistěte, aby byly oba obslužné rutiny [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="b0775-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="b0775-267">Pokud je obslužná rutina úspěšná, když zásada vyhodnotí `BuildingEntryRequirement`, vyhodnocení zásad je úspěšné.</span><span class="sxs-lookup"><span data-stu-id="b0775-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="b0775-268">Splnění zásad pomocí funkce</span><span class="sxs-lookup"><span data-stu-id="b0775-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="b0775-269">Mohou nastat situace, kdy je vhodné zásadu snadno vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="b0775-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="b0775-270">Při konfiguraci zásady pomocí nástroje `RequireAssertion` Builder zásad je možné uvést `Func<AuthorizationHandlerContext, bool>`.</span><span class="sxs-lookup"><span data-stu-id="b0775-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="b0775-271">Předchozí `BadgeEntryHandler` například můžete přepsat následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="b0775-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="b0775-272">Přístup k kontextu požadavku MVC v obslužných rutinách</span><span class="sxs-lookup"><span data-stu-id="b0775-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="b0775-273">Metoda `HandleRequirementAsync`, kterou implementujete v obslužné rutině autorizace, má dva parametry: `AuthorizationHandlerContext` a `TRequirement`, které zpracováváte.</span><span class="sxs-lookup"><span data-stu-id="b0775-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="b0775-274">Rozhraní, jako je MVC nebo Jabbr, jsou zdarma, pokud chcete do vlastnosti `Resource` v `AuthorizationHandlerContext` přidat libovolný objekt pro předání dalších informací.</span><span class="sxs-lookup"><span data-stu-id="b0775-274">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="b0775-275">MVC například projde instanci [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) ve vlastnosti `Resource`.</span><span class="sxs-lookup"><span data-stu-id="b0775-275">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="b0775-276">Tato vlastnost poskytuje přístup k `HttpContext`, `RouteData`a všechno ostatní poskytované MVC a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="b0775-276">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="b0775-277">Použití vlastnosti `Resource` je specifické pro rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b0775-277">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="b0775-278">Použití informací z vlastnosti `Resource` omezí vaše zásady autorizace na konkrétní architektury.</span><span class="sxs-lookup"><span data-stu-id="b0775-278">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="b0775-279">Vlastnost `Resource` byste měli přetypovat pomocí klíčového slova `is` a pak potvrdit, že přetypování bylo úspěšné, aby při spuštění v jiných rozhraních nedošlo k chybě kódu `InvalidCastException`.</span><span class="sxs-lookup"><span data-stu-id="b0775-279">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
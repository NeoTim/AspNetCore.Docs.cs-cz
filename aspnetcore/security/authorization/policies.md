---
title: Autorizace založená na zásadách v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit a používat obslužné rutiny zásad autorizace pro vynucení požadavků na autorizaci v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488758"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="8f3e7-103">Autorizace založená na zásadách v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8f3e7-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8f3e7-104">Pod kryty, [autorizace založená na rolích](xref:security/authorization/roles) a [autorizace založená na deklaracích](xref:security/authorization/claims) používá požadavek, obslužnou rutinu požadavků a předem nakonfigurovanou zásadu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="8f3e7-105">Tyto stavební bloky podporují vyjádření hodnocení autorizace v kódu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="8f3e7-106">Výsledkem je bohatší, opakovaně použitelná, testovatelná autorizační struktura.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="8f3e7-107">Zásady autorizace se skládají z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="8f3e7-108">Je registrován jako součást konfigurace autorizační služby v metodě: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="8f3e7-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="8f3e7-109">V předchozím příkladu je vytvořena zásada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="8f3e7-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="8f3e7-110">Má jediný požadavek&mdash;na minimální věk, který je dodáván jako parametr požadavku.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="8f3e7-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="8f3e7-111">IAuthorizationService</span></span> 

<span data-ttu-id="8f3e7-112">Primární služba, která určuje, zda <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>je autorizace úspěšná, je :</span><span class="sxs-lookup"><span data-stu-id="8f3e7-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="8f3e7-113">Předchozí kód zvýrazní dvě metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="8f3e7-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="8f3e7-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda je autorizace úspěšná.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="8f3e7-115">Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> z nich je zodpovědný za kontrolu, zda jsou splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="8f3e7-116">Třída <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> je to, co obslužná rutina používá k označení, zda byly splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="8f3e7-117">Následující kód zobrazuje zjednodušenou (a notoponos s poznámkami) výchozí implementaci autorizační služby:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="8f3e7-118">Následující kód ukazuje `ConfigureServices`typické :</span><span class="sxs-lookup"><span data-stu-id="8f3e7-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="8f3e7-119">Použití <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` nebo pro autorizaci.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="8f3e7-120">Použití zásad na řadiče MVC</span><span class="sxs-lookup"><span data-stu-id="8f3e7-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="8f3e7-121">Pokud používáte Razor Pages, [přečtěte si téma Použití zásad pro stránky Razor](#applying-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="8f3e7-122">Zásady jsou použity pro `[Authorize]` řadiče pomocí atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="8f3e7-123">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="8f3e7-124">Použití zásad na stránky Razor</span><span class="sxs-lookup"><span data-stu-id="8f3e7-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="8f3e7-125">Zásady jsou použity na `[Authorize]` Razor Pages pomocí atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="8f3e7-126">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="8f3e7-127">Zásady lze také použít na Stránky Razor pomocí [konvence autorizace](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="8f3e7-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="8f3e7-128">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8f3e7-128">Requirements</span></span>

<span data-ttu-id="8f3e7-129">Požadavek na autorizaci je kolekce parametrů dat, které zásady mohou použít k vyhodnocení aktuálního uživatelského objektu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="8f3e7-130">V naší politice "AtLeast21" je požadavkem jediný parametr&mdash;minimální věk.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="8f3e7-131">Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="8f3e7-132">Parametrizovaný požadavek na minimální věk by mohl být implementován takto:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="8f3e7-133">Pokud zásada autorizace obsahuje více požadavků na autorizaci, musí všechny požadavky projít, aby bylo vyhodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="8f3e7-134">Jinými slovy, více požadavků na autorizaci přidaných do jedné zásady autorizace jsou zpracovány na základě **AND.**</span><span class="sxs-lookup"><span data-stu-id="8f3e7-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="8f3e7-135">Požadavek nemusí mít data nebo vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="8f3e7-136">Obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="8f3e7-136">Authorization handlers</span></span>

<span data-ttu-id="8f3e7-137">Obslužná rutina autorizace je zodpovědná za vyhodnocení vlastností požadavku.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="8f3e7-138">Obslužná rutina autorizace vyhodnotí požadavky proti poskytnuté [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, zda je povolen přístup.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="8f3e7-139">Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="8f3e7-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="8f3e7-140">Obslužná rutina může `TRequirement` dědit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde je požadavek, který má být zpracován.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="8f3e7-141">Alternativně obslužná rutina může implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jeden typ požadavku.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="8f3e7-142">Použití obslužné rutiny pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="8f3e7-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="8f3e7-143">Následuje příklad vztahu 1:1, ve kterém obslužná rutina minimálního věku využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="8f3e7-144">Předchozí kód určuje, zda aktuální zaregistrovaný objekt uživatele má datum narození deklarace, která byla vydána známé a důvěryhodné vystavitele.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="8f3e7-145">K autorizaci nemůže dojít, pokud deklarace chybí, v takovém případě je vrácena dokončená úloha.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="8f3e7-146">Pokud je k dispozici deklarace, vypočítá se věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="8f3e7-147">Pokud uživatel splňuje minimální věk definovaný požadavkem, autorizace se považuje za úspěšnou.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="8f3e7-148">Pokud je autorizace úspěšná, `context.Succeed` je vyvolána s splněným požadavkem jako jeho jediným parametrem.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="8f3e7-149">Použití obslužné rutiny pro více požadavků</span><span class="sxs-lookup"><span data-stu-id="8f3e7-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="8f3e7-150">Následuje příklad relace 1:N, ve které obslužná rutina oprávnění může zpracovat tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="8f3e7-151">Předchozí kód prochází [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky, které nejsou označeny jako úspěšné.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="8f3e7-152">Pro `ReadPermission` požadavek musí být uživatel vlastníkem nebo sponzorem, aby měl přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="8f3e7-153">V případě `EditPermission` nebo `DeletePermission` požadavku musí být vlastníkem, aby měl přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="8f3e7-154">Registrace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="8f3e7-154">Handler registration</span></span>

<span data-ttu-id="8f3e7-155">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="8f3e7-156">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="8f3e7-157">Předchozí kód registruje `MinimumAgeHandler` jako singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="8f3e7-158">Obslužné rutiny lze zaregistrovat pomocí některé [hod-do provozní doby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="8f3e7-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="8f3e7-159">Co by měl obslužná rutina vrátit?</span><span class="sxs-lookup"><span data-stu-id="8f3e7-159">What should a handler return?</span></span>

<span data-ttu-id="8f3e7-160">Všimněte `Handle` si, že metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) vrátí žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="8f3e7-161">Jak je uveden stav úspěchu nebo neúspěchu?</span><span class="sxs-lookup"><span data-stu-id="8f3e7-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="8f3e7-162">Obslužná `context.Succeed(IAuthorizationRequirement requirement)`rutina označuje úspěch voláním , předáním požadavku, který byl úspěšně ověřen.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="8f3e7-163">Obslužná rutina nemusí obecně zpracovávat chyby, protože ostatní obslužné rutiny pro stejný požadavek mohou být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="8f3e7-164">Chcete-li zaručit selhání, i v `context.Fail`případě, že ostatní obslužné rutiny požadavku úspěšné, volání .</span><span class="sxs-lookup"><span data-stu-id="8f3e7-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="8f3e7-165">Pokud volání `context.Succeed` obslužné rutiny nebo `context.Fail`, všechny ostatní obslužné rutiny jsou stále volány.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="8f3e7-166">To umožňuje požadavky na vedlejší účinky, jako je například protokolování, ke kterému dochází i v případě, že jiná obslužná rutina úspěšně ověřena nebo se nezdařilpožadavek.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="8f3e7-167">Pokud je `false`nastavena na , [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) vlastnost (k dispozici v ASP.NET Core 1.1 a novější) zkratuje provádění obslužné rutiny při `context.Fail` volání.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="8f3e7-168">`InvokeHandlersAfterFailure`výchozí hodnota `true`je na , v takovém případě jsou volány všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="8f3e7-169">Obslužné rutiny autorizace se nazývají i v případě, že se ověření nezdaří.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="8f3e7-170">Proč bych měl chtít více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="8f3e7-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="8f3e7-171">V případech, kdy chcete, aby hodnocení bylo na základě **or,** implementovat více obslužné rutiny pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="8f3e7-172">Například Microsoft má dveře, které se otevírají pouze s klíčovými kartami.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="8f3e7-173">Pokud si necháte svou kartu doma, recepční vytiskne dočasnou nálepku a otevře vám dveře.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="8f3e7-174">V tomto scénáři byste měli jeden požadavek *BuildingEntry*, ale více obslužné rutiny, každý z nich zkoumá jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="8f3e7-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="8f3e7-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="8f3e7-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="8f3e7-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="8f3e7-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="8f3e7-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="8f3e7-178">Ujistěte se, že jsou [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)obě obslužné rutiny .</span><span class="sxs-lookup"><span data-stu-id="8f3e7-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="8f3e7-179">Pokud buď obslužná rutina úspěšné při vyhodnotí zásady `BuildingEntryRequirement`, vyhodnocení zásady úspěšné.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="8f3e7-180">Použití func ke splnění politiky</span><span class="sxs-lookup"><span data-stu-id="8f3e7-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="8f3e7-181">Mohou nastat situace, ve kterých je plnění zásady jednoduché vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="8f3e7-182">Je možné zadat při `Func<AuthorizationHandlerContext, bool>` konfiguraci zásad s `RequireAssertion` tvůrcem zásad.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="8f3e7-183">Předchozí `BadgeEntryHandler` může být například přepsána takto:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="8f3e7-184">Přístup k kontextu požadavku MVC v obslužných rutinách</span><span class="sxs-lookup"><span data-stu-id="8f3e7-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="8f3e7-185">Metoda, `HandleRequirementAsync` kterou implementujete v obslužné rutině autorizace, má dva parametry: a `AuthorizationHandlerContext` `TRequirement` zpracováváte.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="8f3e7-186">Architektury jako MVC nebo Jabbr mohou přidat libovolný `Resource` objekt `AuthorizationHandlerContext` do vlastnosti předat další informace.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="8f3e7-187">Například MVC předá instanci [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) ve vlastnosti. `Resource`</span><span class="sxs-lookup"><span data-stu-id="8f3e7-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="8f3e7-188">Tato vlastnost poskytuje `HttpContext` `RouteData`přístup k , a vše ostatní poskytované MVC a Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="8f3e7-189">Použití vlastnosti `Resource` je specifické pro architekturu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="8f3e7-190">Použití informací `Resource` ve vlastnosti omezuje zásady autorizace na konkrétní architektury.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="8f3e7-191">Měli byste `Resource` přetypovat `is` vlastnost pomocí klíčového slova a pak potvrďte, že `InvalidCastException` obsazení se podařilo zajistit, že váš kód není selhání s při spuštění na jiných rozhraních:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="8f3e7-192">Pod kryty, [autorizace založená na rolích](xref:security/authorization/roles) a [autorizace založená na deklaracích](xref:security/authorization/claims) používá požadavek, obslužnou rutinu požadavků a předem nakonfigurovanou zásadu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="8f3e7-193">Tyto stavební bloky podporují vyjádření hodnocení autorizace v kódu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="8f3e7-194">Výsledkem je bohatší, opakovaně použitelná, testovatelná autorizační struktura.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="8f3e7-195">Zásady autorizace se skládají z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="8f3e7-196">Je registrován jako součást konfigurace autorizační služby v metodě: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="8f3e7-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="8f3e7-197">V předchozím příkladu je vytvořena zásada "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="8f3e7-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="8f3e7-198">Má jediný požadavek&mdash;na minimální věk, který je dodáván jako parametr požadavku.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="8f3e7-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="8f3e7-199">IAuthorizationService</span></span> 

<span data-ttu-id="8f3e7-200">Primární služba, která určuje, zda <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>je autorizace úspěšná, je :</span><span class="sxs-lookup"><span data-stu-id="8f3e7-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="8f3e7-201">Předchozí kód zvýrazní dvě metody [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="8f3e7-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="8f3e7-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>je služba značek bez metod a mechanismus pro sledování, zda je autorizace úspěšná.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="8f3e7-203">Každý <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> z nich je zodpovědný za kontrolu, zda jsou splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="8f3e7-204">Třída <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> je to, co obslužná rutina používá k označení, zda byly splněny požadavky:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="8f3e7-205">Následující kód zobrazuje zjednodušenou (a notoponos s poznámkami) výchozí implementaci autorizační služby:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="8f3e7-206">Následující kód ukazuje `ConfigureServices`typické :</span><span class="sxs-lookup"><span data-stu-id="8f3e7-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="8f3e7-207">Použití <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` nebo pro autorizaci.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="8f3e7-208">Použití zásad na řadiče MVC</span><span class="sxs-lookup"><span data-stu-id="8f3e7-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="8f3e7-209">Pokud používáte Razor Pages, [přečtěte si téma Použití zásad pro stránky Razor](#applying-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="8f3e7-210">Zásady jsou použity pro `[Authorize]` řadiče pomocí atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="8f3e7-211">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="8f3e7-212">Použití zásad na stránky Razor</span><span class="sxs-lookup"><span data-stu-id="8f3e7-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="8f3e7-213">Zásady jsou použity na `[Authorize]` Razor Pages pomocí atributu s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="8f3e7-214">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="8f3e7-215">Zásady lze také použít na Stránky Razor pomocí [konvence autorizace](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="8f3e7-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="8f3e7-216">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8f3e7-216">Requirements</span></span>

<span data-ttu-id="8f3e7-217">Požadavek na autorizaci je kolekce parametrů dat, které zásady mohou použít k vyhodnocení aktuálního uživatelského objektu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="8f3e7-218">V naší politice "AtLeast21" je požadavkem jediný parametr&mdash;minimální věk.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="8f3e7-219">Požadavek implementuje [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je prázdné rozhraní značky.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="8f3e7-220">Parametrizovaný požadavek na minimální věk by mohl být implementován takto:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="8f3e7-221">Pokud zásada autorizace obsahuje více požadavků na autorizaci, musí všechny požadavky projít, aby bylo vyhodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="8f3e7-222">Jinými slovy, více požadavků na autorizaci přidaných do jedné zásady autorizace jsou zpracovány na základě **AND.**</span><span class="sxs-lookup"><span data-stu-id="8f3e7-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="8f3e7-223">Požadavek nemusí mít data nebo vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="8f3e7-224">Obslužné rutiny autorizace</span><span class="sxs-lookup"><span data-stu-id="8f3e7-224">Authorization handlers</span></span>

<span data-ttu-id="8f3e7-225">Obslužná rutina autorizace je zodpovědná za vyhodnocení vlastností požadavku.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="8f3e7-226">Obslužná rutina autorizace vyhodnotí požadavky proti poskytnuté [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, zda je povolen přístup.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="8f3e7-227">Požadavek může mít [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="8f3e7-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="8f3e7-228">Obslužná rutina může `TRequirement` dědit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde je požadavek, který má být zpracován.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="8f3e7-229">Alternativně obslužná rutina může implementovat [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jeden typ požadavku.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="8f3e7-230">Použití obslužné rutiny pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="8f3e7-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="8f3e7-231">Následuje příklad vztahu 1:1, ve kterém obslužná rutina minimálního věku využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="8f3e7-232">Předchozí kód určuje, zda aktuální zaregistrovaný objekt uživatele má datum narození deklarace, která byla vydána známé a důvěryhodné vystavitele.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="8f3e7-233">K autorizaci nemůže dojít, pokud deklarace chybí, v takovém případě je vrácena dokončená úloha.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="8f3e7-234">Pokud je k dispozici deklarace, vypočítá se věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="8f3e7-235">Pokud uživatel splňuje minimální věk definovaný požadavkem, autorizace se považuje za úspěšnou.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="8f3e7-236">Pokud je autorizace úspěšná, `context.Succeed` je vyvolána s splněným požadavkem jako jeho jediným parametrem.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="8f3e7-237">Použití obslužné rutiny pro více požadavků</span><span class="sxs-lookup"><span data-stu-id="8f3e7-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="8f3e7-238">Následuje příklad relace 1:N, ve které obslužná rutina oprávnění může zpracovat tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="8f3e7-239">Předchozí kód prochází [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky, které nejsou označeny jako úspěšné.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="8f3e7-240">Pro `ReadPermission` požadavek musí být uživatel vlastníkem nebo sponzorem, aby měl přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="8f3e7-241">V případě `EditPermission` nebo `DeletePermission` požadavku musí být vlastníkem, aby měl přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="8f3e7-242">Registrace obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="8f3e7-242">Handler registration</span></span>

<span data-ttu-id="8f3e7-243">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="8f3e7-244">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="8f3e7-245">Předchozí kód registruje `MinimumAgeHandler` jako singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="8f3e7-246">Obslužné rutiny lze zaregistrovat pomocí některé [hod-do provozní doby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="8f3e7-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="8f3e7-247">Co by měl obslužná rutina vrátit?</span><span class="sxs-lookup"><span data-stu-id="8f3e7-247">What should a handler return?</span></span>

<span data-ttu-id="8f3e7-248">Všimněte `Handle` si, že metoda v [příkladu obslužné rutiny](#security-authorization-handler-example) vrátí žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="8f3e7-249">Jak je uveden stav úspěchu nebo neúspěchu?</span><span class="sxs-lookup"><span data-stu-id="8f3e7-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="8f3e7-250">Obslužná `context.Succeed(IAuthorizationRequirement requirement)`rutina označuje úspěch voláním , předáním požadavku, který byl úspěšně ověřen.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="8f3e7-251">Obslužná rutina nemusí obecně zpracovávat chyby, protože ostatní obslužné rutiny pro stejný požadavek mohou být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="8f3e7-252">Chcete-li zaručit selhání, i v `context.Fail`případě, že ostatní obslužné rutiny požadavku úspěšné, volání .</span><span class="sxs-lookup"><span data-stu-id="8f3e7-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="8f3e7-253">Pokud volání `context.Succeed` obslužné rutiny nebo `context.Fail`, všechny ostatní obslužné rutiny jsou stále volány.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="8f3e7-254">To umožňuje požadavky na vedlejší účinky, jako je například protokolování, ke kterému dochází i v případě, že jiná obslužná rutina úspěšně ověřena nebo se nezdařilpožadavek.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="8f3e7-255">Pokud je `false`nastavena na , [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) vlastnost (k dispozici v ASP.NET Core 1.1 a novější) zkratuje provádění obslužné rutiny při `context.Fail` volání.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="8f3e7-256">`InvokeHandlersAfterFailure`výchozí hodnota `true`je na , v takovém případě jsou volány všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="8f3e7-257">Obslužné rutiny autorizace se nazývají i v případě, že se ověření nezdaří.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="8f3e7-258">Proč bych měl chtít více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="8f3e7-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="8f3e7-259">V případech, kdy chcete, aby hodnocení bylo na základě **or,** implementovat více obslužné rutiny pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="8f3e7-260">Například Microsoft má dveře, které se otevírají pouze s klíčovými kartami.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="8f3e7-261">Pokud si necháte svou kartu doma, recepční vytiskne dočasnou nálepku a otevře vám dveře.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="8f3e7-262">V tomto scénáři byste měli jeden požadavek *BuildingEntry*, ale více obslužné rutiny, každý z nich zkoumá jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="8f3e7-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="8f3e7-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="8f3e7-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="8f3e7-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="8f3e7-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="8f3e7-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="8f3e7-266">Ujistěte se, že jsou [registrovány](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)obě obslužné rutiny .</span><span class="sxs-lookup"><span data-stu-id="8f3e7-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="8f3e7-267">Pokud buď obslužná rutina úspěšné při vyhodnotí zásady `BuildingEntryRequirement`, vyhodnocení zásady úspěšné.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="8f3e7-268">Použití func ke splnění politiky</span><span class="sxs-lookup"><span data-stu-id="8f3e7-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="8f3e7-269">Mohou nastat situace, ve kterých je plnění zásady jednoduché vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="8f3e7-270">Je možné zadat při `Func<AuthorizationHandlerContext, bool>` konfiguraci zásad s `RequireAssertion` tvůrcem zásad.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="8f3e7-271">Předchozí `BadgeEntryHandler` může být například přepsána takto:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="8f3e7-272">Přístup k kontextu požadavku MVC v obslužných rutinách</span><span class="sxs-lookup"><span data-stu-id="8f3e7-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="8f3e7-273">Metoda, `HandleRequirementAsync` kterou implementujete v obslužné rutině autorizace, má dva parametry: a `AuthorizationHandlerContext` `TRequirement` zpracováváte.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="8f3e7-274">Architektury jako MVC nebo SignalR jsou zdarma `Resource` přidat libovolný `AuthorizationHandlerContext` objekt do vlastnosti na předat další informace.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="8f3e7-275">Při použití směrování koncových bodů je autorizace obvykle zpracována middlewarem autorizace.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="8f3e7-276">V tomto případě `Resource` je vlastnost <xref:Microsoft.AspNetCore.Http.Endpoint>instancí třídy .</span><span class="sxs-lookup"><span data-stu-id="8f3e7-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="8f3e7-277">Koncový bod lze použít k prominování základní prostředek, do kterého směrování.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="8f3e7-278">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="8f3e7-279">S tradiční směrování, nebo když autorizace se stane jako součást `Resource` filtru <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> autorizace MVC, hodnota je instance.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="8f3e7-280">Tato vlastnost poskytuje `HttpContext` `RouteData`přístup k , a vše ostatní poskytované MVC a Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="8f3e7-281">Použití vlastnosti `Resource` je specifické pro architekturu.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="8f3e7-282">Použití informací `Resource` ve vlastnosti omezuje zásady autorizace na konkrétní architektury.</span><span class="sxs-lookup"><span data-stu-id="8f3e7-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="8f3e7-283">Měli byste `Resource` přetypovat `is` vlastnost pomocí klíčového slova a pak potvrďte, že `InvalidCastException` obsazení se podařilo zajistit, že váš kód není selhání s při spuštění na jiných rozhraních:</span><span class="sxs-lookup"><span data-stu-id="8f3e7-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end

---
title: Autorizace na základě zásad v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit a použít rutiny zásad autorizace pro vynucují požadavky pro autorizaci v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2017
uid: security/authorization/policies
ms.openlocfilehash: c2bc626b2dd341dda878a151def6b405884357d7
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665399"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="0bbe4-103">Autorizace na základě zásad v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0bbe4-103">Policy-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="0bbe4-104">Na pozadí [autorizace na základě rolí](xref:security/authorization/roles) a [autorizace na základě rolí](xref:security/authorization/claims) pomocí požadavku, obslužné rutiny požadavku a předem nakonfigurované zásady.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="0bbe4-105">Tyto stavební bloky podporují výraz vyhodnocení autorizace v kódu.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="0bbe4-106">Výsledkem je struktura bohatší, opakovaně použitelný, testovatelného autorizace.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="0bbe4-107">Zásady autorizace se skládá z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="0bbe4-108">Je registrován jako součást konfigurace služby ověřování v `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="0bbe4-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63,72)]

<span data-ttu-id="0bbe4-109">V předchozím příkladu se vytvoří zásadu "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="0bbe4-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="0bbe4-110">Má jeden požadavek&mdash;s minimálním stářím, který je poskytnut jako parametr s požadavkem.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

<span data-ttu-id="0bbe4-111">Zásady se použijí s použitím `[Authorize]` atribut s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-111">Policies are applied by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="0bbe4-112">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0bbe4-112">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="requirements"></a><span data-ttu-id="0bbe4-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="0bbe4-113">Requirements</span></span>

<span data-ttu-id="0bbe4-114">Požadavek na autorizaci je kolekce dat parametry, které zásady můžete použít k vyhodnocení aktuální hlavní název uživatele.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-114">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="0bbe4-115">V zásadách "AtLeast21" požadavek je jediný parametr&mdash;minimálním stářím.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-115">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="0bbe4-116">Implementuje požadavek [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je rozhraní prázdnou značku.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-116">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="0bbe4-117">Parametrizované minimální stáří požadavek může být implementován takto:</span><span class="sxs-lookup"><span data-stu-id="0bbe4-117">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="0bbe4-118">Zásad autorizace obsahuje více požadavků na autorizaci, všechny požadavky musí předat v pořadí pro hodnocení zásad proběhla úspěšně.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-118">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="0bbe4-119">Jinými slovy, více autorizace požadavky do jednoho autorizační zásady jsou považovány na **a** základ.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-119">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="0bbe4-120">Požadavek není nutné mít data nebo vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-120">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="0bbe4-121">Povolení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="0bbe4-121">Authorization handlers</span></span>

<span data-ttu-id="0bbe4-122">Je zodpovědná za vyhodnocení vlastností požadavek na obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-122">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="0bbe4-123">Obslužná rutina autorizace vyhodnotí požadavky proti zadaný [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, zda je povolen přístup.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-123">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="0bbe4-124">Může mít požadavek [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="0bbe4-124">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="0bbe4-125">Obslužná rutina může dědit [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je potřeba zpracovat.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-125">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="0bbe4-126">Alternativně může implementovat obslužnou rutinu [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jeden typ požadavku.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-126">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="0bbe4-127">Použijte obslužnou rutinu pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="0bbe4-127">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="0bbe4-128">Následuje příklad vztah 1: 1, ve kterém obslužnou rutinu minimální stáří využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="0bbe4-128">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="0bbe4-129">Předchozí kód určuje, zda má aktuální uživatel instančního objektu datum narození deklarace identity, který byl vydán známého a důvěryhodného vystavitele.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-129">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="0bbe4-130">Ověření nelze provést, pokud chybí deklarace identity, v takovém případě se vrátí dokončeného úkolu.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-130">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="0bbe4-131">Při deklaraci identity je k dispozici, se vypočítá věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-131">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="0bbe4-132">Pokud uživatel splňuje minimální stáří určené požadavku, autorizace se bude považovat za úspěšné.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-132">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="0bbe4-133">Když je ověření úspěšné, `context.Succeed` vyvolání splnění požadavku jako svůj jediný parametr.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-133">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="0bbe4-134">Použijte obslužnou rutinu pro několik požadavků</span><span class="sxs-lookup"><span data-stu-id="0bbe4-134">Use a handler for multiple requirements</span></span>

<span data-ttu-id="0bbe4-135">Následuje příklad vztah jeden mnoho, ve kterém obslužnou rutinu oprávnění dokáže zpracovat tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="0bbe4-135">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="0bbe4-136">Předchozí kód projde [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky není označena jako úspěšně dokončený.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-136">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="0bbe4-137">Pro `ReadPermission` požadavek, uživatel musí být vlastníkem nebo sponzor přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-137">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="0bbe4-138">V případě třídy `EditPermission` nebo `DeletePermission` požadavek, nezíská musí být vlastníkem pro přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-138">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="0bbe4-139">Zápis obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="0bbe4-139">Handler registration</span></span>

<span data-ttu-id="0bbe4-140">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-140">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="0bbe4-141">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0bbe4-141">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63-65,72)]

<span data-ttu-id="0bbe4-142">Předchozí kód registruje `MinimumAgeHandler` jako singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-142">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="0bbe4-143">Obslužné rutiny lze registrovat pomocí některé z integrovaného [služby životnosti](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="0bbe4-143">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="0bbe4-144">Co by měl vrátit obslužnou rutinu?</span><span class="sxs-lookup"><span data-stu-id="0bbe4-144">What should a handler return?</span></span>

<span data-ttu-id="0bbe4-145">Všimněte si, že `Handle` metodu [příklad obslužná rutina](#security-authorization-handler-example) nevrací žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-145">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="0bbe4-146">Jak se stavem úspěch nebo selhání uvedené?</span><span class="sxs-lookup"><span data-stu-id="0bbe4-146">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="0bbe4-147">Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)`, předá tento požadavek, který se úspěšně ověřilo.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-147">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="0bbe4-148">Obslužná rutina nemusí řešit selhání obecně platí, jako ostatní obslužné rutiny pro stejný požadavek může být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-148">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="0bbe4-149">Chcete-li zaručit selhání, i v případě ostatních obslužných rutin požadavek úspěšný, zavolejte `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-149">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="0bbe4-150">Pokud je nastavena na `false`, [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) vlastnosti (k dispozici v ASP.NET Core 1.1 nebo novější) zkratům provádění obslužné rutiny při `context.Fail` je volána.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-150">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="0bbe4-151">`InvokeHandlersAfterFailure` Výchozí hodnota je `true`, v takovém případě se nazývají všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-151">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span> <span data-ttu-id="0bbe4-152">To umožňuje požadavek na vytvoření vedlejší účinky, jako je například protokolování, které vždycky provedou i v případě `context.Fail` byla volána v jiné rutině.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-152">This allows requirements to produce side effects, such as logging, which always take place even if `context.Fail` has been called in another handler.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="0bbe4-153">Proč byste měli více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="0bbe4-153">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="0bbe4-154">V případech, kde chcete vyhodnocení na **nebo** základ, implementovat více obslužných rutin pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-154">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="0bbe4-155">Microsoft má například dveří, které otevřít pouze pomocí klíčů karty.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-155">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="0bbe4-156">Ponecháte-li karta s klíčem v domácnostech, recepční vytiskne dočasné štítku a otevře dveře za vás.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-156">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="0bbe4-157">V tomto scénáři bude mít jeden požadavek *BuildingEntry*, ale více obslužných rutin, každý z nich zkoumání jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-157">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="0bbe4-158">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="0bbe4-158">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="0bbe4-159">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="0bbe4-159">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="0bbe4-160">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="0bbe4-160">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="0bbe4-161">Ujistěte se, že jsou oba obslužné rutiny [zaregistrovaný](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="0bbe4-161">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="0bbe4-162">Pokud je buď obslužná rutina úspěšné při zásady vyhodnotí `BuildingEntryRequirement`, hodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-162">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="0bbe4-163">Používat funkci func pro splnění zásad</span><span class="sxs-lookup"><span data-stu-id="0bbe4-163">Using a func to fulfill a policy</span></span>

<span data-ttu-id="0bbe4-164">Může nastat situace, ve které splňující zásady je jednoduchý vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-164">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="0bbe4-165">Je možné zadat `Func<AuthorizationHandlerContext, bool>` při konfiguraci zásad s `RequireAssertion` tvůrce zásad.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-165">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="0bbe4-166">Například předchozí `BadgeEntryHandler` měl by být přepsán následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="0bbe4-166">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=52-53,57-63)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="0bbe4-167">Přístup k kontext požadavku MVC v obslužné rutině</span><span class="sxs-lookup"><span data-stu-id="0bbe4-167">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="0bbe4-168">`HandleRequirementAsync` Implementovat v obslužné rutině autorizační metoda má dva parametry: `AuthorizationHandlerContext` a `TRequirement` obsluhujete.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-168">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="0bbe4-169">Architektur, jako je MVC nebo Jabbr jsou můžete přidat libovolný objekt `Resource` vlastnost `AuthorizationHandlerContext` k předávání dalších informací.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-169">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="0bbe4-170">Například MVC předá instance [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) v `Resource` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-170">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="0bbe4-171">Tato vlastnost poskytuje přístup k `HttpContext`, `RouteData`a všechno, co je jinak poskytované MVC a stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-171">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="0bbe4-172">Použití `Resource` vlastnost je konkrétní rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-172">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="0bbe4-173">Pomocí informací `Resource` vlastnost omezuje vaše zásady autorizace pro konkrétní platformy.</span><span class="sxs-lookup"><span data-stu-id="0bbe4-173">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="0bbe4-174">Měli přetypovat `Resource` pomocí vlastnosti `is` – klíčové slovo a potom potvrdit přetypování proběhla úspěšně, aby se váš kód nebude k chybě s `InvalidCastException` při spuštění na ostatní platformy:</span><span class="sxs-lookup"><span data-stu-id="0bbe4-174">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

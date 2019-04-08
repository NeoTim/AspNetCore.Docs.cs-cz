---
title: Autorizace na základě zásad v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit a použít rutiny zásad autorizace pro vynucují požadavky pro autorizaci v aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: security/authorization/policies
ms.openlocfilehash: ea9d687d3810c104d5b3fa39033849c21569709b
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068167"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="74852-103">Autorizace na základě zásad v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="74852-103">Policy-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="74852-104">Na pozadí [autorizace na základě rolí](xref:security/authorization/roles) a [autorizace na základě rolí](xref:security/authorization/claims) pomocí požadavku, obslužné rutiny požadavku a předem nakonfigurované zásady.</span><span class="sxs-lookup"><span data-stu-id="74852-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="74852-105">Tyto stavební bloky podporují výraz vyhodnocení autorizace v kódu.</span><span class="sxs-lookup"><span data-stu-id="74852-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="74852-106">Výsledkem je struktura bohatší, opakovaně použitelný, testovatelného autorizace.</span><span class="sxs-lookup"><span data-stu-id="74852-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="74852-107">Zásady autorizace se skládá z jednoho nebo více požadavků.</span><span class="sxs-lookup"><span data-stu-id="74852-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="74852-108">Je registrován jako součást konfigurace služby ověřování v `Startup.ConfigureServices` metody:</span><span class="sxs-lookup"><span data-stu-id="74852-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="74852-109">V předchozím příkladu se vytvoří zásadu "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="74852-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="74852-110">Má jeden požadavek&mdash;s minimálním stářím, který je poskytnut jako parametr s požadavkem.</span><span class="sxs-lookup"><span data-stu-id="74852-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="74852-111">Přiřazení zásad pro kontrolery MVC</span><span class="sxs-lookup"><span data-stu-id="74852-111">Applying policies to MVC controllers</span></span>

<span data-ttu-id="74852-112">Pokud používáte pro stránky Razor, přečtěte si téma [přiřazení zásad pro stránky Razor](#applying-policies-to-razor-pages) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="74852-112">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="74852-113">Zásady se použijí k řadičům pomocí `[Authorize]` atribut s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="74852-113">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="74852-114">Příklad:</span><span class="sxs-lookup"><span data-stu-id="74852-114">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="74852-115">Přiřazení zásad pro stránky Razor</span><span class="sxs-lookup"><span data-stu-id="74852-115">Applying policies to Razor Pages</span></span>

<span data-ttu-id="74852-116">Zásady se použijí pro stránky Razor pomocí `[Authorize]` atribut s názvem zásady.</span><span class="sxs-lookup"><span data-stu-id="74852-116">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="74852-117">Příklad:</span><span class="sxs-lookup"><span data-stu-id="74852-117">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="74852-118">Zásady můžete použít také pro stránky Razor pomocí [autorizace konvence](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="74852-118">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="74852-119">Požadavky</span><span class="sxs-lookup"><span data-stu-id="74852-119">Requirements</span></span>

<span data-ttu-id="74852-120">Požadavek na autorizaci je kolekce dat parametry, které zásady můžete použít k vyhodnocení aktuální hlavní název uživatele.</span><span class="sxs-lookup"><span data-stu-id="74852-120">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="74852-121">V zásadách "AtLeast21" požadavek je jediný parametr&mdash;minimálním stářím.</span><span class="sxs-lookup"><span data-stu-id="74852-121">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="74852-122">Implementuje požadavek [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), což je rozhraní prázdnou značku.</span><span class="sxs-lookup"><span data-stu-id="74852-122">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="74852-123">Parametrizované minimální stáří požadavek může být implementován takto:</span><span class="sxs-lookup"><span data-stu-id="74852-123">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="74852-124">Zásad autorizace obsahuje více požadavků na autorizaci, všechny požadavky musí předat v pořadí pro hodnocení zásad proběhla úspěšně.</span><span class="sxs-lookup"><span data-stu-id="74852-124">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="74852-125">Jinými slovy, více autorizace požadavky do jednoho autorizační zásady jsou považovány na **a** základ.</span><span class="sxs-lookup"><span data-stu-id="74852-125">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="74852-126">Požadavek není nutné mít data nebo vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="74852-126">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="74852-127">Povolení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="74852-127">Authorization handlers</span></span>

<span data-ttu-id="74852-128">Je zodpovědná za vyhodnocení vlastností požadavek na obslužnou rutinu ověřování.</span><span class="sxs-lookup"><span data-stu-id="74852-128">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="74852-129">Obslužná rutina autorizace vyhodnotí požadavky proti zadaný [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) k určení, zda je povolen přístup.</span><span class="sxs-lookup"><span data-stu-id="74852-129">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="74852-130">Může mít požadavek [více obslužných rutin](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="74852-130">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="74852-131">Obslužná rutina může dědit [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), kde `TRequirement` je potřeba zpracovat.</span><span class="sxs-lookup"><span data-stu-id="74852-131">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="74852-132">Alternativně může implementovat obslužnou rutinu [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pro zpracování více než jeden typ požadavku.</span><span class="sxs-lookup"><span data-stu-id="74852-132">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="74852-133">Použijte obslužnou rutinu pro jeden požadavek</span><span class="sxs-lookup"><span data-stu-id="74852-133">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="74852-134">Následuje příklad vztah 1: 1, ve kterém obslužnou rutinu minimální stáří využívá jeden požadavek:</span><span class="sxs-lookup"><span data-stu-id="74852-134">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="74852-135">Předchozí kód určuje, zda má aktuální uživatel instančního objektu datum narození deklarace identity, který byl vydán známého a důvěryhodného vystavitele.</span><span class="sxs-lookup"><span data-stu-id="74852-135">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="74852-136">Ověření nelze provést, pokud chybí deklarace identity, v takovém případě se vrátí dokončeného úkolu.</span><span class="sxs-lookup"><span data-stu-id="74852-136">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="74852-137">Při deklaraci identity je k dispozici, se vypočítá věk uživatele.</span><span class="sxs-lookup"><span data-stu-id="74852-137">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="74852-138">Pokud uživatel splňuje minimální stáří určené požadavku, autorizace se bude považovat za úspěšné.</span><span class="sxs-lookup"><span data-stu-id="74852-138">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="74852-139">Když je ověření úspěšné, `context.Succeed` vyvolání splnění požadavku jako svůj jediný parametr.</span><span class="sxs-lookup"><span data-stu-id="74852-139">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="74852-140">Použijte obslužnou rutinu pro několik požadavků</span><span class="sxs-lookup"><span data-stu-id="74852-140">Use a handler for multiple requirements</span></span>

<span data-ttu-id="74852-141">Následuje příklad vztah jeden mnoho, ve kterém obslužnou rutinu oprávnění dokáže zpracovat tři různé typy požadavků:</span><span class="sxs-lookup"><span data-stu-id="74852-141">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="74852-142">Předchozí kód projde [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;vlastnost obsahující požadavky není označena jako úspěšně dokončený.</span><span class="sxs-lookup"><span data-stu-id="74852-142">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="74852-143">Pro `ReadPermission` požadavek, uživatel musí být vlastníkem nebo sponzor přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="74852-143">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="74852-144">V případě třídy `EditPermission` nebo `DeletePermission` požadavek, nezíská musí být vlastníkem pro přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="74852-144">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="74852-145">Zápis obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="74852-145">Handler registration</span></span>

<span data-ttu-id="74852-146">Obslužné rutiny jsou registrovány v kolekci služeb během konfigurace.</span><span class="sxs-lookup"><span data-stu-id="74852-146">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="74852-147">Příklad:</span><span class="sxs-lookup"><span data-stu-id="74852-147">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="74852-148">Předchozí kód registruje `MinimumAgeHandler` jako singleton vyvoláním `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="74852-148">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="74852-149">Obslužné rutiny lze registrovat pomocí některé z integrovaného [služby životnosti](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="74852-149">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="74852-150">Co by měl vrátit obslužnou rutinu?</span><span class="sxs-lookup"><span data-stu-id="74852-150">What should a handler return?</span></span>

<span data-ttu-id="74852-151">Všimněte si, že `Handle` metodu [příklad obslužná rutina](#security-authorization-handler-example) nevrací žádnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="74852-151">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="74852-152">Jak se stavem úspěch nebo selhání uvedené?</span><span class="sxs-lookup"><span data-stu-id="74852-152">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="74852-153">Obslužná rutina indikuje úspěch voláním `context.Succeed(IAuthorizationRequirement requirement)`, předá tento požadavek, který se úspěšně ověřilo.</span><span class="sxs-lookup"><span data-stu-id="74852-153">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="74852-154">Obslužná rutina nemusí řešit selhání obecně platí, jako ostatní obslužné rutiny pro stejný požadavek může být úspěšné.</span><span class="sxs-lookup"><span data-stu-id="74852-154">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="74852-155">Chcete-li zaručit selhání, i v případě ostatních obslužných rutin požadavek úspěšný, zavolejte `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="74852-155">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="74852-156">Pokud obslužná rutina volá `context.Succeed` nebo `context.Fail`, stále se nazývají všechny ostatní obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="74852-156">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="74852-157">To umožňuje požadavek na vytvoření vedlejší účinky, jako je například protokolování, které dojde i v případě, že má jinou obslužnou rutinu úspěšně ověřen nebo požadavku se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="74852-157">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="74852-158">Pokud je nastavena na `false`, [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) vlastnosti (k dispozici v ASP.NET Core 1.1 nebo novější) zkratům provádění obslužné rutiny při `context.Fail` je volána.</span><span class="sxs-lookup"><span data-stu-id="74852-158">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> `InvokeHandlersAfterFailure` <span data-ttu-id="74852-159">Výchozí hodnota je `true`, v takovém případě se nazývají všechny obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="74852-159">defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="74852-160">I když se ověřování nezdaří, jsou volány obslužné rutiny autorizace.</span><span class="sxs-lookup"><span data-stu-id="74852-160">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="74852-161">Proč byste měli více obslužných rutin pro požadavek?</span><span class="sxs-lookup"><span data-stu-id="74852-161">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="74852-162">V případech, kde chcete vyhodnocení na **nebo** základ, implementovat více obslužných rutin pro jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="74852-162">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="74852-163">Microsoft má například dveří, které otevřít pouze pomocí klíčů karty.</span><span class="sxs-lookup"><span data-stu-id="74852-163">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="74852-164">Ponecháte-li karta s klíčem v domácnostech, recepční vytiskne dočasné štítku a otevře dveře za vás.</span><span class="sxs-lookup"><span data-stu-id="74852-164">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="74852-165">V tomto scénáři bude mít jeden požadavek *BuildingEntry*, ale více obslužných rutin, každý z nich zkoumání jeden požadavek.</span><span class="sxs-lookup"><span data-stu-id="74852-165">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

*<span data-ttu-id="74852-166">BuildingEntryRequirement.cs</span><span class="sxs-lookup"><span data-stu-id="74852-166">BuildingEntryRequirement.cs</span></span>*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*<span data-ttu-id="74852-167">BadgeEntryHandler.cs</span><span class="sxs-lookup"><span data-stu-id="74852-167">BadgeEntryHandler.cs</span></span>*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*<span data-ttu-id="74852-168">TemporaryStickerHandler.cs</span><span class="sxs-lookup"><span data-stu-id="74852-168">TemporaryStickerHandler.cs</span></span>*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="74852-169">Ujistěte se, že jsou oba obslužné rutiny [zaregistrovaný](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="74852-169">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="74852-170">Pokud je buď obslužná rutina úspěšné při zásady vyhodnotí `BuildingEntryRequirement`, hodnocení zásad úspěšné.</span><span class="sxs-lookup"><span data-stu-id="74852-170">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="74852-171">Používat funkci func pro splnění zásad</span><span class="sxs-lookup"><span data-stu-id="74852-171">Using a func to fulfill a policy</span></span>

<span data-ttu-id="74852-172">Může nastat situace, ve které splňující zásady je jednoduchý vyjádřit v kódu.</span><span class="sxs-lookup"><span data-stu-id="74852-172">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="74852-173">Je možné zadat `Func<AuthorizationHandlerContext, bool>` při konfiguraci zásad s `RequireAssertion` tvůrce zásad.</span><span class="sxs-lookup"><span data-stu-id="74852-173">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="74852-174">Například předchozí `BadgeEntryHandler` měl by být přepsán následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="74852-174">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="74852-175">Přístup k kontext požadavku MVC v obslužné rutině</span><span class="sxs-lookup"><span data-stu-id="74852-175">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="74852-176">`HandleRequirementAsync` Implementovat v obslužné rutině autorizační metoda má dva parametry: `AuthorizationHandlerContext` a `TRequirement` obsluhujete.</span><span class="sxs-lookup"><span data-stu-id="74852-176">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="74852-177">Architektur, jako je MVC nebo Jabbr jsou můžete přidat libovolný objekt `Resource` vlastnost `AuthorizationHandlerContext` k předávání dalších informací.</span><span class="sxs-lookup"><span data-stu-id="74852-177">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="74852-178">Například MVC předá instance [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) v `Resource` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="74852-178">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="74852-179">Tato vlastnost poskytuje přístup k `HttpContext`, `RouteData`a všechno, co je jinak poskytované MVC a stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="74852-179">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="74852-180">Použití `Resource` vlastnost je konkrétní rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="74852-180">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="74852-181">Pomocí informací `Resource` vlastnost omezuje vaše zásady autorizace pro konkrétní platformy.</span><span class="sxs-lookup"><span data-stu-id="74852-181">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="74852-182">Měli přetypovat `Resource` pomocí vlastnosti `is` – klíčové slovo a potom potvrdit přetypování proběhla úspěšně, aby se váš kód nebude k chybě s `InvalidCastException` při spuštění na ostatní platformy:</span><span class="sxs-lookup"><span data-stu-id="74852-182">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

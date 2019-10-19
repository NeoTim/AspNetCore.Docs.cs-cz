---
title: Ověřování na základě prostředků v ASP.NET Core
author: scottaddie
description: Naučte se implementovat autorizaci na základě prostředků v ASP.NET Core aplikaci, když atribut autorizovat nestačí.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: 835592521c714e270595e1448ae6e0aed1707b77
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72590008"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="80bca-103">Ověřování na základě prostředků v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80bca-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="80bca-104">Autorizační strategie závisí na prostředku, ke kterému se přistupoval.</span><span class="sxs-lookup"><span data-stu-id="80bca-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="80bca-105">Vezměte v úvahu dokument, který má vlastnost Author.</span><span class="sxs-lookup"><span data-stu-id="80bca-105">Consider a document that has an author property.</span></span> <span data-ttu-id="80bca-106">Dokument může aktualizovat pouze autor.</span><span class="sxs-lookup"><span data-stu-id="80bca-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="80bca-107">V důsledku toho musí být dokument načten z úložiště dat ještě před tím, než může dojít k vyhodnocení autorizace.</span><span class="sxs-lookup"><span data-stu-id="80bca-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="80bca-108">K vyhodnocení atributu dojde před datovou vazbou a před spuštěním obslužné rutiny stránky nebo akce, která dokument načte.</span><span class="sxs-lookup"><span data-stu-id="80bca-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="80bca-109">Z těchto důvodů nestačí deklarativní autorizace s atributem `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="80bca-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="80bca-110">Místo toho můžete vyvolat vlastní metodu autorizace &mdash;a stylu, který se označuje jako *imperativní autorizace*.</span><span class="sxs-lookup"><span data-stu-id="80bca-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

<span data-ttu-id="80bca-111">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="80bca-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="80bca-112">[Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací](xref:security/authorization/secure-data) obsahuje ukázkovou aplikaci, která používá autorizaci založenou na prostředku.</span><span class="sxs-lookup"><span data-stu-id="80bca-112">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="80bca-113">Použít imperativní autorizaci</span><span class="sxs-lookup"><span data-stu-id="80bca-113">Use imperative authorization</span></span>

<span data-ttu-id="80bca-114">Autorizace je implementována jako služba [načetl služby IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) a je registrována v kolekci služeb v rámci třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="80bca-114">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="80bca-115">Služba je zpřístupněna prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection) na obslužné rutiny stránky nebo akce.</span><span class="sxs-lookup"><span data-stu-id="80bca-115">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="80bca-116">`IAuthorizationService` má dvě přetížení metod `AuthorizeAsync`: jedno přijetí prostředku a název zásady a druhý přijetí prostředku a seznam požadavků, které se mají vyhodnotit.</span><span class="sxs-lookup"><span data-stu-id="80bca-116">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

<span data-ttu-id="80bca-117">V následujícím příkladu je prostředek, který má být zabezpečen, načten do vlastního objektu `Document`.</span><span class="sxs-lookup"><span data-stu-id="80bca-117">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="80bca-118">Je vyvolána `AuthorizeAsync` přetížení, které určuje, zda má aktuální uživatel povolený úpravu zadaného dokumentu.</span><span class="sxs-lookup"><span data-stu-id="80bca-118">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="80bca-119">Vlastní zásady autorizace "EditPolicy" se připravují na rozhodnutí.</span><span class="sxs-lookup"><span data-stu-id="80bca-119">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="80bca-120">Další informace o vytváření zásad autorizace najdete v tématu věnovaném [vlastním autorizaci na základě zásad](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="80bca-120">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="80bca-121">Následující ukázky kódu předpokládají, že ověřování běželo a nastavilo vlastnost `User`.</span><span class="sxs-lookup"><span data-stu-id="80bca-121">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="80bca-122">Zápis obslužné rutiny založené na prostředku</span><span class="sxs-lookup"><span data-stu-id="80bca-122">Write a resource-based handler</span></span>

<span data-ttu-id="80bca-123">Zápis obslužné rutiny pro autorizaci založenou na prostředku není mnohem jiný než [zápis obslužné rutiny s jednoduchými požadavky](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span><span class="sxs-lookup"><span data-stu-id="80bca-123">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="80bca-124">Vytvořte vlastní třídu požadavků a implementujte třídu obslužné rutiny požadavků.</span><span class="sxs-lookup"><span data-stu-id="80bca-124">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="80bca-125">Další informace o vytváření třídy požadavku najdete v tématu [požadavky](xref:security/authorization/policies#requirements).</span><span class="sxs-lookup"><span data-stu-id="80bca-125">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="80bca-126">Třída obslužné rutiny určuje jak požadavek, tak i typ prostředku.</span><span class="sxs-lookup"><span data-stu-id="80bca-126">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="80bca-127">Například obslužná rutina, která využívá `SameAuthorRequirement` a prostředek `Document`, je následující:</span><span class="sxs-lookup"><span data-stu-id="80bca-127">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="80bca-128">V předchozím příkladu představte, že `SameAuthorRequirement` je zvláštní případ obecnější `SpecificAuthorRequirement` třídy.</span><span class="sxs-lookup"><span data-stu-id="80bca-128">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="80bca-129">Třída `SpecificAuthorRequirement` (není zobrazená) obsahuje vlastnost `Name` představující jméno autora.</span><span class="sxs-lookup"><span data-stu-id="80bca-129">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="80bca-130">Vlastnost `Name` lze nastavit na aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="80bca-130">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="80bca-131">Zaregistrujte požadavek a obslužnou rutinu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="80bca-131">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a><span data-ttu-id="80bca-132">Provozní požadavky</span><span class="sxs-lookup"><span data-stu-id="80bca-132">Operational requirements</span></span>

<span data-ttu-id="80bca-133">Pokud provádíte rozhodnutí na základě výsledků operací CRUD (vytváření, čtení, aktualizace, odstranění), použijte pomocnou třídu [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) .</span><span class="sxs-lookup"><span data-stu-id="80bca-133">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="80bca-134">Tato třída umožňuje napsat jednu obslužnou rutinu místo jednotlivé třídy pro každý typ operace.</span><span class="sxs-lookup"><span data-stu-id="80bca-134">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="80bca-135">Pokud ho chcete použít, zadejte několik názvů operací:</span><span class="sxs-lookup"><span data-stu-id="80bca-135">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="80bca-136">Obslužná rutina je implementována následujícím způsobem, a to pomocí `OperationAuthorizationRequirement` požadavku a prostředku `Document`:</span><span class="sxs-lookup"><span data-stu-id="80bca-136">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="80bca-137">Předchozí obslužná rutina ověří operaci pomocí prostředku, identity uživatele a vlastnosti `Name` požadavku.</span><span class="sxs-lookup"><span data-stu-id="80bca-137">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a><span data-ttu-id="80bca-138">Vyvolávat výzvu a zakazují je pomocí obslužné rutiny provozního prostředku</span><span class="sxs-lookup"><span data-stu-id="80bca-138">Challenge and forbid with an operational resource handler</span></span>

<span data-ttu-id="80bca-139">V této části se dozvíte, jak jsou zpracovávány výsledky akce výzva a zakázat, a jak se liší výzvy a zakazující se.</span><span class="sxs-lookup"><span data-stu-id="80bca-139">This section shows how the challenge and forbid action results are processed and how challenge and forbid differ.</span></span>

<span data-ttu-id="80bca-140">Pro volání obslužné rutiny provozního prostředku určete operaci při vyvolání `AuthorizeAsync` v obslužné rutině nebo akci stránky.</span><span class="sxs-lookup"><span data-stu-id="80bca-140">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="80bca-141">Následující příklad určuje, zda má ověřený uživatel oprávnění zobrazit zadaný dokument.</span><span class="sxs-lookup"><span data-stu-id="80bca-141">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="80bca-142">Následující ukázky kódu předpokládají, že ověřování běželo a nastavilo vlastnost `User`.</span><span class="sxs-lookup"><span data-stu-id="80bca-142">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="80bca-143">Pokud se autorizace zdaří, vrátí se stránka pro zobrazení dokumentu.</span><span class="sxs-lookup"><span data-stu-id="80bca-143">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="80bca-144">Pokud se autorizace nepovede, ale uživatel bude ověřený, vrátí `ForbidResult` informuje jakýkoliv middleware pro ověřování, který autorizace nezdařila.</span><span class="sxs-lookup"><span data-stu-id="80bca-144">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="80bca-145">Pokud je nutné provést ověření, je vrácena `ChallengeResult`.</span><span class="sxs-lookup"><span data-stu-id="80bca-145">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="80bca-146">Pro klienty interaktivního prohlížeče může být vhodné přesměrovat uživatele na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="80bca-146">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="80bca-147">Pokud je ověření úspěšné, vrátí se zobrazení dokumentu.</span><span class="sxs-lookup"><span data-stu-id="80bca-147">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="80bca-148">Pokud se autorizace nezdařila, vrácení `ChallengeResult` informuje jakýkoliv middleware ověřování, že Autorizace selhala, a middleware může přijmout příslušnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="80bca-148">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="80bca-149">Vhodná odpověď může vrátit stavový kód 401 nebo 403.</span><span class="sxs-lookup"><span data-stu-id="80bca-149">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="80bca-150">U interaktivních klientů prohlížeče může to znamenat přesměrování uživatele na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="80bca-150">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end

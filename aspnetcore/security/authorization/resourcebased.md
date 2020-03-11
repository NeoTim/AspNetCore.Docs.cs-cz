---
title: Ověřování na základě prostředků v ASP.NET Core
author: scottaddie
description: Naučte se implementovat autorizaci na základě prostředků v ASP.NET Core aplikaci, když atribut autorizovat nestačí.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: 2be611c754583d996db7107f341b1be03cef73cf
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664799"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="6a568-103">Ověřování na základě prostředků v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a568-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="6a568-104">Autorizační strategie závisí na prostředku, ke kterému se přistupoval.</span><span class="sxs-lookup"><span data-stu-id="6a568-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="6a568-105">Vezměte v úvahu dokument, který má vlastnost Author.</span><span class="sxs-lookup"><span data-stu-id="6a568-105">Consider a document that has an author property.</span></span> <span data-ttu-id="6a568-106">Dokument může aktualizovat pouze autor.</span><span class="sxs-lookup"><span data-stu-id="6a568-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="6a568-107">V důsledku toho musí být dokument načten z úložiště dat ještě před tím, než může dojít k vyhodnocení autorizace.</span><span class="sxs-lookup"><span data-stu-id="6a568-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="6a568-108">K vyhodnocení atributu dojde před datovou vazbou a před spuštěním obslužné rutiny stránky nebo akce, která dokument načte.</span><span class="sxs-lookup"><span data-stu-id="6a568-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="6a568-109">Z těchto důvodů nestačí deklarativní autorizace s atributem `[Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="6a568-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="6a568-110">Místo toho můžete vyvolat vlastní metodu autorizace&mdash;stylu, který se označuje jako *imperativní autorizace*.</span><span class="sxs-lookup"><span data-stu-id="6a568-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="6a568-111">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6a568-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
<span data-ttu-id="6a568-112">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6a568-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
<span data-ttu-id="6a568-113">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6a568-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

<span data-ttu-id="6a568-114">[Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací](xref:security/authorization/secure-data) obsahuje ukázkovou aplikaci, která používá autorizaci založenou na prostředku.</span><span class="sxs-lookup"><span data-stu-id="6a568-114">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="6a568-115">Použít imperativní autorizaci</span><span class="sxs-lookup"><span data-stu-id="6a568-115">Use imperative authorization</span></span>

<span data-ttu-id="6a568-116">Autorizace je implementována jako služba [načetl služby IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) a je registrována v kolekci služeb v rámci třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="6a568-116">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="6a568-117">Služba je zpřístupněna prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection) na obslužné rutiny stránky nebo akce.</span><span class="sxs-lookup"><span data-stu-id="6a568-117">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="6a568-118">`IAuthorizationService` má dvě přetížení metod `AuthorizeAsync`: jedno přijetí prostředku a název zásady a druhý přijetí prostředku a seznam požadavků, které se mají vyhodnotit.</span><span class="sxs-lookup"><span data-stu-id="6a568-118">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

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

<span data-ttu-id="6a568-119">V následujícím příkladu je prostředek, který má být zabezpečen, načten do vlastního objektu `Document`.</span><span class="sxs-lookup"><span data-stu-id="6a568-119">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="6a568-120">Je vyvolána `AuthorizeAsync` přetížení, které určuje, zda má aktuální uživatel povolený úpravu zadaného dokumentu.</span><span class="sxs-lookup"><span data-stu-id="6a568-120">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="6a568-121">Vlastní zásady autorizace "EditPolicy" se připravují na rozhodnutí.</span><span class="sxs-lookup"><span data-stu-id="6a568-121">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="6a568-122">Další informace o vytváření zásad autorizace najdete v tématu věnovaném [vlastním autorizaci na základě zásad](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="6a568-122">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="6a568-123">Následující ukázky kódu předpokládají, že ověřování běželo a nastavilo vlastnost `User`.</span><span class="sxs-lookup"><span data-stu-id="6a568-123">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="6a568-124">Zápis obslužné rutiny založené na prostředku</span><span class="sxs-lookup"><span data-stu-id="6a568-124">Write a resource-based handler</span></span>

<span data-ttu-id="6a568-125">Zápis obslužné rutiny pro autorizaci založenou na prostředku není mnohem jiný než [zápis obslužné rutiny s jednoduchými požadavky](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span><span class="sxs-lookup"><span data-stu-id="6a568-125">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="6a568-126">Vytvořte vlastní třídu požadavků a implementujte třídu obslužné rutiny požadavků.</span><span class="sxs-lookup"><span data-stu-id="6a568-126">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="6a568-127">Další informace o vytváření třídy požadavku najdete v tématu [požadavky](xref:security/authorization/policies#requirements).</span><span class="sxs-lookup"><span data-stu-id="6a568-127">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="6a568-128">Třída obslužné rutiny určuje jak požadavek, tak i typ prostředku.</span><span class="sxs-lookup"><span data-stu-id="6a568-128">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="6a568-129">Například obslužná rutina, která využívá `SameAuthorRequirement` a prostředek `Document`, je následující:</span><span class="sxs-lookup"><span data-stu-id="6a568-129">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="6a568-130">V předchozím příkladu představte, že `SameAuthorRequirement` je zvláštní případ obecnější `SpecificAuthorRequirement` třídy.</span><span class="sxs-lookup"><span data-stu-id="6a568-130">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="6a568-131">Třída `SpecificAuthorRequirement` (není zobrazená) obsahuje vlastnost `Name` představující jméno autora.</span><span class="sxs-lookup"><span data-stu-id="6a568-131">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="6a568-132">Vlastnost `Name` lze nastavit na aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="6a568-132">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="6a568-133">Zaregistrujte požadavek a obslužnou rutinu v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6a568-133">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a><span data-ttu-id="6a568-134">Provozní požadavky</span><span class="sxs-lookup"><span data-stu-id="6a568-134">Operational requirements</span></span>

<span data-ttu-id="6a568-135">Pokud provádíte rozhodnutí na základě výsledků operací CRUD (vytváření, čtení, aktualizace, odstranění), použijte pomocnou třídu [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) .</span><span class="sxs-lookup"><span data-stu-id="6a568-135">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="6a568-136">Tato třída umožňuje napsat jednu obslužnou rutinu místo jednotlivé třídy pro každý typ operace.</span><span class="sxs-lookup"><span data-stu-id="6a568-136">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="6a568-137">Pokud ho chcete použít, zadejte několik názvů operací:</span><span class="sxs-lookup"><span data-stu-id="6a568-137">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="6a568-138">Obslužná rutina je implementována následujícím způsobem, a to pomocí `OperationAuthorizationRequirement` požadavku a prostředku `Document`:</span><span class="sxs-lookup"><span data-stu-id="6a568-138">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="6a568-139">Předchozí obslužná rutina ověří operaci pomocí prostředku, identity uživatele a vlastnosti `Name` požadavku.</span><span class="sxs-lookup"><span data-stu-id="6a568-139">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a><span data-ttu-id="6a568-140">Vyvolávat výzvu a zakazují je pomocí obslužné rutiny provozního prostředku</span><span class="sxs-lookup"><span data-stu-id="6a568-140">Challenge and forbid with an operational resource handler</span></span>

<span data-ttu-id="6a568-141">V této části se dozvíte, jak jsou zpracovávány výsledky akce výzva a zakázat, a jak se liší výzvy a zakazující se.</span><span class="sxs-lookup"><span data-stu-id="6a568-141">This section shows how the challenge and forbid action results are processed and how challenge and forbid differ.</span></span>

<span data-ttu-id="6a568-142">Pro volání obslužné rutiny provozního prostředku určete operaci při vyvolání `AuthorizeAsync` v obslužné rutině nebo akci stránky.</span><span class="sxs-lookup"><span data-stu-id="6a568-142">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="6a568-143">Následující příklad určuje, zda má ověřený uživatel oprávnění zobrazit zadaný dokument.</span><span class="sxs-lookup"><span data-stu-id="6a568-143">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="6a568-144">Následující ukázky kódu předpokládají, že ověřování běželo a nastavilo vlastnost `User`.</span><span class="sxs-lookup"><span data-stu-id="6a568-144">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="6a568-145">Pokud se autorizace zdaří, vrátí se stránka pro zobrazení dokumentu.</span><span class="sxs-lookup"><span data-stu-id="6a568-145">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="6a568-146">Pokud se autorizace nepovede, ale uživatel bude ověřený, vrátí `ForbidResult` informuje jakýkoliv middleware pro ověřování, který autorizace nezdařila.</span><span class="sxs-lookup"><span data-stu-id="6a568-146">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="6a568-147">Pokud je nutné provést ověření, je vrácena `ChallengeResult`.</span><span class="sxs-lookup"><span data-stu-id="6a568-147">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="6a568-148">Pro klienty interaktivního prohlížeče může být vhodné přesměrovat uživatele na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="6a568-148">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="6a568-149">Pokud je ověření úspěšné, vrátí se zobrazení dokumentu.</span><span class="sxs-lookup"><span data-stu-id="6a568-149">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="6a568-150">Pokud se autorizace nezdařila, vrácení `ChallengeResult` informuje jakýkoliv middleware ověřování, že Autorizace selhala, a middleware může přijmout příslušnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="6a568-150">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="6a568-151">Vhodná odpověď může vrátit stavový kód 401 nebo 403.</span><span class="sxs-lookup"><span data-stu-id="6a568-151">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="6a568-152">U interaktivních klientů prohlížeče může to znamenat přesměrování uživatele na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="6a568-152">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end

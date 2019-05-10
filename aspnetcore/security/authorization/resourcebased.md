---
title: Autorizace na základě prostředků v ASP.NET Core
author: scottaddie
description: Zjistěte, jak implementovat ověřování na základě prostředků v aplikaci ASP.NET Core atribut Authorize nestačí.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: afc152ea677cab42d57bd642b4821159f125117e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898341"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="15369-103">Autorizace na základě prostředků v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="15369-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="15369-104">Strategii autorizace závisí na prostředek, ke kterému přistupujete.</span><span class="sxs-lookup"><span data-stu-id="15369-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="15369-105">Vezměte v úvahu dokument, který se má vytvořit vlastnost.</span><span class="sxs-lookup"><span data-stu-id="15369-105">Consider a document that has an author property.</span></span> <span data-ttu-id="15369-106">Pouze autor můžou aktualizovat dokument.</span><span class="sxs-lookup"><span data-stu-id="15369-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="15369-107">V důsledku toho dokumentu musí načíst z úložiště dat, než dojde k vyhodnocení autorizace.</span><span class="sxs-lookup"><span data-stu-id="15369-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="15369-108">Před datové vazby a před spuštěním obslužná rutina stránky nebo akci, která načte dokument dojde k vyhodnocení atribut.</span><span class="sxs-lookup"><span data-stu-id="15369-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="15369-109">Z těchto důvodů, deklarativní autorizace pomocí `[Authorize]` atribut nebude stačit.</span><span class="sxs-lookup"><span data-stu-id="15369-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="15369-110">Místo toho můžete vyvolat vlastní autorizační metoda&mdash;styl říká *imperativní autorizace*.</span><span class="sxs-lookup"><span data-stu-id="15369-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

<span data-ttu-id="15369-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="15369-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="15369-112">[Vytvoření aplikace ASP.NET Core s uživatelskými daty chráněnými autorizací](xref:security/authorization/secure-data) obsahuje ukázkovou aplikaci, která používá ověřování založené na resource.</span><span class="sxs-lookup"><span data-stu-id="15369-112">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="15369-113">Použití imperativní autorizace</span><span class="sxs-lookup"><span data-stu-id="15369-113">Use imperative authorization</span></span>

<span data-ttu-id="15369-114">Autorizace je implementovaný jako [služby IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) služby a je registrován v kolekci služby v rámci `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="15369-114">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="15369-115">Služba je k dispozici prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection) obslužných rutin stránky nebo provádět akce.</span><span class="sxs-lookup"><span data-stu-id="15369-115">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="15369-116">`IAuthorizationService` má dva `AuthorizeAsync` přetížení metody: jedna přijímá prostředku a název zásady a druhý přijímá prostředku a seznam všech požadavků k vyhodnocení.</span><span class="sxs-lookup"><span data-stu-id="15369-116">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

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

<span data-ttu-id="15369-117">V následujícím příkladu je prostředek, který má být zabezpečeny načten do vlastní `Document` objektu.</span><span class="sxs-lookup"><span data-stu-id="15369-117">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="15369-118">`AuthorizeAsync` Přetížení je vyvoláno pro zjištění, zda má aktuální uživatel může upravit zadaný dokument.</span><span class="sxs-lookup"><span data-stu-id="15369-118">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="15369-119">Vlastní zásady autorizace "EditPolicy" je rozdělen do rozhodnutí.</span><span class="sxs-lookup"><span data-stu-id="15369-119">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="15369-120">Zobrazit [vlastní autorizace na základě zásad](xref:security/authorization/policies) pro další informace o vytváření zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="15369-120">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="15369-121">Následující kód ukázky předpokládají bylo spuštěno ověřování a nastavení `User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="15369-121">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="15369-122">Zápis obslužné rutiny založené na prostředcích</span><span class="sxs-lookup"><span data-stu-id="15369-122">Write a resource-based handler</span></span>

<span data-ttu-id="15369-123">Zápis obslužné rutiny pro ověřování na základě prostředku se výrazně liší od [zápis obslužné rutiny jednoduché požadavky](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span><span class="sxs-lookup"><span data-stu-id="15369-123">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="15369-124">Vytvořit vlastní požadavek třídy a implementovat třídu obslužné rutiny požadavku.</span><span class="sxs-lookup"><span data-stu-id="15369-124">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="15369-125">Další informace o vytvoření třídy požadavek, naleznete v tématu [požadavky](xref:security/authorization/policies#requirements).</span><span class="sxs-lookup"><span data-stu-id="15369-125">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="15369-126">Třída obslužné rutiny určuje požadavky a typ prostředku.</span><span class="sxs-lookup"><span data-stu-id="15369-126">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="15369-127">Například obslužnou rutinu využitím `SameAuthorRequirement` a `Document` prostředků takto:</span><span class="sxs-lookup"><span data-stu-id="15369-127">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="15369-128">V předchozím příkladu, představte si, že `SameAuthorRequirement` je zvláštní případ další obecného `SpecificAuthorRequirement` třídy.</span><span class="sxs-lookup"><span data-stu-id="15369-128">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="15369-129">`SpecificAuthorRequirement` Obsahuje třídu (není vidět) `Name` vlastnost představující jméno autora.</span><span class="sxs-lookup"><span data-stu-id="15369-129">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="15369-130">`Name` Vlastnost může být nastavena na aktuální uživatel.</span><span class="sxs-lookup"><span data-stu-id="15369-130">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="15369-131">Registrovat požadavku a obslužné rutiny v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="15369-131">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a><span data-ttu-id="15369-132">Provozní požadavky</span><span class="sxs-lookup"><span data-stu-id="15369-132">Operational requirements</span></span>

<span data-ttu-id="15369-133">Pokud jste už rozhodování na základě výsledků operace CRUD (vytváření, čtení, Update, Delete), použijte [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) pomocná třída.</span><span class="sxs-lookup"><span data-stu-id="15369-133">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="15369-134">Tato třída umožňuje zapisovat jedna obslužná rutina namísto jednotlivých tříd pro každý typ operace.</span><span class="sxs-lookup"><span data-stu-id="15369-134">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="15369-135">Pro použití je třeba zadejte názvy některé operace:</span><span class="sxs-lookup"><span data-stu-id="15369-135">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="15369-136">Obslužná rutina je implementován takto, pomocí `OperationAuthorizationRequirement` požadavek a `Document` prostředků:</span><span class="sxs-lookup"><span data-stu-id="15369-136">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="15369-137">Předchozí obslužná rutina ověří pomocí prostředku, identitu uživatele a požadavek na operaci `Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="15369-137">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

<span data-ttu-id="15369-138">Volání obslužné rutiny provozní prostředků, zadejte operaci při vyvolání `AuthorizeAsync` v obslužné rutině stránky nebo akce.</span><span class="sxs-lookup"><span data-stu-id="15369-138">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="15369-139">Následující příklad určuje, zda chcete-li zobrazit poskytnutý dokument smí obsahovat ověřeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="15369-139">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="15369-140">Následující kód ukázky předpokládají bylo spuštěno ověřování a nastavení `User` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="15369-140">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="15369-141">Pokud autorizace úspěšná, vrátí se stránka pro zobrazení dokumentu.</span><span class="sxs-lookup"><span data-stu-id="15369-141">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="15369-142">Pokud autorizace selže, ale uživatel je ověřen, vrací `ForbidResult` informuje veškerý middleware ověřování, který autorizace se nezdařila.</span><span class="sxs-lookup"><span data-stu-id="15369-142">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="15369-143">A `ChallengeResult` je vrácena při ověřování musí být provedeno.</span><span class="sxs-lookup"><span data-stu-id="15369-143">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="15369-144">Pro klienty prohlížeče interaktivní může být vhodné přesměruje uživatele na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="15369-144">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="15369-145">Pokud autorizace úspěšná, vrátí se zobrazení pro dokument.</span><span class="sxs-lookup"><span data-stu-id="15369-145">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="15369-146">Pokud se ověření nezdaří, vrátí `ChallengeResult` informuje veškerý middleware ověřování, autorizace se nezdařila a middleware může trvat odpovídající odpověď.</span><span class="sxs-lookup"><span data-stu-id="15369-146">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="15369-147">Stavový kód 401 nebo 403 může vrací odpovídající odpověď.</span><span class="sxs-lookup"><span data-stu-id="15369-147">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="15369-148">Pro klienty prohlížeče interaktivní může to znamenat přesměruje uživatele na přihlašovací stránku.</span><span class="sxs-lookup"><span data-stu-id="15369-148">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end

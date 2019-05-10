---
title: Autorizace na základě zobrazení v ASP.NET Core MVC
author: rick-anderson
description: Tento dokument ukazuje, jak vložit a využívat službu ověřování v rámci zobrazení o ASP.NET Core Razor.
ms.author: riande
ms.date: 10/30/2017
uid: security/authorization/views
ms.openlocfilehash: e497c41d4dca29fed8733f18cf727804e3f06d8c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898650"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="904c5-103">Autorizace na základě zobrazení v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="904c5-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="904c5-104">Vývojáři často chce zobrazení skrytí nebo jinak upravit uživatelské rozhraní založené na aktuální identitu uživatele.</span><span class="sxs-lookup"><span data-stu-id="904c5-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="904c5-105">Přistupujete k povolení služby v rámci zobrazení MVC prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="904c5-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="904c5-106">Chcete-li vložit do zobrazení Razor autorizační službu, použijte `@inject` – direktiva:</span><span class="sxs-lookup"><span data-stu-id="904c5-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="904c5-107">Pokud chcete službu ověřování v každé zobrazení, umístěte `@inject` směrnice do *_ViewImports.cshtml* soubor *zobrazení* adresáře.</span><span class="sxs-lookup"><span data-stu-id="904c5-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="904c5-108">Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="904c5-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="904c5-109">Použití service vložené autorizace k vyvolání `AuthorizeAsync` přesně stejným způsobem by kontroly během [autorizace na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="904c5-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="904c5-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="904c5-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="904c5-111">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="904c5-111">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

---

<span data-ttu-id="904c5-112">V některých případech bude prostředek modelu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="904c5-112">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="904c5-113">Vyvolání `AuthorizeAsync` přesně stejným způsobem by kontroly během [autorizace na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="904c5-113">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="904c5-114">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="904c5-114">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="904c5-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="904c5-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

---

<span data-ttu-id="904c5-116">V předchozím kódu modelu je předán jako prostředek, které by měl provést vyhodnocení zásad v úvahu.</span><span class="sxs-lookup"><span data-stu-id="904c5-116">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="904c5-117">Není využívají přepínání viditelnosti prvků uživatelského rozhraní aplikace jako jediný autorizace kontrolu.</span><span class="sxs-lookup"><span data-stu-id="904c5-117">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="904c5-118">Skrytí prvku uživatelského rozhraní nemusí zabránit zcela přístup k přidruženému kontroleru akcí.</span><span class="sxs-lookup"><span data-stu-id="904c5-118">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="904c5-119">Představte si třeba tlačítko v předchozím fragmentu kódu.</span><span class="sxs-lookup"><span data-stu-id="904c5-119">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="904c5-120">Uživatel může vyvolat `Edit` metody akce, pokud uživatel zná zdroj relativní adresa URL je */Document/Edit/1*.</span><span class="sxs-lookup"><span data-stu-id="904c5-120">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="904c5-121">Z tohoto důvodu `Edit` metoda akce se má provést vlastní kontroly autorizace.</span><span class="sxs-lookup"><span data-stu-id="904c5-121">For this reason, the `Edit` action method should perform its own authorization check.</span></span>

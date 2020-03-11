---
title: Ověřování na základě zobrazení v ASP.NET Core MVC
author: rick-anderson
description: Tento dokument ukazuje, jak vložit a využívat autorizační službu v zobrazení ASP.NET Core Razor.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
uid: security/authorization/views
ms.openlocfilehash: fc03da9eb98d36ffdda932ee5b16f327c2be9f83
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663595"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="d00a1-103">Ověřování na základě zobrazení v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="d00a1-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="d00a1-104">Vývojář často chce zobrazit, skrýt nebo jinak upravit uživatelské rozhraní na základě identity aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="d00a1-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="d00a1-105">K autorizační službě můžete přistupovat v zobrazeních MVC prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d00a1-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d00a1-106">Chcete-li vložit službu autorizace do zobrazení Razor, použijte direktivu `@inject`:</span><span class="sxs-lookup"><span data-stu-id="d00a1-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="d00a1-107">Pokud chcete službu autorizace v každém zobrazení, umístěte direktivu `@inject` do souboru *_ViewImports. cshtml* adresáře *views* .</span><span class="sxs-lookup"><span data-stu-id="d00a1-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="d00a1-108">Další informace najdete v tématu [vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d00a1-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="d00a1-109">Pomocí vložené autorizační služby můžete vyvolat `AuthorizeAsync` přesně stejným způsobem jako při [autorizaci na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="d00a1-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="d00a1-110">V některých případech bude prostředkem model zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d00a1-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="d00a1-111">Vyvolat `AuthorizeAsync` stejným způsobem jako při [autorizaci na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="d00a1-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="d00a1-112">V předchozím kódu je model předán jako prostředek, který by měl brát v úvahu vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="d00a1-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="d00a1-113">Nespoléhá se na přepínání viditelnosti prvků uživatelského rozhraní vaší aplikace jako jedinou kontrolu autorizace.</span><span class="sxs-lookup"><span data-stu-id="d00a1-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="d00a1-114">Skrytí prvku uživatelského rozhraní nemusí zcela zabránit přístupu k jeho přidružené akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d00a1-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="d00a1-115">Například zvažte tlačítko v předchozím fragmentu kódu.</span><span class="sxs-lookup"><span data-stu-id="d00a1-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="d00a1-116">Uživatel může vyvolat metodu `Edit` akce, pokud ví, že relativní adresa URL prostředku je */Document/Edit/1*.</span><span class="sxs-lookup"><span data-stu-id="d00a1-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="d00a1-117">Z tohoto důvodu by měla metoda `Edit` Action provádět vlastní kontrolu autorizace.</span><span class="sxs-lookup"><span data-stu-id="d00a1-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>

---
title: Ověřování na základě zobrazení v ASP.NET Core MVC
author: rick-anderson
description: Tento dokument ukazuje, jak vložit a používat autorizační službu v Razor zobrazení ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: a9576a48ad6badc5130d89940e4112e69eada1b2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021936"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="efee2-103">Ověřování na základě zobrazení v ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="efee2-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="efee2-104">Vývojář často chce zobrazit, skrýt nebo jinak upravit uživatelské rozhraní na základě identity aktuálního uživatele.</span><span class="sxs-lookup"><span data-stu-id="efee2-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="efee2-105">K autorizační službě můžete přistupovat v zobrazeních MVC prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="efee2-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="efee2-106">K vložení autorizační služby do Razor zobrazení použijte tuto `@inject` direktivu:</span><span class="sxs-lookup"><span data-stu-id="efee2-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="efee2-107">Pokud chcete autorizační službu v každém zobrazení, umístěte `@inject` direktivu do souboru *_ViewImports. cshtml* adresáře *zobrazení* .</span><span class="sxs-lookup"><span data-stu-id="efee2-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="efee2-108">Další informace najdete v tématu [vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="efee2-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="efee2-109">Využijte vloženou autorizační službu k vyvolání `AuthorizeAsync` přesně stejným způsobem jako při [autorizaci na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="efee2-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="efee2-110">V některých případech bude prostředkem model zobrazení.</span><span class="sxs-lookup"><span data-stu-id="efee2-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="efee2-111">Vyvolat `AuthorizeAsync` přesně stejným způsobem, jako byste kontrolovali během [autorizace na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="efee2-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="efee2-112">V předchozím kódu je model předán jako prostředek, který by měl brát v úvahu vyhodnocení zásad.</span><span class="sxs-lookup"><span data-stu-id="efee2-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="efee2-113">Nespoléhá se na přepínání viditelnosti prvků uživatelského rozhraní vaší aplikace jako jedinou kontrolu autorizace.</span><span class="sxs-lookup"><span data-stu-id="efee2-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="efee2-114">Skrytí prvku uživatelského rozhraní nemusí zcela zabránit přístupu k jeho přidružené akci kontroleru.</span><span class="sxs-lookup"><span data-stu-id="efee2-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="efee2-115">Například zvažte tlačítko v předchozím fragmentu kódu.</span><span class="sxs-lookup"><span data-stu-id="efee2-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="efee2-116">Uživatel může `Edit` metodu Action vyvolat, pokud ví, že relativní adresa URL prostředku je */Document/Edit/1*.</span><span class="sxs-lookup"><span data-stu-id="efee2-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="efee2-117">Z tohoto důvodu `Edit` by měla metoda Action provádět vlastní kontrolu autorizace.</span><span class="sxs-lookup"><span data-stu-id="efee2-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>

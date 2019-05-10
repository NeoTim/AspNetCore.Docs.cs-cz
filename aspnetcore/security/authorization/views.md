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
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Autorizace na základě zobrazení v ASP.NET Core MVC

Vývojáři často chce zobrazení skrytí nebo jinak upravit uživatelské rozhraní založené na aktuální identitu uživatele. Přistupujete k povolení služby v rámci zobrazení MVC prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection). Chcete-li vložit do zobrazení Razor autorizační službu, použijte `@inject` – direktiva:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Pokud chcete službu ověřování v každé zobrazení, umístěte `@inject` směrnice do *_ViewImports.cshtml* soubor *zobrazení* adresáře. Další informace najdete v tématu [injektáž závislostí do zobrazení](xref:mvc/views/dependency-injection).

Použití service vložené autorizace k vyvolání `AuthorizeAsync` přesně stejným způsobem by kontroly během [autorizace na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

---

V některých případech bude prostředek modelu zobrazení. Vyvolání `AuthorizeAsync` přesně stejným způsobem by kontroly během [autorizace na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

---

V předchozím kódu modelu je předán jako prostředek, které by měl provést vyhodnocení zásad v úvahu.

> [!WARNING]
> Není využívají přepínání viditelnosti prvků uživatelského rozhraní aplikace jako jediný autorizace kontrolu. Skrytí prvku uživatelského rozhraní nemusí zabránit zcela přístup k přidruženému kontroleru akcí. Představte si třeba tlačítko v předchozím fragmentu kódu. Uživatel může vyvolat `Edit` metody akce, pokud uživatel zná zdroj relativní adresa URL je */Document/Edit/1*. Z tohoto důvodu `Edit` metoda akce se má provést vlastní kontroly autorizace.

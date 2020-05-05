---
title: Ověřování na základě zobrazení v ASP.NET Core MVC
author: rick-anderson
description: Tento dokument ukazuje, jak vložit a používat autorizační službu v zobrazení ASP.NET Core Razor .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: 1a3b4a92d270844fa99fc9fb0283226e94edb22d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775619"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Ověřování na základě zobrazení v ASP.NET Core MVC

Vývojář často chce zobrazit, skrýt nebo jinak upravit uživatelské rozhraní na základě identity aktuálního uživatele. K autorizační službě můžete přistupovat v zobrazeních MVC prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection). K vložení autorizační služby do Razor zobrazení použijte tuto `@inject` direktivu:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Pokud chcete autorizační službu v každém zobrazení, umístěte `@inject` direktivu do souboru *_ViewImports. cshtml* adresáře *zobrazení* . Další informace najdete v tématu [vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).

Využijte vloženou autorizační službu k vyvolání `AuthorizeAsync` přesně stejným způsobem jako při [autorizaci na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

V některých případech bude prostředkem model zobrazení. Vyvolat `AuthorizeAsync` přesně stejným způsobem, jako byste kontrolovali během [autorizace na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

V předchozím kódu je model předán jako prostředek, který by měl brát v úvahu vyhodnocení zásad.

> [!WARNING]
> Nespoléhá se na přepínání viditelnosti prvků uživatelského rozhraní vaší aplikace jako jedinou kontrolu autorizace. Skrytí prvku uživatelského rozhraní nemusí zcela zabránit přístupu k jeho přidružené akci kontroleru. Například zvažte tlačítko v předchozím fragmentu kódu. Uživatel může metodu Action vyvolat `Edit` , pokud ví, že relativní adresa URL prostředku je */Document/Edit/1*. Z `Edit` tohoto důvodu by měla metoda Action provádět vlastní kontrolu autorizace.

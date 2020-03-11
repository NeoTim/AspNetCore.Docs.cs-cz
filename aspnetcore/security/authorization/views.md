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
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Ověřování na základě zobrazení v ASP.NET Core MVC

Vývojář často chce zobrazit, skrýt nebo jinak upravit uživatelské rozhraní na základě identity aktuálního uživatele. K autorizační službě můžete přistupovat v zobrazeních MVC prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection). Chcete-li vložit službu autorizace do zobrazení Razor, použijte direktivu `@inject`:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Pokud chcete službu autorizace v každém zobrazení, umístěte direktivu `@inject` do souboru *_ViewImports. cshtml* adresáře *views* . Další informace najdete v tématu [vkládání závislostí do zobrazení](xref:mvc/views/dependency-injection).

Pomocí vložené autorizační služby můžete vyvolat `AuthorizeAsync` přesně stejným způsobem jako při [autorizaci na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

V některých případech bude prostředkem model zobrazení. Vyvolat `AuthorizeAsync` stejným způsobem jako při [autorizaci na základě prostředků](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

V předchozím kódu je model předán jako prostředek, který by měl brát v úvahu vyhodnocení zásad.

> [!WARNING]
> Nespoléhá se na přepínání viditelnosti prvků uživatelského rozhraní vaší aplikace jako jedinou kontrolu autorizace. Skrytí prvku uživatelského rozhraní nemusí zcela zabránit přístupu k jeho přidružené akci kontroleru. Například zvažte tlačítko v předchozím fragmentu kódu. Uživatel může vyvolat metodu `Edit` akce, pokud ví, že relativní adresa URL prostředku je */Document/Edit/1*. Z tohoto důvodu by měla metoda `Edit` Action provádět vlastní kontrolu autorizace.

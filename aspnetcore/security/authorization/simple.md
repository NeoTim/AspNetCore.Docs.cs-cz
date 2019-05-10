---
title: Jednoduchá autorizace v ASP.NET Core
author: rick-anderson
description: Další informace o použití atributu Authorize k omezení přístupu k ASP.NET Core kontrolery a akce.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64903129"
---
# <a name="simple-authorization-in-aspnet-core"></a>Jednoduchá autorizace v ASP.NET Core

<a name="security-authorization-simple"></a>

Autorizace v aplikaci MVC je řízen pomocí `AuthorizeAttribute` atribut a jeho různé parametry. V nejjednodušším, použití `AuthorizeAttribute` atributu na kontroler nebo akce omezíte přístup ke kontroleru nebo akce, které všem ověřeným uživatelům.

Například následující kód omezuje přístup `AccountController` všem ověřeným uživatelům.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Pokud chcete použít autorizaci pro akce spíše než kontroleru, použije `AuthorizeAttribute` atribut pro vlastní akci:

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

Teď můžete přistupovat pouze ověřeným uživatelům `Logout` funkce.

Můžete také použít `AllowAnonymous` atribut pro povolení přístupu podle neověřených uživatelů k jednotlivým akcím. Příklad:

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

To by umožnilo pouze ověřeným uživatelům `AccountController`, s výjimkou `Login` akce, které je přístupné všem uživatelům, bez ohledu na jejich stav ověření nebo neověřený / anonymní.

> [!WARNING]
> `[AllowAnonymous]` vynechá všechny příkazy autorizace. Pokud kombinujete `[AllowAnonymous]` a jakékoli `[Authorize]` atribut `[Authorize]` atributy se ignorují. Například pokud použijete `[AllowAnonymous]` na úrovni kontroleru jakékoli `[Authorize]` atributy na stejný kontrolér (nebo na jakoukoli akci v rámci něj) se ignoruje.

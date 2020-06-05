---
title: Jednoduché ověřování v ASP.NET Core
author: rick-anderson
description: Naučte se používat atribut autorizovat k omezení přístupu k ASP.NET Core řadičům a akcím.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: 4ec31354d7fe11af75fd3a0045b4045f83721cb5
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84272122"
---
# <a name="simple-authorization-in-aspnet-core"></a>Jednoduché ověřování v ASP.NET Core

<a name="security-authorization-simple"></a>

Autorizaci v ASP.NET Core řídí s <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> a jeho různými parametry. V nejjednodušším formuláři použití `[Authorize]` atributu na kontroler, akce nebo Razor stránku omezuje přístup k této součásti na libovolného ověřeného uživatele.

Například následující kód omezuje přístup k `AccountController` libovolnému ověřenému uživateli.

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

Pokud chcete použít autorizaci pro akci místo kontroleru, použijte `AuthorizeAttribute` atribut na vlastní akci:

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

K této funkci mají přístup jenom ověření uživatelé `Logout` .

Atribut můžete použít také `AllowAnonymous` k povolení přístupu neověřeným uživatelům k jednotlivým akcím. Příklad:

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

To umožní pouze ověřeným uživatelům v `AccountController` , s výjimkou `Login` akce, která je přístupná všem, bez ohledu na jejich ověřený nebo neověřený nebo anonymní stav.

> [!WARNING]
> `[AllowAnonymous]`obchází všechny příkazy autorizace. Pokud zkombinujete `[AllowAnonymous]` a jakýkoliv `[Authorize]` atribut, `[Authorize]` atributy se ignorují. Například pokud použijete `[AllowAnonymous]` na úrovni řadiče, všechny `[Authorize]` atributy na stejném kontroleru (nebo na jakékoli akci v ní) se ignorují.

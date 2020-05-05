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
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775632"
---
# <a name="simple-authorization-in-aspnet-core"></a>Jednoduché ověřování v ASP.NET Core

<a name="security-authorization-simple"></a>

Autorizace v MVC je řízena prostřednictvím `AuthorizeAttribute` atributu a jeho různých parametrů. V nejjednodušším případě použití `AuthorizeAttribute` atributu na kontroler nebo akce omezuje přístup k řadiči nebo akci na libovolného ověřeného uživatele.

Například následující kód omezuje přístup `AccountController` k libovolnému ověřenému uživateli.

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

K této `Logout` funkci mají přístup jenom ověření uživatelé.

`AllowAnonymous` Atribut můžete použít také k povolení přístupu neověřeným uživatelům k jednotlivým akcím. Příklad:

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

To umožní pouze ověřeným uživatelům v `AccountController`, s výjimkou `Login` akce, která je přístupná všem, bez ohledu na jejich ověřený nebo neověřený nebo anonymní stav.

> [!WARNING]
> `[AllowAnonymous]`obchází všechny příkazy autorizace. Pokud zkombinujete `[AllowAnonymous]` a jakýkoliv `[Authorize]` atribut, `[Authorize]` atributy se ignorují. Například pokud použijete `[AllowAnonymous]` na úrovni řadiče, všechny `[Authorize]` atributy na stejném kontroleru (nebo na jakékoli akci v ní) se ignorují.

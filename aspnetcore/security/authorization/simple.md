---
title: Jednoduché ověřování v ASP.NET Core
author: rick-anderson
description: Naučte se používat atribut autorizovat k omezení přístupu k ASP.NET Core řadičům a akcím.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663581"
---
# <a name="simple-authorization-in-aspnet-core"></a>Jednoduché ověřování v ASP.NET Core

<a name="security-authorization-simple"></a>

Autorizace v MVC se řídí pomocí atributu `AuthorizeAttribute` a jeho různými parametry. V nejjednodušším případě použití atributu `AuthorizeAttribute` na kontroler nebo akce omezí přístup k řadiči nebo akci pro libovolného ověřeného uživatele.

Například následující kód omezuje přístup k `AccountController` všem ověřeným uživatelům.

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

Pokud chcete použít autorizaci pro akci místo kontroleru, použijte atribut `AuthorizeAttribute` pro samotnou akci:

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

K funkci `Logout` mají nyní přístup pouze ověření uživatelé.

Můžete také použít atribut `AllowAnonymous` k povolení přístupu neověřeným uživatelům k jednotlivým akcím. Příklad:

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

To umožňuje, aby `AccountController`jenom ověřeným uživatelům, s výjimkou akce `Login`, která je přístupná všem, bez ohledu na jejich ověřený nebo neověřený nebo anonymní stav.

> [!WARNING]
> `[AllowAnonymous]` obchází všechny autorizační příkazy. Pokud kombinujete `[AllowAnonymous]` a všechny `[Authorize]` atributu, atributy `[Authorize]` jsou ignorovány. Pokud například použijete `[AllowAnonymous]` na úrovni řadiče, všechny `[Authorize]` atributy na stejném kontroleru (nebo na jakékoli akci v ní) se ignorují.

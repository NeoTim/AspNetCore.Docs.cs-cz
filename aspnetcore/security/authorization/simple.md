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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="33223-103">Jednoduché ověřování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33223-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="33223-104">Autorizace v MVC je řízena prostřednictvím `AuthorizeAttribute` atributu a jeho různých parametrů.</span><span class="sxs-lookup"><span data-stu-id="33223-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="33223-105">V nejjednodušším případě použití `AuthorizeAttribute` atributu na kontroler nebo akce omezuje přístup k řadiči nebo akci na libovolného ověřeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="33223-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="33223-106">Například následující kód omezuje přístup `AccountController` k libovolnému ověřenému uživateli.</span><span class="sxs-lookup"><span data-stu-id="33223-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="33223-107">Pokud chcete použít autorizaci pro akci místo kontroleru, použijte `AuthorizeAttribute` atribut na vlastní akci:</span><span class="sxs-lookup"><span data-stu-id="33223-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="33223-108">K této `Logout` funkci mají přístup jenom ověření uživatelé.</span><span class="sxs-lookup"><span data-stu-id="33223-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="33223-109">`AllowAnonymous` Atribut můžete použít také k povolení přístupu neověřeným uživatelům k jednotlivým akcím.</span><span class="sxs-lookup"><span data-stu-id="33223-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="33223-110">Příklad:</span><span class="sxs-lookup"><span data-stu-id="33223-110">For example:</span></span>

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

<span data-ttu-id="33223-111">To umožní pouze ověřeným uživatelům v `AccountController`, s výjimkou `Login` akce, která je přístupná všem, bez ohledu na jejich ověřený nebo neověřený nebo anonymní stav.</span><span class="sxs-lookup"><span data-stu-id="33223-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="33223-112">`[AllowAnonymous]`obchází všechny příkazy autorizace.</span><span class="sxs-lookup"><span data-stu-id="33223-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="33223-113">Pokud zkombinujete `[AllowAnonymous]` a jakýkoliv `[Authorize]` atribut, `[Authorize]` atributy se ignorují.</span><span class="sxs-lookup"><span data-stu-id="33223-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="33223-114">Například pokud použijete `[AllowAnonymous]` na úrovni řadiče, všechny `[Authorize]` atributy na stejném kontroleru (nebo na jakékoli akci v ní) se ignorují.</span><span class="sxs-lookup"><span data-stu-id="33223-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

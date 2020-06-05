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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="69f56-103">Jednoduché ověřování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="69f56-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="69f56-104">Autorizaci v ASP.NET Core řídí s <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> a jeho různými parametry.</span><span class="sxs-lookup"><span data-stu-id="69f56-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="69f56-105">V nejjednodušším formuláři použití `[Authorize]` atributu na kontroler, akce nebo Razor stránku omezuje přístup k této součásti na libovolného ověřeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="69f56-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="69f56-106">Například následující kód omezuje přístup k `AccountController` libovolnému ověřenému uživateli.</span><span class="sxs-lookup"><span data-stu-id="69f56-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="69f56-107">Pokud chcete použít autorizaci pro akci místo kontroleru, použijte `AuthorizeAttribute` atribut na vlastní akci:</span><span class="sxs-lookup"><span data-stu-id="69f56-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="69f56-108">K této funkci mají přístup jenom ověření uživatelé `Logout` .</span><span class="sxs-lookup"><span data-stu-id="69f56-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="69f56-109">Atribut můžete použít také `AllowAnonymous` k povolení přístupu neověřeným uživatelům k jednotlivým akcím.</span><span class="sxs-lookup"><span data-stu-id="69f56-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="69f56-110">Příklad:</span><span class="sxs-lookup"><span data-stu-id="69f56-110">For example:</span></span>

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

<span data-ttu-id="69f56-111">To umožní pouze ověřeným uživatelům v `AccountController` , s výjimkou `Login` akce, která je přístupná všem, bez ohledu na jejich ověřený nebo neověřený nebo anonymní stav.</span><span class="sxs-lookup"><span data-stu-id="69f56-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="69f56-112">`[AllowAnonymous]`obchází všechny příkazy autorizace.</span><span class="sxs-lookup"><span data-stu-id="69f56-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="69f56-113">Pokud zkombinujete `[AllowAnonymous]` a jakýkoliv `[Authorize]` atribut, `[Authorize]` atributy se ignorují.</span><span class="sxs-lookup"><span data-stu-id="69f56-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="69f56-114">Například pokud použijete `[AllowAnonymous]` na úrovni řadiče, všechny `[Authorize]` atributy na stejném kontroleru (nebo na jakékoli akci v ní) se ignorují.</span><span class="sxs-lookup"><span data-stu-id="69f56-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="6aad6-103">Jednoduché ověřování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6aad6-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="6aad6-104">Autorizace v MVC se řídí pomocí atributu `AuthorizeAttribute` a jeho různými parametry.</span><span class="sxs-lookup"><span data-stu-id="6aad6-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="6aad6-105">V nejjednodušším případě použití atributu `AuthorizeAttribute` na kontroler nebo akce omezí přístup k řadiči nebo akci pro libovolného ověřeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="6aad6-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="6aad6-106">Například následující kód omezuje přístup k `AccountController` všem ověřeným uživatelům.</span><span class="sxs-lookup"><span data-stu-id="6aad6-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="6aad6-107">Pokud chcete použít autorizaci pro akci místo kontroleru, použijte atribut `AuthorizeAttribute` pro samotnou akci:</span><span class="sxs-lookup"><span data-stu-id="6aad6-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="6aad6-108">K funkci `Logout` mají nyní přístup pouze ověření uživatelé.</span><span class="sxs-lookup"><span data-stu-id="6aad6-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="6aad6-109">Můžete také použít atribut `AllowAnonymous` k povolení přístupu neověřeným uživatelům k jednotlivým akcím.</span><span class="sxs-lookup"><span data-stu-id="6aad6-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="6aad6-110">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6aad6-110">For example:</span></span>

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

<span data-ttu-id="6aad6-111">To umožňuje, aby `AccountController`jenom ověřeným uživatelům, s výjimkou akce `Login`, která je přístupná všem, bez ohledu na jejich ověřený nebo neověřený nebo anonymní stav.</span><span class="sxs-lookup"><span data-stu-id="6aad6-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="6aad6-112">`[AllowAnonymous]` obchází všechny autorizační příkazy.</span><span class="sxs-lookup"><span data-stu-id="6aad6-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="6aad6-113">Pokud kombinujete `[AllowAnonymous]` a všechny `[Authorize]` atributu, atributy `[Authorize]` jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="6aad6-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="6aad6-114">Pokud například použijete `[AllowAnonymous]` na úrovni řadiče, všechny `[Authorize]` atributy na stejném kontroleru (nebo na jakékoli akci v ní) se ignorují.</span><span class="sxs-lookup"><span data-stu-id="6aad6-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

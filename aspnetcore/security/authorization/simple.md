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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="c6146-103">Jednoduchá autorizace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c6146-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="c6146-104">Autorizace v aplikaci MVC je řízen pomocí `AuthorizeAttribute` atribut a jeho různé parametry.</span><span class="sxs-lookup"><span data-stu-id="c6146-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="c6146-105">V nejjednodušším, použití `AuthorizeAttribute` atributu na kontroler nebo akce omezíte přístup ke kontroleru nebo akce, které všem ověřeným uživatelům.</span><span class="sxs-lookup"><span data-stu-id="c6146-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="c6146-106">Například následující kód omezuje přístup `AccountController` všem ověřeným uživatelům.</span><span class="sxs-lookup"><span data-stu-id="c6146-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="c6146-107">Pokud chcete použít autorizaci pro akce spíše než kontroleru, použije `AuthorizeAttribute` atribut pro vlastní akci:</span><span class="sxs-lookup"><span data-stu-id="c6146-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="c6146-108">Teď můžete přistupovat pouze ověřeným uživatelům `Logout` funkce.</span><span class="sxs-lookup"><span data-stu-id="c6146-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="c6146-109">Můžete také použít `AllowAnonymous` atribut pro povolení přístupu podle neověřených uživatelů k jednotlivým akcím.</span><span class="sxs-lookup"><span data-stu-id="c6146-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="c6146-110">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c6146-110">For example:</span></span>

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

<span data-ttu-id="c6146-111">To by umožnilo pouze ověřeným uživatelům `AccountController`, s výjimkou `Login` akce, které je přístupné všem uživatelům, bez ohledu na jejich stav ověření nebo neověřený / anonymní.</span><span class="sxs-lookup"><span data-stu-id="c6146-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="c6146-112">`[AllowAnonymous]` vynechá všechny příkazy autorizace.</span><span class="sxs-lookup"><span data-stu-id="c6146-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="c6146-113">Pokud kombinujete `[AllowAnonymous]` a jakékoli `[Authorize]` atribut `[Authorize]` atributy se ignorují.</span><span class="sxs-lookup"><span data-stu-id="c6146-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="c6146-114">Například pokud použijete `[AllowAnonymous]` na úrovni kontroleru jakékoli `[Authorize]` atributy na stejný kontrolér (nebo na jakoukoli akci v rámci něj) se ignoruje.</span><span class="sxs-lookup"><span data-stu-id="c6146-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

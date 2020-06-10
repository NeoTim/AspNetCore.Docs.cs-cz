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
ms.openlocfilehash: b5f97038145ed479c315af50a35d6c64d85425a7
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652949"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="5c6d6-103">Jednoduché ověřování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c6d6-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="5c6d6-104">Autorizaci v ASP.NET Core řídí s <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> a jeho různými parametry.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="5c6d6-105">V nejjednodušším formuláři použití `[Authorize]` atributu na kontroler, akce nebo Razor stránku omezuje přístup k této součásti na libovolného ověřeného uživatele.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="5c6d6-106">Například následující kód omezuje přístup k `AccountController` libovolnému ověřenému uživateli.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="5c6d6-107">Pokud chcete použít autorizaci pro akci místo kontroleru, použijte `AuthorizeAttribute` atribut na vlastní akci:</span><span class="sxs-lookup"><span data-stu-id="5c6d6-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="5c6d6-108">K této funkci mají přístup jenom ověření uživatelé `Logout` .</span><span class="sxs-lookup"><span data-stu-id="5c6d6-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="5c6d6-109">Atribut můžete použít také `AllowAnonymous` k povolení přístupu neověřeným uživatelům k jednotlivým akcím.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="5c6d6-110">Například:</span><span class="sxs-lookup"><span data-stu-id="5c6d6-110">For example:</span></span>

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

<span data-ttu-id="5c6d6-111">To umožní pouze ověřeným uživatelům v `AccountController` , s výjimkou `Login` akce, která je přístupná všem, bez ohledu na jejich ověřený nebo neověřený nebo anonymní stav.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="5c6d6-112">`[AllowAnonymous]`obchází všechny příkazy autorizace.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="5c6d6-113">Pokud zkombinujete `[AllowAnonymous]` a jakýkoliv `[Authorize]` atribut, `[Authorize]` atributy se ignorují.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="5c6d6-114">Například pokud použijete `[AllowAnonymous]` na úrovni řadiče, všechny `[Authorize]` atributy na stejném kontroleru (nebo na jakékoli akci v ní) se ignorují.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a><span data-ttu-id="5c6d6-115">Autorizovat atributy a Razor stránky</span><span class="sxs-lookup"><span data-stu-id="5c6d6-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="5c6d6-116">Nelze <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> použít ***not*** na Razor obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can ***not*** be applied to Razor Page handlers.</span></span> <span data-ttu-id="5c6d6-117">Například `[Authorize]` nelze použít pro `OnGet` , `OnPost` nebo žádné jiné obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span>

<span data-ttu-id="5c6d6-118">K použití autorizace na obslužné rutiny stránky lze použít následující dva způsoby Razor :</span><span class="sxs-lookup"><span data-stu-id="5c6d6-118">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

* <span data-ttu-id="5c6d6-119">Pro obslužné rutiny stránek, které vyžadují jinou autorizaci, použijte samostatné stránky.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-119">Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="5c6d6-120">Sdílený obsah byl přesunut do jednoho nebo více [částečných zobrazení](xref:mvc/views/partial).</span><span class="sxs-lookup"><span data-stu-id="5c6d6-120">Moved shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="5c6d6-121">Pokud je to možné, jedná se o doporučený postup.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-121">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="5c6d6-122">Pro obsah, který musí sdílet společnou stránku, napište filtr, který provádí autorizaci jako součást [IAsyncPageFilter. OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="5c6d6-122">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="5c6d6-123">Projekt GitHub [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) demonstruje tento přístup:</span><span class="sxs-lookup"><span data-stu-id="5c6d6-123">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="5c6d6-124">[AuthorizePageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizePageHandlerFilter.cs) implementuje ověřovací filtr:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="5c6d6-124">The [AuthorizePageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizePageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="5c6d6-125">Atribut [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) je použit pro `OnGet` obslužnou rutinu stránky:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="5c6d6-125">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="5c6d6-126">Vzorový přístup [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) ***není***:</span><span class="sxs-lookup"><span data-stu-id="5c6d6-126">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does ***not***:</span></span>
> * <span data-ttu-id="5c6d6-127">Můžete vytvářet pomocí autorizačních atributů, které se aplikují na stránku, model stránky nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-127">Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="5c6d6-128">Sestavování autorizačních atributů má za následek ověřování a autorizaci, která se spouští několikrát, když máte `AuthorizeAttribute` `AuthorizeFilter` na stránce použit i jeden další výskyt.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-128">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="5c6d6-129">Pracujte ve spojení se zbytkem ASP.NET Core ověřování a autorizačním systémem.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-129">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="5c6d6-130">Je nutné ověřit, že tento přístup bude správně fungovat pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-130">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="5c6d6-131">Neexistují žádné plány pro podporu pro `AuthorizeAttribute` Razor obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="5c6d6-131">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 

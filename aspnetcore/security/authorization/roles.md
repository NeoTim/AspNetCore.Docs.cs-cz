---
title: Ověřování na základě rolí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak omezit ASP.NET Core kontroler a přístup k akcím předáním rolí do autorizačního atributu.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/roles
ms.openlocfilehash: 01d4239377b128f711a110a821e1afea58ca14a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776536"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="0a848-103">Ověřování na základě rolí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a848-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="0a848-104">Při vytvoření identity může patřit k jedné nebo více rolím.</span><span class="sxs-lookup"><span data-stu-id="0a848-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="0a848-105">Tracy může například patřit rolím Správce a uživatele, zatímco Scott může patřit pouze do role uživatele.</span><span class="sxs-lookup"><span data-stu-id="0a848-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="0a848-106">Způsob vytváření a správy těchto rolí závisí na záložním úložišti procesu autorizace.</span><span class="sxs-lookup"><span data-stu-id="0a848-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="0a848-107">Role jsou k dispozici vývojářům prostřednictvím metody [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) třídy [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .</span><span class="sxs-lookup"><span data-stu-id="0a848-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="0a848-108">Přidávání kontrol rolí</span><span class="sxs-lookup"><span data-stu-id="0a848-108">Adding role checks</span></span>

<span data-ttu-id="0a848-109">Kontroly autorizace na základě rolí jsou deklarativní&mdash;, když je vývojář vloží do svého kódu, proti kontroleru nebo akci v rámci kontroleru, přičemž určení rolí, které musí být aktuálním uživatelem členem, aby bylo možné získat přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="0a848-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="0a848-110">Například následující kód omezuje přístup k jakýmkoli akcím `AdministrationController` na uživatele, kteří jsou členy této `Administrator` role:</span><span class="sxs-lookup"><span data-stu-id="0a848-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="0a848-111">Jako seznam oddělený čárkami můžete zadat více rolí:</span><span class="sxs-lookup"><span data-stu-id="0a848-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="0a848-112">Tento kontroler by měl být přístupný jenom uživatelům, kteří jsou členy `HRManager` role nebo `Finance` role.</span><span class="sxs-lookup"><span data-stu-id="0a848-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="0a848-113">Pokud použijete více atributů, musí být přistupující uživatel členem všech zadaných rolí. Následující ukázka vyžaduje, aby uživatel byl členem role `PowerUser` i. `ControlPanelUser`</span><span class="sxs-lookup"><span data-stu-id="0a848-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="0a848-114">Přístup můžete dále omezit použitím atributů autorizace další role na úrovni akce:</span><span class="sxs-lookup"><span data-stu-id="0a848-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

<span data-ttu-id="0a848-115">V předchozím fragmentu kódu, který je `Administrator` členem role nebo `PowerUser` role, má přístup k řadiči a `SetTime` akci, ale k této `Administrator` `ShutDown` akci mají přístup jenom členové této role.</span><span class="sxs-lookup"><span data-stu-id="0a848-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="0a848-116">Můžete také Uzamknout kontroler, ale povolit anonymní a neověřený přístup k jednotlivým akcím.</span><span class="sxs-lookup"><span data-stu-id="0a848-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="0a848-117">Pro Razor stránky `AuthorizeAttribute` lze použít buď:</span><span class="sxs-lookup"><span data-stu-id="0a848-117">For Razor Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="0a848-118">Pomocí [konvence](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)nebo</span><span class="sxs-lookup"><span data-stu-id="0a848-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="0a848-119">`AuthorizeAttribute` Aplikuje se na `PageModel` instanci:</span><span class="sxs-lookup"><span data-stu-id="0a848-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="0a848-120">Atributy filtru, včetně `AuthorizeAttribute`, lze použít pouze na PageModel a nelze je použít na konkrétní metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="0a848-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="0a848-121">Kontroly rolí na základě zásad</span><span class="sxs-lookup"><span data-stu-id="0a848-121">Policy based role checks</span></span>

<span data-ttu-id="0a848-122">Požadavky role lze také vyjádřit pomocí nové syntaxe zásad, kde vývojář při spuštění zaregistruje zásadu při spuštění jako součást konfigurace autorizační služby.</span><span class="sxs-lookup"><span data-stu-id="0a848-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="0a848-123">K tomu obvykle dochází `ConfigureServices()` v souboru *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="0a848-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

<span data-ttu-id="0a848-124">Zásady se aplikují pomocí `Policy` vlastnosti u `AuthorizeAttribute` atributu:</span><span class="sxs-lookup"><span data-stu-id="0a848-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="0a848-125">Pokud chcete v požadavku zadat více povolených rolí, můžete je zadat jako parametry `RequireRole` metody:</span><span class="sxs-lookup"><span data-stu-id="0a848-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="0a848-126">Tento příklad autorizuje uživatele, kteří patří do `Administrator`rolí `PowerUser` nebo `BackupAdministrator` .</span><span class="sxs-lookup"><span data-stu-id="0a848-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-identity"></a><span data-ttu-id="0a848-127">Přidat služby rolí doIdentity</span><span class="sxs-lookup"><span data-stu-id="0a848-127">Add Role services to Identity</span></span>

<span data-ttu-id="0a848-128">Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:</span><span class="sxs-lookup"><span data-stu-id="0a848-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end


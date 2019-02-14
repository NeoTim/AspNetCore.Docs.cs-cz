---
title: Autorizace na základě rolí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak omezit přístup kontroleru a akce ASP.NET Core předáním role atribut Authorize.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 0467ea82831bffe6882e584930c2fa1212a244c7
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248092"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="84a61-103">Autorizace na základě rolí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84a61-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="84a61-104">Když se vytvoří identitu, která může patřit do jedné nebo více rolí.</span><span class="sxs-lookup"><span data-stu-id="84a61-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="84a61-105">Například Tracy může patřit do role správce a uživatele, zatímco Scott lze přiřadit pouze k roli uživatele.</span><span class="sxs-lookup"><span data-stu-id="84a61-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="84a61-106">Způsob vytvoření a správa těchto rolí závisí na záložním úložišti proces autorizace.</span><span class="sxs-lookup"><span data-stu-id="84a61-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="84a61-107">Role jsou vystaveny pro vývojáře prostřednictvím [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) metodu [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) třídy.</span><span class="sxs-lookup"><span data-stu-id="84a61-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

::: moniker range=">= aspnetcore-2.0"

> [!IMPORTANT]
> <span data-ttu-id="84a61-108">Toto téma se **není** platí pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="84a61-108">This topic does **not** apply to Razor Pages.</span></span> <span data-ttu-id="84a61-109">Stránky Razor podporuje [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter).</span><span class="sxs-lookup"><span data-stu-id="84a61-109">Razor Pages supports [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter).</span></span> <span data-ttu-id="84a61-110">Další informace najdete v tématu [metody filtrování pro Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="84a61-110">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

::: moniker-end

## <a name="adding-role-checks"></a><span data-ttu-id="84a61-111">Přidání kontroly role</span><span class="sxs-lookup"><span data-stu-id="84a61-111">Adding role checks</span></span>

<span data-ttu-id="84a61-112">Kontroly autorizace na základě rolí jsou deklarativní&mdash;vývojář vloží je do jejich kód proti kontroler nebo akce v kontroleru, zadání role, které aktuální uživatel musí být členem skupiny pro přístup k požadovanému prostředku.</span><span class="sxs-lookup"><span data-stu-id="84a61-112">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="84a61-113">Například následující kód omezuje přístup na všechny akce na `AdministrationController` uživatelům, kteří jsou členy `Administrator` role:</span><span class="sxs-lookup"><span data-stu-id="84a61-113">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="84a61-114">Můžete určit víc rolí jako seznam oddělený čárkami:</span><span class="sxs-lookup"><span data-stu-id="84a61-114">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="84a61-115">Tento kontroler by přístupný pouze uživatelé, kteří jsou členy z `HRManager` role nebo `Finance` role.</span><span class="sxs-lookup"><span data-stu-id="84a61-115">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="84a61-116">Pokud použijete víc atributů uživatele s přístupem k musí být členem všech rolí zadaný; Následující ukázka vyžaduje, že uživatel musí být členem obou `PowerUser` a `ControlPanelUser` role.</span><span class="sxs-lookup"><span data-stu-id="84a61-116">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="84a61-117">Dále můžete omezit přístup s použitím atributů autorizace další role na úrovni akce:</span><span class="sxs-lookup"><span data-stu-id="84a61-117">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

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

<span data-ttu-id="84a61-118">V předchozím kódu fragmentu členů `Administrator` role nebo `PowerUser` role kontroleru přístup a `SetTime` akce, ale jen členové `Administrator` role přístup `ShutDown` akce.</span><span class="sxs-lookup"><span data-stu-id="84a61-118">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="84a61-119">Můžete také uzamknout řadiči, ale povolit anonymní, neověření přístup k jednotlivým akcím.</span><span class="sxs-lookup"><span data-stu-id="84a61-119">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

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

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="84a61-120">Kontroly rolí na základě zásad</span><span class="sxs-lookup"><span data-stu-id="84a61-120">Policy based role checks</span></span>

<span data-ttu-id="84a61-121">Požadavky na role se dají vyjádřit také pomocí nové zásady syntaxe, kde vývojáři zaregistruje jako součást konfigurace služby ověření zásad při spuštění.</span><span class="sxs-lookup"><span data-stu-id="84a61-121">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="84a61-122">K tomuto obvykle dochází u `ConfigureServices()` ve vašich *Startup.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="84a61-122">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="84a61-123">Zásady se aplikují pomocí `Policy` vlastnost `AuthorizeAttribute` atribut:</span><span class="sxs-lookup"><span data-stu-id="84a61-123">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="84a61-124">Pokud chcete zadat víc rolí povolené v požadavku, můžete je zadat jako parametry `RequireRole` metody:</span><span class="sxs-lookup"><span data-stu-id="84a61-124">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="84a61-125">Tento příklad povolí uživatelům patřícím do `Administrator`, `PowerUser` nebo `BackupAdministrator` role.</span><span class="sxs-lookup"><span data-stu-id="84a61-125">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-identity"></a><span data-ttu-id="84a61-126">Přidání služby Role na identitu</span><span class="sxs-lookup"><span data-stu-id="84a61-126">Add Role services to Identity</span></span>

<span data-ttu-id="84a61-127">Připojit [kliknutím na Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) přidat služby rolí:</span><span class="sxs-lookup"><span data-stu-id="84a61-127">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](roles/samples/Startup.cs?name=snippet&highlight=7)]
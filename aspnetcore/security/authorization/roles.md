---
title: Ověřování na základě rolí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak omezit ASP.NET Core kontroler a přístup k akcím předáním rolí do autorizačního atributu.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 28aa3df6aa661d0b762df78fe611cd827af43f75
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73660055"
---
# <a name="role-based-authorization-in-aspnet-core"></a>Ověřování na základě rolí v ASP.NET Core

<a name="security-authorization-role-based"></a>

Při vytvoření identity může patřit k jedné nebo více rolím. Tracy může například patřit rolím Správce a uživatele, zatímco Scott může patřit pouze do role uživatele. Způsob vytváření a správy těchto rolí závisí na záložním úložišti procesu autorizace. Role jsou k dispozici vývojářům prostřednictvím metody [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) třídy [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .

## <a name="adding-role-checks"></a>Přidávání kontrol rolí

Kontroly autorizace na základě rolí jsou deklarativní&mdash;vývojář je vloží do svého kódu, proti kontroleru nebo akci v rámci kontroleru, přičemž určení rolí, které musí být aktuálním uživatelem členem, aby bylo možné získat přístup k požadovanému prostředku.

Následující kód například omezuje přístup k jakýmkoli akcím v `AdministrationController` uživatelům, kteří jsou členy role `Administrator`:

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Jako seznam oddělený čárkami můžete zadat více rolí:

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Tento kontroler by měl být přístupný jenom uživatelům, kteří jsou členy role `HRManager` nebo role `Finance`.

Pokud použijete více atributů, musí být přistupující uživatel členem všech zadaných rolí. Následující ukázka vyžaduje, aby uživatel byl členem role `PowerUser` i `ControlPanelUser`.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Přístup můžete dále omezit použitím atributů autorizace další role na úrovni akce:

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

V předchozím fragmentu kódu mohou členové role `Administrator` nebo role `PowerUser` získat přístup k řadiči a akci `SetTime`, ale k akci `ShutDown` mají přístup pouze členové role `Administrator`.

Můžete také Uzamknout kontroler, ale povolit anonymní a neověřený přístup k jednotlivým akcím.

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

V případě Razor Pages lze `AuthorizeAttribute` použít buď takto:

* Pomocí [konvence](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)nebo
* Použití `AuthorizeAttribute` na instanci `PageModel`:

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
> Atributy filtru, včetně `AuthorizeAttribute`, lze použít pouze na PageModel a nelze je použít na konkrétní metody obslužné rutiny stránky.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Kontroly rolí na základě zásad

Požadavky role lze také vyjádřit pomocí nové syntaxe zásad, kde vývojář při spuštění zaregistruje zásadu při spuštění jako součást konfigurace autorizační služby. K tomu obvykle dochází v `ConfigureServices()` v souboru *Startup.cs* .

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

Zásady se aplikují pomocí vlastnosti `Policy` u atributu `AuthorizeAttribute`:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Pokud chcete v požadavku zadat více povolených rolí, můžete je zadat jako parametry `RequireRole` metody:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Tento příklad autorizuje uživatele, kteří patří do rolí `Administrator`, `PowerUser` nebo `BackupAdministrator`.

### <a name="add-role-services-to-identity"></a>Přidání služeb role k identitě

Připojit [Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pro přidání služeb role:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end


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
# <a name="role-based-authorization-in-aspnet-core"></a>Autorizace na základě rolí v ASP.NET Core

<a name="security-authorization-role-based"></a>

Když se vytvoří identitu, která může patřit do jedné nebo více rolí. Například Tracy může patřit do role správce a uživatele, zatímco Scott lze přiřadit pouze k roli uživatele. Způsob vytvoření a správa těchto rolí závisí na záložním úložišti proces autorizace. Role jsou vystaveny pro vývojáře prostřednictvím [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) metodu [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) třídy.

::: moniker range=">= aspnetcore-2.0"

> [!IMPORTANT]
> Toto téma se **není** platí pro stránky Razor. Stránky Razor podporuje [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter) a [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter). Další informace najdete v tématu [metody filtrování pro Razor Pages](xref:razor-pages/filter).

::: moniker-end

## <a name="adding-role-checks"></a>Přidání kontroly role

Kontroly autorizace na základě rolí jsou deklarativní&mdash;vývojář vloží je do jejich kód proti kontroler nebo akce v kontroleru, zadání role, které aktuální uživatel musí být členem skupiny pro přístup k požadovanému prostředku.

Například následující kód omezuje přístup na všechny akce na `AdministrationController` uživatelům, kteří jsou členy `Administrator` role:

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Můžete určit víc rolí jako seznam oddělený čárkami:

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Tento kontroler by přístupný pouze uživatelé, kteří jsou členy z `HRManager` role nebo `Finance` role.

Pokud použijete víc atributů uživatele s přístupem k musí být členem všech rolí zadaný; Následující ukázka vyžaduje, že uživatel musí být členem obou `PowerUser` a `ControlPanelUser` role.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Dále můžete omezit přístup s použitím atributů autorizace další role na úrovni akce:

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

V předchozím kódu fragmentu členů `Administrator` role nebo `PowerUser` role kontroleru přístup a `SetTime` akce, ale jen členové `Administrator` role přístup `ShutDown` akce.

Můžete také uzamknout řadiči, ale povolit anonymní, neověření přístup k jednotlivým akcím.

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

## <a name="policy-based-role-checks"></a>Kontroly rolí na základě zásad

Požadavky na role se dají vyjádřit také pomocí nové zásady syntaxe, kde vývojáři zaregistruje jako součást konfigurace služby ověření zásad při spuštění. K tomuto obvykle dochází u `ConfigureServices()` ve vašich *Startup.cs* souboru.

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

Zásady se aplikují pomocí `Policy` vlastnost `AuthorizeAttribute` atribut:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Pokud chcete zadat víc rolí povolené v požadavku, můžete je zadat jako parametry `RequireRole` metody:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Tento příklad povolí uživatelům patřícím do `Administrator`, `PowerUser` nebo `BackupAdministrator` role.

### <a name="add-role-services-to-identity"></a>Přidání služby Role na identitu

Připojit [kliknutím na Přidat role](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) přidat služby rolí:

[!code-csharp[](roles/samples/Startup.cs?name=snippet&highlight=7)]
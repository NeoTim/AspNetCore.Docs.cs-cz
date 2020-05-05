---
title: Ověřování na základě deklarací identity v ASP.NET Core
author: rick-anderson
description: Naučte se, jak do aplikace ASP.NET Core přidat kontroly deklarací identity pro autorizaci.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/claims
ms.openlocfilehash: de8ab915e6a8529c7401f89fad067ec33d5d0713
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774415"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>Ověřování na základě deklarací identity v ASP.NET Core

<a name="security-authorization-claims-based"></a>

Při vytvoření identity může být přiřazena jedna nebo více deklarací identity vydaných důvěryhodnou stranou. Deklarace identity je dvojice název hodnota, která představuje, co je předmět, a ne co může předmět dělat. Můžete mít například licenci řidiče, kterou vystavila místní řídící autorita pro vydávání licencí. Vaše licence k vašemu ovladači má datum narození. V takovém případě bude název deklarace identity `DateOfBirth`, hodnota deklarace by byla vaše datum narození, například `8th June 1970` , a vystavitelem bude řídící licenční autorita. Autorizace založená na deklaracích, která je nejjednodušší, kontroluje hodnotu deklarace identity a umožňuje přístup k prostředku na základě této hodnoty. Například pokud chcete mít přístup k nočnímu klubu, může být proces autorizace:

Pracovník zabezpečení dveří vyhodnotí hodnotu vašeho data narození a bez ohledu na to, jestli důvěřuje vystaviteli (řídící licenční autorita) před udělením přístupu.

Identita může obsahovat více deklarací s více hodnotami a může obsahovat více deklarací stejného typu.

## <a name="adding-claims-checks"></a>Přidání kontrol deklarací identity

Kontroly autorizace na základě deklarace identity jsou deklarativní – vývojář je vloží do svého kódu, před kontroler nebo akci v rámci kontroleru, určí deklarace identity, které musí mít aktuální uživatel, a volitelně hodnotu, kterou musí deklarace identity držet pro přístup k požadovanému prostředku. Požadavky na deklarace identity jsou založené na zásadách, vývojář musí sestavit a zaregistrovat zásady, které vyjadřují požadavky na deklarace identity.

Nejjednodušší typ zásad deklarace identity vyhledá přítomnost deklarace identity a nekontroluje hodnotu.

Nejdřív je potřeba tuto zásadu sestavit a zaregistrovat. K tomu dochází v rámci konfigurace autorizační služby, která obvykle využívá součást `ConfigureServices()` v souboru *Startup.cs* .

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
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
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

V takovém případě `EmployeeOnly` zásady kontrolují přítomnost `EmployeeNumber` deklarace identity na aktuální identitě.

Pak použijete zásadu pomocí `Policy` vlastnosti u `AuthorizeAttribute` atributu k zadání názvu zásady;

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

`AuthorizeAttribute` Atribut lze použít pro celý kontroler. v této instanci budou mít přístup k jakékoli akci na řadiči povolené jenom identity, které odpovídají zásadám.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

Pokud máte kontroler, který je chráněný `AuthorizeAttribute` atributem, ale chcete, aby byl anonymní přístup povolen k určitým akcím, `AllowAnonymousAttribute` které použijete v atributu.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

Většina deklarací přináší hodnotu. Při vytváření zásad můžete zadat seznam povolených hodnot. Následující příklad by byl úspěšný jenom pro zaměstnance, jejichž číslo zaměstnance bylo 1, 2, 3, 4 nebo 5.

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
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
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a>Přidat obecnou kontrolu deklarací identity

Pokud hodnota deklarace identity není jediná hodnota, nebo je nutné transformovat, použijte [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion). Další informace najdete v tématu [použití funkce Func ke splnění zásad](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Vícenásobné vyhodnocení zásad

Pokud pro kontroler nebo akci použijete více zásad, musí všechny zásady před udělením přístupu projít. Příklad:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

V předchozím příkladu bude mít přístup k `EmployeeOnly` `Payslip` akci, když se tato zásada na řadiči vynutila, na základě identity, která tyto zásady splňuje. Pro volání `UpdateSalary` akce musí identita *ale splňovat* `EmployeeOnly` zásady a `HumanResources` zásady.

Pokud chcete složitější zásady, jako je třeba vynechání data narození, vypočítání stáří z něj a kontrola stáří 21 nebo starší, budete muset napsat [vlastní obslužné rutiny zásad](xref:security/authorization/policies).

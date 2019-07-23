---
title: Autorizace na základě rolí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak přidat kontroly deklarací identity pro autorizaci v aplikaci ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: 6b60ae5515819b017ab577f655ed91ee4d8ed0dd
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086157"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>Autorizace na základě rolí v ASP.NET Core

<a name="security-authorization-claims-based"></a>

Když se vytvoří identitu, která může přiřadit jeden nebo více deklarací identity vystavených důvěryhodná strana. Deklarace identity je, že je dvojice název-hodnota, která představuje jaké předmětu, můžete provést není co předmět. Například může mít ovladače licence vydané místní autorita dodávala licence. Ve vašem ovladači licenci, má vaše datum narození. V tomto případě bude název deklarace `DateOfBirth`, hodnota deklarace identity by například vaše datum narození, `8th June 1970` a vystavitele by dodávala licenční oprávnění. Autorizace deklarovaných v nejjednodušším zkontroluje hodnotu vlastnosti deklarace identity a umožňuje přístup k prostředkům na základě této hodnoty. Příklad: Pokud chcete, aby přístup k club noční proces autorizace může být:

Dvířka knihovny bezpečnostního úředníka by vyhodnotit hodnotu vaše datum narození deklarace identity a určuje, zda důvěřují vystavitele (řízení licenční oprávnění) před udělením přístupu.

Identitu, která může obsahovat více deklarací identity s více hodnotami a může obsahovat více deklarací stejného typu.

## <a name="adding-claims-checks"></a>Přidání deklarace identity kontroly

Deklarace identity jsou deklarativní kontroly autorizace na základě – vývojář vloží je do jejich kód proti kontroler nebo akce v kontroleru, určení deklarace identity, která má aktuální uživatel musí mít, a volitelně musí obsahovat hodnotu deklarace identity pro přístup k požadovaný prostředek. Deklarace identity jsou požadavky na základě zásad, vývojář musí sestavení a registrací zásadu vyjádření požadavky deklarací identity.

O nejjednodušší typ deklarace identity zásad hledá přítomnost deklarace identity a neprovádí kontrolu hodnoty.

Je třeba nejprve pro sestavení a registrací zásady. Tato akce se provede jako součást konfigurace autorizace služby, které obvykle trvá část v `ConfigureServices()` ve vašich *Startup.cs* souboru.

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

V tomto případě `EmployeeOnly` zásad kontroly na přítomnost `EmployeeNumber` na aktuální identitu deklarací identity.

Pak použijete pomocí zásad `Policy` vlastnost `AuthorizeAttribute` atribut zadejte název zásady;

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

`AuthorizeAttribute` Atribut můžete použít pro celý kontroler, v tomto případě jen identit odpovídající zásady bude jim povolen přístup k žádné akci na řadiči.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

Pokud máte kontroler, který je chráněn `AuthorizeAttribute` atribut, ale chcete umožnit anonymní přístup k určité akce, které můžete použít `AllowAnonymousAttribute` atribut.

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

Většina deklarací identity obsahuje hodnotu. Seznam povolených hodnot můžete zadat při vytváření zásad. V následujícím příkladu by pouze uspěl pro zaměstnance jehož číslo bylo 1, 2, 3, 4 nebo 5.

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

### <a name="add-a-generic-claim-check"></a>Přidat kontrolu obecné deklarace identity

Pokud hodnota deklarace identity není jedinou hodnotu nebo transformace je povinný, použijte [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion). Další informace najdete v tématu [pomocí funkci func pro splnění zásad](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Vícenásobné vyhodnocení zásad

Pokud použijete víc zásad na kontroler nebo akce, musíte předat všechny zásady, před udělením přístupu. Příklad:

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

V předchozím příkladu všechny identity, který splňuje požadavky `EmployeeOnly` zásad se dostanete `Payslip` akci, jako tyto zásady se nevynutí na řadiči. Ale pro volání `UpdateSalary` akce musí splňovat identitu *obě* `EmployeeOnly` zásad a `HumanResources` zásad.

Pokud chcete složitější zásady, jako je například trvá datum narození deklarace identity, výpočtu stáří z něj pak kontroluje stáří 21 nebo starší pak budete muset napsat [obslužné rutiny vlastních zásad](xref:security/authorization/policies).

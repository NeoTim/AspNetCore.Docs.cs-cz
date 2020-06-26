---
title: Jednoduché ověřování v ASP.NET Core
author: rick-anderson
description: Naučte se používat atribut autorizovat k omezení přístupu k ASP.NET Core řadičům a akcím.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: 497103a14591476f3167602631b6b011264f5086
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408328"
---
# <a name="simple-authorization-in-aspnet-core"></a>Jednoduché ověřování v ASP.NET Core

<a name="security-authorization-simple"></a>

Autorizaci v ASP.NET Core řídí s <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> a jeho různými parametry. V nejjednodušším formuláři použití `[Authorize]` atributu na kontroler, akce nebo Razor stránku omezuje přístup k této součásti na libovolného ověřeného uživatele.

Například následující kód omezuje přístup k `AccountController` libovolnému ověřenému uživateli.

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

Pokud chcete použít autorizaci pro akci místo kontroleru, použijte `AuthorizeAttribute` atribut na vlastní akci:

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

K této funkci mají přístup jenom ověření uživatelé `Logout` .

Atribut můžete použít také `AllowAnonymous` k povolení přístupu neověřeným uživatelům k jednotlivým akcím. Například:

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

To umožní pouze ověřeným uživatelům v `AccountController` , s výjimkou `Login` akce, která je přístupná všem, bez ohledu na jejich ověřený nebo neověřený nebo anonymní stav.

> [!WARNING]
> `[AllowAnonymous]`obchází všechny příkazy autorizace. Pokud zkombinujete `[AllowAnonymous]` a jakýkoliv `[Authorize]` atribut, `[Authorize]` atributy se ignorují. Například pokud použijete `[AllowAnonymous]` na úrovni řadiče, všechny `[Authorize]` atributy na stejném kontroleru (nebo na jakékoli akci v ní) se ignorují.

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a>Autorizovat atributy a Razor stránky

Nelze <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> použít ***not*** na Razor obslužné rutiny stránky. Například `[Authorize]` nelze použít pro `OnGet` , `OnPost` nebo žádné jiné obslužné rutiny stránky. Zvažte použití ASP.NET Coreho kontroleru MVC pro stránky s různými autorizačními požadavky pro různé obslužné rutiny.

K použití autorizace na obslužné rutiny stránky lze použít následující dva způsoby Razor :

* Pro obslužné rutiny stránek, které vyžadují jinou autorizaci, použijte samostatné stránky. Sdílený obsah byl přesunut do jednoho nebo více [částečných zobrazení](xref:mvc/views/partial). Pokud je to možné, jedná se o doporučený postup.
* Pro obsah, který musí sdílet společnou stránku, napište filtr, který provádí autorizaci jako součást [IAsyncPageFilter. OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A). Projekt GitHub [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) demonstruje tento přístup:
  * [AuthorizePageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizePageHandlerFilter.cs) implementuje ověřovací filtr:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]

  * Atribut [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) je použit pro `OnGet` obslužnou rutinu stránky:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]

> [!WARNING]
> Vzorový přístup [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) ***není***:
> * Můžete vytvářet pomocí autorizačních atributů, které se aplikují na stránku, model stránky nebo globálně. Sestavování autorizačních atributů má za následek ověřování a autorizaci, která se spouští několikrát, když máte `AuthorizeAttribute` `AuthorizeFilter` na stránce použit i jeden další výskyt.
> * Pracujte ve spojení se zbytkem ASP.NET Core ověřování a autorizačním systémem. Je nutné ověřit, že tento přístup bude správně fungovat pro vaši aplikaci.

Neexistují žádné plány pro podporu pro `AuthorizeAttribute` Razor obslužné rutiny stránky. 

---
title: Migrace z ClaimsPrincipal. Current
author: mjrousos
description: Naučte se migrovat z ClaimsPrincipal. Current a načíst identitu a deklarace identity aktuálně ověřeného uživatele v ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
uid: migration/claimsprincipal-current
ms.openlocfilehash: f7472f5b851d3869da3d26b881e276ce4ca004fb
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74115965"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrace z ClaimsPrincipal. Current

V projektech ASP.NET 4. x bylo běžné použít [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) k načtení identity a deklarací identity aktuálního ověřeného uživatele. V ASP.NET Core Tato vlastnost již není nastavena. Kód, který byl v závislosti na tom, je nutné aktualizovat, aby získal aktuální identitu ověřeného uživatele jiným způsobem.

## <a name="context-specific-data-instead-of-static-data"></a>Data specifická pro kontext místo statických dat

Při použití ASP.NET Core nejsou nastaveny hodnoty obou `ClaimsPrincipal.Current` a `Thread.CurrentPrincipal`. Obě tyto vlastnosti znázorňují statický stav, který ASP.NET Core obecně vyhnout. Místo toho je ASP.NET Core architektury načítat závislosti (například identitu aktuálního uživatele) z kolekcí služeb specifických pro konkrétní kontext (pomocí modelu pro [vkládání závislostí](xref:fundamentals/dependency-injection) (di)). A co víc, `Thread.CurrentPrincipal` je vlákno statické, takže v některých asynchronních scénářích nemusíte zachovávat změny (a `ClaimsPrincipal.Current` jenom volání `Thread.CurrentPrincipal` ve výchozím nastavení).

Pro pochopení seřazení statických členů vláken může v asynchronních scénářích vést k použití následujícího fragmentu kódu:

```csharp
// Create a ClaimsPrincipal and set Thread.CurrentPrincipal
var identity = new ClaimsIdentity();
identity.AddClaim(new Claim(ClaimTypes.Name, "User1"));
Thread.CurrentPrincipal = new ClaimsPrincipal(identity);

// Check the current user
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");

// For the method to complete asynchronously
await Task.Yield();

// Check the current user after
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");
```

Předchozí ukázkový kód nastaví `Thread.CurrentPrincipal` a zkontroluje jeho hodnotu před a po očekávání asynchronního volání. `Thread.CurrentPrincipal` je specifické pro *vlákno* , na kterém je nastavena, a metoda je zřejmě pokračovat v provádění v jiném vlákně po očekávání. V důsledku toho `Thread.CurrentPrincipal` k dispozici, pokud je nejprve zaškrtnuto, ale po volání `await Task.Yield()`je null.

Získávání identity aktuálního uživatele z kolekce služeb DI Service je více testovatelné, protože testovací identity je možné snadno vložit.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Načtení aktuálního uživatele v aplikaci ASP.NET Core

K dispozici je několik možností, jak načíst aktuální `ClaimsPrincipal` ověřeného uživatele v ASP.NET Core místo `ClaimsPrincipal.Current`:

* **ControllerBase. User**. Řadiče MVC mají přístup k aktuálnímu ověřenému uživateli s vlastností [uživatele](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User**. Komponenty s přístupem k aktuálnímu `HttpContext` (například middleware) mohou získat aktuálního uživatele `ClaimsPrincipal` z [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Předáno od volajícího**. Knihovny bez přístupu k aktuálnímu `HttpContext` jsou často volány z řadičů nebo součástí middlewaru a mohou mít identitu aktuálního uživatele předanou jako argument.
* **IHttpContextAccessor**. Projekt, který je migrován do ASP.NET Core může být příliš velký, aby mohl snadno předat identitu aktuálního uživatele do všech potřebných umístění. V takových případech je možné [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) použít jako alternativní řešení. `IHttpContextAccessor` je schopný získat přístup k aktuálnímu `HttpContext` (pokud existuje). Pokud se používá DI, přečtěte si téma <xref:fundamentals/httpcontext>. Krátkodobé řešení pro získání identity aktuálního uživatele v kódu, který se ještě neaktualizoval, aby fungoval s architekturou založenou na ASP.NET Core DI, by byl:

  * Zpřístupněte `IHttpContextAccessor` k dispozici v kontejneru DI voláním [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) v `Startup.ConfigureServices`.
  * Během spouštění získat instanci `IHttpContextAccessor` a uložit ji do statické proměnné. Instance je zpřístupněna kódu, který dříve načítá aktuálního uživatele ze statické vlastnosti.
  * Načte `ClaimsPrincipal` aktuálního uživatele pomocí `HttpContextAccessor.HttpContext?.User`. Pokud je tento kód použit mimo kontext požadavku HTTP, `HttpContext` je null.

Poslední možnost, která používá instanci `IHttpContextAccessor` uloženou ve statické proměnné, je v rozporu se zásadami ASP.NET Core (předchází vložené závislosti do statických závislostí). Místo toho naplánujte, abyste nakonec načetli `IHttpContextAccessor` instance z injektáže závislosti. Statická pomoc může být užitečný most, ale při migraci velkých stávajících aplikací ASP.NET, které dříve používaly `ClaimsPrincipal.Current`.

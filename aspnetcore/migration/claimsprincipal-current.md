---
title: Migrace z ClaimsPrincipal. Current
author: mjrousos
description: Naučte se migrovat z ClaimsPrincipal. Current a načíst identitu a deklarace identity aktuálně ověřeného uživatele v ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: 5f6b5b960eacf176088d9fc60f9ba16014e613fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776087"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrace z ClaimsPrincipal. Current

V projektech ASP.NET 4. x bylo běžné použít [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) k načtení identity a deklarací identity aktuálního ověřeného uživatele. V ASP.NET Core Tato vlastnost již není nastavena. Kód, který byl v závislosti na tom, je nutné aktualizovat, aby získal aktuální identitu ověřeného uživatele jiným způsobem.

## <a name="context-specific-data-instead-of-static-data"></a>Data specifická pro kontext místo statických dat

Při použití ASP.NET Core hodnoty `ClaimsPrincipal.Current` a `Thread.CurrentPrincipal` nejsou nastaveny. Obě tyto vlastnosti znázorňují statický stav, který ASP.NET Core obecně vyhnout. Místo toho je ASP.NET Core architektury načítat závislosti (například identitu aktuálního uživatele) z kolekcí služeb specifických pro konkrétní kontext (pomocí modelu pro [vkládání závislostí](xref:fundamentals/dependency-injection) (di)). Co je více, `Thread.CurrentPrincipal` je vlákno statické, takže nesmí zachovávat změny v některých asynchronních scénářích ( `ClaimsPrincipal.Current` a ve `Thread.CurrentPrincipal` výchozím nastavení pouze volání).

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

Předchozí vzor kódu nastaví `Thread.CurrentPrincipal` a zkontroluje jeho hodnotu před a po čekání na asynchronní volání. `Thread.CurrentPrincipal`je specifický pro *vlákno* , ve kterém je nastaveno, a metoda je zřejmě pokračovat v provádění v jiném vlákně po očekávání. V důsledku `Thread.CurrentPrincipal` toho je k dispozici, pokud je nejprve zaškrtnuto, ale po volání `await Task.Yield()`metody je null.

Získávání identity aktuálního uživatele z kolekce služeb DI Service je více testovatelné, protože testovací identity je možné snadno vložit.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Načtení aktuálního uživatele v aplikaci ASP.NET Core

Existuje několik možností, jak načíst aktuálního ověřeného uživatele `ClaimsPrincipal` v ASP.NET Core místo: `ClaimsPrincipal.Current`

* **ControllerBase. User**. Řadiče MVC mají přístup k aktuálnímu ověřenému uživateli s vlastností [uživatele](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User**. Komponenty s přístupem k aktuálnímu `HttpContext` (middlewaru) mohou získat aktuálního uživatele `ClaimsPrincipal` z [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Předáno od volajícího**. Knihovny bez přístupu k aktuálnímu `HttpContext` jsou často volány z řadičů nebo součástí middlewaru a mohou mít identitu aktuálního uživatele předanou jako argument.
* **IHttpContextAccessor**. Projekt, který je migrován do ASP.NET Core může být příliš velký, aby mohl snadno předat identitu aktuálního uživatele do všech potřebných umístění. V takových případech je možné [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) použít jako alternativní řešení. `IHttpContextAccessor`je schopný získat přístup k aktuálnímu `HttpContext` (pokud existuje). Pokud se používá DI, přečtěte <xref:fundamentals/httpcontext>si téma. Krátkodobé řešení pro získání identity aktuálního uživatele v kódu, který se ještě neaktualizoval, aby fungoval s architekturou založenou na ASP.NET Core DI, by byl:

  * Zpřístupněte `IHttpContextAccessor` v kontejneru di voláním [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) v `Startup.ConfigureServices`.
  * Získat instanci `IHttpContextAccessor` při spuštění a uložit ji do statické proměnné. Instance je zpřístupněna kódu, který dříve načítá aktuálního uživatele ze statické vlastnosti.
  * Načte aktuálního uživatele `ClaimsPrincipal` pomocí `HttpContextAccessor.HttpContext?.User`. Pokud je tento kód použit mimo kontext požadavku HTTP, `HttpContext` má hodnotu null.

Poslední možnost, která používá `IHttpContextAccessor` instanci uloženou ve statické proměnné, je v rozporu se zásadami ASP.NET Core (předkládání vložených závislostí na statické závislosti). Naplánujte nakonec `IHttpContextAccessor` načtení instancí z injektáže závislosti. Statická pomoc může být užitečný most, ale při migraci velkých stávajících aplikací ASP.NET, které dříve používaly `ClaimsPrincipal.Current`.

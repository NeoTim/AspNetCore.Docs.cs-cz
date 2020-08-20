---
title: Migrace z ClaimsPrincipal. Current
author: mjrousos
description: Naučte se migrovat z ClaimsPrincipal. Current a načíst identitu a deklarace identity aktuálně ověřeného uživatele v ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: 426fd90374a460cb283d0d3ba921e1312fb17940
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634069"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrace z ClaimsPrincipal. Current

V projektech ASP.NET 4. x bylo běžné použít [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) k načtení identity a deklarací identity aktuálního ověřeného uživatele. V ASP.NET Core Tato vlastnost již není nastavena. Kód, který byl v závislosti na tom, je nutné aktualizovat, aby získal aktuální identitu ověřeného uživatele jiným způsobem.

## <a name="context-specific-state-instead-of-static-state"></a>Stav specifický pro kontext, nikoli pro statický stav

Při použití ASP.NET Core hodnoty `ClaimsPrincipal.Current` a `Thread.CurrentPrincipal` nejsou nastaveny. Obě tyto vlastnosti znázorňují statický stav, který ASP.NET Core obecně vyhnout. Místo toho ASP.NET Core používá [vkládání závislostí](xref:fundamentals/dependency-injection) (di) k poskytnutí závislostí, jako je například identita aktuálního uživatele. Získání totožnosti aktuálního uživatele od DI je víc testovatelné, protože testovací identity je možné snadno vložit.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Načtení aktuálního uživatele v aplikaci ASP.NET Core

Existuje několik možností, jak načíst aktuálního ověřeného uživatele `ClaimsPrincipal` v ASP.NET Core místo `ClaimsPrincipal.Current` :

* **ControllerBase. User**. Řadiče MVC mají přístup k aktuálnímu ověřenému uživateli s vlastností [uživatele](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User**. Komponenty s přístupem k aktuálnímu `HttpContext` (middlewaru) mohou získat aktuálního uživatele `ClaimsPrincipal` z [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Předáno od volajícího**. Knihovny bez přístupu k aktuálnímu `HttpContext` jsou často volány z řadičů nebo součástí middlewaru a mohou mít identitu aktuálního uživatele předanou jako argument.
* **IHttpContextAccessor**. Projekt, který je migrován do ASP.NET Core může být příliš velký, aby mohl snadno předat identitu aktuálního uživatele do všech potřebných umístění. V takových případech je možné [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) použít jako alternativní řešení. `IHttpContextAccessor` je schopný získat přístup k aktuálnímu `HttpContext` (pokud existuje). Pokud se používá DI, přečtěte si téma <xref:fundamentals/httpcontext> . Krátkodobé řešení pro získání identity aktuálního uživatele v kódu, který se ještě neaktualizoval, aby fungoval s architekturou založenou na ASP.NET Core DI, by byl:

  * Zpřístupněte `IHttpContextAccessor` v kontejneru di voláním [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) v `Startup.ConfigureServices` .
  * Získat instanci `IHttpContextAccessor` při spuštění a uložit ji do statické proměnné. Instance je zpřístupněna kódu, který dříve načítá aktuálního uživatele ze statické vlastnosti.
  * Načte aktuálního uživatele `ClaimsPrincipal` pomocí `HttpContextAccessor.HttpContext?.User` . Pokud je tento kód použit mimo kontext požadavku HTTP, má `HttpContext` hodnotu null.

Poslední možnost, která používá `IHttpContextAccessor` instanci uloženou ve statické proměnné, je v rozporu s principem ASP.NET Core předvodit vložené závislosti na statické závislosti. Naplánujte nakonec načtení `IHttpContextAccessor` instancí z di místo. Statická pomoc může být užitečný most, ale při migraci velkých stávajících aplikací ASP.NET, které používají `ClaimsPrincipal.Current` .

---
title: Injektáž závislostí v obslužné rutině požadavek v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vložit obslužné rutiny požadavek ověřování do aplikace ASP.NET Core pomocí vkládání závislostí.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 71d563e11d308a95c08e6d012d3a071f4697d2de
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902505"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Injektáž závislostí v obslužné rutině požadavek v ASP.NET Core

<a name="security-authorization-di"></a>

[Povolení obslužné rutiny musí být zaregistrovaný](xref:security/authorization/policies#handler-registration) v kolekci služby během konfigurace (pomocí [injektáž závislostí](xref:fundamentals/dependency-injection)).

Předpokládejme, že jste měli úložiště pravidel, který chcete vyhodnotit uvnitř obslužné rutiny autorizace a tohoto úložiště bylo registrováno v kolekci služby. Autorizace se vyřešit a vložení, který do vašeho konstruktoru.

Například, pokud jste chtěli použít ASP. NET je protokolování infrastruktury budete pravděpodobně chtít vložit `ILoggerFactory` do vaší obslužné rutiny. Tato obslužná rutina může vypadat:

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

By zaregistrovat obslužnou rutinu s `services.AddSingleton()`:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Instance se obslužná rutina se vytvoří při spuštění aplikace a bude DI vložit zaregistrovanou `ILoggerFactory` do vaší konstruktoru.

> [!NOTE]
> Obslužné rutiny, které používají rozhraní Entity Framework by neměla být registrována jako jednotlivé prvky.

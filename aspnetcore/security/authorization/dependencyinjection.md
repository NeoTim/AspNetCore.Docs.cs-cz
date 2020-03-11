---
title: Vkládání závislostí v obslužných rutinách požadavků v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak vložit obslužné rutiny autorizačních požadavků do aplikace ASP.NET Core pomocí injektáže závislosti.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 71d563e11d308a95c08e6d012d3a071f4697d2de
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666087"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Vkládání závislostí v obslužných rutinách požadavků v ASP.NET Core

<a name="security-authorization-di"></a>

[Obslužné rutiny autorizace musí být registrovány](xref:security/authorization/policies#handler-registration) v kolekci služby během konfigurace (pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection)).

Předpokládejme, že máte úložiště pravidel, která jste chtěli vyhodnotit uvnitř obslužné rutiny autorizace, a toto úložiště bylo zaregistrováno v kolekci služeb. Autorizace se vyřeší a vloží do vašeho konstruktoru.

Například pokud jste chtěli použít ASP. Infrastruktura protokolování sítě, kterou chcete vložit `ILoggerFactory` do obslužné rutiny. Obslužná rutina může vypadat takto:

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

Obslužná rutina by měla být zaregistrována s `services.AddSingleton()`:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Instance obslužné rutiny se vytvoří při spuštění aplikace a příkaz DI vloží registrovanou `ILoggerFactory` do konstruktoru.

> [!NOTE]
> Obslužné rutiny, které používají Entity Framework, by neměly být registrovány jako singleton.

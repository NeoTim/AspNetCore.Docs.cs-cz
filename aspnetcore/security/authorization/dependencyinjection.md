---
title: Vkládání závislostí v obslužných rutinách požadavků v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak vložit obslužné rutiny autorizačních požadavků do aplikace ASP.NET Core pomocí injektáže závislosti.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 16285f6f731455d6e45a04f82437793891a77668
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775117"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="89fdc-103">Vkládání závislostí v obslužných rutinách požadavků v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="89fdc-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="89fdc-104">[Obslužné rutiny autorizace musí být registrovány](xref:security/authorization/policies#handler-registration) v kolekci služby během konfigurace (pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="89fdc-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="89fdc-105">Předpokládejme, že máte úložiště pravidel, která jste chtěli vyhodnotit uvnitř obslužné rutiny autorizace, a toto úložiště bylo zaregistrováno v kolekci služeb.</span><span class="sxs-lookup"><span data-stu-id="89fdc-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="89fdc-106">Autorizace se vyřeší a vloží do vašeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="89fdc-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="89fdc-107">Například pokud jste chtěli použít ASP. Infrastruktura protokolování sítě, kterou chcete vložit `ILoggerFactory` do obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="89fdc-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="89fdc-108">Obslužná rutina může vypadat takto:</span><span class="sxs-lookup"><span data-stu-id="89fdc-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="89fdc-109">Zaregistrujte obslužnou rutinu `services.AddSingleton()`pomocí:</span><span class="sxs-lookup"><span data-stu-id="89fdc-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="89fdc-110">Instance obslužné rutiny se vytvoří při spuštění aplikace a příkaz DI zapíše do konstruktoru registraci `ILoggerFactory` .</span><span class="sxs-lookup"><span data-stu-id="89fdc-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="89fdc-111">Obslužné rutiny, které používají Entity Framework, by neměly být registrovány jako singleton.</span><span class="sxs-lookup"><span data-stu-id="89fdc-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>

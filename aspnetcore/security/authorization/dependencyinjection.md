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
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="dec9c-103">Injektáž závislostí v obslužné rutině požadavek v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dec9c-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="dec9c-104">[Povolení obslužné rutiny musí být zaregistrovaný](xref:security/authorization/policies#handler-registration) v kolekci služby během konfigurace (pomocí [injektáž závislostí](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="dec9c-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="dec9c-105">Předpokládejme, že jste měli úložiště pravidel, který chcete vyhodnotit uvnitř obslužné rutiny autorizace a tohoto úložiště bylo registrováno v kolekci služby.</span><span class="sxs-lookup"><span data-stu-id="dec9c-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="dec9c-106">Autorizace se vyřešit a vložení, který do vašeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="dec9c-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="dec9c-107">Například, pokud jste chtěli použít ASP. NET je protokolování infrastruktury budete pravděpodobně chtít vložit `ILoggerFactory` do vaší obslužné rutiny.</span><span class="sxs-lookup"><span data-stu-id="dec9c-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="dec9c-108">Tato obslužná rutina může vypadat:</span><span class="sxs-lookup"><span data-stu-id="dec9c-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="dec9c-109">By zaregistrovat obslužnou rutinu s `services.AddSingleton()`:</span><span class="sxs-lookup"><span data-stu-id="dec9c-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="dec9c-110">Instance se obslužná rutina se vytvoří při spuštění aplikace a bude DI vložit zaregistrovanou `ILoggerFactory` do vaší konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="dec9c-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="dec9c-111">Obslužné rutiny, které používají rozhraní Entity Framework by neměla být registrována jako jednotlivé prvky.</span><span class="sxs-lookup"><span data-stu-id="dec9c-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>

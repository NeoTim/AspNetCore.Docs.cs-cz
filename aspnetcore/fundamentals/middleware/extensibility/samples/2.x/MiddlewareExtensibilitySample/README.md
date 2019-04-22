---
ms.openlocfilehash: 41021e30ae85dd0ae42cbe6f1606727e21bd7707
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705520"
---
# <a name="aspnet-core-middleware-extensibility-sample"></a><span data-ttu-id="85790-101">Ukázky rozšiřitelnosti Middleware ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="85790-101">ASP.NET Core Middleware Extensibility Sample</span></span>

<span data-ttu-id="85790-102">Tato ukázka demonstruje scénáře popsané v [aktivace middleware založený na objekt pro vytváření v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).</span><span class="sxs-lookup"><span data-stu-id="85790-102">This sample demonstrates the scenarios described in [Factory-based middleware activation in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).</span></span>

<span data-ttu-id="85790-103">Ukázková aplikace předvádí middleware aktivoval:</span><span class="sxs-lookup"><span data-stu-id="85790-103">The sample app demonstrates middleware activated by:</span></span>

* <span data-ttu-id="85790-104">Konvence.</span><span class="sxs-lookup"><span data-stu-id="85790-104">Convention.</span></span> <span data-ttu-id="85790-105">Další informace o aktivaci konvenční middleware, najdete v článku [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) tématu.</span><span class="sxs-lookup"><span data-stu-id="85790-105">For more information on conventional middleware activation, see the [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) topic.</span></span>
* <span data-ttu-id="85790-106">[IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) implementace.</span><span class="sxs-lookup"><span data-stu-id="85790-106">An [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) implementation.</span></span> <span data-ttu-id="85790-107">Výchozí hodnota [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) třídy aktivuje middleware.</span><span class="sxs-lookup"><span data-stu-id="85790-107">The default [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) class activates the middleware.</span></span>

<span data-ttu-id="85790-108">Implementace middlewaru fungovat stejně jako a si poznamenejte hodnotu poskytovanou infrastrukturou parametru řetězce dotazu (`key`).</span><span class="sxs-lookup"><span data-stu-id="85790-108">The middleware implementations function identically and record the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="85790-109">Middlewares objekt context vloženého databáze (vymezené služby) slouží k zaznamenání hodnotu řetězce dotazu v databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="85790-109">The middlewares use an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

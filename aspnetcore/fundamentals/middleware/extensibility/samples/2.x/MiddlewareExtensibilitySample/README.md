---
ms.openlocfilehash: 41021e30ae85dd0ae42cbe6f1606727e21bd7707
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705520"
---
# <a name="aspnet-core-middleware-extensibility-sample"></a>Ukázky rozšiřitelnosti Middleware ASP.NET Core

Tato ukázka demonstruje scénáře popsané v [aktivace middleware založený na objekt pro vytváření v ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).

Ukázková aplikace předvádí middleware aktivoval:

* Konvence. Další informace o aktivaci konvenční middleware, najdete v článku [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) tématu.
* [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) implementace. Výchozí hodnota [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) třídy aktivuje middleware.

Implementace middlewaru fungovat stejně jako a si poznamenejte hodnotu poskytovanou infrastrukturou parametru řetězce dotazu (`key`). Middlewares objekt context vloženého databáze (vymezené služby) slouží k zaznamenání hodnotu řetězce dotazu v databázi v paměti.

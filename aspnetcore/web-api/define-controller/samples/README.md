---
ms.openlocfilehash: 07abb12af390c0f2a50e98fc5e53545b6635f968
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665506"
---
# <a name="aspnet-core-web-api-controller-sample"></a>Ukázka Kontroleru rozhraní API ASP.NET Core Web

Tato ukázková aplikace se skládá z následující projekty:

- **WebApiSample.Api.22**: Projekt ASP.NET Core 2.2 cílí na .NET Core 2.2.
- **WebApiSample.Api.21**: Projekt ASP.NET Core 2.1 cílí na .NET Core 2.1.
- **WebApiSample.Api.Pre21**: Projekt ASP.NET Core 2.0, která cílí na .NET Core 2.0.
- **WebApiSample.DataAccess**: Knihovna tříd .NET Standard 2.0, který slouží jako vrstvě přístupu k datům pro 2 projekty webového rozhraní API.

Tato ukázka ilustruje variace vytvoření kontroleru webového rozhraní API:

- [Odvodit třídu z ControllerBase](https://docs.microsoft.com/aspnet/core/web-api#derive-class-from-controllerbase)
- [Třída s atributem ApiControllerAttribute opatřit poznámkami](https://docs.microsoft.com/aspnet/core/web-api#annotate-class-with-apicontrollerattribute)

---
title: Napište vlastní ASP.NET middleware Core
author: rick-anderson
description: Naučte se psát vlastní ASP.NET middleware Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: e74bba9e1bd826d4f493b0ee642a198f984daada
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663924"
---
# <a name="write-custom-aspnet-core-middleware"></a>Napište vlastní ASP.NET middleware Core

[Podle Rick Anderson](https://twitter.com/RickAndMSFT) a Steve [Smith](https://ardalis.com/)

Middleware je software, který je sestaven do kanálu aplikace pro zpracování požadavků a odpovědí. ASP.NET Core poskytuje bohatou sadu vestavěných middleware komponent, ale v některých scénářích možná budete chtít napsat vlastní middleware.

## <a name="middleware-class"></a>Třída Middleware

Middleware je obecně zapouzdřen ve třídě a vystavena s metodou rozšíření. Zvažte následující middleware, který nastaví jazykovou verzi pro aktuální požadavek z řetězce dotazu:

[!code-csharp[](write/snapshot/StartupCulture.cs)]

Předchozí ukázkový kód se používá k předvedení vytvoření součásti middleware. ASP.NET integrovanou podporu lokalizace jádra <xref:fundamentals/localization>najdete v tématu .

Otestujte middleware předáním v jazykové verzi. Například požadavek `https://localhost:5001/?culture=no`.

Následující kód přesune delegáta middlewaru do třídy:

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

Třída middleware musí obsahovat:

* Veřejný konstruktor s parametrem <xref:Microsoft.AspNetCore.Http.RequestDelegate>typu .
* Veřejná metoda `Invoke` `InvokeAsync`s názvem nebo . Tato metoda musí:
  * Vrátí `Task`. .
  * Přijměte první <xref:Microsoft.AspNetCore.Http.HttpContext>parametr typu .
  
Další parametry pro konstruktor u `Invoke` / `InvokeAsync` jsou [naplněny vkládání závislostí (DI)](xref:fundamentals/dependency-injection).

## <a name="middleware-dependencies"></a>Závislosti middlewaru

Middleware by se měl řídit [zásadou explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) tím, že odhalí své závislosti v jeho konstruktoru. Middleware je konstruován jednou za *životnost aplikace*. Pokud potřebujete sdílet služby s middlewarem v rámci žádosti, podívejte se do části [Závislosti middlewaru pro požadavek](#per-request-middleware-dependencies) na vyžádání.

Součásti middlewaru mohou vyřešit své závislosti z [vkládání závislostí (DI)](xref:fundamentals/dependency-injection) prostřednictvím parametrů konstruktoru. [UseMiddleware&lt;&gt; T](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) může také přijímat další parametry přímo.

## <a name="per-request-middleware-dependencies"></a>Závislosti middlewaru pro vyžádání

Vzhledem k tomu, middleware je vytvořen při spuštění aplikace, nikoli na vyžádání, *rozsahem* životnost služby používané middleware konstruktory nejsou sdíleny s jinými typy vložené závislostmi během každého požadavku. Pokud je nutné sdílet službu *s vymezeným oborem* mezi `Invoke` middlewarem a jinými typy, přidejte tyto služby do podpisu metody. Metoda `Invoke` může přijmout další parametry, které jsou naplněny DI:

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a>Middleware rozšiřující metoda

Následující metoda rozšíření zveřejňuje middleware <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>prostřednictvím :

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

Následující kód volá middleware `Startup.Configure`z :

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

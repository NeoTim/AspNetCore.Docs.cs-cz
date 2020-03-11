---
title: Zápis vlastního middlewaru ASP.NET Core
author: rick-anderson
description: Naučte se psát vlastní middleware ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: e74bba9e1bd826d4f493b0ee642a198f984daada
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663924"
---
# <a name="write-custom-aspnet-core-middleware"></a>Zápis vlastního middlewaru ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

Middleware je software, který je včleněn do kanálu aplikace a zpracovává požadavky a odpovědi. ASP.NET Core poskytuje bohatou sadu integrovaných komponent middlewaru, ale v některých případech můžete chtít napsat vlastní middleware.

## <a name="middleware-class"></a>Middleware – třída

Middleware je obecně zapouzdřena ve třídě a vystavena s metodou rozšíření. Vezměte v úvahu následující middleware, který nastaví jazykovou verzi pro aktuální požadavek z řetězce dotazu:

[!code-csharp[](write/snapshot/StartupCulture.cs)]

Předchozí vzorový kód slouží k předvedení vytváření komponenty middlewaru. Integrovanou podporu lokalizace ASP.NET Core najdete v tématu <xref:fundamentals/localization>.

Otestujte middleware předáním v jazykové verzi. Například požadavek `https://localhost:5001/?culture=no`.

Následující kód přesune delegáta middlewaru do třídy:

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

Třída middleware musí zahrnovat:

* Veřejný konstruktor s parametrem typu <xref:Microsoft.AspNetCore.Http.RequestDelegate>.
* Veřejná metoda s názvem `Invoke` nebo `InvokeAsync`. Tato metoda musí:
  * Vrátí `Task`.
  * Přijměte první parametr typu <xref:Microsoft.AspNetCore.Http.HttpContext>.
  
Další parametry pro konstruktor a `Invoke`/`InvokeAsync` jsou vyplněny pomocí [Injektáže závislosti (di)](xref:fundamentals/dependency-injection).

## <a name="middleware-dependencies"></a>Závislosti middlewaru

Middleware by měly následovat po [principu explicitní závislosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) tím, že vystaví jeho závislosti ve svém konstruktoru. Middleware je postaven jednou za *dobu života aplikace*. Pokud potřebujete v rámci žádosti sdílet služby se middlewarem, podívejte se na část [závislosti middlewaru na žádost](#per-request-middleware-dependencies) .

Komponenty middlewaru mohou vyřešit své závislosti z [Injektáže závislosti (di)](xref:fundamentals/dependency-injection) prostřednictvím parametrů konstruktoru. [UseMiddleware&lt;t&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) může také přímo přijmout další parametry.

## <a name="per-request-middleware-dependencies"></a>Závislosti middlewaru na požadavek

Vzhledem k tomu, že middleware se vytvářejí při spuštění aplikace, ne na vyžádání, jsou *služby životnosti* , které jsou používány konstruktory middleware, sdíleny s jinými typy vloženými závislostmi během každé žádosti. Pokud potřebujete sdílet *vymezenou* službu mezi middlewarem a jinými typy, přidejte tyto služby do signatury `Invoke` metody. Metoda `Invoke` může přijmout další parametry, které jsou vyplněny parametrem DI:

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

## <a name="middleware-extension-method"></a>Metoda rozšíření middlewaru

Následující rozšiřující metoda zpřístupňuje middleware prostřednictvím <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

Následující kód volá middleware z `Startup.Configure`:

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

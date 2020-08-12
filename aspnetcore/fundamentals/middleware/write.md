---
title: Zápis vlastního middlewaru ASP.NET Core
author: rick-anderson
description: Naučte se psát vlastní middleware ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/18/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/write
ms.openlocfilehash: 480120718959a364ce8008949c3b76c558c0e995
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130584"
---
# <a name="write-custom-aspnet-core-middleware"></a>Zápis vlastního middlewaru ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)

Middleware je software, který se sestaví do kanálu aplikace za účelem zpracování požadavků a odpovědí. ASP.NET Core poskytuje bohatou sadu integrovaných komponent middlewaru, ale v některých případech můžete chtít napsat vlastní middleware.

> [!NOTE]
> Toto téma popisuje, jak napsat middleware *založený na konvencích* . Přístup, který používá silné psaní a aktivaci podle požadavků, najdete v tématu <xref:fundamentals/middleware/extensibility> .

## <a name="middleware-class"></a>Middleware – třída

Middleware je obecně zapouzdřena ve třídě a vystavena s metodou rozšíření. Vezměte v úvahu následující middleware, který nastaví jazykovou verzi pro aktuální požadavek z řetězce dotazu:

[!code-csharp[](write/snapshot/StartupCulture.cs)]

Předchozí vzorový kód slouží k předvedení vytváření komponenty middlewaru. Integrovanou podporu lokalizace ASP.NET Core najdete v tématu <xref:fundamentals/localization> .

Otestujte middleware předáním v jazykové verzi. Například Request `https://localhost:5001/?culture=no` .

Následující kód přesune delegáta middlewaru do třídy:

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

Třída middleware musí zahrnovat:

* Veřejný konstruktor s parametrem typu <xref:Microsoft.AspNetCore.Http.RequestDelegate> .
* Veřejná metoda s názvem `Invoke` nebo `InvokeAsync` . Tato metoda musí:
  * Vrátí `Task` .
  * Přijměte první parametr typu <xref:Microsoft.AspNetCore.Http.HttpContext> .
  
Další parametry pro konstruktor a `Invoke` / `InvokeAsync` jsou vyplněny pomocí [Injektáže závislosti (di)](xref:fundamentals/dependency-injection).

## <a name="middleware-dependencies"></a>Závislosti middlewaru

Middleware by měly následovat po [principu explicitní závislosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) tím, že vystaví jeho závislosti ve svém konstruktoru. Middleware je postaven jednou za *dobu života aplikace*. Pokud potřebujete v rámci žádosti sdílet služby se middlewarem, podívejte se na část [závislosti middlewaru na žádost](#per-request-middleware-dependencies) .

Komponenty middlewaru mohou vyřešit své závislosti z [Injektáže závislosti (di)](xref:fundamentals/dependency-injection) prostřednictvím parametrů konstruktoru. [UseMiddleware &lt; T &gt; ](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) může také přijmout další parametry přímo.

## <a name="per-request-middleware-dependencies"></a>Závislosti middlewaru na požadavek

Vzhledem k tomu, že middleware se vytvářejí při spuštění aplikace, ne na vyžádání, jsou *služby životnosti* , které jsou používány konstruktory middleware, sdíleny s jinými typy vloženými závislostmi během každé žádosti. Pokud potřebujete sdílet *vymezenou* službu mezi middlewarem a jinými typy, přidejte tyto služby do `Invoke` signatury metody. `Invoke`Metoda může přijmout další parametry, které jsou vyplněny parametrem di:

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

[Možnosti životního cyklu a registrace](xref:fundamentals/dependency-injection#lifetime-and-registration-options) obsahují kompletní ukázku middlewaru s *oborem* služeb pro celou dobu života.

## <a name="middleware-extension-method"></a>Metoda rozšíření middlewaru

Následující rozšiřující metoda zpřístupňuje middleware prostřednictvím <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> :

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

Následující kód volá middleware z `Startup.Configure` :

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>Další zdroje

* [Možnosti životního cyklu a registrace](xref:fundamentals/dependency-injection#lifetime-and-registration-options) obsahují kompletní ukázku middlewaru s *rozsahem*, *přechodnými*a neplatnými službami typu *singleton* .
* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

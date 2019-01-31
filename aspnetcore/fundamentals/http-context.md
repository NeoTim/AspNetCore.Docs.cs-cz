---
title: Přístup k objektu HttpContext v ASP.NET Core
author: coderandhiker
description: Zjistěte, jak získat přístup k objektu HttpContext v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2018
uid: fundamentals/httpcontext
ms.openlocfilehash: babc637cdec8590ac14f7924c17e862e5b2f6a81
ms.sourcegitcommit: d22b3c23c45a076c4f394a70b1c8df2fbcdf656d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55428483"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Přístup k objektu HttpContext v ASP.NET Core

Přístup aplikací ASP.NET Core `HttpContext` prostřednictvím [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) rozhraní a jeho výchozí implementace [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor). Pouze je nutné použít `IHttpContextAccessor` když potřebujete přístup k `HttpContext` uvnitř služby.

::: moniker range=">= aspnetcore-2.0"

## <a name="use-httpcontext-from-razor-pages"></a>Použití objektu HttpContext ze stránky Razor

Stránky Razor [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) zpřístupňuje [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) vlastnost:

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

::: moniker-end

## <a name="use-httpcontext-from-a-razor-view"></a>Použití objektu HttpContext ze zobrazení Razor

Zobrazení syntaxe Razor vystavit `HttpContext` přímo prostřednictvím [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) vlastnost pro zobrazení. Následující příklad načte aktuální uživatelské jméno v intranetu aplikaci pomocí ověřování Windows:

```cshtml
@{
    var username = Context.User.Identity.Name;
}
```

## <a name="use-httpcontext-from-a-controller"></a>Použití objektu HttpContext z kontroleru

Vystavení řadiče [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) vlastnost:

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;
        // Do something with the PathBase.

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a>Použití objektu HttpContext z middlewaru

Při práci s vlastní middlewarových komponent `HttpContext` je předána do `Invoke` nebo `InvokeAsync` metody a je přístupný, pokud je nakonfigurovaná middleware:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        // Middleware initialization optionally using HttpContext
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Použití objektu HttpContext z vlastní komponenty

Pro jiné rozhraní framework a vlastních součástech, které vyžadují přístup k `HttpContext`, doporučuje se zaregistrovat závislost použití předdefinované [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru. Poskytuje kontejner vkládání závislostí `IHttpContextAccessor` na všechny třídy, které ji deklarovat v závislosti na jejich konstruktory.

::: moniker range=">= aspnetcore-2.1"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc();
     services.AddSingleton<IHttpContextAccessor, HttpContextAccessor>();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

V následujícím příkladu:

* `UserRepository` deklaruje své závislosti na `IHttpContextAccessor`.
* Při vkládání závislostí řeší řetězec závislostí a vytvoří instanci objektu zadán závislost `UserRepository`.

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

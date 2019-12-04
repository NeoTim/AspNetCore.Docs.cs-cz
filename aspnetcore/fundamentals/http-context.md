---
title: Přístup HttpContext v ASP.NET Core
author: coderandhiker
description: Přečtěte si, jak získat přístup k HttpContext v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: fundamentals/httpcontext
ms.openlocfilehash: 8a7ee180380c42ea745c91b8e6a18c1baa820220
ms.sourcegitcommit: 5974e3e66dab3398ecf2324fbb82a9c5636f70de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74778736"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Přístup HttpContext v ASP.NET Core

Aplikace ASP.NET Core Access `HttpContext` prostřednictvím rozhraní <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> a výchozího <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>implementace. Je potřeba použít `IHttpContextAccessor` jenom v případě, že potřebujete přístup k `HttpContext` v rámci služby.

## <a name="use-httpcontext-from-razor-pages"></a>Použít HttpContext z Razor Pages

Vlastnost <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> zpřístupňuje Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>:

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

## <a name="use-httpcontext-from-a-razor-view"></a>Použití HttpContext ze zobrazení Razor

Zobrazení Razor zpřístupňují `HttpContext` přímo prostřednictvím vlastnosti [RazorPage. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) v zobrazení. Následující příklad načte aktuální uživatelské jméno v intranetové aplikaci pomocí ověřování systému Windows:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Použití HttpContext z kontroleru

Řadiče zpřístupňují vlastnost [ControllerBase. HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) :

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a>Použití HttpContext z middlewaru

Při práci s vlastními součástmi middlewaru se `HttpContext` předává do metody `Invoke` nebo `InvokeAsync` a lze k nim přihlédnout, pokud je nakonfigurován middleware:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Použití HttpContext z vlastních komponent

Pro jiné architektury a vlastní součásti, které vyžadují přístup k `HttpContext`, je doporučený přístup k registraci závislosti pomocí integrovaného kontejneru [Injektáže v závislosti](xref:fundamentals/dependency-injection) . Kontejner pro vkládání závislostí poskytuje `IHttpContextAccessor` do jakékoliv třídy, které ji deklaruje jako závislost v jejich konstruktorech:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

V následujícím příkladu:

* `UserRepository` deklaruje svou závislost na `IHttpContextAccessor`.
* Závislost je poskytnuta, když injektáže závislosti vyřeší řetěz závislostí a vytvoří instanci `UserRepository`.

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

## <a name="httpcontext-access-from-a-background-thread"></a>HttpContext přístupu z vlákna na pozadí

`HttpContext` není bezpečná pro přístup z více vláken. Čtení nebo zápis vlastností `HttpContext` mimo zpracování požadavku může mít za následek <xref:System.NullReferenceException>.

> [!NOTE]
> Pokud vaše aplikace generuje občasné `NullReferenceException` chyby, Projděte si část kódu, která spouští zpracování na pozadí nebo pokračuje v zpracování po dokončení žádosti. Vyhledejte chyby, jako je například definování metody kontroleru jako `async void`.

Bezpečné provádění práce na pozadí s `HttpContext`mi daty:

* Během zpracování žádosti zkopírujte požadovaná data.
* Předejte zkopírovaná data do úlohy na pozadí.

Aby nedocházelo k nebezpečnému kódu, nikdy nepředávejte `HttpContext` do metody, která provádí práci na pozadí. Místo toho předejte požadovaná data. V následujícím příkladu je zavolána `SendEmailCore` pro zahájení odesílání e-mailů. `correlationId` je předán do `SendEmailCore`, nikoli `HttpContext`. Provádění kódu nečeká na dokončení `SendEmailCore`:

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}

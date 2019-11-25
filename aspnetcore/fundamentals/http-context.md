---
title: Přístup HttpContext v ASP.NET Core
author: coderandhiker
description: Přečtěte si, jak získat přístup k HttpContext v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: fundamentals/httpcontext
ms.openlocfilehash: 0bf40f9cd2554f5ba01ccc06001fa4f1940d51a5
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289053"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Přístup HttpContext v ASP.NET Core

Aplikace ASP.NET Core přistupují k `HttpContext` prostřednictvím rozhraní [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) a jeho výchozí [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor)implementace. Je potřeba použít `IHttpContextAccessor` jenom v případě, že potřebujete přístup k `HttpContext` v rámci služby.

::: moniker range=">= aspnetcore-2.0"

## <a name="use-httpcontext-from-razor-pages"></a>Použít HttpContext z Razor Pages

Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) zpřístupňuje vlastnost [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) :

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

## <a name="use-httpcontext-from-a-razor-view"></a>Použití HttpContext ze zobrazení Razor

Zobrazení Razor zpřístupňují `HttpContext` přímo prostřednictvím vlastnosti [RazorPage. Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) v zobrazení. Následující příklad načte aktuální uživatelské jméno v intranetové aplikaci pomocí ověřování systému Windows:

```cshtml
@{
    var username = Context.User.Identity.Name;
}
```

## <a name="use-httpcontext-from-a-controller"></a>Použití HttpContext z kontroleru

Řadiče zpřístupňují vlastnost [ControllerBase. HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) :

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

## <a name="use-httpcontext-from-middleware"></a>Použití HttpContext z middlewaru

Při práci s vlastními součástmi middlewaru se `HttpContext` předává do metody `Invoke` nebo `InvokeAsync` a lze k nim přihlédnout, pokud je nakonfigurován middleware:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        // Middleware initialization optionally using HttpContext
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Použití HttpContext z vlastních komponent

Pro jiné architektury a vlastní součásti, které vyžadují přístup k `HttpContext`, je doporučený přístup k registraci závislosti pomocí integrovaného kontejneru [Injektáže v závislosti](xref:fundamentals/dependency-injection) . Kontejner pro vkládání závislostí poskytuje `IHttpContextAccessor` do jakékoliv třídy, které ji deklaruje jako závislost v jejich konstruktorech.

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

`HttpContext` není bezpečný pro přístup z více vláken. Čtení nebo zápis vlastností `HttpContext` mimo zpracování požadavku může mít za následek `NullReferenceException`.

> [!NOTE]
> Použití `HttpContext` mimo zpracování požadavku často vede k `NullReferenceException`. Pokud vaše aplikace generuje občasné `NullReferenceException`s, Projděte si část kódu, která spouští zpracování na pozadí nebo pokračuje v zpracování po dokončení žádosti. Vyhledejte chyby, jako je například definování metody kontroleru jako `async void`.

Bezpečné provádění práce na pozadí s `HttpContext`mi daty:

* Během zpracování žádosti zkopírujte požadovaná data.
* Předejte zkopírovaná data do úlohy na pozadí.

Aby nedocházelo k nebezpečnému kódu, nikdy předejte `HttpContext` do metody, která provádí práci na pozadí – předejte data, která potřebujete.

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        // Starts sending an email, but doesn't wait for it to complete
        _ = SendEmailCore(correlationId);
        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        // send the email
    }
}

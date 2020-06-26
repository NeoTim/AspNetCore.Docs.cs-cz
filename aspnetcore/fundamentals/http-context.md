---
title: Přístup HttpContext v ASP.NET Core
author: coderandhiker
description: Přečtěte si, jak získat přístup k HttpContext v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/httpcontext
ms.openlocfilehash: d4512c9fa136e518fa0230c0cf9c607519eed6d8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399449"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Přístup HttpContext v ASP.NET Core

Aplikace ASP.NET Core přístup `HttpContext` prostřednictvím <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> rozhraní a jeho výchozí implementace <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> . Je nutné použít pouze `IHttpContextAccessor` v případě, že potřebujete přístup ke `HttpContext` službě v rámci služby.

## <a name="use-httpcontext-from-razor-pages"></a>Použití HttpContext ze Razor stránek

RazorStránky <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> zpřístupňují <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> vlastnost:

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

## <a name="use-httpcontext-from-a-razor-view"></a>Použití HttpContext ze Razor zobrazení

Razorzobrazení zpřístupňují `HttpContext` přímo přes vlastnost [RazorPage. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) v zobrazení. Následující příklad načte aktuální uživatelské jméno v intranetové aplikaci pomocí ověřování systému Windows:

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

Při práci s vlastními součástmi middlewaru `HttpContext` se předává do `Invoke` `InvokeAsync` metody nebo a je možné k nim přihlédnout, pokud je nakonfigurován middleware:

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

Pro jiné architektury a vlastní součásti, které vyžadují přístup k portálu `HttpContext` , je doporučený přístup k registraci závislosti pomocí integrovaného kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) . Kontejner pro vkládání závislostí poskytuje `IHttpContextAccessor` všechny třídy, které ji deklaruje jako závislost ve svých konstruktorech:

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

* `UserRepository`deklaruje jeho závislost na `IHttpContextAccessor` .
* Závislost je poskytnuta, když injektáže závislosti vyřeší řetěz závislostí a vytvoří instanci `UserRepository` .

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

`HttpContext`není bezpečná pro přístup z více vláken. Čtení nebo zápis vlastností `HttpContext` mimo zpracování požadavku může mít za následek <xref:System.NullReferenceException> .

> [!NOTE]
> Pokud vaše aplikace generuje občasné `NullReferenceException` chyby, Projděte si část kódu, která spouští zpracování na pozadí nebo pokračuje v zpracování po dokončení žádosti. Vyhledejte chyby, jako je například definování metody kontroleru `async void` .

Bezpečné provádění práce na pozadí s `HttpContext` daty:

* Během zpracování žádosti zkopírujte požadovaná data.
* Předejte zkopírovaná data do úlohy na pozadí.

Aby nedocházelo k nebezpečnému kódu, nikdy nepředávejte `HttpContext` do metody, která provádí práci na pozadí. Místo toho předejte požadovaná data. V následujícím příkladu `SendEmailCore` je volána pro zahájení odesílání e-mailů. `correlationId`Je předán do `SendEmailCore` , nikoli `HttpContext` . Provádění kódu nečeká `SendEmailCore` na dokončení:

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
```

## <a name="blazor-and-shared-state"></a>Blazora sdílený stav

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]

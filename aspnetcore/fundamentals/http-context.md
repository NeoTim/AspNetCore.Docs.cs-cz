---
title: Přístup k httpcontext v ASP.NET jádru
author: coderandhiker
description: Zjistěte, jak získat přístup k httpcontext v ASP.NET core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: fundamentals/httpcontext
ms.openlocfilehash: 8a7ee180380c42ea745c91b8e6a18c1baa820220
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658744"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Přístup k httpcontext v ASP.NET jádru

ASP.NET přístup `HttpContext` k <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> aplikacím Core <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>prostřednictvím rozhraní a jeho výchozí implementaci . Je nutné použít pouze `IHttpContextAccessor` v případě, `HttpContext` že potřebujete přístup k vnitřní službě.

## <a name="use-httpcontext-from-razor-pages"></a>Použití httpcontext z holicích stránek

Razor Stránky <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> zveřejňuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> vlastnost:

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

## <a name="use-httpcontext-from-a-razor-view"></a>Použití kontextu httpcontext ze zobrazení Razor

Razor zobrazení `HttpContext` vystavit přímo prostřednictvím [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) vlastnost v zobrazení. Následující příklad načte aktuální uživatelské jméno v intranetové aplikaci pomocí ověřování systému Windows:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Použití kontextu HttpContext z řadiče

Řadiče zveřejňují vlastnost [ControllerBase.HttpContext:](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext)

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

## <a name="use-httpcontext-from-middleware"></a>Použití httpcontext z middlewaru

Při práci s vlastními `HttpContext` komponentami `Invoke` middlewaru je předán do metody nebo `InvokeAsync` a je přístupný při konfiguraci middlewaru:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Použití kontextu HttpContext z vlastních součástí

Pro jiné rozhraní a vlastní součásti, které vyžadují přístup k `HttpContext`, doporučený mandatář je zaregistrovat závislost pomocí integrovaného kontejneru vkládání [závislostí.](xref:fundamentals/dependency-injection) Kontejner vkládání závislostí dodává `IHttpContextAccessor` všechny třídy, které deklarují jako závislost v jejich konstruktory:

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

* `UserRepository`deklaruje `IHttpContextAccessor`svou závislost na .
* Závislost je zadána při vkládání závislostí řeší řetěz závislostí `UserRepository`a vytvoří instanci .

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

## <a name="httpcontext-access-from-a-background-thread"></a>HttpContext přístup z vlákna na pozadí

`HttpContext`není bezpečný pro přístup z více vláken. Čtení nebo zápis `HttpContext` vlastnosti mimo zpracování požadavku <xref:System.NullReferenceException>může mít za následek .

> [!NOTE]
> Pokud vaše aplikace `NullReferenceException` generuje sporadické chyby, zkontrolujte části kódu, které spustí zpracování na pozadí nebo které pokračují ve zpracování po dokončení požadavku. Vyhledejte chyby, jako je například definování metody kontroleru jako `async void`.

Bezpečné provedení práce `HttpContext` na pozadí s daty:

* Zkopírujte požadovaná data během zpracování požadavku.
* Předajte zkopírovaná data úlohě na pozadí.

Chcete-li se vyhnout `HttpContext` nebezpečný kód, nikdy předat do metody, která provádí práci na pozadí. Místo toho předaj požadovaná data. V následujícím příkladu `SendEmailCore` se volá, aby se začal oslat e-mail. Je `correlationId` předána `SendEmailCore`do `HttpContext`, nikoli . Spuštění kódu nečeká `SendEmailCore` na dokončení:

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

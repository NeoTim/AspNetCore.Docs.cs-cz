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
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="e4b73-103">Přístup HttpContext v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4b73-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="e4b73-104">Aplikace ASP.NET Core Access `HttpContext` prostřednictvím rozhraní <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> a výchozího <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>implementace.</span><span class="sxs-lookup"><span data-stu-id="e4b73-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="e4b73-105">Je potřeba použít `IHttpContextAccessor` jenom v případě, že potřebujete přístup k `HttpContext` v rámci služby.</span><span class="sxs-lookup"><span data-stu-id="e4b73-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="e4b73-106">Použít HttpContext z Razor Pages</span><span class="sxs-lookup"><span data-stu-id="e4b73-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="e4b73-107">Vlastnost <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> zpřístupňuje Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>:</span><span class="sxs-lookup"><span data-stu-id="e4b73-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="e4b73-108">Použití HttpContext ze zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="e4b73-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="e4b73-109">Zobrazení Razor zpřístupňují `HttpContext` přímo prostřednictvím vlastnosti [RazorPage. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e4b73-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="e4b73-110">Následující příklad načte aktuální uživatelské jméno v intranetové aplikaci pomocí ověřování systému Windows:</span><span class="sxs-lookup"><span data-stu-id="e4b73-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="e4b73-111">Použití HttpContext z kontroleru</span><span class="sxs-lookup"><span data-stu-id="e4b73-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="e4b73-112">Řadiče zpřístupňují vlastnost [ControllerBase. HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) :</span><span class="sxs-lookup"><span data-stu-id="e4b73-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="e4b73-113">Použití HttpContext z middlewaru</span><span class="sxs-lookup"><span data-stu-id="e4b73-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="e4b73-114">Při práci s vlastními součástmi middlewaru se `HttpContext` předává do metody `Invoke` nebo `InvokeAsync` a lze k nim přihlédnout, pokud je nakonfigurován middleware:</span><span class="sxs-lookup"><span data-stu-id="e4b73-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="e4b73-115">Použití HttpContext z vlastních komponent</span><span class="sxs-lookup"><span data-stu-id="e4b73-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="e4b73-116">Pro jiné architektury a vlastní součásti, které vyžadují přístup k `HttpContext`, je doporučený přístup k registraci závislosti pomocí integrovaného kontejneru [Injektáže v závislosti](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="e4b73-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="e4b73-117">Kontejner pro vkládání závislostí poskytuje `IHttpContextAccessor` do jakékoliv třídy, které ji deklaruje jako závislost v jejich konstruktorech:</span><span class="sxs-lookup"><span data-stu-id="e4b73-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="e4b73-118">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e4b73-118">In the following example:</span></span>

* <span data-ttu-id="e4b73-119">`UserRepository` deklaruje svou závislost na `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="e4b73-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="e4b73-120">Závislost je poskytnuta, když injektáže závislosti vyřeší řetěz závislostí a vytvoří instanci `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="e4b73-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="e4b73-121">HttpContext přístupu z vlákna na pozadí</span><span class="sxs-lookup"><span data-stu-id="e4b73-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="e4b73-122">`HttpContext` není bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="e4b73-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="e4b73-123">Čtení nebo zápis vlastností `HttpContext` mimo zpracování požadavku může mít za následek <xref:System.NullReferenceException>.</span><span class="sxs-lookup"><span data-stu-id="e4b73-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="e4b73-124">Pokud vaše aplikace generuje občasné `NullReferenceException` chyby, Projděte si část kódu, která spouští zpracování na pozadí nebo pokračuje v zpracování po dokončení žádosti.</span><span class="sxs-lookup"><span data-stu-id="e4b73-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="e4b73-125">Vyhledejte chyby, jako je například definování metody kontroleru jako `async void`.</span><span class="sxs-lookup"><span data-stu-id="e4b73-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="e4b73-126">Bezpečné provádění práce na pozadí s `HttpContext`mi daty:</span><span class="sxs-lookup"><span data-stu-id="e4b73-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="e4b73-127">Během zpracování žádosti zkopírujte požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="e4b73-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="e4b73-128">Předejte zkopírovaná data do úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="e4b73-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="e4b73-129">Aby nedocházelo k nebezpečnému kódu, nikdy nepředávejte `HttpContext` do metody, která provádí práci na pozadí.</span><span class="sxs-lookup"><span data-stu-id="e4b73-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="e4b73-130">Místo toho předejte požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="e4b73-130">Pass the required data instead.</span></span> <span data-ttu-id="e4b73-131">V následujícím příkladu je zavolána `SendEmailCore` pro zahájení odesílání e-mailů.</span><span class="sxs-lookup"><span data-stu-id="e4b73-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="e4b73-132">`correlationId` je předán do `SendEmailCore`, nikoli `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="e4b73-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="e4b73-133">Provádění kódu nečeká na dokončení `SendEmailCore`:</span><span class="sxs-lookup"><span data-stu-id="e4b73-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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

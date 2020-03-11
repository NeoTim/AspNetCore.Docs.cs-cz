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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658744"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="d893b-103">Přístup HttpContext v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d893b-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="d893b-104">Aplikace ASP.NET Core Access `HttpContext` prostřednictvím rozhraní <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> a výchozího <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>implementace.</span><span class="sxs-lookup"><span data-stu-id="d893b-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="d893b-105">Je potřeba použít `IHttpContextAccessor` jenom v případě, že potřebujete přístup k `HttpContext` v rámci služby.</span><span class="sxs-lookup"><span data-stu-id="d893b-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="d893b-106">Použít HttpContext z Razor Pages</span><span class="sxs-lookup"><span data-stu-id="d893b-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="d893b-107">Vlastnost <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> zpřístupňuje Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>:</span><span class="sxs-lookup"><span data-stu-id="d893b-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="d893b-108">Použití HttpContext ze zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="d893b-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="d893b-109">Zobrazení Razor zpřístupňují `HttpContext` přímo prostřednictvím vlastnosti [RazorPage. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="d893b-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="d893b-110">Následující příklad načte aktuální uživatelské jméno v intranetové aplikaci pomocí ověřování systému Windows:</span><span class="sxs-lookup"><span data-stu-id="d893b-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="d893b-111">Použití HttpContext z kontroleru</span><span class="sxs-lookup"><span data-stu-id="d893b-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="d893b-112">Řadiče zpřístupňují vlastnost [ControllerBase. HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) :</span><span class="sxs-lookup"><span data-stu-id="d893b-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="d893b-113">Použití HttpContext z middlewaru</span><span class="sxs-lookup"><span data-stu-id="d893b-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="d893b-114">Při práci s vlastními součástmi middlewaru se `HttpContext` předává do metody `Invoke` nebo `InvokeAsync` a lze k nim přihlédnout, pokud je nakonfigurován middleware:</span><span class="sxs-lookup"><span data-stu-id="d893b-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="d893b-115">Použití HttpContext z vlastních komponent</span><span class="sxs-lookup"><span data-stu-id="d893b-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="d893b-116">Pro jiné architektury a vlastní součásti, které vyžadují přístup k `HttpContext`, je doporučený přístup k registraci závislosti pomocí integrovaného kontejneru [Injektáže v závislosti](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="d893b-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d893b-117">Kontejner pro vkládání závislostí poskytuje `IHttpContextAccessor` do jakékoliv třídy, které ji deklaruje jako závislost v jejich konstruktorech:</span><span class="sxs-lookup"><span data-stu-id="d893b-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="d893b-118">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="d893b-118">In the following example:</span></span>

* <span data-ttu-id="d893b-119">`UserRepository` deklaruje svou závislost na `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="d893b-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="d893b-120">Závislost je poskytnuta, když injektáže závislosti vyřeší řetěz závislostí a vytvoří instanci `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="d893b-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="d893b-121">HttpContext přístupu z vlákna na pozadí</span><span class="sxs-lookup"><span data-stu-id="d893b-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="d893b-122">`HttpContext` není bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="d893b-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="d893b-123">Čtení nebo zápis vlastností `HttpContext` mimo zpracování požadavku může mít za následek <xref:System.NullReferenceException>.</span><span class="sxs-lookup"><span data-stu-id="d893b-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="d893b-124">Pokud vaše aplikace generuje občasné `NullReferenceException` chyby, Projděte si část kódu, která spouští zpracování na pozadí nebo pokračuje v zpracování po dokončení žádosti.</span><span class="sxs-lookup"><span data-stu-id="d893b-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="d893b-125">Vyhledejte chyby, jako je například definování metody kontroleru jako `async void`.</span><span class="sxs-lookup"><span data-stu-id="d893b-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="d893b-126">Bezpečné provádění práce na pozadí s `HttpContext`mi daty:</span><span class="sxs-lookup"><span data-stu-id="d893b-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="d893b-127">Během zpracování žádosti zkopírujte požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="d893b-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="d893b-128">Předejte zkopírovaná data do úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d893b-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="d893b-129">Aby nedocházelo k nebezpečnému kódu, nikdy nepředávejte `HttpContext` do metody, která provádí práci na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d893b-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="d893b-130">Místo toho předejte požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="d893b-130">Pass the required data instead.</span></span> <span data-ttu-id="d893b-131">V následujícím příkladu je zavolána `SendEmailCore` pro zahájení odesílání e-mailů.</span><span class="sxs-lookup"><span data-stu-id="d893b-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="d893b-132">`correlationId` je předán do `SendEmailCore`, nikoli `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="d893b-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="d893b-133">Provádění kódu nečeká na dokončení `SendEmailCore`:</span><span class="sxs-lookup"><span data-stu-id="d893b-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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

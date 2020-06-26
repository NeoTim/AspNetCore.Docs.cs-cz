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
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="b9d6e-103">Přístup HttpContext v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9d6e-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="b9d6e-104">Aplikace ASP.NET Core přístup `HttpContext` prostřednictvím <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> rozhraní a jeho výchozí implementace <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> .</span><span class="sxs-lookup"><span data-stu-id="b9d6e-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="b9d6e-105">Je nutné použít pouze `IHttpContextAccessor` v případě, že potřebujete přístup ke `HttpContext` službě v rámci služby.</span><span class="sxs-lookup"><span data-stu-id="b9d6e-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="b9d6e-106">Použití HttpContext ze Razor stránek</span><span class="sxs-lookup"><span data-stu-id="b9d6e-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="b9d6e-107">RazorStránky <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> zpřístupňují <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> vlastnost:</span><span class="sxs-lookup"><span data-stu-id="b9d6e-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="b9d6e-108">Použití HttpContext ze Razor zobrazení</span><span class="sxs-lookup"><span data-stu-id="b9d6e-108">Use HttpContext from a Razor view</span></span>

Razor<span data-ttu-id="b9d6e-109">zobrazení zpřístupňují `HttpContext` přímo přes vlastnost [RazorPage. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="b9d6e-109"> views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="b9d6e-110">Následující příklad načte aktuální uživatelské jméno v intranetové aplikaci pomocí ověřování systému Windows:</span><span class="sxs-lookup"><span data-stu-id="b9d6e-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="b9d6e-111">Použití HttpContext z kontroleru</span><span class="sxs-lookup"><span data-stu-id="b9d6e-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="b9d6e-112">Řadiče zpřístupňují vlastnost [ControllerBase. HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) :</span><span class="sxs-lookup"><span data-stu-id="b9d6e-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="b9d6e-113">Použití HttpContext z middlewaru</span><span class="sxs-lookup"><span data-stu-id="b9d6e-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="b9d6e-114">Při práci s vlastními součástmi middlewaru `HttpContext` se předává do `Invoke` `InvokeAsync` metody nebo a je možné k nim přihlédnout, pokud je nakonfigurován middleware:</span><span class="sxs-lookup"><span data-stu-id="b9d6e-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="b9d6e-115">Použití HttpContext z vlastních komponent</span><span class="sxs-lookup"><span data-stu-id="b9d6e-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="b9d6e-116">Pro jiné architektury a vlastní součásti, které vyžadují přístup k portálu `HttpContext` , je doporučený přístup k registraci závislosti pomocí integrovaného kontejneru pro [vkládání závislostí](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="b9d6e-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b9d6e-117">Kontejner pro vkládání závislostí poskytuje `IHttpContextAccessor` všechny třídy, které ji deklaruje jako závislost ve svých konstruktorech:</span><span class="sxs-lookup"><span data-stu-id="b9d6e-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="b9d6e-118">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="b9d6e-118">In the following example:</span></span>

* <span data-ttu-id="b9d6e-119">`UserRepository`deklaruje jeho závislost na `IHttpContextAccessor` .</span><span class="sxs-lookup"><span data-stu-id="b9d6e-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="b9d6e-120">Závislost je poskytnuta, když injektáže závislosti vyřeší řetěz závislostí a vytvoří instanci `UserRepository` .</span><span class="sxs-lookup"><span data-stu-id="b9d6e-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="b9d6e-121">HttpContext přístupu z vlákna na pozadí</span><span class="sxs-lookup"><span data-stu-id="b9d6e-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="b9d6e-122">`HttpContext`není bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="b9d6e-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="b9d6e-123">Čtení nebo zápis vlastností `HttpContext` mimo zpracování požadavku může mít za následek <xref:System.NullReferenceException> .</span><span class="sxs-lookup"><span data-stu-id="b9d6e-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="b9d6e-124">Pokud vaše aplikace generuje občasné `NullReferenceException` chyby, Projděte si část kódu, která spouští zpracování na pozadí nebo pokračuje v zpracování po dokončení žádosti.</span><span class="sxs-lookup"><span data-stu-id="b9d6e-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="b9d6e-125">Vyhledejte chyby, jako je například definování metody kontroleru `async void` .</span><span class="sxs-lookup"><span data-stu-id="b9d6e-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="b9d6e-126">Bezpečné provádění práce na pozadí s `HttpContext` daty:</span><span class="sxs-lookup"><span data-stu-id="b9d6e-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="b9d6e-127">Během zpracování žádosti zkopírujte požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="b9d6e-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="b9d6e-128">Předejte zkopírovaná data do úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="b9d6e-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="b9d6e-129">Aby nedocházelo k nebezpečnému kódu, nikdy nepředávejte `HttpContext` do metody, která provádí práci na pozadí.</span><span class="sxs-lookup"><span data-stu-id="b9d6e-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="b9d6e-130">Místo toho předejte požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="b9d6e-130">Pass the required data instead.</span></span> <span data-ttu-id="b9d6e-131">V následujícím příkladu `SendEmailCore` je volána pro zahájení odesílání e-mailů.</span><span class="sxs-lookup"><span data-stu-id="b9d6e-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="b9d6e-132">`correlationId`Je předán do `SendEmailCore` , nikoli `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="b9d6e-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="b9d6e-133">Provádění kódu nečeká `SendEmailCore` na dokončení:</span><span class="sxs-lookup"><span data-stu-id="b9d6e-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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

## <a name="blazor-and-shared-state"></a>Blazor<span data-ttu-id="b9d6e-134">a sdílený stav</span><span class="sxs-lookup"><span data-stu-id="b9d6e-134"> and shared state</span></span>

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]

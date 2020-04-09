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
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="181c5-103">Přístup k httpcontext v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="181c5-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="181c5-104">ASP.NET přístup `HttpContext` k <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> aplikacím Core <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>prostřednictvím rozhraní a jeho výchozí implementaci .</span><span class="sxs-lookup"><span data-stu-id="181c5-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="181c5-105">Je nutné použít pouze `IHttpContextAccessor` v případě, `HttpContext` že potřebujete přístup k vnitřní službě.</span><span class="sxs-lookup"><span data-stu-id="181c5-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="181c5-106">Použití httpcontext z holicích stránek</span><span class="sxs-lookup"><span data-stu-id="181c5-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="181c5-107">Razor Stránky <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> zveřejňuje <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> vlastnost:</span><span class="sxs-lookup"><span data-stu-id="181c5-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="181c5-108">Použití kontextu httpcontext ze zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="181c5-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="181c5-109">Razor zobrazení `HttpContext` vystavit přímo prostřednictvím [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) vlastnost v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="181c5-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="181c5-110">Následující příklad načte aktuální uživatelské jméno v intranetové aplikaci pomocí ověřování systému Windows:</span><span class="sxs-lookup"><span data-stu-id="181c5-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="181c5-111">Použití kontextu HttpContext z řadiče</span><span class="sxs-lookup"><span data-stu-id="181c5-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="181c5-112">Řadiče zveřejňují vlastnost [ControllerBase.HttpContext:](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext)</span><span class="sxs-lookup"><span data-stu-id="181c5-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="181c5-113">Použití httpcontext z middlewaru</span><span class="sxs-lookup"><span data-stu-id="181c5-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="181c5-114">Při práci s vlastními `HttpContext` komponentami `Invoke` middlewaru je předán do metody nebo `InvokeAsync` a je přístupný při konfiguraci middlewaru:</span><span class="sxs-lookup"><span data-stu-id="181c5-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="181c5-115">Použití kontextu HttpContext z vlastních součástí</span><span class="sxs-lookup"><span data-stu-id="181c5-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="181c5-116">Pro jiné rozhraní a vlastní součásti, které vyžadují přístup k `HttpContext`, doporučený mandatář je zaregistrovat závislost pomocí integrovaného kontejneru vkládání [závislostí.](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="181c5-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="181c5-117">Kontejner vkládání závislostí dodává `IHttpContextAccessor` všechny třídy, které deklarují jako závislost v jejich konstruktory:</span><span class="sxs-lookup"><span data-stu-id="181c5-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="181c5-118">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="181c5-118">In the following example:</span></span>

* <span data-ttu-id="181c5-119">`UserRepository`deklaruje `IHttpContextAccessor`svou závislost na .</span><span class="sxs-lookup"><span data-stu-id="181c5-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="181c5-120">Závislost je zadána při vkládání závislostí řeší řetěz závislostí `UserRepository`a vytvoří instanci .</span><span class="sxs-lookup"><span data-stu-id="181c5-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="181c5-121">HttpContext přístup z vlákna na pozadí</span><span class="sxs-lookup"><span data-stu-id="181c5-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="181c5-122">`HttpContext`není bezpečný pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="181c5-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="181c5-123">Čtení nebo zápis `HttpContext` vlastnosti mimo zpracování požadavku <xref:System.NullReferenceException>může mít za následek .</span><span class="sxs-lookup"><span data-stu-id="181c5-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="181c5-124">Pokud vaše aplikace `NullReferenceException` generuje sporadické chyby, zkontrolujte části kódu, které spustí zpracování na pozadí nebo které pokračují ve zpracování po dokončení požadavku.</span><span class="sxs-lookup"><span data-stu-id="181c5-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="181c5-125">Vyhledejte chyby, jako je například definování metody kontroleru jako `async void`.</span><span class="sxs-lookup"><span data-stu-id="181c5-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="181c5-126">Bezpečné provedení práce `HttpContext` na pozadí s daty:</span><span class="sxs-lookup"><span data-stu-id="181c5-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="181c5-127">Zkopírujte požadovaná data během zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="181c5-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="181c5-128">Předajte zkopírovaná data úlohě na pozadí.</span><span class="sxs-lookup"><span data-stu-id="181c5-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="181c5-129">Chcete-li se vyhnout `HttpContext` nebezpečný kód, nikdy předat do metody, která provádí práci na pozadí.</span><span class="sxs-lookup"><span data-stu-id="181c5-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="181c5-130">Místo toho předaj požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="181c5-130">Pass the required data instead.</span></span> <span data-ttu-id="181c5-131">V následujícím příkladu `SendEmailCore` se volá, aby se začal oslat e-mail.</span><span class="sxs-lookup"><span data-stu-id="181c5-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="181c5-132">Je `correlationId` předána `SendEmailCore`do `HttpContext`, nikoli .</span><span class="sxs-lookup"><span data-stu-id="181c5-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="181c5-133">Spuštění kódu nečeká `SendEmailCore` na dokončení:</span><span class="sxs-lookup"><span data-stu-id="181c5-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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

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
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="ad4fd-103">Přístup HttpContext v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ad4fd-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="ad4fd-104">Aplikace ASP.NET Core přistupují k `HttpContext` prostřednictvím rozhraní [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) a jeho výchozí [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor)implementace.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-104">ASP.NET Core apps access the `HttpContext` through the [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) interface and its default implementation [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor).</span></span> <span data-ttu-id="ad4fd-105">Je potřeba použít `IHttpContextAccessor` jenom v případě, že potřebujete přístup k `HttpContext` v rámci služby.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

::: moniker range=">= aspnetcore-2.0"

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="ad4fd-106">Použít HttpContext z Razor Pages</span><span class="sxs-lookup"><span data-stu-id="ad4fd-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="ad4fd-107">Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) zpřístupňuje vlastnost [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) :</span><span class="sxs-lookup"><span data-stu-id="ad4fd-107">The Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) exposes the [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="ad4fd-108">Použití HttpContext ze zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="ad4fd-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="ad4fd-109">Zobrazení Razor zpřístupňují `HttpContext` přímo prostřednictvím vlastnosti [RazorPage. Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) property on the view.</span></span> <span data-ttu-id="ad4fd-110">Následující příklad načte aktuální uživatelské jméno v intranetové aplikaci pomocí ověřování systému Windows:</span><span class="sxs-lookup"><span data-stu-id="ad4fd-110">The following example retrieves the current username in an Intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="ad4fd-111">Použití HttpContext z kontroleru</span><span class="sxs-lookup"><span data-stu-id="ad4fd-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="ad4fd-112">Řadiče zpřístupňují vlastnost [ControllerBase. HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) :</span><span class="sxs-lookup"><span data-stu-id="ad4fd-112">Controllers expose the [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="ad4fd-113">Použití HttpContext z middlewaru</span><span class="sxs-lookup"><span data-stu-id="ad4fd-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="ad4fd-114">Při práci s vlastními součástmi middlewaru se `HttpContext` předává do metody `Invoke` nebo `InvokeAsync` a lze k nim přihlédnout, pokud je nakonfigurován middleware:</span><span class="sxs-lookup"><span data-stu-id="ad4fd-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        // Middleware initialization optionally using HttpContext
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="ad4fd-115">Použití HttpContext z vlastních komponent</span><span class="sxs-lookup"><span data-stu-id="ad4fd-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="ad4fd-116">Pro jiné architektury a vlastní součásti, které vyžadují přístup k `HttpContext`, je doporučený přístup k registraci závislosti pomocí integrovaného kontejneru [Injektáže v závislosti](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="ad4fd-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ad4fd-117">Kontejner pro vkládání závislostí poskytuje `IHttpContextAccessor` do jakékoliv třídy, které ji deklaruje jako závislost v jejich konstruktorech.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors.</span></span>

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

<span data-ttu-id="ad4fd-118">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ad4fd-118">In the following example:</span></span>

* <span data-ttu-id="ad4fd-119">`UserRepository` deklaruje svou závislost na `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="ad4fd-120">Závislost je poskytnuta, když injektáže závislosti vyřeší řetěz závislostí a vytvoří instanci `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="ad4fd-121">HttpContext přístupu z vlákna na pozadí</span><span class="sxs-lookup"><span data-stu-id="ad4fd-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="ad4fd-122">`HttpContext` není bezpečný pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-122">`HttpContext` is not thread-safe.</span></span> <span data-ttu-id="ad4fd-123">Čtení nebo zápis vlastností `HttpContext` mimo zpracování požadavku může mít za následek `NullReferenceException`.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a `NullReferenceException`.</span></span>

> [!NOTE]
> <span data-ttu-id="ad4fd-124">Použití `HttpContext` mimo zpracování požadavku často vede k `NullReferenceException`.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-124">Using `HttpContext` outside of processing a request often results in a `NullReferenceException`.</span></span> <span data-ttu-id="ad4fd-125">Pokud vaše aplikace generuje občasné `NullReferenceException`s, Projděte si část kódu, která spouští zpracování na pozadí nebo pokračuje v zpracování po dokončení žádosti.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-125">If your app generates sporadic `NullReferenceException`s , review parts of the code that start background processing, or that continue processing after a request completes.</span></span> <span data-ttu-id="ad4fd-126">Vyhledejte chyby, jako je například definování metody kontroleru jako `async void`.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-126">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="ad4fd-127">Bezpečné provádění práce na pozadí s `HttpContext`mi daty:</span><span class="sxs-lookup"><span data-stu-id="ad4fd-127">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="ad4fd-128">Během zpracování žádosti zkopírujte požadovaná data.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-128">Copy the required data during request processing.</span></span>
* <span data-ttu-id="ad4fd-129">Předejte zkopírovaná data do úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-129">Pass the copied data to a background task.</span></span>

<span data-ttu-id="ad4fd-130">Aby nedocházelo k nebezpečnému kódu, nikdy předejte `HttpContext` do metody, která provádí práci na pozadí – předejte data, která potřebujete.</span><span class="sxs-lookup"><span data-stu-id="ad4fd-130">To avoid unsafe code, never pass the `HttpContext` into a method that does background work - pass the data you need instead.</span></span>

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

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
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="cd409-103">Přístup k objektu HttpContext v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd409-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="cd409-104">Přístup aplikací ASP.NET Core `HttpContext` prostřednictvím [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) rozhraní a jeho výchozí implementace [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor).</span><span class="sxs-lookup"><span data-stu-id="cd409-104">ASP.NET Core apps access the `HttpContext` through the [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) interface and its default implementation [HttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.httpcontextaccessor).</span></span> <span data-ttu-id="cd409-105">Pouze je nutné použít `IHttpContextAccessor` když potřebujete přístup k `HttpContext` uvnitř služby.</span><span class="sxs-lookup"><span data-stu-id="cd409-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

::: moniker range=">= aspnetcore-2.0"

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="cd409-106">Použití objektu HttpContext ze stránky Razor</span><span class="sxs-lookup"><span data-stu-id="cd409-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="cd409-107">Stránky Razor [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) zpřístupňuje [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) vlastnost:</span><span class="sxs-lookup"><span data-stu-id="cd409-107">The Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) exposes the [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext) property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="cd409-108">Použití objektu HttpContext ze zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="cd409-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="cd409-109">Zobrazení syntaxe Razor vystavit `HttpContext` přímo prostřednictvím [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) vlastnost pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="cd409-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.context#Microsoft_AspNetCore_Mvc_Razor_RazorPage_Context) property on the view.</span></span> <span data-ttu-id="cd409-110">Následující příklad načte aktuální uživatelské jméno v intranetu aplikaci pomocí ověřování Windows:</span><span class="sxs-lookup"><span data-stu-id="cd409-110">The following example retrieves the current username in an Intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="cd409-111">Použití objektu HttpContext z kontroleru</span><span class="sxs-lookup"><span data-stu-id="cd409-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="cd409-112">Vystavení řadiče [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) vlastnost:</span><span class="sxs-lookup"><span data-stu-id="cd409-112">Controllers expose the [ControllerBase.HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.httpcontext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="cd409-113">Použití objektu HttpContext z middlewaru</span><span class="sxs-lookup"><span data-stu-id="cd409-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="cd409-114">Při práci s vlastní middlewarových komponent `HttpContext` je předána do `Invoke` nebo `InvokeAsync` metody a je přístupný, pokud je nakonfigurovaná middleware:</span><span class="sxs-lookup"><span data-stu-id="cd409-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        // Middleware initialization optionally using HttpContext
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="cd409-115">Použití objektu HttpContext z vlastní komponenty</span><span class="sxs-lookup"><span data-stu-id="cd409-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="cd409-116">Pro jiné rozhraní framework a vlastních součástech, které vyžadují přístup k `HttpContext`, doporučuje se zaregistrovat závislost použití předdefinované [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru.</span><span class="sxs-lookup"><span data-stu-id="cd409-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="cd409-117">Poskytuje kontejner vkládání závislostí `IHttpContextAccessor` na všechny třídy, které ji deklarovat v závislosti na jejich konstruktory.</span><span class="sxs-lookup"><span data-stu-id="cd409-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors.</span></span>

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

<span data-ttu-id="cd409-118">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="cd409-118">In the following example:</span></span>

* <span data-ttu-id="cd409-119">`UserRepository` deklaruje své závislosti na `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="cd409-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="cd409-120">Při vkládání závislostí řeší řetězec závislostí a vytvoří instanci objektu zadán závislost `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="cd409-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

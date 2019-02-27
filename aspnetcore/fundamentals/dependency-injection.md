---
title: Vkládání závislostí v ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak se používá.
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: 5e1522e0819d989a7029c2928c1c33624c1774c7
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899356"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="fe384-103">Vkládání závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fe384-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="fe384-104">Podle [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fe384-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fe384-105">ASP.NET Core podporuje návrhový vzor vkládání závislostí (Dependency Injection, DI), což je technika používaná pro dosažení [inverze řízení (Inversion of Control, IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="fe384-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="fe384-106">Další informace specifické pro vkládání závislostí do kontrolerů MVC najdete v tématu <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="fe384-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="fe384-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fe384-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="fe384-108">Přehled vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="fe384-108">Overview of dependency injection</span></span>

<span data-ttu-id="fe384-109">*Závislost* je libovolný objekt, který je vyžadován jiným objektem.</span><span class="sxs-lookup"><span data-stu-id="fe384-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="fe384-110">Prohlédněte si následující třídu `MyDependency` s metodou `WriteMessage`, na které závisí jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fe384-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="fe384-111">Pro zpřístupnění metody `WriteMessage` v jiné třídě je možné vytvořit instanci třídy `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fe384-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="fe384-112">Třída `MyDependency` je závislost třídy `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="fe384-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="fe384-113">Pro zpřístupnění metody `WriteMessage` v jiné třídě je možné vytvořit instanci třídy `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fe384-113">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="fe384-114">Třída `MyDependency` je závislostí třídy `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="fe384-114">The `MyDependency` class is a dependency of the `HomeController` class:</span></span>

```csharp
public class HomeController : Controller
{
    MyDependency _dependency = new MyDependency();

    public async Task<IActionResult> Index()
    {
        await _dependency.WriteMessage(
            "HomeController.Index created this message.");

        return View();
    }
}
```

::: moniker-end

<span data-ttu-id="fe384-115">Třída vytváří instanci třídy `MyDependency`, na které přímo závisí.</span><span class="sxs-lookup"><span data-stu-id="fe384-115">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="fe384-116">Přímé závislosti v kódu (jako ta v předchozím příkladu) jsou problematické a měli byste se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="fe384-116">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="fe384-117">Chcete-li nahradit `MyDependency` jinou implementací, musí být modifikována třída, která na ní závisí.</span><span class="sxs-lookup"><span data-stu-id="fe384-117">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="fe384-118">Pokud má třída `MyDependency` sama závislosti, musí být taktéž nakonfigurovány v dané třídě.</span><span class="sxs-lookup"><span data-stu-id="fe384-118">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="fe384-119">Ve velkých projektech s více třídami závislých na `MyDependency` je tak konfigurační kód roztroušen na různých místech aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe384-119">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="fe384-120">Tento způsob implementace je obtížný na jednotkové testování.</span><span class="sxs-lookup"><span data-stu-id="fe384-120">This implementation is difficult to unit test.</span></span> <span data-ttu-id="fe384-121">Aplikace by měla používat mock nebo stub třídy `MyDependency`, což však není možné s tímto přístupem.</span><span class="sxs-lookup"><span data-stu-id="fe384-121">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="fe384-122">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="fe384-122">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="fe384-123">Použití rozhraní k abstrakci implementace závislostí.</span><span class="sxs-lookup"><span data-stu-id="fe384-123">The use of an interface to abstract the dependency implementation.</span></span>
* <span data-ttu-id="fe384-124">Registrace závislosti v kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="fe384-124">Registration of the dependency in a service container.</span></span> <span data-ttu-id="fe384-125">ASP.NET Core poskytuje integrovaný kontejner služeb, [IServiceProvider](/dotnet/api/system.iserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="fe384-125">ASP.NET Core provides a built-in service container, [IServiceProvider](/dotnet/api/system.iserviceprovider).</span></span> <span data-ttu-id="fe384-126">Služby jsou registrované v metodě `Startup.ConfigureServices` aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe384-126">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="fe384-127">*Vkládání* služeb do konstruktoru třídy, ve které se používá.</span><span class="sxs-lookup"><span data-stu-id="fe384-127">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="fe384-128">Framework přebírá zodpovědnost za vytváření instancí závislostí a jejich uvolňování, kdy již nejsou dále potřebné.</span><span class="sxs-lookup"><span data-stu-id="fe384-128">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="fe384-129">V [ukázkové aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definuje rozhraní `IMyDependency` metody, které poskytuje služba aplikaci:</span><span class="sxs-lookup"><span data-stu-id="fe384-129">In the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fe384-130">Toto rozhraní je implementováno konkrétním typem `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="fe384-130">This interface is implemented by a concrete type, `MyDependency`:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fe384-131">Třída `MyDependency` požaduje [ILogger&lt;TCategoryName&gt; ](/dotnet/api/microsoft.extensions.logging.ilogger-1) ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="fe384-131">`MyDependency` requests an [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1) in its constructor.</span></span> <span data-ttu-id="fe384-132">Není neobvyklé používat zřetězené vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="fe384-132">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="fe384-133">Každá požadovaná závislost může v zápětí požadovat své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="fe384-133">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="fe384-134">Kontejner řeší závislosti v grafu a vrací plně vyřešené služby.</span><span class="sxs-lookup"><span data-stu-id="fe384-134">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="fe384-135">Množina závislostí, které musí být rozhodnuty, se obvykle označuje jako *strom závislostí*, *graf závislostí*, nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="fe384-135">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="fe384-136">`IMyDependency` a `ILogger<TCategoryName>` musí být zaregistrovány v kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="fe384-136">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="fe384-137">`IMyDependency` je zaregistrovaný v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fe384-137">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fe384-138">`ILogger<TCategoryName>` je registrován infrastrukturou pro abstrakci protokolování, takže je [službou poskytovanou frameworkem](#framework-provided-services) registrovanou ve výchozím nastavení frameworkem.</span><span class="sxs-lookup"><span data-stu-id="fe384-138">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="fe384-139">V ukázkové aplikaci je služba `IMyDependency` registrována s konkrétním typem `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="fe384-139">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="fe384-140">Registrace specifikuje rámec životnosti služby na životnost jednoho požadavku.</span><span class="sxs-lookup"><span data-stu-id="fe384-140">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="fe384-141">[Životnosti služby](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="fe384-141">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="fe384-142">Každá rozšiřující metoda `services.Add{SERVICE_NAME}` přidává (a potenciálně konfiguruje) služby.</span><span class="sxs-lookup"><span data-stu-id="fe384-142">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="fe384-143">Například `services.AddMvc()` přidává služby Stránek Razor a MVC.</span><span class="sxs-lookup"><span data-stu-id="fe384-143">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="fe384-144">Doporučujeme, aby v aplikacích byla tato konvence dodržena.</span><span class="sxs-lookup"><span data-stu-id="fe384-144">We recommended that apps follow this convention.</span></span> <span data-ttu-id="fe384-145">Umístěte rozšiřující metody do jmenného prostoru [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pro zapouzdření skupin registrací služeb.</span><span class="sxs-lookup"><span data-stu-id="fe384-145">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="fe384-146">Pokud konstruktor služby vyžaduje primitivní typ, například `string`, může být vložen pomocí [konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností (options pattern)](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="fe384-146">If the service's constructor requires a primitive, such as a `string`, the primitive can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="fe384-147">Instance služby je požadována prostřednictvím konstruktoru třídy, kde se služba využívá a přiřazuje do privátního pole.</span><span class="sxs-lookup"><span data-stu-id="fe384-147">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="fe384-148">Toto pole se používá pro přístup ke službě podle potřeby v rámci celé třídy.</span><span class="sxs-lookup"><span data-stu-id="fe384-148">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="fe384-149">V ukázkové aplikaci je požadována instance `IMyDependency` a posléze použita k volání metody `WriteMessage` dané služby:</span><span class="sxs-lookup"><span data-stu-id="fe384-149">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/MyDependencyController.cs?name=snippet1&highlight=3,5-8,13-14)]

::: moniker-end

## <a name="framework-provided-services"></a><span data-ttu-id="fe384-150">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="fe384-150">Framework-provided services</span></span>

<span data-ttu-id="fe384-151">Metoda `Startup.ConfigureServices` zodpovídá za definování služeb používaných aplikací, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="fe384-151">The `Startup.ConfigureServices` method is responsible for defining the services the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="fe384-152">Zpočátku jsou v `IServiceCollection`, který je poskytován metodě `ConfigureServices`, definovány následující služby (v závislosti na [konfiguraci hostitele](xref:fundamentals/index#host)):</span><span class="sxs-lookup"><span data-stu-id="fe384-152">Initially, the `IServiceCollection` provided to `ConfigureServices` has the following services defined (depending on [how the host was configured](xref:fundamentals/index#host)):</span></span>

| <span data-ttu-id="fe384-153">Typ služby</span><span class="sxs-lookup"><span data-stu-id="fe384-153">Service Type</span></span> | <span data-ttu-id="fe384-154">Životnost</span><span class="sxs-lookup"><span data-stu-id="fe384-154">Lifetime</span></span> |
| ------------ | -------- |
| [<span data-ttu-id="fe384-155">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span><span class="sxs-lookup"><span data-stu-id="fe384-155">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | <span data-ttu-id="fe384-156">Přechodná</span><span class="sxs-lookup"><span data-stu-id="fe384-156">Transient</span></span> |
| [<span data-ttu-id="fe384-157">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="fe384-157">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | <span data-ttu-id="fe384-158">Singleton</span><span class="sxs-lookup"><span data-stu-id="fe384-158">Singleton</span></span> |
| [<span data-ttu-id="fe384-159">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="fe384-159">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) | <span data-ttu-id="fe384-160">Singleton</span><span class="sxs-lookup"><span data-stu-id="fe384-160">Singleton</span></span> |
| [<span data-ttu-id="fe384-161">Microsoft.AspNetCore.Hosting.IStartup</span><span class="sxs-lookup"><span data-stu-id="fe384-161">Microsoft.AspNetCore.Hosting.IStartup</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartup) | <span data-ttu-id="fe384-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="fe384-162">Singleton</span></span> |
| [<span data-ttu-id="fe384-163">Microsoft.AspNetCore.Hosting.IStartupFilter</span><span class="sxs-lookup"><span data-stu-id="fe384-163">Microsoft.AspNetCore.Hosting.IStartupFilter</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) | <span data-ttu-id="fe384-164">Přechodná</span><span class="sxs-lookup"><span data-stu-id="fe384-164">Transient</span></span> |
| [<span data-ttu-id="fe384-165">Microsoft.AspNetCore.Hosting.Server.IServer</span><span class="sxs-lookup"><span data-stu-id="fe384-165">Microsoft.AspNetCore.Hosting.Server.IServer</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) | <span data-ttu-id="fe384-166">Singleton</span><span class="sxs-lookup"><span data-stu-id="fe384-166">Singleton</span></span> |
| [<span data-ttu-id="fe384-167">Microsoft.AspNetCore.Http.IHttpContextFactory</span><span class="sxs-lookup"><span data-stu-id="fe384-167">Microsoft.AspNetCore.Http.IHttpContextFactory</span></span>](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextfactory) | <span data-ttu-id="fe384-168">Přechodná</span><span class="sxs-lookup"><span data-stu-id="fe384-168">Transient</span></span> |
| [<span data-ttu-id="fe384-169">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="fe384-169">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.logging.ilogger) | <span data-ttu-id="fe384-170">Singleton</span><span class="sxs-lookup"><span data-stu-id="fe384-170">Singleton</span></span> |
| [<span data-ttu-id="fe384-171">Microsoft.Extensions.Logging.ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="fe384-171">Microsoft.Extensions.Logging.ILoggerFactory</span></span>](/dotnet/api/microsoft.extensions.logging.iloggerfactory) | <span data-ttu-id="fe384-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="fe384-172">Singleton</span></span> |
| [<span data-ttu-id="fe384-173">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span><span class="sxs-lookup"><span data-stu-id="fe384-173">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span></span>](/dotnet/api/microsoft.extensions.objectpool.objectpoolprovider) | <span data-ttu-id="fe384-174">Singleton</span><span class="sxs-lookup"><span data-stu-id="fe384-174">Singleton</span></span> |
| [<span data-ttu-id="fe384-175">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="fe384-175">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) | <span data-ttu-id="fe384-176">Přechodná</span><span class="sxs-lookup"><span data-stu-id="fe384-176">Transient</span></span> |
| [<span data-ttu-id="fe384-177">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="fe384-177">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.ioptions-1) | <span data-ttu-id="fe384-178">Singleton</span><span class="sxs-lookup"><span data-stu-id="fe384-178">Singleton</span></span> |
| [<span data-ttu-id="fe384-179">System.Diagnostics.DiagnosticSource</span><span class="sxs-lookup"><span data-stu-id="fe384-179">System.Diagnostics.DiagnosticSource</span></span>](/dotnet/core/api/system.diagnostics.diagnosticsource) | <span data-ttu-id="fe384-180">Singleton</span><span class="sxs-lookup"><span data-stu-id="fe384-180">Singleton</span></span> |
| [<span data-ttu-id="fe384-181">System.Diagnostics.DiagnosticListener</span><span class="sxs-lookup"><span data-stu-id="fe384-181">System.Diagnostics.DiagnosticListener</span></span>](/dotnet/core/api/system.diagnostics.diagnosticlistener) | <span data-ttu-id="fe384-182">Singleton</span><span class="sxs-lookup"><span data-stu-id="fe384-182">Singleton</span></span> |

<span data-ttu-id="fe384-183">Pokud je dostupná rozšiřující metoda rozšiřující kolekci služeb o registraci služby (případě jejích závislých služeb, je-li to požadováno), je zvykem použít jedinou rozšiřující metodu `Add{SERVICE_NAME}` k registraci všech služeb vyžadovaných danou službu.</span><span class="sxs-lookup"><span data-stu-id="fe384-183">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="fe384-184">Následující kód je příkladem toho, jak přidat dodatečné služby do kontejneru pomocí rozšiřujících metod [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity), a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):</span><span class="sxs-lookup"><span data-stu-id="fe384-184">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity), and [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddMvc();
}
```

<span data-ttu-id="fe384-185">Další informace naleznete v tématu [Třída ServiceCollection](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="fe384-185">For more information, see the [ServiceCollection Class](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="fe384-186">Životnost služby</span><span class="sxs-lookup"><span data-stu-id="fe384-186">Service lifetimes</span></span>

<span data-ttu-id="fe384-187">Zvolte odpovídající životnost pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="fe384-187">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="fe384-188">Služby ASP.NET Core můžete nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="fe384-188">ASP.NET Core services can be configured with the following lifetimes:</span></span>

<span data-ttu-id="fe384-189">**Transient (přechodná)**</span><span class="sxs-lookup"><span data-stu-id="fe384-189">**Transient**</span></span>

<span data-ttu-id="fe384-190">Služby s přechodnou životností se vytvoří pokaždé, kdy jsou požadovány.</span><span class="sxs-lookup"><span data-stu-id="fe384-190">Transient lifetime services are created each time they're requested.</span></span> <span data-ttu-id="fe384-191">Tato životnost je vhodná pro jednoduché, bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="fe384-191">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="fe384-192">**Scoped (vymezená)**</span><span class="sxs-lookup"><span data-stu-id="fe384-192">**Scoped**</span></span>

<span data-ttu-id="fe384-193">Služby s vymezenou životností se vytváří jedinkrát za HTTP požadavek.</span><span class="sxs-lookup"><span data-stu-id="fe384-193">Scoped lifetime services are created once per request.</span></span>

> [!WARNING]
> <span data-ttu-id="fe384-194">Při použití služby s vymezenou živostností v middlewaru vkládejte službu do metody `Invoke` nebo `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="fe384-194">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="fe384-195">Nevkládejte službu prostřednictvím konstruktoru, protože se služba bude chovat se jako singleton.</span><span class="sxs-lookup"><span data-stu-id="fe384-195">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="fe384-196">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="fe384-196">For more information, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="fe384-197">**Singleton**</span><span class="sxs-lookup"><span data-stu-id="fe384-197">**Singleton**</span></span>

<span data-ttu-id="fe384-198">Služby se životností typu singleton se vytvoří při prvním vyžádání služby (nebo když je spuštěna metoda `ConfigureServices` a instance je specifikována při registraci služby).</span><span class="sxs-lookup"><span data-stu-id="fe384-198">Singleton lifetime services are created the first time they're requested (or when `ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="fe384-199">Každý další požadavek použije stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="fe384-199">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="fe384-200">Pokud je v aplikaci vyžadováno chování ve stylu návrhového vzoru Singleton, doporučuje se použít kontejner služeb ke správě životnosti takového objektu.</span><span class="sxs-lookup"><span data-stu-id="fe384-200">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="fe384-201">Neposkytujte vlastní implementaci návrhového vzoru Singleton a umožněte tak uživatelskému kódu spravovat životnost objektu v rámci třídy.</span><span class="sxs-lookup"><span data-stu-id="fe384-201">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="fe384-202">Je nebezpečné získávat vymezené služby ze singletonů.</span><span class="sxs-lookup"><span data-stu-id="fe384-202">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="fe384-203">Může to způsobit nesprávný stav služby při zpracování následujících požadavků.</span><span class="sxs-lookup"><span data-stu-id="fe384-203">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="fe384-204">Chování vkládání pomocí konstruktoru</span><span class="sxs-lookup"><span data-stu-id="fe384-204">Constructor injection behavior</span></span>

<span data-ttu-id="fe384-205">Služby mohou být řešeny pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="fe384-205">Services can be resolved by two mechanisms:</span></span>

* `IServiceProvider`
* <span data-ttu-id="fe384-206">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; umožňuje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="fe384-206">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="fe384-207">`ActivatorUtilities` se používá s uživatelsky orientovanými abstrakcemi, jako jsou například Tag Helpery, kontrolery MVC a bindery modelů.</span><span class="sxs-lookup"><span data-stu-id="fe384-207">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="fe384-208">Konstruktory mohou přijímat argumenty, které nejsou poskytovány v rámci vkládání závislostí, ale takové argumenty musí mít přiřazené výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="fe384-208">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="fe384-209">Pokud jsou služby řešeny pomocí `IServiceProvider` nebo `ActivatorUtilities`, pak je vyžadován *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="fe384-209">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="fe384-210">Když jsou vyřešeny služby `ActivatorUtilities`, vkládání konstruktor vyžaduje, že pouze jeden použít konstruktor existuje.</span><span class="sxs-lookup"><span data-stu-id="fe384-210">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="fe384-211">Přetížení konstruktoru je podporováno, ale může existovat pouze jedno přetížením, jehož argumenty jsou splnitelné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="fe384-211">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="fe384-212">Kontexty Entity Frameworku</span><span class="sxs-lookup"><span data-stu-id="fe384-212">Entity Framework contexts</span></span>

<span data-ttu-id="fe384-213">Entity Framework kontexty jsou obvykle přidány do služby kontejneru pomocí [s vymezeným oborem životnost](#service-lifetimes) protože operací databáze webové aplikace jsou obvykle omezená na žádost o.</span><span class="sxs-lookup"><span data-stu-id="fe384-213">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the request.</span></span> <span data-ttu-id="fe384-214">Výchozí doba života má obor, pokud se nezadá životnost <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> přetížení při registraci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="fe384-214">The default lifetime is scoped if a lifetime isn't specified by an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> overload when registering the database context.</span></span> <span data-ttu-id="fe384-215">Služby danou dobu života neměli používat kontext databáze s životností kratší než služba.</span><span class="sxs-lookup"><span data-stu-id="fe384-215">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="fe384-216">Životnosti a možnosti registrace</span><span class="sxs-lookup"><span data-stu-id="fe384-216">Lifetime and registration options</span></span>

<span data-ttu-id="fe384-217">Na ukázku rozdílů mezi životnostmi a možnostmi registrace si prohlédněte následující rozhraní reprezentující úlohy jako operace s jedinečným identifikátorem `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="fe384-217">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="fe384-218">V závislosti na tom, jak je životnost této služby nakonfigurována pro následující rozhraní, poskytne kontejner stejnou nebo rozdílnou instanci služby během vyžádání třídou:</span><span class="sxs-lookup"><span data-stu-id="fe384-218">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fe384-219">Rozhraní jsou implementovány ve třídě `Operation`.</span><span class="sxs-lookup"><span data-stu-id="fe384-219">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="fe384-220">Konstruktor `Operation` vytvoří identifikátor GUID, pokud není explicitně poskytnut:</span><span class="sxs-lookup"><span data-stu-id="fe384-220">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fe384-221">Služba `OperationService` je zaregistrována tak, aby závisela na jednotlivých typech tříd `Operation`.</span><span class="sxs-lookup"><span data-stu-id="fe384-221">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="fe384-222">Když je `OperationService` vyžádána pomocí vkládání závislostí, obdrží buď novou nebo stávající instanci třídy jednotlivých služeb v závislosti na životnosti závislých služeb.</span><span class="sxs-lookup"><span data-stu-id="fe384-222">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="fe384-223">Pokud jsou služby s přechodnou životností vytvořeny při vyžádání, `OperationId` služby `IOperationTransient` se bude lišit od `OperationId` služby `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="fe384-223">If transient services are created when requested, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="fe384-224">`OperationService` obdrží novou instanci třídy `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="fe384-224">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="fe384-225">Nová instance implikuje rozdílné `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="fe384-225">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="fe384-226">Pokud jsou služby s vymezenou životností vytvořeny při požadavku, `OperationId` ze služby `IOperationScoped` je stejné jako u `OperationService` v rámci požadavku.</span><span class="sxs-lookup"><span data-stu-id="fe384-226">If scoped services are created per request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a request.</span></span> <span data-ttu-id="fe384-227">Napříč různými požadavky obě služby sdílejí jinou hodnotu `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="fe384-227">Across requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="fe384-228">Pokud jsou singletony a služby se životností typu singleton vytvářeny jednou napříč všemi požadavky a službami, `OperationId` je konstantní mezi všemi požadavky služeb.</span><span class="sxs-lookup"><span data-stu-id="fe384-228">If singleton and singleton-instance services are created once and used across all requests and all services, the `OperationId` is constant across all service requests.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fe384-229">V `Startup.ConfigureServices` je každý typ přidán do kontejneru na základě svého pojmenování podle životnosti:</span><span class="sxs-lookup"><span data-stu-id="fe384-229">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=12-15,18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

<span data-ttu-id="fe384-230">Služba `IOperationSingletonInstance` používá konkrétní instanci se známým ID nastaveným na `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="fe384-230">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="fe384-231">Je pak zřejmé, kdy je tento typ použit (jeho identifikátor GUID obsahuje samé nuly).</span><span class="sxs-lookup"><span data-stu-id="fe384-231">It's clear when this type is in use (its GUID is all zeroes).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="fe384-232">Ukázková aplikace demonstruje živostnosti objektů v rámci jednotlivých požadavků a mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="fe384-232">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="fe384-233">Třída `IndexModel` ukázkové aplikace vyžaduje každý druh typu `IOperation` a službu `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="fe384-233">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="fe384-234">Stránka následně vypisuje všechny hodnoty `OperationId` modelu stránky a služby prostřednictvím přiřazených vlastností:</span><span class="sxs-lookup"><span data-stu-id="fe384-234">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="fe384-235">Ukázková aplikace demonstruje živostnosti objektů v rámci jednotlivých požadavků a mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="fe384-235">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="fe384-236">Ukázková aplikace obsahuje `OperationsController`, který vyžaduje každý druh typu`IOperation` a službu `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="fe384-236">The sample app includes an `OperationsController` that requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="fe384-237">Akce `Index` nastaví služby do objektu `ViewBag`, aby bylo možné zobrazit hodnotu `OperationId` služby:</span><span class="sxs-lookup"><span data-stu-id="fe384-237">The `Index` action sets the services into the `ViewBag` for display of the service's `OperationId` values:</span></span>

[!code-csharp[](dependency-injection/samples/1.x/DependencyInjectionSample/Controllers/OperationsController.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fe384-238">Následující výpis ukazuje výsledek dvou HTTP požadavků:</span><span class="sxs-lookup"><span data-stu-id="fe384-238">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="fe384-239">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="fe384-239">**First request:**</span></span>

<span data-ttu-id="fe384-240">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="fe384-240">Controller operations:</span></span>

<span data-ttu-id="fe384-241">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="fe384-241">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="fe384-242">Nastavit rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="fe384-242">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="fe384-243">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fe384-243">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fe384-244">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fe384-244">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fe384-245">Operace `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="fe384-245">`OperationService` operations:</span></span>

<span data-ttu-id="fe384-246">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="fe384-246">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="fe384-247">Nastavit rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="fe384-247">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="fe384-248">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fe384-248">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fe384-249">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fe384-249">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fe384-250">**Druhý požadavek:**</span><span class="sxs-lookup"><span data-stu-id="fe384-250">**Second request:**</span></span>

<span data-ttu-id="fe384-251">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="fe384-251">Controller operations:</span></span>

<span data-ttu-id="fe384-252">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="fe384-252">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="fe384-253">Nastavit rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="fe384-253">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="fe384-254">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fe384-254">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fe384-255">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fe384-255">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fe384-256">Operace `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="fe384-256">`OperationService` operations:</span></span>

<span data-ttu-id="fe384-257">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="fe384-257">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="fe384-258">Nastavit rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="fe384-258">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="fe384-259">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="fe384-259">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="fe384-260">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="fe384-260">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="fe384-261">Všimněte si, které hodnoty `OperationId` se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="fe384-261">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="fe384-262">Objekty s *přechodnou* životností jsou vždy rozdílné.</span><span class="sxs-lookup"><span data-stu-id="fe384-262">*Transient* objects are always different.</span></span> <span data-ttu-id="fe384-263">Poznamenejme, že se hodnota přechodného `OperationId` pro první i druhý požadavek liší jak pro obě operace `OperationService`, tak i mezi požadavky.</span><span class="sxs-lookup"><span data-stu-id="fe384-263">Note that the transient `OperationId` value for both the first and second requests are different for both `OperationService` operations and across requests.</span></span> <span data-ttu-id="fe384-264">Nová instance je poskytnuta pro každou službu a každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="fe384-264">A new instance is provided to each service and request.</span></span>
* <span data-ttu-id="fe384-265">Objekty s vymezenou *životností* jsou stejné v rámci jednoho požadavku, ale jiné napříč různými požadavky.</span><span class="sxs-lookup"><span data-stu-id="fe384-265">*Scoped* objects are the same within a request but different across requests.</span></span>
* <span data-ttu-id="fe384-266">Objekty se živostností typu *singleton jsou* stejné pro všechny objekty a všechny požadavky bez ohledu na to, jestli je instance `Operation` poskytnuta v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fe384-266">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="fe384-267">Volání služeb z main</span><span class="sxs-lookup"><span data-stu-id="fe384-267">Call services from main</span></span>

<span data-ttu-id="fe384-268">Vytvořte [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) obsahující metodu [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) k rozhodování služeb s životností vymezenou v rámci oboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe384-268">Create an [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) with [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="fe384-269">Tento způsob je užitečný pro přístup k službám s vymezenou životností při provádění inicializačních úloh během spuštění.</span><span class="sxs-lookup"><span data-stu-id="fe384-269">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="fe384-270">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="fe384-270">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = CreateWebHostBuilder(args).Build();

    using (var serviceScope = host.Services.CreateScope())
    {
        var services = serviceScope.ServiceProvider;

        try
        {
            var serviceContext = services.GetRequiredService<MyScopedService>();
            // Use the context here
        }
        catch (Exception ex)
        {
            var logger = services.GetRequiredService<ILogger<Program>>();
            logger.LogError(ex, "An error occurred.");
        }
    }

    host.Run();
}
```

## <a name="scope-validation"></a><span data-ttu-id="fe384-271">Ověření rámce životnosti</span><span class="sxs-lookup"><span data-stu-id="fe384-271">Scope validation</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="fe384-272">Pokud aplikace běží ve vývojovém prostředí, výchozí poskytovatel služeb provádí kontroly pro ověření toho, že:</span><span class="sxs-lookup"><span data-stu-id="fe384-272">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="fe384-273">Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="fe384-273">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="fe384-274">Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.</span><span class="sxs-lookup"><span data-stu-id="fe384-274">Scoped services aren't directly or indirectly injected into singletons.</span></span>

::: moniker-end

<span data-ttu-id="fe384-275">Kořenový poskytovatel služeb je vytvořen při volání [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="fe384-275">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="fe384-276">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe384-276">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="fe384-277">Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="fe384-277">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="fe384-278">Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe384-278">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="fe384-279">Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="fe384-279">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="fe384-280">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="fe384-280">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="fe384-281">Služby požadavků</span><span class="sxs-lookup"><span data-stu-id="fe384-281">Request Services</span></span>

<span data-ttu-id="fe384-282">Služby dostupné v rámci požadavků ASP.NET Core z `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices).</span><span class="sxs-lookup"><span data-stu-id="fe384-282">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices) collection.</span></span>

<span data-ttu-id="fe384-283">Služby požadavků představují služby nakonfigurované a požadováné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe384-283">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="fe384-284">Pokud objekty specifikují závislosti, jsou řešeny pomocí typů dostupných v `RequestServices`, nikoli `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="fe384-284">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="fe384-285">Aplikace by obvykle neměly používat tyto vlastnosti přímo.</span><span class="sxs-lookup"><span data-stu-id="fe384-285">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="fe384-286">Místo toho získávejte požadované typy pomocí konstruktoru třídy a nechte framework vložit odpovídající závislosti.</span><span class="sxs-lookup"><span data-stu-id="fe384-286">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="fe384-287">To poskytuje třídy, které usnadňuje testování.</span><span class="sxs-lookup"><span data-stu-id="fe384-287">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="fe384-288">Pro přístup ke kolekci `RequestServices` upřednostněte získávání závislostí jako parametr konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="fe384-288">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="fe384-289">Návrh služeb pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="fe384-289">Design services for dependency injection</span></span>

<span data-ttu-id="fe384-290">Osvědčené postupy jsou následující:</span><span class="sxs-lookup"><span data-stu-id="fe384-290">Best practices are to:</span></span>

* <span data-ttu-id="fe384-291">Navrhujte služby tak, aby využívaly vkládání závislostí pro získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="fe384-291">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="fe384-292">Vyhněte se volání stavová a statické metody.</span><span class="sxs-lookup"><span data-stu-id="fe384-292">Avoid stateful, static method calls.</span></span>
* <span data-ttu-id="fe384-293">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="fe384-293">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="fe384-294">Přímé vytváření instancí způsobuje přímou závislost na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="fe384-294">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="fe384-295">Malé, skvěle a snadno otestované, vytvořit třídy aplikace.</span><span class="sxs-lookup"><span data-stu-id="fe384-295">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="fe384-296">Pokud má třída nepřiměřeně mnoho vložených závislostí, je to obecně znakem toho, že má třída příliš mnoho zodpovědností a porušuje tak [zásadu jediné zodpovědnosti (Single Responsibility Principle, SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="fe384-296">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="fe384-297">V tom případě se pokuste refaktorovat třídu tak, že některé z jeho zodpovědností přesunete do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="fe384-297">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="fe384-298">Mějte na paměti, že třídy modelů stránek Razor a třídy kontrolerů MVC by měly být zaměřeny na aspekty uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fe384-298">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="fe384-299">Business pravidla a konkrétní implementace přístupu k datům by měly být v odpovídajících třídách respektující [princip oddělení zopovědnosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="fe384-299">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="fe384-300">Uvolnění služeb</span><span class="sxs-lookup"><span data-stu-id="fe384-300">Disposal of services</span></span>

<span data-ttu-id="fe384-301">Kontejner volá metodu `Dispose` u všech typů, které vytvořil a které implementují rozhraní `IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="fe384-301">The container calls `Dispose` for the `IDisposable` types it creates.</span></span> <span data-ttu-id="fe384-302">Instance přidané do kontejneru pomocí uživatelské kódu nejsou automaticky uvolňovány.</span><span class="sxs-lookup"><span data-stu-id="fe384-302">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

```csharp
// Services that implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // The container creates the following instances and disposes them automatically:
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // The container doesn't create the following instances, so it doesn't dispose of
    // the instances automatically:
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

::: moniker range="= aspnetcore-1.0"

> [!NOTE]
> <span data-ttu-id="fe384-303">V ASP.NET Core 1.0 volá kontejner metodu Dispose u *všech* `IDisposable` objektů včetně těch, které se nepovedlo vytvořit.</span><span class="sxs-lookup"><span data-stu-id="fe384-303">In ASP.NET Core 1.0, the container calls dispose on *all* `IDisposable` objects, including those it didn't create.</span></span>

::: moniker-end

## <a name="default-service-container-replacement"></a><span data-ttu-id="fe384-304">Nahrazení výchozího kontejneru služeb</span><span class="sxs-lookup"><span data-stu-id="fe384-304">Default service container replacement</span></span>

<span data-ttu-id="fe384-305">Integrovaný kontejner služeb je primárně určen pro naplnění potřeb frameworku a většiny uživatelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="fe384-305">The built-in service container is meant to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="fe384-306">Doporučujeme používat integrovaný kontejner, dokud nebudete potřebovat specifické funkce nepodporované kontejnerem.</span><span class="sxs-lookup"><span data-stu-id="fe384-306">We recommend using the built-in container unless you need a specific feature that it doesn't support.</span></span> <span data-ttu-id="fe384-307">Některé z funkcí podporovaných v kontejnerech 3. stran neobsažených ve výchozím kontejneru jsou:</span><span class="sxs-lookup"><span data-stu-id="fe384-307">Some of the features supported in 3rd party containers not found in the built-in container:</span></span>

* <span data-ttu-id="fe384-308">Vkládání pomocí vlastností</span><span class="sxs-lookup"><span data-stu-id="fe384-308">Property injection</span></span>
* <span data-ttu-id="fe384-309">Vkládání podle názvu</span><span class="sxs-lookup"><span data-stu-id="fe384-309">Injection based on name</span></span>
* <span data-ttu-id="fe384-310">Vnořené kontejnery</span><span class="sxs-lookup"><span data-stu-id="fe384-310">Child containers</span></span>
* <span data-ttu-id="fe384-311">Vlastní správa životnosti</span><span class="sxs-lookup"><span data-stu-id="fe384-311">Custom lifetime management</span></span>
* <span data-ttu-id="fe384-312">Podpora `Func<T>` pro línou inicializaci</span><span class="sxs-lookup"><span data-stu-id="fe384-312">`Func<T>` support for lazy initialization</span></span>

<span data-ttu-id="fe384-313">Pro seznam některých kontejnerů podporujících adaptéry, vizte [Soubor readme.md ke Vkládání závislostí](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection).</span><span class="sxs-lookup"><span data-stu-id="fe384-313">See the [Dependency Injection readme.md file](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) for a list of some of the containers that support adapters.</span></span>

<span data-ttu-id="fe384-314">Následující příklad nahrazuje integrovaný kontejner kontejnerem [Autofac](https://autofac.org/):</span><span class="sxs-lookup"><span data-stu-id="fe384-314">The following sample replaces the built-in container with [Autofac](https://autofac.org/):</span></span>

* <span data-ttu-id="fe384-315">Nainstalujte odpovídající balíčky kontejneru:</span><span class="sxs-lookup"><span data-stu-id="fe384-315">Install the appropriate container package(s):</span></span>

  * [<span data-ttu-id="fe384-316">Autofac</span><span class="sxs-lookup"><span data-stu-id="fe384-316">Autofac</span></span>](https://www.nuget.org/packages/Autofac/)
  * [<span data-ttu-id="fe384-317">Autofac.Extensions.DependencyInjection</span><span class="sxs-lookup"><span data-stu-id="fe384-317">Autofac.Extensions.DependencyInjection</span></span>](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* <span data-ttu-id="fe384-318">Nakonfigurujte kontejner v `Startup.ConfigureServices` a vraťte `IServiceProvider`:</span><span class="sxs-lookup"><span data-stu-id="fe384-318">Configure the container in `Startup.ConfigureServices` and return an `IServiceProvider`:</span></span>

    ```csharp
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        // Add other framework services

        // Add Autofac
        var containerBuilder = new ContainerBuilder();
        containerBuilder.RegisterModule<DefaultModule>();
        containerBuilder.Populate(services);
        var container = containerBuilder.Build();
        return new AutofacServiceProvider(container);
    }
    ```

    <span data-ttu-id="fe384-319">`Startup.ConfigureServices` musí vracet `IServiceProvider` pro použití kontejneru 3. stran.</span><span class="sxs-lookup"><span data-stu-id="fe384-319">To use a 3rd party container, `Startup.ConfigureServices` must return `IServiceProvider`.</span></span>

* <span data-ttu-id="fe384-320">Konfigurace Autofacu v `DefaultModule`:</span><span class="sxs-lookup"><span data-stu-id="fe384-320">Configure Autofac in `DefaultModule`:</span></span>

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

<span data-ttu-id="fe384-321">Za běhu je použit Autofac pro rozhodování typů a vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="fe384-321">At runtime, Autofac is used to resolve types and inject dependencies.</span></span> <span data-ttu-id="fe384-322">Další informace o používání Autofac s ASP.NET Core naleznete v [dokumentaci Autofac](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span><span class="sxs-lookup"><span data-stu-id="fe384-322">To learn more about using Autofac with ASP.NET Core, see the [Autofac documentation](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span></span>

### <a name="thread-safety"></a><span data-ttu-id="fe384-323">Bezpečný přístup z více vláken</span><span class="sxs-lookup"><span data-stu-id="fe384-323">Thread safety</span></span>

<span data-ttu-id="fe384-324">Singleton služby musí být bezpečné pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="fe384-324">Singleton services need to be thread safe.</span></span> <span data-ttu-id="fe384-325">Pokud služby typu singleton obsahují závislosti na službách s přechodnou životností, pak je možné, že tyto služby budou muset být taktéž zabezpečeny pro přístup z více vláken v závislosti na tom, jak jsou používány v rámci singleton služby.</span><span class="sxs-lookup"><span data-stu-id="fe384-325">If a singleton service has a dependency on a transient service, the transient service may also need to be thread safe depending how it's used by the singleton.</span></span>

<span data-ttu-id="fe384-326">Factory metody jedné služby, jako je například druhý argument metody [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider, TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), nebude musí být bezpečné pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="fe384-326">The factory method of single service, such as the second argument to [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), doesn't need to be thread-safe.</span></span> <span data-ttu-id="fe384-327">Stejně jako u typového (`static`) konstruktoru je zaručeno, že je volán jednou jediným vláknem.</span><span class="sxs-lookup"><span data-stu-id="fe384-327">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="fe384-328">Doporučení</span><span class="sxs-lookup"><span data-stu-id="fe384-328">Recommendations</span></span>

* <span data-ttu-id="fe384-329">`async/await` a `Task` závislosti služby rozlišení není podporováno.</span><span class="sxs-lookup"><span data-stu-id="fe384-329">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="fe384-330">C# nepodporuje asynchronní konstruktory, proto je doporučený vzor používání asynchronních metod po synchronně překladu služby.</span><span class="sxs-lookup"><span data-stu-id="fe384-330">C# does not support asynchronous constructors, therefore the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="fe384-331">Vyhněte se ukládání dat a konfigurace přímo do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="fe384-331">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="fe384-332">Například nákupní košík uživatele by neměl být přidáván do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="fe384-332">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="fe384-333">Konfigurace by měla používat [vzor možností (options pattern)](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="fe384-333">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="fe384-334">Podobně se vystříhejte použití objektů "držící data", jejichž jediným účelem je poskytnutí přístupu k některému jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="fe384-334">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="fe384-335">Je lepší požádat o skutečné položky prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="fe384-335">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="fe384-336">Vyhněte se statickému přístupu ke službám (například staticky typovaným [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="fe384-336">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) for use elsewhere).</span></span>

* <span data-ttu-id="fe384-337">Vyhněte se použití *služby lokátoru vzor*.</span><span class="sxs-lookup"><span data-stu-id="fe384-337">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="fe384-338">Není třeba vyvolat <xref:System.IServiceProvider.GetService*> při DI místo toho můžete získat instanci služby.</span><span class="sxs-lookup"><span data-stu-id="fe384-338">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead.</span></span> <span data-ttu-id="fe384-339">Další variantou Lokátor služby, aby se vkládá objekt factory, který řeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="fe384-339">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="fe384-340">Obě tyto postupy kombinace [ovládacího prvku inverzi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategie.</span><span class="sxs-lookup"><span data-stu-id="fe384-340">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="fe384-341">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).</span><span class="sxs-lookup"><span data-stu-id="fe384-341">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).</span></span>

<span data-ttu-id="fe384-342">Stejně jako u všech doporučení mohou nastat situace, ve kterých je možné tato doporučení ignorovat.</span><span class="sxs-lookup"><span data-stu-id="fe384-342">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="fe384-343">Výjimky se vyskytují jen vzácně &mdash; nejčastěji jsou to speciální případy uvnitř frameworku samotného.</span><span class="sxs-lookup"><span data-stu-id="fe384-343">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="fe384-344">DI je *alternativní* na vzorech přístupu statická/globální objekt.</span><span class="sxs-lookup"><span data-stu-id="fe384-344">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="fe384-345">Nebudete moci využít výhod DI, jsou-li zkombinovány s přístupem statický objekt.</span><span class="sxs-lookup"><span data-stu-id="fe384-345">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe384-346">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fe384-346">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="fe384-347">Psaní čistého kódu v ASP.NET Core pomocí vkládání závislostí (MSDN)</span><span class="sxs-lookup"><span data-stu-id="fe384-347">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="fe384-348">Návrh kontejneru spravované aplikace, Prelude: Kam patří kontejneru?</span><span class="sxs-lookup"><span data-stu-id="fe384-348">Container-Managed Application Design, Prelude: Where does the Container Belong?</span></span>](https://blogs.msdn.microsoft.com/nblumhardt/2008/12/26/container-managed-application-design-prelude-where-does-the-container-belong/)
* [<span data-ttu-id="fe384-349">Princip explicitních závislostí</span><span class="sxs-lookup"><span data-stu-id="fe384-349">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="fe384-350">Kontejner inverze závislostí a vzor vkládání závislostí (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="fe384-350">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="fe384-351">Postup pro registraci služeb s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="fe384-351">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

---
title: Vkládání závislostí v ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak se používá.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: da6ddf1f0efd164a58f017ff55ce216bbefa7cc6
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068320"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="cae32-103">Vkládání závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cae32-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="cae32-104">Podle [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cae32-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cae32-105">ASP.NET Core podporuje návrhový vzor vkládání závislostí (Dependency Injection, DI), což je technika používaná pro dosažení [inverze řízení (Inversion of Control, IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="cae32-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="cae32-106">Další informace specifické pro vkládání závislostí do kontrolerů MVC najdete v tématu <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="cae32-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="cae32-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cae32-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="cae32-108">Přehled vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="cae32-108">Overview of dependency injection</span></span>

<span data-ttu-id="cae32-109">*Závislost* je libovolný objekt, který je vyžadován jiným objektem.</span><span class="sxs-lookup"><span data-stu-id="cae32-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="cae32-110">Prohlédněte si následující třídu `MyDependency` s metodou `WriteMessage`, na které závisí jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="cae32-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="cae32-111">Pro zpřístupnění metody `WriteMessage` v jiné třídě je možné vytvořit instanci třídy `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="cae32-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="cae32-112">Třída `MyDependency` je závislostí třídy `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="cae32-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="cae32-113">Třída vytváří instanci třídy `MyDependency`, na které přímo závisí.</span><span class="sxs-lookup"><span data-stu-id="cae32-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="cae32-114">Přímé závislosti v kódu (jako ta v předchozím příkladu) jsou problematické a měli byste se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="cae32-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="cae32-115">Chcete-li nahradit `MyDependency` jinou implementací, musí být modifikována třída, která na ní závisí.</span><span class="sxs-lookup"><span data-stu-id="cae32-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="cae32-116">Pokud má třída `MyDependency` sama závislosti, musí být taktéž nakonfigurovány v dané třídě.</span><span class="sxs-lookup"><span data-stu-id="cae32-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="cae32-117">Ve velkých projektech s více třídami závislých na `MyDependency` je tak konfigurační kód roztroušen na různých místech aplikace.</span><span class="sxs-lookup"><span data-stu-id="cae32-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="cae32-118">Tento způsob implementace je obtížný na jednotkové testování.</span><span class="sxs-lookup"><span data-stu-id="cae32-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="cae32-119">Aplikace by měla používat mock nebo stub třídy `MyDependency`, což však není možné s tímto přístupem.</span><span class="sxs-lookup"><span data-stu-id="cae32-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="cae32-120">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="cae32-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="cae32-121">Použití rozhraní k abstrakci implementace závislostí.</span><span class="sxs-lookup"><span data-stu-id="cae32-121">The use of an interface to abstract the dependency implementation.</span></span>
* <span data-ttu-id="cae32-122">Registrace závislosti v kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="cae32-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="cae32-123">ASP.NET Core poskytuje integrovaný kontejner služeb, [IServiceProvider](/dotnet/api/system.iserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="cae32-123">ASP.NET Core provides a built-in service container, [IServiceProvider](/dotnet/api/system.iserviceprovider).</span></span> <span data-ttu-id="cae32-124">Služby jsou registrované v metodě `Startup.ConfigureServices` aplikace.</span><span class="sxs-lookup"><span data-stu-id="cae32-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="cae32-125">*Vkládání* služeb do konstruktoru třídy, ve které se používá.</span><span class="sxs-lookup"><span data-stu-id="cae32-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="cae32-126">Framework přebírá zodpovědnost za vytváření instancí závislostí a jejich uvolňování, kdy již nejsou dále potřebné.</span><span class="sxs-lookup"><span data-stu-id="cae32-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="cae32-127">V [ukázkové aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definuje rozhraní `IMyDependency` metody, které poskytuje služba aplikaci:</span><span class="sxs-lookup"><span data-stu-id="cae32-127">In the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="cae32-128">Toto rozhraní je implementováno konkrétním typem `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="cae32-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` <span data-ttu-id="cae32-129">Požadavky [ILogger&lt;TCategoryName&gt; ](/dotnet/api/microsoft.extensions.logging.ilogger-1) 've svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="cae32-129">requests an [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1) in its constructor.</span></span> <span data-ttu-id="cae32-130">Není neobvyklé používat zřetězené vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="cae32-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="cae32-131">Každá požadovaná závislost může v zápětí požadovat své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="cae32-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="cae32-132">Kontejner řeší závislosti v grafu a vrací plně vyřešené služby.</span><span class="sxs-lookup"><span data-stu-id="cae32-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="cae32-133">Množina závislostí, které musí být rozhodnuty, se obvykle označuje jako *strom závislostí*, *graf závislostí*, nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="cae32-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

`IMyDependency` <span data-ttu-id="cae32-134">a `ILogger<TCategoryName>` musí být zaregistrovaný v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="cae32-134">and `ILogger<TCategoryName>` must be registered in the service container.</span></span> `IMyDependency` <span data-ttu-id="cae32-135">je zaregistrovaný v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cae32-135">is registered in `Startup.ConfigureServices`.</span></span> `ILogger<TCategoryName>` <span data-ttu-id="cae32-136">registraci protokolování abstrakce infrastrukturu, takže má [služby poskytované rozhraním](#framework-provided-services) registrován ve výchozím nastavení v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cae32-136">is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="cae32-137">Odstraňuje kontejner `ILogger<TCategoryName>` s využitím [(Obecné) otevřete typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), tím eliminuje nutnost zaregistrovat každý [(Obecné) konstruovaný typ.](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="cae32-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

<span data-ttu-id="cae32-138">V ukázkové aplikaci je služba `IMyDependency` registrována s konkrétním typem `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="cae32-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="cae32-139">Registrace specifikuje rámec životnosti služby na životnost jednoho požadavku.</span><span class="sxs-lookup"><span data-stu-id="cae32-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="cae32-140">[Životnosti služby](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="cae32-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="cae32-141">Každá rozšiřující metoda `services.Add{SERVICE_NAME}` přidává (a potenciálně konfiguruje) služby.</span><span class="sxs-lookup"><span data-stu-id="cae32-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="cae32-142">Například `services.AddMvc()` přidává služby Stránek Razor a MVC.</span><span class="sxs-lookup"><span data-stu-id="cae32-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="cae32-143">Doporučujeme, aby v aplikacích byla tato konvence dodržena.</span><span class="sxs-lookup"><span data-stu-id="cae32-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="cae32-144">Umístěte rozšiřující metody do jmenného prostoru [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pro zapouzdření skupin registrací služeb.</span><span class="sxs-lookup"><span data-stu-id="cae32-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="cae32-145">Pokud konstruktor služby vyžaduje [předdefinovaný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), jako `string`, typ lze vloženy pomocí [konfigurace](xref:fundamentals/configuration/index) nebo [možnosti vzor](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="cae32-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="cae32-146">Instance služby je požadována prostřednictvím konstruktoru třídy, kde se služba využívá a přiřazuje do privátního pole.</span><span class="sxs-lookup"><span data-stu-id="cae32-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="cae32-147">Toto pole se používá pro přístup ke službě podle potřeby v rámci celé třídy.</span><span class="sxs-lookup"><span data-stu-id="cae32-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="cae32-148">V ukázkové aplikaci je požadována instance `IMyDependency` a posléze použita k volání metody `WriteMessage` dané služby:</span><span class="sxs-lookup"><span data-stu-id="cae32-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="framework-provided-services"></a><span data-ttu-id="cae32-149">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="cae32-149">Framework-provided services</span></span>

<span data-ttu-id="cae32-150">Metoda `Startup.ConfigureServices` zodpovídá za definování služeb používaných aplikací, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cae32-150">The `Startup.ConfigureServices` method is responsible for defining the services the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="cae32-151">Zpočátku jsou v `IServiceCollection`, který je poskytován metodě `ConfigureServices`, definovány následující služby (v závislosti na [konfiguraci hostitele](xref:fundamentals/index#host)):</span><span class="sxs-lookup"><span data-stu-id="cae32-151">Initially, the `IServiceCollection` provided to `ConfigureServices` has the following services defined (depending on [how the host was configured](xref:fundamentals/index#host)):</span></span>

| <span data-ttu-id="cae32-152">Typ služby</span><span class="sxs-lookup"><span data-stu-id="cae32-152">Service Type</span></span> | <span data-ttu-id="cae32-153">Životnost</span><span class="sxs-lookup"><span data-stu-id="cae32-153">Lifetime</span></span> |
| ------------ | -------- |
| [<span data-ttu-id="cae32-154">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span><span class="sxs-lookup"><span data-stu-id="cae32-154">Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | <span data-ttu-id="cae32-155">Přechodná</span><span class="sxs-lookup"><span data-stu-id="cae32-155">Transient</span></span> |
| [<span data-ttu-id="cae32-156">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="cae32-156">Microsoft.AspNetCore.Hosting.IApplicationLifetime</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | <span data-ttu-id="cae32-157">Singleton</span><span class="sxs-lookup"><span data-stu-id="cae32-157">Singleton</span></span> |
| [<span data-ttu-id="cae32-158">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="cae32-158">Microsoft.AspNetCore.Hosting.IHostingEnvironment</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) | <span data-ttu-id="cae32-159">Singleton</span><span class="sxs-lookup"><span data-stu-id="cae32-159">Singleton</span></span> |
| [<span data-ttu-id="cae32-160">Microsoft.AspNetCore.Hosting.IStartup</span><span class="sxs-lookup"><span data-stu-id="cae32-160">Microsoft.AspNetCore.Hosting.IStartup</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartup) | <span data-ttu-id="cae32-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="cae32-161">Singleton</span></span> |
| [<span data-ttu-id="cae32-162">Microsoft.AspNetCore.Hosting.IStartupFilter</span><span class="sxs-lookup"><span data-stu-id="cae32-162">Microsoft.AspNetCore.Hosting.IStartupFilter</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) | <span data-ttu-id="cae32-163">Přechodná</span><span class="sxs-lookup"><span data-stu-id="cae32-163">Transient</span></span> |
| [<span data-ttu-id="cae32-164">Microsoft.AspNetCore.Hosting.Server.IServer</span><span class="sxs-lookup"><span data-stu-id="cae32-164">Microsoft.AspNetCore.Hosting.Server.IServer</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) | <span data-ttu-id="cae32-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="cae32-165">Singleton</span></span> |
| [<span data-ttu-id="cae32-166">Microsoft.AspNetCore.Http.IHttpContextFactory</span><span class="sxs-lookup"><span data-stu-id="cae32-166">Microsoft.AspNetCore.Http.IHttpContextFactory</span></span>](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextfactory) | <span data-ttu-id="cae32-167">Přechodná</span><span class="sxs-lookup"><span data-stu-id="cae32-167">Transient</span></span> |
| [<span data-ttu-id="cae32-168">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="cae32-168">Microsoft.Extensions.Logging.ILogger&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.logging.ilogger) | <span data-ttu-id="cae32-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="cae32-169">Singleton</span></span> |
| [<span data-ttu-id="cae32-170">Microsoft.Extensions.Logging.ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="cae32-170">Microsoft.Extensions.Logging.ILoggerFactory</span></span>](/dotnet/api/microsoft.extensions.logging.iloggerfactory) | <span data-ttu-id="cae32-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="cae32-171">Singleton</span></span> |
| [<span data-ttu-id="cae32-172">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span><span class="sxs-lookup"><span data-stu-id="cae32-172">Microsoft.Extensions.ObjectPool.ObjectPoolProvider</span></span>](/dotnet/api/microsoft.extensions.objectpool.objectpoolprovider) | <span data-ttu-id="cae32-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="cae32-173">Singleton</span></span> |
| [<span data-ttu-id="cae32-174">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="cae32-174">Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) | <span data-ttu-id="cae32-175">Přechodná</span><span class="sxs-lookup"><span data-stu-id="cae32-175">Transient</span></span> |
| [<span data-ttu-id="cae32-176">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span><span class="sxs-lookup"><span data-stu-id="cae32-176">Microsoft.Extensions.Options.IOptions&lt;T&gt;</span></span>](/dotnet/api/microsoft.extensions.options.ioptions-1) | <span data-ttu-id="cae32-177">Singleton</span><span class="sxs-lookup"><span data-stu-id="cae32-177">Singleton</span></span> |
| [<span data-ttu-id="cae32-178">System.Diagnostics.DiagnosticSource</span><span class="sxs-lookup"><span data-stu-id="cae32-178">System.Diagnostics.DiagnosticSource</span></span>](/dotnet/core/api/system.diagnostics.diagnosticsource) | <span data-ttu-id="cae32-179">Singleton</span><span class="sxs-lookup"><span data-stu-id="cae32-179">Singleton</span></span> |
| [<span data-ttu-id="cae32-180">System.Diagnostics.DiagnosticListener</span><span class="sxs-lookup"><span data-stu-id="cae32-180">System.Diagnostics.DiagnosticListener</span></span>](/dotnet/core/api/system.diagnostics.diagnosticlistener) | <span data-ttu-id="cae32-181">Singleton</span><span class="sxs-lookup"><span data-stu-id="cae32-181">Singleton</span></span> |

<span data-ttu-id="cae32-182">Pokud je dostupná rozšiřující metoda rozšiřující kolekci služeb o registraci služby (případě jejích závislých služeb, je-li to požadováno), je zvykem použít jedinou rozšiřující metodu `Add{SERVICE_NAME}` k registraci všech služeb vyžadovaných danou službu.</span><span class="sxs-lookup"><span data-stu-id="cae32-182">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="cae32-183">Následující kód je příkladem toho, jak přidat dodatečné služby do kontejneru pomocí rozšiřujících metod [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity), a [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):</span><span class="sxs-lookup"><span data-stu-id="cae32-183">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity), and [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):</span></span>

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

<span data-ttu-id="cae32-184">Další informace naleznete v tématu [Třída ServiceCollection](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="cae32-184">For more information, see the [ServiceCollection Class](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="cae32-185">Životnost služby</span><span class="sxs-lookup"><span data-stu-id="cae32-185">Service lifetimes</span></span>

<span data-ttu-id="cae32-186">Zvolte odpovídající životnost pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="cae32-186">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="cae32-187">Služby ASP.NET Core můžete nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="cae32-187">ASP.NET Core services can be configured with the following lifetimes:</span></span>

**<span data-ttu-id="cae32-188">Přechodná</span><span class="sxs-lookup"><span data-stu-id="cae32-188">Transient</span></span>**

<span data-ttu-id="cae32-189">Pokaždé, když jste vyžádaný z kontejneru služby jsou vytvořeny přechodné životnosti služby.</span><span class="sxs-lookup"><span data-stu-id="cae32-189">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="cae32-190">Tato životnost je vhodná pro jednoduché, bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="cae32-190">This lifetime works best for lightweight, stateless services.</span></span>

**<span data-ttu-id="cae32-191">Obor</span><span class="sxs-lookup"><span data-stu-id="cae32-191">Scoped</span></span>**

<span data-ttu-id="cae32-192">S vymezeným oborem životnost služby se vytvoří jednou za žádost klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="cae32-192">Scoped lifetime services are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="cae32-193">Při použití služby s vymezenou živostností v middlewaru vkládejte službu do metody `Invoke` nebo `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="cae32-193">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="cae32-194">Nevkládejte službu prostřednictvím konstruktoru, protože se služba bude chovat se jako singleton.</span><span class="sxs-lookup"><span data-stu-id="cae32-194">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="cae32-195">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="cae32-195">For more information, see <xref:fundamentals/middleware/index>.</span></span>

**<span data-ttu-id="cae32-196">Singleton</span><span class="sxs-lookup"><span data-stu-id="cae32-196">Singleton</span></span>**

<span data-ttu-id="cae32-197">Služby se životností typu singleton se vytvoří při prvním vyžádání služby (nebo když je spuštěna metoda `ConfigureServices` a instance je specifikována při registraci služby).</span><span class="sxs-lookup"><span data-stu-id="cae32-197">Singleton lifetime services are created the first time they're requested (or when `ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="cae32-198">Každý další požadavek použije stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="cae32-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="cae32-199">Pokud je v aplikaci vyžadováno chování ve stylu návrhového vzoru Singleton, doporučuje se použít kontejner služeb ke správě životnosti takového objektu.</span><span class="sxs-lookup"><span data-stu-id="cae32-199">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="cae32-200">Neposkytujte vlastní implementaci návrhového vzoru Singleton a umožněte tak uživatelskému kódu spravovat životnost objektu v rámci třídy.</span><span class="sxs-lookup"><span data-stu-id="cae32-200">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="cae32-201">Je nebezpečné získávat vymezené služby ze singletonů.</span><span class="sxs-lookup"><span data-stu-id="cae32-201">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="cae32-202">Může to způsobit nesprávný stav služby při zpracování následujících požadavků.</span><span class="sxs-lookup"><span data-stu-id="cae32-202">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="cae32-203">Chování vkládání pomocí konstruktoru</span><span class="sxs-lookup"><span data-stu-id="cae32-203">Constructor injection behavior</span></span>

<span data-ttu-id="cae32-204">Služby mohou být řešeny pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="cae32-204">Services can be resolved by two mechanisms:</span></span>

* `IServiceProvider`
* <span data-ttu-id="cae32-205">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; umožňuje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="cae32-205">[ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; Permits object creation without service registration in the dependency injection container.</span></span> `ActivatorUtilities` <span data-ttu-id="cae32-206">se používá s přístupných abstrakce, jako je například pomocných rutin značek, kontrolery MVC a vazače modelů.</span><span class="sxs-lookup"><span data-stu-id="cae32-206">is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="cae32-207">Konstruktory mohou přijímat argumenty, které nejsou poskytovány v rámci vkládání závislostí, ale takové argumenty musí mít přiřazené výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="cae32-207">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="cae32-208">Pokud jsou služby řešeny pomocí `IServiceProvider` nebo `ActivatorUtilities`, pak je vyžadován *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="cae32-208">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="cae32-209">Když jsou vyřešeny služby `ActivatorUtilities`, vkládání konstruktor vyžaduje, že pouze jeden použít konstruktor existuje.</span><span class="sxs-lookup"><span data-stu-id="cae32-209">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="cae32-210">Přetížení konstruktoru je podporováno, ale může existovat pouze jedno přetížením, jehož argumenty jsou splnitelné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="cae32-210">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="cae32-211">Kontexty Entity Frameworku</span><span class="sxs-lookup"><span data-stu-id="cae32-211">Entity Framework contexts</span></span>

<span data-ttu-id="cae32-212">Entity Framework kontexty jsou obvykle přidány do služby kontejneru pomocí [s vymezeným oborem životnost](#service-lifetimes) protože operací databáze webové aplikace jsou obvykle vymezené požadavku klienta.</span><span class="sxs-lookup"><span data-stu-id="cae32-212">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="cae32-213">Výchozí doba života má obor, pokud se nezadá životnost <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> přetížení při registraci kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="cae32-213">The default lifetime is scoped if a lifetime isn't specified by an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> overload when registering the database context.</span></span> <span data-ttu-id="cae32-214">Služby danou dobu života neměli používat kontext databáze s životností kratší než služba.</span><span class="sxs-lookup"><span data-stu-id="cae32-214">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="cae32-215">Životnosti a možnosti registrace</span><span class="sxs-lookup"><span data-stu-id="cae32-215">Lifetime and registration options</span></span>

<span data-ttu-id="cae32-216">Na ukázku rozdílů mezi životnostmi a možnostmi registrace si prohlédněte následující rozhraní reprezentující úlohy jako operace s jedinečným identifikátorem `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="cae32-216">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="cae32-217">V závislosti na tom, jak je životnost této služby nakonfigurována pro následující rozhraní, poskytne kontejner stejnou nebo rozdílnou instanci služby během vyžádání třídou:</span><span class="sxs-lookup"><span data-stu-id="cae32-217">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="cae32-218">Rozhraní jsou implementovány ve třídě `Operation`.</span><span class="sxs-lookup"><span data-stu-id="cae32-218">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="cae32-219">Konstruktor `Operation` vytvoří identifikátor GUID, pokud není explicitně poskytnut:</span><span class="sxs-lookup"><span data-stu-id="cae32-219">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="cae32-220">Služba `OperationService` je zaregistrována tak, aby závisela na jednotlivých typech tříd `Operation`.</span><span class="sxs-lookup"><span data-stu-id="cae32-220">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="cae32-221">Když je `OperationService` vyžádána pomocí vkládání závislostí, obdrží buď novou nebo stávající instanci třídy jednotlivých služeb v závislosti na životnosti závislých služeb.</span><span class="sxs-lookup"><span data-stu-id="cae32-221">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="cae32-222">Při vytváření přechodné služby na požádání v kontejneru `OperationId` z `IOperationTransient` služby se liší od `OperationId` z `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="cae32-222">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> `OperationService` <span data-ttu-id="cae32-223">obdrží novou instanci třídy `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="cae32-223">receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="cae32-224">Nová instance implikuje rozdílné `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="cae32-224">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="cae32-225">Při vytváření služby s vymezeným oborem každý požadavek klienta `OperationId` z `IOperationScoped` služba je stejné jako u `OperationService` v požadavku klienta.</span><span class="sxs-lookup"><span data-stu-id="cae32-225">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="cae32-226">Mezi požadavky klientů, obě služby sdílené složky jiný `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="cae32-226">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="cae32-227">Když jsou služby typu singleton a instanci typu singleton vytvořit jednou a použít v rámci všech požadavků klientů a všemi službami, `OperationId` je konstantní napříč všemi požadavky služby.</span><span class="sxs-lookup"><span data-stu-id="cae32-227">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="cae32-228">V `Startup.ConfigureServices` je každý typ přidán do kontejneru na základě svého pojmenování podle životnosti:</span><span class="sxs-lookup"><span data-stu-id="cae32-228">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="cae32-229">Služba `IOperationSingletonInstance` používá konkrétní instanci se známým ID nastaveným na `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="cae32-229">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="cae32-230">Je pak zřejmé, kdy je tento typ použit (jeho identifikátor GUID obsahuje samé nuly).</span><span class="sxs-lookup"><span data-stu-id="cae32-230">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="cae32-231">Ukázková aplikace demonstruje živostnosti objektů v rámci jednotlivých požadavků a mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="cae32-231">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="cae32-232">Třída `IndexModel` ukázkové aplikace vyžaduje každý druh typu `IOperation` a službu `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="cae32-232">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="cae32-233">Stránka následně vypisuje všechny hodnoty `OperationId` modelu stránky a služby prostřednictvím přiřazených vlastností:</span><span class="sxs-lookup"><span data-stu-id="cae32-233">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="cae32-234">Následující výpis ukazuje výsledek dvou HTTP požadavků:</span><span class="sxs-lookup"><span data-stu-id="cae32-234">Two following output shows the results of two requests:</span></span>

**<span data-ttu-id="cae32-235">První požadavek:</span><span class="sxs-lookup"><span data-stu-id="cae32-235">First request:</span></span>**

<span data-ttu-id="cae32-236">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="cae32-236">Controller operations:</span></span>

<span data-ttu-id="cae32-237">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="cae32-237">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="cae32-238">Nastavit rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="cae32-238">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="cae32-239">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cae32-239">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cae32-240">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cae32-240">Instance: 00000000-0000-0000-0000-000000000000</span></span>

`OperationService` <span data-ttu-id="cae32-241">operace:</span><span class="sxs-lookup"><span data-stu-id="cae32-241">operations:</span></span>

<span data-ttu-id="cae32-242">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="cae32-242">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="cae32-243">Nastavit rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="cae32-243">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="cae32-244">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cae32-244">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cae32-245">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cae32-245">Instance: 00000000-0000-0000-0000-000000000000</span></span>

**<span data-ttu-id="cae32-246">Druhou žádost:</span><span class="sxs-lookup"><span data-stu-id="cae32-246">Second request:</span></span>**

<span data-ttu-id="cae32-247">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="cae32-247">Controller operations:</span></span>

<span data-ttu-id="cae32-248">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="cae32-248">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="cae32-249">Nastavit rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="cae32-249">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="cae32-250">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cae32-250">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cae32-251">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cae32-251">Instance: 00000000-0000-0000-0000-000000000000</span></span>

`OperationService` <span data-ttu-id="cae32-252">operace:</span><span class="sxs-lookup"><span data-stu-id="cae32-252">operations:</span></span>

<span data-ttu-id="cae32-253">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="cae32-253">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="cae32-254">Nastavit rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="cae32-254">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="cae32-255">Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cae32-255">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cae32-256">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cae32-256">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cae32-257">Všimněte si, které hodnoty `OperationId` se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="cae32-257">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="cae32-258">Objekty s *přechodnou* životností jsou vždy rozdílné.</span><span class="sxs-lookup"><span data-stu-id="cae32-258">*Transient* objects are always different.</span></span> <span data-ttu-id="cae32-259">Přechodná `OperationId` hodnotu prvního a druhého klienta požadavky se liší pro obě `OperationService` operace napříč požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="cae32-259">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="cae32-260">Novou instanci se poskytuje pro každou žádost o službu a požadavek klienta.</span><span class="sxs-lookup"><span data-stu-id="cae32-260">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="cae32-261">*Obor* objekty jsou stejné v požadavku klienta, ale jiné napříč požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="cae32-261">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="cae32-262">Objekty se živostností typu *singleton jsou* stejné pro všechny objekty a všechny požadavky bez ohledu na to, jestli je instance `Operation` poskytnuta v `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cae32-262">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="cae32-263">Volání služeb z main</span><span class="sxs-lookup"><span data-stu-id="cae32-263">Call services from main</span></span>

<span data-ttu-id="cae32-264">Vytvořte [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) obsahující metodu [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) k rozhodování služeb s životností vymezenou v rámci oboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="cae32-264">Create an [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) with [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="cae32-265">Tento způsob je užitečný pro přístup k službám s vymezenou životností při provádění inicializačních úloh během spuštění.</span><span class="sxs-lookup"><span data-stu-id="cae32-265">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="cae32-266">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="cae32-266">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="cae32-267">Ověření rámce životnosti</span><span class="sxs-lookup"><span data-stu-id="cae32-267">Scope validation</span></span>

<span data-ttu-id="cae32-268">Pokud aplikace běží ve vývojovém prostředí, výchozí poskytovatel služeb provádí kontroly pro ověření toho, že:</span><span class="sxs-lookup"><span data-stu-id="cae32-268">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="cae32-269">Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="cae32-269">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="cae32-270">Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.</span><span class="sxs-lookup"><span data-stu-id="cae32-270">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="cae32-271">Kořenový poskytovatel služeb je vytvořen při volání [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider).</span><span class="sxs-lookup"><span data-stu-id="cae32-271">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="cae32-272">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="cae32-272">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="cae32-273">Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="cae32-273">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="cae32-274">Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace.</span><span class="sxs-lookup"><span data-stu-id="cae32-274">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="cae32-275">Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="cae32-275">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="cae32-276">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="cae32-276">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="cae32-277">Služby požadavků</span><span class="sxs-lookup"><span data-stu-id="cae32-277">Request Services</span></span>

<span data-ttu-id="cae32-278">Služby dostupné v rámci požadavků ASP.NET Core z `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices).</span><span class="sxs-lookup"><span data-stu-id="cae32-278">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices) collection.</span></span>

<span data-ttu-id="cae32-279">Služby požadavků představují služby nakonfigurované a požadováné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="cae32-279">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="cae32-280">Pokud objekty specifikují závislosti, jsou řešeny pomocí typů dostupných v `RequestServices`, nikoli `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="cae32-280">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="cae32-281">Aplikace by obvykle neměly používat tyto vlastnosti přímo.</span><span class="sxs-lookup"><span data-stu-id="cae32-281">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="cae32-282">Místo toho získávejte požadované typy pomocí konstruktoru třídy a nechte framework vložit odpovídající závislosti.</span><span class="sxs-lookup"><span data-stu-id="cae32-282">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="cae32-283">To poskytuje třídy, které usnadňuje testování.</span><span class="sxs-lookup"><span data-stu-id="cae32-283">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="cae32-284">Pro přístup ke kolekci `RequestServices` upřednostněte získávání závislostí jako parametr konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="cae32-284">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="cae32-285">Návrh služeb pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="cae32-285">Design services for dependency injection</span></span>

<span data-ttu-id="cae32-286">Osvědčené postupy jsou následující:</span><span class="sxs-lookup"><span data-stu-id="cae32-286">Best practices are to:</span></span>

* <span data-ttu-id="cae32-287">Navrhujte služby tak, aby využívaly vkládání závislostí pro získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="cae32-287">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="cae32-288">Vyhněte se volání stavová a statické metody.</span><span class="sxs-lookup"><span data-stu-id="cae32-288">Avoid stateful, static method calls.</span></span>
* <span data-ttu-id="cae32-289">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="cae32-289">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="cae32-290">Přímé vytváření instancí způsobuje přímou závislost na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="cae32-290">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="cae32-291">Malé, skvěle a snadno otestované, vytvořit třídy aplikace.</span><span class="sxs-lookup"><span data-stu-id="cae32-291">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="cae32-292">Pokud má třída nepřiměřeně mnoho vložených závislostí, je to obecně znakem toho, že má třída příliš mnoho zodpovědností a porušuje tak [zásadu jediné zodpovědnosti (Single Responsibility Principle, SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="cae32-292">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="cae32-293">V tom případě se pokuste refaktorovat třídu tak, že některé z jeho zodpovědností přesunete do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="cae32-293">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="cae32-294">Mějte na paměti, že třídy modelů stránek Razor a třídy kontrolerů MVC by měly být zaměřeny na aspekty uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cae32-294">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="cae32-295">Business pravidla a konkrétní implementace přístupu k datům by měly být v odpovídajících třídách respektující [princip oddělení zopovědnosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="cae32-295">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="cae32-296">Uvolnění služeb</span><span class="sxs-lookup"><span data-stu-id="cae32-296">Disposal of services</span></span>

<span data-ttu-id="cae32-297">Kontejner volá metodu `Dispose` u všech typů, které vytvořil a které implementují rozhraní `IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="cae32-297">The container calls `Dispose` for the `IDisposable` types it creates.</span></span> <span data-ttu-id="cae32-298">Instance přidané do kontejneru pomocí uživatelské kódu nejsou automaticky uvolňovány.</span><span class="sxs-lookup"><span data-stu-id="cae32-298">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

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

## <a name="default-service-container-replacement"></a><span data-ttu-id="cae32-299">Nahrazení výchozího kontejneru služeb</span><span class="sxs-lookup"><span data-stu-id="cae32-299">Default service container replacement</span></span>

<span data-ttu-id="cae32-300">Integrovaný kontejner služeb je primárně určen pro naplnění potřeb frameworku a většiny uživatelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="cae32-300">The built-in service container is meant to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="cae32-301">Doporučujeme používat integrovaný kontejner, dokud nebudete potřebovat specifické funkce nepodporované kontejnerem.</span><span class="sxs-lookup"><span data-stu-id="cae32-301">We recommend using the built-in container unless you need a specific feature that it doesn't support.</span></span> <span data-ttu-id="cae32-302">Některé z funkcí podporovaných v kontejnerech 3. stran neobsažených ve výchozím kontejneru jsou:</span><span class="sxs-lookup"><span data-stu-id="cae32-302">Some of the features supported in 3rd party containers not found in the built-in container:</span></span>

* <span data-ttu-id="cae32-303">Vkládání pomocí vlastností</span><span class="sxs-lookup"><span data-stu-id="cae32-303">Property injection</span></span>
* <span data-ttu-id="cae32-304">Vkládání podle názvu</span><span class="sxs-lookup"><span data-stu-id="cae32-304">Injection based on name</span></span>
* <span data-ttu-id="cae32-305">Vnořené kontejnery</span><span class="sxs-lookup"><span data-stu-id="cae32-305">Child containers</span></span>
* <span data-ttu-id="cae32-306">Vlastní správa životnosti</span><span class="sxs-lookup"><span data-stu-id="cae32-306">Custom lifetime management</span></span>
* `Func<T>` <span data-ttu-id="cae32-307">Podpora pro opožděná inicializace</span><span class="sxs-lookup"><span data-stu-id="cae32-307">support for lazy initialization</span></span>

<span data-ttu-id="cae32-308">Pro seznam některých kontejnerů podporujících adaptéry, vizte [Soubor readme.md ke Vkládání závislostí](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection).</span><span class="sxs-lookup"><span data-stu-id="cae32-308">See the [Dependency Injection readme.md file](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection) for a list of some of the containers that support adapters.</span></span>

<span data-ttu-id="cae32-309">Následující příklad nahrazuje integrovaný kontejner kontejnerem [Autofac](https://autofac.org/):</span><span class="sxs-lookup"><span data-stu-id="cae32-309">The following sample replaces the built-in container with [Autofac](https://autofac.org/):</span></span>

* <span data-ttu-id="cae32-310">Nainstalujte odpovídající balíčky kontejneru:</span><span class="sxs-lookup"><span data-stu-id="cae32-310">Install the appropriate container package(s):</span></span>

  * [<span data-ttu-id="cae32-311">Autofac</span><span class="sxs-lookup"><span data-stu-id="cae32-311">Autofac</span></span>](https://www.nuget.org/packages/Autofac/)
  * [<span data-ttu-id="cae32-312">Autofac.Extensions.DependencyInjection</span><span class="sxs-lookup"><span data-stu-id="cae32-312">Autofac.Extensions.DependencyInjection</span></span>](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* <span data-ttu-id="cae32-313">Nakonfigurujte kontejner v `Startup.ConfigureServices` a vraťte `IServiceProvider`:</span><span class="sxs-lookup"><span data-stu-id="cae32-313">Configure the container in `Startup.ConfigureServices` and return an `IServiceProvider`:</span></span>

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

    <span data-ttu-id="cae32-314">`Startup.ConfigureServices` musí vracet `IServiceProvider` pro použití kontejneru 3. stran.</span><span class="sxs-lookup"><span data-stu-id="cae32-314">To use a 3rd party container, `Startup.ConfigureServices` must return `IServiceProvider`.</span></span>

* <span data-ttu-id="cae32-315">Konfigurace Autofacu v `DefaultModule`:</span><span class="sxs-lookup"><span data-stu-id="cae32-315">Configure Autofac in `DefaultModule`:</span></span>

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

<span data-ttu-id="cae32-316">Za běhu je použit Autofac pro rozhodování typů a vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="cae32-316">At runtime, Autofac is used to resolve types and inject dependencies.</span></span> <span data-ttu-id="cae32-317">Další informace o používání Autofac s ASP.NET Core naleznete v [dokumentaci Autofac](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span><span class="sxs-lookup"><span data-stu-id="cae32-317">To learn more about using Autofac with ASP.NET Core, see the [Autofac documentation](https://docs.autofac.org/en/latest/integration/aspnetcore.html).</span></span>

### <a name="thread-safety"></a><span data-ttu-id="cae32-318">Bezpečný přístup z více vláken</span><span class="sxs-lookup"><span data-stu-id="cae32-318">Thread safety</span></span>

<span data-ttu-id="cae32-319">Vytvoření deklarace služeb typu singleton bezpečné pro vlákna.</span><span class="sxs-lookup"><span data-stu-id="cae32-319">Create thread-safe singleton services.</span></span> <span data-ttu-id="cae32-320">Pokud služby typu singleton obsahuje závislost na přechodné služby, přechodné služba může také požadovat bezpečnost vlákna v závislosti, jak se používají v typu singleton.</span><span class="sxs-lookup"><span data-stu-id="cae32-320">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="cae32-321">Factory metody jedné služby, jako je například druhý argument metody [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider, TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), nebude musí být bezpečné pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="cae32-321">The factory method of single service, such as the second argument to [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), doesn't need to be thread-safe.</span></span> <span data-ttu-id="cae32-322">Stejně jako u typového (`static`) konstruktoru je zaručeno, že je volán jednou jediným vláknem.</span><span class="sxs-lookup"><span data-stu-id="cae32-322">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="cae32-323">Doporučení</span><span class="sxs-lookup"><span data-stu-id="cae32-323">Recommendations</span></span>

* `async/await` <span data-ttu-id="cae32-324">a `Task` závislosti služby rozlišení není podporováno.</span><span class="sxs-lookup"><span data-stu-id="cae32-324">and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="cae32-325">C#nepodporuje asynchronní konstruktory; doporučený model je proto používání asynchronních metod po synchronně překladu služby.</span><span class="sxs-lookup"><span data-stu-id="cae32-325">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="cae32-326">Vyhněte se ukládání dat a konfigurace přímo do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="cae32-326">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="cae32-327">Například nákupní košík uživatele by neměl být přidáván do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="cae32-327">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="cae32-328">Konfigurace by měla používat [vzor možností (options pattern)](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="cae32-328">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="cae32-329">Podobně se vystříhejte použití objektů "držící data", jejichž jediným účelem je poskytnutí přístupu k některému jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="cae32-329">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="cae32-330">Je lepší požádat o skutečné položky prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="cae32-330">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="cae32-331">Vyhněte se statickému přístupu ke službám (například staticky typovaným [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="cae32-331">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) for use elsewhere).</span></span>

* <span data-ttu-id="cae32-332">Vyhněte se použití *služby lokátoru vzor*.</span><span class="sxs-lookup"><span data-stu-id="cae32-332">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="cae32-333">Není třeba vyvolat <xref:System.IServiceProvider.GetService*> při DI místo toho můžete získat instanci služby:</span><span class="sxs-lookup"><span data-stu-id="cae32-333">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  **<span data-ttu-id="cae32-334">Nesprávný:</span><span class="sxs-lookup"><span data-stu-id="cae32-334">Incorrect:</span></span>**

  ```csharp
  public void MyMethod()
  {
      var options = 
          _services.GetService<IOptionsMonitor<MyOptions>>();
      var option = options.CurrentValue.Option;

      ...
  }
  ```

  <span data-ttu-id="cae32-335">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="cae32-335">**Correct**:</span></span>

  ```csharp
  private readonly MyOptions _options;

  public MyClass(IOptionsMonitor<MyOptions> options)
  {
      _options = options.CurrentValue;
  }

  public void MyMethod()
  {
      var option = _options.Option;

      ...
  }
  ```

* <span data-ttu-id="cae32-336">Další variantou Lokátor služby, aby se vkládá objekt factory, který řeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="cae32-336">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="cae32-337">Obě tyto postupy kombinace [ovládacího prvku inverzi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategie.</span><span class="sxs-lookup"><span data-stu-id="cae32-337">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="cae32-338">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).</span><span class="sxs-lookup"><span data-stu-id="cae32-338">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).</span></span>

<span data-ttu-id="cae32-339">Stejně jako u všech doporučení mohou nastat situace, ve kterých je možné tato doporučení ignorovat.</span><span class="sxs-lookup"><span data-stu-id="cae32-339">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="cae32-340">Výjimky se vyskytují jen vzácně &mdash; nejčastěji jsou to speciální případy uvnitř frameworku samotného.</span><span class="sxs-lookup"><span data-stu-id="cae32-340">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="cae32-341">DI je *alternativní* na vzorech přístupu statická/globální objekt.</span><span class="sxs-lookup"><span data-stu-id="cae32-341">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="cae32-342">Nebudete moci využít výhod DI, jsou-li zkombinovány s přístupem statický objekt.</span><span class="sxs-lookup"><span data-stu-id="cae32-342">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cae32-343">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cae32-343">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="cae32-344">Zápis čistý kód v ASP.NET Core s injektáž závislostí (MSDN)</span><span class="sxs-lookup"><span data-stu-id="cae32-344">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="cae32-345">Princip explicitní závislosti.</span><span class="sxs-lookup"><span data-stu-id="cae32-345">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="cae32-346">Inverze – kontejnery ovládacích prvků a vzor injektáž závislostí (Martina Fowlera)</span><span class="sxs-lookup"><span data-stu-id="cae32-346">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="cae32-347">Postup při registraci služby s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="cae32-347">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

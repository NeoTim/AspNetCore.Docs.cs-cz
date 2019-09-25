---
title: Vkládání závislostí v ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak se používá.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/24/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: fefd0b9df71d5b0e7c30a31620292fd37eeecfa4
ms.sourcegitcommit: e54672f5c493258dc449fac5b98faf47eb123b28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71248266"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="0d9ab-103">Vkládání závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d9ab-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="0d9ab-104">[Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0d9ab-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0d9ab-105">ASP.NET Core podporuje návrhový vzor vkládání závislostí (Dependency Injection, DI), což je technika používaná pro dosažení [inverze řízení (Inversion of Control, IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0d9ab-106">Další informace, které jsou specifické pro vkládání závislostí v rámci <xref:mvc/controllers/dependency-injection>řadičů MVC, najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0d9ab-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0d9ab-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0d9ab-108">Přehled vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="0d9ab-108">Overview of dependency injection</span></span>

<span data-ttu-id="0d9ab-109">*Závislost* je libovolný objekt, který je vyžadován jiným objektem.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0d9ab-110">Prohlédněte si následující třídu `MyDependency` s metodou `WriteMessage`, na které závisí jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0d9ab-111">Pro zpřístupnění metody `WriteMessage` v jiné třídě je možné vytvořit instanci třídy `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0d9ab-112">Třída `MyDependency` je závislost třídy `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0d9ab-113">Třída vytváří instanci třídy `MyDependency`, na které přímo závisí.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0d9ab-114">Přímé závislosti v kódu (jako ta v předchozím příkladu) jsou problematické a měli byste se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0d9ab-115">Chcete-li nahradit `MyDependency` jinou implementací, musí být modifikována třída, která na ní závisí.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0d9ab-116">Pokud má třída `MyDependency` sama závislosti, musí být taktéž nakonfigurovány v dané třídě.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0d9ab-117">Ve velkých projektech s více třídami závislých na `MyDependency` je tak konfigurační kód roztroušen na různých místech aplikace.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0d9ab-118">Tento způsob implementace je obtížný na jednotkové testování.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0d9ab-119">Aplikace by měla používat mock nebo stub třídy `MyDependency`, což však není možné s tímto přístupem.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0d9ab-120">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0d9ab-121">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0d9ab-122">Registrace závislosti v kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0d9ab-123">ASP.NET Core poskytuje integrovaný kontejner <xref:System.IServiceProvider>služeb.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0d9ab-124">Služby jsou registrované v metodě `Startup.ConfigureServices` aplikace.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0d9ab-125">*Vkládání* služeb do konstruktoru třídy, ve které se používá.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0d9ab-126">Framework přebírá zodpovědnost za vytváření instancí závislostí a jejich uvolňování, kdy již nejsou dále potřebné.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0d9ab-127">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definuje rozhraní `IMyDependency` metody, které poskytuje služba aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-127">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0d9ab-128">Toto rozhraní je implementováno konkrétním typem `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0d9ab-129">`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0d9ab-130">Není neobvyklé používat zřetězené vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0d9ab-131">Každá požadovaná závislost může v zápětí požadovat své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0d9ab-132">Kontejner řeší závislosti v grafu a vrací plně vyřešené služby.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0d9ab-133">Množina závislostí, které musí být rozhodnuty, se obvykle označuje jako *strom závislostí*, *graf závislostí*, nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="0d9ab-134">`IMyDependency` a `ILogger<TCategoryName>` musí být zaregistrovány v kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0d9ab-135">`IMyDependency`je zaregistrován v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0d9ab-136">`ILogger<TCategoryName>` je registrován infrastrukturou pro abstrakci protokolování, takže je [službou poskytovanou frameworkem](#framework-provided-services) registrovanou ve výchozím nastavení frameworkem.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0d9ab-137">Kontejner řeší `ILogger<TCategoryName>` s využitím [(generických) otevřených typů](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), čímž eliminuje nutnost registrovat každý [(generický) konstruovaný typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="0d9ab-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

<span data-ttu-id="0d9ab-138">V ukázkové aplikaci je služba `IMyDependency` registrována s konkrétním typem `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0d9ab-139">Registrace specifikuje rámec životnosti služby na životnost jednoho požadavku.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0d9ab-140">[Životnosti služby](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="0d9ab-141">Každá rozšiřující metoda `services.Add{SERVICE_NAME}` přidává (a potenciálně konfiguruje) služby.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0d9ab-142">Například `services.AddMvc()` přidává služby Stránek Razor a MVC.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0d9ab-143">Doporučujeme, aby v aplikacích byla tato konvence dodržena.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0d9ab-144">Umístěte rozšiřující metody do jmenného prostoru [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pro zapouzdření skupin registrací služeb.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0d9ab-145">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string`,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="0d9ab-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0d9ab-146">Instance služby je požadována prostřednictvím konstruktoru třídy, kde se služba využívá a přiřazuje do privátního pole.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0d9ab-147">Toto pole se používá pro přístup ke službě podle potřeby v rámci celé třídy.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0d9ab-148">V ukázkové aplikaci je požadována instance `IMyDependency` a posléze použita k volání metody `WriteMessage` dané služby:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

## <a name="services-injected-into-startup"></a><span data-ttu-id="0d9ab-149">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="0d9ab-149">Services injected into Startup</span></span>

<span data-ttu-id="0d9ab-150">Při použití obecného hostitele ( `Startup` <xref:Microsoft.Extensions.Hosting.IHostBuilder>) mohou být do konstruktoru vloženy pouze následující typy služeb:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0d9ab-151">Služby je možné vložit do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0d9ab-152">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0d9ab-153">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="0d9ab-153">Framework-provided services</span></span>

<span data-ttu-id="0d9ab-154">`Startup.ConfigureServices` Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0d9ab-155">Zpočátku služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný.](xref:fundamentals/index#host) `IServiceCollection` `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="0d9ab-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0d9ab-156">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0d9ab-157">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-157">A small sample of framework-registered services is listed in the following table.</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="0d9ab-158">Typ služby</span><span class="sxs-lookup"><span data-stu-id="0d9ab-158">Service Type</span></span> | <span data-ttu-id="0d9ab-159">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="0d9ab-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0d9ab-160">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0d9ab-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="0d9ab-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="0d9ab-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0d9ab-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0d9ab-164">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0d9ab-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0d9ab-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0d9ab-166">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0d9ab-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0d9ab-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0d9ab-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0d9ab-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0d9ab-170">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0d9ab-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0d9ab-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0d9ab-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0d9ab-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-173">Singleton</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="0d9ab-174">Typ služby</span><span class="sxs-lookup"><span data-stu-id="0d9ab-174">Service Type</span></span> | <span data-ttu-id="0d9ab-175">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="0d9ab-175">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0d9ab-176">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0d9ab-176">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="0d9ab-177">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-177">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="0d9ab-178">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-178">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0d9ab-179">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-179">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0d9ab-180">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0d9ab-180">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0d9ab-181">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-181">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0d9ab-182">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0d9ab-182">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0d9ab-183">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-183">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0d9ab-184">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-184">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0d9ab-185">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-185">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0d9ab-186">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0d9ab-186">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0d9ab-187">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-187">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0d9ab-188">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-188">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0d9ab-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-189">Singleton</span></span> |

::: moniker-end

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0d9ab-190">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="0d9ab-190">Register additional services with extension methods</span></span>

<span data-ttu-id="0d9ab-191">Pokud je dostupná rozšiřující metoda rozšiřující kolekci služeb o registraci služby (případě jejích závislých služeb, je-li to požadováno), je zvykem použít jedinou rozšiřující metodu `Add{SERVICE_NAME}` k registraci všech služeb vyžadovaných danou službu.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-191">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0d9ab-192">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [\<AddDbContext TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a: <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*></span><span class="sxs-lookup"><span data-stu-id="0d9ab-192">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="0d9ab-193">Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-193">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0d9ab-194">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="0d9ab-194">Service lifetimes</span></span>

<span data-ttu-id="0d9ab-195">Zvolte odpovídající životnost pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-195">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0d9ab-196">Služby ASP.NET Core můžete nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-196">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0d9ab-197">Přechodná</span><span class="sxs-lookup"><span data-stu-id="0d9ab-197">Transient</span></span>

<span data-ttu-id="0d9ab-198">Služba přechodných životností (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-198">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0d9ab-199">Tato životnost je vhodná pro jednoduché, bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-199">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="0d9ab-200">Obor</span><span class="sxs-lookup"><span data-stu-id="0d9ab-200">Scoped</span></span>

<span data-ttu-id="0d9ab-201">Oborové služby ()<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>s rozsahem platnosti () se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="0d9ab-201">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="0d9ab-202">Při použití služby s vymezenou živostností v middlewaru vkládejte službu do metody `Invoke` nebo `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-202">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0d9ab-203">Nevkládejte službu prostřednictvím konstruktoru, protože se služba bude chovat se jako singleton.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-203">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0d9ab-204">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-204">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0d9ab-205">Singleton</span><span class="sxs-lookup"><span data-stu-id="0d9ab-205">Singleton</span></span>

<span data-ttu-id="0d9ab-206">Služba singleton životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>() je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby).</span><span class="sxs-lookup"><span data-stu-id="0d9ab-206">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0d9ab-207">Každý další požadavek použije stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-207">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0d9ab-208">Pokud je v aplikaci vyžadováno chování ve stylu návrhového vzoru Singleton, doporučuje se použít kontejner služeb ke správě životnosti takového objektu.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-208">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0d9ab-209">Neposkytujte vlastní implementaci návrhového vzoru Singleton a umožněte tak uživatelskému kódu spravovat životnost objektu v rámci třídy.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-209">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="0d9ab-210">Je nebezpečné získávat vymezené služby ze singletonů.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-210">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0d9ab-211">Může to způsobit nesprávný stav služby při zpracování následujících požadavků.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0d9ab-212">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="0d9ab-212">Service registration methods</span></span>

<span data-ttu-id="0d9ab-213">Každá metoda rozšíření registrace služby nabízí přetížení, které jsou užitečné v konkrétních scénářích.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-213">Each service registration extension method offers overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0d9ab-214">Metoda</span><span class="sxs-lookup"><span data-stu-id="0d9ab-214">Method</span></span> | <span data-ttu-id="0d9ab-215">Automatické</span><span class="sxs-lookup"><span data-stu-id="0d9ab-215">Automatic</span></span><br><span data-ttu-id="0d9ab-216">odkazy objektů</span><span class="sxs-lookup"><span data-stu-id="0d9ab-216">object</span></span><br><span data-ttu-id="0d9ab-217">odvod</span><span class="sxs-lookup"><span data-stu-id="0d9ab-217">disposal</span></span> | <span data-ttu-id="0d9ab-218">Několik</span><span class="sxs-lookup"><span data-stu-id="0d9ab-218">Multiple</span></span><br><span data-ttu-id="0d9ab-219">implementace</span><span class="sxs-lookup"><span data-stu-id="0d9ab-219">implementations</span></span> | <span data-ttu-id="0d9ab-220">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="0d9ab-220">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0d9ab-221">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-221">Example:</span></span><br>`services.AddScoped<IMyDep, MyDep>();` | <span data-ttu-id="0d9ab-222">Ano</span><span class="sxs-lookup"><span data-stu-id="0d9ab-222">Yes</span></span> | <span data-ttu-id="0d9ab-223">Ano</span><span class="sxs-lookup"><span data-stu-id="0d9ab-223">Yes</span></span> | <span data-ttu-id="0d9ab-224">Ne</span><span class="sxs-lookup"><span data-stu-id="0d9ab-224">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0d9ab-225">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-225">Examples:</span></span><br>`services.AddScoped<IMyDep>(sp => new MyDep());`<br>`services.AddScoped<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0d9ab-226">Ano</span><span class="sxs-lookup"><span data-stu-id="0d9ab-226">Yes</span></span> | <span data-ttu-id="0d9ab-227">Ano</span><span class="sxs-lookup"><span data-stu-id="0d9ab-227">Yes</span></span> | <span data-ttu-id="0d9ab-228">Ano</span><span class="sxs-lookup"><span data-stu-id="0d9ab-228">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0d9ab-229">Příklad:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-229">Example:</span></span><br>`services.AddScoped<MyDep>();` | <span data-ttu-id="0d9ab-230">Ano</span><span class="sxs-lookup"><span data-stu-id="0d9ab-230">Yes</span></span> | <span data-ttu-id="0d9ab-231">Ne</span><span class="sxs-lookup"><span data-stu-id="0d9ab-231">No</span></span> | <span data-ttu-id="0d9ab-232">Ne</span><span class="sxs-lookup"><span data-stu-id="0d9ab-232">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0d9ab-233">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-233">Examples:</span></span><br>`services.AddScoped<IMyDep>(new MyDep());`<br>`services.AddScoped<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0d9ab-234">Ne</span><span class="sxs-lookup"><span data-stu-id="0d9ab-234">No</span></span> | <span data-ttu-id="0d9ab-235">Ano</span><span class="sxs-lookup"><span data-stu-id="0d9ab-235">Yes</span></span> | <span data-ttu-id="0d9ab-236">Ano</span><span class="sxs-lookup"><span data-stu-id="0d9ab-236">Yes</span></span> |
| `Add{LIFETIME}(new {IMPLEMENTATION})`<br><span data-ttu-id="0d9ab-237">Příklady:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-237">Examples:</span></span><br>`services.AddScoped(new MyDep());`<br>`services.AddScoped(new MyDep("A string!"));` | <span data-ttu-id="0d9ab-238">Ne</span><span class="sxs-lookup"><span data-stu-id="0d9ab-238">No</span></span> | <span data-ttu-id="0d9ab-239">Ne</span><span class="sxs-lookup"><span data-stu-id="0d9ab-239">No</span></span> | <span data-ttu-id="0d9ab-240">Ano</span><span class="sxs-lookup"><span data-stu-id="0d9ab-240">Yes</span></span> |

<span data-ttu-id="0d9ab-241">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="0d9ab-241">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0d9ab-242">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0d9ab-242">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0d9ab-243">`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-243">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0d9ab-244">V následujícím příkladu se první řádek registruje `MyDependency` pro. `IMyDependency`</span><span class="sxs-lookup"><span data-stu-id="0d9ab-244">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="0d9ab-245">Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-245">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="0d9ab-246">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-246">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0d9ab-247">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-247">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0d9ab-248">Několik služeb je vyřešeno `IEnumerable<{SERVICE}>`prostřednictvím.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-248">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0d9ab-249">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-249">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0d9ab-250">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-250">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0d9ab-251">V následujícím příkladu se první řádek registruje `MyDep` pro. `IMyDep1`</span><span class="sxs-lookup"><span data-stu-id="0d9ab-251">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0d9ab-252">Druhý řádek se `MyDep` registruje `IMyDep2`pro.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-252">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0d9ab-253">Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou `MyDep`implementaci:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-253">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0d9ab-254">Chování vkládání pomocí konstruktoru</span><span class="sxs-lookup"><span data-stu-id="0d9ab-254">Constructor injection behavior</span></span>

<span data-ttu-id="0d9ab-255">Služby mohou být řešeny pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-255">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0d9ab-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Povoluje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0d9ab-257">`ActivatorUtilities` se používá s uživatelsky orientovanými abstrakcemi, jako jsou například Tag Helpery, kontrolery MVC a bindery modelů.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-257">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0d9ab-258">Konstruktory mohou přijímat argumenty, které nejsou poskytovány v rámci vkládání závislostí, ale takové argumenty musí mít přiřazené výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-258">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0d9ab-259">Pokud jsou služby řešeny pomocí `IServiceProvider` nebo `ActivatorUtilities`, pak je vyžadován *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-259">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="0d9ab-260">Pokud jsou služby řešeny pomocí `ActivatorUtilities`, konstruktorová injekce vyžaduje, aby existoval pouze jediný použitelný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-260">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0d9ab-261">Přetížení konstruktoru je podporováno, ale může existovat pouze jedno přetížení, jehož argumenty jsou splnitelné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-261">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0d9ab-262">Kontexty Entity Frameworku</span><span class="sxs-lookup"><span data-stu-id="0d9ab-262">Entity Framework contexts</span></span>

<span data-ttu-id="0d9ab-263">Kontexty Entity Frameworku jsou obvykle přidány do kontejneru služeb s [vymezenou životností](#service-lifetimes), protože operace webové aplikace nad databází jsou obvykle vymezeny na požadavek klienta.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-263">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0d9ab-264">Výchozí doba životnosti je vymezená, pokud není specifikována v přetížení metody [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) při registraci databázového kontextu.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-264">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0d9ab-265">Služby s danou životností by neměly používat databázový kontext s životností kratší, než je životnost služby.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-265">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0d9ab-266">Životnosti a možnosti registrace</span><span class="sxs-lookup"><span data-stu-id="0d9ab-266">Lifetime and registration options</span></span>

<span data-ttu-id="0d9ab-267">Na ukázku rozdílů mezi životnostmi a možnostmi registrace si prohlédněte následující rozhraní reprezentující úlohy jako operace s jedinečným identifikátorem `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-267">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0d9ab-268">V závislosti na tom, jak je životnost této služby nakonfigurována pro následující rozhraní, poskytne kontejner stejnou nebo rozdílnou instanci služby během vyžádání třídou:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-268">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0d9ab-269">Rozhraní jsou implementovány ve třídě `Operation`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-269">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0d9ab-270">Konstruktor `Operation` vytvoří identifikátor GUID, pokud není explicitně poskytnut:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-270">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0d9ab-271">Služba `OperationService` je zaregistrována tak, aby závisela na jednotlivých typech tříd `Operation`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-271">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0d9ab-272">Když je `OperationService` vyžádána pomocí vkládání závislostí, obdrží buď novou nebo stávající instanci třídy jednotlivých služeb v závislosti na životnosti závislých služeb.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-272">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0d9ab-273">Když se v případě vyžádání z kontejneru `OperationId` vytvoří přechodné služby, `IOperationTransient` `OperationService`liší `OperationId` se od služby.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-273">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0d9ab-274">`OperationService` obdrží novou instanci třídy `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-274">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0d9ab-275">Nová instance implikuje rozdílné `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-275">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0d9ab-276">Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-276">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0d9ab-277">V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-277">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0d9ab-278">Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, je `OperationId` v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-278">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="0d9ab-279">V `Startup.ConfigureServices` je každý typ přidán do kontejneru na základě svého pojmenování podle životnosti:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-279">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

<span data-ttu-id="0d9ab-280">Služba `IOperationSingletonInstance` používá konkrétní instanci se známým ID nastaveným na `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-280">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0d9ab-281">Je pak zřejmé, kdy je tento typ použit (jeho identifikátor GUID obsahuje samé nuly).</span><span class="sxs-lookup"><span data-stu-id="0d9ab-281">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0d9ab-282">Ukázková aplikace demonstruje živostnosti objektů v rámci jednotlivých požadavků a mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-282">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0d9ab-283">Třída `IndexModel` ukázkové aplikace vyžaduje každý druh typu `IOperation` a službu `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-283">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0d9ab-284">Stránka následně vypisuje všechny hodnoty `OperationId` modelu stránky a služby prostřednictvím přiřazených vlastností:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-284">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

<span data-ttu-id="0d9ab-285">Následující výpis ukazuje výsledek dvou HTTP požadavků:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-285">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0d9ab-286">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="0d9ab-286">**First request:**</span></span>

<span data-ttu-id="0d9ab-287">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-287">Controller operations:</span></span>

<span data-ttu-id="0d9ab-288">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0d9ab-288">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0d9ab-289">Obor 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0d9ab-289">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0d9ab-290">Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d9ab-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d9ab-291">Případě 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d9ab-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d9ab-292">Operace `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-292">`OperationService` operations:</span></span>

<span data-ttu-id="0d9ab-293">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0d9ab-293">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0d9ab-294">Obor 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0d9ab-294">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0d9ab-295">Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d9ab-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d9ab-296">Případě 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d9ab-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d9ab-297">**Druhý požadavek:**</span><span class="sxs-lookup"><span data-stu-id="0d9ab-297">**Second request:**</span></span>

<span data-ttu-id="0d9ab-298">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-298">Controller operations:</span></span>

<span data-ttu-id="0d9ab-299">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0d9ab-299">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0d9ab-300">Obor 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0d9ab-300">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0d9ab-301">Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d9ab-301">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d9ab-302">Případě 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d9ab-302">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d9ab-303">Operace `OperationService`:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-303">`OperationService` operations:</span></span>

<span data-ttu-id="0d9ab-304">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0d9ab-304">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0d9ab-305">Obor 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0d9ab-305">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0d9ab-306">Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0d9ab-306">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0d9ab-307">Případě 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0d9ab-307">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0d9ab-308">Všimněte si, které hodnoty `OperationId` se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-308">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0d9ab-309">Objekty s *přechodnou* životností jsou vždy rozdílné.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-309">*Transient* objects are always different.</span></span> <span data-ttu-id="0d9ab-310">Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-310">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0d9ab-311">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-311">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0d9ab-312">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-312">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0d9ab-313">Objekty se živostností typu *singleton jsou* stejné pro všechny objekty a všechny požadavky bez ohledu na to, jestli je instance `Operation` poskytnuta v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-313">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0d9ab-314">Volání služeb z main</span><span class="sxs-lookup"><span data-stu-id="0d9ab-314">Call services from main</span></span>

<span data-ttu-id="0d9ab-315">Vytvořte pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace. <xref:Microsoft.Extensions.DependencyInjection.IServiceScope></span><span class="sxs-lookup"><span data-stu-id="0d9ab-315">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0d9ab-316">Tento způsob je užitečný pro přístup k službám s vymezenou životností při provádění inicializačních úloh během spuštění.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-316">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0d9ab-317">Následující příklad ukazuje, jak získat kontext pro `MyScopedService`: `Program.Main`</span><span class="sxs-lookup"><span data-stu-id="0d9ab-317">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

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
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
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
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

::: moniker-end

## <a name="scope-validation"></a><span data-ttu-id="0d9ab-318">Ověření rámce životnosti</span><span class="sxs-lookup"><span data-stu-id="0d9ab-318">Scope validation</span></span>

<span data-ttu-id="0d9ab-319">Pokud aplikace běží ve vývojovém prostředí, výchozí poskytovatel služeb provádí kontroly pro ověření toho, že:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-319">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0d9ab-320">Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-320">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0d9ab-321">Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-321">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0d9ab-322">Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-322">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0d9ab-323">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-323">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0d9ab-324">Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-324">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0d9ab-325">Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-325">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0d9ab-326">Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-326">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0d9ab-327">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-327">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="0d9ab-328">Služby požadavků</span><span class="sxs-lookup"><span data-stu-id="0d9ab-328">Request Services</span></span>

<span data-ttu-id="0d9ab-329">Služby dostupné v rámci požadavků ASP.NET Core z `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).</span><span class="sxs-lookup"><span data-stu-id="0d9ab-329">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0d9ab-330">Služby požadavků představují služby nakonfigurované a požadováné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-330">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0d9ab-331">Pokud objekty specifikují závislosti, jsou řešeny pomocí typů dostupných v `RequestServices`, nikoli `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-331">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0d9ab-332">Aplikace by obvykle neměly používat tyto vlastnosti přímo.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-332">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0d9ab-333">Místo toho získávejte požadované typy pomocí konstruktoru třídy a nechte framework vložit odpovídající závislosti.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-333">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="0d9ab-334">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-334">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0d9ab-335">Pro přístup ke kolekci `RequestServices` upřednostněte získávání závislostí jako parametr konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-335">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0d9ab-336">Návrh služeb pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="0d9ab-336">Design services for dependency injection</span></span>

<span data-ttu-id="0d9ab-337">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-337">Best practices are to:</span></span>

* <span data-ttu-id="0d9ab-338">Navrhujte služby tak, aby využívaly vkládání závislostí pro získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-338">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0d9ab-339">Vyhněte se stavovým a statickým voláním metody.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-339">Avoid stateful, static method calls.</span></span>
* <span data-ttu-id="0d9ab-340">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-340">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0d9ab-341">Přímé vytváření instancí způsobuje přímou závislost na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-341">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0d9ab-342">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-342">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0d9ab-343">Pokud má třída nepřiměřeně mnoho vložených závislostí, je to obecně znakem toho, že má třída příliš mnoho zodpovědností a porušuje tak [zásadu jediné zodpovědnosti (Single Responsibility Principle, SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="0d9ab-343">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0d9ab-344">V tom případě se pokuste refaktorovat třídu tak, že některé z jeho zodpovědností přesunete do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-344">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0d9ab-345">Mějte na paměti, že třídy modelů stránek Razor a třídy kontrolerů MVC by měly být zaměřeny na aspekty uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-345">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0d9ab-346">Business pravidla a konkrétní implementace přístupu k datům by měly být v odpovídajících třídách respektující [princip oddělení zopovědnosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="0d9ab-346">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0d9ab-347">Uvolnění služeb</span><span class="sxs-lookup"><span data-stu-id="0d9ab-347">Disposal of services</span></span>

<span data-ttu-id="0d9ab-348">Kontejner volá metodu <xref:System.IDisposable.Dispose*> u všech typů, které vytvořil a které implementují rozhraní <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-348">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0d9ab-349">Instance přidané do kontejneru pomocí uživatelské kódu nejsou automaticky uvolňovány.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-349">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

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

## <a name="default-service-container-replacement"></a><span data-ttu-id="0d9ab-350">Nahrazení výchozího kontejneru služeb</span><span class="sxs-lookup"><span data-stu-id="0d9ab-350">Default service container replacement</span></span>

<span data-ttu-id="0d9ab-351">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-351">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0d9ab-352">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-352">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0d9ab-353">Vkládání pomocí vlastností</span><span class="sxs-lookup"><span data-stu-id="0d9ab-353">Property injection</span></span>
* <span data-ttu-id="0d9ab-354">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="0d9ab-354">Injection based on name</span></span>
* <span data-ttu-id="0d9ab-355">Vnořené kontejnery</span><span class="sxs-lookup"><span data-stu-id="0d9ab-355">Child containers</span></span>
* <span data-ttu-id="0d9ab-356">Vlastní správa životnosti</span><span class="sxs-lookup"><span data-stu-id="0d9ab-356">Custom lifetime management</span></span>
* <span data-ttu-id="0d9ab-357">Podpora `Func<T>` pro línou inicializaci</span><span class="sxs-lookup"><span data-stu-id="0d9ab-357">`Func<T>` support for lazy initialization</span></span>

<span data-ttu-id="0d9ab-358">S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-358">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0d9ab-359">Autofac</span><span class="sxs-lookup"><span data-stu-id="0d9ab-359">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0d9ab-360">DryIoc</span><span class="sxs-lookup"><span data-stu-id="0d9ab-360">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0d9ab-361">Integrit</span><span class="sxs-lookup"><span data-stu-id="0d9ab-361">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0d9ab-362">LightInject</span><span class="sxs-lookup"><span data-stu-id="0d9ab-362">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0d9ab-363">Lamar</span><span class="sxs-lookup"><span data-stu-id="0d9ab-363">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0d9ab-364">Stashbox</span><span class="sxs-lookup"><span data-stu-id="0d9ab-364">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0d9ab-365">Unity</span><span class="sxs-lookup"><span data-stu-id="0d9ab-365">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0d9ab-366">Bezpečný přístup z více vláken</span><span class="sxs-lookup"><span data-stu-id="0d9ab-366">Thread safety</span></span>

<span data-ttu-id="0d9ab-367">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-367">Create thread-safe singleton services.</span></span> <span data-ttu-id="0d9ab-368">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-368">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0d9ab-369">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton\<TService > (\<IServiceCollection, Func IServiceProvider, TService >)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-369">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0d9ab-370">Stejně jako u typového (`static`) konstruktoru je zaručeno, že je volán jednou jediným vláknem.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-370">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0d9ab-371">Doporučení</span><span class="sxs-lookup"><span data-stu-id="0d9ab-371">Recommendations</span></span>

* <span data-ttu-id="0d9ab-372">`async/await`řešení `Task` služeb a se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-372">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0d9ab-373">C#nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-373">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0d9ab-374">Vyhněte se ukládání dat a konfigurace přímo do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-374">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0d9ab-375">Například nákupní košík uživatele by neměl být přidáván do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-375">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0d9ab-376">Konfigurace by měla používat [vzor možností (options pattern)](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="0d9ab-376">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0d9ab-377">Podobně se vystříhejte použití objektů "držící data", jejichž jediným účelem je poskytnutí přístupu k některému jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-377">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0d9ab-378">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-378">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="0d9ab-379">Vyhněte se statickému přístupu ke službám (například staticky typovaným [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="0d9ab-379">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="0d9ab-380">Vyhněte se použití *vzoru lokátoru služby*.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-380">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="0d9ab-381">Například Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-381">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="0d9ab-382">**Špatný**</span><span class="sxs-lookup"><span data-stu-id="0d9ab-382">**Incorrect:**</span></span>

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  <span data-ttu-id="0d9ab-383">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="0d9ab-383">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* <span data-ttu-id="0d9ab-384">Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-384">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="0d9ab-385">Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="0d9ab-385">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="0d9ab-386">Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0d9ab-386">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0d9ab-387">Stejně jako u všech doporučení mohou nastat situace, ve kterých je možné tato doporučení ignorovat.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-387">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0d9ab-388">Výjimky se vyskytují jen vzácně &mdash; nejčastěji jsou to speciální případy uvnitř frameworku samotného.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-388">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0d9ab-389">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-389">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0d9ab-390">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="0d9ab-390">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d9ab-391">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0d9ab-391">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0d9ab-392">Psaní čistého kódu v ASP.NET Core pomocí vkládání závislostí (MSDN)</span><span class="sxs-lookup"><span data-stu-id="0d9ab-392">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="0d9ab-393">Princip explicitních závislostí</span><span class="sxs-lookup"><span data-stu-id="0d9ab-393">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="0d9ab-394">Kontejner inverze závislostí a vzor vkládání závislostí (Martin Fowler)</span><span class="sxs-lookup"><span data-stu-id="0d9ab-394">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="0d9ab-395">Postup pro registraci služeb s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="0d9ab-395">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

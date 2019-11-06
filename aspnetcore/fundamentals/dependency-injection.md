---
title: Vkládání závislostí v ASP.NET Core
author: guardrex
description: Přečtěte si, jak ASP.NET Core implementuje vkládání závislostí a jak ho používat.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/05/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: c46e7322e86c2836a15bd0720995a8634bb185be
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634015"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="f8029-103">Vkládání závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8029-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="f8029-104">[Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f8029-104">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f8029-105">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="f8029-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="f8029-106">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="f8029-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="f8029-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f8029-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="f8029-108">Přehled injektáže závislosti</span><span class="sxs-lookup"><span data-stu-id="f8029-108">Overview of dependency injection</span></span>

<span data-ttu-id="f8029-109">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="f8029-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="f8029-110">Projděte si následující třídu `MyDependency` s metodou `WriteMessage`, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="f8029-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="f8029-111">Lze vytvořit instanci třídy `MyDependency`, aby byla metoda `WriteMessage` dostupná pro třídu.</span><span class="sxs-lookup"><span data-stu-id="f8029-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="f8029-112">Třída `MyDependency` je závislost `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="f8029-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="f8029-113">Třída vytvoří a přímo závisí na instanci `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f8029-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="f8029-114">Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="f8029-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="f8029-115">Chcete-li nahradit `MyDependency` odlišnou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="f8029-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="f8029-116">Pokud `MyDependency` obsahuje závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="f8029-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="f8029-117">Ve velkém projektu s více třídami v závislosti na `MyDependency` je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="f8029-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="f8029-118">Tato implementace je obtížná pro testování částí.</span><span class="sxs-lookup"><span data-stu-id="f8029-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="f8029-119">Aplikace by měla použít třídu typu `MyDependency` nebo zástupnou proceduru, která není s tímto přístupem možná.</span><span class="sxs-lookup"><span data-stu-id="f8029-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="f8029-120">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="f8029-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="f8029-121">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="f8029-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="f8029-122">Registrace závislosti v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="f8029-123">ASP.NET Core poskytuje integrovaný kontejner služby <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="f8029-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="f8029-124">Služby jsou zaregistrované v `Startup.ConfigureServices` metodě aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8029-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="f8029-125">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="f8029-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="f8029-126">Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.</span><span class="sxs-lookup"><span data-stu-id="f8029-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="f8029-127">V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)definuje rozhraní `IMyDependency` metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="f8029-127">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f8029-128">Toto rozhraní je implementováno konkrétním typem `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="f8029-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f8029-129">`MyDependency` vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f8029-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="f8029-130">Není neobvyklé používat vkládání závislostí v zřetězeném způsobem.</span><span class="sxs-lookup"><span data-stu-id="f8029-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="f8029-131">Každá požadovaná závislost zase vyžádá své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="f8029-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="f8029-132">Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu.</span><span class="sxs-lookup"><span data-stu-id="f8029-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="f8029-133">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="f8029-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="f8029-134">`IMyDependency` a `ILogger<TCategoryName>` musí být registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="f8029-135">`IMyDependency` je zaregistrován v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8029-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f8029-136">`ILogger<TCategoryName>` je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu poskytovanou rozhraním](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8029-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="f8029-137">Kontejner překládá `ILogger<TCategoryName>` tím, že využívá [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="f8029-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

<span data-ttu-id="f8029-138">V ukázkové aplikaci je `IMyDependency` služba zaregistrovaná u konkrétního typu `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f8029-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="f8029-139">Registruje rozsah platnosti služby po dobu životnosti jediného požadavku.</span><span class="sxs-lookup"><span data-stu-id="f8029-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="f8029-140">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="f8029-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="f8029-141">Jednotlivé metody rozšíření `services.Add{SERVICE_NAME}` přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="f8029-142">`services.AddMvc()` například přidá služby Razor Pages a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="f8029-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="f8029-143">Doporučujeme, aby aplikace dodržovaly tuto konvenci.</span><span class="sxs-lookup"><span data-stu-id="f8029-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="f8029-144">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="f8029-145">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string`, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="f8029-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="f8029-146">Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli.</span><span class="sxs-lookup"><span data-stu-id="f8029-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="f8029-147">Pole se používá pro přístup ke službě podle potřeby v celé třídě.</span><span class="sxs-lookup"><span data-stu-id="f8029-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="f8029-148">V ukázkové aplikaci se vyžádá `IMyDependency` instance, která se používá k volání metody `WriteMessage` služby:</span><span class="sxs-lookup"><span data-stu-id="f8029-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

## <a name="services-injected-into-startup"></a><span data-ttu-id="f8029-149">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="f8029-149">Services injected into Startup</span></span>

<span data-ttu-id="f8029-150">Při použití obecného hostitele (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) lze do konstruktoru `Startup` vložit pouze následující typy služeb:</span><span class="sxs-lookup"><span data-stu-id="f8029-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="f8029-151">Služby je možné vložit do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f8029-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="f8029-152">Další informace najdete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="f8029-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="f8029-153">Služby poskytované rozhraním</span><span class="sxs-lookup"><span data-stu-id="f8029-153">Framework-provided services</span></span>

<span data-ttu-id="f8029-154">Metoda `Startup.ConfigureServices` zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="f8029-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="f8029-155">Zpočátku `IServiceCollection` poskytnutý pro `ConfigureServices` má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="f8029-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="f8029-156">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8029-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="f8029-157">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8029-157">A small sample of framework-registered services is listed in the following table.</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="f8029-158">Typ služby</span><span class="sxs-lookup"><span data-stu-id="f8029-158">Service Type</span></span> | <span data-ttu-id="f8029-159">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="f8029-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="f8029-160">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f8029-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="f8029-161">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="f8029-162">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="f8029-163">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="f8029-164">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f8029-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="f8029-165">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="f8029-166">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f8029-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="f8029-167">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="f8029-168">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="f8029-169">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="f8029-170">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f8029-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="f8029-171">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="f8029-172">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="f8029-173">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-173">Singleton</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="f8029-174">Typ služby</span><span class="sxs-lookup"><span data-stu-id="f8029-174">Service Type</span></span> | <span data-ttu-id="f8029-175">Doba platnosti</span><span class="sxs-lookup"><span data-stu-id="f8029-175">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="f8029-176">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f8029-176">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="f8029-177">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-177">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="f8029-178">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-178">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="f8029-179">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-179">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="f8029-180">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f8029-180">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="f8029-181">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-181">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="f8029-182">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f8029-182">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="f8029-183">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-183">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="f8029-184">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-184">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="f8029-185">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-185">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="f8029-186">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f8029-186">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="f8029-187">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-187">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="f8029-188">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-188">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="f8029-189">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-189">Singleton</span></span> |

::: moniker-end

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="f8029-190">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="f8029-190">Register additional services with extension methods</span></span>

<span data-ttu-id="f8029-191">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou metodu rozšíření `Add{SERVICE_NAME}` k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="f8029-191">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="f8029-192">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí rozšiřujících metod [AddDbContext \<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="f8029-192">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="f8029-193">Další informace najdete v dokumentaci k rozhraní API v tématu Třída <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>.</span><span class="sxs-lookup"><span data-stu-id="f8029-193">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="f8029-194">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="f8029-194">Service lifetimes</span></span>

<span data-ttu-id="f8029-195">Vyberte odpovídající dobu života pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="f8029-195">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="f8029-196">Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="f8029-196">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="f8029-197">Dočasný</span><span class="sxs-lookup"><span data-stu-id="f8029-197">Transient</span></span>

<span data-ttu-id="f8029-198">Služba přechodných dob života (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-198">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="f8029-199">Tato životnost funguje nejlépe pro odlehčené bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-199">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="f8029-200">Obor</span><span class="sxs-lookup"><span data-stu-id="f8029-200">Scoped</span></span>

<span data-ttu-id="f8029-201">Oborové služby (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) s vymezeným rozsahem se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="f8029-201">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="f8029-202">Při použití oboru služby v middlewaru zahájí službu do metody `Invoke` nebo `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="f8029-202">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="f8029-203">Nepoužívejte vkládání pomocí injektáže konstruktoru, protože vynutí, aby se služba chovala jako typ singleton.</span><span class="sxs-lookup"><span data-stu-id="f8029-203">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="f8029-204">Další informace najdete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="f8029-204">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="f8029-205">singleton</span><span class="sxs-lookup"><span data-stu-id="f8029-205">Singleton</span></span>

<span data-ttu-id="f8029-206">Služba singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) je vytvořena při prvním vyžádání (nebo při spuštění `Startup.ConfigureServices` a instance se zadává s registrací služby).</span><span class="sxs-lookup"><span data-stu-id="f8029-206">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="f8029-207">Každý další požadavek používá stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="f8029-207">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="f8029-208">Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-208">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="f8029-209">Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.</span><span class="sxs-lookup"><span data-stu-id="f8029-209">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="f8029-210">Není bezpečné přeložit oborovou službu z typu singleton.</span><span class="sxs-lookup"><span data-stu-id="f8029-210">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="f8029-211">Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.</span><span class="sxs-lookup"><span data-stu-id="f8029-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="f8029-212">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="f8029-212">Service registration methods</span></span>

<span data-ttu-id="f8029-213">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="f8029-213">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="f8029-214">Metoda</span><span class="sxs-lookup"><span data-stu-id="f8029-214">Method</span></span> | <span data-ttu-id="f8029-215">Automatické</span><span class="sxs-lookup"><span data-stu-id="f8029-215">Automatic</span></span><br><span data-ttu-id="f8029-216">odkazy objektů</span><span class="sxs-lookup"><span data-stu-id="f8029-216">object</span></span><br><span data-ttu-id="f8029-217">odvod</span><span class="sxs-lookup"><span data-stu-id="f8029-217">disposal</span></span> | <span data-ttu-id="f8029-218">Několik</span><span class="sxs-lookup"><span data-stu-id="f8029-218">Multiple</span></span><br><span data-ttu-id="f8029-219">implementace</span><span class="sxs-lookup"><span data-stu-id="f8029-219">implementations</span></span> | <span data-ttu-id="f8029-220">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="f8029-220">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="f8029-221">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f8029-221">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="f8029-222">Ano</span><span class="sxs-lookup"><span data-stu-id="f8029-222">Yes</span></span> | <span data-ttu-id="f8029-223">Ano</span><span class="sxs-lookup"><span data-stu-id="f8029-223">Yes</span></span> | <span data-ttu-id="f8029-224">Ne</span><span class="sxs-lookup"><span data-stu-id="f8029-224">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="f8029-225">Příklady:</span><span class="sxs-lookup"><span data-stu-id="f8029-225">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="f8029-226">Ano</span><span class="sxs-lookup"><span data-stu-id="f8029-226">Yes</span></span> | <span data-ttu-id="f8029-227">Ano</span><span class="sxs-lookup"><span data-stu-id="f8029-227">Yes</span></span> | <span data-ttu-id="f8029-228">Ano</span><span class="sxs-lookup"><span data-stu-id="f8029-228">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="f8029-229">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f8029-229">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="f8029-230">Ano</span><span class="sxs-lookup"><span data-stu-id="f8029-230">Yes</span></span> | <span data-ttu-id="f8029-231">Ne</span><span class="sxs-lookup"><span data-stu-id="f8029-231">No</span></span> | <span data-ttu-id="f8029-232">Ne</span><span class="sxs-lookup"><span data-stu-id="f8029-232">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="f8029-233">Příklady:</span><span class="sxs-lookup"><span data-stu-id="f8029-233">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="f8029-234">Ne</span><span class="sxs-lookup"><span data-stu-id="f8029-234">No</span></span> | <span data-ttu-id="f8029-235">Ano</span><span class="sxs-lookup"><span data-stu-id="f8029-235">Yes</span></span> | <span data-ttu-id="f8029-236">Ano</span><span class="sxs-lookup"><span data-stu-id="f8029-236">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="f8029-237">Příklady:</span><span class="sxs-lookup"><span data-stu-id="f8029-237">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="f8029-238">Ne</span><span class="sxs-lookup"><span data-stu-id="f8029-238">No</span></span> | <span data-ttu-id="f8029-239">Ne</span><span class="sxs-lookup"><span data-stu-id="f8029-239">No</span></span> | <span data-ttu-id="f8029-240">Ano</span><span class="sxs-lookup"><span data-stu-id="f8029-240">Yes</span></span> |

<span data-ttu-id="f8029-241">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="f8029-241">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="f8029-242">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="f8029-242">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="f8029-243">metody `TryAdd{LIFETIME}` registrují pouze v případě, že není zaregistrována implementace.</span><span class="sxs-lookup"><span data-stu-id="f8029-243">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="f8029-244">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="f8029-244">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="f8029-245">Druhý řádek nemá žádný vliv, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="f8029-245">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="f8029-246">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="f8029-246">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="f8029-247">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="f8029-247">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="f8029-248">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="f8029-248">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="f8029-249">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="f8029-249">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="f8029-250">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="f8029-250">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="f8029-251">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="f8029-251">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="f8029-252">Druhý řádek registruje `MyDep` pro `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="f8029-252">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="f8029-253">Třetí řádek nemá žádný vliv, protože `IMyDep1` již má registrovanou implementaci `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="f8029-253">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="f8029-254">Chování injektáže konstruktoru</span><span class="sxs-lookup"><span data-stu-id="f8029-254">Constructor injection behavior</span></span>

<span data-ttu-id="f8029-255">Služby je možné vyřešit pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="f8029-255">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="f8029-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; povoluje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="f8029-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="f8029-257">`ActivatorUtilities` se používá s uživateli orientovanými na abstrakce, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="f8029-257">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="f8029-258">Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f8029-258">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="f8029-259">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities`, injektáže konstruktoru vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="f8029-259">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="f8029-260">Když jsou služby vyřešeny `ActivatorUtilities`, injektáže konstruktoru vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="f8029-260">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="f8029-261">Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="f8029-261">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="f8029-262">Entity Framework kontexty</span><span class="sxs-lookup"><span data-stu-id="f8029-262">Entity Framework contexts</span></span>

<span data-ttu-id="f8029-263">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="f8029-263">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="f8029-264">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není určena doba platnosti [AddDbContext \<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="f8029-264">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="f8029-265">Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.</span><span class="sxs-lookup"><span data-stu-id="f8029-265">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="f8029-266">Možnosti života a registrace</span><span class="sxs-lookup"><span data-stu-id="f8029-266">Lifetime and registration options</span></span>

<span data-ttu-id="f8029-267">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="f8029-267">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="f8029-268">V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:</span><span class="sxs-lookup"><span data-stu-id="f8029-268">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f8029-269">Rozhraní jsou implementována ve třídě `Operation`.</span><span class="sxs-lookup"><span data-stu-id="f8029-269">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="f8029-270">Konstruktor `Operation` generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="f8029-270">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f8029-271">Je zaregistrována `OperationService`, která závisí na všech ostatních typech `Operation`.</span><span class="sxs-lookup"><span data-stu-id="f8029-271">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="f8029-272">Pokud je požadováno `OperationService` prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-272">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="f8029-273">Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` `IOperationTransient` služby se liší od `OperationId` `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="f8029-273">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="f8029-274">`OperationService` obdrží novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="f8029-274">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="f8029-275">Nová instance vrací jiný `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="f8029-275">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="f8029-276">Když se vytvoří vymezené služby na žádost klienta, `OperationId` služby `IOperationScoped` stejné jako u `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="f8029-276">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="f8029-277">V rámci požadavků klientů obě služby sdílejí jinou hodnotu `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="f8029-277">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="f8029-278">Když se v rámci všech požadavků klientů a všech služeb vytvoří a použije tento počet služeb instance singleton a singleton, je `OperationId` v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="f8029-278">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="f8029-279">V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="f8029-279">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

<span data-ttu-id="f8029-280">Služba `IOperationSingletonInstance` používá specifickou instanci se známým ID `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="f8029-280">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="f8029-281">Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).</span><span class="sxs-lookup"><span data-stu-id="f8029-281">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="f8029-282">Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky.</span><span class="sxs-lookup"><span data-stu-id="f8029-282">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="f8029-283">`IndexModel` ukázkové aplikace požaduje každý typ `IOperation` a `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="f8029-283">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="f8029-284">Na stránce se pak zobrazí všechny hodnoty `OperationId` třídy modelu stránky a hodnot služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="f8029-284">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

<span data-ttu-id="f8029-285">Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:</span><span class="sxs-lookup"><span data-stu-id="f8029-285">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="f8029-286">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="f8029-286">**First request:**</span></span>

<span data-ttu-id="f8029-287">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="f8029-287">Controller operations:</span></span>

<span data-ttu-id="f8029-288">Přechodný: d233e165-f417-469b-A866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="f8029-288">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="f8029-289">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f8029-289">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f8029-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8029-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8029-291">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8029-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8029-292">`OperationService` operace:</span><span class="sxs-lookup"><span data-stu-id="f8029-292">`OperationService` operations:</span></span>

<span data-ttu-id="f8029-293">Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="f8029-293">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="f8029-294">Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="f8029-294">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="f8029-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8029-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8029-296">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8029-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8029-297">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="f8029-297">**Second request:**</span></span>

<span data-ttu-id="f8029-298">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="f8029-298">Controller operations:</span></span>

<span data-ttu-id="f8029-299">Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="f8029-299">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="f8029-300">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f8029-300">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f8029-301">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8029-301">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8029-302">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8029-302">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8029-303">`OperationService` operace:</span><span class="sxs-lookup"><span data-stu-id="f8029-303">`OperationService` operations:</span></span>

<span data-ttu-id="f8029-304">Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="f8029-304">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="f8029-305">Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="f8029-305">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="f8029-306">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="f8029-306">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="f8029-307">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="f8029-307">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="f8029-308">Pozor, který z `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="f8029-308">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="f8029-309">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="f8029-309">*Transient* objects are always different.</span></span> <span data-ttu-id="f8029-310">Přechodný `OperationId` hodnota pro první i druhý požadavek klienta se liší pro operace `OperationService` a mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="f8029-310">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="f8029-311">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="f8029-311">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="f8029-312">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="f8029-312">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="f8029-313">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, jestli je `Operation` instance k dispozici v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f8029-313">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="f8029-314">Volat služby z Main</span><span class="sxs-lookup"><span data-stu-id="f8029-314">Call services from main</span></span>

<span data-ttu-id="f8029-315">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8029-315">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="f8029-316">Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace.</span><span class="sxs-lookup"><span data-stu-id="f8029-316">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="f8029-317">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f8029-317">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="f8029-318">Ověřování oboru</span><span class="sxs-lookup"><span data-stu-id="f8029-318">Scope validation</span></span>

<span data-ttu-id="f8029-319">Když je aplikace spuštěná ve vývojovém prostředí, provede výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="f8029-319">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="f8029-320">Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-320">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="f8029-321">Oborové služby nejsou přímo nebo nepřímo vložené do singleton.</span><span class="sxs-lookup"><span data-stu-id="f8029-321">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="f8029-322">Poskytovatel kořenové služby se vytvoří, když se zavolá <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="f8029-322">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="f8029-323">Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8029-323">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="f8029-324">Oborové služby jsou uvolněny kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="f8029-324">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="f8029-325">Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="f8029-325">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="f8029-326">Při ověřování oborů služeb se tyto situace zachytí, když se zavolá `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="f8029-326">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="f8029-327">Další informace najdete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="f8029-327">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="f8029-328">Žádosti o služby</span><span class="sxs-lookup"><span data-stu-id="f8029-328">Request Services</span></span>

<span data-ttu-id="f8029-329">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core z `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="f8029-329">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="f8029-330">Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8029-330">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="f8029-331">Pokud objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices`, nikoli `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="f8029-331">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="f8029-332">Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo.</span><span class="sxs-lookup"><span data-stu-id="f8029-332">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="f8029-333">Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vložit závislosti.</span><span class="sxs-lookup"><span data-stu-id="f8029-333">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="f8029-334">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="f8029-334">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="f8029-335">Pro přístup ke kolekci `RequestServices` preferovat požadavky závislosti jako na parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="f8029-335">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="f8029-336">Navrhnout služby pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="f8029-336">Design services for dependency injection</span></span>

<span data-ttu-id="f8029-337">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="f8029-337">Best practices are to:</span></span>

* <span data-ttu-id="f8029-338">Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="f8029-338">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="f8029-339">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="f8029-339">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="f8029-340">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="f8029-340">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="f8029-341">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="f8029-341">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="f8029-342">Přímá instance Couples kód na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="f8029-342">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="f8029-343">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="f8029-343">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="f8029-344">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="f8029-344">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="f8029-345">Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="f8029-345">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="f8029-346">Mějte na paměti, že Razor Pages třídy modelu stránky a třídy kontroleru MVC by se měly soustředit na uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8029-346">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="f8029-347">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="f8029-347">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="f8029-348">Vyřazení služeb</span><span class="sxs-lookup"><span data-stu-id="f8029-348">Disposal of services</span></span>

<span data-ttu-id="f8029-349">Kontejner volá <xref:System.IDisposable.Dispose*> pro typy <xref:System.IDisposable>, které vytvoří.</span><span class="sxs-lookup"><span data-stu-id="f8029-349">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="f8029-350">Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.</span><span class="sxs-lookup"><span data-stu-id="f8029-350">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

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

## <a name="default-service-container-replacement"></a><span data-ttu-id="f8029-351">Výchozí nahrazení kontejneru služby</span><span class="sxs-lookup"><span data-stu-id="f8029-351">Default service container replacement</span></span>

<span data-ttu-id="f8029-352">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="f8029-352">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="f8029-353">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="f8029-353">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="f8029-354">Vkládání vlastností</span><span class="sxs-lookup"><span data-stu-id="f8029-354">Property injection</span></span>
* <span data-ttu-id="f8029-355">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="f8029-355">Injection based on name</span></span>
* <span data-ttu-id="f8029-356">Podřízené kontejnery</span><span class="sxs-lookup"><span data-stu-id="f8029-356">Child containers</span></span>
* <span data-ttu-id="f8029-357">Vlastní Správa životního cyklu</span><span class="sxs-lookup"><span data-stu-id="f8029-357">Custom lifetime management</span></span>
* <span data-ttu-id="f8029-358">`Func<T>` podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="f8029-358">`Func<T>` support for lazy initialization</span></span>

<span data-ttu-id="f8029-359">S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="f8029-359">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="f8029-360">Autofac</span><span class="sxs-lookup"><span data-stu-id="f8029-360">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="f8029-361">DryIoc</span><span class="sxs-lookup"><span data-stu-id="f8029-361">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="f8029-362">Integrit</span><span class="sxs-lookup"><span data-stu-id="f8029-362">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="f8029-363">LightInject</span><span class="sxs-lookup"><span data-stu-id="f8029-363">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="f8029-364">Lamar</span><span class="sxs-lookup"><span data-stu-id="f8029-364">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="f8029-365">Stashbox</span><span class="sxs-lookup"><span data-stu-id="f8029-365">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="f8029-366">Unity</span><span class="sxs-lookup"><span data-stu-id="f8029-366">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="f8029-367">Bezpečnost vlákna</span><span class="sxs-lookup"><span data-stu-id="f8029-367">Thread safety</span></span>

<span data-ttu-id="f8029-368">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="f8029-368">Create thread-safe singleton services.</span></span> <span data-ttu-id="f8029-369">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="f8029-369">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="f8029-370">Metoda pro vytváření jedné služby, jako je například druhý argument pro [AddSingleton \<TService > (IServiceCollection, Func \<IServiceProvider, TService >)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="f8029-370">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="f8029-371">Podobně jako konstruktor typu (`static`), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="f8029-371">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="f8029-372">Doporučit</span><span class="sxs-lookup"><span data-stu-id="f8029-372">Recommendations</span></span>

* <span data-ttu-id="f8029-373">řešení služeb `async/await` a `Task` na základě se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="f8029-373">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="f8029-374">C#nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-374">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="f8029-375">Neukládejte data a konfiguraci přímo do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-375">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="f8029-376">Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="f8029-376">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="f8029-377">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="f8029-377">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="f8029-378">Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="f8029-378">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="f8029-379">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="f8029-379">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="f8029-380">Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="f8029-380">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="f8029-381">Vyhněte se použití *vzoru lokátoru služby*.</span><span class="sxs-lookup"><span data-stu-id="f8029-381">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="f8029-382">Například Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít DI:</span><span class="sxs-lookup"><span data-stu-id="f8029-382">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="f8029-383">**Špatný**</span><span class="sxs-lookup"><span data-stu-id="f8029-383">**Incorrect:**</span></span>

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

  <span data-ttu-id="f8029-384">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="f8029-384">**Correct**:</span></span>

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

* <span data-ttu-id="f8029-385">Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="f8029-385">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="f8029-386">Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="f8029-386">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="f8029-387">Nepoužívejte statický přístup k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="f8029-387">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="f8029-388">Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení.</span><span class="sxs-lookup"><span data-stu-id="f8029-388">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="f8029-389">Výjimky jsou zřídka &mdash;mostly zvláštních případech v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8029-389">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="f8029-390">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="f8029-390">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="f8029-391">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="f8029-391">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8029-392">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f8029-392">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="f8029-393">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="f8029-393">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="f8029-394">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="f8029-394">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="f8029-395">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="f8029-395">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="f8029-396">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="f8029-396">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

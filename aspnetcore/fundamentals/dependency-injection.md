---
title: Vkládání závislostí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak se používá.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: 3080d1a19bb48996e2bc7a3ce824f48bfc1bcbce
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663763"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="18e92-103">Vkládání závislostí v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18e92-103">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="18e92-104">[Steve Smith](https://ardalis.com/) a [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="18e92-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="18e92-105">ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.</span><span class="sxs-lookup"><span data-stu-id="18e92-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="18e92-106">Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="18e92-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="18e92-107">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18e92-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="18e92-108">Přehled vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="18e92-108">Overview of dependency injection</span></span>

<span data-ttu-id="18e92-109">*Závislost* je libovolný objekt, který vyžaduje jiný objekt.</span><span class="sxs-lookup"><span data-stu-id="18e92-109">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="18e92-110">Projděte si následující třídu `MyDependency` s metodou `WriteMessage`, na které jsou závislé jiné třídy v aplikaci:</span><span class="sxs-lookup"><span data-stu-id="18e92-110">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="18e92-111">Lze vytvořit instanci třídy `MyDependency`, aby byla metoda `WriteMessage` dostupná pro třídu.</span><span class="sxs-lookup"><span data-stu-id="18e92-111">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="18e92-112">Třída `MyDependency` je závislost `IndexModel` třídy:</span><span class="sxs-lookup"><span data-stu-id="18e92-112">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="18e92-113">Třída vytvoří a přímo závisí na instanci `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="18e92-113">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="18e92-114">Přímé závislosti v kódu (jako ta v předchozím příkladu) jsou problematické a měli byste se jim vyhnout z následujících důvodů:</span><span class="sxs-lookup"><span data-stu-id="18e92-114">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="18e92-115">Chcete-li nahradit `MyDependency` odlišnou implementací, třída musí být upravena.</span><span class="sxs-lookup"><span data-stu-id="18e92-115">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="18e92-116">Pokud `MyDependency` obsahuje závislosti, musí být nakonfigurovány třídou.</span><span class="sxs-lookup"><span data-stu-id="18e92-116">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="18e92-117">Ve velkém projektu s více třídami v závislosti na `MyDependency`je kód konfigurace rozptýlen napříč aplikací.</span><span class="sxs-lookup"><span data-stu-id="18e92-117">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="18e92-118">Tento způsob implementace je obtížný na jednotkové testování.</span><span class="sxs-lookup"><span data-stu-id="18e92-118">This implementation is difficult to unit test.</span></span> <span data-ttu-id="18e92-119">Aplikace by měla použít třídu typu `MyDependency` nebo zástupnou proceduru, která není s tímto přístupem možná.</span><span class="sxs-lookup"><span data-stu-id="18e92-119">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="18e92-120">Vkládání závislostí řeší tyto problémy prostřednictvím:</span><span class="sxs-lookup"><span data-stu-id="18e92-120">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="18e92-121">Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.</span><span class="sxs-lookup"><span data-stu-id="18e92-121">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="18e92-122">Registrace závislosti v kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="18e92-122">Registration of the dependency in a service container.</span></span> <span data-ttu-id="18e92-123">ASP.NET Core poskytuje integrovaný kontejner služby <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="18e92-123">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="18e92-124">Služby jsou zaregistrované v `Startup.ConfigureServices` metodě aplikace.</span><span class="sxs-lookup"><span data-stu-id="18e92-124">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="18e92-125">*Vložení* služby do konstruktoru třídy, kde se používá.</span><span class="sxs-lookup"><span data-stu-id="18e92-125">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="18e92-126">Framework přebírá zodpovědnost za vytváření instancí závislostí a jejich uvolňování, kdy již nejsou dále potřebné.</span><span class="sxs-lookup"><span data-stu-id="18e92-126">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="18e92-127">V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)definuje rozhraní `IMyDependency` metodu, kterou služba poskytuje aplikaci:</span><span class="sxs-lookup"><span data-stu-id="18e92-127">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="18e92-128">Toto rozhraní je implementováno konkrétním typem `MyDependency`:</span><span class="sxs-lookup"><span data-stu-id="18e92-128">This interface is implemented by a concrete type, `MyDependency`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="18e92-129">`MyDependency` vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="18e92-129">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="18e92-130">Není neobvyklé používat zřetězené vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="18e92-130">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="18e92-131">Každá požadovaná závislost může v zápětí požadovat své vlastní závislosti.</span><span class="sxs-lookup"><span data-stu-id="18e92-131">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="18e92-132">Kontejner řeší závislosti v grafu a vrací plně vyřešené služby.</span><span class="sxs-lookup"><span data-stu-id="18e92-132">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="18e92-133">Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.</span><span class="sxs-lookup"><span data-stu-id="18e92-133">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="18e92-134">`IMyDependency` a `ILogger<TCategoryName>` musí být registrovány v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="18e92-134">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="18e92-135">`IMyDependency` je zaregistrován v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18e92-135">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="18e92-136">`ILogger<TCategoryName>` je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu poskytovanou rozhraním](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18e92-136">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="18e92-137">Kontejner překládá `ILogger<TCategoryName>` tím, že využívá [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span><span class="sxs-lookup"><span data-stu-id="18e92-137">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="18e92-138">V ukázkové aplikaci je `IMyDependency` služba zaregistrovaná u konkrétního typu `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="18e92-138">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="18e92-139">Registrace specifikuje rámec životnosti služby na životnost jednoho požadavku.</span><span class="sxs-lookup"><span data-stu-id="18e92-139">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="18e92-140">[Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="18e92-140">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="18e92-141">Jednotlivé metody rozšíření `services.Add{SERVICE_NAME}` přidávají (a případně nakonfigurují) služby.</span><span class="sxs-lookup"><span data-stu-id="18e92-141">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="18e92-142">`services.AddMvc()` například přidá služby Razor Pages a MVC vyžaduje.</span><span class="sxs-lookup"><span data-stu-id="18e92-142">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="18e92-143">Doporučujeme, aby v aplikacích byla tato konvence dodržena.</span><span class="sxs-lookup"><span data-stu-id="18e92-143">We recommended that apps follow this convention.</span></span> <span data-ttu-id="18e92-144">Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.</span><span class="sxs-lookup"><span data-stu-id="18e92-144">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="18e92-145">Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string`, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="18e92-145">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="18e92-146">Instance služby je požadována prostřednictvím konstruktoru třídy, kde se služba využívá a přiřazuje do privátního pole.</span><span class="sxs-lookup"><span data-stu-id="18e92-146">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="18e92-147">Toto pole se používá pro přístup ke službě podle potřeby v rámci celé třídy.</span><span class="sxs-lookup"><span data-stu-id="18e92-147">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="18e92-148">V ukázkové aplikaci se vyžádá `IMyDependency` instance, která se používá k volání metody `WriteMessage` služby:</span><span class="sxs-lookup"><span data-stu-id="18e92-148">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

## <a name="services-injected-into-startup"></a><span data-ttu-id="18e92-149">Služby vložené do spuštění</span><span class="sxs-lookup"><span data-stu-id="18e92-149">Services injected into Startup</span></span>

<span data-ttu-id="18e92-150">Při použití obecného hostitele (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) lze do konstruktoru `Startup` vložit pouze následující typy služeb:</span><span class="sxs-lookup"><span data-stu-id="18e92-150">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="18e92-151">Služby je možné vložit do `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="18e92-151">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="18e92-152">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="18e92-152">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="18e92-153">Služby poskytované frameworkem</span><span class="sxs-lookup"><span data-stu-id="18e92-153">Framework-provided services</span></span>

<span data-ttu-id="18e92-154">Metoda `Startup.ConfigureServices` zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="18e92-154">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="18e92-155">Zpočátku `IServiceCollection` poskytnutý pro `ConfigureServices` má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="18e92-155">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="18e92-156">Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18e92-156">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="18e92-157">V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18e92-157">A small sample of framework-registered services is listed in the following table.</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="18e92-158">Typ služby</span><span class="sxs-lookup"><span data-stu-id="18e92-158">Service Type</span></span> | <span data-ttu-id="18e92-159">Životnost</span><span class="sxs-lookup"><span data-stu-id="18e92-159">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="18e92-160">Přechodná</span><span class="sxs-lookup"><span data-stu-id="18e92-160">Transient</span></span> |
| `IHostApplicationLifetime` | <span data-ttu-id="18e92-161">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-161">Singleton</span></span> |
| `IWebHostEnvironment` | <span data-ttu-id="18e92-162">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-162">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="18e92-163">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-163">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="18e92-164">Přechodná</span><span class="sxs-lookup"><span data-stu-id="18e92-164">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="18e92-165">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-165">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="18e92-166">Přechodná</span><span class="sxs-lookup"><span data-stu-id="18e92-166">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="18e92-167">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-167">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="18e92-168">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-168">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="18e92-169">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-169">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="18e92-170">Přechodná</span><span class="sxs-lookup"><span data-stu-id="18e92-170">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="18e92-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-171">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="18e92-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-172">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="18e92-173">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-173">Singleton</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="18e92-174">Typ služby</span><span class="sxs-lookup"><span data-stu-id="18e92-174">Service Type</span></span> | <span data-ttu-id="18e92-175">Životnost</span><span class="sxs-lookup"><span data-stu-id="18e92-175">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="18e92-176">Přechodná</span><span class="sxs-lookup"><span data-stu-id="18e92-176">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="18e92-177">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-177">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="18e92-178">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-178">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="18e92-179">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-179">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="18e92-180">Přechodná</span><span class="sxs-lookup"><span data-stu-id="18e92-180">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="18e92-181">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-181">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="18e92-182">Přechodná</span><span class="sxs-lookup"><span data-stu-id="18e92-182">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="18e92-183">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-183">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="18e92-184">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-184">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="18e92-185">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-185">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="18e92-186">Přechodná</span><span class="sxs-lookup"><span data-stu-id="18e92-186">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="18e92-187">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-187">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="18e92-188">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-188">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="18e92-189">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-189">Singleton</span></span> |

::: moniker-end

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="18e92-190">Registrace dalších služeb pomocí rozšiřujících metod</span><span class="sxs-lookup"><span data-stu-id="18e92-190">Register additional services with extension methods</span></span>

<span data-ttu-id="18e92-191">Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou metodu rozšíření `Add{SERVICE_NAME}` k registraci všech služeb vyžadovaných touto službou.</span><span class="sxs-lookup"><span data-stu-id="18e92-191">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="18e92-192">Následující kód je příkladem přidání dalších služeb do kontejneru pomocí rozšiřujících metod [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span><span class="sxs-lookup"><span data-stu-id="18e92-192">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="18e92-193">Další informace najdete v dokumentaci k rozhraní API v tématu Třída <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>.</span><span class="sxs-lookup"><span data-stu-id="18e92-193">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="18e92-194">Životnost služeb</span><span class="sxs-lookup"><span data-stu-id="18e92-194">Service lifetimes</span></span>

<span data-ttu-id="18e92-195">Zvolte odpovídající životnost pro každou registrovanou službu.</span><span class="sxs-lookup"><span data-stu-id="18e92-195">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="18e92-196">Služby ASP.NET Core můžete nakonfigurovat s následujícími životnostmi:</span><span class="sxs-lookup"><span data-stu-id="18e92-196">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="18e92-197">Přechodná</span><span class="sxs-lookup"><span data-stu-id="18e92-197">Transient</span></span>

<span data-ttu-id="18e92-198">Služba přechodných dob života (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se vytvoří pokaždé, když se požadují z kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="18e92-198">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="18e92-199">Tato životnost je vhodná pro jednoduché, bezstavové služby.</span><span class="sxs-lookup"><span data-stu-id="18e92-199">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="18e92-200">Obor</span><span class="sxs-lookup"><span data-stu-id="18e92-200">Scoped</span></span>

<span data-ttu-id="18e92-201">Oborové služby (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) s vymezeným rozsahem se vytvoří jednou za požadavek klienta (připojení).</span><span class="sxs-lookup"><span data-stu-id="18e92-201">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="18e92-202">Při použití oboru služby v middlewaru zahájí službu do metody `Invoke` nebo `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="18e92-202">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="18e92-203">Nevkládejte službu prostřednictvím konstruktoru, protože se služba bude chovat se jako singleton.</span><span class="sxs-lookup"><span data-stu-id="18e92-203">Don't inject via constructor injection because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="18e92-204">Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="18e92-204">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="18e92-205">Singleton</span><span class="sxs-lookup"><span data-stu-id="18e92-205">Singleton</span></span>

<span data-ttu-id="18e92-206">Služba singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) je vytvořena při prvním vyžádání (nebo při spuštění `Startup.ConfigureServices` a instance se zadává s registrací služby).</span><span class="sxs-lookup"><span data-stu-id="18e92-206">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="18e92-207">Každý další požadavek použije stejnou instanci.</span><span class="sxs-lookup"><span data-stu-id="18e92-207">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="18e92-208">Pokud je v aplikaci vyžadováno chování ve stylu návrhového vzoru Singleton, doporučuje se použít kontejner služeb ke správě životnosti takového objektu.</span><span class="sxs-lookup"><span data-stu-id="18e92-208">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="18e92-209">Neposkytujte vlastní implementaci návrhového vzoru Singleton a umožněte tak uživatelskému kódu spravovat životnost objektu v rámci třídy.</span><span class="sxs-lookup"><span data-stu-id="18e92-209">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="18e92-210">Je nebezpečné získávat vymezené služby ze singletonů.</span><span class="sxs-lookup"><span data-stu-id="18e92-210">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="18e92-211">Může to způsobit nesprávný stav služby při zpracování následujících požadavků.</span><span class="sxs-lookup"><span data-stu-id="18e92-211">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="18e92-212">Metody registrace služby</span><span class="sxs-lookup"><span data-stu-id="18e92-212">Service registration methods</span></span>

<span data-ttu-id="18e92-213">Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.</span><span class="sxs-lookup"><span data-stu-id="18e92-213">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="18e92-214">Metoda</span><span class="sxs-lookup"><span data-stu-id="18e92-214">Method</span></span> | <span data-ttu-id="18e92-215">Automaticky</span><span class="sxs-lookup"><span data-stu-id="18e92-215">Automatic</span></span><br><span data-ttu-id="18e92-216">objekt</span><span class="sxs-lookup"><span data-stu-id="18e92-216">object</span></span><br><span data-ttu-id="18e92-217">odvod</span><span class="sxs-lookup"><span data-stu-id="18e92-217">disposal</span></span> | <span data-ttu-id="18e92-218">Několik</span><span class="sxs-lookup"><span data-stu-id="18e92-218">Multiple</span></span><br><span data-ttu-id="18e92-219">implementace</span><span class="sxs-lookup"><span data-stu-id="18e92-219">implementations</span></span> | <span data-ttu-id="18e92-220">Pass – argumenty</span><span class="sxs-lookup"><span data-stu-id="18e92-220">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="18e92-221">Příklad:</span><span class="sxs-lookup"><span data-stu-id="18e92-221">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="18e92-222">Ano</span><span class="sxs-lookup"><span data-stu-id="18e92-222">Yes</span></span> | <span data-ttu-id="18e92-223">Ano</span><span class="sxs-lookup"><span data-stu-id="18e92-223">Yes</span></span> | <span data-ttu-id="18e92-224">Ne</span><span class="sxs-lookup"><span data-stu-id="18e92-224">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="18e92-225">Příklady:</span><span class="sxs-lookup"><span data-stu-id="18e92-225">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="18e92-226">Ano</span><span class="sxs-lookup"><span data-stu-id="18e92-226">Yes</span></span> | <span data-ttu-id="18e92-227">Ano</span><span class="sxs-lookup"><span data-stu-id="18e92-227">Yes</span></span> | <span data-ttu-id="18e92-228">Ano</span><span class="sxs-lookup"><span data-stu-id="18e92-228">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="18e92-229">Příklad:</span><span class="sxs-lookup"><span data-stu-id="18e92-229">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="18e92-230">Ano</span><span class="sxs-lookup"><span data-stu-id="18e92-230">Yes</span></span> | <span data-ttu-id="18e92-231">Ne</span><span class="sxs-lookup"><span data-stu-id="18e92-231">No</span></span> | <span data-ttu-id="18e92-232">Ne</span><span class="sxs-lookup"><span data-stu-id="18e92-232">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="18e92-233">Příklady:</span><span class="sxs-lookup"><span data-stu-id="18e92-233">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="18e92-234">Ne</span><span class="sxs-lookup"><span data-stu-id="18e92-234">No</span></span> | <span data-ttu-id="18e92-235">Ano</span><span class="sxs-lookup"><span data-stu-id="18e92-235">Yes</span></span> | <span data-ttu-id="18e92-236">Ano</span><span class="sxs-lookup"><span data-stu-id="18e92-236">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="18e92-237">Příklady:</span><span class="sxs-lookup"><span data-stu-id="18e92-237">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="18e92-238">Ne</span><span class="sxs-lookup"><span data-stu-id="18e92-238">No</span></span> | <span data-ttu-id="18e92-239">Ne</span><span class="sxs-lookup"><span data-stu-id="18e92-239">No</span></span> | <span data-ttu-id="18e92-240">Ano</span><span class="sxs-lookup"><span data-stu-id="18e92-240">Yes</span></span> |

<span data-ttu-id="18e92-241">Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) .</span><span class="sxs-lookup"><span data-stu-id="18e92-241">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="18e92-242">Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="18e92-242">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="18e92-243">metody `TryAdd{LIFETIME}` registrují pouze v případě, že není zaregistrována implementace.</span><span class="sxs-lookup"><span data-stu-id="18e92-243">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="18e92-244">V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="18e92-244">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="18e92-245">Druhý řádek nemá žádný vliv, protože `IMyDependency` už má registrovanou implementaci:</span><span class="sxs-lookup"><span data-stu-id="18e92-245">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="18e92-246">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="18e92-246">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="18e92-247">Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*.</span><span class="sxs-lookup"><span data-stu-id="18e92-247">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="18e92-248">Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>`.</span><span class="sxs-lookup"><span data-stu-id="18e92-248">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="18e92-249">Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný.</span><span class="sxs-lookup"><span data-stu-id="18e92-249">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="18e92-250">Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.</span><span class="sxs-lookup"><span data-stu-id="18e92-250">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="18e92-251">V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="18e92-251">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="18e92-252">Druhý řádek registruje `MyDep` pro `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="18e92-252">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="18e92-253">Třetí řádek nemá žádný vliv, protože `IMyDep1` již má registrovanou implementaci `MyDep`:</span><span class="sxs-lookup"><span data-stu-id="18e92-253">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="18e92-254">Chování vkládání pomocí konstruktoru</span><span class="sxs-lookup"><span data-stu-id="18e92-254">Constructor injection behavior</span></span>

<span data-ttu-id="18e92-255">Služby mohou být řešeny pomocí dvou mechanismů:</span><span class="sxs-lookup"><span data-stu-id="18e92-255">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="18e92-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; povoluje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="18e92-256"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="18e92-257">`ActivatorUtilities` se používá s uživateli orientovanými na abstrakce, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.</span><span class="sxs-lookup"><span data-stu-id="18e92-257">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="18e92-258">Konstruktory mohou přijímat argumenty, které nejsou poskytovány v rámci vkládání závislostí, ale takové argumenty musí mít přiřazené výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="18e92-258">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="18e92-259">Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities`, injektáže konstruktoru vyžaduje *veřejný* konstruktor.</span><span class="sxs-lookup"><span data-stu-id="18e92-259">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, constructor injection requires a *public* constructor.</span></span>

<span data-ttu-id="18e92-260">Když jsou služby vyřešeny `ActivatorUtilities`, injektáže konstruktoru vyžaduje, aby existoval pouze jeden příslušný konstruktor.</span><span class="sxs-lookup"><span data-stu-id="18e92-260">When services are resolved by `ActivatorUtilities`, constructor injection requires that only one applicable constructor exists.</span></span> <span data-ttu-id="18e92-261">Přetížení konstruktoru je podporováno, ale může existovat pouze jedno přetížením, jehož argumenty jsou splnitelné vkládáním závislostí.</span><span class="sxs-lookup"><span data-stu-id="18e92-261">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="18e92-262">Kontexty Entity Frameworku</span><span class="sxs-lookup"><span data-stu-id="18e92-262">Entity Framework contexts</span></span>

<span data-ttu-id="18e92-263">Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta.</span><span class="sxs-lookup"><span data-stu-id="18e92-263">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="18e92-264">Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není určena doba platnosti [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení.</span><span class="sxs-lookup"><span data-stu-id="18e92-264">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="18e92-265">Služby s danou životností by neměly používat databázový kontext s životností kratší, než je životnost služby.</span><span class="sxs-lookup"><span data-stu-id="18e92-265">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="18e92-266">Životnosti a možnosti registrace</span><span class="sxs-lookup"><span data-stu-id="18e92-266">Lifetime and registration options</span></span>

<span data-ttu-id="18e92-267">Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="18e92-267">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="18e92-268">V závislosti na tom, jak je životnost této služby nakonfigurována pro následující rozhraní, poskytne kontejner stejnou nebo rozdílnou instanci služby během vyžádání třídou:</span><span class="sxs-lookup"><span data-stu-id="18e92-268">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="18e92-269">Rozhraní jsou implementována ve třídě `Operation`.</span><span class="sxs-lookup"><span data-stu-id="18e92-269">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="18e92-270">Konstruktor `Operation` generuje identifikátor GUID, pokud není dodán:</span><span class="sxs-lookup"><span data-stu-id="18e92-270">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="18e92-271">Je zaregistrována `OperationService`, která závisí na všech ostatních typech `Operation`.</span><span class="sxs-lookup"><span data-stu-id="18e92-271">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="18e92-272">Pokud je požadováno `OperationService` prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.</span><span class="sxs-lookup"><span data-stu-id="18e92-272">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="18e92-273">Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` `IOperationTransient` služby se liší od `OperationId` `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="18e92-273">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="18e92-274">`OperationService` obdrží novou instanci `IOperationTransient` třídy.</span><span class="sxs-lookup"><span data-stu-id="18e92-274">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="18e92-275">Nová instance vrací jiný `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="18e92-275">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="18e92-276">Když se vytvoří vymezené služby na žádost klienta, `OperationId` služby `IOperationScoped` stejné jako u `OperationService` v rámci žádosti klienta.</span><span class="sxs-lookup"><span data-stu-id="18e92-276">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="18e92-277">V rámci požadavků klientů obě služby sdílejí jinou hodnotu `OperationId`.</span><span class="sxs-lookup"><span data-stu-id="18e92-277">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="18e92-278">Když se v rámci všech požadavků klientů a všech služeb vytvoří a použije tento počet služeb instance singleton a singleton, je `OperationId` v rámci všech žádostí o službu konstantní.</span><span class="sxs-lookup"><span data-stu-id="18e92-278">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="18e92-279">V `Startup.ConfigureServices`je každý typ přidán do kontejneru podle jeho pojmenované životnosti:</span><span class="sxs-lookup"><span data-stu-id="18e92-279">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

<span data-ttu-id="18e92-280">Služba `IOperationSingletonInstance` používá specifickou instanci se známým ID `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="18e92-280">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="18e92-281">Je pak zřejmé, kdy je tento typ použit (jeho identifikátor GUID obsahuje samé nuly).</span><span class="sxs-lookup"><span data-stu-id="18e92-281">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="18e92-282">Ukázková aplikace demonstruje živostnosti objektů v rámci jednotlivých požadavků a mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="18e92-282">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="18e92-283">`IndexModel` ukázkové aplikace požaduje každý typ `IOperation` a `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="18e92-283">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="18e92-284">Na stránce se pak zobrazí všechny hodnoty `OperationId` třídy modelu stránky a hodnot služby prostřednictvím přiřazení vlastností:</span><span class="sxs-lookup"><span data-stu-id="18e92-284">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

<span data-ttu-id="18e92-285">Následující výpis ukazuje výsledek dvou HTTP požadavků:</span><span class="sxs-lookup"><span data-stu-id="18e92-285">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="18e92-286">**První požadavek:**</span><span class="sxs-lookup"><span data-stu-id="18e92-286">**First request:**</span></span>

<span data-ttu-id="18e92-287">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="18e92-287">Controller operations:</span></span>

<span data-ttu-id="18e92-288">Transient: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="18e92-288">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="18e92-289">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="18e92-289">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="18e92-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="18e92-290">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="18e92-291">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="18e92-291">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="18e92-292">`OperationService` operace:</span><span class="sxs-lookup"><span data-stu-id="18e92-292">`OperationService` operations:</span></span>

<span data-ttu-id="18e92-293">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="18e92-293">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="18e92-294">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="18e92-294">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="18e92-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="18e92-295">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="18e92-296">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="18e92-296">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="18e92-297">**Druhá žádost:**</span><span class="sxs-lookup"><span data-stu-id="18e92-297">**Second request:**</span></span>

<span data-ttu-id="18e92-298">Operace kontroleru:</span><span class="sxs-lookup"><span data-stu-id="18e92-298">Controller operations:</span></span>

<span data-ttu-id="18e92-299">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="18e92-299">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="18e92-300">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="18e92-300">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="18e92-301">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="18e92-301">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="18e92-302">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="18e92-302">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="18e92-303">`OperationService` operace:</span><span class="sxs-lookup"><span data-stu-id="18e92-303">`OperationService` operations:</span></span>

<span data-ttu-id="18e92-304">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="18e92-304">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="18e92-305">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="18e92-305">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="18e92-306">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="18e92-306">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="18e92-307">Instance: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="18e92-307">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="18e92-308">Pozor, který z `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:</span><span class="sxs-lookup"><span data-stu-id="18e92-308">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="18e92-309">*Přechodné* objekty jsou vždy odlišné.</span><span class="sxs-lookup"><span data-stu-id="18e92-309">*Transient* objects are always different.</span></span> <span data-ttu-id="18e92-310">Přechodný `OperationId` hodnota pro první i druhý požadavek klienta se liší pro operace `OperationService` a mezi požadavky klientů.</span><span class="sxs-lookup"><span data-stu-id="18e92-310">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="18e92-311">Každé žádosti o službu a žádosti klienta se poskytne nová instance.</span><span class="sxs-lookup"><span data-stu-id="18e92-311">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="18e92-312">*Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.</span><span class="sxs-lookup"><span data-stu-id="18e92-312">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="18e92-313">Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, jestli je `Operation` instance k dispozici v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="18e92-313">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="18e92-314">Volání služeb z main</span><span class="sxs-lookup"><span data-stu-id="18e92-314">Call services from main</span></span>

<span data-ttu-id="18e92-315">Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="18e92-315">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="18e92-316">Tento způsob je užitečný pro přístup k službám s vymezenou životností při provádění inicializačních úloh během spuštění.</span><span class="sxs-lookup"><span data-stu-id="18e92-316">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="18e92-317">Následující příklad ukazuje, jak získat kontext pro `MyScopedService` v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="18e92-317">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="18e92-318">Ověření rámce životnosti</span><span class="sxs-lookup"><span data-stu-id="18e92-318">Scope validation</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18e92-319">Když je aplikace spuštěná ve vývojovém prostředí a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="18e92-319">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18e92-320">Když je aplikace spuštěná ve vývojovém prostředí a volá [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:</span><span class="sxs-lookup"><span data-stu-id="18e92-320">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) to build the host, the default service provider performs checks to verify that:</span></span>

::: moniker-end

* <span data-ttu-id="18e92-321">Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.</span><span class="sxs-lookup"><span data-stu-id="18e92-321">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="18e92-322">Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.</span><span class="sxs-lookup"><span data-stu-id="18e92-322">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="18e92-323">Poskytovatel kořenové služby se vytvoří, když se zavolá <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>.</span><span class="sxs-lookup"><span data-stu-id="18e92-323">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="18e92-324">Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="18e92-324">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="18e92-325">Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil.</span><span class="sxs-lookup"><span data-stu-id="18e92-325">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="18e92-326">Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace.</span><span class="sxs-lookup"><span data-stu-id="18e92-326">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="18e92-327">Při ověřování oborů služeb se tyto situace zachytí, když se zavolá `BuildServiceProvider`.</span><span class="sxs-lookup"><span data-stu-id="18e92-327">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="18e92-328">Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="18e92-328">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="18e92-329">Služby požadavků</span><span class="sxs-lookup"><span data-stu-id="18e92-329">Request Services</span></span>

<span data-ttu-id="18e92-330">Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core z `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .</span><span class="sxs-lookup"><span data-stu-id="18e92-330">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="18e92-331">Služby požadavků představují služby nakonfigurované a požadováné v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="18e92-331">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="18e92-332">Pokud objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices`, nikoli `ApplicationServices`.</span><span class="sxs-lookup"><span data-stu-id="18e92-332">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="18e92-333">Aplikace by obvykle neměly používat tyto vlastnosti přímo.</span><span class="sxs-lookup"><span data-stu-id="18e92-333">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="18e92-334">Místo toho získávejte požadované typy pomocí konstruktoru třídy a nechte framework vložit odpovídající závislosti.</span><span class="sxs-lookup"><span data-stu-id="18e92-334">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="18e92-335">To poskytuje třídy, které jsou snáze testovány.</span><span class="sxs-lookup"><span data-stu-id="18e92-335">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="18e92-336">Pro přístup ke kolekci `RequestServices` preferovat požadavky závislosti jako na parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="18e92-336">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="18e92-337">Návrh služeb pro vkládání závislostí</span><span class="sxs-lookup"><span data-stu-id="18e92-337">Design services for dependency injection</span></span>

<span data-ttu-id="18e92-338">Osvědčené postupy:</span><span class="sxs-lookup"><span data-stu-id="18e92-338">Best practices are to:</span></span>

* <span data-ttu-id="18e92-339">Navrhujte služby tak, aby využívaly vkládání závislostí pro získání jejich závislostí.</span><span class="sxs-lookup"><span data-stu-id="18e92-339">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="18e92-340">Vyhněte se stavovým, statickým třídám a členům.</span><span class="sxs-lookup"><span data-stu-id="18e92-340">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="18e92-341">Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.</span><span class="sxs-lookup"><span data-stu-id="18e92-341">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="18e92-342">Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb.</span><span class="sxs-lookup"><span data-stu-id="18e92-342">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="18e92-343">Přímé vytváření instancí způsobuje přímou závislost na konkrétní implementaci.</span><span class="sxs-lookup"><span data-stu-id="18e92-343">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="18e92-344">Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.</span><span class="sxs-lookup"><span data-stu-id="18e92-344">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="18e92-345">Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span><span class="sxs-lookup"><span data-stu-id="18e92-345">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="18e92-346">V tom případě se pokuste refaktorovat třídu tak, že některé z jeho zodpovědností přesunete do nové třídy.</span><span class="sxs-lookup"><span data-stu-id="18e92-346">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="18e92-347">Mějte na paměti, že třídy modelů stránek Razor a třídy kontrolerů MVC by měly být zaměřeny na aspekty uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18e92-347">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="18e92-348">Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="18e92-348">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="18e92-349">Uvolnění služeb</span><span class="sxs-lookup"><span data-stu-id="18e92-349">Disposal of services</span></span>

<span data-ttu-id="18e92-350">Kontejner volá <xref:System.IDisposable.Dispose*> pro typy <xref:System.IDisposable>, které vytvoří.</span><span class="sxs-lookup"><span data-stu-id="18e92-350">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="18e92-351">Instance přidané do kontejneru pomocí uživatelské kódu nejsou automaticky uvolňovány.</span><span class="sxs-lookup"><span data-stu-id="18e92-351">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

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

## <a name="default-service-container-replacement"></a><span data-ttu-id="18e92-352">Nahrazení výchozího kontejneru služeb</span><span class="sxs-lookup"><span data-stu-id="18e92-352">Default service container replacement</span></span>

<span data-ttu-id="18e92-353">Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací.</span><span class="sxs-lookup"><span data-stu-id="18e92-353">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="18e92-354">Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:</span><span class="sxs-lookup"><span data-stu-id="18e92-354">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="18e92-355">Vkládání pomocí vlastností</span><span class="sxs-lookup"><span data-stu-id="18e92-355">Property injection</span></span>
* <span data-ttu-id="18e92-356">Vkládání na základě názvu</span><span class="sxs-lookup"><span data-stu-id="18e92-356">Injection based on name</span></span>
* <span data-ttu-id="18e92-357">Vnořené kontejnery</span><span class="sxs-lookup"><span data-stu-id="18e92-357">Child containers</span></span>
* <span data-ttu-id="18e92-358">Vlastní správa životnosti</span><span class="sxs-lookup"><span data-stu-id="18e92-358">Custom lifetime management</span></span>
* <span data-ttu-id="18e92-359">`Func<T>` podpora opožděné inicializace</span><span class="sxs-lookup"><span data-stu-id="18e92-359">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="18e92-360">Registrace založená na konvencích</span><span class="sxs-lookup"><span data-stu-id="18e92-360">Convention-based registration</span></span>

<span data-ttu-id="18e92-361">S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:</span><span class="sxs-lookup"><span data-stu-id="18e92-361">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="18e92-362">Autofac</span><span class="sxs-lookup"><span data-stu-id="18e92-362">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="18e92-363">DryIoc</span><span class="sxs-lookup"><span data-stu-id="18e92-363">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="18e92-364">Integrit</span><span class="sxs-lookup"><span data-stu-id="18e92-364">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="18e92-365">LightInject</span><span class="sxs-lookup"><span data-stu-id="18e92-365">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="18e92-366">Lamar</span><span class="sxs-lookup"><span data-stu-id="18e92-366">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="18e92-367">Stashbox</span><span class="sxs-lookup"><span data-stu-id="18e92-367">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="18e92-368">Unity</span><span class="sxs-lookup"><span data-stu-id="18e92-368">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="18e92-369">Bezpečný přístup z více vláken</span><span class="sxs-lookup"><span data-stu-id="18e92-369">Thread safety</span></span>

<span data-ttu-id="18e92-370">Vytvořte služby, které jsou v bezpečí pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="18e92-370">Create thread-safe singleton services.</span></span> <span data-ttu-id="18e92-371">Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.</span><span class="sxs-lookup"><span data-stu-id="18e92-371">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="18e92-372">Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton\<TService > (IServiceCollection, Func\<IServiceProvider, TService >)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken.</span><span class="sxs-lookup"><span data-stu-id="18e92-372">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="18e92-373">Podobně jako konstruktor typu (`static`), je zaručeno, že bude volán jednou vláknem.</span><span class="sxs-lookup"><span data-stu-id="18e92-373">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="18e92-374">Doporučení</span><span class="sxs-lookup"><span data-stu-id="18e92-374">Recommendations</span></span>

* <span data-ttu-id="18e92-375">řešení služeb `async/await` a `Task` na základě se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="18e92-375">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="18e92-376">C#nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.</span><span class="sxs-lookup"><span data-stu-id="18e92-376">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="18e92-377">Vyhněte se ukládání dat a konfigurace přímo do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="18e92-377">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="18e92-378">Například nákupní košík uživatele by neměl být přidáván do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="18e92-378">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="18e92-379">Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="18e92-379">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="18e92-380">Podobně se vystříhejte použití objektů "držící data", jejichž jediným účelem je poskytnutí přístupu k některému jinému objektu.</span><span class="sxs-lookup"><span data-stu-id="18e92-380">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="18e92-381">Je lepší žádat o skutečnou položku přes DI.</span><span class="sxs-lookup"><span data-stu-id="18e92-381">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="18e92-382">Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).</span><span class="sxs-lookup"><span data-stu-id="18e92-382">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="18e92-383">Vyhněte se použití *vzoru lokátoru služby*.</span><span class="sxs-lookup"><span data-stu-id="18e92-383">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="18e92-384">Například Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít DI:</span><span class="sxs-lookup"><span data-stu-id="18e92-384">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="18e92-385">**Špatný**</span><span class="sxs-lookup"><span data-stu-id="18e92-385">**Incorrect:**</span></span>

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

  <span data-ttu-id="18e92-386">**Správné**:</span><span class="sxs-lookup"><span data-stu-id="18e92-386">**Correct**:</span></span>

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

* <span data-ttu-id="18e92-387">Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu.</span><span class="sxs-lookup"><span data-stu-id="18e92-387">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="18e92-388">Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .</span><span class="sxs-lookup"><span data-stu-id="18e92-388">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="18e92-389">Nepoužívejte statický přístup k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="18e92-389">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="18e92-390">Stejně jako u všech doporučení mohou nastat situace, ve kterých je možné tato doporučení ignorovat.</span><span class="sxs-lookup"><span data-stu-id="18e92-390">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="18e92-391">Výjimky jsou zřídka&mdash;většinou zvláštní případy v rámci samotného rozhraní.</span><span class="sxs-lookup"><span data-stu-id="18e92-391">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="18e92-392">DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům.</span><span class="sxs-lookup"><span data-stu-id="18e92-392">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="18e92-393">Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.</span><span class="sxs-lookup"><span data-stu-id="18e92-393">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="18e92-394">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="18e92-394">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="18e92-395">Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)</span><span class="sxs-lookup"><span data-stu-id="18e92-395">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="18e92-396">Explicitní závislosti – princip</span><span class="sxs-lookup"><span data-stu-id="18e92-396">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [<span data-ttu-id="18e92-397">Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)</span><span class="sxs-lookup"><span data-stu-id="18e92-397">Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)</span></span>](https://www.martinfowler.com/articles/injection.html)
* [<span data-ttu-id="18e92-398">Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="18e92-398">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
